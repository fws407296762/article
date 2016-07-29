#获取“-webkit-transform”的属性值方法

今天做了一个手机端的拖动效果，以前根本没做过，唉，移动端做的太少了，或者以前在意的太多了，现在不要在意太多了，一点一点的来，就好了。

我做的移动端拖动效果，用的是原生JS中的“touchstart”、“touchmove”、“touchend”,也就是手指点击、手指移动、手指移开三个步骤，其实和PC
端的思路是一样的。

主要的难点在PC端用的是left、top,在JS中很简单可以用offsetLeft、offsetTop获取到left、top的值，但是我们在移动端用的是transform:tranlate(X,Y),
当我们使用这个属性的时候，移动的并不是left、top，而是X轴的值和Y轴的值，那在这个时候，我们考虑需要获取的值就是X轴和Y轴的值了，
但是在JS中暂时还没有可以获取到这两个值的属性。这样我们就需要想办法来获取到X轴和Y轴的值了，首先我们得获取到transform的值，既然我们是运行在移动端的，
那我们就可以选择用getComputedStyle方法来获取到样式的值，在这里我们俩聊getComputedStyle。

在jQuery中，获取到CSS样式的属性值，我们可以用css()，在原生的JS中，有人选择用element.style.CSS属性值，嗯，这样确实可以获取到内联样式，但是获取不到CSS文件以及style标签中的样式，这个时候，
我们就需要用到getComputedStyle方法了，用法：getComputedStyle(元素,伪类或者null一般为null)，
它返回了一个**CSSStyleDeclaration**对象，在这个对象中有一个方法getPropertyValue(CSS属性名)，这个方法返回CSS对应的属性值

用法如下：

    var main = document.getElementById("main");
    var styleObj = window.getComputedStyle(main,null);
    var styleWidth = styleObj.gePropertyValue("width");//通过gePropertyValue方法获取到width的属性值
    var styleWidth = styleObj.width;//通过属性获取到width的属性值
    var styleWidth = styleObj[width];//通过属性获取到width的属性值，和上面的区别只是在与写法不同，没啥太多的区别

说了这么多，我们说的都是现代浏览器的做法，万恶的IE怎么可能会轻易的在这么有趣的事情上放过我们呢？在IE中，我们用到的方法是currentStyle(属性值)。
它返回的一样是一个object CSSStyleDeclaration，仅仅在IE下，本来我还想找到更多关于currentStyle方法的说明，但是在微软的官方说明中，对currentStyle的描述是一个对象，我暂时还不理解，我会在后面认真的看看关于这块的知识，写一篇更好的关于JS操作CSS的文章。
不过由于我们这篇文章是关于在移动端的操作，所以我们就不会用到currentStyle方法

首先我们写出结构：

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
        <title>拖动</title>
        <style>
            *{margin: 0;padding: 0;}
            #main{width: 50px;height: 50px;background: -webkit-linear-gradient(#eee,#ccc);border: 1px solid #ccc;border-radius: 25px;box-shadow:0 0 5px #ccc; -webkit-transform:translate(0px,0px);text-align: center;line-height: 50px;}
        </style>
    </head>
    <body>
        <div id="main">拖动</div>
    </body>
    </html>

知道了如何获取到属性值，那么我们就用代码来获取一下：

    var main = document.getElementById("main");//获取到我们要操作的DOM
    var transformVal = window.getComputedStyle(main,null).getPropertyValue("-webkit-transform");//获取到transform的属性值
    console.log(transformVal);//知道这里会在控制台中显示什么吗？...返回的是matrix(0,0,0,0,transformX的值，transformY的值);

到这里可能有更多的人开始迷茫了，我明明设置的属性是translate，为什么在这里返回的是matrix呢？呵呵，其实我也不知道...不过我们要获取的是transformX的值和transformY的值，暂时不关心这里

既然我们要获取到的是最后面两个数值，那我们就观察一下这个matrix的值，我们发现里面的值都是数字，其实我一直都在查JS中是否有直接获取到transformX、transformY的值的属性，但是一直都没有找到，可能JS中根本就没有获取这两个值的属性，既然如此，我们就考虑用正则来获取到数字

    var numReg = /-?[0-9]+/g;//可能有负数所以就加个负号
    var valAry = transformVal.match(numReg);//返回一个包含有数字值的数组
    var transformXVal = valAry[valAry.length - 2],transformYVal = valAry[valAry.length - 1];//返回transformX、transformY的值

就这样我们获取到了transformX、transformY的值，这样我们就可以来对这两个值进行操作了

    main.style.webkitTransform = "translate("+transformXVal+","+transformYVal+")";//设置-webkit-transform的值

下次我们再说如何来写一个移动端拖动效果