<!--
* Copyright (c) 2017, 2019 IBM Corp. and others
*
* This program and the accompanying materials are made
* available under the terms of the Eclipse Public License 2.0
* which accompanies this distribution and is available at
* https://www.eclipse.org/legal/epl-2.0/ or the Apache
* License, Version 2.0 which accompanies this distribution and
* is available at https://www.apache.org/licenses/LICENSE-2.0.
*
* This Source Code may also be made available under the
* following Secondary Licenses when the conditions for such
* availability set forth in the Eclipse Public License, v. 2.0
* are satisfied: GNU General Public License, version 2 with
* the GNU Classpath Exception [1] and GNU General Public
* License, version 2 with the OpenJDK Assembly Exception [2].
*
* [1] https://www.gnu.org/software/classpath/license.html
* [2] http://openjdk.java.net/legal/assembly-exception.html
*
* SPDX-License-Identifier: EPL-2.0 OR Apache-2.0 OR GPL-2.0 WITH
* Classpath-exception-2.0 OR LicenseRef-GPL-2.0 WITH Assembly-exception
-->

# Eclipse OpenJ9

OpenJ9是一种高性能，可扩展的Java&trade;虚拟机（VM）实现，完全符合[Java虚拟机规范](https://docs.oracle.com/javase/specs/index.html).

在运行时，这VM去解释由Java编译器编译的Java字节码, 同时VM做为语言与底层操作系统和硬件之间的转换器。一个Java程序需要特定的VM在特定平台上运行，例如Linux&reg;，z/OS&reg;或Windows&trade;。

此参考资料提供有关VM配置和调优选项的信息以及默认设置。

## 配置你的系统

对于正常的操作，必须在操作系统级别设置某些环境变量。您可能还需要设置允许VM利用硬件和操作系统特性的其他配置选项，具体取决于您的系统环境。阅读[自定义您的系统](https://www.ibm.com/support/knowledgecenter/SSYKE2_8.0.0/com.ibm.java.vm.80.doc/docs/j9_configure.html)去了解有关以下选项的更多信息：

- 设置PATH和CLASSPATH环境变量。
- 设置LIBPATH或LD_LIBRARY_PATH环境变量（AIX&reg;和Linux）去指明在何处查找共享库。
- 在AIX和Linux系统上设置ulimits，以确保操作系统为您的应用程序分配足够的资源。
- 在z/OS系统上设置区域大小，BPXPRM参数和LanguageEvvironment&reg;运行时选项。
- 如果应用程序分配大量内存并经常访问该内存，则可能需要在系统上启用大页面支持。请参阅[大内存页的分配](https://www.ibm.com/support/knowledgecenter/SSYKE2_8.0.0/com.ibm.java.vm.80.doc/docs/j9_configure_large_page.html)。
- 在AIX系统上配置动态LPAR的支持。

<i class="fa fa-pencil-square-o" aria-hidden="true"></i> **注意**：在macOS&reg;系统上，必须设置DYLD_LIBRARY_PATH环境变量以指明在何处查找共享库。此外，为确保有适合您的应用足够的资源，您可能需要提高系统的限制, 例如`launchctl`，`ulimit`或`kern.<VARIABLES>`。有关详细说明，请参阅macOS版本的发布文档。

## 性能调优

OpenJ9初始配置了一系列的默认选项，这些选项可以为具有典型工作负载场景的Java应用程序提供最佳运行时环境。但是，如果您的应用程序是运行在某些特殊的场景，则可以通过参数调整OpenJ9 VM来提高性能。同样您还可以通过在应用程序代码, 来启用硬件功能或使用特定API来提高性能。

### 垃圾收集策略
OpenJ9包含几个垃圾收集策略。要了解有关这些策略和更多的负载类型等信息, 以及如何从中收益，请参阅[垃圾收集](gc.md)。

### 类数据共享
你可以共享你的类数据在运行中的多个VM之间, 当VM第一次启动的时候会创建和缓存相关的数据, 这样可以减少启动时间. 更多的信息, 请参考[类数据共享](shrc.md)。

### 本地数据操作
如果你的Java程序需要操作本地数据, 可以考虑让你的应用程序使用Data Access Accelerator 的相关API。

### 云端的优化
要提高在容器中运行的应用程序的性能，请尝试设置以下调整选项：

  - 配置 (`-Xshareclasses -XX:SharedCacheHardLimit=200m -Xscmx60m`) 来使用带有Ahead-Of-Time（AOT）编译的共享类. 更多的信息请参考 [类数据共享](shrc.md) 和 [AOT编译器](aot.md).
  - 使用 [-Xtune:virtualized](xtunevirtualized.md) 选项, 该选项将OpenJ9配置为云部署的标准模式，其中VM虚拟机配置了少量虚拟CPU，以最大限度地增加可运行的应用程序的数量。开启后，OpenJ9会调整其内部进程以减少CPU消耗量并减少内存占用。这些变化的代价是损失很小的吞吐量。
  
OpenJ9 VM会自动检测它何时在docker容器中运行，并使用一种机制来检测VM何时处于空闲状态。检测到空闲状态时，OpenJ9运行垃圾收集周期并将可用内存页释放回操作系统。同时压缩对象Heap堆以充分利用可用内存, 以进行进一步的应用程序处理。对于根据内存使用的某些收费的云服务，保持较小的占用空间可以节省成本。对于这类调整此过程的调整选项的详细信息，请参阅[-XX:IdleTuningMinIdleWaitTime](xxidletuningminidlewaittime.md).

### 加密操作

OpenJDK默认使用内置的Java加密类进行了实现。但是，本地系统加密实现通常可提供更好的性能。OpenSSL是一种用于传输层安全性（TLS）和安全套接字层（SSL）协议的本机开源加密工具包，它已经很好地建立并与许多企业应用程序一起使用。目前，Digest，CBC，GCM和RSA算法支持OpenSSL V1.0.x和V1.1.x实现。ChaCha20和ChaCha20-Poly1305算法也支持OpenSSL V1.1.x实现。

<i class="fa fa-exclamation-triangle" aria-hidden="true"></i> **Restriction:** ![Start of content that applies to Java 8 and later](cr/java8plus.png) 由于问题 [#5611](https://github.com/eclipse/openj9/issues/5611), 这种摘要算法是被禁止的. ![End of content that applies to Java 8 and later)](cr/java_close_lts.png)

在Linux和AIX平台上，可以在系统路径上找到OpenSSL 1.0.x或1.1.x的相关库。如果使用安装包管理器安装的OpenSSL，系统路径将自动配置更新好。在其他平台上，OpenSSL 1.1.x库目前与AdoptOpenJDK中的二进制文件捆绑在一起。

默认情况下，为所有支持的算法都启用对OpenSSL的支持。如果要限制对特定算法的支持，可以使用一些系统属性来调整实现。

通过在命令行上设置以下系统属性，可以自由的禁用每一个算法：

- 要关闭 **Digest** 摘要算法, 设置 `-Djdk.nativeDigest=false` (查看 **限制**. 此系统属性不能启用摘要算法)
- 要关闭 **ChaCha20** 和 **ChaCha20-Poly1305**, 设置 `-Djdk.nativeChaCha20=false`. <i class="fa fa-pencil-square-o" aria-hidden="true"></i> **注意:** ![Start of content that applies to Java 8 (LTS)](cr/java8.png) ![Start of content that applies to Java 12 (LTS)](cr/java12.png) 这些算法是不在 Java 8 or 12上支持的![End of content that applies only to Java 8 and 12 (LTS)](cr/java_close_lts.png)
- 要关闭 **CBC**, 设置 `-Djdk.nativeCBC=false`
- 要关闭 **GCM**, 设置 `-Djdk.nativeGCM=false`
- 要关闭 **RSA**, 设置 `-Djdk.nativeRSA=false`

您可以通过在命令行上设置以下系统属性来关闭所有算法：

```
-Djdk.nativeCrypto=false
```

要使用包含OpenSSL支持的OpenJ9来构建OpenJDK的版本，请按照详细构建说明中的步骤操作：

- [基于OpenJDK 8 的 OpenJ9](https://github.com/eclipse/openj9/blob/master/doc/build-instructions/Build_Instructions_V8.md).
- [基于OpenJDK 11 的 OpenJ9](https://github.com/eclipse/openj9/blob/master/doc/build-instructions/Build_Instructions_V11.md).
- [基于OpenJDK 12 的 OpenJ9](https://github.com/eclipse/openj9/blob/master/doc/build-instructions/Build_Instructions_V12.md).

<i class="fa fa-pencil-square-o" aria-hidden="true"></i> **注意:** If you obtain an OpenJDK with OpenJ9 build from [AdoptOpenJDK](https://adoptopenjdk.net/) that includes OpenSSL or build a version yourself that includes OpenSSL support, the following acknowledgements apply in accordance with the license terms:

如果你获得了包括OpenSSL支持的OpenJDK与OpenJ9的构建物从[AdoptOpenJDK](https://adoptopenjdk.net/), 或者建立一个包括OpenSSL的支持的自己的版本，确认申请需要按照下面的许可证条款：

- *本产品包含由OpenSSL Project开发的用于OpenSSL Toolkit的软件（http://www.openssl.org/）。*
- *本产品包含由Eric Young（eay@cryptsoft.com）编写的加密软件。*


## 运行时选项

运行时选项在命令行中指定，包括系统属性，标准选项，非标准（**-X**）选项和 **-XX** 选项等。有关运行时选项的详细列表，请参阅[OpenJ9 命令行选项](cmdline_specifying.md)

## 默认设置

如果在运行时未在命令行上指定任何选项，则OpenJ9 VM将使用定义好的其默认设置启动。有关这些设置的详细信息，请参阅[OpenJ9 VM 默认设置](openj9_defaults.md)。

## ![Start of content that applies to Java 11 (LTS) and later](cr/java11plus.png) 使用 Jlink

在Java 11及更高版本中，您可以使用该`jlink`实用程序创建自定义的OpenJ9运行时映像，例如可以可以优化映像大小。如果您不需要英语翻译，可以删除翻译文件以进一步优化大小。您可以通过`--exclude-files=**java_**.properties`在运行时指定选项来实现特殊目标的`jlink`。默认的英文`java.properties`文件则不受影响。

## 故障排除

OpenJ9诊断组件包含大量功能来帮助和确认问题。诊断数据在默认条件下生成，但也可以通过使用[-Xdump option](xdump.md)选项或使用`com.ibm.jvm.DumpAPI` 启动VM来进一步控制。您还可以使用[-Xtrace option](xtrace.md)选项跟踪Java应用程序，方法和VM的内部操作等。

要开始尝试，请阅读[诊断工具和数据](diag_overview.md)。

<!-- ==== END OF TOPIC ==== index.md ==== -->
