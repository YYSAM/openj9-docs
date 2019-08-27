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

欢迎阅读 Eclipse OpenJ9虚拟机（VM）的用户文档。

这个用户文档支持 OpenJ9 VM 在 OpenJDK 运行时中的配置、调优和诊断。 但是，由于 Java SE 类库之间的差异，特定的选项可能只适用于某一个Java SE版本。我们会使用图标用于指示应用差异的位置。 例如:

![适用于Java 8 (LTS)的开始](cr/java8.png) 这个图标开头的文字只适用于包含 OpenJ9虚拟机的 Java 8二进制文件。 LTS 版本的图标就是这个颜色。 ![适用于Java 8 (LTS)的截止](cr/java_close_lts.png)

![适用于Java 12 和之后版本的开始](cr/java12plus.png) 这个图标开头的文字只适用于包含 OpenJ9虚拟机的 Java 12或更高版本的二进制文件。 这种新特性的图标就是这种颜色。![适用于Java 12 和之后版本的截止](cr/java_close.png)

要查看哪些 Java 版本是 LTS 版本，哪些是新特性版本，以及有关发布节奏、支持的平台和构建环境等信息，请参阅[环境支持](openj9_support.md)。

<i class="fa fa-pencil-square-o" aria-hidden="true"></i> **注意：** 支持OpenJ9的文档仍在编写构建中。当前内容涵盖了有关OpenJ9组件的一些高级信息，以及可用于在启动应用程序时配置VM的命令行选项和环境变量。我们希望随着时间的推移提供更多内容。由于OpenJ9是由IBM贡献给给Eclipse Foundation的，因此该内容包含一些指向额外信息的链接，这些信息来自IBM Knowledge Center并属于[IBM&reg; SDK, Java&trade; Technology Edition product documentation](https://www.ibm.com/support/knowledgecenter/SSYKE2/welcome_javasdk_family.html) 产品文档的一部分。该内容补充了此处的文档，直到有更完整的用户文档可用。

我们欢迎对用户文档的贡献。 如果你想参与，请阅读我们的[贡献指南](https://github.com/eclipse/openj9-docs/blob/master/CONTRIBUTING.md)。 如果您在文档中发现任何[错误](https://github.com/eclipse/openj9-docs/issues/new?template=documentation-error.md)，请在我们的 GitHub 库中提出问题。

## 环境支持

大量常见的硬件和流行的操作系统都支持包含 OpenJ9 VM 的 OpenJDK 二进制文件。 这个范围随着 Eclipse基金会的工作进展而扩大。 有关详细信息，请参阅[当前支持的环境列表](openj9_support.md)。

<i class="fa fa-pencil-square-o" aria-hidden="true"></i> **注意:** 本用户指南包含有关在 z/OS&reg; 平台上配置、调优和调试 OpenJ9的信息。 这些内容是由 IBM 贡献的，因此当为 z/OS&reg; 平台创建 OpenJDK 二进制文件的工作完成时，这些内容将变得可用。


## 特定版本的文档

提供了几个版本的文档，涵盖了OpenJ9的所有版本：

- [上一个版本的文档](https://www.eclipse.org/openj9/docs/index.html)
- [即将发布版本的文档](https://eclipse.github.io/openj9-docs/)
- [可下载的早起版本文档](https://github.com/eclipse/openj9-docs/tree/master/downloads): 要下载zip文件，请单击文件名，然后单击“**下载**”。下载`.zip`文件后，将其解压缩，然后在浏览器中打开`index.html`文件即可。

## 其他有用的链接

- [Eclipse OpenJ9 官网地址](https://www.eclipse.org/openj9)
- [Eclipse OpenJ9 GitHub 地址](https://github.com/eclipse/openj9)
- [Eclipse Foundation OpenJ9 项目主页](https://projects.eclipse.org/projects/technology.openj9)
- [预先构建的AdoptOpenJDK项目地址](https://adoptopenjdk.net/releases.html?variant=openjdk8&jvmVariant=openj9)

<!-- ==== END OF TOPIC ==== index.md ==== -->
