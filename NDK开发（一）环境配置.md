#一、新建项目 
	1.注意勾选  include C++ support

![](https://i.imgur.com/eXX27ss.png)


#### 然后一路next就好了


# 二、NDK环境的配置
#####     1.将下载的NDK改名为ndk-bundle，拷贝NDK到对应的目录：
######     C: \Android_ studio_ sdk\ndk- bundle
#####     2.在path路径最后配置 ndk-bundle的路径：
######      {sdk的路径} \ndk -bundle; 
######     例：G:\ASSDK\sdk\ndk-bundle 
###### 测试环境是否成功：cmd下：ndk-build
#####     3.创建Project 在 local.properties 中
######     a.里面配置NDK路径：
######     ndk.dir=C\: \\Android_ studio_ sdk\\ndk -bundle
######     b.配置以下兼容老的NDK
######     android.useDeprecatedNdk=true    

    
# 在AndroidStudio 3.0.1 中的使用编译NDK
# 在NDK开发（二）文档中，
# 需要学习的同学请前往查看，谢谢！！！


