---
title: 在 Azure Kubernetes 服务 (AKS) 中自定义用户定义路由 (UDR)
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中定义自定义流出量路由
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: babfd70a6a9732113531be13073af212a6820557
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677887"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>使用用户定义的路由自定义群集流出量（预览）

可以自定义 AKS 群集的流出量，以适应特定方案。 默认情况下，AKS 将预配一个标准 SKU 负载均衡器，使其用于流出量。 但是，如果不允许公共 IP，或者流出量需要其他跃点，则默认设置可能无法满足所有方案的要求。

本文介绍如何自定义群集的流出量路由以支持自定义网络方案，例如不允许使用公共 IP 并要求群集位于网络虚拟设备 (NVA) 后面的那些方案。

> [!IMPORTANT]
> AKS 预览功能是自助服务，可基于选择进行提供。 预览功能是“按现状”和“按可用”提供的，不包括在服务级别协议 (SLA) 和有限保证中 。 AKS 预览功能由客户支持尽最大努力部分覆盖。 因此，这些功能并不适合用于生产。 有关详细信息，请参阅以下支持文章：
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="prerequisites"></a>先决条件
* Azure CLI 2.0.81 或更高版本
* Azure CLI 预览版扩展 0.4.28 或更高版本
* API `2020-01-01` 版或更高版本

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>安装最新的 Azure CLI AKS 预览版扩展
若要设置群集的出站类型，需要 Azure CLI AKS 预览版扩展 0.4.18 或更高版本。 使用 az extension add 命令安装 Azure CLI AKS 预览版扩展，然后使用以下 az extension update 命令检查是否有任何可用的更新：

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>限制
* 在预览期间，`outboundType` 只能在创建群集时定义，以后无法更新。
* 在预览期间，`outboundType` AKS 群集应使用 Azure CNI。 Kubenet 是可配置的，使用时需要将路由表手动关联到 AKS 子网。
* 设置 `outboundType` 要求 AKS 群集具有 `vm-set-type` 的 `VirtualMachineScaleSets` 和 `Standard` 的 `load-balancer-sku`。
* 将 `outboundType` 设置为 `UDR` 的值需要用户定义的路由，该路由具有群集的有效出站连接。
* 如果将 `outboundType` 设置为 `UDR` 的值，则意味着路由到负载均衡器的流入量源 IP 可能与群集的流出量目标地址不匹配。

## <a name="overview-of-outbound-types-in-aks"></a>AKS 中的出站类型概述

可以使用唯一的 `outboundType` 类型的负载均衡器或用户定义的路由来自定义 AKS 群集。

> [!IMPORTANT]
> 出站类型仅影响群集的出口流量。 有关详细信息，请参阅[设置流入量控制器](ingress-basic.md)。

### <a name="outbound-type-of-loadbalancer"></a>LoadBalancer 出站类型

如果设置 `loadBalancer`，则 AKS 会自动完成以下设置。 负载均衡器用于通过 AKS 分配的公共 IP 的流出量。 `loadBalancer` 出站类型支持 `loadBalancer` 类型的 Kubernetes 服务，该服务期望使用 AKS 资源提供程序创建的负载均衡器的流出量。

以下设置由 AKS 完成。
   * 针对群集流出量预配公共 IP 地址。
   * 将公共 IP 地址分配给负载均衡器资源。
   * 针对群集中代理节点设置负载均衡器的后端池。

下面是默认情况下在 AKS 群集中部署的网络拓扑，该网络拓扑使用的 `outboundType` 为 `loadBalancer`。

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>UserDefinedRouting 出站类型

> [!NOTE]
> 使用出站类型是一种高级网络方案，需要正确的网络配置。

如果设置 `userDefinedRouting`，则 AKS 将不会自动配置流出量路径。 以下操作应由用户执行。

必须将 AKS 群集部署到已配置子网的现有虚拟网络中。 使用标准负载均衡器 (SLB) 体系结构时，必须建立显式流出量。 这需要向设备（例如防火墙、网关、本地）发送流出量请求，或允许通过分配到标准负载均衡器或给定节点的公共 IP 完成流出量。

