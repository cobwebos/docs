---
title: 什么是 Azure DNS 专用区域
description: 专用 DNS 区域概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961228"
---
# <a name="what-is-a-private-azure-dns-zone"></a>什么是专用 Azure DNS 区域

Azure 专用 DNS 提供可靠、安全的 DNS 服务来管理和解析虚拟网络中的域名，无需添加自定义 DNS 解决方案。 借助专用 DNS 区域，可以使用自定义域名而不使用当前可用的由 Azure 提供的名称。 

专用 DNS 区域中包含的记录无法从 Internet 解析。 针对专用 DNS 区域的 DNS 解析仅适用于链接到它的虚拟网络。

可以通过创建[虚拟网络链接](./private-dns-virtual-network-links.md)，将专用 DNS 区域链接到一个或多个虚拟网络。
你还可以启用[自动注册](./private-dns-autoregistration.md)功能，以自动管理虚拟网络中部署的虚拟机的 DNS 记录的生命周期。

## <a name="limits"></a>限制

若要了解可以在一个订阅中创建的专用 DNS 区域数量以及专用 DNS 区域中支持的记录集数，请参阅[Azure DNS 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>限制

* 不支持单个标记专用 DNS 区域。 专用 DNS 区域必须有两个或两个以上的标签。 例如，contoso.com 包含两个用点分隔的标签。 专用 DNS 区域最多可以有34个标签。
* 无法在专用 DNS 区域中创建区域委派（NS 记录）。 如果你打算使用子域，则可以直接创建域作为专用 DNS 区域，并将其链接到虚拟网络，无需从父区域设置 nameserver 委托。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。
