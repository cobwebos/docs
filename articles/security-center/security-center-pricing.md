---
title: Azure 安全中心层级的定价
description: Azure 安全中心具有两种层级 - 免费层和标准层。 此页面显示如何从“免费层”升级到“标准层”。
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
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 2eb302462e402e701cc5ae664239c457c90ab1b9
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612786"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>升级到标准层以增强安全性

Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 它可以提供针对混合云工作负载的可见性和可控性、可减小在威胁下的曝光面的积极防御功能以及有助于随时响应快速演变的网络攻击的智能检测功能。

## <a name="pricing-tiers"></a>定价层
安全中心分两个层提供：

- 当你首次在 Azure 门户中访问 Azure 安全中心仪表板时，或者如果通过 API 以编程方式启用，则会在所有 Azure 订阅上启用**免费**层。 免费层提供安全策略、持续的安全评估和切实可行的安全建议来帮助你保护 Azure 资源。

- **标准**层将免费层的功能扩展到在私有云和其他公有云中运行的工作负荷，并跨混合云工作负荷提供统一的安全管理和威胁防护。 标准层还添加了威胁防护功能，这些功能使用内置行为分析和机器学习来识别攻击和零天攻击、访问和应用程序控制，以减少网络攻击和恶意软件的暴露程度。 此外，标准层还添加了对虚拟机进行漏洞扫描。 可以免费试用标准层。 安全中心标准版支持 Azure 资源，包括 Vm、虚拟机规模集、应用服务、SQL 服务器和存储帐户。 如果你拥有 Azure 安全中心标准版，则可以选择不提供基于资源类型的支持。 

Vm 的大多数免费层安全评估以及许多标准层安全警报都需要安装 Log Analytics 代理。 可以在安全中心启用自动预配，以自动在 Azure Vm 上部署代理。

## <a name="try-standard-tier-free-for-30-days"></a>免费试用标准层30天
标准层在前30天内免费。 30 天后，如果选择继续使用服务，会自动开始收取使用费用。

可以将整个 Azure 订阅升级到标准层，这会由订阅中的所有资源继承。

若要获取标准层：

1. 在“安全中心”主菜单上选择“定价和设置”****。****
2. 选择要升级到 "标准" 的订阅。
3. 选择“定价层”。 
4. 选择“标准层”**** 以进行升级。
5. 单击“保存”  。

[![安全中心定价](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> 若要启用包括威胁防护功能在内的所有安全中心功能，必须将标准定价层应用到包含适用工作负荷的订阅。 配置工作区定价不会为 Azure 资源启用实时 VM 访问、自适应应用程序控件和网络检测功能。 
>
> 可以在订阅级别或资源级别为**Azure 存储帐户**启用威胁防护。
> 可以在订阅级别或资源级别为**AZURE Sql 数据库 sql server**启用威胁防护。
> 只能在资源级别为**Azure Database for MariaDB/MySQL/PostgreSQL**启用威胁防护。


## <a name="why-upgrade-to-standard"></a>为什么升级到标准版？
安全中心可为混合云工作负载提供增强的安全和威胁防护功能，其中包括：

- **** 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自多个源（包括防火墙和其他合作伙伴解决方案）的安全数据。
- **安全警报**-使用高级分析和 Microsoft Intelligent Security Graph 来获取有关不断发展的网络攻击的优势。 利用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
- **** 虚拟机的漏洞扫描 - 轻松地向所有虚拟机部署一个一个扫描程序，提供行业内的出色漏洞管理解决方案。 直接在安全中心内查看、调查和修复扫描到的漏洞。 
- **访问和应用程序控件** - 通过应用适合特定工作负载且由机器学习提供支持的允许列表建议，阻止恶意软件和其他不需要的应用程序。 实时减小网络受攻击面，控制对 Azure VM 上的管理端口的访问。 这会显著减小在暴力和其他网络攻击下的曝光面。
- **容器安全功能**-从容器化环境中的漏洞管理和实时威胁防护中获益。 启用容器注册表资源时，启用所有功能可能需要长达 12 小时的时间。


## <a name="next-steps"></a>后续步骤
本文介绍了安全中心定价。 若要详细了解标准级别的增强安全性和高级威胁防护，请参阅：

- [Azure 安全中心的威胁防护](threat-protection.md)
- [实时 VM 访问控制](security-center-just-in-time.md)
- [容器安全性概述](container-security.md)
- [根据你的选择货币和你所在的区域的定价详细信息](https://azure.microsoft.com/pricing/details/security-center/)