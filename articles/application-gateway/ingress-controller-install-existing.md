---
title: 使用现有应用程序网关创建入口控制器
description: 本文提供了有关如何使用现有应用程序网关部署应用程序网关入口控制器的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/22/2019
ms.author: caya
ms.openlocfilehash: 045fb54956e78e826b06dc1c56c29e1c7bd430bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513414"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>使用现有应用程序网关安装应用程序网关入口控制器（AGIC）

应用程序网关入口控制器（AGIC）是 Kubernetes 群集中的 pod。
AGIC 监视 Kubernetes[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)资源，并根据 Kubernetes 群集的状态创建和应用应用程序网关配置。

## <a name="outline"></a>空心
- [先决条件](#prerequisites)
- [Azure 资源管理器身份验证（ARM）](#azure-resource-manager-authentication)
    - 选项1：[设置 aad-pod 标识](#set-up-aad-pod-identity)并在扶手上创建 Azure 标识
    - 选项2：[使用服务主体](#using-a-service-principal)
- [使用 Helm 安装入口控制器](#install-ingress-controller-as-a-helm-chart)
- [多群集/共享应用程序网关](#multi-cluster--shared-application-gateway)：在环境中安装 AGIC，在该环境中，应用程序网关在一个或多个 AKS 群集和/或其他 Azure 组件之间共享。

## <a name="prerequisites"></a>必备组件
本文档假设你已安装以下工具和基础结构：
- 启用了[高级网络](https://docs.microsoft.com/azure/aks/configure-azure-cni)的[AKS](https://azure.microsoft.com/services/kubernetes-service/)
- 与 AKS 位于同一虚拟网络中的[应用程序网关 v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant)
- 已在 AKS 群集上安装[AAD Pod 标识](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/)是安装了 `az` CLI、`kubectl`和 `helm` 的 Azure Shell 环境。 以下命令需要这些工具。

请在安装 AGIC 之前__备份应用程序网关的配置__：
  1. 使用[Azure 门户](https://portal.azure.com/)导航到 `Application Gateway` 实例
  2. 从 `Export template` 单击 "`Download`

下载的 zip 文件将具有 JSON 模板、bash 和 PowerShell 脚本，你可以使用这些脚本来还原应用网关（如果需要）

## <a name="install-helm"></a>安装 Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)是 Kubernetes 的包管理器。 我们将利用它来安装 `application-gateway-kubernetes-ingress` 软件包。
使用[Cloud Shell](https://shell.azure.com/)安装 Helm：

1. 安装[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)并运行以下项以添加 `application-gateway-kubernetes-ingress` Helm 包：

    - *已启用 RBAC*AKS 群集

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *已禁用 RBAC*AKS 群集

    ```bash
    helm init
    ```

1. 添加 AGIC Helm 存储库：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure 资源管理器身份验证

AGIC 与 Kubernetes API 服务器和 Azure 资源管理器通信。 它需要一个标识来访问这些 Api。

## <a name="set-up-aad-pod-identity"></a>设置 AAD Pod 标识

[AAD Pod 标识](https://github.com/Azure/aad-pod-identity)是一个控制器，类似于 AGIC，后者也在你的 AKS 上运行。 它将 Azure Active Directory 标识绑定到 Kubernetes pod。 Kubernetes pod 中的应用程序需要标识，才能与其他 Azure 组件通信。 在此特定情况下，我们需要对 AGIC pod 的授权才能向[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)发出 HTTP 请求。

按照[AAD Pod 标识安装说明](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra)将此组件添加到你的 AKS。

接下来，我们需要创建一个 Azure 标识并向其授予权限。
使用[Cloud Shell](https://shell.azure.com/)运行以下所有命令并创建标识：

1. **在与 AKS 节点相同的资源组中**创建一个 Azure 标识。 选取正确的资源组十分重要。 以下命令中所需的资源组*不*是在 AKS 门户窗格上引用的资源组。 这是 `aks-agentpool` 虚拟机的资源组。 通常，资源组以 `MC_` 开头，并包含 AKS 的名称。 例如： `MC_resourceGroup_aksABCD_westus`

    ```bash
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 对于下面的角色分配命令，我们需要获取新创建的标识的 `principalId`：

    ```bash
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. 向身份 `Contributor` 授予对应用程序网关的访问权限。 为此，需要应用程序网关的 ID，如下所示： `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    在订阅中获取应用程序网关 Id 列表： `az network application-gateway list --query '[].id'`

    ```bash
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. 为标识 `Reader` 访问应用程序网关资源组。 资源组 ID 如下所示： `/subscriptions/A/resourceGroups/B`。 可以通过以下方式获取所有资源组： `az group list --query '[].id'`

    ```bash
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>使用服务主体
还可以通过 Kubernetes 机密提供对 ARM 的 AGIC 访问。

1. 创建 Active Directory 服务主体，并使用 base64 编码。 JSON blob 需要 base64 编码才能保存到 Kubernetes。

```bash
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. 将 base64 编码的 JSON blob 添加到 `helm-config.yaml` 文件中。 有关 `helm-config.yaml` 的详细信息，请在下一部分。
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>将入口控制器安装为 Helm 图
在前几个步骤中，我们在 Kubernetes 群集上安装 Helm 的 Tiller。 使用[Cloud Shell](https://shell.azure.com/)安装 AGIC Helm 包：

1. 添加 `application-gateway-kubernetes-ingress` helm 存储库，并执行 helm 更新

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. 下载 helm-yaml，这将配置 AGIC：
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    或复制以下 YAML 文件： 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. 编辑 helm-yaml 并填写 `appgw` 和 `armAuth`的值。
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` 和 `<identity-client-id>` 是在上一节中设置的 Azure AD 标识的属性。 可以通过运行以下命令检索此信息： "`az identity show -g <resourcegroup> -n <identity-name>`"，其中，`<resourcegroup>` 是部署顶级 AKS 群集对象、应用程序网关和托管标识的资源组。

1. 在上一步中安装具有 `helm-config.yaml` 配置的 Helm 图表 `application-gateway-kubernetes-ingress`

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    或者，你可以在一个步骤中组合 `helm-config.yaml` 和 Helm 命令：
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 检查新创建的 pod 的日志以验证其是否正确启动

请参阅[本操作方法指南](ingress-controller-expose-service-over-http-https.md)，了解如何使用 Azure 应用程序网关通过 HTTP 或 HTTPS 向 INTERNET 公开 AKS 服务。



## <a name="multi-cluster--shared-application-gateway"></a>多群集/共享应用程序网关
默认情况下，AGIC 会将其链接到的应用程序网关获得完全所有权。 AGIC 版本0.8.0 和更高版本可与其他 Azure 组件共享单个应用程序网关。 例如，我们可以为虚拟机规模集上托管的应用和 AKS 群集使用同一应用程序网关。

在启用此设置之前，请__备份应用程序网关的配置__：
  1. 使用[Azure 门户](https://portal.azure.com/)导航到 `Application Gateway` 实例
  2. 从 `Export template` 单击 "`Download`

下载的 zip 文件将具有可用于还原应用程序网关的 JSON 模板、bash 和 PowerShell 脚本

### <a name="example-scenario"></a>示例方案
让我们看看虚构的应用程序网关，该网关管理2个网站的流量：
  - `dev.contoso.com` 在新的 AKS 上托管，使用应用程序网关和 AGIC
  - 在[Azure 虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)上托管 `prod.contoso.com`

对于默认设置，AGIC 假定其指向的应用程序网关的所有权为100%。 AGIC 覆盖应用程序网关的所有配置。 如果我们要为 `prod.contoso.com` （在应用程序网关上）手动创建侦听器，而不在 Kubernetes 入口中定义它，AGIC 将在几秒钟内删除 `prod.contoso.com` 配置。

若要安装 AGIC 并提供我们的虚拟机规模集计算机的 `prod.contoso.com`，必须将 AGIC 仅限制为配置 `dev.contoso.com`。 这可通过实例化以下[.crd](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)来加速：

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

上述命令将创建一个 `AzureIngressProhibitedTarget` 对象。 这会使 AGIC （版本0.8.0 和更高版本）了解 `prod.contoso.com` 的应用程序网关配置是否存在，并显式指示其避免更改与该主机名相关的任何配置。


### <a name="enable-with-new-agic-installation"></a>使用 new AGIC 安装启用
若要将 AGIC （版本0.8.0 和更高版本）限制为部分应用程序网关配置，请修改 `helm-config.yaml` 模板。
在 `appgw:` 部分中，添加 `shared` 键，并将其设置为以 `true`。

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

应用 Helm 更改：
  1. 确保安装了 `AzureIngressProhibitedTarget` .CRD：
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. 更新 Helm：
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

因此，AKS 将具有名为 `prohibit-all-targets``AzureIngressProhibitedTarget` 的新实例：
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

如名称所示，`prohibit-all-targets`对象禁止 AGIC 更改*任何*主机和路径的配置。
Helm install with `appgw.shared=true` 将部署 AGIC，但不会对应用程序网关进行任何更改。


### <a name="broaden-permissions"></a>拓展权限
由于带有 `appgw.shared=true` 的 Helm 和默认 `prohibit-all-targets` 阻止应用任何配置的 AGIC。

拓展 AGIC 权限：
1. 使用特定设置创建新 `AzureIngressProhibitedTarget`：
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 只有在创建了自己的自定义禁止之后，才能删除默认值，该默认值过于广泛：

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>为现有 AGIC 安装启用
假设已有一个有效的 AKS、应用程序网关和群集中配置的 AGIC。 我们有一个入口用于 `prod.contosor.com`，并成功地从 AKS 为其提供流量。 我们想要将 `staging.contoso.com` 添加到现有的应用程序网关，但需要将其托管在一个[VM](https://azure.microsoft.com/services/virtual-machines/)上。 我们将重新使用现有的应用程序网关，并为 `staging.contoso.com`手动配置侦听器和后端池。 但手动调整应用程序网关配置（通过[门户](https://portal.azure.com)、 [ARM api](https://docs.microsoft.com/rest/api/resources/)或[Terraform](https://www.terraform.io/)）会与 AGIC 的完全所有权假设发生冲突。 应用更改后不久，AGIC 将覆盖或删除它们。

我们可以禁止 AGIC 对部分配置进行更改。

1. 创建 `AzureIngressProhibitedTarget` 对象：
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 查看新创建的对象：
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 通过门户修改应用程序网关配置-添加侦听器、路由规则、后端等。我们创建的新对象（`manually-configured-staging-environment`）将禁止 AGIC 覆盖与 `staging.contoso.com`相关的应用程序网关配置。
