#关于Touch事件的介绍，略懂，请指教

这两天在研究移动端，在移动端中，我用的是原生JS，在原生JS中对移动端增加了一个事件对象---Touch，在MDN中， ziyunfei对Touch的翻译是：

>每个Touch 对象代表一个人和触摸设备(例如触摸屏和触摸板)接触的触点.

理解这句话的意思：当我们和触摸屏或者是触摸板进行肌肤之亲的时候，接触一个点就会产生一个Touch对象。一个手指触摸在手机屏上就会产生一个Touch对象，两个手指就会产生两个Touch对象...
这些Touch对象是存放在Touchlist对象中的，而Touchlist对象则是在TouchEvent对象中的，也就是说，当我们手指触摸到手机屏幕的时候，就会产生一个TouchEvent对象，
TouchEvent对象会产生一个TouchList对象，TouchList对象则会产生一个Touch对象，这样我们就可以调用Touch对象中的属性了。顺着这个思路下去，我们脑海中有了这样的一条线：

>TouchEvent.Touchlist[index].Touch属性

既然这样，我们就按照顺序，先说说 **TouchEvent** 对象，在TouchEvent对象中有三个属性可以产生TouchList对象

>_TouchEvent.changedTouches_

>这个属性会返回一个TouchList对象，这个对象包含了代表所有从发生了状态改变的触点的Touch对象，例如：我开始用一个手指在手机上滑动，
那么changedTouches就只有我这个手指，如果在下一秒钟，我用另一只手指点击屏幕，这个时候，changedTouches中Touch对象就是新增的手指的Touch对象了，
，以此类推，当我的其中一个手指离开了屏幕，这个时候，changedTouches中的Touch对象就是离开触点的手指对象了，同时新增了一个，changedTouches对象的Touch对象就是新增的这个了

>_TouchEvent.targetTouches_

>这个属性我还不知道是什么意思！

>_TouchEvent.touches_

>这个属性就会返回一个TouchList对象，这个对象包含所有在屏幕上的Touch对象，有几个手指就有几个Touch对象

然后我们说说**TouchList**对象

**TouchList**对象包含有触摸平面上的所有触点，比如，你有一个手指就有一个触点，这时**TouchList**对象就有一个Touch对象，
你有三个手指就有三个触点，这时**TouchList**对象就有三个Touch对象...

>_TouchList.length_

>Touch对象的数量，也就是和触摸屏幕接触的手指个数，有一个length就为1，有两个length就是2

>_TouchList.identifiedTouch()_

>

