---
title: 数据驻留
description: 数据驻留和有关启用了 Azure Arc 的服务器的信息。
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856443"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>启用了 Azure Arc 的服务器：数据驻留

本文介绍数据驻留的概念，以及如何将其应用于启用了 Azure Arc 的服务器。

已启用 Azure Arc 的服务器可在**美国、欧洲、英国、澳大利亚和亚太**中**[找到](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)**。

## <a name="data-residency"></a>数据驻留

启用了 azure Arc 的服务器存储 [AZURE VM 扩展](manage-vm-extensions.md) 配置设置 (也就是说，扩展) 的属性值需要在连接的计算机上尝试启用之前指定。 例如，当你启用 Log Analytics VM 扩展时，它将要求提供 Log Analytics **工作区 ID** 和 **主密钥**。

还会收集有关连接的计算机的元数据信息。 尤其是在下列情况下：

* 操作系统名称和版本
* 计算机名称
* 计算机的完全限定域名 (FQDN) 
* 连接的计算机代理版本

启用 Arc 的服务器允许指定存储数据的区域。 Microsoft 可能会复制到其他区域以进行数据恢复，但 Microsoft 不会复制或移动地理位置之外的数据。 此数据存储在配置 Azure Arc 计算机资源的区域中。 例如，如果计算机是在美国东部地区的 Arc 上注册的，则此数据存储在美国区域。

有关区域复原和合规性支持的详细信息，请参阅 [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 复原](/azure/architecture/reliability/architect)设计的详细信息。