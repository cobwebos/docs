---
title: 教程 - 连接到 Azure Red Hat OpenShift 4 群集
description: 了解如何连接 Microsoft Azure Red Hat OpenShift 群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 24990087507dee09bc38418f40c72911386e5efb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469110"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>教程：连接到 Azure Red Hat OpenShift 4 群集

在本教程（我们推出的一系列教程的第二部分，共三部分）中，你将以 kubeadmin 用户身份通过 OpenShift Web 控制台连接到运行 OpenShift 4 的 Azure Red Hat OpenShift 群集。 学习如何：
> [!div class="checklist"]
> * 获取群集的 `kubeadmin` 凭据
> * 安装 OpenShift CLI
> * 使用 OpenShift CLI 连接到 Azure Red Hat OpenShift 群集

## <a name="before-you-begin"></a>开始之前

在前面的教程中，已经创建了一个 Azure Red Hat OpenShift 群集。 如果尚未完成这些步骤，因此想要逐一完成它们，请先阅读[教程 1 - 创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="connect-to-the-cluster"></a>连接到群集

可以使用 `kubeadmin` 用户登录到群集。  请运行以下命令，找到 `kubeadmin` 用户的密码。

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

以下示例输出表明密码将位于 `kubeadminPassword` 中。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

可以通过运行以下命令查找群集控制台 URL，它类似于 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`。

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

在浏览器中启动控制台 URL，使用 `kubeadmin` 凭据登录。

![Azure Red Hat OpenShift 登录屏幕](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>安装 OpenShift CLI

登录到 OpenShift Web 控制台后，单击右上角的“?”，  然后单击“命令行工具”。  下载适用于你的计算机的版本。

![Azure Red Hat OpenShift 登录屏幕](media/aro4-download-cli.png)

也可从 <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> 下载适用于你的计算机的最新 CLI 版本。

如果在 Azure Cloud Shell 中运行这些命令，请下载适用于 Linux 的最新 OpenShift 4 CLI。

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>使用 OpenShift CLI 进行连接

检索 API 服务器的地址。

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

使用以下命令登录到 OpenShift 群集的 API 服务器。 将 \<kubeadmin password> 替换为刚才检索到的密码。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：
> [!div class="checklist"]
> * 获取群集的 `kubeadmin` 凭据
> * 安装 OpenShift CLI
> * 使用 OpenShift CLI 连接到 Azure Red Hat OpenShift 群集

转到下一教程：
> [!div class="nextstepaction"]
> [删除 Azure Red Hat OpenShift 群集](tutorial-delete-cluster.md)