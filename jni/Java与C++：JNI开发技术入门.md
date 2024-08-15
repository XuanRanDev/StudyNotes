## JNI概念
JNI是Java Native Interface的缩写，通过使用 Java本地接口书写程序，可以确保代码在不同的平台上方便移植。 从Java1.1开始，JNI标准成为java平台的一部分，它允许Java代码和其他语言写的代码进行交互。JNI一开始是为了本地已编译语言，尤其是C和C++而设计的，这就意味这我们可以使Java和CPP代码进行互相调用

### 优势与缺点
+ 性能优化：JNI允许直接调用本地代码，这通常比通过Java实现的代码更高效，特别是在处理计算密集型任务或需要直接操作硬件时。例如，通过JNI可以调用优化过的数学库或图形处理库，提升应用程序的性能。
+ 现有代码的重用：许多现有的系统库和第三方库是用C或C++编写的。使用JNI可以将这些已经经过优化和测试的库集成到Java应用程序中，从而避免重新实现相同的功能。
+ 系统级访问：JNI允许Java程序访问底层操作系统的功能，这些功能可能不直接通过Java API提供。例如，访问操作系统特有的功能或与系统硬件进行交互。
+ 平台特定功能：对于一些特定于平台的功能，JNI提供了一种方式使Java程序能够利用平台特定的特性或优化。例如，Windows特有的功能或Linux的系统调用。
+ 实现Java和本地代码的互操作：JNI提供了Java代码与本地代码之间的桥梁，支持在Java中调用本地方法，并在本地代码中调用Java方法。这种互操作性对于实现复杂的系统集成至关重要。
+ 开发复杂度增加、调试困难和可能的安全风险。因此，在使用JNI时应仔细考虑这些因素，并确保良好的设计和测试实践



### 了解so与dll

`.so` 文件和 `.dll` 文件都是动态链接库的文件格式，用于在程序运行时动态加载和调用共享代码。它们的主要区别在于使用的操作系统和文件格式：

1. **`.so` 文件（Shared Object）**：
   - **操作系统**：主要用于类Unix系统，如Linux和macOS。
   - **用途**：`.so` 文件用于共享代码，提供函数和数据供其他程序调用。在Linux系统中，`.so` 文件可以被多个程序共享，从而减少内存使用和存储空间。
   - **加载方式**：程序在运行时通过动态链接加载`.so` 文件，这种机制允许程序在运行时调用库中的函数而不需要在编译时链接到库。
2. **`.dll` 文件（Dynamic Link Library）**：
   - **操作系统**：主要用于Windows操作系统。
   - **用途**：`.dll` 文件也是用于共享代码的库，提供函数和数据供其他Windows程序调用。它们具有类似于`.so` 文件的功能，但在Windows环境中使用。
   - **加载方式**：程序通过动态链接加载`.dll` 文件，允许在程序运行时调用其中的函数。

**主要区别**：

- **平台依赖**：`.so` 文件用于类Unix系统，而`.dll` 文件用于Windows系统。
- **文件格式**：`.so` 和 `.dll` 是不同的文件格式，尽管它们的功能类似，但在实现细节和平台相关性上有所不同。
- **调用约定和接口**：在Windows平台上，`.dll` 文件使用Windows特定的调用约定，而在类Unix系统上，`.so` 文件遵循POSIX标准和其他相关的调用约定。



### 定义JNI接口

先使用IDEA新建一个普通的Java工程，并新建一个Main文件，代码如下所示，这里篇幅原因我直接省略了创建工程的步骤。

在这段代码中我们定义了一个方法Main方法，Main方法的输出是调用了helloJni这个被native所修饰的方法，并且在static中我们从resource文件夹中读取所有的so文件并加载。

之所以要这么写是因为使用纯Java开发JNI没有安卓中的那么方便，我们每次增加so文件都得修改编译选项，使用这个代码我们就只需往resource文件夹中新增so文件就行了。

