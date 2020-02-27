---
title: Microsoft Threat Modeling Tool 版本 02/11/2020-Azure
description: 阐述 Threat Modeling Tool 的发行说明
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624834"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool 更新版本 7.3.00206.1-02/11/2020

Microsoft Threat Modeling Tool （TMT）的版本7.3.00206.1 于 11 2020 年2月发布，其中包含以下更改：

- Bug 修复

## <a name="notable-bug-fixes"></a>值得注意的 bug 修复

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>与预期范围外的优先级值相关的错误

当打开在 "Threat Modeling Tool 2016" 或自定义模板中创建的文件时，某些客户报告接收到以下错误消息：

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

此问题已在此版本中得到解决。

## <a name="system-requirements"></a>系统要求

- 受支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
