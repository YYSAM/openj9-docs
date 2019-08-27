

# AdoptOpenJDK 的构建

Adoptopenjdk 社区项目使用 OpenJ9提供 OpenJDK 的预构建二进制文件，这些文件已经过严格测试，并且达到预期的质量标准。 可以通过以下链接获得 Eclipse OpenJ9最新版本的二进制文件:

- [OpenJDK8 with OpenJ9](https://adoptopenjdk.net/releases.html?variant=openjdk8&jvmVariant=openj9)
- [OpenJDK11 with OpenJ9](https://adoptopenjdk.net/releases.html?variant=openjdk11&jvmVariant=openj9)
- [OpenJDK12 with OpenJ9](https://adoptopenjdk.net/releases.html?variant=openjdk12&jvmVariant=openj9)

使用 OpenJ9构建 OpenJDK 的每日构建版本也可以从项目中获得。

## 支持的平台

社区为 OpenJDK 源代码开发和维护一个构建和测试基础架构，该架构跨越多种平台。 有关构建支持的平台和最低操作系统级别支持的信息，请参阅 AdoptOpenJDK [平台支持矩阵](https://adoptopenjdk.net/supported_platforms.html)。

## 安装前的条件

如果你从 AdoptOpenJDK 社区获得二进制文件，则需要遵循以下先决条件:

- Adoptopenjdk 社区中用于 Linux 和 AIX 平台的 OpenJDK 二进制文件不再捆绑 OpenSSL 加密库, 你可以在系统路径上找到相关库。 如果要使用 OpenSSL 加密加速，必须在系统上安装 OpenSSL 1.0.2或1.1.*版本。 如果在系统路径上没有找到该库，则使用内置的 Java 密码图形实现，该实现的性能会较差些.

- ![开始部分的内容 Java 8](cr/java8.png) 在Linux的系统上, 当AWT字体子系统库被初始化的时候, 这`fontconfig.x86_64`包应该被安装好以避免造成`NullPointerException`错误. 目前AdoptOpenJDK项目正在为他们的OpenJDK的包努力解决这个问题.


<!-- ==== END OF TOPIC ==== adoptopenjdk.md ==== -->
