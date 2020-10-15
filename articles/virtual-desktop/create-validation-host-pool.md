---
title: Windows 虚拟桌面主机池服务更新 - Azure
description: 如何创建验证主机池以在将更新推广到生产之前监视服务更新。
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8eb8378480b8784939e3ab63327e73e3705c74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526504"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>教程：创建主机池以验证服务更新

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)。

主机池是 Windows 虚拟桌面环境中包含一个或多个相同虚拟机的集合。 我们强烈建议先创建一个要应用服务更新的验证主机池。 这样可以在服务将更新应用于标准或非验证环境之前，监视服务更新。 如果没有验证主机池，则可能无法发现引入错误的更改，这可能会在标准环境中对用户导致故障时间。

若要确保应用适用于最新更新，验证主机池应尽可能地与非验证环境中的主机池类似。 用户应如同连接到标准主机池一样频繁地连接到验证主机池。 如果在主机池上进行自动测试，则应在验证主机池上包含自动测试。

可以使用[诊断功能](diagnostics-role-service.md)或 [Windows 虚拟桌面故障排除文章](troubleshoot-set-up-overview.md)对验证主机池中的问题进行调试。

>[!NOTE]
> 建议保留验证主机池以测试所有将来的更新。

>[!IMPORTANT]
>集成 Azure 资源管理器的 Windows 虚拟桌面目前在启用和禁用验证环境时会遇到麻烦。 当解决此问题后，我们会更新本文。

## <a name="prerequisites"></a>先决条件

在开始之前，请按照[设置 Windows 虚拟桌面 PowerShell 模块](powershell-module.md)中的说明设置 PowerShell 模块并登录到 Azure。

## <a name="create-your-host-pool"></a>创建主机池

可以按照以下任何文章中的说明创建主机池：
- [教程：通过 Azure 市场创建主机池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>将主机池定义为验证主机池

运行以下 PowerShell cmdlet 可将新主机池定义为验证主机池。 将括号中的值替换为与你的会话相关的值：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

运行以下 PowerShell cmdlet 可确认是否设置了验证属性。 将括号中的值替换为与你的会话相关的值。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

cmdlet 的结果应类似于以下输出：

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>更新计划

服务更新每月进行一次。 如果存在重大问题，则会更频繁地提供关键更新。

如果有任何服务更新，请确保每天至少有一小组用户登录，以验证环境。 建议你定期访问我们的 [TechCommunity 站点](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true)并关注包含 WVDUPdate 的任何帖子，以便随时了解服务更新。

## <a name="next-steps"></a>后续步骤

既然已创建了验证主机池，接下来可以了解如何使用 Azure 服务运行状况来监视 Windows 虚拟桌面部署。

> [!div class="nextstepaction"]
> [设置服务警报](./set-up-service-alerts.md)
