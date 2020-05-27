---
title: 在 Azure 实验室服务中配置 VM 的自动关闭
description: 本文介绍如何在实验室帐户中配置 VM 的自动关闭。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588201"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>为实验室帐户配置“断开连接时自动关闭 VM”设置
可以启用或禁用“断开远程桌面连接后自动关闭 Windows 实验室 VM (模板 VM 或学生 VM)”。 还可以指定实验室服务在自动关闭之前应等待用户重新连接多长时间。

## <a name="enable-automatic-shutdown"></a>启用自动关闭

1. 在“实验室帐户”页上，选择左侧菜单中的“实验室设置”。
2. 选择“用户断开连接时自动关闭虚拟机”选项。
3. 指定实验室服务在自动关闭 VM 之前应等待用户重新连接多长时间。

    ![实验室帐户中的自动关闭设置](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    此设置适用于在实验室帐户中创建的所有实验室。 实验室创建者（教师）可以在实验室级别重写此设置。 在实验室帐户中更改此设置只会影响更改后创建的实验室。

    若要禁用此设置，请在此页上取消选中“用户断开连接时自动关闭虚拟机”选项所对应的复选框。 

## <a name="next-steps"></a>后续步骤
若要了解实验室所有者如何在实验室级别配置或重写此设置，请参阅[此文](how-to-enable-shutdown-disconnect.md)
