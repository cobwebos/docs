---
title: 连接到 Azure Blob 存储 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Azure 逻辑应用在 Azure 存储中创建和管理 Blob
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 49d08135dee4568d1a9d65ec2d22d17ee3bda2ea
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294673"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>使用 Azure 逻辑应用在 Azure Blob 存储中创建和管理 Blob

本文介绍如何使用 Azure Blob 存储连接器通过逻辑应用在 Azure 存储帐户中访问和管理作为 Blob 存储的文件。 可以通过这种方式创建逻辑应用，以便自动完成进行文件管理所需的任务和工作流。 例如，可以生成用于在存储帐户中创建、获取、更新和删除文件的逻辑应用。

假定你有一个在 Azure 网站上进行更新的工具。 它充当逻辑应用的触发器。 当此事件发生时，可以让逻辑应用更新 Blob 存储容器中的某些文件，这是逻辑应用中的一项操作。 

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。
如需特定于连接器的技术信息，请参阅 <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">Azure Blob 存储连接器参考</a>。

## <a name="prerequisites"></a>先决条件

* [Azure 存储帐户和存储容器](../storage/blobs/storage-quickstart-blobs-portal.md)

* 需在其中访问 Azure Blob 存储帐户的逻辑应用。 若要通过 Azure Blob 存储触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>添加 Blob 存储触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

此示例演示了如何在存储容器中添加或更新 Blob 的属性时，使用“Azure Blob 存储 - 添加或修改 Blob 时(仅属性)”触发器来启动逻辑应用工作流。 

1. 在 Azure 门户或 Visual Studio 中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 此示例使用 Azure 门户。

2. 在搜索框中，输入“azure blob”作为筛选器。 在触发器列表中，选择所需的触发器。

   此示例使用以下触发器：**Azure Blob 存储 - 添加或修改 Blob 时(仅属性)**

   ![选择触发器](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. 如果系统提示输入连接详细信息，请[立即创建 Blob 存储连接](#create-connection)。 或者，如果连接已存在，请提供触发器所需的信息。

   对于此示例，请选择要监视的容器和文件夹。

   1. 在“容器”框中，选择文件夹图标。

   2. 在文件夹列表中选择右尖括号 ( **>** )，然后以浏览方式查找并选择所需的文件夹。 

      ![选择文件夹](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 选择你希望触发器以多大时间间隔和频率来检查文件夹中的更改。

4. 完成后，请在设计器工具栏上选择“保存”。

5. 现在请继续向逻辑应用添加一个或多个操作，以便完成需对触发器结果执行的任务。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>添加 Blob 存储操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 就此示例来说，逻辑应用一开始使用[定期触发器](../connectors/connectors-native-recurrence.md)。

1. 在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 此示例使用 Azure 门户。

2. 在逻辑应用设计器的触发器或操作下，选择“新建步骤” > “添加操作”。

   ![添加操作](./media/connectors-create-api-azureblobstorage/add-action.png) 

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

3. 在搜索框中，输入“azure blob”作为筛选器。 从操作列表中选择所需的操作。

   此示例使用此操作：**Azure Blob 存储 - 获取 Blob 内容**

   ![选择操作](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. 如果系统提示输入连接详细信息，请[立即创建 Azure Blob 存储连接](#create-connection)。 或者，如果连接已存在，请提供操作所需的信息。 

   对于此示例，请选择所需的文件。

   1. 从“Blob”框中选择文件夹图标。
  
      ![选择文件夹](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. 根据 Blob 的 **ID** 号查找并选择所需的文件。 可以在 Blob 的元数据中找到此 **ID** 号，该元数据是由前述 Blob 存储触发器返回的。

5. 完成后，请在设计器工具栏上选择“保存”。
若要测试逻辑应用，请确保所选文件夹包含一个 Blob。

此示例仅获取 Blob 的内容。 若要查看这些内容，请使用另一连接器添加另一操作，以便创建包含此 Blob 的文件。 例如，添加一个 OneDrive 操作，以便根据 Blob 内容创建文件。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>连接到存储帐户

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/azureblobconnector/)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
