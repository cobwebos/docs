---
title: "Azure 安全中心常见问题 (FAQ) | Microsoft Docs"
description: "此 FAQ 解答有关 Azure 安全中心的问题。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 506c23179d09e2e22065c8ba2bc85a341bb0ea09
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure 安全中心常见问题 (FAQ)
此 FAQ 解答有关 Azure 安全中心的问题。Azure 安全中心可帮助预防、检测和响应威胁，同时增加 Microsoft Azure 资源的可见性和安全方面的可控性。

> [!NOTE]
> 自 2017 年 6 月初开始，安全中心将使用 Microsoft Monitoring Agent 来收集和存储数据。 若要了解详细信息，请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)。 本文中的信息表示转换到 Microsoft Monitoring Agent 后的安全中心功能。
>
>

## <a name="general-questions"></a>一般问题
### <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
Azure 安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 该服务提供订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

### <a name="how-do-i-get-azure-security-center"></a>如何获取Azure 安全中心？
Azure 安全中心通过 Microsoft Azure 订阅启用，可从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问。 （[登录到门户](https://portal.azure.com)，选“浏览”，并滚动到“安全中心”）。  

## <a name="billing"></a>计费
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure 安全中心如何计费？
安全中心分两个层提供：

免费层提供 Azure 资源、基本安全策略、安全建议以及与合作伙伴安全产品和服务的集成的安全状态。

标准层增添了高级威胁检测功能，包括威胁情报、行为分析、异常检测、安全事件和威胁归属报告。 标准层在前 60 天免费。 如果 60 天后选择继续使用该服务，将自动开始计费。  如要升级，请选择[安全策略](security-center-policies.md#set-security-policies)中的“定价层”。 若要了解详细信息，请参阅[安全中心定价](security-center-pricing.md)。

## <a name="permissions"></a>权限
Azure 安全中心使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../active-directory/role-based-access-built-in-roles.md)。

安全中心会评估资源的配置以识别安全问题和漏洞。 如果你分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与资源相关的信息。

若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](security-center-permissions.md)。

## <a name="data-collection"></a>数据收集
安全中心会收集来自虚拟机的数据，进而评估其安全状态、提供安全建议并发出威胁警报。 初次访问安全中心时，将在订阅中的所有虚拟机上启用数据收集。 也可在安全中心策略中启用数据收集。

