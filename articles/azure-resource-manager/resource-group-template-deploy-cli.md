---
title: "使用 Azure CLI 和模板部署资源 | Microsoft Docs"
description: "使用 Azure Resource Manager 和 Azure CLI 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 模板和 Azure CLI 部署资源
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [门户](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

本主题介绍如何将 Azure CLI 与 Resource Manager 模板配合使用向 Azure 部署资源。  你的模板可以是本地文件或是可通过 URI 访问的外部文件。 如果模板驻留在存储帐户中，你可以限制对该模板的访问，并在部署过程中提供共享访问签名 (SAS) 令牌。

> [!TIP]
> 有关在部署过程中调试错误的帮助，请参阅：
> 
> * [查看部署操作](resource-manager-deployment-operations.md)，了解如何获取有助于排查错误的信息
> * [排查使用 Azure Resource Manager 将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)，了解如何解决常见的部署错误
> 
> 

## <a name="quick-steps-to-deployment"></a>快速部署步骤
若要快速开始进行部署，请使用以下命令：

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

这些命令会创建一个资源组，并将模板部署到该资源组。 模板文件和参数文件都是本地文件。 如果此操作成功，则你会获得所需的全部部署资源。 但是，还有更多可用于指定要部署的资源的选项。 本文的其余部分介绍了部署过程中可用的所有选项。 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>部署
如果以前未曾将 Azure CLI 与 Resource Manager 配合使用过，请参阅[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](xplat-cli-azure-resource-manager.md)。

1. 登录到你的 Azure 帐户。 提供凭据后，该命令将返回你的登录结果。
   
   ```
   azure login
   ```
2. 如果有多个订阅，请提供要用于部署的订阅 ID。
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. 在部署模板时，必须指定要包含已部署资源的资源组。 如果有要部署到的现有资源组，可以跳过此步骤，然后使用该资源组。 
   
     若要创建资源组，请提供资源组的名称和位置。 需要提供资源组的位置，因为资源组存储与资源有关的元数据。 出于合规目的，你可能想要指定该元数据的存储位置。 一般情况下，建议指定大部分资源将驻留的位置。 使用相同位置可简化模板。
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     将返回新资源组的摘要。
   
5. 在执行部署之前先运行 **azure group template validate** 命令验证部署。 测试部署时，请提供与执行部署时所提供的完全相同的参数（如下一步中所示）。
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. 若要将资源部署到资源组，请运行 **azure group deployment create** 命令并提供所需的参数。 参数包括部署的名称、资源组的名称、模板的路径或 URL，以及方案所需的任何其他参数。 如果未指定 **mode** 参数，将使用 **Incremental** 的默认值。 若要运行完整部署，请将 **mode** 设置为 **Complete**。 使用完整模式时要小心，因为可能会无意中删除不在模板中的资源。
   
     若要部署本地模板，请使用 **template-file** 参数：
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     若要部署外部模板，请使用 **template-uri** 参数：
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     前两个示例不包括参数值。 了解要在[参数](#parameters)部分中传递参数值的选项。 现在，系统会提示你提供使用以下语法的参数值：

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      部署资源组后，你会看到部署摘要。 该摘要包括 **ProvisioningState**，它指示部署是否成功。

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. 如果要记录可能帮助排查任何部署错误的有关部署的其他信息，请使用 **debug-setting** 参数。 你可以指定在对部署操作进行日志记录时记录请求内容或/和响应内容。
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>使用 SAS 令牌从存储空间部署模板
可以将模板添加到存储帐户，并在部署过程中使用 SAS 令牌链接到这些模板。

> [!IMPORTANT]
> 通过执行以下步骤，只有帐户所有者可以访问包含模板的 blob。 但是，如果为 blob 创建 SAS 令牌，则拥有该 URI 的任何人都可以访问 blob。 如果其他用户截获了该 URI，则此用户可以访问该模板。 使用 SAS 令牌是限制对模板的访问的好方法，但不应直接在模板中包括密码等敏感数据。
> 
> 

### <a name="add-private-template-to-storage-account"></a>将专用模板添加到存储帐户
以下步骤用于为模板设置存储帐户：

1. 创建资源组。
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. 创建存储帐户。 存储帐户名称必须在 Azure 中唯一，因此，请为帐户提供自己的名称。
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. 为存储帐户和密钥设置变量。
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. 创建容器。 权限设置为“关闭”，这意味着只有所有者可以访问该容器。
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. 将模板添加到该容器。
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>在部署期间提供 SAS 令牌
若要在存储帐户中部署专用模板，请检索 SAS 令牌，并将其包括在模板的 URI 中。

1. 创建具有读取权限和到期时间的 SAS 令牌来限制访问。 设置到期时间以允许足够的时间来完成部署。 检索包括 SAS 令牌的模板的完整 URI。
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. 通过提供包括 SAS 令牌的 URI 来部署该模板。
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

有关将 SAS 令牌与链接模板配合使用的示例，请参阅[将已链接的模版与 Azure Resource Manager 配合使用](resource-group-linked-templates.md)。

## <a name="parameters"></a>parameters

可以使用以下选项提供参数值： 
   
- 使用内联参数。 每个参数采用以下格式：`"ParameterName": { "value": "ParameterValue" }`。 以下示例显示带转义符的参数。

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- 使用参数文件。

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>后续步骤
* 有关通过 .NET 客户端库部署资源的示例，请参阅[使用 .NET 库和模板部署资源](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关将解决方案部署到不同环境的指南，请参阅 [Microsoft Azure 中的开发和测试环境](solution-dev-test-environments.md)。
* 有关使用 KeyVault 引用来传递安全值的详细信息，请参阅[在部署期间传递安全值](resource-manager-keyvault-parameter.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。
* 有关自动部署的四部分系列，请参阅[将应用程序自动部署到 Azure 虚拟机](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此系列介绍应用程序体系结构、访问和安全、可用性和缩放以及应用程序部署。




<!--HONumber=Jan17_HO2-->


