---
title: 教程 - 在 Azure 存储中存储 Terraform 状态
description: 在 Azure 存储中存储 Terraform 状态的简介。
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 374936c39221d79d59fc8a54dc2bc4a49800240d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078567"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>教程：在 Azure 存储中存储 Terraform 状态

Terraform 状态用于对已部署资源和 Terraform 配置进行对帐。 Terraform 可通过状态了解需要添加、更新或删除的 Azure 资源。 默认情况下，在运行 `terraform apply` 命令时，Terraform 状态存储在本地。 此配置并不理想，原因如下：

- 本地状态不适用于团队或协作环境。
- Terraform 状态可能包含敏感信息。
- 本地存储状态会增加意外删除的可能性。

Terraform 支持在远程存储中保存状态。 其中一个受支持的后端是 Azure 存储。 本文档介绍如何配置和使用 Azure 存储以实现此目的。

## <a name="configure-storage-account"></a>配置存储帐户

必须先创建存储帐户，才能将 Azure 存储用作后端。 可使用 Azure 门户、PowerShell、Azure CLI 或 Terraform 本身创建存储帐户。 按照以下示例通过 Azure CLI 配置存储帐户。

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

记录存储帐户名称、容器名称和存储访问密钥。 配置远程状态时需要这些值。

## <a name="configure-state-back-end"></a>配置状态后端

Terraform 状态后端是在运行 `terraform init` 命令时配置的。 配置状态后端需要以下数据：

- **storage_account_name**：Azure 存储帐户的名称。
- **container_name**：blob 容器的名称。
- **key**：要创建的状态存储文件的名称。
- **access_key**：存储访问密钥。

其中每个值都可以在 Terraform 配置文件中或在命令行上指定。 建议对 `access_key` 值使用环境变量。 使用环境变量可防止将密钥写入磁盘。

创建具有 Azure 存储访问密钥值的环境变量，名称为 `ARM_ACCESS_KEY`。

```bash
export ARM_ACCESS_KEY=<storage access key>
```

若要进一步保护 Azure 存储帐户访问密钥，请将其存储在 Azure Key Vault 中。 然后可使用如下命令设置该环境变量。 有关 Azure Key Vault 的详细信息，请参阅 [Azure Key Vault 文档](../key-vault/quick-create-cli.md)。

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

若要将 Terraform 配置为使用后端，需要执行以下步骤：
- 包含类型为 `azurerm` 的 `backend` 配置块。
- 向配置块添加 `storage_account_name` 值。
- 向配置块添加 `container_name` 值。
- 向配置块添加 `key` 值。

下面的示例配置 Terraform 后端并创建 Azure 资源组。

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

通过执行以下步骤初始化配置：

1. 运行 `terraform init` 命令。
1. 运行 `terraform apply` 命令。

现可在 Azure 存储 blob 中找到状态文件。

## <a name="state-locking"></a>状态锁定

在执行任何写入状态的操作之前，Azure 存储 blob 都会自动锁定。 此模式可防止并发状态操作（可能导致损坏）。 

有关详细信息，请参阅 Terraform 文档中的[状态锁定](https://www.terraform.io/docs/state/locking.html)。

通过 Azure 门户或其他 Azure 管理工具检查 blob 时，可以查看该锁。

![具有锁的 Azure blob](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>静态加密

Azure blob 中存储的数据会在保存之前进行加密。 如有需要，Terraform 会从后端检索状态并将其存储在本地内存中。 使用此模式时，状态永远不会写入本地磁盘。

有关 Azure 存储加密的详细信息，请参阅[静态数据的 Azure 存储服务加密](../storage/common/storage-service-encryption.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [详细了解如何在 Azure 中使用 Terraform](/azure/terraform)