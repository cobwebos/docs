---
title: 数据驻留
description: 数据驻留和有关启用了 Azure Arc 的服务器的信息 (预览版) 。
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048850"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>已启用 Azure Arc 的服务器 (预览版) ：数据驻留

本文介绍数据驻留的概念，以及如何将其应用于启用了 Azure Arc 的服务器 (预览版) 。

已启用 Azure Arc 的服务器 (预览版) 在**美国、欧洲或亚太**中**[以预览版提供](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)**。

## <a name="data-residency"></a>数据驻留

启用了 azure Arc 的服务器 (预览版) 存储 [AZURE VM 扩展](manage-vm-extensions.md) 配置设置 (也就是说，扩展) 的属性值需要在连接的计算机上尝试启用之前指定。 例如，当你启用 Log Analytics VM 扩展时，它将要求提供 Log Analytics **工作区 ID** 和 **主密钥**。

还会收集有关连接的计算机的元数据信息。 具体来说：

* 操作系统名称和版本
* 计算机名称
* 计算机的完全限定域名 (FQDN) 
* 连接的计算机代理版本

启用 Arc 的服务器 (预览) 允许指定存储数据的区域。 Microsoft 可能会复制到其他区域以进行数据恢复，但 Microsoft 不会复制或移动地理位置之外的数据。 此数据存储在配置 Azure Arc 计算机资源的区域中。 例如，如果计算机是在美国东部地区的 Arc 上注册的，则此数据存储在美国区域。

有关区域复原和合规性支持的详细信息，请参阅 [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 复原](/azure/architecture/reliability/architect)设计的详细信息。