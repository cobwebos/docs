---
title: Azure 防火墙的基础结构 FQDN
description: 了解 Azure 防火墙中的基础结构 FQDN
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130205"
---
# <a name="infrastructure-fqdns"></a>基础结构 FQDN

Azure 防火墙包含默认情况下允许的基础结构 FQDN 的内置规则集合。 这些 FQDN 特定于平台，不能用于其他目的。 

内置规则集合中包含以下服务：

- 存储平台映像存储库 (PIR) 的计算访问权限
- 托管磁盘状态存储访问权限
- Azure 诊断和日志记录 (MDS)

## <a name="overriding"></a>替代 

可以通过创建最后处理的“全部拒绝”应用程序规则集合，来替代这个内置基础结构规则集合。 该应用程序规则集合始终在基础结构规则集合之前进行处理。 默认情况下，会拒绝基础结构规则集合中不包含的任何条件。

## <a name="next-steps"></a>后续步骤

- 了解如何[部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。