---
title: 在 Kubernetes 上通过 KEDA Azure Functions
description: 了解如何使用基于 KEDA 的 Kubernetes 事件驱动自动缩放, 在云中或本地的 Kubernetes 中运行 Azure Functions。
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure 函数, 函数, 事件处理, 动态计算, 无服务器体系结构, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096098"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>在 Kubernetes 上通过 KEDA Azure Functions

Azure Functions 运行时可在托管位置和所需的位置提供灵活性。  [KEDA](https://github.com/kedacore/kore)(基于 Kubernetes 的事件驱动自动缩放), 与 Azure Functions 运行时和工具无缝结合, 可在 Kubernetes 中提供事件驱动规模。

## <a name="how-kubernetes-based-functions-work"></a>基于 Kubernetes 的函数的工作原理

Azure Functions 服务由两个关键组件组成: 运行时和缩放控制器。  函数运行时运行并执行您的代码。  运行时包括如何触发、记录和管理函数执行的逻辑。  另一个组件是缩放控制器。  缩放控制器监视以函数为目标的事件的速率, 并主动调整运行应用程序的实例数。  若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md)。

基于 Kubernetes 的函数在[Docker 容器](functions-create-function-linux-custom-image.md)中提供函数运行时, 并通过 KEDA 进行事件驱动缩放。  KEDA 可以向下扩展到0个实例 (没有事件发生时) 和多达*n*个实例。 它通过公开 Kubernetes 自动缩放程序的自定义指标 (水平 Pod 自动缩放程序) 来实现此功能。  将函数容器与 KEDA 结合使用, 可以在任何 Kubernetes 群集中复制无服务器函数功能。  还可以使用[Azure Kubernetes Services (AKS) 虚拟节点](../aks/virtual-nodes-cli.md)功能为无服务器基础结构部署这些函数。

## <a name="managing-keda-and-functions-in-kubernetes"></a>在 Kubernetes 中管理 KEDA 和函数

若要在 Kubernetes 群集上运行函数, 必须安装 KEDA 组件。 您可以使用[Azure Functions Core Tools](functions-run-local.md)安装此组件。

### <a name="installing-with-the-azure-functions-core-tools"></a>安装 Azure Functions Core Tools

默认情况下, 核心工具将同时安装 KEDA 和 Osiris 组件, 这些组件分别支持事件驱动和 HTTP 缩放。  安装使用`kubectl`在当前上下文中运行。

通过运行以下安装命令在群集中安装 KEDA:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>将 function app 部署到 Kubernetes

可以将任何函数应用部署到运行 KEDA 的 Kubernetes 群集。  由于函数在 Docker 容器中运行, 因此, 项目需要`Dockerfile`。  如果还没有, 可以通过在函数项目的根目录中运行以下命令来添加 Dockerfile:

```cli
func init --docker-only
```

若要生成映像并将函数部署到 Kubernetes, 请运行以下命令:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 将 `<name-of-function-deployment>` 替换为你的函数应用的名称。

这会创建 Kubernetes `Deployment`资源`ScaledObject` 、资源和`Secrets` `local.settings.json` , 其中包括从文件导入的环境变量。

## <a name="removing-a-function-app-from-kubernetes"></a>从 Kubernetes 中删除函数应用

部署后, 可以通过删除关联`Deployment` `ScaledObject`的、和`Secrets`创建的来删除函数。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>从 Kubernetes 卸载 KEDA

可以运行以下 core tools 命令从 Kubernetes 群集中删除 KEDA:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>KEDA 中支持的触发器

KEDA 目前处于 beta 阶段, 支持以下 Azure 函数触发器:

* [Azure 存储队列](functions-bindings-storage-queue.md)
* [Azure 服务总线队列](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [使用自定义映像创建函数](functions-create-function-linux-custom-image.md)
* [在本地对 Azure Functions 进行编码和测试](functions-develop-local.md)
* [Azure 函数消耗计划的工作原理](functions-scale.md)