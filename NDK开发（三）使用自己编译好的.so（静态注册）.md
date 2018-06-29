# 使用含有第三方动态库的自己编译的.so #
###### 之前我们已经配置好ndk和写好.so 接下来我们使用这个.so
###### 使用.so一般是在libs下新建对应ABI文件夹放置.so，然后在Buildgradle下配置一下就好了，结果我发现无法使用。总是无法找到对应库
###### 后来询问大佬才了解，这是jni的注册方式导致的。一般来说我们使用的是静态注册，如果像第三方那样使用需要动态注册。大佬回答如下：
![](https://i.imgur.com/daS7dpE.jpg)

###### 这个向大佬提问的软件，是大佬的知识星球，有兴趣的朋友
###### 也可以加入，不过这是知识付费的哦，
######  这是加入的链接：[https://t.zsxq.com/7UbA6MV](https://t.zsxq.com/7UbA6MV "https://t.zsxq.com/7UbA6MV")
###### 扫一下即可！（给大佬广告了一波，emmm里面氛围还是不错的，可以认识其他大佬，嘿嘿嘿）
####   现在我们先说明一下自己编写的静态注册下的.so使用方式吧！

----------
## 一、
	1.新建一个项目（或者你的需要.so的项目）
	2.在新建项目的libs中新建对应的ABI文件夹（我这里.so 是armeabi-v7a的）
	3.将自己编译的.so和第三方.so放入进去
	4.配置app下的buildgradle 
	    sourceSets {
            main {
                jniLibs.srcDirs = ['libs']
            }
        }
	5.最重要的一步：将jni类复制过来（也可以在写Jni的时候将jni类的包打成jar，在依赖到项目中使用），要保证jni类的路径和你编译.so的路径一致！
	 在编译.so的时候，我们的包路径是：com.example.kevin.MyJniUtils，类名是JniUtils （参加NDK开发（二））
	 所以在我们这里要保持路径需要创建相同路径包，我这里包路径是相同的，只要复制MyJniUtils和JniUtils类就好了
	 
路径配置相关如下图：ps：红色方法没关系，不报错
	 ![jni路径一致](https://i.imgur.com/8inRF4J.png)
	
##### ps：实际开发中你们项目路径名不同，创建相同路径的包就可以了。
##### 如果路径不对你会报如下错误： java.lang.UnsatisfiedLinkError: No implementation found for XXXXXXXXXX
#####	  这里给大家提供一个网上解决办法，写的还好比我详细，你们可以看看
##### [https://blog.csdn.net/ouyang_peng/article/details/52997698](https://blog.csdn.net/ouyang_peng/article/details/52997698 "https://blog.csdn.net/ouyang_peng/article/details/52997698")
	
	6.最后在自己的MainActity中写方法调用吧！
	代码很简单，如下：
![](https://i.imgur.com/OUNthtF.png)
	
	7.成功调用！！！是不是很6呢？
	
![](https://i.imgur.com/2sOXGtz.png)

#  总结：这里只是讲到了使用含有第三方动态库的静态注册的自己编译的.so的方式！ 有点绕口不要紧，因为我相信我的配图你能懂~~~
#  如果有机会，我后面还会给大家分享动态注册的方式~~那要看我的学习程度了，加油吧~
#  三篇终于都写完了，如果有任何问题，都可以向我指出，谢谢啦！
#  期间感谢各路大佬的帮忙，这里就不点名啦，嘿嘿！