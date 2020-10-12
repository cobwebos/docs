---
title: 删除 Windows 虚拟桌面主机池-Azure
description: 如何删除 Windows 虚拟桌面中的主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88007583"
---
# <a name="delete-a-host-pool"></a>删除主机池

在 Windows 虚拟桌面中创建的所有主机池均附加到会话主机和应用组。 若要删除主机池，需要删除其关联的应用组和会话主机。 删除应用组相当简单，但删除会话主机会更复杂。 删除会话主机时，需要确保该主机没有任何活动的用户会话。 应注销会话主机上的所有用户会话，以防用户丢失数据。

## <a name="delete-a-host-pool-with-powershell"></a>使用 PowerShell 删除主机池

若要使用 PowerShell 删除主机池，首先需要删除该主机池中的所有应用组。 若要删除所有应用组，请运行以下 PowerShell cmdlet：

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

接下来，运行此 cmdlet 以删除主机池：

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

此 cmdlet 将删除主机池的会话主机上的所有现有用户会话。 它还将从主机池中注销会话主机。 任何相关的虚拟机 (Vm) 仍将存在于你的订阅中。

## <a name="delete-a-host-pool-with-the-azure-portal"></a>删除具有 Azure 门户的主机池

删除 Azure 门户中的主机池：

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 搜索并选择“Windows 虚拟桌面”  。

3. 在页面左侧的菜单中选择 " **主机池** "，然后选择要删除的主机池的名称。

4. 在页面左侧的菜单中，选择 " **应用程序组**"。

5. 选择要删除的主机池中的所有应用程序组，然后选择 " **删除**"。

6. 删除应用组后，请切换到页面左侧的菜单，并选择 " **概述**"。

7. 选择“删除” 。

8. 如果要删除的主机池中有会话主机，你会看到一条消息，要求你提供权限才能继续。 请选择“是”。

9. Azure 门户将立即删除所有会话主机并删除主机池。 与会话主机相关的 Vm 不会被删除，并将保留在订阅中。

## <a name="next-steps"></a>后续步骤

若要了解如何创建主机池，请查看以下文章：

- [使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)

若要了解如何配置主机池设置，请查看以下文章：

- [自定义主机池远程桌面协议属性](customize-rdp-properties.md)
- [配置 Windows 虚拟桌面负载均衡方法](configure-host-pool-load-balancing.md)
- [配置个人桌面主机池分配类型](configure-host-pool-personal-desktop-assignment-type.md)