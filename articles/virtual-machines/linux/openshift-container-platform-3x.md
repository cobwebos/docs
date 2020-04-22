---
title: 在 Azure 中部署 OpenShift 容器平台 3.11
description: 在 Azure 中部署 OpenShift 容器平台 3.11。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757899"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平台 3.11

可以使用以下几种方法之一在 Azure 中部署 OpenShift 容器平台 3.11：

- 您可以手动部署必要的 Azure 基础结构组件，然后按照[OpenShift 容器平台文档](https://docs.openshift.com/container-platform)操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-container-platform/)，该模板可简化 OpenShift 容器平台群集的部署流程。
- 另一个选项是使用[Azure 应用商店产品/服务](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)。

所有选项均需要 Red Hat 订阅。 在部署期间，会将 Red Hat Enterprise Linux 实例注册到 Red Hat 订阅并附加到包含 OpenShift 容器平台权利的池 ID。
确保提供有效的 Red Hat 订阅管理员 (RHSM) 用户名、密码和池 ID。 可以使用激活密钥、组织 ID 和池 ID。 可通过登录 https://access.redhat.com 验证此信息。


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>使用 OpenShift 容器平台资源管理器 3.11 模板进行部署

### <a name="private-clusters"></a>专用群集

部署专用 OpenShift 群集需要的不仅仅是没有与主负载均衡器（Web 控制台）或红外负载均衡器（路由器）关联的公共 IP。  专用群集通常使用自定义 DNS 服务器（不是默认的 Azure DNS）、自定义域名（如contoso.com）和预定义的虚拟网络。  对于专用群集，您需要提前使用所有适当的子网和 DNS 服务器设置配置虚拟网络。  然后使用**现有的主子网参考**、**现有的InfraSubnet参考**、**现有的CnsSubnet参考**和**现有的NodeSubnet参考**来指定现有的子网供群集使用。

如果选择了私有主机（**主群集类型**=私有），则需要为**主私有群集Ip**指定静态专用 IP。  此 IP 将分配给主负载均衡器的前端。  IP 必须在主子网的 CIDR 内，并且不在使用中。  **主群集Dns类型**必须设置为"自定义"，并且必须为**主群集Dns**提供主 DNS 名称。  DNS 名称必须映射到静态专用 IP，并将用于访问主节点上的控制台。

如果选择了专用路由器（**路由器群集类型**=专用），则需要为**路由器专用群集Ip**指定静态专用 IP。  此 IP 将分配给红外负载均衡器的前端。  IP 必须在红外线网的 CIDR 内，并且未使用。  **路由子域类型**必须设置为"自定义"，并且必须为**路由提供**路由的通配符 DNS 名称。  

如果选择了专用主机和专用路由器，则还必须为**域名**输入自定义域名

成功部署后，堡垒节点是唯一具有公共 IP 的节点。该节点可以放入其中。  即使主节点配置为公共访问，也不会公开用于 ssh 访问。

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
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
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
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
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
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
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
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

请将参数替换为你的特定信息。

不同的版本可能具有不同的参数，因此，请验证所用分支的必要参数。

### <a name="azuredeployparametersjson-file-explained"></a>azure 部署。参数.json 文件解释

| 属性 | 说明 | 有效选项 | 默认值 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 项目 URL（json、脚本等） |  |  https：\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 要将资源部署到 |  |  |
| `masterVmSize` | 主 VM 的大小。 从 azuredeploy.json 文件中列出的允许的 VM 大小之一中进行选择 |  | Standard_E2s_v3 |
| `infraVmSize` | Infra VM 的大小。 从 azuredeploy.json 文件中列出的允许的 VM 大小之一中进行选择 |  | Standard_D4s_v3 |
| `nodeVmSize` | 应用节点 VM 的大小。 从 azuredeploy.json 文件中列出的允许的 VM 大小之一中进行选择 |  | Standard_D4s_v3 |
| `cnsVmSize` | 容器本机存储 （CNS） 节点 VM 的大小。 从 azuredeploy.json 文件中列出的允许的 VM 大小之一中进行选择 |  | Standard_E4s_v3 |
| `osImageType` | 要使用的 RHEL 映像。 默认库：按需;市场：第三方形象 | 默认库 <br> 市场 | 默认库 |
| `marketplaceOsImage` | 如果是`osImageType`市场，则输入市场报价的"发布者"、"优惠"、"sku"和"版本"的适当值。 此参数是对象类型 |  |  |
| `storageKind` | 要使用的存储类型  | 托管式<br> unmanaged | 托管式 |
| `openshiftClusterPrefix` | 用于配置所有节点的主机名的群集前缀。  1 到 20 个字符之间 |  | 我的集群 |
| `minoVersion` | 要部署的 OpenShift 容器平台 3.11 的次要版本 |  | 69 |
| `masterInstanceCount` | 要部署的主机节点数 | 1, 3, 5 | 3 |
| `infraInstanceCount` | 要部署的红外线节点数 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 要部署的节点数 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | 要部署的 CNS 节点数 | 3、4 | 3 |
| `osDiskSize` | VM OS 磁盘的大小（以 GB） | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | 要附加到 Docker 卷节点的数据磁盘的大小（以 GB 表示） | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | 要附加到 CNS 节点的数据磁盘大小，供 glusterfs 使用（以 GB 表示） | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | 管理两个操作系统 （VM） 登录名和初始 OpenShift 用户的管理员用户名 |  | 奥cpadmin |
| `enableMetrics` | 启用指标。 指标需要更多的资源，因此为 Infra VM 选择适当的大小 | true <br> false | false |
| `enableLogging` | 启用日志记录。 弹性搜索窗格需要 8 GB RAM，因此为 Infra VM 选择适当的大小 | true <br> false | false |
| `enableCNS` | 启用容器本机存储 | true <br> false | false |
| `rhsmUsernameOrOrgId` | 红帽订阅管理器用户名或组织 ID |  |  |
| `rhsmPoolId` | 包含计算节点的 OpenShift 权限的红帽订阅管理器池 ID |  |  |
| `rhsmBrokerPoolId` | 红帽订阅管理器池 ID，其中包含主节点和红外线节点的 OpenShift 权限。 如果您没有不同的池 ID，请输入与"rhsmPoolId"相同的池 ID |  |
| `sshPublicKey` | 在此处复制您的 SSH 公钥 |  |  |
| `keyVaultSubscriptionId` | 包含密钥保管库的订阅的订阅 ID |  |  |
| `keyVaultResourceGroup` | 包含密钥保管库的资源组的名称 |  |  |
| `keyVaultName` | 您创建的密钥保管库的名称 |  |  |
| `enableAzure` | 启用 Azure 云提供程序 | true <br> false | true |
| `aadClientId` | Azure 活动目录客户端 ID 也称为服务主体的应用程序 ID |  |  |
| `domainName` | 要使用的自定义域名的名称（如果适用）。 如果未部署完全专用群集，则设置为"无" |  | none |
| `masterClusterDnsType` | OpenShift Web 控制台的域类型。 "默认"将使用主红外公共 IP 的 DNS 标签。 "自定义"允许您定义自己的名称 | default <br> 自定义 | default |
| `masterClusterDns` | 自定义 DNS 名称用于访问 OpenShift Web 控制台（如果选择"自定义"）`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | 如果设置为"nipio"，`routingSubDomain`将使用nip.io。  如果您有自己的域要用于路由，请使用"自定义" | 尼皮奥 <br> 自定义 | 尼皮奥 |
| `routingSubDomain` | 如果选择"自定义"，则要用于路由的通配符 DNS 名称`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 选择是使用现有虚拟网络还是创建新的虚拟网络 | 现有 <br> 新 | 新 |
| `virtualNetworkResourceGroupName` | 如果为`virtualNetworkNewOrExisting` |  | 资源组（）名称 |
| `virtualNetworkName` | 选择"新"时要创建的新虚拟网络的名称`virtualNetworkNewOrExisting` |  | 开移vnet |
| `addressPrefixes` | 新虚拟网络的地址前缀 |  | 10.0.0.0/14 |
| `masterSubnetName` | 主子网的名称 |  | 主子网 |
| `masterSubnetPrefix` | 用于主子网的 CIDR - 需要是地址前缀的子集 |  | 10.1.0.0/16 |
| `infraSubnetName` | 红外线网的名称 |  | 红外线 |
| `infraSubnetPrefix` | 用于红外线网的 CIDR - 需要是地址前缀的子集 |  | 10.2.0.0/16 |
| `nodeSubnetName` | 节点子网的名称 |  | 节点子网 |
| `nodeSubnetPrefix` | 用于节点子网的 CIDR - 需要是地址前缀的子集 |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 主节点对现有子网的完整引用。 如果创建新的 vNet / 子网，则不需要 |  |  |
| `existingInfraSubnetReference` | 对红外线节点的现有子网的完整引用。 如果创建新的 vNet / 子网，则不需要 |  |  |
| `existingCnsSubnetReference` | 完全引用 CNS 节点的现有子网。 如果创建新的 vNet / 子网，则不需要 |  |  |
| `existingNodeSubnetReference` | 对计算节点的现有子网的完整引用。 如果创建新的 vNet / 子网，则不需要 |  |  |
| `masterClusterType` | 指定群集是使用专用节点还是公共主节点。 如果选择私有节点，主节点将不会通过公共 IP 向 Internet 公开。 相反，它将使用`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | 如果选择了专用主节点，则必须指定专用 IP 地址，供主节点的内部负载均衡器使用。 此静态 IP 必须位于主子网的 CIDR 块内，并且尚未使用。 如果选择了公共主节点，则不会使用此值，但仍必须指定 |  | 10.1.0.200 |
| `routerClusterType` | 指定群集是使用专用节点还是公共红外线节点。 如果选择私有，则红外线节点不会通过公共 IP 向 Internet 公开。 相反，它将使用`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | 如果选择了专用红外线节点，则必须指定专用 IP 地址，供内部负载均衡器用于红外线节点。 此静态 IP 必须位于红外线网的 CIDR 块内，并且尚未使用。 如果选择了公共红外线节点，则此值不会使用，但仍必须指定 |  | 10.2.0.200 |
| `routingCertType` | 将自定义证书用于路由域或默认自签名证书 - 按照**自定义证书**部分中的说明操作 | 自签名 <br> 自定义 | 自签名 |
| `masterCertType` | 对主域或默认自签名证书使用自定义证书 - 按照**自定义证书**部分中的说明操作 | 自签名 <br> 自定义 | 自签名 |

<br>

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 进行部署

> [!NOTE] 
> 以下命令需要 Azure CLI 2.0.8 或更高版本。 可以使用 `az --version` 命令检查 CLI 版本。 若要更新 CLI 版本，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)。

以下示例使用名为 myOpenShiftCluster 的部署将 OpenShift 群集和所有相关资源部署到名为 openshiftrg 的资源组中。 它直接从 GitHub 存储库引用模板并使用名为 azuredeploy.parameters.json 的本地参数文件。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

根据部署的节点总数和配置的选项，部署至少需要 60 分钟才能完成。 部署完成时，终端上会输出 OpenShift 控制台的守护主机 DNS FQDN 和 URL。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

如果不想在命令行上等着部署完成，请将 `--no-wait` 添加为组部署的选项之一。 可以在 Azure 门户中资源组的部署部分检索部署的输出。

## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，从部署输出部分检索连接。 使用**OpenShift 控制台 URL**使用浏览器连接到 OpenShift 控制台。 您还可以 SSH 到堡垒主机。 在以下示例中，管理员用户名为 clusteradmin，守护主机的公共 IP DNS FQDN 为 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-container-platform-3x-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-container-platform-3x-troubleshooting.md)
- [使用 OpenShift 容器平台入门](https://docs.openshift.com)
