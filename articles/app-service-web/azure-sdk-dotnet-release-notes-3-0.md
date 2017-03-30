---
title: "Azure SDK for .NET 3.0 发行说明 | Microsoft 文档"
description: "Azure SDK for .NET 3.0 发行说明"
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
ms.date: 03/07/2017
ms.author: juliako;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: dea4a174aaf3727d66e9d69d32d433ff24e0d06d
ms.lasthandoff: 03/22/2017


---
# <a name="azure-sdk-for-net-30-release-notes"></a>Azure SDK for .NET 3.0 发行说明

本主题包括用于 .NET 的 Azure SDK 版本 3.0 的发行说明。

##<a name="azure-sdk-for-net-30-release-summary"></a>Azure SDK for .NET 3.0 发行摘要

发布日期：2017/03/07
 
此版本中未引入任何对 Azure SDK 3.0 的重大更改。 此外，将此 SDK 用于现有云服务项目也无需任何升级过程。 若要在无需升级过程的情况下允许使用 Azure SDK 3.0，则需将 Azure SDK 3.0 安装到与 Azure SDK 2.9 相同的目录中。 大多数组件没有更改主版本 2.9，而只是更新了内部版本号。

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- 在 Visual Studio 2017 中，此版本的用于 .NET 的 Azure SDK 内置于 Azure 工作负荷中。 从目前开始，进行 Azure 开发所需的所有工具都将成为 Visual Studio 2017 的一部分。 对于 Visual Studio 2015，仍可通过 WebPI 获取 SDK。 既然已发布 Visual Studio 2017，我们已停止使用适用于 Visual Studio 2013 的 Azure SDK.NET 发行版。

### <a name="azure-diagnostics"></a>Azure 诊断

- 针对云服务诊断存储连接字符串更改了行为，以便仅存储部分连接字符串并将密钥替换为令牌。 实际存储密钥现存储在用户配置文件文件夹中，以便可控制其访问。 Visual Studio 将从用户配置文件文件夹中读取存储密钥，以便进行本地调试和发布过程。 
- 针对上述更改，Visual Studio Online 团队增强了 Azure 云服务部署任务模板，以便以持续集成和部署发布到 Azure 时，用户可为设置诊断扩展指定存储密钥。
- 现在可存储 Azure 诊断 (WAD) 的安全连接字符串和词汇切分，以帮助解决跨环境配置的问题。
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 虚拟机

- Visual Studio 现在支持将云服务部署到操作系统系列 5 (Windows Server 2016) 虚拟机。 对于现有云服务，可更改设置以面向新的 OS 系列。 创建新的云服务时，如果选择创建使用 .NET 4.6 或更高版本的服务，则该服务将默认使用 OS 系列 5。  有关详细信息，可查看[来宾 OS 系列支持表](../cloud-services/cloud-services-guestos-update-matrix.md)。

### <a name="known-issues"></a>已知问题

- 删除与 Visual Studio 2015 使用并列配置的 Visual Studio 2017 时，Azure .NET SDK 3.0 引入了一个问题。  如果已为 Visual Studio 2015 安装 Azure SDK，卸载 Visual Studio 2017 时，将删除 Microsoft Azure 存储模拟器和 Microsoft Azure 计算模拟器。  在 Visual Studio 2015 中创建和调试新的云服务项目时，这将生成错误。 若要解决此问题，请从 Web 平台安装程序重新安装 Azure SDK。  此问题将在未来的 Visual Studio 2017 更新中解决。  。

 
### <a name="azure-in-role-cache"></a>Azure 角色中缓存 

- 从 2016 年 11 月 30 日起，将不再支持 Azure 角色中缓存。 有关详细信息，请单击[此处](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)。





