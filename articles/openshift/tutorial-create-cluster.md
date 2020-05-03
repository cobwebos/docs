---
title: 教程 - 创建 Azure Red Hat OpenShift 4 群集
description: 了解如何使用 Azure CLI 创建 Microsoft Azure Red Hat OpenShift 群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509714"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>教程：创建 Azure Red Hat OpenShift 4 群集

在本教程（三个教程的第一部分）中，你将准备好环境以创建一个运行 OpenShift 4 的 Azure Red Hat OpenShift 群集，并创建一个群集。 将了解如何执行以下操作：
> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 部署群集

## <a name="before-you-begin"></a>开始之前

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.75 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

### <a name="install-the-az-aro-extension"></a>安装 `az aro` 扩展
借助 `az aro` 扩展，可以使用 Azure CLI 直接从命令行创建、访问和删除 Azure Red Hat OpenShift 群集。

运行以下命令可安装 `az aro` 扩展。

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

如果已安装了该扩展，可以通过运行以下命令进行更新。

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>注册资源提供程序

接下来，需要在订阅中注册 `Microsoft.RedHatOpenShift` 资源提供程序。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

验证该扩展是否已注册。

```azurecli-interactive
az -v
```

  应会获得类似于以下内容的输出。

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>获取 Red Hat 拉取机密（可选）

Red Hat 拉取机密使群集能够访问 Red Hat 容器注册表以及其他内容。 此步骤是可选的，但建议执行。

通过导航到 https://cloud.redhat.com/openshift/install/azure/aro-provisioned 并单击“下载拉取机密”  来获取你的拉取机密。

你将需要使用你的企业电子邮件登录 Red Hat 帐户或创建一个新的 Red Hat 帐户，并接受条款和条件。

将保存的 `pull-secret.txt` 文件保存在一个安全的位置 - 每次创建群集时都要使用该文件。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>创建包含两个空子网的虚拟网络

接下来，你将创建一个包含两个空子网的虚拟网络。

1. **设置以下变量。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **创建资源组**

    Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

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

    运行 OpenShift 4 的 Azure Red Hat OpenShift 群集需要一个包含两个空子网（用于主节点和工作器节点）的虚拟网络。

    在之前创建的同一资源组中创建新的虚拟网络。

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    以下示例输出显示已成功创建了虚拟网络：

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

3. 为主节点添加一个空子网。 

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. 为工作器节点添加一个空子网。 

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. 在主子网上[禁用子网专用终结点策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。  为了能够连接和管理群集，必须执行此操作。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>创建群集

运行以下命令以创建群集。 （可选）可以传递一个拉取机密，该机密使群集能够访问 Red Hat 容器注册表以及其他内容。 通过导航到 [Red Hat OpenShift 群集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)并单击“复制拉取机密”  ，来访问你的拉取机密。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> 创建群集通常需要 35 分钟时间。

>[!IMPORTANT]
> 如果选择指定自定义域（例如 **foo.example.com**），则 OpenShift 控制台将在诸如 `https://console-openshift-console.apps.foo.example.com` 之类的 URL（而不是内置域 `https://console-openshift-console.apps.<random>.<location>.aroapp.io`）上提供。
>
> 默认情况下，OpenShift 对 `*.apps.<random>.<location>.aroapp.io` 上创建的所有路由使用自签名证书。  如果在连接到群集后选择使用自定义 DNS，则需按照 OpenShift 文档[为入口控制器配置自定义 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html)，并[为 API 服务器配置自定义 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)。
>

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：
> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 部署群集

转到下一教程：
> [!div class="nextstepaction"]
> [连接到 Azure Red Hat OpenShift 群集](tutorial-connect-cluster.md)
