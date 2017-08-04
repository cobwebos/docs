---
title: "启用 Azure 安全中心中的数据收集 | Microsoft 文档"
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
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>启用 Azure 安全中心中的数据收集

> [!NOTE]
> 自 2017 年 6 月初开始，安全中心将使用 Microsoft Monitoring Agent 来收集和存储数据。 若要了解详细信息，请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)。 本文中的信息表示转换到 Microsoft Monitoring Agent 后的安全中心功能。
>
>

安全中心会收集来自虚拟机 (VM) 的数据，进而评估其安全状态、提供安全建议并发出威胁警报。 首次访问安全中心时，可选择在订阅的所有 VM 上启用数据收集。 如果未启用数据收集，安全中心会建议打开该订阅的安全策略中的数据收集。

启用数据收集后，安全中心即可在所有受支持的现有 Azure 虚拟机以及任何新创建的虚拟机中预配 Microsoft Monitoring Agent。 Microsoft Monitoring Agent 会扫描各种与安全相关的配置。 此外，操作系统会引发事件日志事件。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址、已登录用户、租户 ID。 Microsoft Monitoring Agent 读取事件日志条目和配置，并将数据复制到工作区进行分析。 Microsoft Monitoring Agent 还将故障转储文件复制到工作区。

如果使用安全中心免费层，则可在“安全策略”中关闭数据收集，禁用从虚拟机收集数据。 禁用数据收集会限制对 VM 的安全评估。 若要了解详细信息，请参阅[禁用数据收集](#disabling-data-collection)。 即使禁用数据收集，也仍可以启用 VM 磁盘快照和项目收集。 安全中心标准层上的订阅需启用数据收集。

> [!NOTE]
> 了解有关安全中心免费和标准[定价层](security-center-pricing.md)的详细信息。
>
>

## <a name="implement-the-recommendation"></a>实现该建议

> [!NOTE]
> 本文档将使用示例部署介绍该服务。 本文档不是一份分步指南。
>
>

1. 在“建议”边栏选项卡中，选择“启用订阅的数据收集”。  这会打开“启用数据收集”边栏选项卡。
   ![“建议”边栏选项卡][2]
2. 在“启用数据收集”边栏选项卡，选择你的订阅。 将打开该订阅的“安全策略”边栏选项卡。
3. 在“安全策略”边栏选项卡，选择“数据收集”下方的“打开”以自动收集日志。 通过打开数据收集，将对订阅中的所有当前和新支持的 VM 设置监视扩展。
4. 选择“保存”。
5. 选择“确定”。

## <a name="disabling-data-collection"></a>禁用收集数据
如果使用安全中心免费层，则可在“安全策略”中关闭数据收集，随时禁用从虚拟机收集数据。 安全中心标准层上的订阅需启用数据收集。

1. 返回到“安全中心”边栏选项卡，然后选择“策略”磁贴。 这将打开“安全策略 - 定义每个订阅的策略”边栏选项卡。
   ![选择策略磁贴][5]
2. 在“安全策略 - 定义每个订阅的策略”边栏选项卡，选择想要禁用数据收集的订阅。
3. 将打开该订阅的“安全策略”边栏选项卡。  选择数据集下方的“关闭”。
4. 在顶部的功能区中选择“保存”。

## <a name="next-steps"></a>后续步骤
本文档演示如何实现安全中心建议“启用数据收集”。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
- [Azure 安全中心的数据安全](security-center-data-security.md) - 了解如何在安全中心管理数据和确保数据安全性。
* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

