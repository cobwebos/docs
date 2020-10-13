---
title: 设置 Windows 虚拟桌面灾难恢复计划-Azure
description: 如何为 Windows 虚拟桌面部署设置业务连续性和灾难恢复计划。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935655"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>设置业务连续性和灾难恢复计划

为了确保组织数据的安全，你可能需要采用业务连续性和灾难恢复 (BCDR) 策略。 当计划内和计划外服务或 Azure 中断期间，BCDR 策略会使应用和工作负荷保持启动并运行。

Windows 虚拟桌面为 Windows 虚拟桌面服务提供 BCDR，以便在中断期间保留客户元数据。 当某个区域发生服务中断时，服务基础结构组件会故障转移到辅助位置，并继续正常运行。 你仍可以访问与服务相关的元数据，并且用户仍可以连接到可用的主机。 只要租户环境或主机保持可访问状态，最终用户连接就将保持联机状态。

为了确保用户在区域服务中断期间仍可以连接，你需要将其虚拟机 (复制到不同位置) 的 Vm。 在中断期间，主站点故障转移到辅助位置中的复制 Vm。 用户可以继续从辅助位置访问应用，而无需中断。 在 VM 复制的基础上，你需要在辅助位置保留用户标识。 如果使用的是配置文件容器，则还需要复制它们。 最后，请确保依赖于主位置数据的业务应用可与其他数据进行故障转移。

总而言之，若要使用户在中断期间保持连接状态，需要按以下顺序执行以下操作：

- 复制辅助位置中的 Vm。
- 如果使用的是配置文件容器，请在辅助位置设置数据复制。
- 请确保在主要位置设置的用户标识在辅助位置中可用。
- 确保依赖于主位置中数据的任何业务线应用程序故障转移到辅助位置。

## <a name="vm-replication"></a>VM 复制

首先，需要将 Vm 复制到辅助位置。 用于执行此操作的选项取决于 Vm 的配置方式：

- 可以通过 Azure Site Recovery 为共用和个人主机池配置所有 Vm。 利用此方法，只需设置一个主机池及其相关的应用组和工作区。
- 你可以在故障转移区域中创建新的主机池，同时将所有资源保持为关闭状态。 对于此方法，需要在故障转移区域中设置新的应用组和工作区。 然后，可以使用 Azure Site Recovery 计划来打开主机池。
- 你可以创建一个由在主区域和故障转移区域中内置的 Vm 填充的主机池，同时保持故障转移区域中的 Vm 处于关闭状态。 在这种情况下，只需设置一个主机池及其相关的应用组和工作区。 可以使用此方法使用 Azure Site Recovery 计划为主机池接通电源。

