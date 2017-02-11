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
ms.date: 12/09/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 0135732e95279f2e717334d3dd39902b56b0aa90
ms.openlocfilehash: 30a327f59b8149f41c3b5206e0b0c2fc859934a0


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure 安全中心常见问题 (FAQ)
此 FAQ 解答有关 Azure 安全中心的问题。Azure 安全中心可帮助预防、检测和响应威胁，同时增加 Microsoft Azure 资源的可见性和安全方面的可控性。

## <a name="general-questions"></a>一般问题
### <a name="what-is-azure-security-center"></a>什么是 Azure 安全中心？
Azure 安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 该服务提供订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

### <a name="how-do-i-get-azure-security-center"></a>如何获取Azure 安全中心？
Azure 安全中心通过 Microsoft Azure 订阅启用，可从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问。 （[登录到门户](https://portal.azure.com)，选“浏览”，并滚动到“安全中心”）。  

## <a name="billing"></a>计费
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure 安全中心如何计费？
Azure 安全中心提供两种层级：免费层和标准层。

通过免费层可设置安全策略和接收安全警报、事件和建议（这些建议将对配置所需控件的整个过程提供指导）。 使用免费层还可监视 Azure 资源和与 Azure 订阅集成的合作伙伴解决方案的安全状态。

标准层除包含免费层的功能外，还提供高级检测：威胁情报、行为分析、故障分析和异常检测。 可免费试用标准层 90 天。 如要升级，请选择[安全策略](security-center-policies.md#set-security-policies-for-subscriptions)中的“定价层”。 若要了解详细信息，请参阅[安全中心定价](security-center-pricing.md)。

## <a name="permissions"></a>权限
Azure 安全中心使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../active-directory/role-based-access-built-in-roles.md)。

安全中心会评估资源的配置以识别安全问题和漏洞。 如果你分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与资源相关的信息。

若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](security-center-permissions.md)。

