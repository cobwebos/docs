---
title: 启用在断开 Azure 实验室服务时关闭 VM | Microsoft Docs
description: 了解如何启用或禁用在断开远程桌面连接时自动关闭 VM。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445706"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>启用在断开连接时自动关闭 VM
本文介绍如何启用或禁用“断开远程桌面连接后自动关闭 Windows 10 实验室 VM (模板 VM 或学生 VM)”。 还可以指定 VM 在自动关闭之前应等待用户重新连接的时间。

实验室帐户管理员可以为你在其中创建实验室的实验室帐户配置此设置。 有关详细信息，请参阅[为实验室帐户配置“断开连接时自动关闭 VM”](how-to-configure-lab-accounts.md)。 作为实验室所有者，你可以在创建实验室时或在创建实验室后重写该设置。 

## <a name="configure-when-creating-a-lab"></a>在创建实验室时配置
在“实验室创建向导”的“步骤 3”页上，可以启用或禁用此功能，还可以指定 VM 在自动关闭之前应等待用户重新连接的时间。 

![在创建实验室时配置](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>在创建实验室后配置
可以在“设置”页上配置此设置，如下图所示： 

![在创建实验室后配置](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> 如果你在断开与 VM 的 RDP 会话连接之前，在 VM 上关闭 Windows 操作系统 (OS)，自动关闭功能将无法正常工作。  

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [用于课堂实验室的仪表板](use-dashboard.md)