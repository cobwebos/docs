---
title: Azure Functions 部署槽
description: 了解如何在 Azure Functions 中创建和使用部署槽
author: craigshoemaker
ms.topic: reference
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 7cfbd533921ba4d1757e7415a3bb8f70aeb71251
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122409"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions 部署槽

Azure Functions 部署槽使函数应用能够运行不同的实例（称作“槽”）。 槽是通过公用终结点公开的不同环境。 一个应用实例始终映射到生产槽，你可以按需交换分配给某个槽的实例。 在应用服务计划下运行的函数应用可以有多个槽，而在消耗计划下运行的函数应用只允许有一个槽。

下面反映了交换槽对函数的影响：

- 流量重定向是无缝的；不会因为交换而丢弃任何请求。
- 如果某个函数正在交换期间运行，则继续执行，并将下一个触发器路由到交换应用程序实例。

> [!NOTE]
> 槽目前不适用于 Linux 消耗计划。

## <a name="why-use-slots"></a>为何要使用槽？

使用部署槽可获得多种优势。 以下方案描述了槽的常见用途：

- **满足不同用途的不同环境**：使用不同的槽可以在交换到生产槽或过渡槽之前区分应用实例。
- **预热**：部署到某个槽而不是直接部署到生产环境可让应用在上线之前进行预热。 此外，使用槽可以降低 HTTP 触发的工作负荷的延迟。 在部署之前准备好实例，这可减少新部署函数的冷启动。
- **轻松回退**：与生产环境交换后，以前包含过渡应用的槽现在包含以前的生产应用。 如果交换到生产槽的更改不符合预期，你可以立即反向交换，以恢复“上次已知正常的实例”。

## <a name="swap-operations"></a>交换操作

在交换期间，一个槽被视为源，另一个槽被视为目标。 源槽包含已应用到目标槽的应用程序实例。 以下步骤可确保在交换期间目标槽不会发生停机：

