---
title: 什么是安全虚拟中心？
description: 了解安全虚拟中心
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518432"
---
# <a name="what-is-a-secured-virtual-hub"></a>什么是安全虚拟中心？

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

虚拟中心是 Microsoft 管理的虚拟网络，支持从其他资源进行连接。 从 Azure 门户中的虚拟 WAN 创建虚拟中心时，将创建虚拟中心 VNet 和网关（可选）。

*安全*虚拟中心是[Azure 虚拟广域网中心](../virtual-wan/virtual-wan-about.md#resources)，具有 Azure 防火墙管理器配置的关联安全和路由策略。 使用安全的虚拟集线器轻松创建具有本地安全服务的集线器和传输式体系结构，以便进行流量治理和保护。 

您可以将安全的虚拟中心用作托管中央 VNet，无需预连接。 它取代了以前 Azure 防火墙部署所需的中央 VNet。 由于安全的虚拟集线器提供自动路由，因此无需配置自己的 UDR（用户定义的路由）来路由通过防火墙的流量。

也可以将安全的虚拟集线器用作完整虚拟 WAN 体系结构的一部分。 此体系结构提供到 Azure 并通过 Azure 的安全、优化和自动化分支连接。 您可以选择用于保护和管理网络流量的服务，包括 Azure 防火墙和其他第三方安全作为服务 （SECaaS） 提供商。

## <a name="create-a-secured-virtual-hub"></a>创建安全虚拟中心

在 Azure 门户中使用防火墙管理器，可以创建新的安全虚拟中心，也可以转换以前使用 Azure 虚拟 WAN 创建的现有虚拟中心。

## <a name="next-steps"></a>后续步骤

要创建安全的虚拟中心并使用它来保护和管理集线器和分支网络，请参阅[教程：使用 Azure 门户使用 Azure 防火墙管理器保护云网络](secure-cloud-network.md)。