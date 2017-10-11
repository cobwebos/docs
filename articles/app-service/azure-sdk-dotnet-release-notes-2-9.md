---
title: "Azure SDK for .NET 2.9 发行说明"
description: "Azure SDK for .NET 2.9 发行说明"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 199f0906f73d693d7cd4b73c928f23ae83b99596
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="azure-sdk-for-net-29-release-notes"></a>用于 .NET 2.9 的 Azure SDK 发行说明

本主题包括用于 .NET 的 Azure SDK 版本 2.9 和 2.9.6 的发行说明。

##<a name="azure-sdk-for-net-296-release-summary"></a>用于 .NET 2.9.6 的 Azure SDK 发行摘要

发布日期：2016/11/16
 
此版本中未引入任何对 Azure SDK 2.9 的重大更改。 此外，将此 SDK 用于现有云服务项目也无需任何升级过程。

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 候选发布

- 在 Visual Studio 2017 RC 中，此版本的用于 .NET 的 Azure SDK 内置于 Azure 工作负荷中。 从目前开始，进行 Azure 开发所需的所有工具都会成为 Visual Studio 2017 RC 的一部分。 对于 Visual Studio 2015 和 Visual Studio 2013，仍可通过 WebPI 获取 SDK。 当 Visual Studio 2017 作为最终产品发布时，我们停止为 Visual Studio 2013 发布用于 .NET 的 Azure SDK。 通过此链接可下载 Visual Studio 2017 RC：https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Azure 诊断

- 针对云服务诊断存储连接字符串更改了行为，以便仅存储部分连接字符串并将密钥替换为令牌。 实际存储密钥现存储在用户配置文件文件夹中，以便可控制其访问。 Visual Studio 会在本地调试和发布过程中从用户配置文件文件夹中读取存储密钥。 
- 针对上述更改，Visual Studio Online 团队增强了 Azure 云服务部署任务模板，以便以持续集成和部署发布到 Azure 时，用户可为设置诊断扩展指定存储密钥。
- 现在可存储 Azure 诊断 (WAD) 的安全连接字符串和词汇切分，以帮助解决跨环境配置的问题。
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 虚拟机

- Visual Studio 现在支持将云服务部署到操作系统系列 5 (Windows Server 2016) 虚拟机。 对于现有云服务，可更改设置以面向新的 OS 系列。 创建新的云服务时，如果选择使用 .net 4.6 或更高版本创建服务，则服务默认使用 OS 系列 5。  有关详细信息，可查看[来宾 OS 系列支持表](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/)。

#### <a name="known-issues"></a>已知问题

- Azure .NET SDK 2.9.6 引入了一个限制，阻止用户使用不受支持的 .NET framework（例如 .NET 4.6）将项目部署到任何 < 5 的 OS 系列。 [此处](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)通过了一个解决方法。

 
### <a name="azure-in-role-cache"></a>Azure 角色中缓存 

- 对 Azure 角色中缓存的支持于 2016 年 11 月 30 日结束。 有关详细信息，请单击[此处](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)。

### <a name="azure-resource-manager-templates-for-azure-stack"></a>用于 Azure Stack 的 Azure Resource Manager 模板

- 我们引入了 Azure Stack 作为 Azure Resource Manager 模板的部署目标。


## <a name="azure-sdk-for-net-29-summary"></a>用于 .NET 2.9 的 Azure SDK 摘要

## <a name="overview"></a>概述
本文档包含 Azure SDK for .NET 2.9 版本的发行说明。 

有关此版本中的更新的详细信息，请参阅 [Azure SDK 2.9 通告文章](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)。

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 for Visual Studio 2015 Update 2 和 Visual Studio“15”预览版
此更新包含下列 bug 修复：

* 与 REST API 客户端生成有关的问题，在此问题中，字符串“未知类型”会作为代码生成文件夹的名称和/或放入所生成代码的命名空间的名称出现。
* 与计划 Web 作业有关的问题，在此问题中，验证信息无法传递给计划程序预配进程。

此更新包含下列新功能：

* 应用服务预配对话框的“服务”选项卡中的辅助应用程序服务支持。 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools for Visual Studio 2015 Update 2
此更新包含下列工具：

* 用于 Visual Studio 的 **Azure Data Lake 工具**现已合并到用于 .NET 的 Azure SDK 版本中。 安装 Azure SDK 时，便会自动安装此工具。 
  
    此工具经常更新，请转到[此处](http://aka.ms/datalaketool)获取更新。
* **服务器资源管理器**现在支持查看所有内容并创建一些 U-SQL 元数据实体。 有关详细信息，请参阅[此博客](https://azure.microsoft.com/documentation/services/data-lake-analytics/)。

## <a name="hdinsight-tools"></a>HDInsight 工具
用于 Visual Studio 的 **HDInsight 工具**现在支持 HDInsight 3.3 版，包括显示 Tez 图形和其他语言修复。

## <a name="azure-resource-manager"></a>Azure Resource Manager
此版本添加了对 Resource Manager 模板的[密钥保管库](../azure-resource-manager/resource-manager-keyvault-parameter.md)支持。

## <a name="see-also"></a>另请参阅
[Azure SDK 2.9 通告文章](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

