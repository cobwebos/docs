---
title: 更改通知中心命名空间的定价层 | Microsoft Docs
description: 了解如何更改 Azure 通知中心命名空间的定价层。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656466"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>更改 Azure 通知中心命名空间的定价层

通知中心分三个层提供：免费、基本和标准。    本文介绍如何更改 Azure 通知中心命名空间的定价层。

## <a name="overview"></a>概述

在 Azure 通知中心，中心是最小的资源/实体。 它通常映射到一个应用程序，并且可以为每个支持应用程序的平台通知系统（PNS）保存一个证书。 应用程序可以是混合应用程序，也可以是本机和跨平台应用程序。

**命名空间**是通知中心的集合。 每个命名空间通常包含多个中心，这些中心是相关的，用于特定目的。 例如，可以分别为开发、测试和生产目的使用三个不同的命名空间。

可以将命名空间与**免费**、**基本**或**标准**定价层关联。 可以将层用于符合要求的命名空间。 以下部分介绍如何更改通知中心命名空间的定价层。

## <a name="use-azure-portal"></a>使用 Azure 门户

使用 Azure 门户时，可以在 "命名空间" 页或中心页上更改命名空间的定价层。 在中心页上更改它时，实际上是在命名空间级别更改它。 它更改命名空间以及命名空间中的所有中心的定价层。

### <a name="change-tier-on-the-namespace-page"></a>在命名空间页上更改层

下面的过程演示如何在命名空间页上更改命名空间的定价层。 更改命名空间的层时，它将应用于该命名空间中的所有中心。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中选择 "**所有服务**"。
3. 在“物联网”部分选择“通知中心命名空间”。******** 如果选择文本旁的星号`*`（），则会将其添加到 **"收藏夹**" 下的左侧导航栏中。 它可帮助你更快地访问命名空间页面。 将其添加到 "收藏夹" 后，选择 "**通知中心命名空间**"。

    ![所有服务 -> 通知中心命名空间](./media/change-pricing-tier/all-services-nhub.png)

4. 在“通知中心命名空间”页上，**** 选择要更改定价层的命名空间。
5. 在命名空间的“通知中心命名空间”页上，**** 可以在“基本信息”部分看到命名空间的当前定价层。**** 在下图中，可以看到命名空间的定价层为“免费”。****

    ![命名空间页上的当前定价层](./media/change-pricing-tier/pricing-tier-before.png)

6. 在命名空间的“通知中心命名空间”页上，**** 在“管理”部分选择“定价层”。********

    ![在命名空间页上选择定价层](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. 更改定价层，然后单击 "**选择**" 按钮。
8. 你可以在**警报**中查看层更改操作的状态。
9. 切换到“概览”页。**** 确认在“基本信息”部分可以看到“定价层”字段的新层。********
10. 此步骤是可选的。 选择命名空间中的任何中心。 确认可以在“基本信息”部分看到同一定价层。**** 对于命名空间中的所有中心，你应看到相同的定价层。

### <a name="change-tier-on-the-hub-page"></a>在中心页上更改层

下面的过程演示如何在 "中心" 页上更改命名空间的定价层。 即使是从中心页开始执行这些步骤，但实际上也会更改命名空间以及命名空间中的所有中心的定价层。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中选择 "**所有服务**"。
3. 在“物联网”部分选择“通知中心”。********
4. 选择通知**中心**。
5. 在左菜单上选择“定价层”。****
6. 更改定价层，然后单击“选择”按钮****。 此操作更改中心所在命名空间的定价层设置。 因此，会在命名空间页以及所有中心页上看到新的定价层。

> [!NOTE]
> 所有定价层更改都将立即生效。

## <a name="use-rest-api"></a>使用 REST API

可以使用以下资源提供程序 REST API 获取当前定价层并将其更新。

### <a name="get-current-pricing-tier-for-a-namespace"></a>获取命名空间的当前定价层

若要获取当前命名空间层，请发送 GET 命令，如以下示例中所示：

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>更新命名空间的定价层

若要更新命名空间层，请发送 PUT 命令，如以下示例中所示：

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>后续步骤

有关这些层和定价的详细信息，请参阅[通知中心定价](https://azure.microsoft.com/pricing/details/notification-hubs/)。
