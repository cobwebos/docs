---
title: "Azure 安全中心简介 | Microsoft 文档"
description: "了解 Azure 安全中心及其主要功能和工作原理。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 8951167213da6ab5341c1ca420353ec625ef5424
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="introduction-to-azure-security-center"></a>Azure 安全中心简介
了解 Azure 安全中心及其主要功能和工作原理。

> [!NOTE]
> 自 2017 年 6 月初开始，安全中心将使用 Microsoft Monitoring Agent 来收集和存储数据。 请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)，了解详细信息。 本文中的信息表示转换到 Microsoft Monitoring Agent 后的安全中心功能。
>
>

## <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
 安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

## <a name="key-capabilities"></a>关键功能
 安全中心提供易于使用且非常有效的威胁预防、检测和响应功能（内置于 Azure）。 主要功能包括：

| 阶段 | 功能 |
| --- | --- |
| 预防 |监视 Azure 资源的安全状态 |
| 预防 | 可基于公司的安全要求、使用的应用程序的类型和数据的敏感性，定义 Azure 订阅的策略 |
| 预防 | 使用策略驱动的安全建议指导服务所有者实施所需控件 |
| 预防 | 快速部署 Microsoft 以及合作伙伴的安全服务和应用 |
| 检测 |自动从 Azure 资源、网络和合作伙伴解决方案（例如恶意软件程序和防火墙）收集和分析安全数据 |
| 检测 | 使用 Microsoft 产品和服务、Microsoft 数字犯罪部门 (DCU)、Microsoft 安全响应中心 (MSRC) 以及外部源提供的全球威胁情报 |
| 检测 | 应用高级分析，包括机器学习和行为分析 |
| 响应 |按优先顺序排列安全事件/警报 |
| 响应 | 对攻击源和受影响资源进行分析 |
| 响应 | 建议组织当前攻击和预防未来攻击的方式 |

## <a name="introductory-walkthrough"></a>介绍性演练

> [!NOTE]
> 本文档将使用示例部署介绍该服务。 本文档不是一份分步指南。
>
>

 从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问安全中心。 [登录到门户](https://portal.azure.com)。 在主门户菜单下，滚动到“安全中心”选项，或选择之前固定到门户仪表板的“安全中心”磁贴。

![Azure 门户中的安全磁贴][1]

从安全中心可设置安全策略、监视安全配置和查看安全警报。

### <a name="security-policies"></a>安全策略
可根据公司安全要求定义 Azure 订阅的策略。 也可根据正在使用的应用程序的类型或每个订阅中的数据的敏感性进行自定义设置。 例如，用于开发或测试的资源的安全要求可能不同于用于产品应用程序的资源的要求。 同样，使用 PII等管控数据的应用程序可能需要更高级别的安全性。

> [!NOTE]
> 若要修改安全策略，用户必须是安全管理员或是该订阅的所有者或参与者。 若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](security-center-permissions.md)。
>
>

在“安全中心”边栏选项卡中，为一列订阅和资源组选择“策略”磁贴。   

![“安全中心”边栏选项卡][2]

在“**安全策略**”边栏选项卡中，选择某个订阅以查看其策略详细信息。

“数据收集”对安全策略启用数据收集。 启用后提供的服务：

* 每日扫描所有支持的虚拟机 (VM) 以进行安全监视和提供建议。
* 分析安全事件以进行分析和威胁检测。

> [!NOTE]
> 数据收集在订阅级别进行配置。
>
>

选择“预防策略”打开“预防策略”边栏选项卡。 通过“显示建议”，可基于订阅内资源的安全要求来选择要监视的安全控件和想要看到的建议。

### <a name="security-recommendations"></a>安全建议
 安全中心将分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 会有一列建议对所需控件的整个配置过程提供指导。 示例包括：

* 设置反恶意软件可帮助识别和删除恶意软件
* 配置网络安全组和规则来控制发送到 VM 的流量
* 设置 web 应用程序防火墙，帮助抵御针对 web 应用程序的攻击
* 部署缺少的系统更新
* 解决与推荐基线不匹配的操作系统配置

单击“建议”磁贴获取建议列表。 单击每一项建议，查看其他信息或采取操作以解决问题。

![Azure 安全中心的安全建议][5]

### <a name="security-state-of-azure-resources"></a>Azure 资源的安全状态
仪表板的“预防”部分会按资源类型（包括 VM、Web 应用程序和其他资源）显示环境的总体安全状况。   

在“预防”下选择一种资源类型，以查看其详细信息（包括已发现的任何潜在安全漏洞列表）。 （下面的示例中选择了“计算机”。）

![资源运行状况磁贴][6]

### <a name="security-alerts"></a>安全警报
 安全中心自动从 Azure 资源、网络和合作伙伴解决方案（例如恶意软件程序和防火墙）收集、分析和整合数据。 检测到威胁时会创建安全警报。 示例中包括的检测项：

* 与已知的恶意 IP 地址通信的不符合安全性的 VM
* 使用 Windows 错误报告检测到的高级恶意软件
* 对 VM 的暴力破解攻击
* 来自集成的反恶意软件程序和防火墙的安全警报

单击“安全警报”磁贴，显示出一列按优先顺序排列的警报。

![安全警报][7]

选择一个警报将显示有关攻击和修正方式的信息。

![安全警报详细信息][8]

### <a name="partner-solutions"></a>合作伙伴解决方案
可通过“合作伙伴解决方案”磁贴轻松监视与 Azure 订阅相集成的合作伙伴解决方案的安全状态。 安全中心显示来自解决方案的警报。

选择“合作伙伴解决方案”磁贴。 会打开一个边栏选项卡，其中显示已连接的合作伙伴解决方案的列表。

![合作伙伴解决方案][9]

## <a name="get-started"></a>入门
若要开始使用安全中心，需要具有 Microsoft Azure 订阅。 安全中心通过 Azure 订阅启用。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

 从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问安全中心。 若要了解详细信息，请参阅[门户文档](https://azure.microsoft.com/documentation/services/azure-portal/)。

[Azure 安全中心入门](security-center-get-started.md)提供有安全中心的安全监视和策略管理组件的快速指南。

## <a name="next-steps"></a>后续步骤
本文档介绍了安全中心以及其主要功能和如何入门。 若要了解更多信息，请参阅下列资源：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [在 Azure 安全中心管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
- [Azure 安全中心的数据安全](security-center-data-security.md) - 了解如何在安全中心管理数据和确保数据安全性。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) — 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