1. **应用设置：** 目标槽的设置将应用到源槽的所有实例。 例如，生产设置将应用到过渡实例。 应用的设置包括以下类别：
    - [特定于槽的](#manage-settings)应用设置和连接字符串（如果适用）
    - [持续部署](../app-service/deploy-continuous-deployment.md)设置（如果已启用）
    - [应用服务身份验证](../app-service/overview-authentication-authorization.md)设置（如果已启用）

1. **等待重启和可用性：** 交换会等待源槽中的每个实例完成其重新启动并可用于请求。 如果有任何实例无法重启，交换操作将会还原对源槽所做的所有更改，并停止操作。

1. **更新路由：** 如果源槽上的所有实例都已成功准备好，则两个槽通过切换路由规则来完成交换。 完成此步骤后，目标槽（例如生产槽）包含先前已在源槽中预热的应用。

1. **重复操作：** 现在源槽具有以前在目标槽中的预交换应用，请通过应用所有设置并重新启动源槽的实例来完成相同的操作。

请注意以下几点：

- 在执行交换操作期间的任何时候，已交换应用的初始化将在源槽上发生。 当源槽准备就绪时，无论交换成功还是失败，目标槽仍保持联机状态。

- 若要将过渡槽与生产槽交换，请确保生产槽始终是目标槽。** 这样，交换操作才不会影响生产应用。

- *开始交换前*，必须将与事件源和绑定相关的设置配置为 "[部署槽设置](#manage-settings)"。 提前将这些设置标记为“粘性”可确保将事件和输出定向到正确的实例。

## <a name="manage-settings"></a>管理设置

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>创建部署设置

你可以将设置标记为部署设置，这会使其 "粘滞"。 粘滞设置不会与应用程序实例交换。

如果在一个槽中创建部署设置，请确保使用交换所涉及的任何其他槽中的唯一值创建相同的设置。 这样，设置的值不会更改，同时设置名称可在不同的槽之间保持一致。 此名称一致性可确保代码不会尝试访问已在一个槽中定义，但未在另一个槽中定义的设置。

使用以下步骤创建部署设置：

1. 在 function app 中导航到 "**部署槽**"，然后选择槽名称。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="在 Azure 门户中查找槽。" border="true":::

1. 选择 "**配置**"，然后选择要用于当前槽的设置名称。

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="在 Azure 门户中配置槽的应用程序设置。" border="true":::

1. 选择 "**部署槽设置**"，然后选择 **"确定"**。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="配置部署槽位设置。" border="true":::

1. 设置节消失后，请选择 "**保存**" 以保留更改

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="保存部署槽设置。" border="true":::

## <a name="deployment"></a>部署

创建槽时，槽是空的。 可以使用任何[支持的部署技术](./functions-deployment-technologies.md)将应用程序部署到槽中。

## <a name="scaling"></a>扩展

所有槽将缩放到与生产槽中相同的辅助角色数。

- 对于消耗计划，槽将随着函数应用的缩放而缩放。
- 对于应用服务计划，应用会缩放到固定数量的辅助角色。 槽在与应用计划相同的数目的辅助角色上运行。

## <a name="add-a-slot"></a>添加槽

可以通过 [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) 或门户添加槽。 以下步骤演示如何在门户中创建新槽：

1. 导航到 function app。

1. 选择 "**部署槽**"，然后选择 " **+ 添加槽**"。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="添加 Azure Functions 部署槽。" border="true":::

1. 键入槽的名称，然后选择 "**添加**"。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="将 Azure Functions 部署槽位。" border="true":::

## <a name="swap-slots"></a>交换槽

可以通过 [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) 或门户交换槽。 以下步骤演示如何在门户中交换槽：

1. 导航到函数应用。
1. 选择 "**部署槽**"，然后选择 "**交换**"。

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="交换部署槽位。" border="true":::

1. 验证交换的配置设置，并选择 "**交换**"
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="交换部署槽位。" border="true":::

执行交换操作可能需要花费一段时间。

## <a name="roll-back-a-swap"></a>回滚交换

如果交换导致出错，或者你想要“撤消”交换，可以回滚到初始状态。 若要恢复到交换前的状态，请再次执行交换以反向交换。

## <a name="remove-a-slot"></a>删除槽

可以通过 [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) 或门户删除槽。 以下步骤演示如何在门户中删除槽：

1. 在 function app 中导航到 "**部署槽**"，然后选择槽名称。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="在 Azure 门户中查找槽。" border="true":::

1. 选择“删除”  。

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="删除 Azure 门户中的部署槽位。" border="true":::

1. 键入要删除的部署槽的名称，然后选择 "**删除**"。

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="删除 Azure 门户中的部署槽位。" border="true":::

1. 关闭 "删除确认" 窗格。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="部署槽删除确认。" border="true":::

## <a name="automate-slot-management"></a>自动槽管理

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest) 可以针对槽自动执行以下操作：

- [创建](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [购](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [自动交换](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>更改应用服务计划

使用在应用服务计划下运行的 function app，你可以更改槽的底层应用服务计划。

> [!NOTE]
> 无法在消耗计划下更改槽的应用服务计划。

使用以下步骤更改槽的应用服务计划：

1. 在 function app 中导航到 "**部署槽**"，然后选择槽名称。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="在 Azure 门户中查找槽。" border="true":::

1. 在 "**应用服务计划**" 下，选择 "**更改应用服务计划**"。

1. 选择要升级到的计划，或创建新的计划。

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="在 Azure 门户中更改应用服务计划。" border="true":::

1. 选择“确定”  。

## <a name="limitations"></a>限制

Azure Functions 部署槽存在以下限制：

- 应用可用的槽数取决于计划。 消耗计划仅允许一个部署槽。 在应用服务计划中运行的应用可以使用更多的槽。
- 交换某个槽会重置其 `AzureWebJobsSecretStorageType` 应用设置等于 `files` 的应用的密钥。
- 槽不适用于 Linux 消耗计划。

## <a name="support-levels"></a>支持级别

部署槽有两个支持级别：

- **公开上市（GA）**：完全支持并批准生产使用。
- **预览版**：目前尚不支持，但预计将来会达到 GA 状态。

| OS/托管计划           | 支持级别     |
| ------------------------- | -------------------- |
| Windows 消耗计划       | 正式版 |
| Windows 高级           | 正式版  |
| Windows 专用计划         | 正式版 |
| Linux 消耗计划         | 不支持          |
| Linux 高级版             | 正式版  |
| Linux 专用计划           | 正式版 |

## <a name="next-steps"></a>后续步骤

- [Azure Functions 中的部署技术](./functions-deployment-technologies.md)
