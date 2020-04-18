    作为后端服务，一般本地开发结束后都会上传到开发服务器，以提供给前端用来联调。
    当然也可以直接在你本地进行联调，这样的话你修改起来也比较方便，不需要进行同步代码或者重新搭载一套新的服务。
    如果你们不在同一个局域网内，那么想让对方请求到你本地的接口，就需要进行端口映射了～
    
#### 准备工作 ####
*     内网IP
*     映射软件


### 一、内网IP ###
    这个是必不可少的。也是很容易就可以满足的。这里就不做多解释了！
### 二、映射软件 ###
映射软件有很多种，我挑几个目前比较常见的几种

1. [ngrok](https://hsk.oray.com/)（花生壳)
    下载链接：https://ngrok.com/download
    
    支持系统：windows、linux、MacOs X、freeBSD
    
    安装：
    
    windos:双击exe文件即可安装使用
    
    mac：uzip进行解压即可
    
    使用：
    
    首先需要进行配置autoToken(账号凭证)，在[官网](https://dashboard.ngrok.com/)注册一个账号，然后在用户中心就可以看到我们的账号凭证
              
    ![ngrok_token](http://q8xla9gnn.bkt.clouddn.com/ngrok_token.jpg)
    
    windows：在ngrok命令行中执行
    
    `./ngrok authtoken 你的authtoken`
    
    Mac：在ngrok文件夹下执行
    
    `./ngrok authtoken 你的authtoken`
    
    执行成功后会提示：
    
    `Authtoken saved to configuration file:路径  这个文件是配置文件`
    
    在命令行界面继续执行ngrok http 80
    
    执行成功后就会如下图所示
    
    ![ngrok](http://q8xla9gnn.bkt.clouddn.com/ngrok.jpg)
    
    其中红框中标注的就是映射出去的外网地址，后边是你的本地地址，我这里映射的是本地的1234端口。即访问http://9499a328.ngrok.io就是在访问我本地的1234端口
    
    注意：这个程序需要保持运行，如果关闭程序，映射也会断开
    
2. [natapp](https://natapp.cn/)

    下载链接：https://natapp.cn/#download
    
    支持系统：windows,macOs X,linux
    
    使用：
    
    先去[官网](https://natapp.cn)注册账号
    
    申请免费隧道并配置你的端口
    
    ![natapp_buy](http://q8xla9gnn.bkt.clouddn.com/natapp_buy.jpg)
    
    ![natapp_my](http://q8xla9gnn.bkt.clouddn.com/natapp_my.jpg)
    
    ![natapp_setting](http://q8xla9gnn.bkt.clouddn.com/natapp_setting.jpg)
    
    然后解压你刚才下载的文件
    
    由于这个文件是没有执行权限的所以需要你给他增加一个可执行权限
    
    `chmod a+x natapp`
    
    执行
    
    `./natapp authtoken=上边设置时候得到的token值`
    
    ![natapp_run](http://q8xla9gnn.bkt.clouddn.com/natapp_run.png)
    
    这时候就执行成功了
    
    注意：这个程序需要保持运行，如果关闭程序，映射也会断开
   