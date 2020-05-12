---
title: Windows 虚拟桌面主机池服务更新 - Azure
description: 如何创建验证主机池以在将更新推广到生产之前监视服务更新。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 21c3e3c7ae515f5a740384d202451f61291bc05b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613843"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>教程：创建主机池以验证服务更新

>[!IMPORTANT]
>本教程的内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 2019 年秋季版。 如果要尝试管理 2020 年春季版更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../create-validation-host-pool.md)。

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 在将主机池部署到生产环境之前，强烈建议创建验证主机池。 更新首先应用于验证主机池，从而可以监视在将服务更新推广到生产环境之前监视它们。 如果没有验证主机池，则可能无法发现引入错误的更改，这可能会在生产环境中对用户导致故障时间。

若要确保应用适用于最新更新，验证主机池应尽可能类似于生产环境中的主机池。 用户应如同连接到生成主机池一样频繁地连接到验证主机池。 如果在主机池上进行自动测试，则应在验证主机池上包含自动测试。

可以使用[诊断功能](diagnostics-role-service-2019.md)或 [Windows 虚拟桌面故障排除文章](troubleshoot-set-up-overview-2019.md)对验证主机池中的问题进行调试。

>[!NOTE]
> 建议保留验证主机池以测试所有将来的更新。

在开始之前，请[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做）。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>创建主机池

可以按照以下任何文章中的说明创建主机池：
- [教程：通过 Azure 市场创建主机池](create-host-pools-azure-marketplace-2019.md)
- [使用 Azure 资源管理器模板创建主机池](create-host-pools-arm-template.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>将主机池定义为验证主机池

运行以下 PowerShell cmdlet 可将新主机池定义为验证主机池。 将引号中的值替换为与会话相关的值：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

运行以下 PowerShell cmdlet 可确认是否设置了验证属性。 将引号中的值替换为与会话相关的值。

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

cmdlet 的结果应类似于以下输出：

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>更新计划

服务更新每月进行一次。 如果存在重大问题，则会更频繁地提供关键更新。

## <a name="next-steps"></a>后续步骤

既然已创建了验证主机池，接下来可以了解如何使用 Azure 服务运行状况来监视 Windows 虚拟桌面部署。 

> [!div class="nextstepaction"]
> [设置服务警报](../set-up-service-alerts.md)
