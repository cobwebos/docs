---
title: 微软威胁建模工具版本 10/16/2019 - Azure
description: 阐述 Threat Modeling Tool 的发行说明
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552043"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 更新版 7.1.61015.1 - 2019/10/16

Microsoft 威胁建模工具 （TMT） 的版本 7.1.61015.1 于 2019 年 10 月 16 日发布，其中包含以下更改：

- 辅助功能改进
- Bug 修复
- Azure 逻辑应用和 Azure 数据资源管理器的新模具

## <a name="notable-bug-fixes"></a>值得注意的错误修复

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>改进了与"威胁建模工具 2016"中创建的文件的向后兼容性

修复了与"威胁建模工具 2016"中创建的威胁模型文件的打开或显示相关的几个错误。

## <a name="feature-enhancements"></a>功能增强功能

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Azure 逻辑应用和 Azure 数据资源管理器的新模具

Azure 逻辑应用和 Azure 数据资源管理器的新模具连同其关联的威胁和缓解措施一起添加到 Azure 模具中。

![Azure 逻辑应用和 Azure 数据资源管理器模具](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>已知问题

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>与预期范围以外的优先级值相关的错误

一些客户在打开"威胁建模工具 2016"或自定义模板中创建的文件时报告收到以下错误消息：

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

此问题正在调查中

## <a name="system-requirements"></a>系统要求

- 受支持的操作系统
  - [微软视窗 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.净 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
