---
title: Azure 函数事件网格本地调试
description: 了解如何在本地调试事件网格事件触发的 Azure 函数
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 函数, 无服务器体系结构
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 96d88fafd6824ed85f1d91bab59374b3490a55b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60428180"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure 函数事件网格触发器本地调试

本文演示如何调试一个用于处理存储帐户所引发 Azure 事件网格事件的本地函数。 

## <a name="prerequisites"></a>必备组件

- 创建或使用现有的函数应用
- 创建或使用现有的存储帐户
- 下载 [ngrok](https://ngrok.com/)，使 Azure 能够调用本地函数

## <a name="create-a-new-function"></a>创建新函数

在 Visual Studio 中打开函数应用，在解决方案资源管理器中右键单击项目名称，然后单击“添加”>“新建 Azure 函数”。 

在“新建 Azure 函数”窗口中选择“事件网格触发器”，然后单击“确定”。   

![创建新的函数](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

创建函数后，打开代码文件，并复制文件顶部已注释掉的 URL。 配置事件网格触发器时将使用此位置。

![复制位置](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

然后，在以 `log.LogInformation` 开头的行中设置一个断点。

![设置断点](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


接下来，**按 F5** 启动调试会话。

## <a name="allow-azure-to-call-your-local-function"></a>使 Azure 能够调用本地函数

若要在计算机上调试的函数处中断，必须通过某种方式使 Azure 能够在云中与本地函数通信。

[ngrok](https://ngrok.com/) 实用工具可让 Azure 调用计算机上运行的函数。 使用以下命令启动 *ngrok*：

```bash
ngrok http -host-header=localhost 7071
```
设置该实用工具后，命令窗口应如以下屏幕截图所示：

![启动 ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

复制运行 *ngrok* 时生成的 **HTTPS** URL。 配置事件网格事件终结点时将使用此值。

## <a name="add-a-storage-event"></a>添加存储事件

打开 Azure 门户并导航到存储帐户，然后单击“事件”选项。 

![添加存储帐户事件](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

在“事件”窗口中，单击“事件订阅”按钮。   在“事件订阅”窗口中，单击“终结点类型”下拉列表并选择“Web Hook”。   

![选择订阅类型](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

配置终结点类型后，单击“选择终结点”以配置终结点值。 

![选择终结点类型](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

“订阅者终结点”值由三个不同的值构成。  前缀是 *ngrok* 生成的 HTTPS URL。 URL 的剩余部分来自函数代码文件中的 URL，其末尾添加了函数名称。 首先处理函数代码文件中的 URL：将 `http://localhost:7071` 替换为 *ngrok* URL，将 `{functionname}` 替换为函数名称。

以下屏幕截图显示了最终 URL 的形式：

![终结点选择](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

输入适当的值后，单击“确认选择”。 

> [!IMPORTANT]
> 每次启动 *ngrok* 时，都会重新生成 HTTPS URL，并且值会更改。 因此，每次通过 *ngrok* 向 Azure 公开函数时，都必须创建新的事件订阅。

## <a name="upload-a-file"></a>上传文件

现在，可将一个文件上传到存储帐户，以触发由本地函数处理的事件网格事件。 

打开[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)并连接到存储帐户。 

- 展开“Blob 容器”  
- 单击右键并选择“创建 Blob 容器”。 
- 将容器命名为 **test**
- 选择 *test* 容器
- 单击“上传”按钮 
- 单击“上传文件” 
- 选择文件并将其上传到 Blob 容器

## <a name="debug-the-function"></a>调试函数

事件网格识别到已将新文件上传到存储容器之后，即会命中本地函数中的断点。

![启动 ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>清理资源

若要清理本文中创建的资源，请删除存储帐户中的 **test** 容器。

## <a name="next-steps"></a>后续步骤

- [使用事件网格自动调整已上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions 的事件网格触发器](./functions-bindings-event-grid.md)
