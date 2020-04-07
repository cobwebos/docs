---
title: 在 Azure 库伯奈斯服务 （AKS） 中自定义用户定义的路由 （UDR）
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中定义自定义出口路由
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 3780680c485aebf1ffc654d31c577821a9b96fff
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676508"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>使用用户定义的路由自定义群集出口（预览）

可以自定义 AKS 群集的出入口以适应特定方案。 默认情况下，AKS 将预配要设置的标准 SKU 负载均衡器并用于出口。 但是，如果不允许使用公共 IP 或出口需要额外的跃点，则默认设置可能无法满足所有方案的要求。

本文介绍如何自定义群集的出口路由以支持自定义网络方案，例如那些不允许公共 IP 并且要求群集位于网络虚拟设备 （NVA） 后面的方案。

> [!IMPORTANT]
> AKS 预览功能是自助服务，可选择加入。 预览版*以现有*和*可用状态*提供，不在服务级别协议 （SLA） 和有限保修中。 客户支持会*尽力*提供 AKS 预览。 因此，这些功能不适合生产用途。 有关详细信息，请参阅以下支持文章：
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="prerequisites"></a>先决条件
* Azure CLI 版本 2.0.81 或更高版本
* Azure CLI 预览扩展版 0.4.28 或更高版本
* API 版本`2020-01-01`或更高版本

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>安装最新的 Azure CLI AKS 预览扩展
要设置群集的出站类型，需要 Azure CLI AKS 预览扩展版本 0.4.18 或更高版本。 使用 az 扩展添加命令安装 Azure CLI AKS 预览扩展，然后使用以下 az 扩展更新命令检查是否有任何可用更新：

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>限制
* 在预览期间`outboundType`，只能在群集创建时间定义，之后无法更新。
* 在预览期间`outboundType`，AKS 群集应使用 Azure CNI。 Kubenet 是可配置的，使用需要路由表的手动关联到 AKS 子网。
* 设置`outboundType``vm-set-type`需要具有`VirtualMachineScaleSets`和`load-balancer-sku`的`Standard`AKS 群集。
* 设置为`outboundType`的值`UDR`需要具有群集有效出站连接的用户定义的路由。
* 设置为`outboundType`的值`UDR`意味着路由到负载均衡器的入口源 IP 可能与群集的传出出口目标地址**不匹配**。

## <a name="overview-of-outbound-types-in-aks"></a>AKS 中的出站类型概述

AKS 群集可以使用唯`outboundType`一的类型负载均衡器或用户定义的路由进行自定义。

> [!IMPORTANT]
> 出站类型仅影响群集的出口流量。 有关详细信息[，请参阅设置入口控制器](ingress-basic.md)。

### <a name="outbound-type-of-loadbalancer"></a>出站式负载平衡器

如果`loadBalancer`设置，AKS 将自动完成以下设置。 负载均衡器用于通过分配的公共 IP 的 AKS 出口。 一种`loadBalancer`出站类型支持 Kubernetes`loadBalancer`类型的服务，它期望从 AKS 资源提供程序创建的负载均衡器中流出。

以下设置由 AKS 完成。
   * 为群集出口预配公共 IP 地址。
   * 公共 IP 地址分配给负载均衡器资源。
   * 为群集中的代理节点设置负载均衡器的后端池。

下面是默认情况下部署在 AKS 群集中的网络拓扑，该拓扑使用 的`outboundType` `loadBalancer`。