### <a name="how-do-i-disable-data-collection"></a>如何禁用数据收集？
如果使用 Azure 安全中心免费层，可随时通过虚拟机禁用数据收集。 标准层上的订阅需启用数据收集。 可在安全策略中对订阅禁用数据收集。 （[登录到 Azure 门户](https://portal.azure.com)，选择“浏览”，选择“安全中心”，然后选择“策略”。）选中一个订阅后，会打开一个新的边栏选项卡，可在其选项中关闭“数据收集”。

### <a name="how-do-i-enable-data-collection"></a>如何启用数据收集？
可在安全中心策略中对 Azure 订阅启用数据收集。 启用数据收集。 [登录 Azure 门户](https://portal.azure.com)，选择“浏览”、“安全中心”，然后选择“策略”。 将“数据收集”设置为“开”。

### <a name="what-happens-when-data-collection-is-enabled"></a>启用数据收集之后会发生什么？
启用数据收集后，将在订阅中部署的所有现有以及新的受支持虚拟机上置备 Microsoft Monitoring Agent。

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 会影响服务器性能吗？
代理使用系统资源的名义帐户，对性能的影响极小。 有关性能影响和代理及扩展的详细信息，请参阅[规划和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。

### <a name="where-is-my-data-stored"></a>我的数据存储在哪？
通过此代理收集的数据存储在与订阅关联的现有 Log Analytics 工作区或新工作区中。 有关详细信息，请参阅[数据安全](security-center-data-security.md)。

## <a name="using-azure-security-center"></a>使用 Azure 安全中心
### <a name="what-is-a-security-policy"></a>什么是安全策略？
安全策略定义一组控件，这些控件是针对指定订阅中的资源建议的。 在 Azure 安全中心，用户需根据公司安全要求和应用程序类型或每个订阅中数据的敏感性，为 Azure 订阅定义策略。

Azure 安全中心中启用的安全策略将使用安全建议和监视。 若要了解有关安全策略的详细信息，请参阅[在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)。

### <a name="who-can-modify-a-security-policy"></a>哪些用户可以修改安全策略？
若要修改安全策略，用户必须是安全管理员或是该订阅的所有者或参与者。

若要了解如何配置安全策略，请参阅[在 Azure 安全中心设置安全策略](security-center-policies.md)。

### <a name="what-is-a-security-recommendation"></a>什么是安全建议？
Azure 安全中心可分析 Azure 资源的安全状态。 发现潜在的安全漏洞后会生成建议。 建议会对所需控件的整个配置过程提供指导。 示例如下：

* 设置反恶意软件可帮助识别和删除恶意软件
* 配置[网络安全组](../virtual-network/virtual-networks-nsg.md)和规则来控制发送到虚拟机的流量
* 设置 web 应用程序防火墙，帮助抵御针对 web 应用程序的攻击
* 部署缺少的系统更新
* 解决与推荐基线不匹配的操作系统配置

安全策略中仅已启用的推荐操作会显示在此处。

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>如何查看 Azure 资源当前安的全状态？
“安全中心概述”边栏选项卡会按计算、网络、存储和数据以及应用程序显示环境的总体安全状况。 每个资源类型都有一个指示符，该指示符显示是否已发现潜在安全漏洞。 单击每个磁贴可显示安全中心发现的安全问题列表和订阅中的资源清单。

### <a name="what-triggers-a-security-alert"></a>什么会触发安全警报？
Azure 安全中心自动从 Azure 资源、网络和合作伙伴解决方案（例如恶意软件和防火墙）收集、分析和融合数据。 检测到威胁时会创建安全警报。 示例中包括的检测项：

* 与已知的恶意 IP 地址通信的不符合安全性的虚拟机
* 使用 Windows 错误报告检测到的高级恶意软件
* 对虚拟机的暴力破解攻击
* 来自集成合作伙伴解决方案（例如反恶意软件或 Web 应用程序防火墙）的安全警报

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft 安全响应中心与 Azure 安全中心检测和警示的威胁之间有何区别？
Microsoft 安全响应中心 (MSRC) 会执行 Azure 网络和基础结构的选择安全监视，并接收来自第三方的威胁情报和恶意投诉。 MSRC 发现不合法或未经授权的某一方访问客户数据或客户使用 Azure 不符合可接受的使用条款时，安全事件管理器会通知客户。 通常会以电子邮件方式向 Azure 安全中心中指定的安全联系人或 Azure 订阅所有者（如果未指定安全联系人）发送通知。

安全中心作为 Azure 的一项服务，可持续监视客户的 Azure 环境，并应用分析来自动广泛地检测潜在的恶意活动。 这些检测结果会作为安全警报显示在安全中心仪表板中。

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>哪些 Azure 资源由 Azure 安全中心监视？
Azure 安全中心监视以下 Azure 资源：

* 虚拟机 (VM)（包括 [云服务](../cloud-services/cloud-services-choose-me.md)）
* Azure 虚拟网络
* Azure SQL 服务
* Azure 存储帐户
* [应用服务环境](../app-service/app-service-app-service-environments-readme.md) 中的 Azure Web 应用
* 与 Azure 订阅集成的合作伙伴解决方案，例如 VM 和[应用服务环境](../app-service/app-service-app-service-environments-readme.md)上的 web 应用程序防火墙

## <a name="virtual-machines"></a>虚拟机
### <a name="what-types-of-virtual-machines-are-supported"></a>支持哪些类型的虚拟机？
使用[经典部署模式和 Resource Manager 部署模式](../azure-classic-rm.md)创建的虚拟机 (VM) 支持监视和建议。

有关支持平台的列表，请参阅[ Azure 安全中心支持的平台](security-center-os-coverage.md)。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure 安全中心为何不能识别我的 Azure VM 上运行的反恶意软件解决方案？
Azure 安全中心可识别出通过 Azure 扩展安装的反恶意软件。 例如，安全中心无法检测出预装在所提供的映像上的反恶意软件或使用自己的进程（例如配置管理系统）在虚拟机上安装的反恶意软件。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>为什么会显示消息称 VM“缺少扫描数据”？
没有可供 VM 扫描的数据时会出现此消息。 在 Azure 安全中心启用数据收集之后，扫描数据导入可能需要一段时间（低于一小时）。 首次导入扫描数据后可能会收到此消息，因为此时根本没有扫描数据或没有最近扫描数据。 扫描不会导入处于停止状态的 VM。 如果最近（根据 Windows 代理的保留策略，此值默认为 30 天）没有导入扫描数据，也可能会显示此消息。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>为什么会显示消息称“VM 代理缺失”？
VM 代理必须安装在 VM 上才能启用数据收集。 对于从 Azure 应用商店部署的 VM，默认安装 VM 代理。 有关如何在其他 VM 上安装 VM 代理的详细信息，请参阅博客文章 [VM 代理和扩展](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。

