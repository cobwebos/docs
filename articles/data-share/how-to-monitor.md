---
title: 如何监视 Azure 数据共享预览
description: 如何监视 Azure 数据共享预览
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789093"
---
# <a name="monitor-azure-data-share-preview"></a>监视 Azure 数据共享预览 

本文介绍如何监视使用 Azure 数据共享预览你的数据共享。 为数据提供程序，你将能够监视你的数据共享的关系的各个方面。 详细信息，例如数据使用者是否已因为它们具有在创建共享订阅，在开始使用你的数据也接受你的邀请到该数据共享，都可用于监视。 

为数据使用者，可以监视已触发快照，到 Azure 订阅。 

## <a name="monitor-invitation-status"></a>监视邀请状态

通过导航到已发送共享你的数据共享邀请的状态视图-> 的邀请。 

![邀请状态](./media/invitation-status.png "邀请状态") 

有您的邀请可以采用的三种状态：

* 挂起的数据共享接收方未接受邀请。
* 已接受-数据共享接收方已接受邀请。
* 拒绝-数据共享接收方已拒绝邀请。

> [!IMPORTANT]
> 如果已接受之后删除邀请，，是不等效于撤消访问权限。 如果你想要停止从要复制到你的数据使用者的存储帐户将来的快照，你必须撤消访问权限通过*共享订阅*选项卡。 

## <a name="monitor-share-subscriptions"></a>监视器共享订阅

通过导航到发送共享共享订阅状态-> 共享的订阅视图。 这将提供有关在接受邀请后创建的数据使用者的活动订阅的详细信息。 可以通过选择共享订阅，选择向数据使用者停止将来的更新*撤消*。 

## <a name="snapshot-history"></a>快照历史记录 

在历史记录选项卡，您是能够查看在复制到数据使用者的租户的快照。 你将能够监视频率和持续的每个快照间隔时间。 

![快照历史记录](./media/sent-shares.png "快照历史记录") 

可以查看有关通过单击运行的开始日期运行每个快照的更多详细信息。 

## <a name="next-steps"></a>后续步骤 

详细了解[Azure 数据共享术语](terminology.md)