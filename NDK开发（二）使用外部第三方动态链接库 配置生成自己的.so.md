# 一、在As 3.0.1中使用第三方动态库编译自己的.so
####	1.配置项目结构
		a.main下建立 jniLibs 文件夹,新建.so对应的ABI文件夹（我的是 armeabi-v7a） 用于存放第三方动态.so库 （一定要对应，这个的很重要）
		b.cpp下创建include文件 用于放.h头文件 (若有其他cpp文件 放置在cpp文件夹下，创建include只是便于区分管理)


项目结构图如下： 

![项目结构图](https://i.imgur.com/MjzRHwl.png)
	

----------

####   2.配置app下的buildgradle
    
	   a.在android{ } 内中配置
	
		defaultConfig {    
   	    externalNativeBuild {
            cmake {
                 abiFilters "x86_64" "armeabi-v7a" "arm64-v8a"			//可以全部 也可以任选其一，和.so要对应起来
                cppFlags "-frtti -fexceptions"
            }
        }
        
        sourceSets {
            main { 
                JNILIBS.SRCDIRS = ['SRC/MAIN/JNILIBS']
            }
        }
                }
          
        buildTypes {
        debug{
            minifyEnabled false
            ndk {
                abiFilters "x86_64" "armeabi-v7a" "arm64-v8a"        //可以全部 也可以任选其一，和.so要对应起来
            }
        }
        release {
            ndk {
                 abiFilters "x86_64" "armeabi-v7a" "arm64-v8a"		//可以全部 也可以任选其一，和.so要对应起来
            }

            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }

    }




----------

####	 3.配置cmakeLists  	
		#我这里配置的是多个.so，如果你只是一个，只要复制一行就可以了
		# CMake版本信息
		cmake_minimum_required(VERSION 3.4.1)

		# 支持-std=gnu++11   也可以换用换用--std=c++11 对c++ 11的支持
		set(CMAKE_VERBOSE_MAKEFILE on)
		set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

		# 工程路径 绝对路径   可以不定义使用绝对路径，直接使用 ${CMAKE_SOURCE_DIR}
		set(pathToProject G:/ASworkSpace/CommunicationTest/Communication)

		# 配置加载native依赖 （头文件）
		include_directories(${pathToProject}/app/src/main/cpp/include)

		# 添加待编译的文件  使用相对路径 ：${CMAKE_SOURCE_DIR}/src/main/cpp/interface.cpp
		add_library(
		native-lib 
		SHARED             
		${pathToProject}/app/src/main/cpp/interface.cpp
		${pathToProject}/app/src/main/cpp/msl.cpp
		${pathToProject}/app/src/main/cpp/native-lib.cpp )

		# 动态方式加载
		add_library(lib_one SHARED IMPORTED)
		add_library(lib_two SHARED IMPORTED)
		add_library(lib_three SHARED IMPORTED)
		add_library(lib_four SHARED IMPORTED)
		add_library(lib_five SHARED IMPORTED)

		# 引入so文件
		set_target_properties(lib_one PROPERTIES IMPORTED_LOCATION ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libTcpCommunication.so )
		set_target_properties(lib_two PROPERTIES IMPORTED_LOCATION  ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libevent.so)
		set_target_properties(lib_three PROPERTIES IMPORTED_LOCATION  ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libevent_pthreads.so)
		set_target_properties(lib_four PROPERTIES IMPORTED_LOCATION ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libGCLogger.so)
		set_target_properties(lib_five PROPERTIES IMPORTED_LOCATION ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libMsgSessionLayer.so)

		#查找库所在目录
		find_library( # Sets the name of the path variable.
              log-lib
              log )
		#链接库  可能出现无法引用的情况  也可以使用 ${lib_one} 的方式去引用
		target_link_libraries(   native-lib
                         lib_one
                         lib_two
                         lib_three
                         lib_four
                         lib_five
                         # ${lib_one} 这样引用也可以
                         ${log-lib})

CMakeLists图如下： `黄色箭头是需要关注的地方，lib_one 这里属于自定义命名`
![CMakeLists图](https://i.imgur.com/WwiW2Ov.png)


####	 4.创建jni类，使用第三方动态库的.so方法
		a.在包下创建MyJniUtils包，再创建jniUtils类  （建议统一包名便于后期维护，这里要注意一下，后面会讲到）
![jni类](https://i.imgur.com/15K6afZ.png)
		
		b.书写jni类接口代码
		public  class JniUtils {
    	static {
   	    System.loadLibrary("native-lib");
    	}
    	public static native String stringFromJNI();
    	public static native int adds(); }

		c.利用alt+enter 快速生成cpp下jni方法   注意jni方法的路径com.example.kevin.MyJniUtils.JniUtils
		extern "C"
		JNIEXPORT jint JNICALL
		Java_com_example_kevin_MyJniUtils_JniUtils_adds(JNIEnv *env, jclass type) {
    	return add(3,3);  	//add方法的实现是在.so里完成的,实现的是所传值的加法运算
		}
		
		d.在MainActity下调用 JniUtils.adds（） 
		e.运行成功后，需要的.so 在 app\build\intermediates\cmake\debug\obj\armeabi-v7a\libnative-lib.so

#### 5.总结
		这里做一个总结，过程看起来繁琐其实熟练后很简单
		就分为四步：
		1.配置项目结构，放置需要的.so文件
		2.配置bulidgradle和CMakeLists
		3.创建jni类包，创建jniUtils,书写对应接口。在cpp文件下调用.so方法
		4.运行调试
		PS：注意写的时候要细心，有bug耐心解决，总会成功的。我也是经历了无数奇葩问题的（抱头哭）
		
#如果需要了解如何使用自己编译的.so（自己的.so包含了第三方的.so）（就请查看 NDK开发（三）的内容吧！谢谢！）
	

