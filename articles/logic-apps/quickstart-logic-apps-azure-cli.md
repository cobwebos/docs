---
title: 使用 Azure CLI 创建和管理逻辑应用
description: 使用 Azure CLI 创建逻辑应用，然后使用 list、show (get)、update 和 delete 等操作管理逻辑应用。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/30/2020
ms.openlocfilehash: e492a5f0afdfc2087e5719df65221d08db0a2e77
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499545"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建和管理逻辑应用

本快速入门演示如何使用 [Azure CLI 逻辑应用扩展](/cli/azure/ext/logic/logic?view=azure-cli-latest) (`az logic`) 创建和管理逻辑应用。 在命令行中，可以使用逻辑应用工作流定义的 JSON 文件来创建逻辑应用。 然后，可以通过从命令行运行 `list`、`show` (`get`)、`update` 和 `delete` 等操作来管理逻辑应用。

> [!WARNING]
> Azure CLI 逻辑应用扩展当前是试验性的，未涵盖在客户支持中 。 请谨慎使用此 CLI 扩展，尤其是在生产环境中选择使用扩展时。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在本地计算机上安装的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 在计算机上安装的[逻辑应用 Azure CLI 扩展](/cli/azure/azure-cli-extensions-list?view=azure-cli-latest)。 若要安装此扩展，请使用此命令：`az extension add --name logic`
* 要在其中创建逻辑应用的 [ Azure 资源组](#example---create-resource-group)。

### <a name="prerequisite-check"></a>先决条件检查

开始之前验证环境：

* 登录 Azure 门户，并通过运行 `az login` 来检查订阅是否处于活动状态。
* 通过运行 `az --version` 在终端或命令窗口中检查 Azure CLI 版本。 有关最新版本，请参阅[最新发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli&view=azure-cli-latest)。
  * 如果没有最新版本，请按照[适用于你操作系统或平台的安装指南](/cli/azure/install-azure-cli?view=azure-cli-latest)来更新安装。

### <a name="example---create-resource-group"></a>示例 - 创建资源组

如果还没有用于逻辑应用的资源组，请使用命令 `az group create` 创建该组。 例如，以下命令在位置 `westus` 创建名为 `testResourceGroup` 的资源组。

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

已成功创建资源组时，输出会将 `provisioningState` 显示为 `Succeeded`：

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>工作流定义

使用 Azure CLI [创建新逻辑应用](#create-logic-apps-from-cli)或[更新现有逻辑应用](#update-logic-apps-from-cli)之前，需要逻辑应用的工作流定义。 在 Azure 门户中，可以通过从“设计器”视图切换到“代码”视图，以 JSON 格式查看逻辑应用的基础工作流定义。

运行命令以创建或更新逻辑应用时，工作流定义会作为必需参数 (`--definition`) 进行上传。 必须将工作流定义创建为遵循[工作流定义语言架构](./logic-apps-workflow-definition-language.md)的 JSON 文件。

## <a name="create-logic-apps-from-cli"></a>从 CLI 创建逻辑应用

可以使用命令 [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create) 以及定义的 JSON 文件，从 Azure CLI 创建逻辑应用工作流。

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

命令必须包含以下[必需参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create-required-parameters)：

| 参数 | 值 | 说明 |
| --------- | ----- | ----------- |
| 工作流定义 | `--definition` | JSON 文件，包含逻辑应用的[工作流定义](#workflow-definition)。 |
| 位置 | `--location -l` | 逻辑应用所在的 Azure 区域。 |
| 名称 | `--name -n` | 逻辑应用的名称。 名称只能包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号 (`()`) 和句点 (`.`)。 该名称还必须在区域间唯一。 |
| 资源组名称 | `--resource-group -g` | 要在其中创建逻辑应用的 [Azure 资源组](../azure-resource-manager/management/overview.md)。 如果还没有用于逻辑应用的资源组，请在开始之前[创建一个资源组](#example---create-resource-group)。 |

还可以包含其他[可选参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create-optional-parameters)以配置逻辑应用的访问控制、终结点、集成帐户、集成服务环境、状态和资源标记。

### <a name="example---create-logic-app"></a>示例 - 创建逻辑应用

在此示例中，在位置 `westus` 的资源组 `testResourceGroup` 中创建了名为 `testLogicApp` 的工作流。 JSON 文件 `testDefinition.json` 包含工作流定义。

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

成功创建工作流后，CLI 会显示新工作流定义的 JSON 代码。 如果工作流创建失败，请参阅[可能错误的列表](#errors)。

## <a name="update-logic-apps-from-cli"></a>从 CLI 更新逻辑应用

还可以使用命令 [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create)，从 Azure CLI 更新逻辑应用工作流。

命令必须包含与[创建逻辑应用](#create-logic-apps-from-cli)时相同的[必需参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create-required-parameters)。 还可以添加与创建逻辑应用时相同的[可选参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create-optional-parameters)。

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>示例 - 更新逻辑应用

在此示例中，会更新[在上一节中创建的示例工作流](#example---create-logic-app)，以使用不同的 JSON 定义文件 `newTestDefinition.json`，并添加两个具有说明值的资源标记 `testTag1` 和 `testTag2`。

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

成功更新工作流后，CLI 会显示逻辑应用更新后的工作流定义。 如果更新失败，请参阅[可能错误的列表](#errors)。

## <a name="delete-logic-apps-from-cli"></a>从 CLI 删除逻辑应用

可以使用命令 [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-delete)，从 Azure CLI 删除逻辑应用工作流。

命令必须包含以下[必需参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-delete-required-parameters)：

| 参数 | 值 | 说明 |
| --------- | ----- | ----------- |
| 名称 | `--name -n` | 逻辑应用的名称。 |
| 资源组名称 | `-resource-group -g` | 逻辑应用所在的资源组。 |

还可以包含一个[可选参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-delete-optional-parameters)以跳过确认提示 `--yes -y`。

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

CLI 随后会提示确认删除逻辑应用。 可以通过将可选参数 `--yes -y` 与命令一起使用来跳过确认提示。

```azurecli

Are you sure you want to perform this operation? (y/n):

```

可以通过[在 CLI 中列出逻辑应用](#list-logic-apps-in-cli)，或者通过在 Azure 门户中查看逻辑应用，来确认逻辑应用的删除。

### <a name="example---delete-logic-app"></a>示例 - 删除逻辑应用

在此示例中，会删除[在上一节中创建的示例工作流](#example---create-logic-app)。

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

在使用 `y` 响应确认提示之后，会删除逻辑应用。

## <a name="show-logic-apps-in-cli"></a>在 CLI 中显示逻辑应用

可以使用命令 [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-show) 获取特定逻辑应用工作流。

```azurecli

az logic workflow show --name
                       --resource-group

```

命令必须包含以下[必需参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-show-required-parameters)

| 参数 | 值 | 说明 |
| --------- | ----- | ----------- |
| 名称 | `--name -n` | 逻辑应用的名称。 |
| 资源组名称 | `--resource-group -g` | 逻辑应用所在的资源组的名称。 |

### <a name="example---get-logic-app"></a>示例 - 获取逻辑应用

在此示例中，会返回资源组 `testResourceGroup` 中的逻辑应用 `testLogicApp` 以及用于调试的完整日志。

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>在 CLI 中列出逻辑应用

可以使用命令 [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-list) 按订阅列出逻辑应用。 此命令返回逻辑应用工作流的 JSON 代码。

可以按以下[可选参数](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-list-optional-parameters)来筛选结果：

| 参数 | 值 | 说明 |
| --------- | ----- | ----------- |
| 资源组名称 | `--resource-group -g` | 要用于筛选结果的资源组的名称。 |
| 项数 | `--top` | 结果中包含的项数。 |
| 筛选器 | `--filter` | 要对列表使用的筛选器的类型。 可以按状态 (`State`)、触发器 (`Trigger`) 以及所引用资源的标识符 (`ReferencedResourceId`) 进行筛选。 |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>示例 - 列出逻辑应用

在此示例中，资源组 `testResourceGroup` 中所有启用的工作流都会以 ASCII 表格式返回。

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>错误

以下错误指示未安装 Azure 逻辑应用 CLI 扩展。 按照先决条件中的步骤在计算机上[安装逻辑应用扩展](#prerequisites)。

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

以下错误可能指示用于上传工作流定义的文件路径不正确。

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>全局参数

可以将以下可选全局 Azure CLI 参数与 `az logic` 命令一起使用：

| 参数 | 值 | 说明 |
| --------- | ----- | ----------- |
| 输出格式 | `--output -o` | 从默认 JSON 更改[输出格式](/cli/azure/format-output-azure-cli?view=azure-cli-latest)。 |
| 仅显示错误 | `--only-show-errors` | 取消显示警告，仅显示错误。 |
| “详细” | `--verbose` | 显示详细日志。 |
| 调试 | `--debug` | 显示所有调试日志。 |
| 帮助消息 | `--help -h` | 显示帮助对话框。 |
| 查询 | `--query` | 为 JSON 输出设置 JMESPath 查询字符串。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/?view=azure-cli-latest)。

有关如何通过 CLI 创建基本逻辑应用的示例，请参阅[示例脚本和工作流定义](sample-logic-apps-cli-script.md)。

可以在 [Microsoft 代码示例浏览器](/samples/browse/?products=azure-logic-apps)中找到其他逻辑应用 CLI 脚本示例。
