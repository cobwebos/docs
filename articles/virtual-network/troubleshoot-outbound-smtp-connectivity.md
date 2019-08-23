---
title: 在 Azure 中排查出站 SMTP 连接问题 | Microsoft Docs
description: 了解如何在 Azure 中排查出站 SMTP 连接问题。
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 64cf633d50fc81ae8d53f2b4ee2a9975a756f0c7
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972480"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>在 Azure 中排查出站 SMTP 连接问题

从 2017 年 11 月 15 日开始，从虚拟机 (VM) 直接发送到外部域（例如 outlook.com 和 gmail.com）的出站电子邮件将仅适用于 Microsoft Azure 中的某些订阅类型。 将阻止使用 TCP 端口 25 的出站 SMTP 连接。 （端口 25 主要用于未经身份验证的电子邮件传递。）

此行为更改仅适用于 2017 年 11 月 15 日及以后的新订阅和新部署。

## <a name="recommended-method-of-sending-email"></a>推荐的电子邮件发送方法
建议使用经过身份验证的 SMTP 中继服务（通常通过 TCP 端口 587 或 443 连接，但也支持其他端口），从 Azure VM 或 Azure 应用服务发送电子邮件。 这些服务用于维护 IP 或域信誉，以尽量降低第三方电子邮件提供商拒绝邮件的可能性。 此类 SMTP 中继服务包括但不限于 [SendGrid](https://sendgrid.com/partners/azure/)。 也可以使用本地运行的安全的 SMTP 中继服务。

在 Azure 中使用这些电子邮件传递服务不受限制（无论是哪种订阅类型）。

## <a name="enterprise-agreement"></a>企业协议
企业协议 Azure 用户仍然无需使用经身份验证的中继便可发送电子邮件。 新的和现有的企业协议用户均可以尝试从 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 虽然不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件，但对于企业协议订阅中的 VM，Azure 平台不会阻止发送尝试。 必须直接与电子邮件提供商接洽，以修复涉及特定提供商的所有邮件发送或垃圾邮件筛选问题。

## <a name="pay-as-you-go"></a>即用即付
如果已在 2017 年 11 月 15 日前注册即用即付或 Microsoft 合作伙伴网络订阅产品/服务，则依然能够尝试发送出站电子邮件。 将能够继续尝试从这些订阅中的 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 同样，不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件，用户需要直接与电子邮件提供商协作，以处理涉及特定提供商的所有邮件发送或垃圾邮件筛选问题。

在 2017 年 11 月 15 日之后创建的即用即付或 Microsoft 合作伙伴网络订阅中存在一个技术限制，它会阻止这些订阅中的 VM 直接发送电子邮件。 如需将电子邮件从 Azure VM 直接发送到外部电子邮件提供商（不使用经身份验证的 SMTP 中继），可以请求去除该限制。 Microsoft 将会根据自行的判断审批请求，并且只会在执行额外的防欺诈检查后才授予权限。 若要发出请求, 请使用以下问题类型打开支持案例:**技术** **虚拟网络** **连接** **无法发送电子邮件 (SMTP/端口 25)。**  >  >  >  请确保添加详细信息，解释为什么要直接向电子邮件提供商发送邮件而不是使用经身份验证的中继。

在从 Azure 门户中免除了即用即付或 Microsoft 合作伙伴网络订阅并且 Vm 已从 "启动" &, 将免除该订阅中的所有 Vm。 豁免仅适用于请求的订阅, 并且仅适用于直接路由到 internet 的虚拟机流量。 不支持通过 Azure PaaS 服务 (例如[Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)) 路由端口25的流量。

> [!NOTE]
> 如果确定发生违反服务条款的情况，Microsoft 保留撤销此豁免的权利。

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN、Azure Pass、Azure 开放许可、教育、BizSpark 和免费试用版
2017 年 11 月 15 日后创建的 MSDN、Azure Pass、Azure 开放许可、Azure 教育、BizSpark、Azure 赞助、Azure Student、免费试用版或任何 Visual Studio 订阅均存在技术限制，会阻止从这些订阅中的 VM 直接向电子邮件提供商发送电子邮件。 此限制是为了防止滥用邮件。 不接受去除此限制的请求。

如果你正在使用这些订阅类型, 则建议你使用 SMTP 中继服务 (如本文前面所述), 或更改你的订阅类型。

## <a name="cloud-service-provider-csp"></a>云服务提供商 (CSP)

如果通过 CSP 使用 Azure 资源, 则可以请求 CSP 在不能使用安全的 SMTP 中继的情况下, 通过 Microsoft 代表你创建取消阻止豁免请求。

## <a name="need-help-contact-support"></a>需要帮助？ 联系技术支持

如果仍需要帮助,[请联系支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)人员, 使用以下问题类型快速解决你的问题:**订阅管理**问题类型:**请求启用端口25电子邮件流**。
