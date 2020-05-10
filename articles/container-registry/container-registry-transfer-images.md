---
title: 传输项目
description: 使用 Azure 存储帐户创建传输管道，将映像或其他项目的集合从一个容器注册表传输到另一个注册表
ms.topic: article
ms.date: 05/08/2020
ms.custom: ''
ms.openlocfilehash: fd551671422931a51f5aa6468de87e28e3a81b5b
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006321"
---
# <a name="transfer-artifacts-to-another-registry"></a>将项目传输到另一个注册表

本文介绍如何将映像或其他注册表项目的集合从一个 Azure 容器注册表传输到另一个注册表。 源和目标注册表可以在相同或不同的订阅中，Active Directory 租户、Azure 云或物理上断开连接的云。 

若要传输项目，请创建一个*传输管道*，用于通过使用[blob 存储](../storage/blobs/storage-blobs-introduction.md)在两个注册表之间复制项目：

* 源注册表中的项目导出到源存储帐户中的 blob 
* Blob 将从源存储帐户复制到目标存储帐户
* 目标存储帐户中的 blob 作为项目导入到目标注册表。 可以将导入管道设置为在目标存储中更新项目 blob 时触发。

传输非常适合用于在物理上断开连接的云中复制两个 Azure 容器注册表中的内容，经过调谐按每个云中的存储帐户进行复制。 若要从连接云中的容器注册表中复制映像（包括 Docker 中心和其他云供应商），建议改为[导入映像](container-registry-import-images.md)。

本文介绍如何使用 Azure 资源管理器模板部署来创建和运行传输管道。 该 Azure CLI 用于设置关联的资源（例如存储机密）。 建议 Azure CLI 版本2.2.0 或更高版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI][azure-cli]。

此功能在“高级”容器注册表服务层级中可用。  有关注册表服务层和限制的信息，请参阅[Azure 容器注册表层](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前以预览版提供。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

* **容器注册表**-需要用于传输项目的现有源注册表和目标注册表。 ACR 传输专用于跨物理断开连接的云进行移动。 为了进行测试，源注册表项和目标注册表可以在相同或不同的 Azure 订阅中 Active Directory 租户或云。 如果需要创建注册表，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 
* **存储帐户**-在订阅中创建源和目标存储帐户，以及选择的位置。 出于测试目的，可以使用与源和目标注册表相同的订阅或订阅。 对于跨云方案，通常在每个云中创建一个单独的存储帐户。 如果需要，请用[Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli)或其他工具创建存储帐户。 

  在每个帐户中创建用于项目传输的 blob 容器。 例如，创建一个名为*transfer*的容器。 两个或多个传输管道可以共享同一存储帐户，但应使用不同的存储容器作用域。
* **Key** vault-需要使用密钥保管库来存储用于访问源和目标存储帐户的 SAS 令牌机密。 在源和目标注册表所在的同一 Azure 订阅或订阅中创建源密钥保管库和目标密钥保管库。 如果需要，请用[Azure CLI](../key-vault/quick-create-cli.md)或其他工具创建密钥保管库。
* **环境变量**-如本文中的命令，请为源和目标环境设置以下环境变量。 所有示例都是通过 Bash shell 进行格式设置的。
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>方案概述

为注册表间的图像传输创建了以下三个管道资源。 所有使用 PUT 运算创建。 这些资源在*源*和*目标*注册表和存储帐户上运行。 

存储身份验证使用 SAS 令牌，作为密钥保管库中的机密进行管理。 管道使用托管标识来读取保管库中的机密。

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** -长期资源，包含有关*源*注册表和存储帐户的高级信息。 此信息包括源存储 blob 容器 URI 和管理源 SAS 令牌的密钥保管库。 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** -持久的资源，包含有关*目标*注册表和存储帐户的高级信息。 此信息包括目标存储 blob 容器 URI 和管理目标 SAS 令牌的密钥保管库。 默认情况下会启用 import 触发器，因此当项目 blob 位于目标存储容器中时，管道会自动运行。 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** -用于调用 ExportPipeline 或 ImportPipeline 资源的资源。  
  * 通过创建 PipelineRun 资源并指定要导出的项目，可手动运行 ExportPipeline。  
  * 如果启用了导入触发器，则 ImportPipeline 会自动运行。 还可以使用 PipelineRun 手动运行。 
  * 目前，每个 PipelineRun 最多可以传输**10 个项目**。

### <a name="things-to-know"></a>使用须知
* ExportPipeline 和 ImportPipeline 通常位于与源云和目标云关联 Active Directory 租户中。 此方案需要用于导出和导入资源的单独托管标识和密钥保管库。 出于测试目的，可以将这些资源置于同一云中，共享标识。
* 管道示例创建系统分配的托管标识以访问密钥保管库机密。 ExportPipelines 和 ImportPipelines 还支持用户分配的标识。 在这种情况下，必须使用标识的访问策略来配置密钥保管库。 

## <a name="create-and-store-sas-keys"></a>创建并存储 SAS 密钥

传输使用共享访问签名（SAS）令牌来访问源和目标环境中的存储帐户。 按照以下部分中的说明生成并存储令牌。  

### <a name="generate-sas-token-for-export"></a>为导出生成 SAS 令牌

运行[az storage container 生成 sas][az-storage-container-generate-sas]命令，为源存储帐户中的容器生成 sas 令牌，用于项目导出。

*建议的令牌权限*：读取、写入、列出、添加。 

在下面的示例中，将命令输出分配给 EXPORT_SAS 环境变量，以 "？" 字符作为前缀。 更新环境`--expiry`的值：

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>存储用于导出的 SAS 令牌

使用[az keyvault 机密集][az-keyvault-secret-set]将 SAS 令牌存储在源 Azure 密钥保管库中：

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>为导入生成 SAS 令牌

运行[az storage container 生成 sas][az-storage-container-generate-sas]命令，为目标存储帐户中的容器生成 sas 令牌，用于项目导入。

*推荐的令牌权限*：读取、删除、列出

在下面的示例中，将命令输出分配给 IMPORT_SAS 环境变量，以 "？" 字符作为前缀。 更新环境`--expiry`的值：

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>存储用于导入的 SAS 令牌

使用[az keyvault 机密集][az-keyvault-secret-set]将 SAS 令牌存储在目标 Azure 密钥保管库中：

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>资源管理器创建 ExportPipeline

使用 Azure 资源管理器模板部署为源容器注册表创建 ExportPipeline 资源。

将 ExportPipeline 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines)复制到本地文件夹。

