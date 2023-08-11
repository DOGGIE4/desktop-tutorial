# 一、 什么是MQ？

MQ全称为Message Queue，即消息队列，消息队列是应用程序与应用程序之间通信的一致方法，即在消息的传输过程中保存消息的容器，多用于分布式系统之间的通信。
它本质上是个队列，原则是先进先出，队列里存放的元素是一条条 Message 。

![image.png](https://upload-images.jianshu.io/upload_images/29177961-72ea6582f295c1f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

数据放进message称为生产者，将数据拿出message称为消费者

# 二、MQ的作用：

##  1. 解耦

#### 未使用MQ之前

如下图有一个订单系统直接调用库存系统，支付系统，物流系统

![image.png](https://upload-images.jianshu.io/upload_images/29177961-c3db14c298f9a361.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


由于订单系统与库存系统是耦合的，如果库存系统出现了错误，可能也会影响订单系统不能工作

![image.png](https://upload-images.jianshu.io/upload_images/29177961-b793338e4e3879bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如果要新接入一个系统我们还要改变订单系统的源码，并添加一系列代码来实现调用

![image.png](https://upload-images.jianshu.io/upload_images/29177961-e33975123d8fc3df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这就暴露了一个巨大的缺点，系统的耦合性越高，容错性就越低，可维护性就越低

#### 使用MQ之后

订单系统只要将对应的数据发送到MQ即可，而库存系统，支付系统，物流系统只需MQ中取出对应的数据即可

![image.png](https://upload-images.jianshu.io/upload_images/29177961-fe4503e724c07383.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如果库存系统出现了错误，也不会影响到订单系统，比如库存系统由于访问量过大突然卡了几秒钟，几秒钟之后可能就好了，好了之后再到MQ中取出对应的数据即可

![image.png](https://upload-images.jianshu.io/upload_images/29177961-5dc1912dcc7d6d9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


比如我们要接入一个系统，我们不需要再修改订单系统的源码，直接让该系统去MQ中取出对应的数据即可

![image.png](https://upload-images.jianshu.io/upload_images/29177961-a06db1b929cee00d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2. 异步

#### 未使用MQ

有一个订单系统如图

![image.png](https://upload-images.jianshu.io/upload_images/29177961-d7f04c803f25e91a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


用户下订单一共需要300+300+300+20=920ms，订单系统需要一一调用数据库，库存，支付，物流系统，耗时极大

####  使用MQ之后

![image.png](https://upload-images.jianshu.io/upload_images/29177961-2b1bf0da11003c24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


用户下订单需要20+5=25ms

用户下单后，订单系统只需要进行数据库查询和将数据发送到MQ即可告诉用户下单成功，剩下的只需让库存，支付，物流系统自行去MQ中取出数据即可

## 3.削峰。
假设系统A在某一段时间请求数暴增，有5000个请求发送过来，系统A这时就会发送5000条SQL进入MySQL进行执行，MySQL对于如此庞大的请求当然处理不过来，MySQL就会崩溃，导致系统瘫痪。

如果使用MQ，系统A不再是直接发送SQL到数据库，而是把数据发送到MQ，MQ短时间积压数据是可以接受的，然后由消费者每次拉取1000条进行处理，防止在请求峰值时期大量的请求直接发送到MySQL导致系统崩溃。

但是这样一来，高峰期产生的数据势必会被积压在MQ中，高峰就被“削”掉了。但是因为消息积压，在高峰期过后的一段时间内，消费消息的速度还是会维持在1000，直到消费完积压的消息,这就叫做“填谷”

![image](https://upload-images.jianshu.io/upload_images/24133009-d2e0d0197a778692?imageMogr2/auto-orient/strip|imageView2/2/w/1069/format/webp)

原文：https://www.jianshu.com/p/8a50d1e51e09

