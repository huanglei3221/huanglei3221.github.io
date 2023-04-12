先装一个virtualbox。



然后下载ubuntu版本镜像，创建一个新的虚拟机：

![image-20230410102928470](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410102928470.png)

![image-20230410102905208](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410102905208.png)



enableEFI 不要勾选， 否则会导致启动失败， 虽然现在的主板已经多数使用UEFI系统而不是bios系统了， 

然后内存和处理器的话勾到一半就行了。

![image-20230410104112924](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410104112924.png)



虚拟硬盘不需要太大， 因为我们可以通过挂载目录添加硬盘

![image-20230410105709052](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410105709052.png)

![image-20230410110725861](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410110725861.png)

勾之后会在那个环境目录下创建一个vdi文件



![image-20230410105853276](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410105853276.png)



然后等它安装就行了：

![image-20230410110433333](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410110433333.png)





通过shared folders来共享文件夹，哈哈哈

![image-20230410112124060](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410112124060.png)





![image-20230410112044957](assets/images/【技术总结】用virtualbox+ubuntu+远程gdb搞一个linux开发调试环境/image-20230410112044957.png)





