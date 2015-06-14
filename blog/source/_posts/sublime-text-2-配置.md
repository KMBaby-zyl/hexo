#sublime text 2

***

###  常用User Setting

    {
        "default_encoding": "UTF-8",
        "font_size": 13.0,
        "tab_size": 4,
        "translate_tabs_to_spaces": true,
        "trim_trailing_white_space_on_save": true
    }


### 插件扩展（package control）

按下 ctrl ＋ ｀ 出现console menu
输入下面的命令

    import urllib2,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')

然后重启sublime

按住 Command-Shift-P (Mac OS X) or Ctrl-Shift-P (Windows) 可以看到许多sublime的命令

然后输入 install package 回车
之后就可以安装各种subline插件了
### 安装HTML-CSS-JS Prettify 插件
Command-Shift-P 再 输入 install package 回车后
输入HTML-CSS-JS Prettify 等待安装完成

Command-Shift-H 命令就可以帮你快速格式化代码了

在菜单栏的preferences 中的 package settings 中可以查看自己安装的插件，更改插件的配置文件
以上 谢谢～

### 最后网上推荐了15款常用插件，随意使用
* Emmet Emmet 提供了一种非常简练的语法规则，然后立刻生成对应的 HTML 结构或者 CSS 代码，同时还有多种实用的功能帮助进行前端开发。
* Sublime​Linter——代码校验插件，支持多种语言；
* HTML5HTML5 bundle;
* Alignment 代码对齐；
* Bracket​Highlighter 括号高亮匹配；
* Git 整合 Git 功能的插件；
* jQuery 智能提示jQuery代码；
* LESS LESS 代码高亮；
* Js​Format JavaScript代码格式化；
* Tag HTML/XML标签缩进、补全和校验；
* LiveReload 即时刷新页面；
* Pretty JSON JSON美化；
* Can I Use 查询 CSS 属性兼容性；
* Coffee​Script Coffee​Script 代码高亮、校验和编译等；
* Color​Picker 跨平台取色器插件；