## <a name="data-collection"></a>数据收集
安全中心会收集来自虚拟机的数据，进而评估其安全状态、提供安全建议并发出威胁警报。 初次访问安全中心时，将在订阅中的所有虚拟机上启用数据收集。 建议使用数据收集，但可通过安全中心策略中[禁用数据收集](#how-do-i-disable-data-collection)来选择退出。

### <a name="how-do-i-disable-data-collection"></a>如何禁用数据收集？
可随时在安全中心策略中对订阅禁用“数据收集”。 （[登录到 Azure 门户](https://portal.azure.com)，选择“浏览”，选择“安全中心”，然后选择“策略”。）选中一个订阅后，会打开一个新的边栏选项卡，可在其选项中关闭“数据收集”。 若要将代理从现有虚拟机中删除，请在顶部功能区中选择“删除代理”选项。

> [!NOTE]
> 虽然可以在 Azure 订阅级别和资源组级别设置安全策略，但必须选择一个订阅以关闭数据收集。
>
>

### <a name="how-do-i-enable-data-collection"></a>如何启用数据收集？
可在安全中心策略中对 Azure 订阅启用数据收集。 若要启用数据收集，请[登录到 Azure 门户](https://portal.azure.com)，选择“浏览”，选择“安全中心”，然后选择“策略”。 将“数据收集”设置为“开启”，然后配置要从中收集数据的存储帐户（参阅“[我的数据存储在哪？](#where-is-my-data-stored)”）。 “数据收集”启用后，会从订阅中所有受支持的虚拟机自动收集安全配置和事件信息。

> [!NOTE]
> 虽然可在 Azure 订阅级别和资源组级别设置安全策略，但只能在订阅级别配置数据收集。
>
>

### <a name="what-happens-when-data-collection-is-enabled"></a>启用数据收集之后会发生什么？
通过 Azure 监视代理和 Azure 安全监视扩展启用数据收集。 Azure 安全监视扩展会扫描各种安全相关配置，并将其发送到 [Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 的跟踪中。 此外，操作系统会创建事件日志条目。  Azure 监视代理读取事件日志条目和 ETW 跟踪，并将其复制到存储帐户进行分析。  这是在安全策略中配置的存储帐户。 有关存储帐户的详细信息，请参阅问题“[我的数据存储在哪？](#where-is-my-data-stored)”

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>监视代理或安全监视扩展会是否会影响服务器性能？
代理和扩展使用系统资源的名义帐户，对性能的影响极小。 有关性能影响和代理及扩展的详细信息，请参阅[规划和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。

### <a name="where-is-my-data-stored"></a>我的数据存储在哪？
对于每个有虚拟机运行的区域，可选择相应的存储帐户存储从这些虚拟机收集的数据。 这样即可轻松地将数据保存在同一地理区域，确保维护隐私和数据所有权。 可在安全策略中为订阅选择存储帐户。 （[登录到 Azure 门户](https://portal.azure.com)，选择“浏览”，选择“安全中心”，然后选择“策略”。）选择订阅后，会打开一个新的边栏选项卡。 若要选择区域，请选择“选择存储帐户”。

> [!NOTE]
> 虽然可在 Azure 订阅级别和资源组级别设置安全策略，但只能在订阅级别选择存储帐户的区域。
>
>

若要了解有关 Azure 存储和存储帐户的详细信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)和[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。

## <a name="using-azure-security-center"></a>使用 Azure 安全中心
### <a name="what-is-a-security-policy"></a>什么是安全策略？
安全策略用于定义一组控制，这些控制是针对指定订阅或资源组中的资源建议的。 在 Azure 安全中心，用户需根据公司安全要求和应用程序类型或每个订阅中数据的敏感性，为 Azure 订阅和资源组定义策略。

例如，用于开发或测试的资源的安全要求可能不同于用于产品应用程序的资源的要求。 同样，使用 PII（个人身份信息）等管控数据的应用程序可能需要更高级别的安全性。 Azure 安全中心中启用的安全策略将使用安全建议和监视。 若要了解有关安全策略的详细信息，请参阅[在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)。

> [!NOTE]
> 在订阅级别策略与资源组级别策略冲突时，以资源组级别策略为准。
>
>

### <a name="who-can-modify-a-security-policy"></a>哪些用户可以修改安全策略？
安全策略是针对每个订阅或资源组而配置的。 仅订阅的所有者或参与者才可修改订阅级别或资源组级别的安全策略。

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
“安全中心”边栏选项卡上的“资源运行状况”磁贴中会按虚拟机、web 应用程序和其他资源显示环境的总体安全状况。 每个资源都有一个指示符，该指示符显示是否已发现潜在安全漏洞。 单击“资源运行状态”磁铁会显示资源并标识需要注意或可能存在问题的地方。

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
* 与 Azure 订阅集成的合作伙伴解决方案，例如 VM 和[应用服务环境](../app-service/app-service-app-service-environments-readme.md)上的 web 应用程序防火墙

## <a name="virtual-machines"></a>虚拟机
### <a name="what-types-of-virtual-machines-are-supported"></a>支持哪些类型的虚拟机？
使用[经典部署模式和 Resource Manager 部署模式](../azure-classic-rm.md)创建的 VM 支持安全运行状况监视和建议。

支持的 Windows VM：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

支持的 Linux VM：

* Ubuntu 版本 12.04、14.04、16.04、16.10
* Debian 版本 7、8
* CentOS 版本 6.\*、7.*
* Red Hat Enterprise Linux (RHEL) 版本 6.\*、7.*
* SUSE Linux Enterprise Server (SLES) 版本 11.\*、12.*
* Oracle Linux 版本 6.\*、7.*

此外还支持云服务中运行的 VM。 仅监视云服务 web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure 安全中心为何不能识别我的 Azure VM 上运行的反恶意软件解决方案？
Azure 安全中心仅可识别出通过 Azure 扩展安装的反恶意软件。 例如，安全中心无法检测出预装在所提供的映像上的反恶意软件或使用自己的进程（例如配置管理系统）在虚拟机上安装的反恶意软件。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>为什么会显示消息称 VM“缺少扫描数据”？
在 Azure 安全中心启用数据收集之后，扫描数据导入可能需要一段时间（低于一小时）。 扫描不会导入处于停止状态的 VM。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>为什么会显示消息称“VM 代理缺失”？
VM 代理必须安装在 VM 上才能启用数据收集。 对于从 Azure 应用商店部署的 VM，默认安装 VM 代理。 有关如何在其他 VM 上安装 VM 代理的详细信息，请参阅博客文章 [VM 代理和扩展](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。



<!--HONumber=Dec16_HO2-->


