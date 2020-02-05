---
title: Azure 堡垒会话监视和管理 |Microsoft Docs
description: 本文介绍如何选择正在进行的会话，并强制断开连接或将其删除。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76981083"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 堡垒的会话监视和管理

在虚拟网络中预配并部署堡垒服务后，可以使用它无缝连接到此虚拟网络中的任何 VM。 当用户连接到工作负荷时，可以使用 Azure 堡垒监视远程会话并执行快速管理操作。 Azure 堡垒会话监视使你可以查看哪些用户连接到哪些 Vm。 它显示用户从其连接的 IP、连接的时间以及连接时间。 利用会话管理体验，你可以选择正在进行的会话，并强制断开连接或删除会话，以便断开用户与正在进行的会话的连接。

## <a name="monitor"></a>监视远程会话

1. 在[Azure 门户](https://portal.azure.com)中，导航到 azure 堡垒资源并从 azure 堡垒页面中选择 "**会话**"。

   ![会话](./media/session-monitoring/sessions.png)
2. 在 "**会话**" 页上，可以在右侧看到正在进行的远程会话。

   ![查看会话](./media/session-monitoring/view-session.png)
3. 选择 "**刷新**" 以查看更新后的远程会话列表。 选择 "刷新" 时，Azure 堡垒将提取最新的监视信息并在门户中刷新它。

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 为从网关管理器到会话监视的入站流量启用端口4443以进行工作。
>

## <a name="view"></a>删除或强制断开正在进行的远程会话

你可以选择一组会话并强制断开连接。 以下步骤说明如何删除远程会话：

1. 导航到 Azure 堡垒资源，并从 Azure 堡垒页面中选择 "**会话**"。

   ![引导](./media/session-monitoring/navigate.png)
2. 选择会话后，将看到远程会话的列表。

   ![列出会话](./media/session-monitoring/list.png)
3. 选择特定的远程会话，然后选择 "会话" 行右端的三个省略号，然后选择 "**删除**"。

   ![delete](./media/session-monitoring/delete.png)
4. 选择 "删除" 时，远程会话将断开连接，并且用户将在远程会话中显示 "已断开连接" 消息。

   ![取消](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。