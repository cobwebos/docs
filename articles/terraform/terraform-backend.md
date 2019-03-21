---
title: 将 Azure 存储用作 Terraform 后端
description: 在 Azure 存储中存储 Terraform 状态的简介。
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 7145a50bc53fd28afafd3de9c724b5e5f71624fa
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762292"
---
# <a name="store-terraform-state-in-azure-storage"></a>在 Azure 存储中存储 Terraform 状态

Terraform 状态用于对已部署资源和 Terraform 配置进行对帐。 Terraform 可通过状态了解需要添加、更新或删除的 Azure 资源。 默认情况下，运行 Terraform apply 的同时将 Terraform 状态存储在本地。 此配置不是理想选择，原因如下：

- 本地状态不适用于团队或协作环境
- Terraform 状态可能包含敏感信息
- 本地存储状态会增加意外删除的可能性

Terraform 涉及状态后端的概念，状态后端是 Terraform 状态的远程存储。 使用状态后端时，状态文件存储在 Azure 存储等数据存储中。 本文档详细介绍如何配置 Azure 存储并将其用作 Terraform 状态后端。

## <a name="configure-storage-account"></a>配置存储帐户

将 Azure 存储用作后端前，必须先创建存储帐户。 可使用 Azure 门户、PowerShell、Azure CLI 或 Terraform 本身创建存储帐户。 按照以下示例通过 Azure CLI 配置存储帐户。

```azurecli-interactive
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

## <a name="configure-state-backend"></a>配置状态后端

在运行 Terraform init 时配置 Terraform 状态后端。 配置状态后端需要以下数据。

- storage_account_name - Azure 存储帐户的名称。
- container_name - blob 容器的名称。
- key - 要创建的状态存储文件的名称。
- access_key - 存储访问密钥。

所有这些值均可在 Terraform 配置文件或命令行中指定，但是，建议对 `access_key` 使用环境变量。 使用环境变量可防止将密钥写入磁盘。

创建具有 Azure 存储访问密钥值的环境变量，名称为 `ARM_ACCESS_KEY`。

```console
export ARM_ACCESS_KEY=<storage access key>
```

若要进一步保护 Azure 存储帐户访问密钥，请将其存储在 Azure Key Vault 中。 然后可使用如下命令设置该环境变量。 有关 Azure Key Vault 的详细信息，请参阅 [Azure Key Vault 文档][azure-key-vault]。

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

要将 Terraform 配置为使用后端，请在 Terraform 配置中包括类型为 azurerm 的后端配置。 向配置块中添加 storage_account_name、container_name 和 key 值。

下面的示例配置 Terraform 后端并创建 Azure 资源组。 将值替换为你的环境中的值。

```json
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

现在，使用 Terraform init 初始化配置，然后通过 Terraform apply 运行配置。 完成后即可在 Azure 存储 Blob 中找到状态文件。

## <a name="state-locking"></a>状态锁定

将 Azure 存储 Blob 用于状态存储时，在任何操作写入状态前，blob 会自动锁定。 此配置可防止多个并发状态操作（可能导致损坏）。 有关详细信息，请参阅 Terraform 文档中的[状态锁定][terraform-state-lock]。

可在通过 Azure 门户或其他 Azure 管理工具检查 blob 时查看该锁。

![具有锁的 Azure blob](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>静态加密

默认情况下，Azure Blob 中存储的数据会在保存到存储基础结构之前进行加密。 当 Terraform 需要状态时，系统将从后端检索状态并将其存储在开发系统内存中。 在此配置中，状态在 Azure 存储中是受保护的，不会写入本地磁盘。

有关 Azure 存储加密的详细信息，请参阅[静态数据的 Azure 存储服务加密][azure-storage-encryption]。

## <a name="next-steps"></a>后续步骤

有关 Terraform 后端配置的详细信息，请参阅 [Terraform 后端文档][terraform-backend]。

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
