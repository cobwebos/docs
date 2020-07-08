---
title: 在 Azure 事件网格中创建、查看和管理系统主题（门户）
description: 本文介绍如何使用 Azure 门户查看现有系统主题如何创建 Azure 事件网格系统主题。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 23b87812492485fc2336b8d2d35bf0426ede4772
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84457229"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>创建、查看和管理 Azure 门户中的事件网格系统主题
本文说明如何使用 Azure 门户创建和管理系统主题。 有关系统主题的概述，请参阅[系统主题](system-topics.md)。

## <a name="create-a-system-topic"></a>创建系统主题
可以通过两种方式为 Azure 资源（存储帐户、事件中心命名空间等）创建系统主题：

- 使用资源（例如，存储帐户或事件中心命名空间）的 "**事件**" 页。 使用 Azure 门户中的 "**事件**" 页为 azure 源（例如 azure 存储帐户）引发的事件创建事件订阅时，门户会为 azure 资源创建系统主题，然后创建系统主题的订阅。 如果是第一次在 Azure 资源上创建事件订阅，则可以指定系统主题的名称。 从第二次开始，系统主题名称将在只读模式下显示。 有关详细步骤，请参阅[快速入门：将 Blob 存储事件路由到 web 终结点，并提供 Azure 门户](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)。
- 使用 "**事件网格系统主题**" 页。 以下步骤介绍如何使用**事件网格系统主题**页创建系统主题。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部的 "搜索" 框中，键入**事件网格系统主题**，然后按**enter**。 

    ![搜索系统主题](./media/create-view-manage-system-topics/search-system-topics.png)
3. 在 "**事件网格系统主题**" 页上，选择工具栏上的 " **+ 添加**"。

    ![添加系统主题-工具栏按钮](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. 在 "**创建事件网格系统" 主题**页上，执行以下步骤：
    1. 选择**主题类型**。 在以下示例中，已选择 "**存储帐户**" 选项。 
    2. 选择包含存储帐户资源的**Azure 订阅**。 
    3. 选择包含存储帐户的**资源组**。 
    4. 选择 **“存储帐户”**。 
    5. 输入要创建的系统主题的**名称**。 
    
        > [!NOTE]
        > 您可以使用此系统主题名称搜索指标和诊断日志。
    6. 选择“查看 + 创建”。

        ![创建系统主题](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. 查看设置，然后选择 "**创建**"。 
        
        ![查看并创建系统主题](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. 部署成功后，选择 "**中转到资源**"，以查看您创建的系统主题的 "**事件网格系统" 主题**页。 

        ![系统主题页](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>查看所有系统主题
按照以下步骤查看所有现有的事件网格系统主题。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部的 "搜索" 框中，键入**事件网格系统主题**，然后按**enter**。 

    ![搜索系统主题](./media/create-view-manage-system-topics/search-system-topics.png)
3. 在 "**事件网格系统主题**" 页上，可以看到所有系统主题。 

    ![系统主题列表](./media/create-view-manage-system-topics/list-system-topics.png)
4. 从列表中选择一个**系统主题**以查看其详细信息。 

    ![系统主题详细信息](./media/create-view-manage-system-topics/system-topic-details.png)

    此页显示有关系统主题的详细信息，如以下信息： 
    - 源。 在其上创建系统主题的资源的名称。
    - 源类型。 资源的类型。 例如： `Microsoft.Storage.StorageAccounts` 、 `Microsoft.EventHub.Namespaces` 等 `Microsoft.Resources.ResourceGroups` 。
    - 为系统主题创建的任何订阅。

    此页允许如下操作：
    - 创建事件订阅在工具栏上选择 " **+ 事件订阅**"。 
    - 删除事件订阅。 在工具栏上选择 "**删除**"。 
    - 为系统主题添加标记。 选择左侧菜单中的 "**标记**"，并指定标记名称和值。 


## <a name="delete-a-system-topic"></a>删除系统主题
1. 按照[查看系统主题](#view-all-system-topics)部分的说明查看所有系统主题，并从列表中选择要删除的系统主题。 
2. 在 "**事件网格系统主题**" 页上，选择工具栏上的 "**删除**"。 

    ![系统主题-"删除" 按钮](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. 在 "确认" 页上，选择 **"确定"** 以确认删除。 它还删除系统主题以及系统主题的所有事件订阅。  

## <a name="create-an-event-subscription"></a>创建事件订阅
1. 按照[查看系统主题](#view-all-system-topics)部分的说明查看所有系统主题，并从列表中选择要删除的系统主题。 
2. 在 "**事件网格系统主题**" 页上，从工具栏中选择 " **+ 事件订阅**"。 

    ![系统主题-"添加事件订阅" 按钮](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. 确认已自动填充**主题类型**、**源资源**和**主题名称**。 输入一个名称，选择一个**终结点类型**，并指定**终结点**。 然后，选择 "**创建**" 创建事件订阅。 

    ![系统主题-创建事件订阅](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>后续步骤
请参阅[Azure 事件网格部分中的系统主题](system-topics.md)，详细了解 Azure 事件网格支持的系统主题和主题类型。 
