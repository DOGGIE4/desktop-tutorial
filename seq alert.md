# 一、seq可以过滤出某些特定的log，并且通过邮箱发送
先去add signals
先输入搜索条件，然后点搜索栏这个图标，右边就会出现新的signals，可以更改一下名字
直接清除搜索框之后，输入@Level = 'Error'，就会自动在右边的signal里面加入@Level = 'Error'
此时我们就有一个名字叫Smarties Error的signal，并且他的filters分别为图上两条

![image.png](https://upload-images.jianshu.io/upload_images/29177961-2b084e1816b6509d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来需要在Apps引入Seq.App.EmailPlus包
![image.png](https://upload-images.jianshu.io/upload_images/29177961-db6f746b444fa315.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

直接输入Seq.App.EmailPlus，intsall就好

![image.png](https://upload-images.jianshu.io/upload_images/29177961-a9bcf20c90d45b73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

成功后我们add instance
![image.png](https://upload-images.jianshu.io/upload_images/29177961-43ef4cd15f2ff68d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29177961-7f2bebbc1d8c5417.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 二、邮箱设置

ps：这只是QQ邮箱的例子，不同的邮箱操作不一样，可能需要再研究一下
step1:
![image.png](https://upload-images.jianshu.io/upload_images/29177961-a604605874909d55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

step2:
![image.png](https://upload-images.jianshu.io/upload_images/29177961-e262a3c5ba62ce3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

step3:
找到这个并开启
![image.png](https://upload-images.jianshu.io/upload_images/29177961-26e0712f2bc1ed2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

初次打开这个服务状态是关闭的，去开启的时候需要验证，验证成功之后就会生成一串密码了，然后把这个密码填到add instance时，Password那一栏

ps：这个接收者的邮箱是可以填好多个的，用逗号隔开就好

# 三：Alerts
![image.png](https://upload-images.jianshu.io/upload_images/29177961-db6cb0a9c23628f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/29177961-229041035f9d8010.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
