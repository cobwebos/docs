---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ec0425ff2188ecf1816d5f5841394c8e32f301d2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132229"
---
## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

使用 Visual Studio Code 可以将函数项目直接发布到 Azure。 在此过程中，将在 Azure 订阅中创建函数应用和相关的资源。 函数应用为函数提供了执行上下文。 该项目将打包并部署到 Azure 订阅中的新函数应用。

本文假定你要创建新的函数应用。 

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。

1. 在“Azure：函数”  区域中，选择“部署到函数应用”图标。

    ![函数应用设置](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. 如果未登录，则系统会提示你**登录到 Azure**。 还可以**创建免费 Azure 帐户**。 在成功从浏览器登录后，返回到 Visual Studio Code。 

1. 如果你有多个订阅，请为函数应用**选择一个订阅**，然后选择“+ 在 Azure 中创建新的函数应用”  。

1. 键入用于标识函数应用的全局唯一名称，然后按 Enter。 函数应用名称的有效字符包括 `a-z`、`0-9` 和 `-`。

1. 选择“+ 创建新的资源组”  ，键入资源组名称（如 `myResourceGroup`），然后按 Enter。 也可以使用现有资源组。

1. 选择“+ 新建存储帐户”  ，键入函数应用使用的新存储帐户的全局唯一名称，然后按 Enter。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可以使用现有帐户。

1. 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)中的位置。

    按 Enter 时，将在你的订阅中创建以下 Azure 资源：

    * **[资源组](../articles/azure-resource-manager/resource-group-overview.md)** ：包含创建的所有 Azure 资源。 名称基于你的函数应用名称。
    * **[存储帐户](../articles/storage/common/storage-quickstart-create-account.md)** ：将使用基于你的函数应用名称的唯一名称创建一个标准存储帐户。
    * **[托管计划](../articles/azure-functions/functions-scale.md)** ：将在美国西部区域中创建一个消耗计划来托管你的无服务器函数应用。
    * **函数应用**：你的项目将部署到此新的函数应用并在其中运行。

    创建函数应用并应用了部署包之后，会显示一个通知。 在此通知中选择“查看输出”  以查看创建和部署结果，其中包括你创建的 Azure 资源。

1. 返回到“Azure：函数”  区域，在你的订阅下展开新的函数应用。 展开“函数”  ，右键单击“HttpTrigger”  ，然后选择“复制函数 URL”  。

    ![复制新的 HTTP 触发器的函数 URL](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
