---
title: Windows 虚拟桌面（经典）主机池服务更新 - Azure
description: 如何创建验证主机池以在将更新推广到生产之前监视服务更新。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 572b74347194070615efadca4ed6b1329b3f89c2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526793"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>教程：在 Windows 虚拟桌面（经典）中创建主机池以验证服务更新

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../create-validation-host-pool.md)。

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 建议先创建一个要应用服务更新的验证主机池。 这样可以在服务将更新应用于标准或非验证环境之前，监视服务更新。 如果没有验证主机池，则可能无法发现引入错误的更改，这可能会在生产环境中对用户导致故障时间。

若要确保应用适用于最新更新，验证主机池应尽可能地与非验证环境中的主机池类似。 用户应如同连接到标准主机池一样频繁地连接到验证主机池。 如果在主机池上进行自动测试，则应在验证主机池上包含自动测试。

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
> [设置服务警报](set-up-service-alerts-2019.md)
