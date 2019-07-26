---
title: 配置 Windows Java 应用-Azure App Service |Microsoft Docs
description: 了解如何将 Java 应用配置为在 Azure 应用服务中的默认 Windows 实例上运行。
keywords: azure 应用服务, web 应用, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498515"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>为 Azure 应用服务配置 Windows Java 应用

Azure 应用服务可让 Java 开发人员在完全托管的基于 Windows 的服务中快速生成、部署和缩放 Tomcat 或 Java Standard Edition (SE) 打包式 Web 应用程序。 可以在命令行或者 IntelliJ、Eclipse 或 Visual Studio Code 等编辑器中使用 Maven 插件部署应用程序。

本指南提供了 Java 开发人员可在应用服务中使用的重要概念和说明。 如果你从未用过 Azure 应用服务，首先应该通读 [Java 快速入门](app-service-web-get-started-java.md)。 [应用服务 Windows 常见问题解答](faq-configuration-and-management.md)中解答了有关使用应用服务且非特定于 Java 开发的一般问题。

> [!NOTE]
> 找不到要查找的内容？ 有关部署和保护 Java 应用的信息, 请参阅[WINDOWS OSS 常见问题解答](faq-configuration-and-management.md)或[Java Linux 配置指南](containers/configure-language-java.md)。

## <a name="configuring-tomcat"></a>配置 Tomcat

若要编辑 Tomcat 的 `server.xml` 或其他配置文件，请首先记下门户中你的 Tomcat 主版本。

1. 通过运行 `env` 命令查找你的版本的 Tomcat 主目录。 搜索以 `AZURE_TOMCAT` 开头并且与你的主版本匹配的环境变量。 例如，`AZURE_TOMCAT85_HOME` 指向 Tomcat 8.5 的 Tomcat 目录。
1. 查明你的版本的 Tomcat 主目录后，将配置目录复制到 `D:\home`。 例如, 如果`AZURE_TOMCAT85_HOME`值为`D:\Program Files (x86)\apache-tomcat-8.5.37`, 则复制的目录的新路径将为`D:\home\apache-tomcat-8.5.37`。

最后，请重启应用服务。 你的部署应当转到 `D:\home\site\wwwroot\webapps`，跟以前完全一样。

## <a name="java-runtime-statement-of-support"></a>Java 运行时支持声明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azure 支持的 Java 开发工具包 (JDK) 为提供 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主版本更新将通过适用于 Windows 的 Azure 应用服务中的新运行时选项提供。 客户可以通过配置应用服务部署来更新到这些较新的 Java 版本，他们需要负责测试和确保重大更新符合其需求。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。

### <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

### <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

### <a name="local-development"></a>本地开发

开发人员可以从 [Azul 下载站点](https://www.azul.com/downloads/azure-only/zulu/)下载 Azul Zulu Enterprise JDK Production Edition 进行本地开发。

### <a name="development-support"></a>开发支持

使用[合格的 azure 支持计划](https://azure.microsoft.com/support/plans/)开发 azure 或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)时, [AZURE 支持的 Azul 祖鲁 JDK](https://www.azul.com/downloads/azure-only/zulu/)的产品支持可通过 Microsoft 获得。

### <a name="runtime-support"></a>运行时支持

如果开发人员有[符合条件的支持计划](https://azure.microsoft.com/support/plans/)，则可以通过 Azure 支持部门针对 Azul Zulu JDK [提出问题](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>后续步骤

本主题提供了对 Windows 上的 Azure 应用服务的支持的 Java 运行时声明。

- 若要详细了解如何使用 Azure 应用服务托管 Web 应用程序，请参阅[应用服务概述](overview.md)。
- 有关 Java on Azure 开发的信息，请参阅 [Azure for Java 开发人员中心](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
