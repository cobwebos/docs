---
title: 在 Azure 中国世纪互联管理性能和连接 | Microsoft Docs
description: 在 Microsoft Azure 中国世纪互联部署和操作应用程序或工作负荷时，我们建议执行性能和网络测试。 如果 Azure 应用程序为中国境外的用户提供服务，则还要注意其他事项。
services: china
cloud: na
documentationcenter: na
author: v-wimarc
manager: edprice
ms.assetid: na
ms.service: china
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: v-wimarc
ms.openlocfilehash: 51c3e610468a8721d05e3a14cf2215a5d0295b4d
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
ms.locfileid: "23635188"
---
# <a name="manage-performance-and-connectivity"></a>管理性能和连接
在世纪互联运营的 Microsoft Azure（Azure 中国世纪互联）中部署和操作应用程序或工作负荷时，我们建议执行性能和网络测试。 此外，如果 Azure 应用程序为中国境外的用户提供服务，请注意以下事项：
- 对于中国境内的用户，请将工作负荷托管在 Microsoft Azure 中国世纪互联中。
- 对于中国境外的用户，请将工作负荷部署到最靠近的 Azure 区域。

## <a name="performance-considerations"></a>性能注意事项
微调并优化 Azure 应用程序，执行性能测试，并考虑以下建议： 
- 在中国标准时间而不是自己所在的时区执行性能测试。 
- 在中国大陆执行测试可以更好地反映响应时间和实际用户体验。
- 在 Internet 高峰期执行测试，测量应用程序在承受较高工作负荷时的性能。 

## <a name="network-latency-in-china"></a>中国的网络延迟
由于中国运用了管制跨界 Internet 流量的中间技术，中国与世界其他国家/地区之间的网络延迟不可避免。 网站用户和管理员可能会遇到性能变慢的问题。 以下提示可能会有帮助： 
- 对于包含流媒体和其他富媒体内容的网站，[Azure 内容交付网络](/azure/china/china-get-started-service-cdn) (CDN) 也许有助于提高响应度。 根据中国法律，在中国使用 CDN 服务也可能需要针对海外网站提交 [ICP 备案](/azure/china/china-overview-policies)。 不要使用在中国大陆没有接入点 (PoP) 的全球 CDN 服务。
- 为获得最佳用户体验，请在中国境内托管网站，为中国境内的用户提供服务。
- 中国境外的网站管理员可以使用安全外壳 (SSH) 连接到远程服务器，以便与 Microsoft Azure 中国世纪互联建立更快的网络连接。 例如，使用 SSH 访问本地 Azure 虚拟机，并在该虚拟机上使用 SSH 连接到 Azure 中国世纪互联虚拟机。 

## <a name="global-connectivity-and-interoperability"></a>全局连接和互操作性
混合云可以扩展 Microsoft Azure 中国世纪互联中的应用程序或工作负荷，并提供全局连接和互操作性。 支持以下连接：
- 使用虚拟专用网络 (VPN) 或 Azure ExpressRoute，在 Azure 与中国境内的本地私有云或后端系统之间建立直接网络连接。
- 设置站点到站点 VPN，将中国境内的 Azure 站点连接到中国境外的本地位置。 不支持使用 ExpressRoute 来与外部（中国境外）站点建立直接网络连接。 全球 Azure 同样也被视为外部站点。

> [!NOTE]
> 在 Azure 托管的服务、ICP 注册的托管位置与外部位置之间设置 VPN 或 ExpressRoute 连接，需要经过中国政府部门工业和信息化部 (MIIT) 的批准。 必须向 MIIT 注册、报告，并获得其批准。 请联系[世纪互联](mailto:icpsupport@oe.21vianet.com)获得审批流程的帮助。

对于 VPN 设置，可通过世纪互联申请例外处理，并提供以下信息：
- 海外 VPN 终结点的海外 IP 地址
- VPN 协议和端口
- 海外 IP 的位置
- 海外 IP 的所有者
- 海外 IP 所有者与 Azure 客户之间的关系

如需更多详细信息，请联系[世纪互联](mailto:icpsupport@oe.21vianet.com)。

## <a name="next-steps"></a>后续步骤
- [Azure 内容交付网络](/azure/china/china-get-started-service-cdn)
- [ICP 备案](/azure/china/china-overview-policies)


