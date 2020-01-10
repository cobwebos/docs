---
title: Azure Functions 部署槽位
description: 了解如何在 Azure Functions 中创建和使用部署槽位
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769211"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions 部署槽位

Azure Functions 部署槽允许函数应用运行称为 "槽" 的不同实例。 槽是通过公开提供的终结点公开的不同环境。 一个应用程序实例始终映射到生产槽，可以按需交换分配给槽的实例。 在应用服务计划下运行的函数应用可能有多个槽，而在消耗计划下，只允许一个槽。

下面反映了交换槽如何影响函数：

- 流量重定向是无缝的;由于交换，不会删除任何请求。
- 如果某个函数正在交换期间运行，则继续执行，随后的触发器将路由到交换应用程序实例。

> [!NOTE]
> 对于 Linux 消耗计划，槽目前不可用。

## <a name="why-use-slots"></a>为什么使用插槽？

使用部署槽位有一些优点。 以下方案描述了槽的常见用途：

- 不同**用途的不同环境**：使用不同的槽使你能够在交换到生产或过渡槽之前区分应用程序实例。
- **Prewarming**：部署到一个槽而不是直接部署到生产环境，使应用程序能够在投入使用前进行预热。 此外，使用槽可减少 HTTP 触发的工作负荷的延迟。 在部署之前准备好实例，从而减少新部署的功能的冷启动。
- **轻松回退**：在与生产交换后，具有以前暂存的应用的槽现在具有以前的生产应用。 如果交换到生产槽中的更改与预期不同，可以立即反转交换以获取 "最后一个已知良好的实例"。

## <a name="swap-operations"></a>交换操作

交换期间，一个槽被视为源，另一个是目标。 源槽包含应用于目标槽的应用程序的实例。 以下步骤确保了在交换期间目标槽不会遇到停机时间：

