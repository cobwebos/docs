---
title: 传输项目
description: 使用 Azure 存储帐户创建传输管道，将映像集合或其他项目从一个容器注册表传输到另一个注册表
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: fd2cee972ef173853572b871bc80b92b28c505cd
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932594"
---
# <a name="transfer-artifacts-to-another-registry"></a>将项目传输到另一个注册表

本文介绍如何将映像集合或其他注册表项目从一个 Azure 容器注册表传输到另一个注册表。 源和目标注册表可存在于相同或不同的订阅、Active Directory 租户、Azure 云或相互独立的云中。 

要传输项目，请创建一个传输管道，以使用 [blob 存储](../storage/blobs/storage-blobs-introduction.md)在两个注册表之间复制项目：

* 将源注册表中的项目导出到源存储帐户中的 blob 
* 将 blob 从源存储帐户复制到目标存储帐户
* 目标存储帐户中的 blob 作为项目导入到目标注册表。 可以将导入管道设置为在目标存储中更新项目 blob 时触发。

传输非常适合用于在相互独立的云中复制两个 Azure 容器注册表之间的内容，这种方法可按每个云中的存储帐户进行调解。 如果要从连接云中的容器注册表中复制映像（包括 Docker 集线器和其他云供应商），则建议使用 [图像导入](container-registry-import-images.md) 。

在本文中，我们将使用 Azure 资源管理器模板部署来创建和运行传输管道。 Azure CLI 用于预配关联的资源（例如存储机密）。 建议使用 Azure CLI 2.2.0 或更高版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI][azure-cli]。

此功能在“高级”容器注册表服务层级中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表层级](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前以预览版提供。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

* **容器注册表** - 需要具有要传输项目的现有源注册表以及目标注册表。 ACR 传输适用于在相互独立的云之间移动内容。 出于测试目的，源注册表和目标注册表可存在于相同或不同的 Azure 订阅、Active Directory 租户或云中。 

   如果需要创建注册表，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 
* **存储帐户** - 在选择的订阅和位置中创建源和目标存储帐户。 出于测试目的，可使用与源和目标注册表相同的一个或多个订阅。 对于跨云方案，通常在每个云中创建一个单独的存储帐户。 

  如果需要，请使用 [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) 或其他工具创建存储帐户。 

  在每个帐户中创建用于传输项目的 blob 容器。 例如，创建一个名为 transfer 的容器。 两个或多个传输管道可以共享同一个存储帐户，但应使用不同的存储容器作用域。
* **密钥保管库** - 需要使用密钥保管库来存储用于访问源和目标存储帐户的 SAS 令牌机密。 请在与源和目标注册表相同的一个或多个 Azure 订阅中创建源和目标密钥保管库。 为了便于演示，本文中使用的模板和命令还假定源和目标密钥保管库分别与源和目标注册表位于同一资源组中。 不需要使用公共资源组，但可以简化本文中使用的模板和命令。

   如果需要，请使用 [Azure CLI](../key-vault/secrets/quick-create-cli.md) 或其他工具创建密钥保管库。

* **环境变量** - 对于本文中的示例命令，可为源和目标环境设置以下环境变量。 所有示例的格式都适用于 Bash shell。
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>方案概述

为注册表间的映像传输创建以下三个管道资源。 这些管道资源均通过 PUT 操作创建。 这些资源在源和目标注册表及存储帐户上运行 。 

存储身份验证使用 SAS 令牌，这些令牌在密钥保管库中作为机密进行管理。 管道使用托管标识来读取保管库中的机密。

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** - 持久资源，其中包含有关源注册表和存储帐户的高级信息。 这些信息包括源存储 blob 容器 URI 以及管理源 SAS 令牌的密钥保管库。 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** - 持久资源，其中包含有关目标注册表和存储帐户的高级信息。 这些信息包括目标存储 blob 容器 URI 以及管理目标 SAS 令牌的密钥保管库。 导入触发器默认启用，因此当项目 blob 位于目标存储容器中时，管道会自动运行。 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** - 用于调用 ExportPipeline 或 ImportPipeline 资源的资源。  
  * 创建 PipelineRun 资源并指定要导出的项目可手动运行 ExportPipeline。  
  * 如果启用了导入触发器，ImportPipeline 则会自动运行。 还可以使用 PipelineRun 手动运行。 
  * 目前，每个 PipelineRun 最多可传输 **50 个项目** 。

### <a name="things-to-know"></a>使用须知
* ExportPipeline 和 ImportPipeline 通常位于与源和目标云关联的不同 Active Directory 租户中。 此方案需要单独的托管标识和密钥保管库，以用于导出和导入资源。 出于测试目的，可以将这些资源放在同一个云中，以共享标识。
* 默认情况下，ExportPipeline 和 ImportPipeline 模板都允许系统分配的托管标识访问密钥保管库机密。 ExportPipeline 和 ImportPipeline 模板还支持你提供的用户分配的标识。 

## <a name="create-and-store-sas-keys"></a>创建并存储 SAS 密钥

传输使用共享访问签名 (SAS) 令牌来访问源和目标环境中的存储帐户。 按照以下部分的说明生成并存储令牌。  

### <a name="generate-sas-token-for-export"></a>生成用于导出的 SAS 令牌

运行 [az storage container generate-sas][az-storage-container-generate-sas] 命令，为源存储帐户中的容器生成 SAS 令牌，用于项目导出。

推荐的令牌权限：读取、写入、列出、添加。 

在下面的示例中，将命令输出分配给 EXPORT_SAS 环境变量，以“?”字符作为前缀。 更新环境的 `--expiry` 值：

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

使用 [az keyvault secret][az-keyvault-secret-set] 将 SAS 令牌存储在源 Azure 密钥保管库中：

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>生成用于导入的 SAS 令牌

运行 [az storage container generate-sas][az-storage-container-generate-sas] 命令，为目标存储帐户中的容器生成 SAS 令牌，用于项目导入。

推荐的令牌权限：读取、删除、列出

在下面的示例中，将命令输出分配给 IMPORT_SAS 环境变量，以“?”字符作为前缀。 更新环境的 `--expiry` 值：

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

使用 [az keyvault secret][az-keyvault-secret-set] 将 SAS 令牌存储在目标 Azure 密钥保管库中：

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>使用资源管理器创建 ExportPipeline

使用 Azure 资源管理器模板部署为源容器注册表创建 ExportPipeline 资源。

将 ExportPipeline 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines)复制到本地文件夹。

