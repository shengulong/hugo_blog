---
title: "Jni"
date: 2019-11-29T19:26:17+08:00
categories: 2019-11
tags: [java]
author: sgl
---

#### JNI

Java平台有个用户和本地C代码进行互操作的API，称为Java Native Interface (Java本地接口)

#### Native

使用native关键字说明这个方法是原生函数，也就是这个方法是用C/C++语言实现的，并且被编译成了DLL，由java去调用。这些函数的实现体在DLL中，JDK的源代码中并不包含，你当然看不到。对于不同的平台它们也是不同的。这也是java的底层机制，实际上java就是在不同的平台上调用不同的native方法实现对操作系统的访问的。

#### Mac下写一个jni的demo

1. 文件都在目录：/Users/xxxx/myJavaDir下

2. HelloNative.java文件：

    ```java
    public class HelloNative
    {
        static
        {
            System.loadLibrary("HelloNative");
        }
    
        public static native void sayHello();
    
        @SuppressWarnings("static-access")
        public static void main(String[] args)
        {
            new HelloNative().sayHello();
        }
    }
    ```

3. javac HelloNative编译生成HelloNative.class

4. javah HelloNative生成文件HelloNative.h

5. HelloNative.c文件内容：

    ```c
    #include "jni.h"
    #include "HelloNative.h"
    #include <stdio.h>
    
    JNIEXPORT void JNICALL Java_HelloNative_sayHello(JNIEnv *env,jobject obj)
    {
        printf("Hello，JNI");
    }
    ```

6. 生成dll动态链接库

    ```
    gcc -dynamiclib -I"/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home/include" -I"/Users/xxxx/myJavaDir" -I"/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home/include/darwin/"  -o libHelloNative.jnilib HelloNative.c
    ```

7. 执行java HelloNative输出Hello，JNI%



#### 参考：

1. https://www.cnblogs.com/Qian123/p/5702574.html#_labelTop
2. https://www.cnblogs.com/chenmo-xpw/p/7501325.html
3. https://www.jianshu.com/p/429dc9aa2ce4
4. https://www.jianshu.com/p/1eb6d859175d