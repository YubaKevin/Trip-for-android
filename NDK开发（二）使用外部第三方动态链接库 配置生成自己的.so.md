# 一、在As 3.0.1中使用第三方动态库编译自己的.so
####	1.项目结构
		a.main下建立 jniLibs 文件夹,新建.so对应的ABI文件夹（我的是 armeabi-v7a） 用于存放第三方动态.so库
		b.cpp下创建include文件 用于放.h头文件 (若有其他cpp文件 放置在cpp文件夹下，创建include只是便于区分管理)