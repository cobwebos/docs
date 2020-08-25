---
title: 教程 - 创建 Azure Red Hat OpenShift 4 群集
description: 了解如何使用 Azure CLI 创建 Microsoft Azure Red Hat OpenShift 群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d4938d2e4649d62ab656b6854e8176fd82b59a8f
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587729"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>教程：创建 Azure Red Hat OpenShift 4 群集

在本教程（三个教程的第一部分）中，你将准备好环境以创建一个运行 OpenShift 4 的 Azure Red Hat OpenShift 群集，并创建一个群集。 将了解如何执行以下操作：
> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 部署群集

## <a name="before-you-begin"></a>开始之前

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

### <a name="verify-your-permissions"></a>验证你的权限

若要创建 Azure Red Hat OpenShift 群集，请对 Azure 订阅、Azure Active Directory 用户或服务主体验证以下权限：

|权限|包含 VNet 的资源组|执行 `az aro create` 的用户|作为 `–client-id` 传递的服务主体|
|----|:----:|:----:|:----:|
|**用户访问管理员**|X|X| |
|**参与者**|X|X|X|

### <a name="register-the-resource-provider"></a>注册资源提供程序

接下来，需要在订阅中注册 `Microsoft.RedHatOpenShift` 资源提供程序。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>获取 Red Hat 拉取机密（可选）

Red Hat 拉取机密使群集能够访问 Red Hat 容器注册表以及其他内容。 此步骤是可选的，但建议执行。

1. [导航到 Red Hat OpenShift 群集管理器门户](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)并登录。

   你将需要使用你的企业电子邮件登录 Red Hat 帐户或创建一个新的 Red Hat 帐户，并接受条款和条件。

2. 如果你是第一次创建群集，请转到 [OpenShift 产品页](https://developers.redhat.com/products/codeready-containers)。 注册后，请转到 [Red Hat OpenShift 群集管理器页](https://cloud.redhat.com/openshift/)，可以其中单击“下载拉取机密”，然后下载用于 ARO 群集的拉取机密 。

将已保存的 `pull-secret.txt` 文件保存在安全的位置。 如果需要创建包含 Red Hat 或认证合作伙伴的示例或运算符的群集，则该文件将用于每个群集创建。

运行 `az aro create` 命令时，可以使用 `--pull-secret @pull-secret.txt` 参数引用拉取机密。 从存储 `pull-secret.txt` 文件的目录执行 `az aro create`。 否则，将 `@pull-secret.txt` 替换为 `@<path-to-my-pull-secret-file>`。

如果要在其他脚本中复制拉取机密或引用它，则应该将拉取机密格式设置为有效的 JSON 字符串。

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>为群集准备自定义域（可选）

运行 `az aro create` 命令时，可以使用 `--domain foo.example.com` 参数为群集指定自定义域。

如果为群集提供自定义域，请注意以下几点：

* 创建群集后，必须在 DNS 服务器中为指定的 `--domain` 创建 2 个 DNS A 记录：
    * **api** - 指向 API 服务器
    * **\*.apps** - 指向流入量
    * 通过执行 `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` 命令检索这些值。

* OpenShift 控制台将在 URL（如 `https://console-openshift-console.apps.foo.example.com`）上可用，而在内置域 `https://console-openshift-console.apps.<random>.<location>.aroapp.io` 中不可用。

* 默认情况下，OpenShift 对 `*.apps.<random>.<location>.aroapp.io` 上创建的所有路由使用自签名证书。  如果在连接到群集后选择使用自定义 DNS，则需按照 OpenShift 文档[为入口控制器配置自定义 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html)，并[为 API 服务器配置自定义 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>创建包含两个空子网的虚拟网络

接下来，你将创建一个包含两个空子网的虚拟网络。

1. **在将执行 `az` 命令的 shell 环境中设置以下变量。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **创建资源组。**

    Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 命令创建资源组。

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

    在之前创建的同一资源组中创建新的虚拟网络：

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

5. 在主子网上[禁用子网专用终结点策略](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。 为了能够连接和管理群集，必须执行此操作。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>创建群集

运行以下命令以创建群集。 如果选择使用以下任一选项，请相应地修改命令：
* （可选）可以[传递 Red Hat 拉取机密](#get-a-red-hat-pull-secret-optional)，该机密使群集能够访问 Red Hat 容器注册表以及其他内容。 将 `--pull-secret @pull-secret.txt` 参数添加到命令中。
* 或者，也可选择[使用自定义域](#prepare-a-custom-domain-for-your-cluster-optional)。 将 `--domain foo.example.com` 参数添加到命令中，将 `foo.example.com` 替换为你自己的自定义域。

> [!NOTE]
> 如果要将任何可选参数添加到命令中，请确保对命令前一行的参数使用尾随反斜杠。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

执行 `az aro create` 命令之后，创建群集通常需要大约 35 分钟。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：
> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 部署群集

转到下一教程：
> [!div class="nextstepaction"]
> [连接到 Azure Red Hat OpenShift 群集](tutorial-connect-cluster.md)
