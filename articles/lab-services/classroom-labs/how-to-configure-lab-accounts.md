---
title: 在 Azure 实验室服务中配置实验室帐户 |Microsoft Docs
description: 本文介绍如何创建实验室帐户、查看所有实验室帐户或在 Azure 实验室服务中删除实验室帐户。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360995"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 实验室服务中配置实验室帐户 
在 Azure 实验室服务中，实验室帐户是托管实验室类型的容器，例如教室实验室。 管理员可以设置一个具有 Azure 实验室服务的实验室帐户，并为能够在帐户中创建实验室的实验室所有者提供访问权限。 

本文介绍如何执行以下任务： 

- 在实验室中为 Vm 指定地址范围
- 断开连接时配置 Vm 自动关闭

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>在实验室中为 Vm 指定地址范围
以下过程包含在实验室中为 Vm 指定地址范围的步骤。 如果更新之前指定的范围，则修改后的地址范围仅适用于在进行更改后创建的 Vm。 

在指定应记住的地址范围时，有一些限制。 

- 前缀必须小于或等于23。 
- 如果虚拟网络对等互连到实验室帐户，则提供的地址范围不能与对等互连虚拟网络中的地址范围重叠。

1. 在 "**实验室帐户**" 页的左侧菜单中，选择 "**实验室设置**"。
2. 为 "**地址范围**" 字段指定将在实验室中创建的 vm 的地址范围。 地址范围应为无类域间路由（CIDR）表示法（例如： 10.20.0.0/23）。 将在此地址范围内创建实验室中的虚拟机。
3. 在工具栏上选择“保存”。 

    ![配置地址范围](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>断开连接时自动关闭 Vm
断开远程桌面连接后，可以启用或禁用 Windows 实验室 Vm （模板或学生）的自动关闭。 你还可以指定 Vm 在自动关机之前应等待用户重新连接的时间。

![实验室帐户中的自动关闭设置](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此设置适用于在实验室帐户中创建的所有实验室。 实验室所有者可以在实验室级别覆盖此设置。 在实验室帐户中对此设置的更改将只影响在更改后创建的实验室。

若要了解实验室所有者如何在实验室级别配置此设置，请参阅[此文](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许 lab creator 选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将实验室的网络连接到对等虚拟网络](how-to-connect-peer-virtual-network.md)
- [将共享映像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
