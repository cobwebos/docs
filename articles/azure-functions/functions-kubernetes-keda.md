---
title: 库伯奈斯上的 Azure 函数与 KEDA
description: 了解如何使用基于 Kubernetes 的事件驱动的自动缩放在云中的 Kubernetes 或本地运行 Azure 函数。
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301669"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>库伯奈斯上的 Azure 函数与 KEDA

Azure 函数运行时提供了在所需位置和方式托管的灵活性。  [KEDA（](https://keda.sh)基于库伯奈斯的事件驱动自动缩放）与 Azure 函数运行时和工具无缝配对，以在 Kubernetes 中提供事件驱动缩放。

## <a name="how-kubernetes-based-functions-work"></a>基于库伯奈斯的功能的工作原理

Azure 函数服务由两个关键组件组成：运行时和缩放控制器。  函数运行时运行并执行代码。  运行时包括有关如何触发、记录和管理函数执行的逻辑。  Azure 函数运行时可以*在任何地方*运行。  另一个组件是比例控制器。  缩放控制器监视针对函数的事件速率，并主动缩放运行应用的实例数。  若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md)。

基于库伯奈斯的功能在[Docker 容器](functions-create-function-linux-custom-image.md)中提供函数运行时，通过 KEDA 提供事件驱动的缩放。  KEDA 可以扩展到 0 个实例（当没有发生事件时）和实例到*n*个实例。 它通过公开库伯内斯自动缩放器（水平 Pod 自动缩放器）的自定义指标来进行此工作。  使用与 KEDA 一起使用函数容器可以复制任何 Kubernetes 群集中的无服务器函数功能。  这些功能也可以使用 Azure [Kubernetes 服务 （AKS） 虚拟节点](../aks/virtual-nodes-cli.md)功能部署，用于无服务器基础结构。

## <a name="managing-keda-and-functions-in-kubernetes"></a>管理库贝内斯的KEDA和职能

要在 Kubernetes 群集上运行函数，必须安装 KEDA 组件。 您可以使用[Azure 函数核心工具](functions-run-local.md)安装此组件。

### <a name="installing-with-helm"></a>使用头盔安装

在包括Helm在内的任何库伯奈斯群集中安装 KEDA 的方法有多种。  部署选项记录在[KEDA 站点](https://keda.sh/deploy/)上。

## <a name="deploying-a-function-app-to-kubernetes"></a>将函数应用部署到库伯内斯

您可以将任何函数应用部署到运行 KEDA 的库伯内斯群集。  由于函数在 Docker 容器中运行，因此项目需要`Dockerfile`。  如果还没有，则可以通过在函数项目的根目录上运行以下命令来添加 Dockerfile：

```cli
func init --docker-only
```

要生成映像并将函数部署到库伯内斯，请运行以下命令：

> [!NOTE]
> 核心工具将利用 Docker CLI 构建和发布映像。 确保已安装 Docker 并与 帐户连接`docker login`。

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 将 `<name-of-function-deployment>` 替换为你的函数应用的名称。

这将创建 Kubernetes`Deployment`资源、`ScaledObject`资源和`Secrets`，其中包括从文件`local.settings.json`导入的环境变量。

### <a name="deploying-a-function-app-from-a-private-registry"></a>从专用注册表部署函数应用

上述流程也适用于私人注册。  如果要从专用注册表提取容器映像，请包括引用运行`--pull-secret``func kubernetes deploy`时持有私有注册表凭据的 Kubernetes 机密的标志。

## <a name="removing-a-function-app-from-kubernetes"></a>从库伯内斯中删除函数应用

部署后，可以通过删除关联的`Deployment`、、`ScaledObject`已创建的函数`Secrets`来删除函数。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>从库伯内斯卸载KEDA

卸载 KEDA 的步骤记录[在 KEDA 网站上](https://keda.sh/deploy/)。

## <a name="supported-triggers-in-keda"></a>KEDA 中支持触发器

KEDA 支持以下 Azure 函数触发器：

* [Azure 存储队列](functions-bindings-storage-queue.md)
* [Azure 服务总线队列](functions-bindings-service-bus.md)
* [Azure 事件/IoT 中心](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [兔子MQ队列](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 触发器支持

可以使用公开 HTTP 触发器的 Azure 函数，但 KEDA 不会直接管理它们。  可以利用 KEDA 参数触发器将[HTTP Azure 函数从 1 缩放到*n*个实例](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)。

## <a name="next-steps"></a>后续步骤
有关更多信息，请参见以下资源：

* [使用自定义映像创建函数](functions-create-function-linux-custom-image.md)
* [在本地对 Azure Functions 进行编码和测试](functions-develop-local.md)
* [Azure 函数使用计划的工作原理](functions-scale.md)