---
title: Threat Modeling Tool 发行版 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: 阐述 Threat Modeling Tool 的发行说明
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6ad6eca20827e1683237b2c668eba71acedfaade
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917625"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>威胁建模工具更新版本 7.1.60408.1-4/9/2019

发布上 9 2019 年 4 月版本 7.1.60408.1 Microsoft 威胁建模工具 (TMT) 的并且包含以下更改：

- Azure 密钥保管库和 Azure 流量管理器的新模具
- 现在主屏幕上显示 TMT 版本数量
- 已更新的支持链接
- Bug 修复

## <a name="feature-changes"></a>功能更改

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure 密钥保管库和 Azure 流量管理器的新模具

![Azure 密钥保管库模具](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

新模板和 Azure 密钥保管库和 Azure 流量管理器的威胁已加入 Azure 模具集。 在打开模型基于 Azure 的模具集时，用户将会提示你更新与模型关联的模板。 更新基于 Azure 的模具集的模型还可以手动启动通过使用"文件"菜单中的"应用模板"命令并重新应用最新的 Azure 云 Services.tb7 文件。

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>现在主屏幕上显示 TMT 版本数量

威胁建模工具的客户端版本现已显示的用于轻松访问的应用程序的主屏幕上。

![Azure 密钥保管库模具](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>已更新的支持链接

已更新为将用户定向到在该工具中的所有支持链接[ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com)而不是 MSDN 论坛。

## <a name="system-requirements"></a>系统要求

- 支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
