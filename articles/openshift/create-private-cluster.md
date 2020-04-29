---
title: 创建 Azure Red Hat OpenShift 4 专用群集
description: 了解如何创建运行 OpenShift 4 的 Azure Red Hat OpenShift 专用群集
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: aro，openshift，az aro，red hat，cli
ms.custom: mvc
ms.openlocfilehash: a0f726d32f2f63cf85101254fded005fc0b5a1db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233544"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>创建 Azure Red Hat OpenShift 4 专用群集

本文介绍如何准备环境，以创建运行 OpenShift 4 的 Azure Red Hat OpenShift 专用群集。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 使用专用 API 服务器终结点和专用入口控制器部署群集

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 版本2.0.75 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>开始之前

### <a name="install-the-az-aro-extension"></a>安装 "az aro" 扩展
使用`az aro`此扩展，可以使用 Azure CLI 从命令行直接创建、访问和删除 Azure Red Hat OpenShift 群集。

运行以下命令以安装`az aro`扩展。

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

如果已安装了该扩展，则可以通过运行以下命令进行更新。

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>注册资源提供程序

接下来，需要将`Microsoft.RedHatOpenShift`资源提供程序注册到订阅中。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

验证扩展是否已注册。

```azurecli-interactive
az -v
```

  应会看到类似于下面的输出。

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="obtain-a-red-hat-pull-secret-optional"></a>获取 Red Hat 请求机密（可选）

Red Hat pull secret 使群集能够访问 Red Hat 容器注册表以及其他内容。 此步骤是可选的，但建议执行。

通过导航到https://cloud.redhat.com/openshift/install/azure/aro-provisioned并单击 "*下载请求机密*" 获取你的请求机密。

你将需要使用你的企业电子邮件登录 Red Hat 帐户或创建一个新的 Red Hat 帐户，并接受条款和条件。

将保存`pull-secret.txt`的文件保存在安全的位置-在每次创建群集时使用。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>创建包含两个子网的虚拟网络

接下来，你将创建一个包含两个空子网的虚拟网络。

1. **设置以下变量。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **创建资源组**

    Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create] [az-create] 命令创建资源组。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    以下示例输出显示已成功创建资源组：

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **创建虚拟网络。**

    运行 OpenShift 4 的 Azure Red Hat OpenShift 群集需要一个包含两个子网的虚拟网络，适用于主节点和辅助节点。

    在之前创建的同一资源组中创建新的虚拟网络。

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    以下示例输出显示已成功创建的虚拟网络：

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **为主节点添加一个空子网。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **添加辅助角色节点的空子网。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[禁用子网中的子网专用终结点策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。** 这是能够连接和管理群集所必需的。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>创建群集

运行以下命令以创建群集。 请注意`apiserver-visibility`和`ingress-visibility`参数。 可以选择传递一个请求机密，使群集能够访问 Red Hat 容器注册表以及其他内容。 导航到[Red Hat OpenShift 群集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)，然后单击 "复制请求机密"，以访问你的请求机密。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain aro.example.com # [OPTIONAL] custom domain
  # --pull-secret 'Pull secret from https://cloud.redhat.com/openshift/install/azure/installer-provisioned/' # [OPTIONAL]
```

>[!NOTE]
> 创建群集通常需要大约35分钟。

>[!IMPORTANT]
> 如果选择指定自定义域（例如**foo.example.com**），则 OpenShift 控制台将出现在 URL （如`https://console-openshift-console.apps.foo.example.com`）中，而不是内置域。 `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> 默认情况下，OpenShift 对在上`*.apps.<random>.<location>.aroapp.io`创建的所有路由使用自签名证书。  如果你选择 "自定义 DNS"，则在连接到群集后，你将需要按照 OpenShift 文档为[入口控制器配置自定义 ca](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) ，并为[API 服务器配置自定义 ca](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)。

## <a name="connect-to-the-private-cluster"></a>连接到专用群集

您可以使用`kubeadmin`用户登录到群集。  运行以下命令以查找`kubeadmin`用户的密码。

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

以下示例输出显示了密码`kubeadminPassword`。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

可以通过运行以下命令（如下所示）查找群集控制台 URL：`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> 若要连接到专用的 Azure Red Hat OpenShift 群集，你需要从你所创建的虚拟网络中的主机或在与群集部署到的虚拟网络[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)的虚拟网络中执行以下步骤。

在浏览器中启动控制台 URL 并使用`kubeadmin`凭据进行登录。

![Azure Red Hat OpenShift 登录屏幕](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>安装 OpenShift CLI

登录到 OpenShift Web 控制台后，单击 **？** 在右上方，然后单击 "**命令行工具**"。 下载适用于你的计算机的版本。

![Azure Red Hat OpenShift 登录屏幕](media/aro4-download-cli.png)

你还可以从<https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>下载适用于你的计算机的最新版本的 CLI。

## <a name="connect-using-the-openshift-cli"></a>使用 OpenShift CLI 进行连接

检索 API 服务器的地址。

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> 若要连接到专用的 Azure Red Hat OpenShift 群集，你需要从你所创建的虚拟网络中的主机或在与群集部署到的虚拟网络[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)的虚拟网络中执行以下步骤。

使用以下命令登录到 OpenShift 群集的 API 服务器。 将** \<kubeadmin password>** 替换为你刚检索的密码。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>后续步骤

在本文中，部署了一个运行 OpenShift 4 的 Azure Red Hat OpenShift 群集。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 部署群集
> * 使用`kubeadmin`用户连接到群集

转到下一篇文章，了解如何使用 Azure Active Directory 配置群集以进行身份验证。


* [使用命令行 Azure Active Directory 配置身份验证](configure-azure-ad-cli.md)


* [使用 Azure 门户和 OpenShift web 控制台配置 Azure Active Directory 的身份验证](configure-azure-ad-cli.md)