```java
public class Main {

    public static void main(String[] args) {
        System.out.println(helloJni());
    }
    
    static {
        loadLibrariesFromResources();
    }

    public static native String helloJni();

    /**
     * 在启动时动态加载所有so文件
     */
    public static void loadLibrariesFromResources() {
        // 获取资源文件的路径
        Path resourceDir = null;
        try {
            resourceDir = Paths.get(Main.class.getResource("/").toURI());
        } catch (URISyntaxException e) {
            throw new RuntimeException(e);
        }
        File[] files = resourceDir.toFile().listFiles((dir, name) -> name.endsWith(".so"));

        if (files != null) {
            for (File file : files) {
                System.load(file.getAbsolutePath());
            }
        }
    }

}
```

#### 生成头文件

使用以下命令将我们的这个Java类生成.h头文件：

```shell
javac -h . Main.java
```

![image-20240815122406349](E:\桌面文件夹\知识\Me\jni.assets\image-20240815122406349.png)

![image-20240815122412264](E:\桌面文件夹\知识\Me\jni.assets\image-20240815122412264.png)



### 创建CPP工程

我这里使用Clion来创建CPP工程，虽然IDEA也是可以做到的，但是我更喜欢将这两者分开，因为IDEA并没有提供像AS那样的JNI开发体验。

![image-20240815122520112](E:\桌面文件夹\知识\Me\jni.assets\image-20240815122520112.png)

将生成的头文件复制过来，并创建一个新的cpp文件：

![image-20240815122710600](E:\桌面文件夹\知识\Me\jni.assets\image-20240815122710600.png)

此时我们会发现我们复制过来的头文件报找不到符号：

![image-20240815122749746](E:\桌面文件夹\知识\Me\jni.assets\image-20240815122749746.png)

这是因为我们没有导入jni的头文件。



#### 导入JNI头文件

在CMakeLists.txt文件中添加两行include_directories，其中的参数为你JDK安装路径，这样就不会报上文的无法解析符号了。

```txt
# cmake最低版本
cmake_minimum_required(VERSION 3.29)
# 工程名称
project(JniDemo)
# cpp标准版本
set(CMAKE_CXX_STANDARD 17)

# 包含JNI头文件目录
include_directories("D:/Environment/Java20/include")
include_directories("D:/Environment/Java20/include/win32")  # 包含Windows特定的头文件

add_library(JniDemo SHARED
        JniDemo.cpp)

```



#### 编写CPP代码

在我们创建的JniDemo.cpp文件中编写cpp代码，这里只返回了一个字符串。

```cpp
#include <jni.h>
#include <string>
#include "dev_xuanran_jnidemo_Main.h"

using namespace std;

JNIEXPORT jstring JNICALL Java_dev_xuanran_jnidemo_Main_helloJni
  (JNIEnv *env, jclass)
{
  return env -> NewStringUTF("Hello JniDemo!");
}
```



#### 编译so文件

如果此时我们直接编译的话会发现它默认生成的是dll文件，但是我们在Java开发中主要使用so文件，所以我们需要进行编译配置：

我们在CMakeLists.txt中添加以下内容：

```txt
# 设置目标属性，使其生成.so文件而不是.dll
set(CMAKE_SHARED_LIBRARY_PREFIX "lib")
set(CMAKE_SHARED_LIBRARY_SUFFIX ".so")
```

完成的内容如下：

```txt
# cmake最低版本
cmake_minimum_required(VERSION 3.29)
# 工程名称
project(JniDemo)
# cpp标准版本
set(CMAKE_CXX_STANDARD 17)

# 包含JNI头文件目录
include_directories("D:/Environment/Java20/include")
include_directories("D:/Environment/Java20/include/win32")  # 包含Windows特定的头文件

# 设置目标属性，使其生成.so文件而不是.dll
set(CMAKE_SHARED_LIBRARY_PREFIX "lib")
set(CMAKE_SHARED_LIBRARY_SUFFIX ".so")

add_library(JniDemo SHARED
        JniDemo.cpp)

```

此时我们编译会发现生成的有so文件：

![image-20240815123751389](E:\桌面文件夹\知识\Me\jni.assets\image-20240815123751389.png)

将其复制到Java中的Resouce资源目录中：


![image-20240815123835897](E:\桌面文件夹\知识\Me\jni.assets\image-20240815123835897.png)

执行编译，我们发现Java成功返回了CPP代码中的字符串

![image-20240815123901360](E:\桌面文件夹\知识\Me\jni.assets\image-20240815123901360.png)