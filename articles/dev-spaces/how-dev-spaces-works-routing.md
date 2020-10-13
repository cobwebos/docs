---
title: 路由如何处理 Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的进程以及路由的工作原理
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977963"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>路由如何处理 Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 在开发环境中运行项目后，Azure Dev Spaces 为项目提供其他网络和路由功能。

本文介绍了如何将路由用于 Dev 空间。

## <a name="how-routing-works"></a>路由的工作原理

Dev 空间构建在 AKS 之上，并使用相同的 [网络概念](../aks/concepts-network.md)。 Azure Dev Spaces 还具有一个集中式 *ingressmanager* 服务，并将其自己的入口控制器部署到 AKS 群集。 *Ingressmanager*服务会监视包含 dev 空间的 AKS 群集，并在群集中扩充 Azure Dev Spaces 入口控制器，其中包含用于路由到应用程序箱的入口对象。 每个 pod 中的 devspaces 容器会根据 URL 向开发环境中的 `azds-route-as` http 流量添加 http 标头。 例如，对 URL 的请求 *http://azureuser.s.default.serviceA.fedcba09...azds.io* 将获取 HTTP 标头 `azds-route-as: azureuser` 。 如果已存在一个标头，devspaces 容器将不会添加 `azds-route-as` 标头。

当从群集外部向服务发出 HTTP 请求时，请求将发送到入口控制器。 入口控制器根据请求的入口对象和规则将请求直接路由到相应的 pod。 Pod 中的 devspaces 容器接收请求， `azds-route-as` 根据 URL 添加标头，然后将请求路由到应用程序容器。

当从群集中的其他服务向服务发出 HTTP 请求时，请求将首先通过调用服务的 devspaces 容器。 Devspaces 容器查看 HTTP 请求，并检查 `azds-route-as` 标头。 根据标头，devspaces 容器将查找与标头值相关联的服务的 IP 地址。 如果找到了 IP 地址，devspaces 容器会将请求重排到该 IP 地址。 如果找不到 IP 地址，devspaces 容器会将请求路由到父应用程序容器。

例如，应用程序 *serviceA* 和 *serviceB* 部署到名为 *default*的父开发人员空间。 *serviceA* 依赖于 *serviceB* 并向其发出 HTTP 调用。 Azure 用户基于名为*azureuser*的*默认*空间创建子开发人员空间。 Azure 用户还会将其自己的 *serviceA* 版本部署到其子空间。 向发出请求时 *http://azureuser.s.default.serviceA.fedcba09...azds.io* ：

![Azure Dev Spaces 路由](media/how-dev-spaces-works/routing.svg)

1. 入口控制器查找与 URL 关联的 pod 的 IP，即 *serviceA. azureuser*。
1. 入口控制器在 Azure 用户的开发区域中查找 pod 的 IP，并将请求路由到 *serviceA. azureuser* pod。
1. *Azureuser* pod 中的 devspaces 容器接收请求并添加 `azds-route-as: azureuser` 为 HTTP 标头。
1. *Azureuser* pod 中的 devspaces 容器将请求路由到*serviceA* pod 中的*serviceA*应用程序容器。
1. *Azureuser* pod 中的*serviceA*应用程序调用*serviceB*。 *ServiceA*应用程序还包含用于保留现有 `azds-route-as` 标头的代码，在本例中为 `azds-route-as: azureuser` 。
1. *Azureuser* pod 中的 devspaces 容器接收请求，并根据标头的值查找*serviceB*的 IP `azds-route-as` 。
1. *Azureuser* pod 中的 devspaces 容器找不到*serviceB*的 IP。
1. *Azureuser* pod 中的 devspaces 容器在父空间中查找*serviceB*的 IP，即 " *serviceB*"。
1. *Azureuser* pod 中的 devspaces 容器查找*serviceB*的 IP，并将该请求路由到*serviceB。*
1. *ServiceB* pod 中的 devspaces 容器接收请求，并将请求路由到*serviceB* pod 中的*serviceB*应用程序容器。
1. *ServiceB* pod 中的*serviceB*应用程序将响应返回到*serviceA。*
1. *Azureuser* pod 中的 devspaces 容器接收响应，并将响应路由到*serviceA* pod 中的*serviceA*应用程序容器。
1. *ServiceA*应用程序接收响应，然后返回其自己的响应。
1. *Azureuser* pod 中的 devspaces 容器接收来自*serviceA*应用程序容器的响应，并将响应路由到群集外部的原始调用方。

非 HTTP 的所有其他 TCP 流量均通过入口控制器和 devspaces-proxy 容器未修改。

## <a name="sharing-a-dev-space"></a>共享开发人员空间

与团队合作时，可以在整个团队中共享一个开发人员空间，并创建派生的开发人员空间。 具有参与者访问该开发人员空间资源组的任何人都可以使用 dev 空间。

你还可以创建派生自另一个开发人员空间的新开发人员空间。 创建派生的开发人员空间时， *azds.io/parent-space=PARENT-SPACE-NAME* 标签将添加到派生开发人员空间的命名空间中。 此外，父 dev 空间中的所有应用程序都与派生的开发人员空间共享。 如果将应用程序的更新版本部署到派生的开发人员空间，则该应用程序将仅存在于派生的开发人员空间中，并且父开发人员空间不受影响。 最多可以有三个级别的派生开发人员空间或 *祖父* 空间。

派生开发人员空间还将智能地在其自己的应用程序和从其父对象共享的应用程序之间路由请求。 路由的工作方式是：尝试将请求路由到派生的开发人员空间中的应用程序，并从父 dev 空间回退到共享应用程序。 如果应用程序不在父空间中，则路由将回退到祖父空间中的共享应用程序。

例如：
* Dev space *默认* 具有应用程序 *serviceA* 和 *serviceB*。
* 开发人员空间 *azureuser* 派生自 *默认值*。
* 将 *serviceA* 的更新版本部署到 *azureuser*。

当使用 *azureuser*时，对 *serviceA* 的所有请求都将路由到 *azureuser*中的更新版本。 对*serviceB*的请求将首先尝试路由到*serviceB*的*azureuser*版本。 由于它不存在，它将路由到*serviceB*的*默认*版本。 如果删除 *azureuser* 版本的 *serviceA* ，则对 *serviceA* 的所有请求都将回退到使用 *默认* 版本的 *serviceA*。

## <a name="next-steps"></a>后续步骤

若要查看 Azure Dev Spaces 如何使用路由提供快速迭代和开发的示例，请参阅 [使用 Azure Dev Spaces 远程调试代码的工作原理][how-it-works-remote-debugging]。


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md