---
title: 撤消 Azure 数据共享中的共享订阅
description: 了解如何使用 Azure 数据共享从收件人撤消共享订阅。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476384"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>如何在 Azure 数据共享中撤消使用者的共享订阅

本文介绍如何使用 Azure 数据共享从一个或多个使用者撤消共享订阅。 这可以防止使用者触发任何更多的快照。 如果使用者尚未触发快照，则一旦取消共享订阅，他们将不会接收数据。 如果他们以前触发了快照，则他们拥有的最新数据将保留在他们的帐户中。

## <a name="navigate-to-a-sent-data-share"></a>导航到已发送的数据共享

在 Azure 数据共享中，导航到已发送的共享并选择"**共享订阅**"选项卡。

![撤销共享订阅](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

选中要删除其共享订阅的收件人旁边的复选框，然后单击"**撤消**"。 使用者将不再获取其数据的更新。

## <a name="next-steps"></a>后续步骤
详细了解如何[监视数据共享](how-to-monitor.md)。