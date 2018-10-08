---
title: 部署 Azure 虚拟网络容器网络 | Microsoft Docs
description: 了解如何为自行部署的 Kubernetes 群集或者使用 ACS-Engine 部署的 Kubernetes 群集，以及为 Docker 容器来部署 Azure 虚拟网络容器网络接口 (CNI) 插件。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 62d19432cba431bce4485aaa2af3e0a23ad8b5f6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970968"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>部署 Azure 虚拟网络容器网络接口插件

Azure 虚拟网络容器网络接口 (CNI) 插件安装在 Azure 的虚拟机中，并为 Kubernetes Pod 和 Docker 容器提供虚拟网络功能。 要了解有关该插件的详细信息，请参阅[启用容器以使用 Azure 虚拟网络功能](container-networking-overview.md)。 此外，通过选择[高级网络](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)选项，该插件可用于 Azure Kubernetes 服务 (AKS)，高级网络选项自动将 AKS 容器放置在虚拟网络中。

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>为 ACS-Engine Kubernetes 群集部署插件

ACS-Engine 使用 Azure 资源管理器模板部署 Kubernetes 群集。 群集配置在 JSON 文件中指定，该文件在生成模板时传递给工具。 要详细了解受支持的群集设置及其说明的完整列表，请参阅 [Microsoft Azure 容器服务引擎 - 群集定义](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md)。 该插件是使用 ACS-Engine 创建的集群的默认网络插件。 配置插件时，以下网络配置设置非常重要：

  | 设置                              | Description                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | 分配给主节点的 IP 地址。 这是必需设置。                                     |
  | kubernetesConfig 下的 clusterSubnet | 在其中部署集群并将 IP 地址分配给 Pod 的虚拟网络子网的 CIDR   |
  | masterProfile 下的 vnetSubnetId     | 指定在其中部署群集的子网的 Azure 资源管理器资源 ID                    |
  | vnetCidr                             | 在其中部署集群的虚拟网络的 CIDR                                                             |
  | kubeletConfig 下的 max-Pod         | 每个代理虚拟机上的 Pod 的最大数量。 对于插件，默认值为 30。 最多可以指定 250 个  |

### <a name="example-configuration"></a>示例配置

下面的 json 示例适用于具有以下属性的群集：
-   1 个主节点和 2 个代理节点 
-   部署在名为 KubeClusterSubnet (10.0.0.0/20) 的子网中，主节点和代理节点都驻留其中。

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>为 Kubernetes 群集部署插件

完成以下步骤以在 Kubernetes 群集中的每个 Azure 虚拟机上安装插件：

1. [下载并安装插件](#download-and-install-the-plug-in)。
2. 在每个虚拟机上预分配虚拟网络 IP 地址池，IP 地址从中分配给 Pod。 每个 Azure 虚拟机在每个网络接口上都附带一个主虚拟网络专用 IP 地址。 Pod 的 IP 地址池将作为辅助地址 (ipconfigs) 添加到虚拟机网络接口上，方法是使用以下某个选项：

   - **CLI**：[使用 Azure CLI 分配多个 IP 地址](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**：[使用 PowerShell 分配多个 IP 地址](virtual-network-multiple-ip-addresses-powershell.md)
   - **门户**：[使用 Azure 门户分配多个 IP 地址](virtual-network-multiple-ip-addresses-portal.md)
   - **Azure 资源管理器模板**：[使用模板分配多个 IP 地址](virtual-network-multiple-ip-addresses-template.md)

   确保为你希望在虚拟机上出现的所有 Pod 添加足够的 IP 地址。

3. 通过在群集创建期间向 Kubelet 传递 `–network-plugin=cni` 命令行选项，选择用于为群集提供网络的插件。 默认情况下，Kubernetes 在已安装插件和配置文件的目录中查找它们。
4. 如果希望 Pod 可以访问互联网，请在 Linux 虚拟机上添加以下 iptables 规则，对 Internet 流量进行源 NAT。 在以下示例中，指定的 IP 范围是 10.0.0.0/8。

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   该规则对未流向指定 IP 范围的流量进行 NAT。 假设以前范围之外的所有流量都是 Internet 流量。 可以选择指定虚拟机的虚拟网络、对等虚拟网络和本地网络的 IP 范围。

  Windows 虚拟机自动为流往虚拟机所属子网范围之外的流量进行源 NAT。 无法指定自定义 IP 范围。

完成前面的步骤后，将自动从虚拟网络向 Kubernetes 代理虚拟机上出现的 Pod 分配专用 IP 地址。

## <a name="deploy-plug-in-for-docker-containers"></a>为 Docker 容器部署插件

1. [下载并安装插件](#download-and-install-the-plug-in)。
2. 使用以下命令创建 Docker 容器：

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

容器自动开始从分配的池中接收 IP 地址。 如果要对流向 Docker 容器的流量进行负载均衡，则必须将 Docker 容器放在软件负载均衡器后面，并且必须配置负载均衡器探测，如同为虚拟机创建策略和探测一样。

### <a name="cni-network-configuration-file"></a>CNI 网络配置文件

CNI 网络配置文件以 JSON 格式描述。 默认情况下，它出现在 `/etc/cni/net.d`（对于 Linux）和 `c:\cni\netconf`（对于 Windows）中。 该文件指定插件的配置且在 Windows 和 Linux 中不同。 下面的 json 是示例 Linux 配置文件，后面是一些关键设置的说明。 无需对文件进行任何更改：

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>设置说明

- **cniVersion**：Azure 虚拟网络 CNI 插件支持 [CNI 规范](https://github.com/containernetworking/cni/blob/master/SPEC.md)的 0.3.0 和 0.3.1版本。
- **名称**：网络的名称。 此属性可以设置为任何唯一值。
- **类型**：网络插件的名称。 设置为 azure vnet。
- **模式**：操作模式。 此字段可选。 支持的唯一模式是“桥接”。 有关更多详细信息，请参阅[操作模式](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md)。
- **桥**：将用于将容器连接到虚拟网络的桥的名称。 此字段可选。 如果省略，则插件会根据主接口索引自动选择唯一名称。
- **ipam 类型**：IPAM 插件的名称。 始终设置为 azure vnet ipam。

## <a name="download-and-install-the-plug-in"></a>下载并安装插件

从 [GitHub](https://github.com/Azure/azure-container-networking/releases) 下载插件。 下载所使用的平台的最新版本：

- **Linux**：[azure-vnet-cni-linux-amd64-\<版本号\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**：[azure-vnet-cni-windows-amd64-\<版本号\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

将 [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) 或 [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) 的安装脚本复制到计算机。 将脚本保存到计算机上的 `scripts` 目录，对于 Linux，将文件命名为 `install-cni-plugin.sh`；对于 Windows，将文件命名为 `install-cni-plugin.ps1`。 要安装插件，请为你的平台运行相应的脚本，指定正在使用的插件的版本。 例如，可以指定 v1.0.12-rc3：

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

针对 Linux，该脚本在 `/opt/cni/bin` 下安装插件；针对 Windows，该脚本在 `c:\cni\bin` 下安装插件。 安装的插件附带简单的网络配置文件，该配置文件可在安装后运行。 它不需要更新。 要了解有关文件中设置的详细信息，请参阅 [CNI 网络配置文件](#cni-network-configuration-file)。