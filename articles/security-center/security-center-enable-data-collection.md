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
ms.date: 05/09/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 360073c0ed75552e62e69ce72b225ba35a2a3e09
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>启用 Azure 安全中心中的数据收集
为了帮助客户防止、检测和应对威胁，Azure 安全中心会收集和处理有关 Azure 虚拟机的数据，包括配置信息、元数据和事件日志。 初次访问安全中心时，将在订阅中的所有虚拟机上启用数据收集。 建议收集数据，但可在安全中心策略中关闭数据收集以选择退出（请参阅[禁用数据收集](#disabling-data-collection)）。 如果关闭数据收集，安全中心会建议打开该订阅的安全策略中的数据收集。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。 这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“启用订阅的数据收集”。  这会打开“启用数据收集”边栏选项卡。
   ![“建议”边栏选项卡][2]
2. 在“启用数据收集”边栏选项卡，选择你的订阅。 将打开该订阅的“安全策略”边栏选项卡。
3. 在“安全策略”边栏选项卡，选择“数据收集”下方的“打开”以自动收集日志。 通过打开数据收集，将对订阅中的所有当前和新支持的 VM 设置监视扩展。

   ![安全策略边栏选项卡][3]

4. 选择“保存”。
5. 选择“选择每个区域的存储帐户”。 对于每个有虚拟机运行的区域，可选择相应的存储帐户存储从这些虚拟机收集的数据。 如果未对每个区域选择一个存储帐户，则系统将为你创建存储帐户并将其置于 securitydata 资源组中。 在此示例中，选择 **newstoracct**。 通过返回到订阅的安全策略并选择不同的存储帐户，可以稍后更改存储帐户。
   ![选择存储帐户][4]
6. 选择“确定”。

> [!NOTE]
> 建议启用数据收集，并首先选择订阅级别的存储帐户。 虽然可以在 Azure 订阅级别和资源组级别设置安全策略，但只能在订阅级别配置数据收集和存储帐户。
>
>

## <a name="after-data-collection-is-enabled"></a>启用数据收集之后
通过 Azure 监视代理和 Azure 安全监视扩展启用数据收集。 Azure 安全监视扩展会扫描各种安全相关配置，并将其发送到 [Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 的跟踪中。 此外，操作系统会创建事件日志条目。 Azure 监视代理读取事件日志条目和 ETW 跟踪，并将其复制到存储帐户进行分析。 监视代理还将故障转储文件复制到存储帐户。 这是在安全策略中配置的存储帐户。

## <a name="disabling-data-collection"></a>禁用收集数据
可随时禁用数据收集，这将自动删除安全中心先前安装的所有监视代理。 必须选择一个订阅以关闭数据收集。

> [!NOTE]
> 虽然可以在 Azure 订阅级别和资源组级别设置安全策略，但必须选择一个订阅以关闭数据收集。
>
>

1. 返回到“安全中心”边栏选项卡，然后选择“策略”磁贴。 这将打开“安全策略 - 定义每个订阅或资源组的策略”边栏选项卡。
   ![选择策略磁贴][5]
2. 在“安全策略 - 定义每个订阅或资源组的策略”边栏选项卡，选择想要禁用数据收集的订阅。
   ![选择要禁用数据收集的订阅][6]
3. 将打开该订阅的“安全策略”边栏选项卡。  选择数据集下方的“关闭”。
4. 在顶部的功能区中选择“保存”。


## <a name="next-steps"></a>后续步骤
本文档演示如何实现安全中心建议“启用数据收集”。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

