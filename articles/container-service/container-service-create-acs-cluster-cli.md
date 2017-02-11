---
title: "通过 CLI 部署 Azure 容器服务群集 | Microsoft Docs"
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
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>使用 Azure CLI 2.0 预览版创建 Azure 容器服务群集

若要创建 Azure 容器服务群集，则需要：
* 一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）
* 已安装 [Azure CLI v.2.0（预览版）](https://github.com/Azure/azure-cli#installation)
* 登录到 Azure 帐户（如下所示）

## <a name="log-in-to-your-account"></a>登录到你的帐户
```azurecli
az login 
```
使用 CLI 中提供的设备代码进行身份验证需要转到此[链接](https://login.microsoftonline.com/common/oauth2/deviceauth)。

![键入命令](media/container-service-create-acs-cluster-cli/login.png)

![浏览器](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>创建资源组
```azurecli
az group create -n acsrg1 -l "westus"
```

![创建映像资源组](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>可用 Azure 容器服务 CLI 命令列表

```azurecli
az acs -h
```

![ACS 命令用法](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>创建 Azure 容器服务群集

在 CLI 中创建 ACS 的用法

```azurecli
az acs create -h
```
容器服务的名称、在上一步中创建的资源组和唯一的 DNS 名称都是必需的。 除非使用各自的开关进行覆盖，其他输入都设置为默认值（请查看屏幕下方的帮助快照）。
![有关创建映像 ACS 的帮助](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

使用默认值快速创建 ACS。如果没有 SSH 密钥，请使用第二个命令。具有 the --generate-ssh-keys 开关的第二个 create 命令将为你创建一个 ACS

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

请确保 dns 前缀 (-d 开关) 唯一。如果遇到错误，请使用唯一字符串重试。

键入前一个命令后，需要大约 10 分钟等待群集创建。

![映像 ACS 创建](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>列出 ACS 群集 

### <a name="under-a-subscription"></a>在订阅下

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>在特定资源组中

```azurecli
az acs list -g acsrg1 --output table
```

![映像 ACS 列表](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>显示容器服务群集的详细信息

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![映像 ACS 列表](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>缩放 ACS 群集
允许缩小和扩大。参数 new-agent-count 是 ACS 群集中新的代理数。

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![映像 ACS 比例](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>删除容器服务群集
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
请注意，此 delete 命令不会删除在创建容器服务过程中创建的所有资源（网络和存储）。若要删除所有资源，建议按资源组创建单个 ACS 群集，不再需要 ACS 群集时删除资源组本身以确保所有相关资源都已删除，无需为其付费。



<!--HONumber=Nov16_HO3-->


