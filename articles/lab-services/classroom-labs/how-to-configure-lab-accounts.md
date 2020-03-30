---
title: 在 Azure 实验室服务中配置实验室帐户 |微软文档
description: 本文介绍如何在 Azure 实验室服务中创建实验室帐户、查看所有实验室帐户或删除实验室帐户。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284299"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 实验室服务中配置实验室帐户 
在 Azure 实验室服务中，实验室帐户是托管实验室类型的容器，如课堂实验室。 管理员可以设置一个具有 Azure 实验室服务的实验室帐户，并为能够在帐户中创建实验室的实验室所有者提供访问权限。 

本文演示如何执行以下任务： 

- 在实验室中为 VM 指定地址范围
- 在断开连接时配置 VM 的自动关机

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>在实验室中为 VM 指定地址范围
以下步骤具有为实验室中的 VM 指定地址范围的步骤。 如果更新以前指定的范围，则修改后的地址范围仅适用于进行更改后创建的 VM。 

以下是指定地址范围时应牢记的一些限制。 

- 前缀必须小于或等于 23。 
- 如果将虚拟网络与实验室帐户对等，则提供的地址范围无法与来自对等虚拟网络的地址范围重叠。

1. 在 **"实验室帐户"** 页上，选择左侧菜单上的 **"实验室"设置**。
2. 对于"**地址范围**"字段，指定将在实验室中创建的 VM 的地址范围。 地址范围应位于无类域间路由 （CIDR） 表示法中（例如：10.20.0.0/23）。 将在此地址范围内创建实验室中的虚拟机。
3. 在工具栏上选择“保存”。**** 

    ![配置地址范围](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>断开时自动关闭 VM
在远程桌面连接断开连接后，您可以启用或禁用 Windows 实验室 VM（模板或学生）的自动关闭。 您还可以指定 VM 应等待用户重新连接的时间，然后再自动关闭。

![实验室帐户的自动关机设置](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此设置适用于实验室帐户中创建的所有实验室。 实验室所有者可以在实验室级别覆盖此设置。 对实验室帐户中的此设置的更改将仅影响进行更改后创建的实验室。

要了解实验室所有者如何在实验室级别配置此设置，请参阅[本文](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将实验室网络与对等虚拟网络连接](how-to-connect-peer-virtual-network.md)
- [将共享图像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [将用户添加为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
