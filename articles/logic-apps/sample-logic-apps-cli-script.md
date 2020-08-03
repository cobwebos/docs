---
title: Azure CLI 脚本示例-创建逻辑应用
description: 通过 Azure CLI 中的逻辑应用扩展创建逻辑应用的示例脚本。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/30/2020
ms.openlocfilehash: ceb4a3356ef78d2129f76bd11f555a9ca5206d51
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505830"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI 脚本示例-创建逻辑应用

此脚本通过[Azure CLI 逻辑应用扩展](/cli/azure/ext/logic/logic?view=azure-cli-latest)（）创建一个示例逻辑应用 `az logic` 。 有关通过 Azure CLI 创建和管理逻辑应用的详细指南，请参阅 Azure CLI 的[逻辑应用快速入门](quickstart-logic-apps-azure-cli.md)。

> [!WARNING]
> Azure CLI 逻辑应用扩展当前为*试验*性，*不包含在客户支持范围*内。 请谨慎使用此 CLI 扩展，尤其是在生产环境中选择使用扩展时。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)安装在本地计算机上。
* 计算机上安装的[逻辑应用 Azure CLI 扩展](/cli/azure/azure-cli-extensions-list?view=azure-cli-latest)。 若要安装此扩展，请使用此命令：`az extension add --name logic`
* 逻辑应用的[工作流定义](quickstart-logic-apps-azure-cli.md#workflow-definition)。 此 JSON 文件必须遵循[工作流定义语言架构](logic-apps-workflow-definition-language.md)。
* 通过与逻辑应用相同的资源组中支持的[逻辑应用连接器](../connectors/apis-list.md)连接到电子邮件帐户的 API。 此示例使用[Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) connector，但你也可以使用其他连接器（如[Outlook.com](../connectors/connectors-create-api-outlook.md)）。

### <a name="prerequisite-check"></a>先决条件检查

在开始之前验证你的环境：

* 登录到 Azure 门户，并通过运行检查订阅是否处于活动状态 `az login` 。

* 通过运行来检查终端或命令窗口中的 Azure CLI 版本 `az --version` 。 有关最新版本，请参阅[最新的发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli&view=azure-cli-latest)。

  * 如果没有最新版本，请遵循[适用于你的操作系统或平台的安装指南](/cli/azure/install-azure-cli?view=azure-cli-latest)来更新你的安装。

### <a name="sample-workflow-explanation"></a>示例工作流说明

此示例工作流定义文件创建了与[Azure 门户的逻辑应用快速入门](quickstart-create-first-logic-app-workflow.md)相同的基本逻辑应用。 

此示例工作流： 

1. `$schema`为逻辑应用指定架构。

1. 定义触发器列表中逻辑应用的触发器 `triggers` 。 触发器 `recurrence` 每3小时重复一次。 `When_a_feed_item_is_published`为指定的 rss 源（）发布新的源项（）时，将触发这些操作 `feedUrl` 。

1. 定义操作列表中逻辑应用的操作 `actions` 。 操作将发送电子邮件（ `Send_an_email_(V2)` ）到 Microsoft 365，其中包含有关 RSS 源项的详细信息，如 `body` 操作输入（）的正文部分（）中所指定 `inputs` 。

## <a name="sample-workflow-definition"></a>示例工作流定义

在运行示例脚本之前，必须先创建一个示例[工作流定义](#prerequisites)。

1. 在您的计算机上创建一个 JSON 文件 `testDefinition.json` 。 

1. 将以下内容复制到 JSON 文件中： 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. 用自己的信息更新占位符值：

    1. 替换占位符电子邮件地址（ `"To": "test@example.com"` ）。 需要使用与逻辑应用连接器兼容的电子邮件地址。 有关详细信息，请参阅[先决条件](#prerequisites)。

    1. 如果你使用的电子邮件连接器与 Office 365 Outlook connector 不同，请更换其他连接器详细信息。

    1. 将连接标识符（）的占位符订阅值（）替换 `00000000-0000-0000-0000-000000000000` 为 `connectionId` 自己的 `id` `$connections` 订阅值。

1. 保存所做更改。

## <a name="sample-script"></a>示例脚本

> [!NOTE]
> 此示例是针对 shell 编写的 `bash` 。 如果要在另一 shell 中运行此示例，如 Windows PowerShell 或命令提示符，则可能需要对脚本进行修改。

运行此示例脚本之前，请运行以下命令连接到 Azure：

```azurecli-interactive

az login

```

接下来，导航到在其中创建工作流定义的目录。 例如，如果您在桌面上创建了工作流定义 JSON 文件：

```azurecli

cd ~/Desktop

```

然后，运行此脚本以创建逻辑应用。 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>清理部署

使用完示例脚本后，运行以下命令以删除资源组及其所有嵌套资源（包括逻辑应用）。

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>脚本说明

此示例脚本使用以下命令创建新的资源组和逻辑应用。

| Command | 注释 |
| ------- | ----- |
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 创建一个资源组，其中存储了逻辑应用的资源。 |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create) | 基于参数中定义的工作流创建逻辑应用 `--definition` 。 |
| [`az group delete`](/cli/azure/vm/extension?view=azure-cli-latest) | 删除资源组及其所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/?view=azure-cli-latest)。

可以在[Microsoft 代码示例浏览器](/samples/browse/?products=azure-logic-apps)中找到其他逻辑应用 CLI 脚本示例。