1. **应用设置：** 来自目标槽的设置将应用于源槽的所有实例。 例如，生产设置将应用于暂存实例。 应用的设置包括以下类别：
    - [特定于槽的](#manage-settings)应用设置和连接字符串（如果适用）
    - [持续部署](../app-service/deploy-continuous-deployment.md)设置（如果已启用）
    - [应用服务身份验证](../app-service/overview-authentication-authorization.md)设置（如果已启用）

1. **等待重启和可用性：** 交换会等待源槽中的每个实例完成其重新启动并可用于请求。 如果任何实例无法重新启动，则交换操作会将所有更改恢复到源槽并停止操作。

1. **更新路由：** 如果源槽上的所有实例都已成功准备好，则两个槽通过切换路由规则来完成交换。 执行此步骤后，目标槽（例如，生产槽）包含先前在源槽中准备好的应用。

1. **重复操作：** 现在源槽具有以前在目标槽中的预交换应用，请通过应用所有设置并重新启动源槽的实例来执行相同的操作。

请注意以下几点：

- 在交换操作的任何时刻，交换的应用的初始化发生在源槽上。 准备好源槽时，目标槽保持联机，无论交换是成功还是失败。

- 若要将过渡槽交换到生产槽，请确保生产槽*始终*是目标槽。 这样，交换操作不会影响生产应用。

- *启动交换之前*，需要将与事件源和绑定相关的设置配置为 "[部署槽设置](#manage-settings)"。 提前将它们标记为 "粘滞" 可确保将事件和输出定向到正确的实例。

## <a name="manage-settings"></a>管理设置

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>创建部署设置

你可以将设置标记为将其设置为 "粘滞" 的部署设置。 粘滞设置不会与应用程序实例交换。

如果在一个槽中创建部署设置，请确保在交换所涉及的任何其他槽中创建相同的设置和唯一值。 这样一来，设置的值不会更改，设置名称在槽之间保持一致。 此名称一致性可确保代码不会尝试访问在一个槽中定义的设置，而不会尝试访问另一个槽中定义的设置。

使用以下步骤来创建部署设置：

- 在函数应用中导航到*槽*
- 单击插槽名称
- 在 "*平台功能 > 常规设置*" 下，单击 "**配置**"
- 单击要坚持当前槽的设置名称
- 单击 "**部署槽" 设置**复选框
- 单击 **“确定”**
- "设置" 边栏选项卡消失后，单击 "**保存**" 以保留更改

![部署槽设置](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>部署

创建槽时，槽为空。 你可以使用任何支持的[部署技术](./functions-deployment-technologies.md)将你的应用程序部署到槽。

## <a name="scaling"></a>缩放

所有槽的规模都与生产槽的数量相同。

- 对于消耗计划，槽将随着函数应用的缩放而扩展。
- 对于应用服务计划，应用会缩放为固定数量的辅助角色。 插槽与应用计划运行在相同数量的辅助角色上。

## <a name="add-a-slot"></a>添加槽位

可以通过[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)或通过门户添加槽。 以下步骤演示了如何在门户中创建新的槽：

1. 导航到 function app，并单击 "*槽*" 旁边的**加号**。

    ![添加 Azure Functions 部署槽位](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. 在文本框中输入名称，然后按 "**创建**" 按钮。

    ![部署槽位 Azure Functions 名称](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>交换槽

可以通过[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)或通过门户交换槽。 以下步骤演示了如何在门户中交换槽：

1. 导航到 function app
1. 单击要交换的源槽名称
1. 在 "*概述*" 选项卡中，单击 "**交换**" 按钮 ![交换 Azure Functions 部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. 验证交换的配置设置，并单击 "**交换**" ![交换 Azure Functions "部署槽](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

执行交换操作时，操作可能需要一段时间。

## <a name="roll-back-a-swap"></a>回滚交换

如果交换导致错误，或者只是想 "撤消" 交换，则可以回滚到初始状态。 若要返回到预交换的状态，请执行另一个交换来反向交换。

## <a name="remove-a-slot"></a>删除槽

可以通过[CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)或通过门户删除槽。 以下步骤演示了如何在门户中删除槽：

1. 导航到 function app 概述

1. 单击 "**删除**" 按钮

    ![添加 Azure Functions 部署槽位](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>自动插槽管理

使用[Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)，可以为槽自动执行以下操作：

- [create](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [自动交换](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>更改应用服务计划

使用在应用服务计划下运行的 function app，你可以选择更改槽的底层应用服务计划。

> [!NOTE]
> 不能在消耗计划下更改槽的应用服务计划。

使用以下步骤更改槽的应用服务计划：

1. 导航到槽

1. 在 "*平台功能*" 下，单击 "**所有设置**"

    ![更改应用服务计划](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. 单击 "**应用服务计划**"

1. 选择新的应用服务计划或创建新计划

1. 单击 **“确定”**

    ![更改应用服务计划](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>限制

Azure Functions 部署槽具有以下限制：

- 应用的可用槽数取决于计划。 消耗计划仅允许一个部署槽。 其他槽适用于在应用服务计划下运行的应用。
- 交换槽会重置 `AzureWebJobsSecretStorageType` 应用设置等于 `files`的应用的密钥。
- 槽不适用于 Linux 消耗计划。

## <a name="support-levels"></a>支持级别

部署槽支持两个级别：

- **公开上市（GA）** ：完全支持并批准生产使用。
- **预览版**：目前尚不支持，但预计将来会达到 GA 状态。

| OS/托管计划           | 支持级别     |
| ------------------------- | -------------------- |
| Windows 消耗       | 正式发布 |
| Windows 高级           | 正式发布  |
| Windows 专用         | 正式发布 |
| Linux 消耗         | 不支持          |
| Linux 高级版             | 正式发布  |
| Linux 专用           | 正式发布 |

## <a name="next-steps"></a>后续步骤

- [Azure Functions 中的部署技术](./functions-deployment-technologies.md)