建议使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 管理其他 azure 位置中的 vm，如 [azure 到 azure 灾难恢复体系结构](../site-recovery/azure-to-azure-architecture.md)中所述。 我们特别推荐对个人主机池使用 Azure Site Recovery，因为 Azure Site Recovery 支持 [基于服务器的 sku 和基于客户端的 sku](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。

如果使用 Azure Site Recovery，则无需手动注册这些 Vm。 辅助 VM 中的 Windows 虚拟桌面代理将自动使用最新的安全令牌连接到最靠近它的服务实例。 辅助位置) 的 VM (会话主机将自动成为主机池的一部分。 在此过程中，最终用户必须重新连接，但除此之外，没有其他手动操作。

如果在中断期间存在用户连接，则在管理员开始故障转移到次要区域之前，你需要结束当前区域中的用户连接。

若要在 Windows 虚拟桌面 (经典) 中断开用户的连接，请运行以下 cmdlet：

```powershell
Invoke-RdsUserSessionLogoff
```

若要在 Azure 集成版本的 Windows 虚拟桌面中断开用户的连接，请运行以下 cmdlet：

```powershell
Remove-AzWvdUserSession
```

在主要区域中注销所有用户后，你可以对主要区域中的 Vm 进行故障转移，并让用户连接到次要区域中的 Vm。 有关此过程的工作原理的详细信息，请参阅 [将 Azure Vm 复制到另一 azure 区域](../site-recovery/azure-to-azure-how-to-enable-replication.md)。

## <a name="virtual-network"></a>虚拟网络

接下来，请考虑在中断期间网络连接。 你需要确保已在你的次要区域中 (VNET) 设置了虚拟网络。 如果用户需要访问本地资源，则需要配置此 VNET 来访问这些资源。 可以使用 VPN、ExpressRoute 或虚拟 WAN 建立本地连接。

建议使用 Azure Site Recovery 在故障转移区域中设置 VNET，因为它会保留你的主网络的设置，而不需要对等互连。

## <a name="user-identities"></a>用户标识

接下来，请确保域控制器在辅助位置可用。 

可以通过三种方式来使域控制器保持可用：

   - 在辅助位置有 Active Directory 域控制器
   - 使用本地 Active Directory 域控制器
   - 使用[Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)复制 Active Directory 域控制器

## <a name="user-and-app-data"></a>用户和应用数据

如果使用的是配置文件容器，则下一步是在辅助位置中设置数据复制。 有五个选项可用于存储 FSLogix 配置文件：

   - 存储空间直通 (S2D)
   - 带有额外驱动器的网络驱动器 (VM) 
   - Azure 文件
   - Azure NetApp 文件
   - 用于复制的云缓存

有关详细信息，请参阅 [Windows 虚拟桌面中的 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

如果要设置配置文件的灾难恢复，可选择以下选项：

   - 设置本机 Azure 复制 (例如，Azure 文件标准存储帐户复制、Azure NetApp 文件复制或 Azure 文件同步) 文件服务器。
    
     >[!NOTE]
     >首次设置 NetApp 后，NetApp 复制会自动进行。 利用 Azure Site Recovery 计划，你可以添加用于故障转移非 VM 资源的前脚本和后脚本复制 Azure 存储资源。

   - 为应用和用户数据设置 FSLogix 云缓存。
   - 仅为应用数据设置灾难恢复，以确保始终能够访问业务关键数据。 利用此方法，你可以在中断后检索用户数据。

让我们看一看如何配置 FSLogix 以设置每个选项的灾难恢复。

### <a name="fslogix-configuration"></a>FSLogix 配置

如果为 FSLogix 配置了注册表项，则 FSLogix 代理可以支持多个配置文件位置。

若要配置注册表项：

1. 打开 **注册表编辑器**。
2. 请参阅**计算机**  >  **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **FSLogix**profile  >  **Profiles**。
   
     > [!div class="mx-imgBorder"]
     > ![注册表编辑器中的 "配置文件" 窗口的屏幕截图。 已选择 VHDLocation。](media/regedit-profiles.png)

3. 右键单击 **VHDLocations** ，然后选择 " **编辑多字符串**"。

     > [!div class="mx-imgBorder"]
     > !["编辑多字符串" 窗口的屏幕截图。 值数据列出了 "美国 Centrual" 和 "美国东部" 位置。](media/multi-string-edit.png)

4. 在 " **数值数据** " 字段中，输入要使用的位置。
5. 完成后，请选择“确定”。

如果第一个位置不可用，则 FSLogix 代理将自动故障转移到第二个位置，依此类推。

建议使用主区域中的辅助位置的路径配置 FSLogix 代理。 主位置关闭后，FLogix 代理将作为 VM Azure Site Recovery 复制的一部分进行复制。 复制的 Vm 准备就绪后，代理将自动尝试路径到次要区域。

例如，假设你的主会话主机 Vm 位于美国中部区域，但出于性能方面的考虑，你的配置文件容器位于美国中部。

在这种情况下，可以使用美国中部存储的路径配置 FSLogix 代理。 你将在美国西部将会话主机 Vm 配置为复制。 美国中部的路径失败后，代理将尝试在美国西部创建新的存储路径。

### <a name="s2d"></a>S2D

由于 S2D 在内部处理跨区域的复制，因此不需要手动设置辅助路径。

### <a name="network-drives-vm-with-extra-drives"></a>带有额外驱动器的网络驱动器 (VM) 

如果使用 Azure Site Recovery 如会话主机 Vm 来复制网络存储 Vm，则恢复将保留相同的路径，这意味着无需重新配置 FSlogix。

### <a name="azure-files"></a>Azure 文件

Azure 文件支持可在创建存储帐户时指定的跨区域异步复制。 如果 Azure 文件的异步特性已经涵盖灾难恢复目标，则无需进行其他配置。

如果你需要同步复制以最大程度地减少数据丢失，则建议你改为使用 FSLogix Cloud Cache。

>[!NOTE]
>本部分未介绍 Azure 文件的故障转移身份验证机制。

### <a name="azure-netapp-files"></a>Azure NetApp 文件

若要详细了解 Azure NetApp 文件，请参阅 [创建 Azure Netapp 文件的复制对等互连](../azure-netapp-files/cross-region-replication-create-peering.md)。

## <a name="app-dependencies"></a>应用依赖项

最后，请确保依赖于主区域中的数据的任何业务应用程序都可以故障转移到辅助位置。 此外，请确保在新位置配置应用需要使用的设置。 例如，如果其中一个应用依赖于 SQL 后端，请确保在辅助位置复制 SQL。 应将应用配置为使用辅助位置作为故障转移过程的一部分，或配置为其默认配置。 你可以对 Azure Site Recovery 计划的应用程序依赖关系建模。 若要了解详细信息，请参阅 [关于恢复计划](../site-recovery/recovery-plan-overview.md)。

## <a name="disaster-recovery-testing"></a>灾难恢复测试

设置完灾难恢复后，需要测试计划以确保其正常运行。

下面是有关如何测试计划的一些建议：

- 如果测试 Vm 具有 internet 访问权限，它们将接管新连接的任何现有会话主机，但与原始会话主机的所有现有连接将保持活动状态。 请确保运行测试的管理员在测试计划前注销所有活动的用户。 
- 你只应在维护时段内执行完全的灾难恢复测试，以免干扰你的用户。 你还可以在验证环境中使用主机池进行测试。 
- 请确保您的测试涵盖了所有关键业务应用程序。
- 建议一次仅故障转移到 100 Vm。 如果 Vm 数多于此数量，我们建议你在每10分钟的时间内对其进行故障转移。

## <a name="next-steps"></a>后续步骤

如果除了规划中断外，还存在有关如何保护数据安全的问题，请参阅我们的 [安全指南](security-guide.md)。