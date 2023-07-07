# 一、SourceTree概述
SourceTree是一款免费的图形界面(GUI)工具，用于与Git版本控制系统进行交互。它提供了一个直观和可视化的界面，使用户可以更轻松地使用Git进行代码管理和团队协作。你可以通过简单的点击、拖放和菜单操作来执行各种Git操作，而不需要记住复杂的命令。

# 二、 Souretree使用方法
## 1. 在SourceTree新建克隆

![image.png](https://upload-images.jianshu.io/upload_images/20387877-1a23a363294904e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2.  将本地项目推送至GitLab在“源路径/URL”输入框中，输入GitLab仓库的URL。本地地址和名称会自动生成，可以自行修改。

![image.png](https://upload-images.jianshu.io/upload_images/20387877-677fd4e293a72316.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.  克隆成功页面如下

![image.png](https://upload-images.jianshu.io/upload_images/20387877-6d2b1c2d83bd4c6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，本地仓库目录显示如下，项目已拉到本地

![image.png](https://upload-images.jianshu.io/upload_images/20387877-2a9eb773fc70b364.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. 我们在本地目录上新建一个文件

![image.png](https://upload-images.jianshu.io/upload_images/20387877-672b32a5fc6ee146.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时SourceTree会检测到这个文件，并且显示状态

![image.png](https://upload-images.jianshu.io/upload_images/20387877-0fa615e663d1ed4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.  我们选中文件可以进行暂存，文件暂存后进入“已暂存文件”区域。
![image.png](https://upload-images.jianshu.io/upload_images/20387877-aa208064c3043c6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 6. 选中已暂存到文件，可以输入备注后提交

![image.png](https://upload-images.jianshu.io/upload_images/20387877-0d0d8c765126f85e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 7. 此时还只是暂存，并没有推送，需要点击“推送按钮”后才可以在gitlab的项目中显示新加的文件。

![image.png](https://upload-images.jianshu.io/upload_images/20387877-98a08c0f19ea488c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 8. 推送后打开gitlab，即可看到我们推送成功的文件

![image.png](https://upload-images.jianshu.io/upload_images/20387877-7a0585c7facd202f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 二、 创建分支
在Sourcetree（或其他版本控制工具）上创建分支是为了实现团队的并行开发、版本控制、功能开发和错误修复以及代码审查和测试等目的。它提供了一种结构化和有效的方式来管理代码库，并促进团队合作和开发流程的顺畅进行。
注：分支的命名需要体现出此分支的作用，用“-”代替空格，此外，可以借助ChatGPT帮助命名，例如对ChatGPT发送：你现在是一个命名大师，我现在创建一个分支对作用是xxx,我该怎么命名。

## 1. 创建分支，初始状态下只有一个master分支，点击“分支”即可创建分支，可以对新分支进行命名

![image.png](https://upload-images.jianshu.io/upload_images/20387877-dcd59d7deee4d79b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时点击左侧分支栏，即可查看到创建成功的分支

![image.png](https://upload-images.jianshu.io/upload_images/20387877-90f722a0520a8452.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2. 我们可以在分支下可创建新的文件

![image.png](https://upload-images.jianshu.io/upload_images/20387877-fe70f63d9dd899ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意：file1是我们在master分支下创建的文件，file2是在分支learn-git-essentials下创建的文件

## 3. 提交文件

![image.png](https://upload-images.jianshu.io/upload_images/20387877-a88789e91c6db5cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. 推送文件

![image.png](https://upload-images.jianshu.io/upload_images/20387877-98ade47251846d1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

推送之后，我们到gitlab去查看会发现多了一个分支learn-git-essentials

![image.png](https://upload-images.jianshu.io/upload_images/20387877-d82e79c6c6db949a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意，新创建的文件只在分支中显示，并不会在master上显示

![image.png](https://upload-images.jianshu.io/upload_images/20387877-77d39d5c48c6d269.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要想在master上显示file2 ，我们需要进行合并分支的操作
# 三、合并分支

## 1. 创建一个issue，创建issue并与合并分支相关联有助于团队更好地组织和跟踪工作，并提供更全面的上下文和记录。 注：title的描写要表述出目的

![image.png](https://upload-images.jianshu.io/upload_images/20387877-efc9c302493fee0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建成功后如下所示，最上方#1是自动生成的编号

![image.png](https://upload-images.jianshu.io/upload_images/20387877-516cc9e03728b560.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2. 申请合并（merge request）中选择source（把什么），target（合并到哪里去）

![image.png](https://upload-images.jianshu.io/upload_images/20387877-6c20c6e94207bdde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3. Close #之前生成的事件编号 点击Create merge request，命名也要体现出目的，尽量不要使用之前提交时编辑的备注，因为如同时提交多个，它只会显示最新的那个，容易出现混乱。

![image.png](https://upload-images.jianshu.io/upload_images/20387877-f10a59a69a82b51a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. 此时点击合并，master上就会显示原来分支上新加的文件

![image.png](https://upload-images.jianshu.io/upload_images/20387877-d0ad8d713e243c6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

成功后如下所示：

![image.png](https://upload-images.jianshu.io/upload_images/20387877-d59780cec0bbcfd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

PS:一般情况下，只需要先将本地的代码提交推送到远端的分支，然后在gitlab上发起merge request请求，由管理者进行learn-git-essentials和master分支的合并操作。