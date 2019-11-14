---
title: 在 Azure 中部署 OpenShift 容器平台3.11
description: 在 Azure 中部署 OpenShift 容器平台3.11。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 56607de57939be769b1951f0eee9078c46d610c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035449"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平台3.11

你可以使用以下几种方法之一在 Azure 中部署 OpenShift 容器平台3.11：

- 可以手动部署必要的 Azure 基础结构组件，然后按照 [OpenShift 容器平台文档](https://docs.openshift.com/container-platform)操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-container-platform/)，该模板可简化 OpenShift 容器平台群集的部署流程。
- 另一种做法是使用 [Azure 市场产品](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)。

所有选项均需要 Red Hat 订阅。 在部署期间，会将 Red Hat Enterprise Linux 实例注册到 Red Hat 订阅并附加到包含 OpenShift 容器平台权利的池 ID。
确保提供有效的 Red Hat 订阅管理员 (RHSM) 用户名、密码和池 ID。 可以使用激活密钥、组织 ID 和池 ID。 可通过登录 https://access.redhat.com 验证此信息。


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>使用 OpenShift 容器平台资源管理器3.11 模板进行部署

### <a name="private-clusters"></a>专用群集

部署私有 OpenShift 群集只需使用与主负载均衡器（web 控制台）关联的公共 IP，或将其部署到基础负载平衡器（路由器）。  专用群集通常使用自定义 DNS 服务器（而非默认 Azure DNS）、自定义域名（如 contoso.com）和预定义虚拟网络。  对于专用群集，需要事先为虚拟网络配置所有适当的子网和 DNS 服务器设置。  然后，使用**existingMasterSubnetReference**、 **existingInfraSubnetReference**、 **existingCnsSubnetReference**和**existingNodeSubnetReference**指定群集使用的现有子网。

如果选择了 "私有主机" （**masterClusterType**= private），则需要为**masterPrivateClusterIp**指定静态专用 IP。  此 IP 将分配给主负载均衡器的前端。  IP 必须在子网的 CIDR 内且不在使用中。  必须将**masterClusterDnsType**设置为 "custom"，并且必须为**MASTERCLUSTERDNS**提供主 DNS 名称。  DNS 名称必须映射到静态专用 IP，并将用于访问主节点上的控制台。

如果选择了专用路由器（**routerClusterType**= private），则需要为**routerPrivateClusterIp**指定静态专用 IP。  此 IP 将分配给基础负载均衡器的前端。  IP 必须在子网的 CIDR 内且不在使用中。  必须将**routingSubDomainType**设置为 "custom"，并且必须为**routingSubDomain**提供用于路由的通配符 DNS 名称。  

如果选择了 "专用主机" 和 "专用路由器"，则还必须为**domainName**输入自定义域名

成功部署后，堡垒节点是具有可通过 ssh 连接到的公共 IP 的唯一节点。  即使主节点配置为公共访问权限，它们也不会公开用于 ssh 访问。

若要使用资源管理器模板进行部署，需要使用参数文件提供输入参数。 若要进一步自定义部署，请将 GitHub 存储库进行分支并更改相应的项。

一些常见的自定义选项包括但不限于：

- 守护 VM 大小（azuredeploy.json 中的变量）
- 命名约定（azuredeploy.json 中的变量）
- OpenShift 群集详细信息，通过主机文件修改 (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>配置参数文件

[OpenShift 容器平台模板](https://github.com/Microsoft/openshift-container-platform)提供适用于不同 OpenShift 容器平台版本的多个分支。  根据需求，可以直接从存储库进行部署，也可以将存储库分叉，并在部署之前对模板或脚本进行自定义更改。

为 `appId` 参数使用前面创建的服务主体中的 `aadClientId` 值。

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

将参数替换为你的特定信息。

不同的版本可能具有不同的参数，因此，请验证所用分支的必要参数。

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.json.已解释的参数 json 文件

| 属性 | 说明 | 有效选项 | 默认值 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 项目的 URL （json、脚本等） |  |  https：\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 要将资源部署到的 Azure 区域 |  |  |
| `masterVmSize` | 主 VM 的大小。 选择 azuredeploy.json 文件中列出的允许 VM 大小之一 |  | Standard_E2s_v3 |
| `infraVmSize` | 基础 VM 的大小。 选择 azuredeploy.json 文件中列出的允许 VM 大小之一 |  | Standard_D4s_v3 |
| `nodeVmSize` | 应用节点 VM 的大小。 选择 azuredeploy.json 文件中列出的允许 VM 大小之一 |  | Standard_D4s_v3 |
| `cnsVmSize` | 容器本机存储（CNS）节点 VM 的大小。 选择 azuredeploy.json 文件中列出的允许 VM 大小之一 |  | Standard_E4s_v3 |
| `osImageType` | 要使用的 RHEL 映像。 defaultgallery：按需;marketplace：第三方映像 | defaultgallery <br> 市场 | defaultgallery |
| `marketplaceOsImage` | 如果 `osImageType` 是 marketplace，则输入 marketplace 产品/服务的 "发布者"、"产品/服务"、"sku" 和 "版本" 的相应值。 此参数是对象类型 |  |  |
| `storageKind` | 要使用的存储类型  | 托管式<br> 无 | 托管式 |
| `openshiftClusterPrefix` | 用于配置所有节点的主机名的群集前缀。  介于1到20个字符之间 |  | mycluster |
| `minoVersion` | 要部署的 OpenShift 容器平台3.11 的次版本 |  | 69 |
| `masterInstanceCount` | 要部署的主控节点数 | 1、3、5 | 3 |
| `infraInstanceCount` | 要部署的基础节点数 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 要部署的节点数 | 1、2、3、4、5、6、7、8、9、10、11、12、13、14、15、16、17、18、19、20、21、22、23、24、25、26、27、28、29、30 | 2 |
| `cnsInstanceCount` | 要部署的 CNS 节点数 | 3、4 | 3 |
| `osDiskSize` | VM 的 OS 磁盘大小（GB） | 64、128、256、512、1024、2048 | 64 |
| `dataDiskSize` | 要附加到 Docker 卷的节点的数据磁盘大小（以 GB 为限） | 32、64、128、256、512、1024、2048 | 64 |
| `cnsGlusterDiskSize` | 要附加到 CNS 节点以供 glusterfs 使用的数据磁盘的大小（GB | 32、64、128、256、512、1024、2048 | 128 |
| `adminUsername` | OS （VM）登录名和初始 OpenShift 用户的管理员用户名 |  | ocpadmin |
| `enableMetrics` | 启用指标。 指标需要更多的资源，因此请为基础 VM 选择合适的大小 | 是 <br> false | false |
| `enableLogging` | 启用日志记录。 elasticsearch pod 需要 8 GB RAM，因此请为基础 VM 选择合适的大小 | 是 <br> false | false |
| `enableCNS` | 启用容器本机存储 | 是 <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat 订阅管理器用户名或组织 ID |  |  |
| `rhsmPoolId` | 包含计算节点 OpenShift 权利的 Red Hat 订阅管理器池 ID |  |  |
| `rhsmBrokerPoolId` | Red Hat 订阅管理器池 ID，其中包含用于主机和基础节点的 OpenShift 权利。 如果没有其他池 Id，请输入与 "rhsmPoolId" 相同的池 ID |  |
| `sshPublicKey` | 在此处复制 SSH 公钥 |  |  |
| `keyVaultSubscriptionId` | 包含 Key Vault 的订阅的订阅 ID |  |  |
| `keyVaultResourceGroup` | 包含 Key Vault 的资源组的名称 |  |  |
| `keyVaultName` | 创建的 Key Vault 的名称 |  |  |
| `enableAzure` | 启用 Azure 云提供程序 | 是 <br> false | 是 |
| `aadClientId` | Azure Active Directory 客户端 ID 也称为服务主体的应用程序 ID |  |  |
| `domainName` | 要使用的自定义域名的名称（如果适用）。 如果不部署完全专用群集，则设置为 "无" |  | 无 |
| `masterClusterDnsType` | OpenShift web 控制台的域类型。 "默认" 将使用主基础公共 IP 的 DNS 标签。 "自定义" 允许您定义自己的名称 | default <br> 客户 | default |
| `masterClusterDns` | 如果为 `masterClusterDnsType` 选择了 "自定义"，则用于访问 OpenShift web 控制台的自定义 DNS 名称 |  | console.contoso.com |
| `routingSubDomainType` | 如果设置为 "nipio"，`routingSubDomain` 将使用 nip.io。  如果你有要用于路由的自己的域，请使用 "自定义" | nipio <br> 客户 | nipio |
| `routingSubDomain` | 如果为 `routingSubDomainType` 选择了 "自定义"，则要用于路由的通配符 DNS 名称 |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 选择是使用现有虚拟网络还是新建虚拟网络 | 现存 <br> 新的 | 新的 |
| `virtualNetworkResourceGroupName` | 如果为 `virtualNetworkNewOrExisting` 选择了 "新建"，则为新虚拟网络的资源组名称 |  | resourceGroup （）. 名称 |
| `virtualNetworkName` | 如果为 `virtualNetworkNewOrExisting` 选择了 "新建"，要创建的新虚拟网络的名称 |  | openshiftvnet |
| `addressPrefixes` | 新虚拟网络的地址前缀 |  | 10.0.0.0/14 |
| `masterSubnetName` | 子网的名称 |  | mastersubnet |
| `masterSubnetPrefix` | 用于子网的 CIDR-需要是 addressPrefix 的子集 |  | 10.1.0.0/16 |
| `infraSubnetName` | 基础子网的名称 |  | infrasubnet |
| `infraSubnetPrefix` | 用于基础子网的 CIDR-需要是 addressPrefix 的子集 |  | 10.2.0.0/16 |
| `nodeSubnetName` | 节点子网的名称 |  | nodesubnet |
| `nodeSubnetPrefix` | 用于节点子网的 CIDR-需要是 addressPrefix 的子集 |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 主节点的现有子网的完整引用。 如果创建新的 vNet/子网，则不需要 |  |  |
| `existingInfraSubnetReference` | 对基础节点的现有子网的完整引用。 如果创建新的 vNet/子网，则不需要 |  |  |
| `existingCnsSubnetReference` | 对 CNS 节点的现有子网的完整引用。 如果创建新的 vNet/子网，则不需要 |  |  |
| `existingNodeSubnetReference` | 完全引用计算节点的现有子网。 如果创建新的 vNet/子网，则不需要 |  |  |
| `masterClusterType` | 指定群集是使用专用还是公用主节点。 如果选择了 "专用"，则不会通过公共 IP 向 Internet 公开主节点。 相反，它将使用 `masterPrivateClusterIp` 中指定的专用 IP | 公共 <br> 专用 | 公共 |
| `masterPrivateClusterIp` | 如果选择了 "专用主节点"，则必须指定专用 IP 地址以供主节点的内部负载均衡器使用。 此静态 IP 必须在子网的 CIDR 块中，但尚未使用。 如果选择了公共主节点，则不会使用此值，但仍必须指定该值 |  | 10.1.0.200 |
| `routerClusterType` | 指定群集是使用专用节点还是公共基础节点。 如果选择 "专用"，则不会通过公共 IP 向 Internet 公开基础节点。 相反，它将使用 `routerPrivateClusterIp` 中指定的专用 IP | 公共 <br> 专用 | 公共 |
| `routerPrivateClusterIp` | 如果选择了 "专用" 基础节点，则必须指定专用 IP 地址以供基础节点的内部负载均衡器使用。 此静态 IP 必须在子网的 CIDR 块中，但尚未使用。 如果选择了公共基础节点，则不会使用此值，但仍必须指定。 |  | 10.2.0.200 |
| `routingCertType` | 使用自定义证书来路由域或默认的自签名证书-按照 "**自定义证书**" 部分中的说明操作 | selfsigned <br> 客户 | selfsigned |
| `masterCertType` | 为主域或默认的自签名证书使用自定义证书-按照**自定义证书**部分中的说明进行操作 | selfsigned <br> 客户 | selfsigned |

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

部署至少需要60分钟才能完成，具体取决于部署的节点总数和配置的选项。 部署完成时，终端上会输出 OpenShift 控制台的守护主机 DNS FQDN 和 URL。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

如果不想在命令行上呆着等待部署完成，请将 `--no-wait` 添加为组部署的选项之一。 可以从 Azure 门户中在资源组的部署部分中检索部署的输出。

## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，从部署输出部分检索连接。 使用**OpenShift 控制台 URL**，通过浏览器连接到 OpenShift 控制台。 还可以通过 SSH 连接到堡垒主机。 在以下示例中，管理员用户名为 clusteradmin，守护主机的公共 IP DNS FQDN 为 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

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
- [OpenShift 容器平台入门](https://docs.openshift.com)
