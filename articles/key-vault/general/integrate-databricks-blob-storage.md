---
title: 使用 Azure Databricks 和 Azure Key Vault 访问 Azure Blob 存储
description: 在本教程中，你将了解如何使用 Azure Key Vault 中存储的机密从 Azure Databricks 访问 Azure Blob 存储
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: 50ea93dea62b6e4e333e58550b2750897c7e0a77
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988230"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>教程：使用 Azure Databricks 和 Azure Key Vault 访问 Azure Blob 存储

在本教程中，你将了解如何使用 Azure Key Vault 中存储的机密从 Azure Databricks 访问 Azure Blob 存储。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure CLI 创建存储帐户和 Blob 容器
> * 创建 Key Vault 并设置机密
> * 创建 Azure Databricks 工作区并添加 Key Vault 机密范围
> * 从 Azure Databricks 工作区访问 Blob 容器

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

在开始学习本教程之前，请安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>使用 Azure CLI 创建存储帐户和 Blob 容器

需要先创建常规用途存储帐户才能使用 Blob。 如果没有[资源组](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)，请在运行该命令之前创建一个资源组。 以下命令会创建存储容器并显示其元数据。 复制 ID。

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![上述命令的控制台输出。 ID 以绿色突出显示，供最终用户查看。](../media/databricks-command-output-1.png)

需要为自己分配[存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)角色，然后才能创建要向其上传 Blob 的容器。 在本例中，角色将分配给之前创建的存储帐户。

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

你现已将角色分配给存储帐户，接下来可为 Blob 创建容器。

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

创建容器后，可将 Blob（所选文件）上传到该容器。 在本例中，上传了包含 helloworld 的 .txt 文件。

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

列出容器中的 Blob，以验证容器是否具有该 Blob。

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![上述命令的控制台输出。 它会显示刚刚存储在容器中的文件。](../media/databricks-command-output-2.png)

使用以下命令获取存储容器的 key1 值。 复制此值。

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![上述命令的控制台输出。 key1 的值在绿色框中突出显示。](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>创建 Key Vault 并设置机密

将使用以下命令创建 Key Vault。 此命令也将显示 Key Vault 的元数据。 复制 ID 和 vaultUri 。

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![图像](../media/databricks-command-output-4.png)
![上述命令的控制台输出。 ID 和 vaultUri 均以绿色突出显示，供用户查看。](../media/databricks-command-output-5.png)

若要创建机密，请使用以下命令。 将机密的值设置为存储帐户中的 key1 值。

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>创建 Azure Databricks 工作区并添加 Key Vault 机密范围

此部分不能通过命令行完成。 按照此[指南](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope)操作。 需要访问 [Azure 门户](https://ms.portal.azure.com/#home)来执行以下操作：

1. 创建 Azure Databricks 资源
1. 启动工作区
1. 创建 Key Vault 支持的机密范围

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>从 Azure Databricks 工作区访问 Blob 容器

此部分不能通过命令行完成。 按照此[指南](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks)操作。 需要使用 Azure Databricks 工作区来执行以下操作：

1. 创建新群集
1. 创建新笔记本
1. 填写 Python 脚本中的相应字段
1. 运行 Python 脚本

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>后续步骤

确保 Key Vault 可恢复：
> [!div class="nextstepaction"]
> [清理资源](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
