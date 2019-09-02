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

### 云的优化
To improve the performance of applications that run in containers, try setting the following tuning options:

  - Use a shared classes cache (`-Xshareclasses -XX:SharedCacheHardLimit=200m -Xscmx60m`) with Ahead-Of-Time (AOT) compilation to improve your startup time. For more information, see [Class Data Sharing](shrc.md) and [AOT Compiler](aot.md).
  - Use the [-Xtune:virtualized](xtunevirtualized.md) option, which configures OpenJ9 for typical cloud deployments where VM guests are provisioned with a small number of virtual CPUs to maximize the number of applications that can be run. When enabled, OpenJ9 adapts its internal processes to reduce the amount of CPU consumed and trim down the memory footprint. These changes come at the expense of only a small loss in throughput.

  The OpenJ9 VM automatically detects when it is running in a docker container and uses a mechanism to detect when the VM is idle. When an idle state is detected, OpenJ9 runs a garbage collection cycle and releases free memory pages back to the operating system. The object heap is also compacted to make best use of the available memory for further application processing. For cloud services that charge based on memory usage, maintaining a small footprint can generate cost savings. For more information about tuning options that control this process, see [-XX:IdleTuningMinIdleWaitTime](xxidletuningminidlewaittime.md).

### Cryptographic operations

OpenJDK uses the in-built Java cryptographic implementation by default. However, native cryptographic implementations
typically provide better performance. OpenSSL is a native open source cryptographic toolkit for Transport Layer Security (TLS) and
Secure Sockets Layer (SSL) protocols, which is well established and used with many enterprise applications. The OpenSSL V1.0.x and V1.1.x implementations are currently supported for the Digest, CBC, GCM, and RSA algorithms. The OpenSSL V1.1.x implementation is also supported for the ChaCha20 and ChaCha20-Poly1305 algorithms.

<i class="fa fa-exclamation-triangle" aria-hidden="true"></i> **Restriction:** ![Start of content that applies to Java 8 and later](cr/java8plus.png) Due to issue [#5611](https://github.com/eclipse/openj9/issues/5611), the Digest algorithm is currently disabled. ![End of content that applies to Java 8 and later)](cr/java_close_lts.png)

On Linux and AIX platforms, the OpenSSL 1.0.x or 1.1.x library is expected to be found on the system path. If you use a package manager to install OpenSSL, the system path will be updated automatically. On other platforms, the OpenSSL 1.1.x library is currently bundled with the binaries from AdoptOpenJDK.

OpenSSL support is enabled by default for all supported algorithms. If you want to limit support to specific algorithms, a number of
system properties are available for tuning the implementation.

Each algorithm can be disabled individually by setting the following system properties on the command line:

- To turn off **Digest**, set `-Djdk.nativeDigest=false` (See **Restriction**. This system property cannot be used to enable the Digest algorithm)
- To turn off **ChaCha20** and **ChaCha20-Poly1305**, set `-Djdk.nativeChaCha20=false`. <i class="fa fa-pencil-square-o" aria-hidden="true"></i> **Note:** ![Start of content that applies to Java 8 (LTS)](cr/java8.png) ![Start of content that applies to Java 12 (LTS)](cr/java12.png) These algorithms are not supported on Java 8 or 12![End of content that applies only to Java 8 and 12 (LTS)](cr/java_close_lts.png)
- To turn off **CBC**, set `-Djdk.nativeCBC=false`
- To turn off **GCM**, set `-Djdk.nativeGCM=false`
- To turn off **RSA**, set `-Djdk.nativeRSA=false`

You can turn off all the algorithms by setting the following system property on the command line:

```
-Djdk.nativeCrypto=false
```

To build a version of OpenJDK with OpenJ9 that includes OpenSSL support, follow the steps in our detailed build instructions:

- [OpenJDK 8 with OpenJ9](https://github.com/eclipse/openj9/blob/master/doc/build-instructions/Build_Instructions_V8.md).
- [OpenJDK 11 with OpenJ9](https://github.com/eclipse/openj9/blob/master/doc/build-instructions/Build_Instructions_V11.md).
- [OpenJDK 12 with OpenJ9](https://github.com/eclipse/openj9/blob/master/doc/build-instructions/Build_Instructions_V12.md).

<i class="fa fa-pencil-square-o" aria-hidden="true"></i> **Note:** If you obtain an OpenJDK with OpenJ9 build from [AdoptOpenJDK](https://adoptopenjdk.net/) that includes OpenSSL or build a version yourself that includes OpenSSL support, the following acknowledgements apply in accordance with the license terms:

- *This product includes software developed by the OpenSSL Project for use in the OpenSSL Toolkit. (http://www.openssl.org/).*
- *This product includes cryptographic software written by Eric Young (eay@cryptsoft.com).*


## Runtime options

Runtime options are specified on the command line and include system properties, standard options, nonstandard (**-X**) options, and **-XX** options. For a detailed list of runtime options, see [OpenJ9 command-line options](cmdline_specifying.md)

## Default settings

If you do not specify any options on the command line at run time, the OpenJ9 VM starts with default settings that define how it operates. For more information about these settings, see [Default settings for the OpenJ9 VM](openj9_defaults.md).

## ![Start of content that applies to Java 11 (LTS) and later](cr/java11plus.png) Using Jlink

On Java 11 and later, you can use the `jlink` utility to create a custom OpenJ9 runtime image, which allows you to optimize image size.
If you do not require translations from the English language, the translation files can be removed to further optimize the size. You can achieve this by specifying the `--exclude-files=**java_**.properties` option when you run `jlink`. The default English `java.properties` file is unaffected.

## Troubleshooting

The OpenJ9 diagnostic component contains extensive features to assist with problem determination. Diagnostic data is produced under default conditions, but can also be controlled by starting the VM with the [-Xdump option](xdump.md) or using the `com.ibm.jvm.Dump` API. You can also trace Java applications, methods, and VM operations by using the [-Xtrace option](xtrace.md).

To get started, read [Diagnostic tools and data](diag_overview.md).


<!-- ==== END OF TOPIC ==== index.md ==== -->
