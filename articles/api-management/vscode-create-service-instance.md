---
title: 使用 Visual Studio Code 创建 Azure API 管理实例 | Microsoft Docs
description: 使用 Visual Studio Code 创建 Azure API 管理实例。
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 19080679291b88b693c95bd71f8ddc0e59286356
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057309"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>快速入门：使用 Visual Studio Code 创建新的 Azure API 管理服务实例

Azure API 管理 (APIM) 可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。 API 管理通过开发人员参与、商业洞察力、分析、安全性和保护提供了核心竞争力以确保成功的 API 程序。 使用 APIM 可以为在任何位置托管的现有后端服务创建和管理新式 API 网关。 有关详细信息，请参阅[概述](api-management-key-concepts.md)主题。

本快速入门介绍了使用 Visual Studio Code 的 *Azure API 管理扩展预览版*创建新的 API 管理实例的步骤。 你还可以使用此扩展对 API 管理实例执行常见管理操作。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

另外，请确保已安装以下项：

- [Visual Studio Code](https://code.visualstudio.com/)

- [Visual Studio Code 的 Azure API 管理扩展（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>登录 Azure

启动 Visual Studio Code，然后打开此 Azure 扩展。 （如果在活动栏上未看到 Azure 图标，请确保已启用 Azure API 管理扩展。）

选择“登录到 Azure...”以启动一个浏览器窗口并登录到你的 Microsoft 帐户。

![从 VS Code 的 API 管理扩展登录到 Azure](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>创建 API 管理服务

登录到你的 Microsoft 帐户后，“Azure：API 管理”资源管理器窗格会列出你的 Azure 订阅。

右键单击要使用的订阅，然后选择“在 Azure 中创建 API 管理”。

![VS Code 中的“创建 API 管理”向导](./media/vscode-create-service-instance/vscode-apim-create.png)

在打开的窗格中，为新的 API 管理实例提供名称。 它在 Azure 中必须是全局唯一的，并且包含 1-50 个字母数字字符和/或连字符，以字母开头，以字母数字结尾。

将使用指定的名称创建新的 API 管理实例（和父资源组）。 默认情况下，将使用“消耗”SKU 在“美国西部”区域中创建实例。 

> [!TIP]
> 如果你在“Azure API 管理扩展设置”中启用了“高级创建”，则还可以指定一个 [API 管理 SKU](https://azure.microsoft.com/pricing/details/api-management/)、[Azure 区域](https://status.azure.com/en-us/status)和一个[资源组](../azure-resource-manager/management/overview.md)来部署你的 API 管理实例。
>
> 虽然“消耗”SKU 需要不到一分钟的时间就能完成预配，但其他 SKU 通常需要 30-40 分钟才能完成创建。

此时，你已准备好导入并发布你的第一个 API。 你可以在 Visual Studio Code 的扩展中执行此操作，还可以执行常见的 API 管理操作。 有关详细信息，请参阅 [Visual Studio Code 的 API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)文档。

![VS Code API 管理扩展窗格中新创建的 API 管理实例](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>清理资源

当不再需要 API 管理实例和资源组时，请通过单击右键并选择“在门户中打开”来[删除 API 管理服务](get-started-create-service-instance.md#clean-up-resources)及其资源组，从而删除该实例和资源组。

另外，你还可以选择“删除 API 管理”，以仅删除 API 管理实例（此操作不会删除其资源组）。

![从 VS Code 中删除 API 管理实例](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入和发布第一个 API](import-and-publish.md)
