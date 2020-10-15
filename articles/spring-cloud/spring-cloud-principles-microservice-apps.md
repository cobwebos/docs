---
title: 适用于 Azure 春季 Cloud 微服务 apps 的 Java 和基本 OS
description: 用于维护 Azure 春季 Cloud 微服务应用正常运行的 Java 和基本操作系统的原则
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c90062f1968cc7be5a742a67363f57b9632fdfa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090669"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>面向 Spring 微服务应用的 Java 和基础操作系统

本文适用于：✔️ Java

以下原则用于维护弹簧微服务应用的正常运行的 Java 和基本操作系统。
## <a name="principles-for-healthy-java-and-base-os"></a>正常 Java 和基本操作系统的原则
* 应为跨层的相同基本操作系统-基本 |标准 |价格.
    * 目前，Azure 春季云中的应用会混合使用 Debian 10 和 Ubuntu 18.04。
    * VMware 生成服务使用 Ubuntu 18.04。
* 应为相同的基本操作系统，而不考虑部署起始点-源 |.JAR
    * 目前，Azure 春季云中的应用会混合使用 Debian 10 和 Ubuntu 18.04。
* 基本操作系统应该不会有安全漏洞。
    * Debian 10 基本操作系统具有147打开标识符。
    * Ubuntu 18.04 基本操作系统具有132开放标识符。
* 应使用 JRE-无外设。
    * 目前，Azure 春季云中的应用使用 JDK。 JRE-无外设是一个较小的映像。
* 应使用最新的 Java 版本。
    * 目前，Azure 春季云中的应用使用 Java 8 build 242。 这是一种过时的版本。
 
Azul 系统将持续扫描基本操作系统的更改，并使最后生成的映像保持最新。 Azure 春季云查找映像的更改，并跨部署持续更新这些更改。
 
## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud 常见问题解答

* 支持哪些版本的 Java？ 主版本和内部版本号。
    * 支持 LTS 版本-Java 8 和11。
    * 使用最新的生成-例如，现在为 Java 8 build 252 和 Java 11 build 7。
* 谁构建了这些 Java 运行时？
    * Azul 系统。
* 什么是映像的基本操作系统？
    * Ubuntu 20.04 LTS (Fossa) 。 应用将继续保持在最新的 Ubuntu LTS 版本上。
    * 请参阅 [Ubuntu 20.04 LTS (焦距 Fossa) ](http://releases.ubuntu.com/focal/)
* 如何为本地开发人员下载受支持的 Java 运行时？ 
    * 请参阅 [安装适用于 Azure 的 JDK 和 Azure Stack](/azure/developer/java/fundamentals/java-jdk-install)
* 如何获取 Java 运行时级别的问题支持？
    * 使用 Azure 支持开支持票证。
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Azure 春季云中的默认部署

> ![默认部署](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)
* [Azure 和 Azure Stack 的 Java 长期支持](/azure/developer/java/fundamentals/java-jdk-long-term-support)