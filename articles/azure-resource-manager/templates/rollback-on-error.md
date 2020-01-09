---
title: 出错时回滚以成功部署
description: 指定失败的部署应回滚到成功的部署。
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 32ba5485e1980eb819bf5429fbfbb597dfe75c2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484293"
---
# <a name="rollback-on-error-to-successful-deployment"></a>发生错误时回滚到成功部署

部署失败时，可以自动重新部署部署历史记录中先前成功的部署。 如果你已经为基础结构部署提供了已知的良好状态，并且想要恢复到此状态，则此功能非常有用。 有一些注意事项和限制：

- 重新部署的运行与以前用相同参数运行的重新部署完全相同。 不能更改参数。
- 先前的部署是使用[完整模式](./deployment-modes.md#complete-mode)运行的。 将删除以前的部署中未包含的任何资源，并将所有资源配置设置为以前的状态。 请确保完全了解[部署模式](./deployment-modes.md)。
- 重新部署只会影响资源，任何数据更改不会受到影响。
- 此功能只能用于资源组部署，不能与订阅或管理组级别部署一起使用。 有关订阅级别部署的详细信息，请参阅[在订阅级别创建资源组和资源](./deploy-to-subscription.md)。
- 只能将此选项用于根级别部署。 从嵌套模板进行的部署不可用于重新部署。

若要使用此选项，部署必须具有唯一的名称，以便可以在历史记录中标识它们。 如果没有唯一名称，则当前失败的部署可能会覆盖历史记录中以前成功的部署。

## <a name="powershell"></a>PowerShell

若要重新部署最后一个成功的部署，请将 `-RollbackToLastDeployment` 参数添加为标志。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

若要重新部署某个特定部署，请使用 `-RollBackDeploymentName` 参数并提供部署名称。 指定的部署必须已成功。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

若要重新部署最后一个成功的部署，请将 `--rollback-on-error` 参数添加为标志。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

若要重新部署某个特定部署，请使用 `--rollback-on-error` 参数并提供部署名称。 指定的部署必须已成功。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

若要在当前部署失败的情况下，重新部署上一个成功部署，请使用：

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

若要在当前部署失败的情况下，重新部署特定部署，请使用：

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

指定的部署必须已成功。

## <a name="next-steps"></a>后续步骤

- 若要安全地将服务扩展到多个区域，请参阅 [Azure 部署管理器](deployment-manager-overview.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。
