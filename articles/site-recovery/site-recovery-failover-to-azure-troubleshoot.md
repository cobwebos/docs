---
title: 对故障转移到 Azure 的故障进行排除 | Microsoft 文档
description: 本指南介绍如何解决在故障转移到 Azure 中的常见错误
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/11/2018
ms.author: mayg
ms.openlocfilehash: 742e7891ec9c7151f23f1ad6eb57e728dd2a1ddd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255085"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>解决从虚拟机到 Azure 的故障转移时出现的错误

在执行从虚拟机到 Azure 的故障转移时，可能会收到以下错误之一。 若要解决错误，请为每个错误条件使用所述步骤。

## <a name="failover-failed-with-error-id-28031"></a>故障转移失败，错误 ID 为 28031

Site Recovery 无法在 Azure 中创建故障转移的虚拟机。 以下其中一个原因也可能导致此情况的发生：

* 没有足够的可用配额来创建虚拟机：可以通过转到“订阅” -> “使用情况 + 配额”来检查可用配额。 可以打开 [新的支持请求](http://aka.ms/getazuresupport) 来增加此配额。

* 尝试在同一个可用性集中故障转移不同大小系列的虚拟机。 确保在同一个可用性集中选择相同大小系列的所有虚拟机。 可以转到虚拟机的“计算和网络”设置来更改大小，然后重试故障转移。

* 订阅上有一个阻止创建虚拟机的策略。 请更改此策略以允许创建虚拟机，然后重试故障转移。

## <a name="failover-failed-with-error-id-28092"></a>故障转移失败，错误 ID 为 28092

Site Recovery 无法为故障转移的虚拟机创建网络接口。 请确保订阅中有足够的配额来创建网络接口。 可以通过转到“订阅” -> “使用情况 + 配额”来检查可用配额。 可以打开 [新的支持请求](http://aka.ms/getazuresupport) 来增加此配额。 如果你拥有足够的配额，则这可能是一个间歇性的问题，请重试该操作。 如果即使在重试后问题仍然存在，请在本文档结尾处留下注释。  

## <a name="failover-failed-with-error-id-70038"></a>故障转移失败，错误 ID 为 70038

Site Recovery 无法在 Azure 中创建故障转移的经典虚拟机。 这可能是因为：

* 创建虚拟机所需的其中一个资源（如虚拟网络）不存在。 在虚拟机的“计算和网络”设置下创建虚拟网络，或者将设置修改为已经存在的虚拟网络，然后重试故障转移。

## <a name="failover-failed-with-error-id-170010"></a>故障转移失败，错误 ID 为 170010

Site Recovery 无法在 Azure 中创建故障转移的虚拟机。 发生此情况可能是因为本地虚拟机上执行的混合的一个内部活动失败。

若要启动 Azure 中的任何计算机，Azure 环境需要某些驱动程序处于引导启动状态，并要求 DHCP 之类的服务处于自动启动状态。 因此，在进行故障转移时，混合活动会将 **atapi、intelide、storflt、vmbus 和 storvsc 驱动程序**的启动类型转换为引导启动。 它还会将 DHCP 之类的一些服务的启动类型转换为自动启动。 此活动可能会由于环境特定问题而失败。 若要手动更改驱动程序的启动类型，请执行以下步骤：

1. [下载](http://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1)“非混合”脚本并如下所述运行脚本。 此脚本检查 VM 是否需要混合。

    `.\Script-no-hydration.ps1`

    如果需要混合，则它提供以下结果：

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    如果 VM 满足“非混合”要求，则脚本将提供结果“此系统满足‘非混合’要求”。 在此情况下，所有驱动程序和服务都处于 Azure 所需的状态并且不需要在 VM 上进行混合。

2. 如果 VM 不满足“非混合”要求，请如下所述运行“非混合”脚本。

    `.\Script-no-hydration.ps1 -set`
    
    这将转换驱动程序的启动类型并提供如下所示的结果：
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>由于虚拟机上的“连接”按钮已灰显，无法连接/通过 RDP/SSH 连接到已故障转移的虚拟机

如果 Azure 中已故障转移的 VM 的“连接”按钮灰显，并且你未通过快速路由或站点到站点 VPN 连接来连接到 Azure，则执行以下操作：

1. 转到“虚拟机” > “网络”，单击所需网络接口的名称。  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. 导航到“IP 配置”，然后单击所需 IP 配置的名称字段。 ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. 若要启用公共 IP 地址，请单击“启用”。 ![启用 IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. 单击“配置所需设置” > “新建”。 ![新建](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. 输入公共地址的名称，选择“SKU”和“分配”的默认选项，然后单击“确定”。
6. 现在，单击“保存”以保存所做的更改。
7. 关闭面板并导航到虚拟机的“概述”部分以进行连接/通过 RDP 连接。

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>无法连接/RDP/SSH - VM 的“连接”按钮可用

如果 Azure 中已故障转移的 VM 的“连接”按钮可用（未灰显），则请检查虚拟机上的“启动诊断”，查看是否有[此文](../virtual-machines/windows/boot-diagnostics.md)中所列的错误。

1. 如果虚拟机尚未启动，请尝试故障转移到以前的恢复点。
2. 如果虚拟机中的应用程序未启动，请尝试故障转移到应用一致的恢复点。
3. 如果虚拟机已加入域，请确保域控制器正确运行。 可以按照下面给出的步骤执行此操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在同一网络中创建新的虚拟机。

    b.  确保能够加入其中应启动故障转移的虚拟机的同一域。

    c. 如果域控制器**未**正确运行，请尝试使用本地管理员帐户登录到故障转移的虚拟机。
4. 如果使用自定义 DNS 服务器，请确保可以访问该服务器。 可以按照下面给出的步骤执行此操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在同一网络中创建新的虚拟机；

    b. 检查虚拟机是否能够使用自定义 DNS 服务器解析名称

>[!Note]
>启用除“启动诊断”以外的任何设置，都需要在故障转移之前在虚拟机中安装 Azure VM 代理

## <a name="unexpected-shutdown-message-event-id-6008"></a>意外关闭消息（事件 ID 6008）

在故障转移后启动 Windows VM 时，如果在恢复的 VM 上收到一条有关意外关闭的消息，则表明在用于故障转移的恢复点中未捕获 VM 关闭状态。 如果恢复到某个点，在这个点 VM 尚未完全关闭，则会发生这种情况。

对于计划外故障转移，这通常不需要担心，通常可以忽略。 对于计划内故障转移，请确保在故障转移之前 VM 正常关闭，并提供足够的时间让待发送的本地复制数据发送到 Azure。 然后使用[“故障转移”屏幕](site-recovery-failover.md#run-a-failover)上的“最新”选项，将 Azure 上的任何待处理数据处理到一个恢复点中，随后用于 VM 故障转移。

## <a name="next-steps"></a>后续步骤
- 排查[通过 RDP 连接到 Windows VM](../virtual-machines/windows/troubleshoot-rdp-connection.md) 的问题
- 排查[通过 SSH 连接到 Linux VM](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md) 的问题

如需更多帮助，请将疑问发布到 [Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)，或在本文档结尾处留下注释。 我们的活动社区应能够为你提供帮助。
