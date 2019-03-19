---
title: 在 Azure 中部署 OpenShift 容器平台 | Microsoft Docs
description: 在 Azure 中部署 OpenShift 容器平台。
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
ms.date: 02/04/2018
ms.author: haroldw
ms.openlocfilehash: 1d869d822cdeb0051836a5fc5f01eb69c523f9e3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995551"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>在 Azure 中部署 OpenShift 容器平台

可以使用多种方法之一在 Azure 中部署 OpenShift 容器平台：

- 可以手动部署必要的 Azure 基础结构组件，然后按照 [OpenShift 容器平台文档](https://docs.openshift.com/container-platform)操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-container-platform/)，该模板可简化 OpenShift 容器平台群集的部署流程。
- 另一种做法是使用 [Azure 市场产品](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)。

所有选项均需要 Red Hat 订阅。 在部署期间，会将 Red Hat Enterprise Linux 实例注册到 Red Hat 订阅并附加到包含 OpenShift 容器平台权利的池 ID。
确保提供有效的 Red Hat 订阅管理员 (RHSM) 用户名、密码和池 ID。 可以使用激活密钥、组织 ID 和池 ID。 可通过登录 https://access.redhat.com 验证此信息。

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>使用 OpenShift 容器平台资源管理器模板进行部署

若要使用资源管理器模板进行部署，需要使用参数文件提供输入参数。 若要进一步自定义部署，请将 GitHub 存储库进行分支并更改相应的项。

一些常见的自定义选项包括但不限于：

- 守护 VM 大小（azuredeploy.json 中的变量）
- 命名约定（azuredeploy.json 中的变量）
- OpenShift 群集详细信息，通过主机文件修改 (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>配置参数文件

[OpenShift 容器平台模板](https://github.com/Microsoft/openshift-container-platform)提供适用于不同 OpenShift 容器平台版本的多个分支。  根据需求，可以直接从存储库进行部署，也可以将存储库分叉，并在部署之前对模板或脚本进行自定义更改。

为 `aadClientId` 参数使用前面创建的服务主体中的 `appId` 值。

以下示例演示如何使用全部所需的输入创建名为 azuredeploy.parameters.json 的参数文件。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
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
        "rhsmBrokerPoolId": {
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
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

请将参数替换为你的特定信息。

不同的版本可能具有不同的参数，因此，请验证所用分支的必要参数。

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 进行部署

> [!NOTE] 
> 以下命令需要 Azure CLI 2.0.8 或更高版本。 可以使用 `az --version` 命令检查 CLI 版本。 若要更新 CLI 版本，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)。

以下示例使用名为 myOpenShiftCluster 的部署将 OpenShift 群集和所有相关资源部署到名为 openshiftrg 的资源组中。 它直接从 GitHub 存储库引用模板并使用名为 azuredeploy.parameters.json 的本地参数文件。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

该部署至少需要 30 分钟才能完成，具体取决于部署的节点总数和配置的选项。 部署完成时，终端上会输出 OpenShift 控制台的守护主机 DNS FQDN 和 URL。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

如果不想在命令行上等着部署完成，请将 `--no-wait` 添加为组部署的选项之一。 可以在 Azure 门户中资源组的部署部分检索部署的输出。
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>使用 OpenShift 容器平台 Azure 市场套餐进行部署

使用 [Azure 市场产品/服务](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)是将 OpenShift Container Platform 部署到 Azure 中的最简单方式。

此方法虽然最为简单，但自定义功能有限。 市场套餐包括以下配置选项：

- **主节点**：三 (3) 个包含可配置实例类型的主节点。
- **Infra 节点**：三 (3) 个包含可配置实例类型的 Infra 节点。
- **节点**：节点数目可配置（2 到 9 个），实例类型也可配置。
- **磁盘类型**：使用托管磁盘。
- **网络**：支持新的或现有的网络以及自定义 CIDR 范围。
- **CNS**：可以启用 CNS。
- **指标**：可以启用指标。
- **日志记录**：可以启用日志记录。
- **Azure 云提供商**：可以启用。

## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，从部署输出部分检索连接。 在浏览器中使用 `OpenShift Console URL` 连接到OpenShift 控制台。 或者，也可以使用 SSH 连接到守护主机。 在以下示例中，管理员用户名为 clusteradmin，守护主机的公共 IP DNS FQDN 为 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-troubleshooting.md)
- [OpenShift 容器平台入门](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>文档贡献者

感谢 Vincent Power (vincepower) 和 Alfred Sin (asinn826) 为此文档的更新所做的贡献！
