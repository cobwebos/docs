---
title: 在 Azure 中部署 OKD | Microsoft Docs
description: 在 Azure 中部署 OKD。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: 7db50007dd32c84a360eaec25bf860709272437b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999065"
---
# <a name="deploy-okd-in-azure"></a>在 Azure 中部署 OKD

可使用两种方式之一在 Azure 中部署 OKD（以前称为 OpenShift Origin）：

- 可以手动部署所有必要的 Azure 基础结构组件，然后按照 [OKD 文档](https://docs.okd.io)进行操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-origin)，该模板可简化 OKD 群集的部署。

## <a name="deploy-using-the-okd-template"></a>使用 OKD 模板进行部署

若要使用资源管理器模板进行部署，需要使用参数文件提供输入参数。 若要进一步自定义部署，请将 GitHub 存储库进行分支并更改相应的项。

一些常见的自定义选项包括但不限于：

- 守护 VM 大小（azuredeploy.json 中的变量）
- 命名约定（azuredeploy.json 中的变量）
- OpenShift 群集详细信息，通过主机文件修改 (deployOpenShift.sh)

[OKD 模板](https://github.com/Microsoft/openshift-origin)具有可用于不同 OKD 版本的多个分支。  根据你的需求，可以直接从存储库进行部署，也可以将存储库进行分支，并在部署前进行自定义更改。

对 `aadClientId` 参数使用前面创建的服务主体中的 `appId` 值。

下面是一个名为 azuredeploy.parameters.json 的参数文件示例，其中包含了所有必需的输入。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

将参数替换为你的特定信息。

不同的版本可能具有不同的参数，因此，请验证你使用的分支的必需参数。

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 进行部署


> [!NOTE] 
> 以下命令需要 Azure CLI 2.0.8 或更高版本。 可以使用 `az --version` 命令检查 CLI 版本。 若要更新 CLI 版本，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

以下示例使用名为 myOpenShiftCluster 的部署将 OKD 群集和所有相关资源部署到名为 openshiftrg 的资源组中。 它直接从 GitHub 存储库引用模板，同时使用名为 azuredeploy.parameters.json 的本地参数文件。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

该部署至少需要 30 分钟才能完成，具体取决于部署的节点总数。 部署完成后，终端中会列显 OpenShift 控制台的 URL，以及 OpenShift 主设备的 DNS 名称。 另外，还可以从 Azure 门户查看部署的输出部分。

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

如果不想在命令行上呆着等待部署完成，请将 `--no-wait` 添加为组部署的选项之一。 可以在 Azure 门户中资源组的部署部分检索部署的输出。

## <a name="connect-to-the-okd-cluster"></a>连接至 OKD 群集

在部署完成后，在浏览器中使用 `OpenShift Console Url` 连接到OpenShift 控制台。 此外，也可以使用 SSH 连接到 OKD 主机。 下面是一个示例，它使用来自部署的输出：

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-post-deployment.md)
- [OpenShift 部署故障排除](./openshift-troubleshooting.md)
- [OKD 入门](https://docs.okd.io)
