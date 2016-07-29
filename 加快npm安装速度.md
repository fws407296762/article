## NPM速度太慢，感觉生命都浪费在了等待上

为了解决史前社会访问NPM速度慢，有聪明的人类发明了CNPM，速度快了不少，但是也有很多遗憾。

很多包安装并不会理睬CNPM的存在，例如react-natice, yeoman等等。装一半还是要Ctrl + C 中断，然后再 cnpm install，感觉相当不舒服。

### npm 参数

### —verbose 加参数看进度

为了解决安装时卡着没反应，聪明的人类发现了可以加参数，例如：

    npm install hexo -g --verbose

这样可以一直看到进程，某种程度上缓解焦虑症，然并卵，它不能从根本上解决问题。

### —ignore-scripts 跳过安装脚本错误

跳过一些安装脚本报错，反正先给我装上再说啊

    npm install hexo -g --verbose --ignore-scripts

**smart-npm**

[github](https://github.com/qiu8310/smart-npm)

这里有个少年，想到了封装npm，按照操作来自动判断使用npm还是cnpm，感觉上智能了很多。只是侵入感太强，要修改很多东西。

**nrm**

[github](https://github.com/qiu8310/smart-npm)

另一个少年，想到了切换 registry 源，可以先测试各地 npm 镜像速度，然后修改注册源的地址，从而达到快速访问效果。

写在最后

如果可能的话，使用cnpm搭建私有源，配合nrm感觉上是最靠谱的。

如果有一个高速VPN，以上都不是问题了～