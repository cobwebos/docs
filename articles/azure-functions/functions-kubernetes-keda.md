---
title: 在 Kubernetes 上通过 KEDA Azure Functions
description: 了解如何使用基于 KEDA 的 Kubernetes 事件驱动自动缩放，在云中或本地的 Kubernetes 中运行 Azure Functions。
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 5c050eb38e47ce2ab9d11e5c92eb7bdd3ac8e572
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121688"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>在 Kubernetes 上通过 KEDA Azure Functions

Azure Functions 运行时可在托管位置和所需的位置提供灵活性。  [KEDA](https://keda.sh) （基于 Kubernetes 的事件驱动自动缩放），可与 Azure Functions 运行时和工具无缝结合，以提供 Kubernetes 中的事件驱动规模。

## <a name="how-kubernetes-based-functions-work"></a>基于 Kubernetes 的函数的工作原理

Azure Functions 服务由两个关键组件组成：运行时和缩放控制器。  函数运行时运行并执行您的代码。  运行时包括如何触发、记录和管理函数执行的逻辑。  Azure Functions 运行时可以在*任何位置*运行。  另一个组件是缩放控制器。  缩放控制器监视以函数为目标的事件的速率，并主动调整运行应用程序的实例数。  若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md)。

基于 Kubernetes 的函数在[Docker 容器](functions-create-function-linux-custom-image.md)中提供函数运行时，并通过 KEDA 进行事件驱动缩放。  KEDA 可以扩展到0个实例（没有事件发生时）和*n*个实例。 它通过公开 Kubernetes 自动缩放程序的自定义指标（水平 Pod 自动缩放程序）来实现此功能。  将函数容器与 KEDA 结合使用，可以在任何 Kubernetes 群集中复制无服务器函数功能。  还可以使用[Azure Kubernetes Services （AKS）虚拟节点](../aks/virtual-nodes-cli.md)功能为无服务器基础结构部署这些函数。

## <a name="managing-keda-and-functions-in-kubernetes"></a>在 Kubernetes 中管理 KEDA 和函数

若要在 Kubernetes 群集上运行函数，必须安装 KEDA 组件。 您可以使用[Azure Functions Core Tools](functions-run-local.md)安装此组件。

### <a name="installing-with-helm"></a>通过 Helm 安装

可以通过多种方式在任何 Kubernetes 群集中安装 KEDA，包括 Helm。  [KEDA 站点](https://keda.sh/docs/deploy/)上介绍了部署选项。

## <a name="deploying-a-function-app-to-kubernetes"></a>将 function app 部署到 Kubernetes

可以将任何函数应用部署到运行 KEDA 的 Kubernetes 群集。  由于函数在 Docker 容器中运行，因此，项目需要 `Dockerfile` 。  如果还没有，可以通过在函数项目的根目录中运行以下命令来添加 Dockerfile：

```cli
func init --docker-only
```

若要生成映像并将函数部署到 Kubernetes，请运行以下命令：

> [!NOTE]
> 核心工具将利用 docker CLI 生成并发布映像。 请确保已安装 docker，并使用连接到你的帐户 `docker login` 。

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 将 `<name-of-function-deployment>` 替换为你的函数应用的名称。

这会创建 Kubernetes `Deployment` 资源、 `ScaledObject` 资源和 `Secrets` ，其中包括从文件导入的环境变量 `local.settings.json` 。

### <a name="deploying-a-function-app-from-a-private-registry"></a>从专用注册表部署函数应用

上述流也适用于私有注册表。  如果要从专用注册表拉取容器映像，请在 `--pull-secret` 运行时包括引用持有专用注册表凭据的 Kubernetes 机密的标志 `func kubernetes deploy` 。

## <a name="removing-a-function-app-from-kubernetes"></a>从 Kubernetes 中删除函数应用

部署后，可以通过删除关联的、和创建的来删除函数 `Deployment` `ScaledObject` `Secrets` 。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>从 Kubernetes 卸载 KEDA

[KEDA 站点上](https://keda.sh/deploy/)介绍了卸载 KEDA 的步骤。

## <a name="supported-triggers-in-keda"></a>KEDA 中支持的触发器

KEDA 支持以下 Azure 函数触发器：

* [Azure 存储队列](functions-bindings-storage-queue.md)
* [Azure 服务总线队列](functions-bindings-service-bus.md)
* [Azure 事件/IoT 中心](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ 队列](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 触发器支持

可以使用公开 HTTP 触发器的 Azure Functions，但 KEDA 不会直接对其进行管理。  可以利用 KEDA prometheus 触发器将[HTTP Azure Functions 从1个实例扩展到*n*个实例](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)。

## <a name="next-steps"></a>后续步骤
有关更多信息，请参见以下资源：

* [使用自定义映像创建函数](functions-create-function-linux-custom-image.md)
* [在本地对 Azure Functions 进行编码和测试](functions-develop-local.md)
* [Azure 函数消耗计划的工作原理](functions-scale.md)