在文件 `azuredeploy.parameters.json` 中输入以下参数值：

|参数  |Value  |
|---------|---------|
|registryName     | 源容器注册表的名称      |
|exportPipelineName     |  为导出管道选择的名称       |
|targetUri     |  源环境（导出管道的目标）中存储容器的 URI。<br/>示例： `https://sourcestorage.blob.core.windows.net/transfer`       |
|KeyVaultName     |  源密钥保管库的名称  |
|sasTokenSecretName  | 源密钥保管库中 SAS 令牌机密的名称 <br/>示例：acrexportsas

### <a name="export-options"></a>导出选项

导出管道的 `options` 属性支持可选的布尔值。 建议使用以下值：

|参数  |Value  |
|---------|---------|
|options | OverwriteBlobs - 覆盖现有目标 blob<br/>ContinueOnErrors - 如果一个项目导出失败，则在源注册表中继续导出其余项目。

### <a name="create-the-resource"></a>创建资源

运行 [az deployment group create][az-deployment-group-create] 以创建名为 *exportPipeline* 的资源，如以下示例中所示。 默认情况下，使用第一个选项时，示例模板将在 ExportPipeline 资源中启用系统分配的标识。 

使用第二个选项，可以为资源提供用户分配的标识。  (创建用户分配的标识。 ) 

使用任一选项时，模板会将标识配置为访问导出密钥保管库中的 SAS 令牌。 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>选项1：创建资源并启用系统分配的标识

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>选项2：创建资源并提供用户分配的标识

在此命令中，提供用户分配的标识的资源 ID 作为附加参数。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

