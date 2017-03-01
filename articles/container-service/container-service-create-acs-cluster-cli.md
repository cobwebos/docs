---
title: "部署 Docker 容器群集 - Azure CLI | Microsoft 文档"
description: "使用 Azure CLI 2.0 预览版部署 Azure 容器服务群集"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: df916670743158d6a22b3f17343630114584fa08
ms.openlocfilehash: 65f1c812472f4a3b6d4a4e6fb7666a2c022af102


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>使用 Azure CLI 2.0（预览版）创建 Azure 容器服务群集

使用 Azure CLI 2.0（预览版）中的 `az acs` 命令，在 Azure 容器服务中创建和管理群集。 也可使用 [Azure 门户](container-service-deployment.md)或 Azure 容器服务 API 部署 Azure 容器服务群集。

如需 `az acs` 命令的帮助，请将 `-h` 参数传递给任何命令。 例如：`az acs create -h`。



## <a name="prerequisites"></a>先决条件
若要使用 Azure CLI 2.0（预览版）创建 Azure 容器服务群集，用户必须：
* 具有一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）
* 已安装并设置 [Azure CLI v.2.0（预览版）](/cli/azure/install-az-cli2)

## <a name="get-started"></a>入门 
### <a name="log-in-to-your-account"></a>登录到你的帐户
```azurecli
az login 
```

按照提示以交互方式登录。 如需其他登录方法，请参阅 [Azure CLI 2.0（预览版）入门](/cli/azure/get-started-with-az-cli2)。

### <a name="set-your-azure-subscription"></a>设置 Azure 订阅

如果有多个 Azure 订阅，可设置默认订阅。 例如：

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>创建资源组
建议为每个群集创建资源组。 指定一个[提供](https://azure.microsoft.com/en-us/regions/services/) Azure 容器服务的 Azure 区域。 例如：

```azurecli
az group create -n acsrg1 -l "westus"
```
输出与下面类似：

![创建资源组](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>创建 Azure 容器服务群集

若要创建群集，请使用 `az acs create`。
群集的名称以及在上一步创建的资源组的名称都是必需参数。 

除非使用各自的开关进行覆盖，其他输入都设置为默认值（请查看以下屏幕）。 例如，协调器默认设置为 DC/OS。 在没有指定的情况下，将会根据群集名称创建 DNS 名称前缀。

![az acs create 用法](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>快速 `acs create`（使用默认值）
如果在默认位置有一个 SSH 公钥文件 `id_rsa.pub`（或者为 [OS X 和 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) 创建了一个），则可使用如下所示的命令：

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
如果没有 SSH 公钥，则使用这第二个命令。 此命令与 `--generate-ssh-keys` 开关结合将创建一个公钥。

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

输入命令后，需要大约 10 分钟等待群集创建。 命令输出包括主节点和代理节点的完全限定域名 (FQDN)，以及一个用于连接到第一个主节点的 SSH 命令。 下面是简略版输出：

![映像 ACS 创建](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> [Kubernetes 演练](container-service-kubernetes-walkthrough.md)介绍了如何使用默认值通过 `az acs create` 创建 Kubernetes 群集。
>

## <a name="manage-acs-clusters"></a>管理 ACS 群集

使用其他 `az acs` 命令管理群集。 下面是一些示例。

### <a name="list-clusters-under-a-subscription"></a>在订阅下列出群集

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>列出资源组中的群集

```azurecli
az acs list -g acsrg1 --output table
```

![acs list](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>显示容器服务群集的详细信息

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs show](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>缩放群集
向内和向外缩放代理节点都是允许的。 参数 `new-agent-count` 是指 ACS 群集中代理的新数目。

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>删除容器服务群集
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
此命令不会删除在创建容器服务时创建的所有资源（网络和存储）。 若要轻松删除所有资源，建议将每个群集部署在不同资源组中。 然后，在不再需要群集时删除资源组。

## <a name="next-steps"></a>后续步骤
创建一个正常运行的群集后，请参阅以下文档了解有关连接和管理的详细信息：

* [Connect to an Azure Container Service cluster](container-service-connect.md)
* [Work with Azure Container Service and DC/OS](container-service-mesos-marathon-rest.md)
* [Work with Azure Container Service and Docker Swarm](container-service-docker-swarm.md)
* [Work with Azure Container Service and Kubernetes](container-service-kubernetes-walkthrough.md)（使用 Azure 容器服务和 Kubernetes）


<!--HONumber=Feb17_HO1-->


