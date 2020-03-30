---
title: 路由如何与 Azure 开发空间配合使用
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述为 Azure 开发空间供电的过程以及路由的工作原理
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241382"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>路由如何与 Azure 开发空间配合使用

Azure 开发人员空间为您提供了多种方法来快速迭代和调试 Kubernetes 应用程序，并与您的团队协作处理 Azure Kubernetes 服务 （AKS） 群集。 项目在开发空间中运行后，Azure 开发人员空间会为项目提供其他网络和路由功能。

本文介绍了路由如何使用开发空间。

## <a name="how-routing-works"></a>路由的工作原理

开发空间构建在 AKS 之上，使用相同的[网络概念](../aks/concepts-network.md)。 Azure 开发人员空间还具有集中*式入口管理器*服务，并将自己的入口控制器部署到 AKS 群集。 *入口管理器*服务使用开发空间监视 AKS 群集，并将群集中的 Azure 开发空间入口控制器与入口对象增强以路由到应用程序窗格。 每个窗格中的开发人员空间代理容器将 HTTP`azds-route-as`流量的 HTTP 标头添加到基于 URL 的开发空间。 例如，对 URL*http://azureuser.s.default.serviceA.fedcba09...azds.io*的请求将获得带有`azds-route-as: azureuser`的 HTTP 标头。 如果已存在`azds-route-as`，则 devspace 代理容器不会添加标头。

当从群集外部向服务发出 HTTP 请求时，该请求将转到入口控制器。 入口控制器根据其入口对象和规则将请求直接路由到相应的窗格。 窗格中的 devspace 代理容器接收请求，根据 URL 添加`azds-route-as`标头，然后将请求路由到应用程序容器。

当从群集中的另一个服务向服务发出 HTTP 请求时，请求首先通过调用服务的 devspace 代理容器。 开发空间代理容器查看 HTTP 请求并检查`azds-route-as`标头。 根据标头，开发空间代理容器将查找与标头值关联的服务的 IP 地址。 如果找到 IP 地址，则开发空间代理容器将请求重新路由到该 IP 地址。 如果未找到 IP 地址，则 devspace 代理容器将请求路由到父应用程序容器。

例如，应用程序服务*A* *和服务 B*部署到称为*默认*的父开发空间。 *服务A*依赖于*服务 B*并对此进行 HTTP 调用。 Azure 用户基于称为*azureuser*的*默认*空间创建子开发空间。 Azure 用户还将自己的*服务 A*版本部署到其子空间。 请求时*http://azureuser.s.default.serviceA.fedcba09...azds.io*：

![Azure 开发空间路由](media/how-dev-spaces-works/routing.svg)

1. 入口控制器查找与 URL 关联的 pod 的 IP，该 URL 是*serviceA.azureuser*。
1. 入口控制器在 Azure 用户的开发空间中查找 Pod 的 IP，并将请求路由到*服务 A.azureuser* pod。
1. *服务 A.azureuser*窗格中的开发人员空间代理容器接收请求并添加`azds-route-as: azureuser`为 HTTP 标头。
1. *服务 A.azureuser*窗格中的开发空间代理容器将请求路由到*服务 A.azureuser*窗格中的*服务 A*应用程序容器。
1. *服务 A.azureuser*窗格中的应用程序调用*服务 B*。 *serviceA* *serviceA*应用程序还包含用于保留现有`azds-route-as`标头的代码，在这种情况下，该标头为`azds-route-as: azureuser`。
1. *serviceA.azureuser*窗格中的开发空间代理容器接收请求，并根据标头的值查找*服务 B*的`azds-route-as`IP。
1. *服务 A.azureuser*窗格中的开发空间代理容器找不到*服务 B.azureuser*的 IP。
1. *serviceA.azureuser*窗格中的开发空间代理容器在父空间中查找服务*B*的 IP，即服务*B.default*。
1. *serviceA.azureuser*窗格中的开发人员空间代理容器查找*服务 B.default*的 IP 并将请求路由到*服务 B.default* pod。
1. *服务 B.default*窗格中的 devspace 代理容器接收请求并将请求路由到*服务 B.default* pod 中的*服务 B*应用程序容器。
1. *服务 B.default* pod 中的*服务 B*应用程序返回对*服务 A.azureuser* pod 的响应。
1. *serviceA.azureuser*窗格中的开发空间代理容器接收响应并将响应路由到*serviceA.azureuser*窗格中的*服务 A*应用程序容器。
1. *服务 应用程序*接收响应，然后返回自己的响应。
1. *serviceA.azureuser*窗格中的 devspace 代理容器接收来自*服务 A*应用程序容器的响应，并将响应路由到群集外部的原始调用方。

所有其他非 HTTP 的 TCP 流量都未经修改地通过入口控制器和开发空间代理容器。

## <a name="sharing-a-dev-space"></a>共享开发空间

与团队合作时，可以[跨整个团队共享开发空间](how-to/share-dev-spaces.md)并创建派生开发空间。 具有对开发空间资源组的贡献者访问权限的任何人都可以使用开发空间。

您还可以创建从另一个开发空间派生的新开发空间。 创建派生开发空间时 *，azds.io/parent-space=PARENT-SPACE-NAME*标签将添加到派生开发空间的命名空间中。 此外，来自父开发空间的所有应用程序都与派生的开发空间共享。 如果将应用程序的更新版本部署到派生的开发空间，则应用程序将仅存在于派生的开发空间中，并且父开发空间将不受影响。 最多可以有三个级别的派生开发空间或*祖父母*空间。

派生的开发空间还将在自己的应用程序和从其父应用程序共享的应用程序之间智能路由请求。 路由的工作原理是尝试将请求路由到派生开发空间中的应用程序，并从父开发空间返回共享应用程序。 如果应用程序不在父空间中，则路由将回退到父级空间中的共享应用程序。

例如：
* 开发空间*默认值*具有应用程序*服务 A* *和服务 B*。
* 开发空间*azureuser*派生自*默认值*。
* *服务 A*的更新版本部署到*azureuser*。

使用*azureuser*时，服务*A*的所有请求都将路由到*azureuser*中的更新版本。 对*服务B*的请求将首先尝试路由到*服务B*的*azureuser*版本。 由于它不存在，它将路由到*默认*版本的*服务B。* 如果删除*服务 A*的*azureuser*版本，则所有对*服务 A*的请求都将回退到使用服务*A*的*默认*版本。

## <a name="next-steps"></a>后续步骤

要查看 Azure 开发人员空间如何使用路由来提供快速迭代和开发的示例，请参阅[如何将开发计算机连接到开发空间工作][how-it-works-connect]原理、[使用 Azure 开发人员空间远程调试代码的工作原理][how-it-works-remote-debugging]以及[& Azure Kubernetes 服务的 GitHub 操作][pr-flow]。

要开始使用 Azure 开发人员空间的路由进行团队开发，请参阅[Azure 开发人员空间中的团队开发][quickstart-team]快速入门。

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md