在命令输出中，记下管道的资源 ID (`id`)。 可运行 [az deployment group show][az-deployment-group-show] 将此值存储在环境变量中供将来使用。 例如：

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>使用资源管理器创建 ImportPipeline 

使用 Azure 资源管理器模板部署在目标容器注册表中创建 ImportPipeline 资源。 默认情况下，当目标环境中的存储帐户具有项目 blob 时，管道会自动导入。

将 ImportPipeline 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines)复制到本地文件夹。

在文件 `azuredeploy.parameters.json` 中输入以下参数值：

参数  |Value  |
|---------|---------|
|registryName     | 目标容器注册表的名称      |
|importPipelineName     |  为导入管道选择的名称       |
|sourceUri     |  目标环境（导入管道的源）中存储容器的 URI。<br/>示例： `https://targetstorage.blob.core.windows.net/transfer/`|
|KeyVaultName     |  目标密钥保管库的名称 |
|sasTokenSecretName     |  目标密钥保管库中 SAS 令牌机密的名称<br/>示例：acr importsas |

### <a name="import-options"></a>导入选项

导入管道的 `options` 属性支持可选的布尔值。 建议使用以下值：

|参数  |Value  |
|---------|---------|
|options | OverwriteTags - 覆盖现有目标标记<br/>DeleteSourceBlobOnSuccess - 成功导入到目标注册表后删除源存储 blob<br/>ContinueOnErrors - 如果一个项目导入失败，则在目标注册表中继续导入其余项目。

### <a name="create-the-resource"></a>创建资源

运行 [az deployment group create][az-deployment-group-create] 以创建名为 *importPipeline* 的资源，如以下示例中所示。 默认情况下，使用第一个选项时，示例模板将在 ImportPipeline 资源中启用系统分配的标识。 

使用第二个选项，可以为资源提供用户分配的标识。  (创建用户分配的标识。 ) 

使用任一选项时，模板会将标识配置为在导入密钥保管库中访问 SAS 令牌。 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>选项1：创建资源并启用系统分配的标识

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>选项2：创建资源并提供用户分配的标识

在此命令中，提供用户分配的标识的资源 ID 作为附加参数。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

如果计划手动运行导入，请记下管道的资源 ID (`id`)。 可以通过运行 [az deployment group show][az-deployment-group-show] 命令将此值存储在环境变量中供以后使用。 例如：

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>使用资源管理器创建用于导出的 PipelineRun 

使用 Azure 资源管理器模板部署为源容器注册表创建 PipelineRun 资源。 此资源运行之前创建的 ExportPipeline 资源，并将容器注册表中的指定项目作为 blob 导出到源存储帐户。

将 PipelineRun 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export)复制到本地文件夹。

在文件 `azuredeploy.parameters.json` 中输入以下参数值：

