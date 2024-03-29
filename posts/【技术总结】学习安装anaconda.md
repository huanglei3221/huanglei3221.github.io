---
layout: post
title:  "学习安装anaconda"
date:   2023-03-22 15:07:24 +0800
tags: thinking
categories: 日常主要
---



主要根据这篇文章来做的：

https://www.bilibili.com/video/BV1S5411X7FY?p=21&vd_source=1115a1b57e46edddf88be0738ef3f5b2



先看看anaconda的架构： 

他是一个python+conda的集成开发环境， 并且它支持在不同的环境间进行切换， 就很nice， 如图： 

![image-20230322230550344](assets/images/学习安装anicoda/image-20230322230550344.png)



# 1安装anaconda

很简单， 就不赘述了，直接从官网下载安装即可。安装完成之后，可以创建一个环境，我创建的就叫hl1:

![image-20230322234457149](assets/images/学习安装anaconda/image-20230322234457149.png)



# 2创建虚拟环境

conda env list

conda create -n hl1 python=3.10

conda active hl1



# 3选择cuda版本：

也就是说要选择一个适合自己算力的cuda版本 

![image-20230322234339953](assets/images/学习安装anaconda/image-20230322234339953.png)



本机的3070 对应算力是8.6， 参见https://en.wikipedia.org/wiki/CUDA

![image-20230322234954220](assets/images/学习安装anaconda/image-20230322234954220.png)



![image-20230322235252517](assets/images/学习安装anaconda/image-20230322235252517.png)

那就要用11.1-11.4版本

![image-20230322235525814](assets/images/学习安装anaconda/image-20230322235525814.png)



# 4执行安装

安装pytouch的命令行参数： 参见官网， 选择cuda的版本

![image-20230322234213525](assets/images/学习安装anicoda/image-20230322234213525.png)

```
conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia
```

这个命令中的-c 表示通道， 如果国外网站比较慢， 就使用一些国内通道。





装完之后：

![image-20230323093349665](assets/images/学习安装anaconda/image-20230323093349665.png)



# 5安装后验证

![image-20230323095533579](assets/images/学习安装anaconda/image-20230323095533579.png)

![image-20230323095619140](assets/images/学习安装anaconda/image-20230323095619140.png)



# 6安装pycharm

安装专业版，就用程序员徐师兄提供的办法即可。

参见：https://blog.junxu666.top/p/7624.html



# 7配置pycharm

![image-20230323104646717](assets/images/学习安装anaconda/image-20230323104646717.png)



配置完成之后，我们就可以写代码执行了， 效果如下，这和在python命令行下的效果是一样的

![image-20230323110430078](assets/images/【学习笔记】学习安装anaconda/image-20230323110430078.png)





# 8给下载项目配置虚拟环境

![image-20230323110327431](assets/images/【学习笔记】学习安装anaconda/image-20230323110327431.png)



# 9怎么给pip配置proxy







