---
title: 微软威胁建模工具版本 4/9/2019
titleSuffix: Azure
description: 阐述 Threat Modeling Tool 的发行说明
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269717"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>威胁建模工具更新版本 7.1.60408.1 - 2019/4/9

Microsoft 威胁建模工具 （TMT） 的版本 7.1.60408.1 于 2019 年 4 月 9 日发布，其中包含以下更改：

- Azure 密钥保管库和 Azure 流量管理器的新模具
- TMT 版本号现在显示在主屏幕上
- 已更新支持链接
- Bug 修复

## <a name="feature-changes"></a>功能更改

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure 密钥保管库和 Azure 流量管理器的新模具

![Azure 密钥保管库模具](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Azure 密钥保管库和 Azure 流量管理器的新模具和威胁已添加到 Azure 模具集中。 基于 Azure 模具集打开模型时，将提示用户更新与模型关联的模板。 还可以通过使用"文件"菜单中的"应用模板"命令并重新应用最新的 Azure 云服务.tb7 文件，手动启动基于 Azure 模具集的更新模型。

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT 版本号现在显示在主屏幕上

为了便于访问，威胁建模工具的客户端版本现在显示在应用程序的主屏幕上。

![Azure 密钥保管库模具](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>已更新支持链接

该工具中的所有支持链接都已更新，以指示用户访问[tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com)MSDN 论坛，而不是 MSDN 论坛。

## <a name="system-requirements"></a>系统要求

- 受支持的操作系统
  - [微软视窗 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.净 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](threat-modeling-tool.md)，其中包含[有关如何使用该工具](threat-modeling-tool-getting-started.md)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
