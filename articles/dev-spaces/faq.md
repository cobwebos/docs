---
title: 常见问题 Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: 查找有关 Azure Dev Spaces 的一些常见问题的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305972"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>常见问题 Azure Dev Spaces

这解决了有关 Azure Dev Spaces 的常见问题。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 区域当前提供 Azure Dev Spaces？

有关可用区域的完整列表，请参阅[支持的区域和配置][supported-regions]。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>是否可以使用没有公共 IP 地址的 Azure Dev Spaces？

不能，无法在没有公共 IP 的 AKS 群集上预配 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公共 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>能否将我自己的入口用于 Azure Dev Spaces？

是的，你可以配置自己的入口，Azure Dev Spaces 创建一个。 例如，可以使用[traefik][ingress-traefik]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>是否可以将 HTTPS 与 Azure Dev Spaces 一起使用？

是的，你可以使用[traefik][ingress-https-traefik]配置你自己的入口和 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>能否在使用 CNI 而不是 kubenet 的群集上使用 Azure Dev Spaces？ 

是的，可以在使用 CNI 进行网络连接的 AKS 群集上使用 Azure Dev Spaces。 例如，可以在具有[现有 Windows 容器][windows-containers]的 AKS 群集上使用 Azure Dev Spaces，使用 CNI 进行网络处理。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>是否可以将 Azure Dev Spaces 与 Windows 容器一起使用？

目前，Azure Dev Spaces 仅在 Linux pod 和节点上运行，但你可以使用[现有的 Windows 容器][windows-containers]在 AKS 群集上运行 Azure Dev Spaces。


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md