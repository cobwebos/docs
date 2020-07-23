---
title: 使用 Azure 安全中心加强安全态势 | Microsoft Docs
description: 本文可帮助你通过监视 Azure 安全中心的资源来加强安全态势。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: d18258d62267c931a39947611dda038ff6e65205
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84718693"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>使用 Azure 安全中心加强安全态势
本文可帮助你加强安全态势。 使用 Azure 安全中心的监视功能，确保资源安全性尽可能严格，并监视策略符合性。

## <a name="how-do-you-strengthen-your-security-posture"></a>如何加强安全态势？
通常可以认为，监视就是指观察并等待某个事件的发生，以便采取应对措施。 加强安全态势是指制定前瞻性策略对资源进行审核，确定不符合组织标准或最佳做法的系统。

为订阅的资源启用[安全策略](tutorial-security-policy.md)之后，安全中心将分析资源的安全性，以识别潜在的漏洞。 可立即提供有关网络配置的信息。 根据安装了代理的 VM 和计算机的数目，在可以使用前可能需要一小时或更长的时间来收集 VM 和计算机配置信息，例如安全更新状态和操作系统配置。 可以在“建议”磁贴中查看完整的问题列表和强化网络和缓解风险的方式。

可以根据资源类型查看资源的安全状态以及任何问题：

- 若要监视计算机资源和应用的运行状况，并收到提高其安全性的建议，请参阅[如何在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-protection.md)
- 若要监视网络资源，例如虚拟机、网络安全组和终结点，并收到提高其安全性的建议，请参阅[在 Azure 安全中心保护网络](security-center-network-recommendations.md)，了解详细信息。 
- 若要监视数据和存储资源，例如 SQL 服务器和存储帐户，并收到提高其安全性的建议，请参阅[在 Azure 安全中心保护 Azure SQL 服务和数据](security-center-sql-service-recommendations.md)，了解详细信息。 
- 若要监视标识和访问资源，包括 MFA 和帐户权限，并收到提高其安全性的建议，请参阅[在 Azure 安全中心监视标识和访问](security-center-identity-access.md)，了解详细信息。 
- 若要监视资源的实时访问，请参阅[使用实时功能管理虚拟机访问](security-center-just-in-time.md)，了解详细信息。


如需详细了解如何应用建议，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。



![资源安全运行状况磁贴](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>另请参阅
本文介绍了如何使用 Azure 安全中心的监视功能。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何在 Azure 安全中心配置安全设置。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状况。