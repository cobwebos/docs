---
title: Azure Application Insights 代理 API 参考
description: Application Insights 代理 API 参考。 Disable-InstrumentationEngine。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671369"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>应用程序见解代理 API：禁用检测引擎

本文介绍属于 [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的 cmdlet。

## <a name="description"></a>描述
通过删除一些注册表项来禁用检测引擎。
重启 IIS 以使这些更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>参数 

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关输出详细的日志。

## <a name="output"></a>输出


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功禁用检测引擎的示例输出

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>后续步骤

 使用 Application Insights 代理执行更多操作：
 - 使用我们的指南对 Application Insights 代理进行[故障排除](status-monitor-v2-troubleshoot.md)。
