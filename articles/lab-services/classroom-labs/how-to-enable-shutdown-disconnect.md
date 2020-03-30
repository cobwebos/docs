---
title: 在断开连接 Azure 实验室服务时启用 VM 的关闭 |微软文档
description: 了解如何在远程桌面连接断开连接时启用或禁用 VM 的自动关闭。
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117126"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>在断开连接时自动关闭 VM
本文介绍如何在远程桌面连接断开连接后启用或禁用**Windows 10**实验室 VM（模板或学生）的自动关闭。 您还可以指定 VM 应等待用户重新连接的时间，然后再自动关闭。

实验室帐户管理员可以为创建实验室的实验室帐户配置此设置。 有关详细信息，请参阅[为实验室帐户在断开连接时配置 VM 的自动关闭](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)。 作为实验室所有者，您可以在创建实验室时或在创建实验室后覆盖该设置。 

## <a name="configure-when-creating-a-lab"></a>创建实验室时配置
在实验室创建向导的步骤 3 页上，您可以启用或禁用此功能，并指定 VM 应等待用户重新连接的时间，然后再自动关闭。 

![在创建实验室时进行配置](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>创建实验室后配置
您可以在 **"设置"** 页上配置此设置，如下图所示： 

![创建实验室后配置](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> 如果在将 RDP 会话断开为 VM 之前关闭 VM 上的 Windows 操作系统 （OS），则自动关机功能将无法正常工作。  

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [教室实验室仪表板](use-dashboard.md)