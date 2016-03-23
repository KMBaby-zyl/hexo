title: 图片优化 base64 vs 雪碧图
date: 2015-08-28 19:14:23
tags: 优化
---
# 图片优化－ base64 vs 雪碧图


页面渲染的过程中太多的request会增加页面的加载时间和渲染时间。为此我们通常会打包css，js文件，把图片打包成雪碧图。有时候我们还可以使用base64把图片资源直接写入css文件，这个做法非常的方面。

### 雪碧图

雪碧图是把各种小图片资源拼凑在一起，成为一张大图片资源。通过 background-position 来设置图片位置起始点，配合 width，height 来获取需要的图片。

EXAMPLE:


![Alt text](http://images.apple.com/global/nav/images/globalnav.png)

1. 优点
    * 把大量图片请求合并为一个图片请求
2. 缺点
    * 难以操作和更新，没有工具的帮忙把各种小图拼在一起不是个容易的工作。
    * 如果雪碧图中有太多留白，不够紧凑会增加额外的空间消耗。
    * 如果雪碧图过于紧凑，会增加一个图片元素中显示了部分其他图片的风险。


### Base64

Base64的形式为 Data URI

        data:[<mime type>][;charset=<charset>][;base64],<encoded data>

在css中

    selector {
	background: url(data:image/gif;base64,R0lGODlhEAAQAMQAAORHHOVSKudfOulrSOp3WOyDZu6QdvCchPGolfO0o/XBs/fNwfjZ0frl3/zy7////wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH5BAkAABAALAAAAAAQABAAAAVVICSOZGlCQAosJ6mu7fiyZeKqNKToQGDsM8hBADgUXoGAiqhSvp5QAnQKGIgUhwFUYLCVDFCrKUE1lBavAViFIDlTImbKC5Gm2hB0SlBCBMQiB0UjIQA7) no-repeat left center;
}

在html中

    <img width="16" height="16" alt="star" src="data:image/gif;base64,R0lGODlhEAAQAMQAAORHHOVSKudfOulrSOp3WOyDZu6QdvCchPGolfO0o/XBs/fNwfjZ0frl3/zy7////wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH5BAkAABAALAAAAAAQABAAAAVVICSOZGlCQAosJ6mu7fiyZeKqNKToQGDsM8hBADgUXoGAiqhSvp5QAnQKGIgUhwFUYLCVDFCrKUE1lBavAViFIDlTImbKC5Gm2hB0SlBCBMQiB0UjIQA7" />


使用Base64可以达到我们要的优化效果，把所有的小图放入css文件中。我们把大量的图片请求都合并在了css文件中。

1. 优点
    * 把大量的图片请求合并在了css文件中
    * 容易更新
2. 缺点
    * 会增加25%的图片资源大小，使用了gzip后 只增加10%。
    * 不支持IE6和7

### 选择哪个？

两者都有人在使用，很多情况下一起配合使用会达到更好的效果。

但如果你的图片需要支持不同设备的时候，雪碧图十分好用。

    selector {
	    background-image: url(our_bg_image.png); /* will be downloaded on common     displays */
    }
    /* Stylesheet for Retina */
    @media screen and (-webkit-min-device-pixel-ratio: 1.5), screen and (min-device-pixel-ratio: 1.5) {
	    selector {
		    background-image: url(our_bg_image@2x.png); /* will be downloaded on retina displays */
	    }
    }

如果我们不需要支持ie < 9 我们也可以使用base64

    <script>
    var retina = window.devicePixelRatio > 1 ? true : false;
    if (retina) {
	    // the user has a retina display
    }
    else {
	    // the user has a non-retina display
    }
    </script>


以上





