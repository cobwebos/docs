---
title: 在 Azure 数据共享预览中吊销共享订阅
description: 撤销共享订阅
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: dc14bb121d18179091aa5f6f2854b391e72632f1
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258632"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>如何在 Azure 数据共享预览中吊销使用者的共享订阅

本文介绍如何使用 Azure 数据共享预览从一个或多个使用者吊销共享订阅。 这会阻止使用者触发更多快照。 如果使用者尚未触发快照，则在吊销共享订阅后，它们永远不会收到数据。 如果它们先前已触发了快照，则他们拥有的最新数据将保留在其帐户中。

## <a name="navigate-to-a-sent-data-share"></a>导航到已发送的数据共享

在 Azure 数据共享预览中，导航到已发送的共享，然后选择 "**共享订阅**" 选项卡。

![撤消共享订阅](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

选中要删除其共享订阅的收件人旁边的复选框，然后单击 "**撤消**"。 使用者将不再获取对其数据的更新。

## <a name="next-steps"></a>后续步骤
了解有关如何[监视数据共享](how-to-monitor.md)的详细信息。