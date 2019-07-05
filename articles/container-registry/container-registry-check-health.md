---
title: 检查注册表在 Azure 容器注册表中的运行状况
description: 了解如何运行使用 Azure 容器注册表，其中包括本地 Docker 配置和连接到注册表时识别常见的问题的快速诊断命令
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555093"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>检查 Azure 容器注册表的运行状况

在使用 Azure 容器注册表，可能偶尔会遇到问题。 例如，可能无法在本地环境中使用 Docker 问题由于拉取容器映像。 或者，网络问题可能会阻止您连接到注册表。 

作为第一个诊断步骤中，运行[az acr 检查运行状况][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli]。

## <a name="run-az-acr-check-health"></a>运行 az acr 检查运行状况

以下示例显示运行的不同方式`az acr check-health`命令。

> [!NOTE]
> 如果在 Azure Cloud Shell 中运行命令，则不会检查本地环境。 但是，您可以检查目标注册表的访问权。

### <a name="check-the-environment-only"></a>检查环境仅

若要检查本地 Docker 守护程序、 CLI 版本和 Helm 客户端配置，请运行不使用其他参数的命令：

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>检查在环境和目标注册表

若要检查对注册表的访问，以及执行本地环境检查，传递目标注册表的名称。 例如：

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>错误报告

该命令记录到标准输出的信息。 如果检测到问题时，它提供错误代码和说明。 有关代码和可能的解决方案的详细信息，请参阅[错误参考](container-registry-health-error-reference.md)。

默认情况下，该命令将停止时发现错误。 您还可以运行该命令，以便输出用于所有运行状况检查，即使未发现错误。 添加`--ignore-errors`参数，如以下示例所示：

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

示例输出：

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>后续步骤

有关返回的错误代码的详细信息[az acr 检查运行状况][az-acr-check-health]命令，请参阅[运行状况检查错误参考](container-registry-health-error-reference.md)。

请参阅[常见问题解答](container-registry-faq.md)的一些常见问题和了解 Azure 容器注册表的其他已知的问题。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
