---
title: 如何监视 Azure 数据共享
description: 了解如何在 Azure 数据共享中监视邀请状态、共享订阅和快照历史记录
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490515"
---
# <a name="monitor-azure-data-share"></a>监视 Azure Data Share  

本文介绍如何使用 Azure 数据共享来监视数据共享。 作为数据提供程序，您可以监视数据共享关系的各个方面。 详细信息，例如，数据使用者是否接受了你对数据共享的邀请，以及他们是否已创建共享订阅并开始使用你的数据，以供监视。 

作为数据使用者，你可以监视已触发到你的 Azure 订阅中的快照。 

## <a name="monitor-invitation-status"></a>监视邀请状态

通过导航到 "已发送共享-> 邀请" 查看数据共享邀请的状态。 

![邀请状态](./media/invitation-status.png "邀请状态") 

你的邀请可在以下三种状态：

* 挂起-数据共享接收方尚未接受邀请。
* 接受-数据共享接收方已接受邀请。
* 已拒绝-数据共享收件人已拒绝邀请。

> [!IMPORTANT]
> 如果在已接受邀请后将其删除，则该邀请不等同于撤消访问权限。 如果你想要阻止未来的快照复制到你的数据使用者存储帐户，则必须通过 "*共享订阅*" 选项卡撤销访问权限。 

## <a name="monitor-share-subscriptions"></a>监视共享订阅

通过导航到 "已发送共享-> 共享订阅" 来查看共享订阅的状态。 这会在接受邀请后，为你的数据使用者创建有关活动订阅的详细信息。 您可以通过选择 "共享订阅" 并选择 "*撤消*" 来停止对数据使用者的将来更新。 

## <a name="snapshot-history"></a>快照历史记录 

在 "历史记录" 选项卡中，可以查看已复制到数据使用者的租户的快照。 可以监视每个快照间隔的频率和持续时间。 

![快照历史记录](./media/sent-shares.png "快照历史记录") 

可以通过单击 "运行" 开始日期来查看有关每个快照运行的详细信息。 

默认显示最多30天的快照历史记录。 如果需要查看超过30天的历史记录，请导航到 "监视->" "诊断设置"，然后选择 "**添加诊断设置**"。 你将需要选择存储帐户来存储这些日志。 

![快照历史记录](./media/diagnostic-settings.png "诊断设置") 

## <a name="next-steps"></a>后续步骤 

详细了解[Azure 数据共享术语](terminology.md)