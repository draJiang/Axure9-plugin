# Axure 9 文档阅读体验优化

## 功能特性
1. 节点突出显示
页面名称尾部加 * 符号

![image](https://upload-images.jianshu.io/upload_images/7756382-f879f9cfe2102e5a?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

导出时会突出显示

![image](https://upload-images.jianshu.io/upload_images/7756382-e9e19e1bbd50effd?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 自动展开站点地图
    浏览 Axure9 文档时站点地图不会自动展开，需要手动开启，非常麻烦
4. 隐藏垃圾桶
    阅读者不会看到垃圾桶及其子页面的内容
    
![image](https://upload-images.jianshu.io/upload_images/7756382-c90a883716c2e464?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 打开任意 `.html` 文件将统一定为到首页（index.html）
因为 Axure 导出的文档，如果不是通过 `index.html` 或 `start.html` 打开则不会显示左侧的站点地图，影响阅读体验

![image](https://upload-images.jianshu.io/upload_images/7756382-b81cfd46071497bb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 使用方法
下载优化后的 `sitemap.js` 、`init.temp.js` 文件（请看附件）

找到 Axure9 的安装目录，我是装在 C 盘
`C:\Program Files (x86)\Axure\Axure RP 9\DefaultSettings\Prototype_Files\plugins\sitemap\`

先把上述目录下的 `sitemap.js` 文件改下名称，避免直接替换（后面不想用还可以切换回去）

![image](https://upload-images.jianshu.io/upload_images/7756382-267e9610f705a017?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

粘贴**优化后的 sitemap.js 文件**，最后是这样

![image](https://upload-images.jianshu.io/upload_images/7756382-1ca194c78c087b40?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同理，将 `init.temp.js` 放到此目录下：

`C:\Program Files (x86)\Axure\Axure RP 9\DefaultSettings\Prototype_Files\resources\scripts\axure\`

## 实现原理
Axure 导出/预览文档时会将 `\Axure RP 9\DefaultSettings\` 文件复制到预览对象中；
其中 `sitemap.js` 负责实现左侧站点地图的生成逻辑，通过修改其中的逻辑实现上述功能

### sitemap.js

```JavaScript
if (document.getElementById('sitemapControlFrameContainer').className == 'selected') {
            //如果站点地图已经展开则忽略
        } else {
            //如果站点地图未展开
            $axure.player.showPlugin(1);//显示或隐藏站点地图
        }
```

```JavaScript
// 如果节点名称 = 垃圾桶，则隐藏此节点及其子节点
        if(hasChildren) {
            margin = (9 + level * 17);

            if(node.pageName=='垃圾桶'){
                returnVal = "<li class='sitemapNode sitemapExpandableNode' style='visibility:hidden'><div><div class='sitemapPageLinkContainer' style='margin-left:" + margin + "px'><a class='sitemapPlusMinusLink'><span class='sitemapMinus'></span></a>";
            }else{
                returnVal = "<li class='sitemapNode sitemapExpandableNode'><div><div class='sitemapPageLinkContainer' style='margin-left:" + margin + "px'><a class='sitemapPlusMinusLink'><span class='sitemapMinus'></span></a>";
            }

            // returnVal = "<li class='sitemapNode sitemapExpandableNode'><div><div class='sitemapPageLinkContainer' style='margin-left:" + margin + "px'><a class='sitemapPlusMinusLink'><span class='sitemapMinus'></span></a>";

        } else {
            margin = (19 + level * 17);
            returnVal = "<li class='sitemapNode sitemapLeafNode'><div><div class='sitemapPageLinkContainer' style='margin-left:" + margin + "px'>";
        }

```

```JavaScript
// 如果节点尾部字符 = * 则设置特殊样式
        if (node.pageName.substring(node.pageName.length - 1, node.pageName.length) == '*') {
            returnVal += "'></span><span class='sitemapPageName' style='color:#ff3366'>";
        } else if (node.pageName == '垃圾桶') {
            returnVal += "'></span><span class='sitemapPageName'>";
        } else {
            returnVal += "'></span><span class='sitemapPageName'>";
        }
```

### init.temp.js

``` JavaScript
//如果不是从 index.html 打开页面，则重定向到 index.html
        var myUrl = window.location.href; //当前页面 URL
        var indexStart = myUrl.lastIndexOf('/')
        var thisUrl = myUrl.substring(indexStart + 1) //当前页面名称

        myUrl = myUrl.substring(0, indexStart + 1) //截取页面名称前的路径
        myUrl = myUrl + 'index.html' //拼接路径

        //是否存在父级页面（存在则表示已经在 index.html 中打开）
        if (window.top == window.self) {//不存在父页面
            window.location.replace(myUrl); //重定向到 index.html
        } else {
            console.log('当前在 index.html 中打开')
        }
```


