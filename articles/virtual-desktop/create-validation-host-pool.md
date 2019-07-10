---
title: 创建 Windows 虚拟桌面预览版主机池以验证服务更新 - Azure
description: 如何创建验证主机池以在将更新推广到生产之前监视服务更新。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c9b2a593a6943fe2e9577acc61b1d5a7bcd98607
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070659"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>教程：创建主机池以验证服务更新

主机池是 Windows 虚拟桌面预览版租户环境中一个或多个相同虚拟机的集合。 在将主机池部署到生产环境之前，强烈建议创建验证主机池。 更新首先应用于验证主机池，从而可以监视在将服务更新推广到生产环境之前监视它们。 如果没有验证主机池，则可能无法发现引入错误的更改，这可能会在生产环境中对用户导致故障时间。

若要确保应用适用于最新更新，验证主机池应尽可能类似于生产环境中的主机池。 用户应如同连接到生成主机池一样频繁地连接到验证主机池。 如果在主机池上进行自动测试，则应在验证主机池上包含自动测试。

可以使用[诊断功能](diagnostics-role-service.md)或 [Windows 虚拟桌面故障排除文章](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview)对验证主机池中的问题进行调试。

>[!NOTE]
> 建议保留验证主机池以测试所有将来的更新。

在开始之前，请[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做）。

## <a name="create-your-host-pool"></a>创建主机池

可以按照以下任何文章中的说明创建主机池：
- [教程：通过 Azure 市场创建主机池](create-host-pools-azure-marketplace.md)
- [使用 Azure 资源管理器模板创建主机池](create-host-pools-arm-template.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>将主机池定义为验证主机池

运行以下 PowerShell cmdlet 可将新主机池定义为验证主机池。 将引号中的值替换为与会话相关的值：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

运行以下 PowerShell cmdlet 可确认是否设置了验证属性。 将引号中的值替换为与会话相关的值。

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

cmdlet 的结果应类似于以下输出：

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>更新计划

在预览版中，服务更新大约每月进行一次。 如果存在重大问题，则会更频繁地提供关键更新。

## <a name="next-steps"></a>后续步骤

现在已创建了验证主机池，你可以了解如何部署和连接到用于管理 Microsoft 虚拟桌面资源的管理工具。

> [!div class="nextstepaction"]
> [部署管理工具教程](./manage-resources-using-ui.md)
