---
title: Azure 堡垒会话监视和管理 |微软文档
description: 在本文中，了解如何选择正在进行的会话并强制断开连接或删除它。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619196"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 堡垒的会话监视和管理

在虚拟网络中预配和部署 Bastion 服务后，可以使用该服务无缝连接到此虚拟网络中的任何 VM。 当用户连接到工作负荷时，Azure 堡垒可用于监视远程会话并采取快速管理操作。 Azure 堡垒会话监视允许您查看哪些用户连接到哪些 VM。 它显示用户连接的 IP、连接时间以及他们连接的时间。 会话管理经验允许您选择正在进行的会话，并强制断开连接或删除会话，以便断开用户与正在进行的会话的连接。

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>监视远程会话

1. 在[Azure 门户](https://portal.azure.com)中，导航到 Azure 堡垒资源，然后从 Azure 堡垒页中选择 **"会话**"。

   ![会话](./media/session-monitoring/sessions.png)
2. 在 **"会话"** 页上，您可以看到右侧正在进行的远程会话。

   ![查看会话](./media/session-monitoring/view-session.png)
3. 选择 **"刷新"** 以查看更新的远程会话列表。 选择"刷新"时，Azure 堡垒将获取最新的监视信息并在门户中刷新信息。

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 为网关管理器的入站流量启用端口 4443，以便会话监视正常工作。
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>删除或强制断开正在进行的远程会话

您可以选择一组会话并强制断开它们。 以下步骤演示如何删除远程会话：

1. 导航到 Azure 堡垒资源，并从 Azure 堡垒页面中选择 **"会话**"。

   ![navigate](./media/session-monitoring/navigate.png)
2. 选择"会话"后，您将看到远程会话的列表。

   ![列表会话](./media/session-monitoring/list.png)
3. 选择特定的远程会话，然后选择会话行右侧末尾的三个椭圆，然后选择 **"删除**"。

   ![delete](./media/session-monitoring/delete.png)
4. 选择"删除"时，远程会话将断开连接，并且用户将在远程会话中显示"您已断开连接"消息。

   ![断开连接](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。