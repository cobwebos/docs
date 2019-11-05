---
title: 使用新的应用程序网关创建入口控制器
description: 本文提供了有关如何使用新的应用程序网关部署应用程序网关入口控制器的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/22/2019
ms.author: caya
ms.openlocfilehash: a11a444ca4e9485273f5dc94209c390289080838
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513440"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>如何使用新的应用程序网关安装应用程序网关入口控制器（AGIC）

以下说明假定应用程序网关入口控制器（AGIC）将安装在不具有预先存在的组件的环境中。

## <a name="required-command-line-tools"></a>必需的命令行工具

建议将[Azure Cloud Shell](https://shell.azure.com/)用于以下所有命令行操作。 从 shell.azure.com 或单击链接启动 shell：

[![嵌入启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

或者，使用以下图标从 Azure 门户启动 Cloud Shell：

![门户启动](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

你的[Azure Cloud Shell](https://shell.azure.com/)已有所有必需的工具。 如果你选择使用其他环境，请确保已安装以下命令行工具：

* `az`-Azure CLI：[安装说明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` Kubernetes 命令行工具：[安装说明](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` Kubernetes 包管理器：[安装说明](https://github.com/helm/helm/releases/latest)
* `jq` 命令行 JSON 处理器：[安装说明](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>创建标识

按照以下步骤创建 Azure Active Directory （AAD）[服务主体对象](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 请记录 `appId`、`password`和 `objectId` 值，这些值将在以下步骤中使用。

1. 创建 AD 服务主体（[详细了解 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)）：
    ```bash
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    将在以下步骤中使用 JSON 输出中的 `appId` 值和 `password` 值


1. 使用上一命令输出中的 `appId` 获取新服务主体的 `objectId`：
    ```bash
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    此命令的输出为 `objectId`，将在下面的 Azure 资源管理器模板中使用

1. 稍后创建将用于 Azure 资源管理器模板部署的参数文件。
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
    若要部署启用了**RBAC**的群集，请将 "`aksEnabledRBAC`" 字段设置为 "`true`

## <a name="deploy-components"></a>部署组件
此步骤会将以下组件添加到你的订阅：

- [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)v2
- 带有 2[个子网](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)的[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [AAD Pod 标识](https://github.com/Azure/aad-pod-identity/blob/master/README.md)将使用的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. 下载 Azure 资源管理器模板，并根据需要修改模板。
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. 使用 `az cli`部署 Azure 资源管理器模板。 这最多可能需要5分钟。
    ```bash
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

1. 部署完成后，将部署输出下载到名为 `deployment-outputs.json`的文件中。
    ```bash
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>设置应用程序网关入口控制器

按照上一部分中的说明创建和配置了新的 AKS 群集和应用程序网关。 现在，我们已准备好将示例应用和入口控制器部署到新的 Kubernetes 基础结构。

### <a name="setup-kubernetes-credentials"></a>设置 Kubernetes 凭据
对于下面的步骤，我们需要安装[kubectl](https://kubectl.docs.kubernetes.io/)命令，该命令将用于连接到新的 Kubernetes 群集。 [Cloud Shell](https://shell.azure.com/)已安装 `kubectl`。 我们将使用 `az` CLI 来获取 Kubernetes 的凭据。

获取新部署的 AKS 的凭据（[了解详细信息](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)）：
```bash
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>安装 AAD Pod 标识
  Azure Active Directory Pod 标识提供对[Azure 资源管理器（ARM）](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的基于令牌的访问。

  [AAD Pod 标识](https://github.com/Azure/aad-pod-identity)会将以下组件添加到 Kubernetes 群集：
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)： `AzureIdentity`、`AzureAssignedIdentity``AzureIdentityBinding`
   * [托管标识控制器（MIC）](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)组件
   * [节点托管标识（NMI）](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)组件


将 AAD Pod 标识安装到群集：

   - *已启用 RBAC*AKS 群集

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
    ```

   - *已禁用 RBAC*AKS 群集

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
    ```

### <a name="install-helm"></a>安装 Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)是 Kubernetes 的包管理器。 我们将利用它来安装 `application-gateway-kubernetes-ingress` 包：

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

### <a name="install-ingress-controller-helm-chart"></a>安装入口控制器 Helm 图

1. 使用上面创建的 `deployment-outputs.json` 文件并创建以下变量。
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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

1. 编辑新下载的 helm yaml，并填写 `appgw` 和 `armAuth`部分。
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
     - `verbosityLevel`：设置 AGIC 日志记录基础结构的详细级别。 有关可能的值，请参阅[日志记录级别](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)。
     - `appgw.subscriptionId`：应用程序网关所在的 Azure 订阅 ID。 示例： `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`：在其中创建应用程序网关的 Azure 资源组的名称。 示例： `app-gw-resource-group`
     - `appgw.name`：应用程序网关的名称。 示例： `applicationgatewayd0f0`
     - `appgw.shared`：此布尔标志应默认为 `false`。 如果需要[共享应用程序网关](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)，请设置为 "`true`"。
     - `kubernetes.watchNamespace`：指定 AGIC 应监视的命名空间。 这可以是单个字符串值或以逗号分隔的命名空间列表。
    - `armAuth.type`： `aadPodIdentity` 或 `servicePrincipal`
    - `armAuth.identityResourceID`： Azure 托管标识的资源 ID
    - `armAuth.identityClientId`：标识的客户端 ID。 有关标识的详细信息，请参阅下文
    - `armAuth.secretJSON`：仅在选择了服务主体机密类型时才需要（当 `armAuth.type` 设置为 `servicePrincipal`时） 


   > [!NOTE]
   > `identityResourceID` 和 `identityClientID` 是在[创建标识](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity)步骤期间创建的值，可以使用以下命令重新获得：
   > ```bash
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > 上述命令中的 `<resource-group>` 是应用程序网关的资源组。 `<identity-name>` 是所创建的标识的名称。 给定订阅的所有标识都可以使用以下列出： `az identity list`


1. 安装应用程序网关入口控制器包：

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>安装示例应用
现在，我们已安装了应用程序网关、AKS 和 AGIC，接下来可以通过[Azure Cloud Shell](https://shell.azure.com/)安装示例应用：

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

您也可以：

* 下载上面的 YAML 文件：

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* 应用 YAML 文件：

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>其他示例
本操作[方法指南](ingress-controller-expose-service-over-http-https.md)包含有关如何使用应用程序网关向 INTERNET 公开 AKS 服务的更多示例。
