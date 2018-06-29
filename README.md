# 【Chrome】Chrome-devtools：对ios-safari移动端的H5页面进行调试（ios-webkit-debug-proxy）


# 说在前头

> 要看干货的，可跳过该“说在前头”章节

该类文章，百度上“一挖一麻袋”，但说句不好听的，“一个能打的都没有”，总结如下：

 1. scoop：win7下，搞这个都烦的一批，到最后“scoop install ios-webkit-debug-proxy”，跟我说要翻墙？
 2. 下了一堆有的没的npm插件，最后还是没法用。
 3. 到最后一步了，NMP跟我讲“能看，但没法debug调试”，我砸键盘了好吗？
 4. ......

吓得我赶紧放了一张老婆镇楼
![镇楼](https://img-blog.csdn.net/20180627095637164?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
既然写了这篇文章，说明这类问题都已经解决了，那我们言归正传，讲讲我们要 **实现什么**？

> chrome-devtools对IOS移动端的H5页面进行调试

简单来说，就是 ——

#### 我对“提交”btn绑定了“console.log（1）”则，我点击移动端H5页面的“提交”时，PC端的Chrome会输出1

有人问了

**Q：我移动端的H5页面一样可以放到PC端的chrome下进行调试啊，为什么要调试真机呢？比如：**
![反方意见](https://img-blog.csdn.net/20180627100456662?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
**A：这个还是要看需求的，如果你的业务不需要，自然也就不需要了，举个栗子：**

 1. App嵌入H5时，你如何调试一些原生交互，如何知道原生是否定义了方法？
 2. 对H5页面进行多指触摸的时候，PC端如何模拟并响应调试？
 3. 运用一些第三方插件库或API的时候，内含严苛的判断，非真机不好调试，咋弄？
 4. ......

#### 接下来就是干货了，让你任意调试H5页面，WebView页面，查看该APP是否存在WebView以及找到它的线上地址！

-------------------

## 一、环境（测试成功的案例）

 1. PC chrome浏览器（版本45、46）
 2. IOS safari浏览器（11.2.6）
 3. win系统（win7、win10）
 4. 一根USB数据线

 -------------------

## 二、软件工具准备

我全放在github集成了，开不开心，please star，fork

[github：chrome_ios_safari_ios-webkit-debug-proxy](https://github.com/gs3170981/chrome_ios_safari_ios-webkit-debug-proxy.git "chrome_ios_safari_ios-webkit-debug-proxy")

> 这里最好“Download ZIP”，下拉不知道要拉几年

当然为了防止“Download ZIP”也很慢，所以我把文件的源地址在这儿逐一列出，可自行分别下载，都是必须的

 1. [iTunes](https://www.apple.com/itunes/download/win64 "chrome_ios_safari_ios-webkit-debug-proxy")
 2. [ios-webkit-debug-proxy-1.8-win64-bin.zip](https://github.com/google/ios-webkit-debug-proxy/releases/download/v1.8/ios-webkit-debug-proxy-1.8-win64-bin.zip "chrome_ios_safari_ios-webkit-debug-proxy")
 3. [Chrome45.0.2454.85_64_installer](https://dl.google.com/chrome/win/747508CB2FEC3FAD/45.0.2454.85_chrome64_installer.exe "chrome_ios_safari_ios-webkit-debug-proxy")

> TIPS：谷歌为45版本是有原因的，当然最新的也可以，后面会讲，总归会麻烦一点

当然如果失效的话，来我github里慢慢down，(*^▽ ^*)

 -------------------

## 三、设置iphone Safart浏览器

设置 > Safart浏览器 > 高级 > 打开Web检查器

如下图：
![iphone01](https://img-blog.csdn.net/20180629101043209?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 -------------------

## 四、打开iTunes，以及数据线连接

iphone连接上PC后，会有“是否信任该计算机”的提示，都按确认，iTunes也将会检索到该iphone设备。有以下图，则说明连接成功。
![iTunes01](https://img-blog.csdn.net/20180629101707925?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 -------------------
 
## 五、打开ios-webkit-debug-proxy-1.8-win64-bin解压的目录

例如，我是解压在F盘的，放个图观摩下
![win01](https://img-blog.csdn.net/20180629102005487?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
打开cmd，进入该盘下，执行以下命令

> ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html

出现以下提示，则连接成功！
![cmd01](https://img-blog.csdn.net/20180629102312634?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 -------------------
 
## 六、核心教程

上面说到端口监听为9221，则打开chorme45输入http://127.0.0.1:9221/
![45-01](https://img-blog.csdn.net/20180629102853416?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
点击“逗逗”（也就是你的设备），你会发现里面是空白的，说明你还没有打开H5页面，用safari打开一个H5页面，刷新chrome，就会发现多了几条数据
![45-02](https://img-blog.csdn.net/20180629103435753?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
划重点，**你对你需要调试的页面，进行审查元素**，我这边举例调试“百度”
![45-03](https://img-blog.csdn.net/20180629103548581?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
将该地址复制到地址栏，则进入调试界面！
![45-04](https://img-blog.csdn.net/20180629103947544?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![iphone02](https://img-blog.csdn.net/2018062910401062?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
宁哥！宁哥！哦，走错片场了。
控制台输入
> alert('Hello World!')

移动端弹出，成功
![iphone03](https://img-blog.csdn.net/20180629104434161?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

> 控制台死活按不下去的，说的就是你，高版本chrome，等会聊

我们对“百度一下”这个btn进行一个DIY的绑定，试试控制台是否真的好用
![45-05](https://img-blog.csdn.net/20180629105108818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
点击ios上的“百度一下”，果然弹出了true，OK

接下来，我们试试断点，点进我们刚才绑定的方法，进入chrome缓存中，进行断点调试
![45-06](https://img-blog.csdn.net/20180629105259306?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
点击“百度一下”，发现进不去！接下来就是黑科技 —— 

### 双击右上角箭头按钮
![45-07](https://img-blog.csdn.net/20180629105647236?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![45-08](https://img-blog.csdn.net/20180629105726512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
再点击“百度一下”发现进来了！输入this，返回的就是当前btn dom元素
![45-09](https://img-blog.csdn.net/20180629105756166?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
在此鼓掌(*^▽ ^*)

> 你说你怎么知道这儿有效的？......一口老血，一把辛酸泪

 -------------------
 
## 七、无法调试的另类问题集合

#### 1、Chrome高版本控制台无法输入的问题

**问题描述**：chrome其他版本，也一样只需要双击右上角的断点箭头，就能debugger调试，不过控制台却死活按不下去
**解决方案**：按F12，控制台有一堆抛错，每次控制台回车，右边会增加一条error提示
![58-01](https://img-blog.csdn.net/20180629115601477?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
点击控制台抛错提示，将该行判断“if(error){console.error(error) ......”改成“if(!error){console.error(error)......”
也就是把它**非**掉，然后ctrl+s解决问题。
**TIPS**：如果被刷新的话，又得重来，就这点来说，并不是很方便。

*在这儿，请教一下大神，有没有方式可让inspector.js永久映射到workspace，解决刷新重来的方式*

#### 2、Chrome断开连接

**问题描述**：chrome下出现“Detached from the target”字样，调试中止
![45-10](https://img-blog.csdn.net/20180629135126672?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
**解决方案**：cmd开启的服务“ios_webkit_debug_proxy”重开，清除chrome缓存，重启chrome解决，以及不要干一些黑操作，该调试页面极不稳定。

#### 3、cmd打开debug_proxy服务时，连接不上手机，未有响应

**问题描述**：执行“ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html”该语句时，如下展示
![cmd02](https://img-blog.csdn.net/2018062913595574?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01ja3lfTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
没有连接设备的响应，或返回error，warn等提示未有设备连接
**解决方案**：查看iTunes是否连接上你的设备，重新插拔一下USB接口，重启服务乃至重启电脑进行尝试，如仍不行，请下载其他版本进行尝试

[github：google/ios-webkit-debug-proxy](https://github.com/google/ios-webkit-debug-proxy/releases)

**TIPS**：要注意的是，如果这步已经跪了，那打开chrome输入9221的端口也一定是空白页。

#### 4、Chrome低版本无法调试的问题

**问题描述**：在30-版本的情况下，无法调出inspector.html调试页面

**解决方案**：30+版本，已经废弃devtools.html页，30以下chrome应在url中输入

> chrome-devtools://devtools/devtools.html?ws=localhost:9222/devtools/page/1

则进入调试页。

**TIPS**：page/1与9222是端口9221中审查dom元素内的传值，应保持一致。

 -------------------

## 关于

make：o︻そ╆OVE▅▅▅▆▇◤（清一色天空）

blog：http://blog.csdn.net/mcky_love

掘金：https://juejin.im/user/59fbe6c66fb9a045186a159a/posts

lofter：http://zcxy-gs.lofter.com/

github：https://github.com/gs3170981?tab=repositories

sf：https://segmentfault.com/u/mybestangel/articles

-------------------


## 结束语

鬼知道我尝试了多少个解决方案，下载了多少个chrome版本，那些无用的文章写出来，加上一堆的分享党占屏百度首页
![你的良心不会疼吗？](http://ww1.rs.fanjian.net/c/26/1b/33/f2265efc6f1b861eb533d906df2afe7e.jpg)
也难怪说，谷歌是最好的搜索引擎，心酸。