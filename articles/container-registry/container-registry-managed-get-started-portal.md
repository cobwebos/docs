---
title: "创建私有 Docker 注册表 - Azure 门户 | Microsoft 文档"
description: "开始使用 Azure 门户创建和管理私有 Docker 容器注册表"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>使用 Azure 门户创建托管的容器注册表

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何使用 Azure 门户创建托管的 Azure 容器注册表实例。

托管的 Azure 容器注册表为预览版，尚未在所有区域推出。

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-container-registry"></a>创建容器注册表

1. 单击 Azure 门户左上角的“新建”按钮。

2. 在应用商店中搜索“Azure 容器注册表”，然后将其选中。

3. 单击“创建”打开创建 ACR 的边栏选项卡。

    ![容器注册表设置](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. 在“Azure 容器注册表”边栏选项卡中输入以下信息。 完成后，单击“创建”。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **注册表名称**：特定注册表的全局唯一顶级域名。 在本示例中，注册表名称为 *myAzureContainerRegistry1*，但需要将它替换为你自己的唯一名称。 该名称只能包含字母和数字。

    b.保留“数据库类型”设置，即设置为“共享”。 **资源组**：选择现有的[资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)，或键入新资源组的名称。

    c. **位置**：选择[提供](https://azure.microsoft.com/regions/services/)该服务的 Azure 数据中心位置，例如“美国中南部”。

    d.单击“下一步”。 **管理员用户**：如果需要，请启用一个管理员用户来访问注册表。 创建注册表后可以更改此设置。

    e. 使用托管的注册表：选择“是”即可让 ACR 自动管理注册表存储、使用 Webhook，以及使用 AAD 身份验证。

    f. 定价层：选择定价层，然后参阅此处的 ACR 定价以了解详细信息。

## <a name="log-in-to-acr-instance"></a>登录到 ACR 实例

在推送和拉取容器映像之前，必须登录到 ACR 实例。 

为此，请使用 Azure CLI 2.0。 首先，请根据需要使用 [az login](/cli/azure/#login) 命令登录到 Azure。 

```azurecli
az login
```

接下来，使用 [az acr login](/cli/azure/acr#login) 命令登录到 Azure 容器注册表。

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>使用 Azure 容器注册表

### <a name="list-container-images"></a>列出容器映像

使用 `az acr` CLI 命令查询存储库中的映像和标记。

> [!NOTE]
> 目前，容器注册表不支持使用 `docker search` 命令查询映像和标记。

### <a name="list-repositories"></a>列出存储库

以下示例使用 JSON（JavaScript 对象表示法）格式列出注册表中的存储库：

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>列出标记

以下示例使用 JSON 格式列出 **samples/nginx** 存储库中的标记：

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Azure 门户创建托管的 Azure 容器注册表实例。

> [!div class="nextstepaction"]
> [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)
