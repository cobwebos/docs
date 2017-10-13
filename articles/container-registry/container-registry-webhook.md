---
title: "Azure 容器注册表 webhook | Microsoft Docs"
description: "Azure 容器注册表 webhook"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker、容器、ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.openlocfilehash: 35f2b940a36ca3176cf850afd97e62af0dd797ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-container-registry-webhooks---azure-portal"></a>使用 Azure 容器注册表 webhook - Azure 门户

Azure 容器注册表可存储和管理专用 Docker 容器映像，其方式类似于 Docker Hub 存储公共 Docker 映像。 使用 webhook 在其中一个注册表存储库中发生特定操作时触发事件。 Webhook 可在注册表级别响应事件或者将其范围缩小到特定存储库标记。 

如需更多背景和概念，请参阅[概述](./container-registry-intro.md)。

## <a name="prerequisites"></a>先决条件 

- Azure 容器托管注册表 - 在 Azure 订阅中创建托管容器注册表。 例如，使用 Azure 门户或 Azure CLI 2.0。 
- Docker CLI - 若要将本地计算机设置为 Docker 主机并访问 Docker CLI 命令，请安装 Docker 引擎。 

## <a name="create-webhook-azure-portal"></a>创建 webhook Azure 门户

1. 登录到 Azure 门户并导航到要在其中创建 webhook 的注册表。 

2. 在容器边栏选项卡中，选择“Webhook”选项卡。 

3. 在 webhook 边栏选项卡工具栏中选择“添加”。 

4. 使用以下信息完成“创建 webhook”窗体：

| 值 | 说明 |
|---|---|
| 名称 | 想要赋予 webhook 的名称。 它只能包含某些小写字母和数字，且长度介于 5-50 个字符之间。 |
| 服务 URI | Webhook 应向其发送 POST 通知的 URI。 |
| 自定义标头 | 想要随 POST 请求一起传递的标头。 它们的格式应该为：“键: 值”。 |
| 触发操作 | 触发 webhook 的操作。 可通过映像的推送和/或删除操作触发当前 webhook。 |
| 状态 | Webhook 创建后的状态。 默认启用。 |
| 范围 | Webhook 的作用域。 默认情况下，作用域为注册表中的所有事件。 可使用“存储库: 标记”格式针对存储库或标记进行指定。 |

示例 Webhook 窗体：

![DCOS UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>创建 webhook Azure CLI

若要使用 Azure CLI 创建 webhook，请使用 [az acr webhook create](/cli/azure/acr/webhook#create) 命令。

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>测试 webhook

### <a name="azure-portal"></a>Azure 门户

在对容器映像推送和删除操作使用 webhook 之前，可以使用“Ping”按钮进行测试。 使用时，Ping 将向指定的终结点发送泛型发布请求并记录响应。 这有助于验证是否已正确设置 webhook。

1. 选择想要测试的 webhook。 
2. 在顶部工具栏中，选择“Ping”操作。 
3. 查看请求和响应。

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 测试 ACR webhook，请使用 [az acr webhook ping](/cli/azure/acr/webhook#ping) 命令。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

若要查看结果，请使用 [az acr webhook list-events](/cli/azure/acr/webhook#list-events) 命令。 

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>删除 webhook

### <a name="azure-portal"></a>Azure 门户

每个 webhook 均可通过在 Azure 门户中选择 webhook，然后选择删除按钮进行删除。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```