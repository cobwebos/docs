---
title: 创建使用新应用程序网关的入口控制器
description: 本文提供有关如何部署使用新应用程序网关的应用程序网关入口控制器的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b46c9f8b0cad74f3a4e9be8903270a60993c01f4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585884"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>如何安装使用新应用程序网关的应用程序网关入口控制器 (AGIC)

以下说明假设将在不包含任何现有组件的环境中安装应用程序网关入口控制器 (AGIC)。

## <a name="required-command-line-tools"></a>所需的命令行工具

我们建议在以下所有命令行操作中使用[Azure 云外壳](https://shell.azure.com/)。 从shell.azure.com启动外壳，或通过单击链接：

[![嵌入启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

或者，使用以下图标从 Azure 门户启动云外壳：

![门户启动](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure 云外壳](https://shell.azure.com/)已具有所有必要的工具。 如果选择使用其他环境，请确保安装了以下命令行工具：

* `az` - Azure CLI：[安装说明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` - Kubernetes 命令行工具：[安装说明](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` - Kubernetes 包管理器：[安装说明](https://github.com/helm/helm/releases/latest)
* `jq` - 命令行 JSON 处理器：[安装说明](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>创建标识

遵循以下步骤创建 Azure Active Directory (AAD) [服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 请记下 `appId`、`password` 和 `objectId` 值 - 在后续步骤中需要用到。

1. 创建 AD 服务主体（[详细了解 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)）：
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    在后续步骤中将要用到 JSON 输出中的 `appId` 和 `password` 值


1. 使用上一命令的输出中的 `appId` 获取新服务主体的 `objectId`：
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    此命令的输出为 `objectId`，在下面所述的 Azure 资源管理器模板中将要用到此值

1. 创建稍后要在 Azure 资源管理器模板部署中使用的参数文件。
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    若要部署启用 **RBAC** 的群集，请将 `aksEnabledRBAC` 字段设置为 `true`

## <a name="deploy-components"></a>部署组件
此步骤将以下组件添加到订阅：

- [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)v2
- 包含 2 个[子网](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)的[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md) 将要使用的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. 下载 Azure 资源管理器模板，并根据需要修改该模板。
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. 使用 `az cli` 部署该 Azure 资源管理器模板。 此步骤最多可能需要 5 分钟。
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. 部署完成后，将部署输出下载到名为 `deployment-outputs.json` 的文件中。
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>设置应用程序网关入口控制器

使用上一节中的说明，我们创建并配置了新的 AKS 群集和应用程序网关。 现在，我们已准备好将一个示例应用和入口控制器部署到新的 Kubernetes 基础结构。

### <a name="setup-kubernetes-credentials"></a>设置 Kubernetes 凭据
对于以下步骤，我们需要设置[库布ectl](https://kubectl.docs.kubernetes.io/)命令，我们将用它来连接到新的 Kubernetes 群集。 [云外壳](https://shell.azure.com/)已`kubectl`安装。 我们将使用 `az` CLI 获取 Kubernetes 的凭据。

获取新部署的 AKS 的凭据（[详细了解](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)）：
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>安装 AAD Pod Identity
  Azure Active Directory Pod Identity 提供对 [Azure 资源管理器 (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 的基于令牌的访问。

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) 会将以下组件添加到 Kubernetes 群集：
   * 库伯内斯[CRD：](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity`， `AzureAssignedIdentity`， ，`AzureIdentityBinding`
   * [托管标识控制器 (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 组件
   * [节点托管标识 (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 组件


将 AAD Pod Identity 安装到群集：

   - 已启用 RBAC 的 AKS 群集**

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - 已禁用 RBAC 的 AKS 群集**

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>安装 Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) 是 Kubernetes 的包管理器。 我们将利用它来安装 `application-gateway-kubernetes-ingress` 包：

1. 安装 [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) 并运行以下命令来添加 `application-gateway-kubernetes-ingress` Helm 包：

    - 已启用 RBAC 的 AKS 群集**

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - 已禁用 RBAC 的 AKS 群集**

        ```bash
        helm init
        ```

1. 添加 AGIC Helm 存储库：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>安装入口控制器 Helm 图表

1. 使用前面创建的 `deployment-outputs.json` 文件并创建以下变量。
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. 下载 helm-config.yaml 用于配置 AGIC：
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

1. 编辑新下载的 helm-config.yaml，并填写 `appgw` 和 `armAuth` 节。
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   值：
     - `verbosityLevel`：设置 AGIC 日志记录基础结构的详细程度。 有关可能的值，请参阅[日志记录级别](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)。
     - `appgw.subscriptionId`：应用程序网关所在的 Azure 订阅 ID。 示例：`a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`：在其中创建应用程序网关的 Azure 资源组的名称。 示例：`app-gw-resource-group`
     - `appgw.name`：应用程序网关的名称。 示例：`applicationgatewayd0f0`
     - `appgw.shared`： 此布尔标志应默认为`false`。 如果需要[共享的应用程序网关](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)，请设置为 `true`。
     - `kubernetes.watchNamespace`：指定 AGIC 应监视的名称空间。 此命名空间可以是单字符串值，也可以是逗号分隔的命名空间列表。
    - `armAuth.type`：可以是 `aadPodIdentity` 或 `servicePrincipal`。
    - `armAuth.identityResourceID`：Azure 托管标识的资源 ID
    - `armAuth.identityClientId`：标识的客户端 ID。 有关标识的详细信息，请参阅下文
    - `armAuth.secretJSON`：仅在选择服务主体机密类型时`armAuth.type`（已设置为`servicePrincipal`） 时才需要 


   > [!NOTE]
   > 和`identityResourceID``identityClientID`的值是在[部署组件](ingress-controller-install-new.md#deploy-components)步骤期间创建的，可以使用以下命令再次获取：
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > 在以上命令中，`<resource-group>` 是应用程序网关的资源组。 `<identity-name>` 是创建的标识的名称。 可以使用 `az identity list` 列出给定订阅的所有标识


1. 安装应用程序网关入口控制器包：

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>安装示例应用
现在，我们已经安装了应用程序网关、AKS 和 AGIC，我们可以通过[Azure 云外壳](https://shell.azure.com/)安装示例应用：

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

或者，可以：

* 下载上述 YAML 文件：

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* 应用 YAML 文件：

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>其他示例
[此操作指南](ingress-controller-expose-service-over-http-https.md)包含了有关如何使用应用程序网关通过 HTTP 或 HTTPS 向 Internet 公开 AKS 服务的更多示例。
