---
title: "保护 Azure SQL 服务和 Azure 安全中心中的数据 |Microsoft 文档"
description: "本文档说明在 Azure 安全中心中的建议，帮助你保护数据和 Azure SQL 服务，并保持符合安全策略。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>保护 Azure SQL 服务和 Azure 安全中心中的数据
Azure 安全中心分析你的 Azure 资源的安全状态。 当安全中心标识潜在的安全漏洞时，它将创建指导你完成配置所需的控件的过程的建议。  建议均适用于 Azure 的资源类型： 虚拟机 (Vm)、 网络、 SQL 和数据，以及应用程序。

本文介绍了适用于 Azure SQL 服务和数据的建议。 建议以启用审核的 Azure SQL 服务器和数据库，启用 SQL 数据库的加密和你的 Azure 存储帐户启用加密为中心。  使用下的表作为参考来帮助你了解可用的 SQL 服务和数据建议和每个用途如果将其应用。

## <a name="available-sql-service-and-data-recommendations"></a>可用的 SQL 服务和数据建议
| 建议 | 描述 |
| --- | --- |
| [启用 SQL server 上的审核和威胁检测](security-center-enable-auditing-on-sql-servers.md) |建议你打开的 Azure SQL 服务器 （Azure SQL 服务仅; 不包括 SQL 虚拟机上运行） 的审核和威胁检测。 |
| [启用 SQL 数据库审核和威胁检测](security-center-enable-auditing-on-sql-databases.md) |建议你打开的 Azure SQL 数据库 （Azure SQL 服务仅; 不包括 SQL 虚拟机上运行） 的审核和威胁检测。 |
| [在 SQL 数据库上启用透明数据加密](security-center-enable-transparent-data-encryption.md) |建议你启用 SQL 数据库 （仅限 Azure SQL 服务） 的加密。 |

## <a name="see-also"></a>另请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下：

* [保护 Azure 安全中心中的虚拟机](security-center-virtual-machine-recommendations.md)
* [保护你在 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护你的网络在 Azure 安全中心](security-center-network-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)-了解如何配置你的 Azure 订阅和资源组的安全策略。
* [管理和响应与在 Azure 安全中心中的安全警报](security-center-managing-and-responding-alerts.md)-了解如何管理和响应的安全警报。
* [Azure 安全中心常见问题](security-center-faq.md)-查找有关使用服务的常见问题。
