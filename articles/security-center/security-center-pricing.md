---
title: Azure 安全中心层的定价
description: Azure 安全中心提供两种级别-免费和标准。 此页面显示了如何从免费升级到标准版。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: 60a88e667918533f2c507846fa75b0e036ba5262
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921277"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>升级到标准层以增强安全性
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 它可以提供针对混合云工作负载的可见性和可控性、可减小在威胁下的曝光面的积极防御功能以及有助于随时响应快速演变的网络攻击的智能检测功能。

## <a name="pricing-tiers"></a>定价层
安全中心分两个层提供：

- 一旦你首次在 Azure 门户中访问 Azure 安全中心仪表板，或者通过 API 以编程方式启用，就会在所有 Azure 订阅上启用**免费**级别。 免费层提供安全策略、持续的安全评估和切实可行的安全建议，以帮助你保护 Azure 资源。
- 标准层将免费层的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 标准层还添加了威胁防护功能，这些功能使用内置行为分析和机器学习来识别攻击和零天攻击、访问和应用程序控制，以减少网络攻击和恶意软件的暴露程度。 此外，标准层还添加了对虚拟机进行漏洞扫描。 可以免费试用标准层。 安全中心标准版支持 Azure 资源，包括 Vm、虚拟机规模集、应用服务、SQL 服务器和存储帐户。 如果你拥有 Azure 安全中心标准版，则可以选择不提供基于资源类型的支持。 

Vm 的大多数免费层安全评估以及许多标准层安全警报都需要安装 Microsoft Monitoring Agent （MMA）功能。 可以在安全中心启用自动预配，以便为 Azure Vm 自动部署代理。

## <a name="try-standard-tier-free-for-30-days"></a>免费试用标准层30天
标准层在前30天内免费。 30 天后，如果选择继续使用服务，会自动开始收取使用费用。

可以将整个 Azure 订阅升级到标准层，这会由订阅中的所有资源继承。

若要获取标准层：

1. 选择 "**安全中心**" 主菜单上的 "**定价 & 设置**"。
2. 选择要升级到 "标准" 的订阅。
3. 选择“定价层”。
4. 选择“标准层”以进行升级。
5. 单击“保存”。

[![安全中心定价](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> 若要启用所有安全中心功能，必须将标准定价层应用到包含合适虚拟机的订阅。 配置工作区的定价不会启用实时 VM 访问、自适应应用程序控件和 Azure 资源的网络检测。
>

## <a name="why-upgrade-to-standard"></a>为什么升级到标准版？
安全中心可为混合云工作负载提供增强的安全和威胁防护功能，其中包括：

- 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自多个源（包括防火墙和其他合作伙伴解决方案）的安全数据。
- **安全警报**-使用高级分析和 Microsoft Intelligent Security Graph 来获取有关不断发展的网络攻击的优势。 利用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
- **虚拟机的漏洞扫描**-可轻松地将扫描仪部署到为漏洞管理提供业界最先进解决方案的所有虚拟机。 直接在安全中心内查看、调查和修正调查结果。 
- **访问和应用程序控件**-通过应用适用于特定工作负荷的机器学习驱动的允许列表建议，阻止恶意软件和其他不需要的应用程序。 使用对 Azure Vm 上管理端口的实时控制访问权限减少网络攻击面。 这大大减少了暴力破解和其他网络攻击。
- **容器安全功能**-从容器化环境中的漏洞管理和实时威胁防护中获益。 启用容器注册表资源时，可能需要12hrs，直到启用了所有功能。


## <a name="next-steps"></a>后续步骤
本文介绍了安全中心定价。 若要详细了解标准层增强的安全性和高级威胁防护功能，请参阅：

- [Azure 安全中心的威胁防护](threat-protection.md)
- [实时 VM 访问控制](security-center-just-in-time.md)
- [容器安全性概述](container-security.md)
- [根据你的选择货币和你所在的区域的定价详细信息](https://azure.microsoft.com/pricing/details/security-center/)