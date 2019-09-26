---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266514"
---
虚拟网络网关使用称作“网关子网”的特定子网。 网关子网是虚拟网络 IP 地址范围的一部分，该范围是在配置虚拟网络时指定的。 网关子网包含虚拟网络网关资源和服务使用的 IP 地址。 


创建网关子网时，请指定子网包含的 IP 地址数。 所需的 IP 地址数目取决于要创建的 VPN 网关配置。 有些配置需要具有比其他配置更多的 IP 地址。 我们建议创建使用 /27 或 /28 的网关子网。


如果出现错误，指出地址空间与子网重叠，或者子网不包含在虚拟网络的地址空间中，请检查 VNet 地址范围。 出错的原因可能是为虚拟网络创建的地址范围中没有足够的可用 IP 地址。 例如，如果默认子网包含整个地址范围，则不会有剩余的 IP 地址用于创建更多子网。 可以调整现有地址空间中的子网以释放 IP 地址，或指定额外的地址范围并在其中创建网关子网。