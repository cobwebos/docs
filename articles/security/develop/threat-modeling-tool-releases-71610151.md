---
title: Threat Modeling Tool 发行版 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: 记录威胁建模工具的发行说明
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
ms.date: 10/16/2019
ms.author: jegeib
ms.openlocfilehash: ba18989b72f0c3f44099031a6949acc54ce41db0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233826"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 更新版 7.1.61015.1 - 2019/10/16

16 2019 年10月发布了 Microsoft Threat Modeling Tool （TMT）的版本7.1.61015.1，其中包含以下更改：

- 辅助功能改进
- Bug 修复
- 适用于 Azure 逻辑应用和 Azure 数据资源管理器的新模具

## <a name="notable-bug-fixes"></a>值得注意的 bug 修复

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>提高了与 "Threat Modeling Tool 2016" 中创建的文件的向后兼容性

已修复与打开或显示在 "Threat Modeling Tool 2016" 中创建的威胁模型文件相关的几个 bug。

## <a name="feature-enhancements"></a>功能增强

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>适用于 Azure 逻辑应用和 Azure 数据资源管理器的新模具

已将适用于 Azure 逻辑应用和 Azure 数据资源管理器的新模具添加到 Azure 模具及其相关威胁和缓解措施。

![Azure 逻辑应用和 Azure 数据资源管理器模具](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>已知问题

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>与预期范围外的优先级值相关的错误

当打开在 "Threat Modeling Tool 2016" 或自定义模板中创建的文件时，某些客户报告接收到以下错误消息：

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

此问题正在调查中

## <a name="system-requirements"></a>系统要求

- 支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
