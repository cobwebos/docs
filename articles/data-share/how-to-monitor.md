---
title: 如何监视 Azure 数据共享
description: 了解如何监视 Azure 数据共享中的邀请状态、共享订阅和快照历史记录
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490515"
---
# <a name="monitor-azure-data-share"></a>监视 Azure Data Share  

本文介绍如何使用 Azure 数据共享监视数据共享。 作为数据提供商，您可以监视数据共享关系的各个方面。 数据使用者是否接受数据共享邀请，以及他们是否创建了共享订阅并开始使用您的数据等详细信息均可进行监控。 

作为数据使用者，可以监视已触发到 Azure 订阅中的快照。 

## <a name="monitor-invitation-status"></a>监视邀请状态

通过导航到已发送共享 -> 邀请，查看数据共享邀请的状态。 

![邀请状态](./media/invitation-status.png "邀请状态") 

您的邀请可以处于三种状态：

* 挂起 - 数据共享收件人尚未接受邀请。
* 已接受 - 数据共享收件人已接受邀请。
* 已拒绝 - 数据共享收件人已拒绝邀请。

> [!IMPORTANT]
> 如果在邀请已被接受后删除邀请，则不等于吊销访问权限。 如果要阻止将将来的快照复制到数据使用者存储帐户，则必须通过 *"共享订阅"* 选项卡撤消访问权限。 

## <a name="monitor-share-subscriptions"></a>监视共享订阅

通过导航到已发送共享 ->共享订阅，查看共享订阅的状态。 这将提供有关数据使用者在接受邀请后创建的活动订阅的详细信息。 您可以通过选择共享订阅并选择 *"撤销"* 来阻止对数据使用者的未来更新。 

## <a name="snapshot-history"></a>快照历史记录 

在历史记录选项卡中，您可以查看已复制到数据使用者租户的快照。 您可以监视每个快照间隔的频率和持续时间。 

![快照历史记录](./media/sent-shares.png "快照历史记录") 

您可以通过单击运行开始日期查看有关每个快照运行的更多详细信息。 

默认情况下，最多显示 30 天的快照历史记录。 如果您需要查看超过 30 天的历史，请导航到"监视 ->诊断设置"，然后选择"**添加诊断设置**"。 您需要选择存储帐户才能将这些日志存储到其中。 

![快照历史记录](./media/diagnostic-settings.png "诊断设置") 

## <a name="next-steps"></a>后续步骤 

了解有关[Azure 数据共享术语的更多](terminology.md)