---
title: "将公司 Internet 域指向流量管理器域名 |Microsoft 文档"
description: "本文将帮助你将公司域名指向流量管理器域名。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 0322b3510cfd4f94031d8c1db8f1cc032b997fa8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>将公司 Internet 域指向 Azure Traffic Manager 域

在创建 Traffic Manager 配置文件时，Azure 会自动分配该配置文件的 DNS 名称。 若要从 DNS 区域中使用的名称，创建将映射到 Traffic Manager 配置文件域名的 CNAME DNS 记录。 你可以查找流量管理器中的域名**常规**配置页上的流量管理器配置文件的部分。

例如，若要名称 www.contoso.com 指向流量管理器 DNS 名称 contoso.trafficmanager.net，将创建以下 DNS 资源记录：

    www.contoso.com IN CNAME contoso.trafficmanager.net

所有流量都请求到*www.contoso.com*获取定向到*contoso.trafficmanager.net*。

> [!IMPORTANT]
> 你不能指向第二级域，如*contoso.com*，到 Traffic Manager 域。 DNS 协议标准不允许第二级域名的 CNAME 记录。

## <a name="next-steps"></a>后续步骤

* [流量管理器路由方法](traffic-manager-routing-methods.md)
* [流量管理器-禁用、 启用或删除配置文件](disable-enable-or-delete-a-profile.md)
* [流量管理器-禁用或启用终结点](disable-or-enable-an-endpoint.md)
