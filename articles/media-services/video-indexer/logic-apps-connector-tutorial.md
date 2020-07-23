---
title: 包含逻辑应用和 Power Automate 的视频索引器连接器教程。
description: 本教程介绍如何使用包含逻辑应用和 Power Automate 的视频索引器连接器来解锁新的体验并创造盈利机会。
author: anikaz
manager: johndeu
ms.author: anzaman
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 932f52aa694c13fd3696d82872135304a4e41bdc
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801121"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>教程：使用包含逻辑应用和 Power Automate 的视频索引器

Azure 媒体服务[视频索引器 v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) 支持服务器到服务器的通信以及客户端到服务器的通信，使视频索引器用户能够轻松将视频和音频见解集成到其应用程序逻辑，从而解锁新的体验并创造盈利机会。

为了进一步简化集成，我们支持与我们的 API 兼容的 [逻辑应用](https://azure.microsoft.com/services/logic-apps/) 和  [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) 。 你可以使用连接器来设置自定义工作流，以便有效地为大量视频和音频文件编制索引并从中提取见解，无需编写任何代码。 此外，为你的集成使用连接器可以更好地洞察工作流的运行状况，并轻松对其进行调试。  

为了帮助你快速开始使用视频索引器连接器，我们将演练一个你可以设置的示例逻辑应用和 Power Automate 解决方案。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 自动上传视频并为其编制索引
> * 设置文件上传流
> * 设置 JSON 提取流

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要开始，还需要有一个视频索引器帐户，并能够通过 API 密钥访问 API。 

此外，需要一个 Azure 存储帐户。 请记下存储帐户的访问密钥。 创建两个容器 – 一个用于存储视频，另一个用于存储视频索引器生成的见解。  

接下来，需要在逻辑应用或 Power Automate（具体取决于使用两者中的哪一个）上打开两个单独的流。  

## <a name="upload-and-index-your-video-automatically"></a>自动上传视频并为其编制索引 

此方案包括一起工作的两个不同流。 在 Azure 存储帐户中添加或修改 Blob 时，会触发第一个流。 该流使用回调 URL 将新文件上传到视频索引器，以便在索引操作完成后发送通知。 第二个流基于回调 URL 触发，将提取的见解保存回到 Azure 存储中的 JSON 文件。 这两种流方法用于有效支持较大文件的异步上传和索引编制。 

### <a name="set-up-the-file-upload-flow"></a>设置文件上传流 

每当在 Azure 存储容器中添加 Blob 时，就会触发第一个流。 触发后，该流将创建一个 SAS URI，你可以使用该 URI 将视频上传到视频索引器中并在视频索引器中为视频编制索引。 首先创建以下流。 

![文件上传流](./media/logic-apps-connector-tutorial/file-upload-flow.png)

若要设置第一个流，需要提供视频索引器 API 密钥和 Azure 存储凭据。 

![Azure Blob 存储](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![连接名称和 API 密钥](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

可以连接到 Azure 存储和视频索引器帐户后，转到“添加或修改 Blob 时”触发器，并选择要将视频文件放置到的容器。 

![容器](./media/logic-apps-connector-tutorial/container.png)

接下来，转到“按路径创建 SAS URI”操作，并从“动态内容”选项中选择“文件路径列表”。  

![按路径创建 SAS URI](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

填写[帐户位置和 ID](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location) 以获取视频索引器帐户令牌。

![获取帐户访问令牌](./media/logic-apps-connector-tutorial/account-access-token.png)

对于“上传视频和索引”，请填写所需的参数和视频 URL。 选择“添加新参数”，然后选择“回调 URL”。 

![上传并编制索引](./media/logic-apps-connector-tutorial/upload-and-index.png)

暂时将回调 URL 留空。 只有在完成用来创建回调 URL 的第二个流后，才能添加回调 URL。 

对于其他参数，可以使用默认值，或者根据需要进行设置。 

单击“保存”并继续配置第二个流，以便在完成上传和索引编制后提取见解。 

## <a name="set-up-the-json-extraction-flow"></a>设置 JSON 提取流 

在第一个流中完成上传和索引编制后，会发送包含正确回调 URL 的 HTTP 请求来触发第二个流。 然后，第二个流会检索视频索引器生成的见解。 在此示例中，该流将索引编制作业的输出存储在 Azure 存储中。  但是，你可以自行决定如何处理输出。  

创建与第一个流不同的第二个流。 

![JSON 提取流](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

若要设置此流，需要再次提供视频索引器 API 密钥和 Azure 存储凭据。 需要更新在设置第一个流时所更新的相同参数。 

对于触发器，你会看到“HTTP POST URL”字段。 只有在保存流之后才会生成该 URL；但是，最后才需要使用该 URL。 稍后我们将返回到此字段。 

填写[帐户位置和 ID](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location) 以获取视频索引器帐户令牌。  

转到“获取视频索引”操作并填写所需的参数。 对于“视频 ID”，请输入以下表达式：triggerOutputs()['queries']['id'] 

![视频索引器操作信息](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

此表达式告知连接器要从触发器的输出获取视频 ID。 在本例中，触发器的输出是第一个触发器中的“上传视频和索引”的输出。 

转到“创建 Blob”操作，选择要将见解保存到的文件夹的路径。 设置所要创建的 Blob 的名称。 对于“Blob 内容”，请输入以下表达式：body(‘Get_Video_Index’) 

![“创建 Blob”操作](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

此表达式提取此流中的“获取视频索引”操作的输出。 

单击“保存流”。 

保存流后，将在触发器中创建 HTTP POST URL。 从触发器中复制 URL。 

![保存 URL 触发器](./media/logic-apps-connector-tutorial/save-url-trigger.png)

现在，返回到第一个流，并在“上传视频和索引”操作中粘贴该 URL 作为“回调 URL”参数。 

确保保存这两个流，此时已准备就绪！ 

通过将视频添加到 Azure Blob 容器来尝试运行新建的逻辑应用或 Power Automate 解决方案，几分钟后返回以查看目标文件夹中是否显示了见解。 

## <a name="clean-up-resources"></a>清理资源

完成本教程后，如果需要，始终可将此逻辑应用或 Power Automate 解决方案保持运行状态。 但是，如果你不希望将此解决方案保持运行状态且不希望产生费用，在使用 Power Automate 的情况下请关闭这两个流。 如果使用的是逻辑应用，请禁用这两个流。 

## <a name="next-steps"></a>后续步骤

本教程只是演示了一个视频索引器连接器示例。 对于视频索引器提供的任何 API 调用，都可以使用视频索引器连接器。 例如：上传和检索见解、转换结果、获取可嵌入的小组件，甚至自定义模型。 此外，还可以选择根据不同的源（例如，对文件存储库的更新或发送的电子邮件）触发这些操作。 然后，你可以选择获取相关基础结构或应用程序的更新结果，或生成任意数量的操作项。  

> [!div class="nextstepaction"]
> [使用视频索引器 API](video-indexer-use-apis.md)
