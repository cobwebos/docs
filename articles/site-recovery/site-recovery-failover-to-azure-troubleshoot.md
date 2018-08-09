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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: b7b5dcd88b6e4e09dd9beb21e83ef405df148115
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443378"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>解决从虚拟机到 Azure 的故障转移时出现的错误

在执行从虚拟机到 Azure 的故障转移时，可能会收到以下错误之一。 若要解决错误，请为每个错误条件使用所述步骤。

## <a name="failover-failed-with-error-id-28031"></a>故障转移失败，错误 ID 为 28031

Site Recovery 无法在 Azure 中创建故障转移的虚拟机。 以下其中一个原因也可能导致此情况的发生：

* 创建虚拟机的可用配额不足：你可以通过转到“订阅” -> “使用情况 + 配额”来检查可用配额。 可以打开 [新的支持请求](http://aka.ms/getazuresupport) 来增加此配额。

* 尝试在同一个可用性集中故障转移不同大小系列的虚拟机。 确保在同一个可用性集中选择相同大小系列的所有虚拟机。 可以转到虚拟机的“计算和网络”设置来更改大小，然后重试故障转移。

* 订阅上有一个阻止创建虚拟机的策略。 请更改此策略以允许创建虚拟机，然后重试故障转移。

## <a name="failover-failed-with-error-id-28092"></a>故障转移失败，错误 ID 为 28092

Site Recovery 无法为故障转移的虚拟机创建网络接口。 请确保订阅中有足够的配额来创建网络接口。 可以通过转到“订阅” -> “使用情况 + 配额”来检查可用配额。 可以打开 [新的支持请求](http://aka.ms/getazuresupport) 来增加此配额。 如果你拥有足够的配额，则这可能是一个间歇性的问题，请重试该操作。 如果即使在重试后问题仍然存在，请在本文档结尾处留下注释。  

## <a name="failover-failed-with-error-id-70038"></a>故障转移失败，错误 ID 为 70038

Site Recovery 无法在 Azure 中创建故障转移的经典虚拟机。 这可能是因为：

* 创建虚拟机所需的其中一个资源（如虚拟网络）不存在。 在虚拟机的“计算和网络”设置下创建虚拟网络，或者将设置修改为已经存在的虚拟网络，然后重试故障转移。

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>由于虚拟机上的“连接”按钮已灰显，无法连接/通过 RDP/SSH 连接到已故障转移的虚拟机

如果“连接”按钮灰显，并且你未通过快速路由或站点到站点 VPN 连接来连接到 Azure，则执行以下操作：

1. 转到“虚拟机” > “网络”，单击所需网络接口的名称。  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
1. 导航到“IP 配置”，然后单击所需 IP 配置的名称字段。 ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
1. 若要启用公共 IP 地址，请单击“启用”。 ![启用 IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
1. 单击“配置所需设置” > “新建”。 ![新建](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
1. 输入公共地址的名称，选择“SKU”和“分配”的默认选项，然后单击“确定”。
1. 现在，单击“保存”以保存所做的更改。
1. 关闭面板并导航到虚拟机的“概述”部分以进行连接/通过 RDP 连接。

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>即使虚拟机上的“连接”按钮可用（未灰显），也无法连接/通过 RDP/SSH 连接到已故障转移的虚拟机

检查虚拟机上的“启动诊断”，查看是否有本文中所列的错误。

1. 如果虚拟机尚未启动，请尝试故障转移到以前的恢复点。
1. 如果虚拟机中的应用程序未启动，请尝试故障转移到应用一致的恢复点。
1. 如果虚拟机已加入域，请确保域控制器正确运行。 可以按照下面给出的步骤执行此操作。
    a. 在同一网络中创建新的虚拟机

    b.  确保能够加入其中应启动故障转移的虚拟机的同一域。

    c. 如果域控制器未正确运行，请尝试使用本地管理员帐户登录到故障转移的虚拟机
1. 如果使用自定义 DNS 服务器，请确保可以访问该服务器。 可以按照下面给出的步骤执行此操作。
    a. 在同一网络中创建新的虚拟机；b. 检查虚拟机是否能够使用自定义 DNS 服务器解析名称

>[!Note]
>启用除“启动诊断”以外的任何设置，都需要在故障转移之前在虚拟机中安装 Azure VM 代理

## <a name="next-steps"></a>后续步骤

如需更多帮助，请将疑问发布到 [Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)，或在本文档结尾处留下注释。 我们的活动社区应能够为你提供帮助。
