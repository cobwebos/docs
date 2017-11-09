---
title: "Azure 安全中心中的数据收集 | Microsoft Docs"
description: " 了解如何启用 Azure 安全中心中的数据收集。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-in-azure-security-center"></a>Azure 安全中心中的数据收集
安全中心从 Azure 虚拟机 (VM) 和非 Azure 计算机收集数据以监视安全漏洞和威胁。 数据是使用 Microsoft Monitoring Agent 收集的，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到工作区以进行分析。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址、已登录用户、租户 ID。 Microsoft Monitoring Agent 还将故障转储文件复制到工作区。

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>启用 Microsoft Monitoring Agent 的自动设置     
启动自动设置后，安全中心可在所有受支持的 Azure VM 以及任何新建的 Azure VM 中预配 Microsoft Monitoring Agent。 强烈建议使用自动设置，并且安全中心标准层上的订阅必须使用自动设置。

> [!NOTE]
> 禁用自动设置会限制对资源的安全监视。 若要了解详细信息，请参阅本文中的[禁用自动设置](security-center-enable-data-collection.md#disable-automatic-provisioning)。 即使禁用了自动设置，也仍可以启用 VM 磁盘快照和项目收集。
>
>

启用 Microsoft Monitoring Agent 自动设置的步骤：
1. 在“安全中心”主菜单下，选择“安全策略”。
2. 选择订阅。
3. 在“安全策略”下，选择“数据收集”。
4. 在“载入”下，选择“打开”以启用自动设置。
5. 选择“保存”。

![启用自动设置][1]

## <a name="default-workspace-configuration"></a>默认工作区配置
安全中心收集的数据存储在 Log Analytics 工作区中。  可以选择从存储在安全中心创建的工作区或创建的现有工作区中的 Azure VM 收集数据。

使用现有 Log Analytics 工作区的步骤：
- 工作区必须与选定 Azure 订阅相关联。
- 至少必须拥有读取权限，才能访问工作区。

选择现有 Log Analytics 工作区的具体步骤：

1. 在“安全策略 – 数据收集”下，选择“使用其他工作区”。

   ![选择现有工作区][2]

2. 从下拉菜单中，选择一个工作区，用于存储所收集的数据。

> [!NOTE]
> 下拉菜单中只显示用户有权访问且 Azure 订阅包含的工作区。
>
>

3. 选择“保存”。
4. 选择“保存”后，系统就会询问是否要重新配置受监视的 VM。

   - 如果只希望在新 VM 上应用新的工作区设置，请选择“否”。 新的工作区设置只会应用于新的代理安装；新发现的 VM 没有安装 Microsoft Monitoring Agent。
   - 如果希望在所有 VM 上应用新的工作区设置，请选择“是”。 此外，所有连接到安全中心创建的工作区的 VM 也都会重新连接到新的目标工作区。

   > [!NOTE]
   > 如果选择“是”，不得删除安全中心创建的工作区，除非所有 VM 已重新连接到新的目标工作区。 如果过早删除工作区，此操作将会失败。
   >
   >

   - 选择“取消”，以取消该操作。

   ![选择现有工作区][3]

## <a name="data-collection-tier"></a>数据收集层
安全中心可以减少事件数量，同时保留足够数量的事件用于调查、审核和威胁检测。 可以从代理要收集的四个事件集中为订阅和工作区选择权限筛选策略。

- 所有事件 – 适用于想要确保已收集所有事件的客户。 这是默认值。
- 通用 – 这是一个事件集，可满足大多数客户的需求，使他们可以进行完整的审核跟踪。
- 最小 – 一个较小事件集，适合希望最大程度地减小事件量的客户。
- 无 – 从安全和 App Locker 日志禁用安全事件收集。 如果客户选择此选项，他们的安全仪表板只包含 Windows 防火墙日志和主动评估，如反恶意软件、基线和更新评估。

> [!NOTE]
> 这些集合专门用于典型应用场景。 请务必先评估哪个事件集适合你的需求，再进行实现。
>
>

为了确定属于通用和最小事件集的事件，我们与客户进行协作，参照行业标准，了解了每个事件及其使用情况的未筛选频率。 我们在此过程中使用了以下准则：

- 最小 - 确保此集只涵盖可能指示成功违反的事件以及数量很少的重要事件。 例如，此集包含用户成功和失败的登录（事件 ID 4624 和 4625），但不包含对审核很重要但对检测毫无意义且数量相对较多的注销。 此集的大多数数据量是登录事件和进程创建事件（事件 ID 4688）。
- 通用 - 提供此集中的完整用户审核跟踪。 例如，此集包含用户登录和用户注销（事件 ID 4634）。 我们加入审核操作，如安全组更改、关键域控制器 Kerberos 操作以及行业组织建议的其他事件。

数量非常少的事件包含在通用集中，因为在所有事件中选择该集的主要动机是为了减少数量，而不是筛选出特定事件。

下面是对每个集的安全和 App Locker 事件 ID 的完整分类：

   ![事件 ID][4]

选择筛选策略的具体步骤：
1. 在“安全策略和设置”边栏选项卡上，选择“安全事件”下的筛选策略。
2. 选择“保存”。

   ![选择筛选策略][5]

## <a name="disable-automatic-provisioning"></a>禁用自动设置
可以随时禁用安全策略中的此设置，禁用资源的自动设置。 强烈建议使用自动设置，以获取有关系统更新、OS 漏洞和终结点保护的安全警报和建议。

> [!NOTE]
> 禁用自动设置不会从已预配代理的 Azure VM 中删除 Microsoft Monitoring Agent。
>
>

1. 返回到“安全中心”主菜单，选择“安全策略”。

   ![禁用自动设置][6]

2. 选择希望禁用自动设置的订阅。
3. 在“安全策略 – 数据收集”边栏选项卡的“载入”下，选择“关闭”，禁用自动设置。
4. 选择“保存”。  

## <a name="next-steps"></a>后续步骤
本文介绍了数据收集和自动设置在安全中心中的工作方式。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
- [Azure 安全中心的数据安全](security-center-data-security.md) - 了解如何在安全中心管理数据和确保数据安全性。
* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
