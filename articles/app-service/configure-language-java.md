---
title: 配置 Windows Java 应用程序-Azure 应用服务 |Microsoft Docs
description: 了解如何配置 Azure 应用服务中的默认 Windows 实例上运行的 Java 应用。
keywords: azure 应用服务、 web 应用、 windows、 os、 java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: 82e8936a888cbc99088ab18423e55dd57a3c2e77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604152"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>配置 Windows Azure 应用服务的 Java 应用程序

Azure 应用服务允许 Java 开发人员能够快速生成、 部署和缩放其 Tomcat 或 Java 标准版 (SE) 打包上完全托管的基于 Windows 的服务的 web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供的重要概念和面向 Java 开发人员使用应用服务中的说明。 如果你从未使用过 Azure 应用服务，则应该通读[Java 快速入门](app-service-web-get-started-java.md)第一个。 有关使用应用服务不是特定于 Java 开发的一般问题中回答[应用服务 Windows 常见问题解答](faq-configuration-and-management.md)。

> [!NOTE]
> 找不到要查找的内容？ 请参阅[Windows OSS 常见问题解答](faq-configuration-and-management.md)或[Java Linux 配置指南](containers/configure-language-java.md)有关部署和保护您的 Java 应用程序的信息。

## <a name="configuring-tomcat"></a>配置 Tomcat

若要编辑 Tomcat 的`server.xml`或其他配置文件，首先在门户中需要记下你的 Tomcat 主要版本。

1. 通过运行你的版本查找 Tomcat 主目录`env`命令。 为环境变量开头搜索`AZURE_TOMCAT`和与你的主要版本匹配。 例如，`AZURE_TOMCAT85_HOME`指向 Tomcat 8.5 Tomcat 目录。
1. 一旦你的版本确定 Tomcat 主目录，将复制到的配置目录`D:\home`。 例如，如果`AZURE_TOMCAT85_HOME`的值`D:\Program Files (x86)\apache-tomcat-8.5.37`，复制的配置目录的完整路径将是`D:\home\tomcat\conf`。

最后，请重启应用服务。 你的部署应转到`D:\home\site\wwwroot\webapps`可以像以前一样。

## <a name="java-runtime-statement-of-support"></a>Java 运行时支持声明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azure 支持的 Java 开发工具包 (JDK) 为提供 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

将通过 Azure 应用服务中的 Windows 的新运行时选项提供的主版本更新。 客户可以通过配置应用服务部署来更新到这些较新的 Java 版本，他们需要负责测试和确保重大更新符合其需求。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

### <a name="local-development"></a>本地开发

开发人员可以从 [Azul 下载站点](https://www.azul.com/downloads/azure-only/zulu/)下载 Azul Zulu Enterprise JDK Production Edition 进行本地开发。

### <a name="development-support"></a>开发支持

产品支持[支持 Azure 的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)针对 Azure 进行开发时可通过 Microsoft 或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)与[限定的 Azure 支持计划](https://azure.microsoft.com/support/plans/)。

### <a name="runtime-support"></a>运行时支持

如果开发人员有[符合条件的支持计划](https://azure.microsoft.com/support/plans/)，则可以通过 Azure 支持部门针对 Azul Zulu JDK [提出问题](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>后续步骤

本主题提供在 Windows 上的 Azure 应用服务支持的 Java 运行时语句。

- 若要详细了解托管 web 应用程序与 Azure 应用服务，请参见[应用服务概述](overview.md)。
- 有关 Java 在 Azure 开发上的信息，请参阅[面向 Java 开发人员中心的 Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
