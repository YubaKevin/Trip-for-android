# 一、NDK环境的配置
#####     1.将下载的NDK改名为ndk-bundle，拷贝NDK到对应的目录：
######     C: \Android_ studio_ sdk\ndk- bundle
#####     2.在path路径最后配置 ndk-bundle的路径：
######      {sdk的路径} \ndk -bundle; 
######     例：G:\ASSDK\sdk\ndk-bundle 
###### 测试环境是否成功：cmd下：ndk-build
#####     3.创建Project
######     a.在local.properties里面配置NDK路径：
######     ndk.dir=C\: \\Android_ studio_ sdk\\ndk -bundle
######     b.配置以下兼容老的NDK
######     android.useDeprecatedNdk=true    
    
# 二、AndroidStudio 3.0.1 中的使用
##### 1.项目结构 
######     a.cpp下创建include文件 用于放.h头文件
######     b.main下建立 jniLibs 文件夹 用于存放第三方动态.so库（需要放置在对应的ABI文件夹中）

##### 1.app下的buildgradle 中 android { } 内
```
  defaultConfig {
        
            
   externalNativeBuild {
            cmake {
                 abiFilters "x86_64" "armeabi-v7a" "arm64-v8a"
                cppFlags "-frtti -fexceptions"
            }
        }
        
        sourceSets {
            main {
                jniLibs.srcDirs = ['src/main/jniLibs']
            }
        }
                }
          
        buildTypes {
        debug{
            minifyEnabled false
            ndk {
                abiFilters "x86_64" "armeabi-v7a" "arm64-v8a"
            }
        }
        release {
            ndk {
                 abiFilters "x86_64" "armeabi-v7a" "arm64-v8a"
            }
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }

    }
```
##### 2.cmakelist 
   
```
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
add_library(native-lib SHARED             
${pathToProject}/app/src/main/cpp/interface.cpp
${pathToProject}/app/src/main/cpp/msl.cpp
${pathToProject}/app/src/main/cpp/native-lib.cpp
                            )

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
```
# 三、编译成功
#####    编译成功后：.so在
######     build---->intermediates---->cmake---->debug---->obj---对应的cpu架构下