|参数  |Value  |
|---------|---------|
|registryName     | 源容器注册表的名称      |
|pipelineRunName     |  为运行选择的名称       |
|pipelineResourceId     |  导出管道的资源 ID。<br/>示例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  为导出到源存储帐户的项目 blob 选择的名称，如 myblob
|项目 | 要传输的源项目的数组，作为标记或清单摘要<br/>示例： `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

如果重新部署具有相同属性的 PipelineRun 资源，则还必须使用 [forceUpdateTag](#redeploy-pipelinerun-resource) 属性。

运行 [az deployment group create][az-deployment-group-create] 以创建 PipelineRun 资源。 下面的示例将部署命名为 exportPipelineRun。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

以后使用时，将管道的资源 ID 存储在一个环境变量中：

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

导出项目可能需要几分钟。 部署成功完成后，请在源存储帐户的 transfer 容器中列出导出的 blob，以验证项目导出。 例如，运行 [az storage blob list][az-storage-blob-list] 命令：

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>传输 blob（可选） 

使用 AzCopy 工具或其他方法将 [blob 数据](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts)从源存储帐户传输到目标存储帐户。

例如，以下 [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) 命令会将源帐户下 transfer 容器中的 myblob 复制到目标帐户下的 transfer 容器 。 如果该 blob 存在于目标帐户中，则会被覆盖。 身份验证使用对源和目标容器具有相应权限的 SAS 令牌。 （未显示用于创建令牌的步骤。）

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>触发 ImportPipeline 资源

如果启用了 ImportPipeline 的 `sourceTriggerStatus` 参数（默认值），将 blob 复制到目标存储帐户后会触发管道。 导入项目可能需要几分钟。 导入成功完成后，请在目标容器注册表中列出存储库，以验证项目导入。 例如，运行 [az acr repository list][az-acr-repository-list]：

```azurecli
az acr repository list --name <target-registry-name>
```

如果未启用导入管道的 `sourceTriggerStatus` 参数，请手动运行 ImportPipeline 资源，如以下部分所示。 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>使用资源管理器创建用于导入的 PipelineRun（可选） 
 
还可以使用 PipelineRun 资源触发用于目标容器注册表项目导入的 ImportPipeline。

将 PipelineRun 资源管理器[模板文件](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import)复制到本地文件夹。

在文件 `azuredeploy.parameters.json` 中输入以下参数值：

|参数  |Value  |
|---------|---------|
|registryName     | 目标容器注册表的名称      |
|pipelineRunName     |  为运行选择的名称       |
|pipelineResourceId     |  导入管道的资源 ID。<br/>示例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  存储帐户中已导出项目的现有 blob 名称，如 myblob

如果重新部署具有相同属性的 PipelineRun 资源，则还必须使用 [forceUpdateTag](#redeploy-pipelinerun-resource) 属性。

运行 [az deployment group create][az-deployment-group-create] 以运行资源。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

以后使用时，将管道的资源 ID 存储在一个环境变量中：

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)

When deployment completes successfully, verify artifact import by listing the repositories in the target container registry. For example, run [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>重新部署 PipelineRun 资源

如果重新部署具有 *相同属性*的 PipelineRun 资源，则必须利用 **forceUpdateTag** 属性。 此属性指示应重新创建 PipelineRun 资源，即使配置尚未更改也是如此。 请确保每次重新部署 PipelineRun 资源时 forceUpdateTag 是不同的。 下面的示例重新创建用于导出的 PipelineRun。 当前日期时间用于设置 forceUpdateTag，从而确保此属性始终唯一。

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>删除管道资源

下面的示例命令使用 [az resource delete][az-resource-delete] 删除本文中创建的管道资源。 资源 Id 之前已存储在环境变量中。

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>故障排除

* **模板部署故障或错误**
  * 如果管道运行失败，请查看运行资源的 `pipelineRunErrorMessage` 属性。
  * 有关常见的模板部署错误，请参阅 [ARM 模板部署疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **导出或导入存储 blob 时出现问题**
  * SAS 令牌可能已过期，或者可能没有足够的权限来运行指定的导出或导入
  * 在多个导出运行期间，源存储帐户中的现有存储 blob 可能未被覆盖。 确认在导出运行中设置了 OverwriteBlob 选项，并且 SAS 令牌具有足够的权限。
  * 在导入运行成功后，可能未删除目标存储帐户中的存储 blob。 确认在导入运行中设置了 DeleteBlobOnSuccess 选项，并且 SAS 令牌具有足够的权限。
  * 未创建或未删除存储 blob。 确认在导出或导入运行中指定的容器存在，或用于手动导入运行的指定存储 blob 存在。 
* **AzCopy 问题**
  * 请参阅 [AzCopy 问题疑难解答](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues)。  
* **项目传输问题**
  * 并未传输所有项目或者根本未传输任何项目。 确认导出运行中的项目拼写，以及导出和导入运行中的 blob 名称。 确认正在传输的项目最多为50。
  * 管道运行可能未完成。 导出或导入运行可能需要一些时间。 
  * 对于其他管道问题，请向 Azure 容器注册表团队提供导出运行或导入运行的部署[相关 ID](../azure-resource-manager/templates/deployment-history.md)。


## <a name="next-steps"></a>后续步骤

要从公共注册表或其他专用注册表将单个容器映像导入到 Azure 容器注册表，请参阅 [az acr import][az-acr-import] 命令参考。

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
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
[az-resource-delete]: /cli/azure/resource#az-resource-delete
