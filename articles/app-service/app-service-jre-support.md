---
title: 适用于 Windows 的 Java 运行时支持 azure 应用服务
description: 本主题提供在 Windows 上的 Azure 应用服务支持的 Java 运行时语句。
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522893"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Windows 上的应用服务支持的 Java 运行时语句

## <a name="jdk-versions-and-maintenance"></a>JDK 版本和维护

Azure 支持的 Java 开发工具包 (JDK) 为提供 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

将通过 Azure 应用服务中的 Windows 的新运行时选项提供的主版本更新。 客户可以通过配置应用服务部署来更新到这些较新的 Java 版本，他们需要负责测试和确保重大更新符合其需求。

支持的 JDK 将在每年的 1 月、4 月、7 月和 10 月按季度自动修补。

## <a name="security-updates"></a>安全更新

重大安全漏洞的修补程序和修复程序将在 Azul Systems 提供后立即发布。 “重大”漏洞是根据 [NIST 常见漏洞评分系统版本 2](https://nvd.nist.gov/cvss.cfm) 提供的基本评分 9.0 或以上来定义的。

## <a name="deprecation-and-retirement"></a>弃用和停用

如果即将停用某个受支持的 Java 运行时，则从停用该运行时之前的至少六个月开始，使用受影响运行时的 Azure 开发人员会收到弃用通知。

## <a name="local-development"></a>本地开发

开发人员可以从 [Azul 下载站点](https://www.azul.com/downloads/azure-only/zulu/)下载 Azul Zulu Enterprise JDK Production Edition 进行本地开发。

## <a name="development-support"></a>开发支持

产品支持[支持 Azure 的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)针对 Azure 进行开发时可通过 Microsoft 或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)与[限定的 Azure 支持计划](https://azure.microsoft.com/support/plans/)。

## <a name="runtime-support"></a>运行时支持

如果开发人员有[符合条件的支持计划](https://azure.microsoft.com/support/plans/)，则可以通过 Azure 支持部门针对 Azul Zulu JDK [提出问题](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>后续步骤

本主题提供在 Windows 上的 Azure 应用服务支持的 Java 运行时语句。
- 若要详细了解托管 web 应用程序与 Azure 应用服务，请参见[应用服务概述](overview.md)。
- 有关 Java 在 Azure 开发上的信息，请参阅[面向 Java 开发人员中心的 Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