![出站型磅](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>出站类型的用户定义路由

> [!NOTE]
> 使用出站类型是一种高级网络方案，需要适当的网络配置。

如果`userDefinedRouting`设置，AKS 不会自动配置出口路径。 用户应完成以下**操作。**

群集必须部署到具有已配置的子网的现有虚拟网络中。 具有出站连接的子网上必须存在有效的用户定义的路由 （UDR）。

AKS 资源提供程序将部署标准负载均衡器 （SLB）。 负载均衡器未配置任何规则，[在放置规则之前不会产生费用](https://azure.microsoft.com/pricing/details/load-balancer/)。 AKS**不会**自动为 SLB 前端预配公共 IP 地址。 AKS**不会**自动配置负载均衡器后端池。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>部署具有出站类型的 UDR 和 Azure 防火墙的群集

为了说明使用用户定义的路由使用出站类型的群集的应用程序，可以在使用 Azure 防火墙对等的虚拟网络上配置群集。

![锁定拓扑](media/egress-outboundtype/outboundtype-udr.png)

* 入口被迫通过防火墙过滤器
   * 隔离子网包含内部负载均衡器，用于路由到代理节点
   * 代理节点在专用子网中隔离
* 出站请求使用用户定义的路由从代理节点启动到 Azure 防火墙内部 IP
   * 来自 AKS 代理节点的请求遵循已放置在 AKS 群集部署到的子网上的 UDR。
   * Azure 防火墙从公共 IP 前端退出虚拟网络
   * 对 AKS 控制平面的访问受 NSG 保护，该 NSG 启用了防火墙前端 IP 地址
   * 访问公共 Internet 或其他 Azure 服务流自防火墙前端 IP 地址

### <a name="set-configuration-via-environment-variables"></a>通过环境变量设置配置

定义一组要用于资源创建的环境变量。

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

接下来，设置订阅代码。

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>创建包含多个子网的虚拟网络

配置一个虚拟网络，其中有三个单独的子网，一个用于群集，一个用于防火墙，一个用于服务入口。

![空网络拓扑](media/egress-outboundtype/empty-network.png)

创建一个资源组来保存所有资源。

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

创建两个虚拟网络来承载 AKS 群集和 Azure 防火墙。 每个都将有自己的子网。 让我们从 AKS 网络开始。

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

必须配置 Azure 防火墙入站和出站规则。 防火墙的主要目的是使组织能够设置精细的入口和出口流量规则进出 AKS 群集。

![防火墙和 UDR](media/egress-outboundtype/firewall-udr.png)

创建标准 SKU 公共 IP 资源，这些资源将用作 Azure 防火墙前端地址。

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

注册预览 cli 扩展以创建 Azure 防火墙。
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

之前创建的 IP 地址现在可以分配给防火墙前端。
> [!NOTE]
> 将公共 IP 地址设置为 Azure 防火墙可能需要几分钟时间。
> 
> 如果以下命令反复收到错误，请删除现有防火墙和公共 IP，并同时通过门户预配公共 IP 和 Azure 防火墙。

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

当前一个命令成功时，保存防火墙前端 IP 地址以供以后配置。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>使用 Azure 防火墙的跃点创建 UDR

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。

创建要与给定子网关联的空路由表。 路由表将下一个跃点定义为上面创建的 Azure 防火墙。 每个子网可以有一个与之关联的路由表，也可以没有。

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

有关如何覆盖 Azure 的默认系统路由或向子网的路由表添加其他路由的[虚拟网络路由表文档](../virtual-network/virtual-networks-udr-overview.md#user-defined)。

## <a name="adding-network-firewall-rules"></a>添加网络防火墙规则

> [!WARNING]
> 下面显示了添加防火墙规则的一个示例。 必须通过应用程序防火墙规则启用[在所需出口终结点](egress.md)中定义的所有出口终结点，以便 AKS 群集正常运行。 如果启用这些终结点，群集将无法运行。

下面是网络和应用程序规则的示例。 我们添加一个网络规则，允许任何协议、源地址、目标地址和目标端口。 我们还为 AKS 所需的**某些**终结点添加应用程序规则。

在生产方案中，应仅启用对应用程序所需的终结点和[在 AKS 中定义的终结点的访问。](egress.md)

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

请参阅[Azure 防火墙文档](https://docs.microsoft.com/azure/firewall/overview)以了解有关 Azure 防火墙服务的信息。

## <a name="associate-the-route-table-to-aks"></a>将路由表关联到 AKS

要将群集与防火墙关联，群集子网的专用子网必须引用上面创建的路由表。 可以通过向同时持有群集和防火墙的虚拟网络发出命令来更新群集子网的路由表，从而完成关联。

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>将具有出站型 UDR 的 AKS 部署到现有网络

现在，AKS 群集可以部署到现有的虚拟网络设置中。 为了将群集出站类型设置为用户定义的路由，必须向 AKS 提供现有子网。

![aks 部署](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>创建具有在现有虚拟网络内进行预配的服务主体

AKS 使用服务主体创建群集资源。 在创建时传递的服务主体用于创建基础 AKS 资源，如 AKS 使用的 VM、存储和负载均衡器。 如果授予的权限太少，它将无法预配 AKS 群集。

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

现在，将`APPID``PASSWORD`和 下替换为服务主体应用id 和服务主体密码，这些密码由以前的命令输出自动生成。 我们将引用 VNET 资源 ID 向服务主体授予权限，以便 AKS 可以将资源部署到其中。

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

最后，AKS 群集可以部署到我们专用于群集的现有子网中。 要部署到的目标子网使用环境变量`$SUBNETID`定义。 在前面的步骤中，我们没有`$SUBNETID`定义变量。 要设置子网 ID 的值，可以使用以下命令：

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

我们将定义出站类型，以遵循子网上存在的 UDR，使 AKS 能够跳过负载均衡器的设置和 IP 配置，现在可以严格内部设置和 IP 配置。

可以添加 API[服务器授权的 IP 范围](api-server-authorized-ip-ranges.md)的 AKS 功能，以将 API 服务器访问限制为防火墙的公共终结点。 授权 IP 范围功能在关系图中表示为 NSG，必须传递以访问控制平面。 启用授权 IP 范围功能以限制 API 服务器访问时，开发人员工具必须使用防火墙虚拟网络中的跳转框，否则必须将所有开发人员终结点添加到授权的 IP 范围。

> [!TIP]
> 可以将其他功能添加到群集部署，例如（专用群集）*。 使用授权 IP 范围时，群集网络内部需要一个跳转框才能访问 API 服务器。

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

### <a name="enable-developer-access-to-the-api-server"></a>启用开发人员对 API 服务器的访问

由于群集的 IP 范围设置，您必须将开发人员工具 IP 地址添加到已批准 IP 范围的 AKS 群集列表中才能访问 API 服务器。 另一个选项是在防火墙虚拟网络中的单独子网内配置具有所需工具的跳转框。

使用以下命令将另一个 IP 地址添加到已批准的范围

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用[az aks 获取凭据][az-aks-get-credentials]命令进行`kubectl`配置以连接到新创建的 Kubernetes 群集。 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>设置内部负载均衡器

AKS 已部署了一个负载均衡器与群集，可以设置为[内部负载均衡器](internal-lb.md)。

要创建内部负载均衡器，请使用服务类型 LoadBalancer 和 azure 负载均衡器内部注释创建名为内部-lb.yaml 的服务清单，如以下示例所示：

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

## <a name="deploy-a-kubernetes-service"></a>部署库伯奈斯服务

由于群集出站类型设置为 UDR，因此 AKS 在群集创建时不会自动完成将代理节点关联为负载均衡器的后端池。 但是，在部署库伯奈斯服务时，后端池关联由库伯内斯 Azure 云提供程序处理。

通过将下面的 yaml 复制到名为`example.yaml`的文件来部署 Azure 投票应用应用程序。

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

通过运行服务部署服务：

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>向 Azure 防火墙添加 DNAT 规则

要配置入站连接，必须将 DNAT 规则写入 Azure 防火墙。 为了测试与群集的连接，为防火墙前端公共 IP 地址定义了一个规则，用于路由到内部服务公开的内部 IP。

可以自定义目标地址，因为它是要访问的防火墙上的端口。 翻译的地址必须是内部负载均衡器的 IP 地址。 翻译后的端口必须是库伯内斯服务的外露端口。

您需要指定分配给 Kubernetes 服务创建的负载均衡器的内部 IP 地址。 通过运行来检索地址：

```bash
kubectl get services
```

所需的 IP 地址将列在"外部 IP"列中，如下所示。

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
> 删除 Kubernetes 内部服务时，如果内部负载均衡器不再被任何服务使用，Azure 云提供程序将删除内部负载均衡器。 在下一个服务部署中，如果找不到请求的配置，将部署负载均衡器。

要清理 Azure 资源，请删除 AKS 资源组。

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>验证连接

在浏览器中导航到 Azure 防火墙前端 IP 地址以验证连接。

您应该会看到 Azure 投票应用的图像。

## <a name="next-steps"></a>后续步骤

请参阅[Azure 网络 UDR 概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

[了解如何创建、更改或删除路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
