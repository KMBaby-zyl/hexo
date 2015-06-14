注：本文出自腾讯AlloyTeam的元彦，文章也可以在github上浏览。请尊重版权，转载请注明来源，多谢～～
***
高性能移动Web相较PC的场景需要考虑的因素也相对更多更复杂，我们总结为以下几点： 流量、功耗与流畅度。 在PC时代我们更多的是考虑体验上的流畅度，而在Mobile端本身丰富的场景下，需要额外关注对用户基站网络流量使用的情况，设备耗电量的情况。

关于流畅度，主要体现在前端动画中，在现有的前端动画体系中，通常有两种模式：JS动画与CSS3动画。 JS动画是通过JS动态改写样式实现动画能力的一种方案，在PC端兼容低端浏览器中不失为一种推荐方案。 而在移动端，我们选择性能更优浏览器原生实现方案：CSS3动画。

然而，CSS3动画在移动多终端设备场景下，相比PC会面对更多的性能问题，主要体现在动画的卡顿与闪烁。

目前对提升移动端CSS3动画体验的主要方法有几点：

### 尽可能多的利用硬件能力，如使用3D变形来开启GPU加速
    -webkit-transform: translate3d(0, 0, 0);
    -moz-transform: translate3d(0, 0, 0);
    -ms-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);

###如动画过程有闪烁（通常发生在动画开始的时候），可以尝试下面的Hack：
    -webkit-backface-visibility: hidden;
    -moz-backface-visibility: hidden;
    -ms-backface-visibility: hidden;
    backface-visibility: hidden;

    -webkit-perspective: 1000;
    -moz-perspective: 1000;
    -ms-perspective: 1000;
    perspective: 1000;

###如下面一个元素通过translate3d右移500px的动画流畅度会明显优于使用left属性：
    #ball-1 {
      transition: -webkit-transform .5s ease;
      -webkit-transform: translate3d(0, 0, 0);
    }
    #ball-1.slidein {
      -webkit-transform: translate3d(500px, 0, 0);
    }

    #ball-2 {
      transition: left .5s ease;
      left：0;
    }
    #ball-2.slidein {
      left：500px;
    }
注：3D变形会消耗更多的内存与功耗，应确实有性能问题时才去使用它，兼在权衡
###尽可能少的使用box-shadows与gradients
box-shadows与gradients往往都是页面的性能杀手，尤其是在一个元素同时都使用了它们，所以拥抱扁平化设计吧。

###尽可能的让动画元素不在文档流中，以减少重排
    position: fixed;
    position: absolute;

###优化 DOM layout 性能

我们从实例开始描述这个主题：

    var newWidth = aDiv.offsetWidth + 10;
    aDiv.style.width = newWidth + 'px';
    var newHeight = aDiv.offsetHeight + 10;
    aDiv.style.height = newHeight + 'px';

    var newWidth = aDiv.offsetWidth + 10;
    var newHeight = aDiv.offsetHeight + 10;
    aDiv.style.width = newWidth + 'px';
    aDiv.style.height = newHeight + 'px';

从注释中可找到规律，连续的读取offsetWidth/Height属性与连续的设置width/height属性，相比分别读取设置单个属性可少触发一次layout。

从结论看似乎与执行队列有关，没错，这是浏览器的优化策略。所有可触发layout的操作都会被暂时放入 layout-queue 中，等到必须更新的时候，再计算整个队列中所有操作影响的结果，如此就可只进行一次的layout，从而提升性能。

关键一，可触发layout的操作，哪些操作下会layout的更新（也称为reflow或者relayout）？

我们从浏览器的源码实现入手，以开源**Webkit/Blink**为例， 对layout的更新，Webkit 主要通过 **Document::updateLayout** 与**Document::updateLayoutIgnorePendingStylesheets** 两个方法：


    void Document::updateLayout()
    {
        ASSERT(isMainThread());

        FrameView* frameView = view();
        if (frameView &amp;&amp; frameView-&gt;isInLayout()) {
            ASSERT_NOT_REACHED();
            return;
        }

        if (Element* oe = ownerElement())
            oe-&gt;document()-&gt;updateLayout();

        updateStyleIfNeeded();

        StackStats::LayoutCheckPoint layoutCheckPoint;

        if (frameView &amp;&amp; renderer() &amp;&amp; (frameView-&gt;layoutPending() || renderer()-&gt;needsLayout()))
            frameView-&gt;layout();

        if (m_focusedNode &amp;&amp; !m_didPostCheckFocusedNodeTask) {
            postTask(CheckFocusedNodeTask::create());
            m_didPostCheckFocusedNodeTask = true;
        }
    }

    void Document::updateLayoutIgnorePendingStylesheets()
    {
        bool oldIgnore = m_ignorePendingStylesheets;

        if (!haveStylesheetsLoaded()) {
            m_ignorePendingStylesheets = true;

            HTMLElement* bodyElement = body();
            if (bodyElement &amp;&amp; !bodyElement-&gt;renderer() &amp;&amp; m_pendingSheetLayout == NoLayoutWithPendingSheets) {
                m_pendingSheetLayout = DidLayoutWithPendingSheets;
                styleResolverChanged(RecalcStyleImmediately);
            } else if (m_hasNodesWithPlaceholderStyle)
                recalcStyle(Force);
        }

        updateLayout();

        m_ignorePendingStylesheets = oldIgnore;
    }



从 updateLayoutIgnorePendingStylesheets 方法的内部实现可知，其也是对 updateLayout 方法的扩展，并且在现有的 layout 更新模式中，大部分场景都是调用 updateLayoutIgnorePendingStylesheets 来进行layout的更新。

搜索 Webkit 实现中调用 updateLayoutIgnorePendingStylesheets 方法的代码, 得到以下可导致触发 layout 的操作：

* Element: clientHeight, clientLeft, clientTop, clientWidth, focus(), getBoundingClientRect(), getClientRects(), innerText, offsetHeight, offsetLeft, offsetParent, offsetTop, offsetWidth, outerText, scrollByLines(), scrollByPages(), scrollHeight, scrollIntoView(), scrollIntoViewIfNeeded(), scrollLeft, scrollTop, scrollWidth
* Frame, HTMLImageElement: height, width
* Range: getBoundingClientRect(), getClientRects()
* SVGLocatable: computeCTM(), getBBox()
* SVGTextContent: getCharNumAtPosition(), getComputedTextLength(), getEndPositionOfChar(), getExtentOfChar(), getNumberOfChars(), getRotationOfChar(), getStartPositionOfChar(), getSubStringLength(), selectSubString()
* SVGUse: instanceRoot
* window: getComputedStyle(), scrollBy(), scrollTo(), scrollX, scrollY, webkitConvertPointFromNodeToPage(), webkitConvertPointFromPageToNode()

进一步深入Layout，那上文中必须更新的必要条件是什么？ 在 Stoyan Stefanov 的 Rendering: repaint, reflow/relayout, restyle 一文中已做比较详细的解答，可移步了解~







