---
title: "在 Azure 中部署 OpenShift 容器平台 | Microsoft Docs"
description: "在 Azure 中部署 OpenShift 容器平台。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 81d1e2a92a24d43c6324b4fe026680c379e656da
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>在 Azure 中部署 OpenShift 容器平台

可以使用多种方法之一在 Azure 中部署 OpenShift 容器平台：

- 可以手动部署必要的 Azure 基础结构组件，然后按照 OpenShift 容器平台[文档](https://docs.openshift.com/container-platform/3.6/welcome/index.html)进行操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-container-platform/)，该模板可简化 OpenShift 容器平台群集的部署流程。
- 另一种做法是使用 [Azure Marketplace 产品](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)。

所有选项均需要 Red Hat 订阅。 在部署期间，会将 Red Hat Enterprise Linux 实例注册到 Red Hat 订阅并附加到包含 OpenShift 容器平台权利的池 ID。
确保具有有效的 Red Hat 订阅管理员 (RHSM) 用户名、密码和池 ID。 可通过登录 https://access.redhat.com 验证此信息。

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>使用 OpenShift 容器平台资源管理器模板进行部署

要使用资源管理器模板进行部署，需使用参数文件提供输入参数。 若要使用输入参数自定义任何未涵盖的部署项，请将 GitHub 存储库进行分叉并更改相应的项。

一些常见的自定义选项包括但不限于：

- 虚拟网络 CIDR（azuredeploy.json 中的变量）
- 守护 VM 大小（azuredeploy.json 中的变量）
- 命名约定（azuredeploy.json 中的变量）
- OpenShift 群集详细信息，通过主机文件修改 (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>配置参数文件

为 `aadClientId` 参数使用前面创建的服务主体中的 `appId` 值。 

以下示例使用所有必需的输入创建一个名为 azuredeploy.parameters.json 的参数文件。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
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

将大括号中的项替换为具体的信息。

### <a name="deploy-by-using-azure-cli"></a>使用 Azure CLI 进行部署

> [!NOTE] 
> 以下命令需要 Azure CLI 2.0.8 或更高版本。 可以使用 `az --version` 命令检查 CLI 版本。 若要更新 CLI 版本，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)。

以下示例使用名为 myOpenShiftCluster 的部署将 OpenShift 群集和所有相关资源部署到名为 myResourceGroup 的资源组。 它直接从 GitHub 存储库引用模板并使用名为 azuredeploy.parameters.json 的本地参数文件。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

该部署至少需要 30 分钟才能完成，具体取决于部署的节点总数。 部署完成后，终端中会列显 OpenShift 控制台的 URL，以及 OpenShift 主设备的 DNS 名称。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>使用 OpenShift 容器平台 Azure Marketplace 产品进行部署

使用 [Azure Marketplace 产品](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)是将 OpenShift 容器平台部署到 Azure 中最简单的方式。

此方法虽然最为简单，但自定义功能有限。 产品/服务包括 3 个配置选项：

- **小型**：使用 1 个主节点、1 个基础结构节点、2 个应用程序节点和 1 个守护节点部署非高可用性 (HA) 群集。 所有节点均为标准 DS2v2 VM 大小。 此群集总共需要 10 个内核，十分适合小型规模测试。
- **中型**：使用 3 个主节点、2 个基础结构节点、4 个应用程序节点和 1 个守护节点部署 HA 群集。 所有节点（守护节点除外）均为标准 DS3v2 VM 大小。 守护节点为标准 DS2v2。 此群集需要 38 个内核。
- **大型**：使用 3 个主节点、2 个基础结构节点、6 个应用程序节点和 1 个守护节点部署 HA 群集。 主节点和基础结构节点为标准 DS3v2 VM 大小。 应用程序节点为标准 DS4v2 VM 大小，守护节点为标准 DS2v2。 此群集需要 70 个内核。

对于中等和大型群集大小，提供配置 Azure 云解决方案提供程序的选项。 对于小型群集大小，则不提供配置 Azure 云解决方案提供程序的选项。

## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，可在浏览器中使用 `OpenShift Console Uri` 连接到OpenShift 控制台。 或者，可使用以下命令连接到 OpenShift 主设备：

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group#delete) 命令将其删除。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-troubleshooting.md)
- [OpenShift 容器平台入门](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
