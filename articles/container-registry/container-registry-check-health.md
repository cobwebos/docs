---
title: 检查 Azure 容器注册表中的注册表运行状况
description: 了解如何运行快速诊断命令来识别使用 Azure 容器注册表时遇到的常见问题, 包括本地 Docker 配置和注册表连接
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3511655d220ee85ce6b5744612e5d6fddafbe877
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309733"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>检查 Azure 容器注册表的运行状况

使用 Azure 容器注册表时, 有时可能会遇到问题。 例如, 你可能无法请求容器映像, 因为你的本地环境中的 Docker 存在问题。 或者, 网络问题可能会阻止您连接到注册表。 

作为第一个诊断步骤, 请运行[az acr 检查运行状况][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli]。

## <a name="run-az-acr-check-health"></a>运行 az acr 检查运行状况

下面的示例演示了运行命令的`az acr check-health`不同方法。

> [!NOTE]
> 如果在 Azure Cloud Shell 中运行命令, 则不会检查本地环境。 不过, 您可以检查对目标注册表的访问权限。

### <a name="check-the-environment-only"></a>仅检查环境

若要检查本地 Docker 守护程序、CLI 版本和 Helm 客户端配置, 请运行命令, 无需其他参数:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>检查环境和目标注册表

若要检查对注册表的访问以及执行本地环境检查, 请传递目标注册表的名称。 例如：

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>错误报告

命令将信息记录到标准输出。 如果检测到问题, 则会提供错误代码和说明。 有关代码和可能的解决方案的详细信息, 请参阅[错误引用](container-registry-health-error-reference.md)。

默认情况下, 此命令将在发现错误时停止。 你还可以运行命令, 以便它提供所有运行状况检查的输出, 即使发现错误。 `--ignore-errors`添加参数, 如以下示例中所示:

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

有关[az acr 检查运行状况][az-acr-check-health]命令返回的错误代码的详细信息, 请参阅[运行状况检查错误引用](container-registry-health-error-reference.md)。

请参阅常见问题和 Azure 容器注册表的其他已知问题的[常见问题解答](container-registry-faq.md)。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