AKS 资源提供程序将部署标准负载均衡器 (SLB)。 负载均衡器未配置任何规则，因此[在配置规则之前不会产生任何费用](https://azure.microsoft.com/pricing/details/load-balancer/)。 AKS 将不会自动为 SLB 前端预配公共 IP 地址。 AKS 将不会自动配置负载均衡器后端池。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>使用 UDR 和 Azure 防火墙出站类型部署群集

若要说明使用用户定义路由且具有出站类型的集群的应用程序，可以在与 Azure 防火墙对等互连的虚拟网络上配置群集。

![锁定拓扑](media/egress-outboundtype/outboundtype-udr.png)

* 强制流入量流经防火墙筛选器
   * 隔离的子网拥有内部负载均衡器，用于路由到代理节点
   * 代理节点隔离在专用子网中
* 出站请求使用用户定义的路由从代理节点发送到 Azure 防火墙内部 IP
   * 来自 AKS 代理节点的请求遵循 UDR，该 UDR 位于 AKS 群集部署到的子网中。
   * Azure 防火墙从公共 IP 前端中的虚拟网络出口流量
   * 对 AKS 控制平面的访问受 NSG 保护，该 NSG 启用了防火墙前端 IP 地址
   * 对公共 Internet 或其他 Azure 服务的访问流入和流出防火墙前端 IP 地址

### <a name="set-configuration-via-environment-variables"></a>通过环境变量设置配置

定义要在资源创建中使用的一组环境变量。

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

接下来，设置订阅 ID。

```azure-cli

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>创建包含多个子网的虚拟网络

预配具有三个单独子网的虚拟网络：一个用于群集、一个用于防火墙、一个用于服务流入量。

![空网络拓扑](media/egress-outboundtype/empty-network.png)

创建资源组以保存所有资源。

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

创建两个虚拟网络来托管 AKS 群集和 Azure 防火墙。 各虚拟网络都有自己的子网。 让我们从 AKS 网络开始。

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>使用 UDR 创建和设置 Azure 防火墙

必须配置 Azure 防火墙入站和出站规则。 防火墙的主要用途是使组织能够在 AKS 群集中设置精细的入口和出口流量规则。

![防火墙和 UDR](media/egress-outboundtype/firewall-udr.png)

创建将用作 Azure 防火墙前端地址的标准 SKU 公共 IP 资源。

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

注册预览版 CLI 扩展以创建 Azure 防火墙。
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

现可将之前创建的 IP 地址分配到防火墙前端。
> [!NOTE]
> 设置 Azure 防火墙的公共 IP 地址可能需要几分钟才能完成。
> 
> 如果以下命令反复收到错误，请删除现有的防火墙和公共 IP，并同时通过门户预配公共 IP 和 Azure 防火墙。

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

当前面的命令成功时，保存防火墙前端 IP 地址以便稍后进行配置。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>使用到 Azure 防火墙的跃点创建 UDR

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。

创建一个要与给定子网关联的空路由表。 该路由表将把下一个跃点定义为前面创建的 Azure 防火墙。 每个子网可以有一个与之关联的路由表，也可以没有。

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

请参阅[虚拟网络路由表文档](../virtual-network/virtual-networks-udr-overview.md#user-defined)，了解如何替代 Azure 的默认系统路由或向子网的路由表添加其他路由。

## <a name="adding-network-firewall-rules"></a>添加网络防火墙规则

> [!WARNING]
> 下面展示了一个添加防火墙规则的示例。 应用程序防火墙规则必须启用在[必需的流出量终结点](egress.md)中定义的所有流出量终结点，AKS 群集才能运行。 如果未启用这些终结点，则群集将无法运行。

下面是一个网络和应用程序规则示例。 我们添加了一个网络规则，该规则允许任何协议、源地址、目标地址和目标端口。 我们还针对 AKS 所需的某些终结点添加了一个应用程序规则。

在生产方案中，只应启用对应用程序所需的终结点以及在 [AKS 所需的流出量](egress.md)中定义的那些终结点的访问。

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

请参阅 [Azure 防火墙文档](https://docs.microsoft.com/azure/firewall/overview)，了解有关 Azure 防火墙服务的详细信息。

## <a name="associate-the-route-table-to-aks"></a>将路由表关联到 AKS

要将群集与防火墙关联，群集子网的专用子网必须引用前面创建的路由表。 可以向群集和防火墙所在的虚拟网络发出命令来更新群集子网的路由表，以此实现关联。

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>将 UDR 出站类型的 AKS 部署到现有网络

现可将 AKS 群集部署到现有的虚拟网络设置。 要将群集出站类型设置为用户定义的路由，必须向 AKS 提供一个现有子网。

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>创建一个服务主体，使其有权访问现有虚拟网络中的预配资源

AKS 使用服务主体来创建群集资源。 在创建时传递的服务主体用于创建 AKS 使用的基础 AKS 资源，如 VM、存储和负载均衡器。 如果授予的权限太少，它将无法预配 AKS 群集。

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

现在，将下面的 `APPID` 和 `PASSWORD` 替换为由前面的命令输出自动生成的服务主体 appid 和服务主体密码。 我们将引用 VNET 资源 ID 来向服务主体授予相应权限，以便 AKS 可以在其中部署资源。

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>部署 AKS

最后，可以将 AKS 群集部署到专用于群集的现有子网中。 要在其中部署的目标子网是使用环境变量 `$SUBNETID` 定义的。 在前面的步骤中，我们未定义 `$SUBNETID` 变量。 要设置子网 ID 的值，可以使用以下命令：

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

我们将定义出站类型，以遵循子网上存在的 UDR，从而使 AKS 可以跳过负载均衡器的设置和 IP 预配，现在严格来说，该负载均衡器是内部的。

可以添加 [API 服务器授权 IP 范围](api-server-authorized-ip-ranges.md)的 AKS 功能，以将 API 服务器限制为仅访问防火墙的公共终结点。 授权 IP 范围功能在图中表示为 NSG，必须传递 NSG 才能访问控制平面。 启用授权 IP 范围功能以限制 API 服务器访问权限时，开发者工具必须使用防火墙的虚拟网络中的 jumpbox，或者必须将所有开发者终结点添加到授权 IP 范围。

> [!TIP]
> 可以将其他功能添加到群集部署中，如 (专用群集)[]。 使用授权 IP 范围时，需要在群集网络内使用 jumpbox 来访问 API 服务器。

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>启用对 API 服务器的开发者访问权限

由于群集存在授权 IP 范围设置，因此必须将开发者工具 IP 地址添加到已批准的 IP 范围的 AKS 群集列表中，才能访问 API 服务器。 另一种方法是使用位于防火墙的虚拟网络中的单独子网内的所需工具配置 jumpbox。

用以下命令将其他 IP 地址添加到已批准的范围

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用 [az aks get-credentials][az-aks-get-credentials] 命令配置 `kubectl`，使其连接到新创建的 Kubernetes 群集。 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>设置内部负载均衡器

AKS 在集群中部署了一个负载均衡器，可将其设置为[内部负载均衡器](internal-lb.md)。

若要创建内部负载均衡器，请创建具有 LoadBalancer 服务类型和 azure-load-balancer-internal 注释的 internal-lb.yaml 服务清单，如以下示例所示：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

使用 kubectl apply 部署内部负载均衡器，并指定 YAML 清单的名称：

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>部署 Kubernetes 服务

由于群集出站类型设置为 UDR，因此在创建群集时，AKS 不会自动完成将代理节点作为负载均衡器的后端池进行关联这一操作。 但是，如果部署了 Kubernetes 服务，后端池关联将由 Kubernetes Azure 云服务提供商处理。

将以下 yaml 复制到名为 `example.yaml` 的文件，部署 Azure 投票应用。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

通过运行以下代码部署服务：

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>将 DNAT 规则添加到 Azure 防火墙

要配置入站连接，必须将 DNAT 规则写入 Azure 防火墙。 要测试与群集的连接，请针对防火墙前端公共 IP 地址定义一个规则，以便路由到由内部服务公开的内部 IP。

由于目标地址是防火墙上要访问的端口，因此可对其进行自定义。 转换后的地址必须是内部负载均衡器的 IP 地址。 转换后的端口必须是 Kubernetes 服务的已公开的端口。

需要指定分配给 Kubernetes 服务创建的负载均衡器的内部 IP 地址。 通过运行以下代码检索地址：

```bash
kubectl get services
```

所需的 IP 地址将列在“EXTERNAL-IP”列中，如下所示。

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>清理资源

> [!NOTE]
> 删除 Kubernetes 内部服务时，如果所有服务都不再使用内部负载均衡器，则 Azure 云服务提供商将删除内部负载均衡器。 下一次部署服务时，如果请求的配置中找不到任何负载均衡器，则将部署负载均衡器。

要清理 Azure 资源，请删除 AKS 资源组。

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>验证连接

在浏览器中导航到 Azure 防火墙前端 IP 地址，以验证连接。

此时应会显示一个 Azure 投票应用的映像。

## <a name="next-steps"></a>后续步骤

请参阅 [Azure 网络 UDR 概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

请参阅[如何创建、更改或删除路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
