---
title: ubuntu 软件安装配置使用总结
date: 2016-01-22 23:36:00
tags: [linux, ubuntu]
categories: os
---

# ubuntu 软件安装总结（由xmind:Depends:java8-runtime but is not installed引出）cc

>不知道抽什么风，这几天PC上又用起了linux操作系统。其实之前断断续续用过linux，记得之前在大学安装ubuntu由于选择了安装第三方软件什么的，然后用着学校2M带宽（下载200多KB/S）在那里下载，晚上一些等到4/5点，然后爬床上睡了一会，连做梦都梦到了安装情况。之后也断断续续在自己的电脑上安装linux操作系统无数，但是没有真正用于工作、生活使用过。所以这次突然抽风使用上手还好没那么难，但是也是折腾的够呛，所以想记录下这几天折腾的情况，要是能让有缘人看到后有所帮助也不枉码这么写字，而且我也是不断从上google/baidu过来的。

<!-- more -->

* 操作系统我使用的版本是：Ubuntu 14.04.3 LTS   64位

### 安装输入法
现在有了搜狗输入法之后，安装输入法简单多了。只要到官网下载deb安装包，双击调用software center安装即可。如果缺少什么包，就使用sudo apt-get install命令安装即可，一般不会遇到什么大问题。

### WPS安装
官网下载wps linux版本的deb包，双击安装。安装一般不会有什么问题，但是打开excel一般会报错”缺少wingdings、wingdings2“什么之类的。下载安装一个制作好的deb包即可。[下载地址](http://download.csdn.net/detail/tao_627/7221953)

### thunderbird配置
这个是折腾的时间比较长的一个软件，因为比较工作使用的多。这个软件的强大之处就是它可以安装各种各样的插件，甚至你自己也可以开发一个。打开安装插件安装界面：`Tools->Add-ons`我这里主要安装的插件有如下几个：
* New Mail Attention  在收到邮件时状态栏图标会闪动且显示有多少未读邮件
* Reply to All as Cc 安装这个后在回复邮件的时候可以区分原来是主送的人员还是回复为主送，抄送的还是抄送，不会回复所有人都是主送
* Send Later  可以加强延迟发送的功能，可以指定发送时间
* Markdown Here 在写邮件的窗口会增加一个按钮，用于讲写完的邮件转换为html格式显示

配置方面：  
* 配置签名  
毫无疑问，这里可以直接支持html的格式，所以你可以做出很漂亮的签名，下面是一个简单的示例：  
``` html
<hr/>
<div style="background: #E6E6E6;color: #989494; margin: 2 auto;line-height: 25px; border: 1px solid #ddd;padding: 8px;">
<p style="margin: 2 auto;">
某某 <em>xxxxx@xxxx.com</em><br>
电话:13800000000 <br>
</p>
</div>
```
显示效果：  
![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160121165737156-321142275.png)

* 配置邮件签名和正文在回复时显示在最上面  
配置路径：右击账号->settings->点击右下角”Manage Identities“->edit->Composition & Addressing  
在这个页面勾选Automatically quote the original message when replying,并选择Then，“start my reply above the quote” and place my signature "below my reply (above the quote)。 Ok，搞定。

* 从excel或是wps复制表格到thunderbird会导致表格的边框消失  
这点也是折腾了好久，无数google/baidu都没有什么好的解决办法，应该是thunderbird的一个bug，2003年的就用人提交了这个bug，链接为
[bug链接](https://bugzilla.mozilla.org/show_bug.cgi?id=195012)

* thunderbird收件人栏显示多行  
可以安装这个插件`MRC Compose`。

thunderbird暂且折腾这么多吧，已经够呛的了，插件都是英文说明，总之折腾+google/baidu无数。

### chrome浏览器
这里折腾了好久主要是由于国内不能google，下载不了deb文件。最后通过最新的hosts文件搞定。

### Deepin Scort
这里一定要介绍这款截图神器，太符合中国人使用了（因为跟qq截图积极相似）。网上可以下载安装包安装，这里主要介绍下配置快捷方式：  
进入系统配置system settings->keyboard->Shortcuts->Custom Shortcuts 按+号增加一个，name里面填写：Deepin Scort，Command里面填写：deepin-scrot，然后点击刚才增加的项，按`Ctrl+Alt+A`完成配置。这里要提一下的就是在禁用快捷键时点击选项，然后按`Backspace`删除键即可。

### 有道词典、sublime、SecurtCRT、myeclipse、eclipse、TeamViewer。。。
这些工具安装基本没有碰到太多问题，所以就不详述。

### xmind安装
这个软件本来安装也没什么好说的，网上有deb下载。但是就是安装不上去。下载deb包后双击使用software center安装直接报错不能安装。
最终不得不使用`sudo dpkg -i xmind-7-linux-amd64.deb`命令，但是一直会报依赖关系错误“Depends: libgtk”之类的，说需要安装default-jre。但是软件已经可以使用了，因为我手动安装过了jdk，配置了环境变量之类的。
通过一系列的折腾也让我学习了不少：
安装后xmind可以使用但是打开software center就报错，导致什么软件都不能安装，如下截图：  
 ![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160121173226078-694613630.png)  
![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160121173250984-1831546467.png)
而且在上面的状态栏还会有一个报错小红圈，简直不能忍啊！  
![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160121173341578-1615273181.png)

无尽的折腾开始了：  
* 查看安装报错：`sudo dpkg --configure -a`
* 系统自带的软件中心都不能使用了，没办法，只能卸载了。命令：`sudo dpkg -r XMind`
由于报错提示没有jre，但是我已经安装了jdk，环境变量之类的也都配置好了。然后想可能是要使用软件中心安装jre就不会报错，所以卸载都又用软件中心安装jre，但是是使用jre安装又报错如下：  
![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160122223425047-298119634.png)
然后就是在安装xmind卸载无线循环中。  
后来仔细想想，既然安装了xmind可以使用就可以忽略软件中心的报错不管，但是不管软件中心又不能使用了，最后想到可以这样：  

1. 使用dpkg命令完成安装；
2. 备份所有安装生产的文件；期间涉及的命令：`find / -name xmind -print` 和 `find / -name XMind -print` ,将查找出来的文件使用命令`cp -r 源文件名 备份文件名` 进行备份，这里要记得对快捷方式进行备份，否则使用`win`键输入xmind不能找到软件，备份快捷方式的目录为：/usr/share/applications/,进入目录后使用命令` cp xmind.desktop xmind.desktop_bak`进行备份；
3. 使用dpkg命令卸载软件；
4. 使用备份的文件恢复软件的文件；恢复备份文件和快捷方式，可以使用`mv`命令；
5. 测试使用；测试一切ok，在terminal终端中输入XMind正常打开，按`win`键输入xmind正常找到软件后输入回车正常打开软件。  
至此xmind软件正常使用，软件中心也正常使用。

在次折腾期间还使用了些命令，这里一并小结下：  
* apt-cache search jre             # 查看可以安装的软件，查看前可以先更新下软件源，命令为：`sudo apt-get update`
* sudo update-alternatives --all   # 查看默认使用的软件，比如我们安装了多个版本的jdk，可以通过这个命令指定默认使用的jdk
* sudo update-alternatives --config java # 如果没有配置指定的软件，可以通过这个命令进行指定

### 字体的安装
由于我安装使用的ubuntu英文版，所以涉及到字体的安装。字体当然是下载最著名的"文泉驿微黑"，下载很简单，使用命令`sudo aptitude install ttf-wqy-microhei`  
但是由于我的系统是英文版的导致我修改字体设置的时候一直找不到中文显示字体，所以设置的时候找了一番，这里设置字体涉及到一个软件，名字是：Ubuntu Tweak，下面是软件截图：  
![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160122101413765-1717080273.png)
设置截图：  
![](http://images2015.cnblogs.com/blog/738557/201601/738557-20160122233750953-125894711.png)  
字体存放目录：/usr/share/fonts，配置文件存放目录：/etc/fonts  
Ubuntu 对中文字体的控制集中在一个文件，/etc/fonts/conf.d/69-language-selector-zh-cn.conf


### 修改ubuntu窗口颜色
1. 使用命令`sudo apt-get install dconf-tools`下载配置软件
2. 打开dconf配置工具找到配置项(在终端中输入：dconf-editor)，路径`org –> gnome -> desktop –> interface -> gtk-color-scheme`,配置值为:base_color:#CCE8CF
3. 进入系统配置system settings -> Appearance -> Theme 修改下主题，再修改回去，就能显示刚才修改的颜色了。
配置文件的位置在：/usr/share/themes/Ambiance/gtk-2.0/gtkrc文件中。

折腾这么几天，眼睛天天累的难受，不过也乐在其中。通过这么几天的折腾，目前ubuntu也基本上可以满足我的工作、学习需求，当然期间还涉及到播放器、eclipse、myeclipse、sublime等软件的安装就不一一记录了，主要是这些软件的安装也没有碰到太大的障碍。

### 驱动折腾
安装驱动图形化管理软件mintdrivers
```
sudo apt-get install gdebi
wget packages.linuxmint.com/pool/main/m/mintdrivers/mintdrivers_1.1.4_all.deb
sudo gdebi mintdrivers_1.1.4_all.deb
```
在dash中输入dirvers启动软件。

### jdk安装

1.删除自带的
2.安装Oracle官方的
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
3.管理
sudo update-alternatives --config java
