---
title: Azure 状态监视器 v2 API 参考：获取配置 |Microsoft Docs
description: 状态监视器 v2 API 引用 Get-ApplicationInsightsMonitoringConfig。 监视网站性能，无需重新部署该网站。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 0e6b47c9b629aed28fa217cb6299edb57423fc6f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870472"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>状态监视器 v2 API:Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

本文档介绍了作为的成员提供的 cmdlet [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 状态监视器 v2 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅[Supplemental Terms of Use 针对 Microsoft Azure 预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

获取配置文件和打印到控制台的值。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parameters 

（没有所需的参数）

## <a name="output"></a>输出


#### <a name="example-output-from-reading-the-config-file"></a>读取配置文件的示例输出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```
