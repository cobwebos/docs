---
title: 更改通知中心命名空间的定价层 | Microsoft Docs
description: 了解如何更改 Azure 通知中心命名空间的定价层。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: 99ea21b3eb01a674a89c70a1b923f02e600cc3c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60234476"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>更改 Azure 通知中心命名空间的定价层
通知中心分三个层提供：免费、基本和标准。    本文介绍如何更改 Azure 通知中心命名空间的定价层。 

## <a name="overview"></a>概述
在 Azure 通知中心，**中心**是最小的资源/实体。 它通常映射到一个应用程序，可以为每个支持用于应用的平台通知系统保留一个证书。 该应用程序可以是一个混合的应用程序，也可以是一个本机的跨平台应用程序。

**命名空间**是通知中心的集合。 每个命名空间通常包含多个中心，这些中心是相关的，用于特定目的。 例如，可以有三个不同的命名空间，分别用于开发、测试和生产目的。 

可以在命名空间级别关联一个定价层。 通知中心支持三个层：免费、基本和标准。    可以将层用于符合要求的命名空间。 以下部分介绍如何更改通知中心命名空间的定价层。 

## <a name="use-azure-portal"></a>使用 Azure 门户 
使用 Azure 门户时，可以在命名空间页或中心页上更改命名空间的定价层。  在中心页上更改它时，实际上是在命名空间级别更改它。 它更改命名空间以及命名空间中的所有中心的定价层。 

### <a name="change-tier-on-the-namespace-page"></a>在命名空间页上更改层
以下过程提供的步骤用于在命名空间页上更改命名空间的定价层。 更改命名空间的层时，所做的更改会应用到命名空间的所有中心。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”  。 
3. 在“物联网”部分选择“通知中心命名空间”。   如果选择文本旁边的星号 (`*`)，则会将其添加到“收藏夹”下的左侧导航栏。  这样一来，从下次开始就可以更快地访问命名空间页。 将其添加到“收藏夹”后，选择“通知中心命名空间”。  

    ![所有服务 -> 通知中心命名空间](./media/change-pricing-tier/all-services-nhub.png)
1. 在“通知中心命名空间”页上，  选择要更改定价层的命名空间。 
2. 在命名空间的“通知中心命名空间”页上，  可以在“基本信息”部分看到命名空间的当前定价层。  在下图中，可以看到命名空间的定价层为“免费”。  

    ![命名空间页上的当前定价层](./media/change-pricing-tier/pricing-tier-before.png)
1. 在命名空间的“通知中心命名空间”页上，  在“管理”部分选择“定价层”。   

    ![在命名空间页上选择定价层](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. 更改定价层，然后单击“选择”按钮  。    
7. 可以在“警报”中看到层更改操作的状态。  
8. 切换到“概览”页。  确认在“基本信息”部分可以看到“定价层”字段的新层。       
1. 此步骤是可选的。 选择命名空间中的任何中心。 确认可以在“基本信息”部分看到同一定价层。  应该在命名空间中看到所有中心有一个相同的定价层。 

### <a name="change-tier-on-the-hub-page"></a>在中心页上更改层
以下过程提供的步骤用于在中心页上更改命名空间的定价层。 即使是从中心页开始执行这些步骤，但实际上也会更改命名空间以及命名空间中的所有中心的定价层。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”  。
3. 在“物联网”部分选择“通知中心”。   
4. 选择通知**中心**。 
5. 在左菜单上选择“定价层”。  
6. 更改定价层，然后单击“选择”按钮  。 此操作更改中心所在命名空间的定价层设置。 因此，会在命名空间页以及所有中心页上看到新的定价层。 

## <a name="use-rest-api"></a>使用 REST API
可以使用以下资源提供程序 REST API 获取当前定价层并将其更新。 

### <a name="get-current-pricing-tier-for-a-namespace"></a>获取命名空间的当前定价层
若要获取**当前命名空间层**，请发送 GET 命令，如以下示例所示： 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>更新命名空间的定价层
若要**更新命名空间层**，请发送 PUT 命令，如以下示例所示： 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>后续步骤
有关这些层和定价的详细信息，请参阅[通知中心定价](https://azure.microsoft.com/pricing/details/notification-hubs/)。