在文件`azuredeploy.parameters.json`中输入以下参数值：

|参数  |Value  |
|---------|---------|
|registryName     | 源容器注册表的名称      |
|exportPipelineName     |  为导出管道选择的名称       |
|targetUri     |  源环境（导出管道的目标）中的存储容器的 URI。<br/>示例： `https://sourcestorage.blob.core.windows.net/transfer`       |
|KeyVaultName     |  源密钥保管库的名称  |
|sasTokenSecretName  | 源密钥保管库中 SAS 令牌机密的名称 <br/>示例： acrexportsas

### <a name="export-options"></a>导出选项

导出`options`管道的属性支持可选的布尔值。 建议使用以下值：

|参数  |Value  |
|---------|---------|
|选项 | OverwriteBlobs-覆盖现有目标 blob<br/>ContinueOnErrors-如果一个项目导出失败，则在源注册表中继续导出剩余项目。

### <a name="create-the-resource"></a>创建资源

运行[az deployment group create][az-deployment-group-create]以创建资源。 下面的示例将部署*exportPipeline*命名为。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

在命令输出中，记下管道的资源 ID （`id`）。 通过运行[az deployment group show][az-deployment-group-show]，你可以将此值存储在环境变量中供以后使用。 例如：

```azurecli
EXPORT_RES_ID=$(az group deployment show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>资源管理器创建 ImportPipeline 

使用 Azure 资源管理器模板部署在目标容器注册表中创建 ImportPipeline 资源。 默认情况下，当目标环境中的存储帐户具有项目 blob 时，管道会自动导入。

将 ImportPipeline 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines)复制到本地文件夹。

在文件`azuredeploy.parameters.json`中输入以下参数值：

参数  |Value  |
|---------|---------|
|registryName     | 目标容器注册表的名称      |
|importPipelineName     |  为导入管道选择的名称       |
|sourceUri     |  目标环境（导入管道的源）中存储容器的 URI。<br/>示例： `https://targetstorage.blob.core.windows.net/transfer/`|
|KeyVaultName     |  目标密钥保管库的名称 |
|sasTokenSecretName     |  目标密钥保管库中 SAS 令牌机密的名称<br/>示例： acr importsas |

### <a name="import-options"></a>导入选项

导`options`入管道的属性支持可选的布尔值。 建议使用以下值：

|参数  |Value  |
|---------|---------|
|选项 | OverwriteTags-覆盖现有目标标记<br/>DeleteSourceBlobOnSuccess-成功导入到目标注册表后删除源存储 blob<br/>ContinueOnErrors-如果一个项目导入失败，则继续在目标注册表中导入剩余项目。

### <a name="create-the-resource"></a>创建资源

运行[az deployment group create][az-deployment-group-create]以创建资源。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json \
  --name importPipeline
```

如果你计划手动运行导入，请记下管道的资源 ID （`id`）。 通过运行[az deployment group show][az-deployment-group-show]，你可以将此值存储在环境变量中供以后使用。 例如：

```azurecli
IMPORT_RES_ID=$(az group deployment show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>创建用于资源管理器导出的 PipelineRun 

使用 Azure 资源管理器模板部署为源容器注册表创建 PipelineRun 资源。 此资源运行之前创建的 ExportPipeline 资源，并将容器注册表中指定的项目作为 blob 导出到源存储帐户。

将 PipelineRun 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export)复制到本地文件夹。

