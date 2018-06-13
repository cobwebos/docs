---
title: 保护 Azure 安全中心的应用程序 | Microsoft Docs
description: 本文档介绍 Azure 安全中心中的建议，以帮助你保护 Azure 应用程序并保持符合安全策略。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23040222"
---
# <a name="protecting-your-applications-in-azure-security-center"></a>保护 Azure 安全中心中的应用程序
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需控件的过程。  适用于 Azure 资源类型的建议：虚拟机 (VM)、网络、SQL，以及应用程序。

本文介绍适用于应用程序的建议。  部署 web 应用程序防火墙的应用程序建议中心。  使用下表作为参考，以便了解可用的应用程序建议，以及应用建议后，每个建议的做法。

## <a name="available-application-recommendations"></a>可用的应用程序建议
| 建议 | 说明 |
| --- | --- |
| [添加 web 应用程序防火墙](security-center-add-web-application-firewall.md) |建议部署 web 终结点的 Web 应用程序防火墙 (WAF)。 为任何面向公众的 IP（实例级 IP 或负载均衡 IP）显示 WAF 建议，该 IP 具有与开放入站 Web 端口 (80,443) 关联的网络安全组。</br></br>安全中心建议设置 WAF，有助于防范针对虚拟机和应用服务环境上 Web 应用程序的攻击。 应用服务环境 (ASE) 是 Azure 应用服务的[高级](https://azure.microsoft.com/pricing/details/app-service/)服务计划选项，可提供完全隔离和专用的环境，以便安全地运行 Azure 应用服务应用。 若要了解有关 ASE 的详细信息，请参阅[应用服务环境文档](../app-service/environment/intro.md)。</br></br>可以通过将应用程序添加到现有的 WAF 部署来保护安全中心中的多个 Web 应用程序。 |
| [完成应用程序保护](security-center-add-web-application-firewall.md#finalize-application-protection) |若要完成 WAF 配置，则流量必须重新路由到 WAF 设备。 遵循此建议，完成必要的安装程序更改。 |

## <a name="see-also"></a>另请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [保护 Azure 安全中心中的虚拟机](security-center-virtual-machine-recommendations.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。
