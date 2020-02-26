---
title: Azure 安全中心常见问题解答-一般问题
description: 有关 Azure 安全中心的常见问题，一种有助于防止、检测和响应威胁的产品
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b13e5e0000cdd8e33f459400cc4c24d107ccaba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604798"
---
# <a name="faq---general-questions-about-azure-security-center"></a>常见问题解答-有关 Azure 安全中心的一般问题

## <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
Azure 安全中心可帮助预防、检测和响应威胁，同时提高资源的可见性并控制其安全性。 该服务提供订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

安全中心使用 Microsoft Monitoring Agent 来收集和存储数据。 有关详细信息，请参阅[Azure 安全中心中的数据收集](security-center-enable-data-collection.md)。


## <a name="how-do-i-get-azure-security-center"></a>如何获取Azure 安全中心？
Azure 安全中心通过 Microsoft Azure 订阅启用，可从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问。 若要访问它，请[登录到门户](https://portal.azure.com)，选择 "**浏览**"，并滚动到 "**安全中心**"。


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>哪些 Azure 资源由 Azure 安全中心监视？
Azure 安全中心监视以下 Azure 资源：

* 虚拟机 (VM)（包括 [云服务](../cloud-services/cloud-services-choose-me.md)）
* 虚拟机规模集
* Azure 虚拟网络
* Azure SQL 服务
* Azure 存储帐户
* [应用服务环境](../app-service/environment/intro.md) 中的 Azure Web 应用
* 与 Azure 订阅集成的合作伙伴解决方案，例如 VM 和应用服务环境上的 Web 应用程序防火墙

此外，Azure 安全中心还可以监视非 Azure （包括本地）计算机（支持[Windows 计算机](./quick-onboard-windows-computer.md)和[Linux 计算机](./quick-onboard-linux-computer.md)）


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>如何查看 Azure 资源当前安的全状态？
"**安全中心概述**" 页显示了你的环境的总体安全状况，按计算、网络、存储 & 数据和应用程序细分。 每个资源类型都有一个指示符，该指示符显示是否已发现潜在安全漏洞。 单击每个磁贴可显示安全中心发现的安全问题列表和订阅中的资源清单。



## <a name="what-is-a-security-policy"></a>什么是安全策略？
安全策略定义一组控件，这些控件是针对指定订阅中的资源建议的。 在 Azure 安全中心，用户需根据公司安全要求和应用程序类型或每个订阅中数据的敏感性，为 Azure 订阅定义策略。

Azure 安全中心中启用的安全策略将使用安全建议和监视。 若要了解有关安全策略的详细信息，请参阅[在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)。


## <a name="who-can-modify-a-security-policy"></a>哪些用户可以修改安全策略？
若要修改安全策略，用户必须是安全管理员或是该订阅的所有者或参与者。

若要了解如何配置安全策略，请参阅[在 Azure 安全中心设置安全策略](tutorial-security-policy.md)。


## <a name="what-is-a-security-recommendation"></a>什么是安全建议？
Azure 安全中心可分析 Azure 资源的安全状态。 发现潜在的安全漏洞后会生成建议。 建议会对所需控件的整个配置过程提供指导。 示例如下：

* 预配反恶意软件可帮助识别和删除恶意软件
* 配置[网络安全组](../virtual-network/security-overview.md)和规则来控制发送到虚拟机的流量
* 设置 web 应用程序防火墙，帮助抵御针对 web 应用程序的攻击
* 部署缺少的系统更新
* 解决与推荐基线不匹配的操作系统配置

安全策略中仅已启用的推荐操作会显示在此处。



## <a name="what-triggers-a-security-alert"></a>什么会触发安全警报？
Azure 安全中心自动从 Azure 资源、网络和合作伙伴解决方案（例如恶意软件和防火墙）收集、分析和融合数据。 检测到威胁时会创建安全警报。 示例中包括的检测项：

* 与已知的恶意 IP 地址通信的不符合安全性的虚拟机
* 使用 Windows 错误报告检测到的高级恶意软件
* 对虚拟机的暴力破解攻击
* 来自集成合作伙伴解决方案（例如反恶意软件或 Web 应用程序防火墙）的安全警报


## 为什么安全分数值发生了变化？ <a name="secure-score-faq"></a>
从2019年2月起，安全中心调整了几项建议的分数，以便更好地满足其严重性。 由于此调整，可能会更改整体安全分数值。  有关安全分数的详细信息，请参阅[安全分数计算](security-center-secure-score.md)。


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft 安全响应中心与 Azure 安全中心检测和警示的威胁之间有何区别？
Microsoft 安全响应中心 (MSRC) 会执行 Azure 网络和基础结构的选择安全监视，并接收来自第三方的威胁情报和恶意投诉。 MSRC 发现不合法或未经授权的某一方访问客户数据或客户使用 Azure 不符合可接受的使用条款时，安全事件管理器会通知客户。 通常会以电子邮件方式向 Azure 安全中心中指定的安全联系人或 Azure 订阅所有者（如果未指定安全联系人）发送通知。

安全中心作为 Azure 的一项服务，可持续监视客户的 Azure 环境，并应用分析来自动广泛地检测潜在的恶意活动。 这些检测结果会作为安全警报显示在安全中心仪表板中。