在文件`azuredeploy.parameters.json`中输入以下参数值：

|参数  |Value  |
|---------|---------|
|registryName     | 源容器注册表的名称      |
|pipelineRunName     |  为运行选择的名称       |
|pipelineResourceId     |  导出管道的资源 ID。<br/>示例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  为导出到源存储帐户的项目 blob 选择的名称，如*myblob*
|项目 | 要传输的源项目的数组，作为标记或清单摘要<br/>示例： `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

运行[az deployment group create][az-deployment-group-create]以创建 PipelineRun 资源。 下面的示例将部署*exportPipelineRun*命名为。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

导出项目可能需要几分钟时间。 部署成功完成后，通过在源存储帐户的*传输*容器中列出导出的 blob 来验证项目导出。 例如，运行[az storage blob list][az-storage-blob-list]命令：

```azurecli
az storage blob list \
  --account-name $SA_SOURCE
  --container transfer
  --output table
```

## <a name="transfer-blob-optional"></a>传输 blob （可选） 

使用 AzCopy 工具或其他方法将[blob 数据](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts)从源存储帐户传输到目标存储帐户。

例如，以下[`azcopy copy`](/azure/storage/common/storage-ref-azcopy-copy)命令将源帐户中的*传输*容器中的 myblob 复制到目标帐户中的*传输*容器。 如果该 blob 存在于目标帐户中，则会被覆盖。 身份验证对源和目标容器使用具有适当权限的 SAS 令牌。 （不显示用于创建令牌的步骤。）

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>触发 ImportPipeline 资源

如果启用了 ImportPipeline `sourceTriggerStatus`的参数（默认值），则在将 blob 复制到目标存储帐户后，将触发管道。 导入项目可能需要几分钟时间。 导入成功完成后，通过列出目标容器注册表中的存储库来验证项目导入。 例如，运行[az acr repository list][az-acr-repository-list]：

```azurecli
az acr repository list --name <target-registry-name>
```

如果未启用导入`sourceTriggerStatus`管道的参数，请手动运行 ImportPipeline 资源，如以下部分所示。 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>创建 PipelineRun 以便资源管理器导入（可选） 
 
还可以使用 PipelineRun 资源触发 ImportPipeline，以将项目导入到目标容器注册表中。

将 PipelineRun 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import)复制到本地文件夹。

在文件`azuredeploy.parameters.json`中输入以下参数值：

|参数  |Value  |
|---------|---------|
|registryName     | 目标容器注册表的名称      |
|pipelineRunName     |  为运行选择的名称       |
|pipelineResourceId     |  导入管道的资源 ID。<br/>示例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  存储帐户中导出项目的现有 blob 的名称，如*myblob*

运行[az deployment group create][az-deployment-group-create]以运行资源。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

部署成功完成后，通过列出目标容器注册表中的存储库来验证项目导入。 例如，运行[az acr repository list][az-acr-repository-list]：

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="delete-pipeline-resources"></a>删除管道资源

若要删除管道资源，请使用[az deployment group delete][az-deployment-group-delete]命令删除其资源管理器部署。 下面的示例删除本文中创建的管道资源：

```azurecli
az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipeline

az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun

az deployment group delete \
  --resource-group $TARGET_RG \
  --name importPipeline  
```

## <a name="troubleshooting"></a>故障排除

* **模板部署失败或错误**
  * 如果管道运行失败，请查看运行资源`pipelineRunErrorMessage`的属性。
  * 有关常见的模板部署错误，请参阅[ARM 模板部署故障排除](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **导出或导入存储 blob 时出现问题**
  * SAS 令牌可能已过期，或者可能没有足够的权限来执行指定的导出或导入
  * 在多个导出运行期间，源存储帐户中的现有存储 blob 可能不会被覆盖。 确认在导出运行中设置了 OverwriteBlob 选项，并且 SAS 令牌具有足够的权限。
  * 在成功执行导入后，可能不会删除目标存储帐户中的存储 blob。 确认在导入运行中设置了 DeleteBlobOnSuccess 选项，并且 SAS 令牌具有足够的权限。
  * 未创建或删除存储 blob。 确认在导出或导入运行中指定的容器存在，或指定的存储 blob 存在，因此无法进行手动导入。 
* **AzCopy 问题**
  * 请参阅[AzCopy 问题的疑难解答](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues)。  
* **项目传输问题**
  * 不是所有项目，也不传输任何项目。 确认导出运行中的项目的拼写和导出和导入运行中的 blob 的名称。 确认正在传输最多10个项目。
  * 管道运行可能未完成。 导出或导入运行可能需要一些时间。 
  * 对于其他管道问题，请向 Azure 容器注册表组提供导出运行或导入运行的部署[相关 ID](../azure-resource-manager/templates/deployment-history.md) 。


## <a name="next-steps"></a>后续步骤

若要将单个容器映像从公共注册表或其他专用注册表导入到 Azure 容器注册表，请参阅[az acr import][az-acr-import]命令参考。

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import



