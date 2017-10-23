---
title: "Azure 安全中心快速入门指南 | Microsoft 文档"
description: "本文会引导了解安全监视和策略管理组件并提供后续步骤的链接，帮助你快速使用 Azure 安全中心。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Azure 安全中心快速入门指南
本文会引导了解安全中心的安全监视和策略管理组件，帮助你快速使用 Azure 安全中心。

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

安全中心的免费层在所有 Azure 订阅上自动启用，并提供安全策略、持续的安全评估和切实可行的安全建议来帮助你保护 Azure 资源。

从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问安全中心。 若要深入了解 Azure 门户，请参阅[门户文档](https://azure.microsoft.com/documentation/services/azure-portal/)。

## <a name="permissions"></a>权限
如果分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与资源相关的信息。 若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](security-center-permissions.md)。

## <a name="data-collection"></a>数据收集
安全中心从 Azure 虚拟机 (VM) 和非 Azure 计算机收集数据以监视安全漏洞和威胁。 数据是使用 Microsoft Monitoring Agent 收集的，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到工作区以进行分析。 安全中心可在所有受支持的现有 Azure VM 以及任何新创建的 Azure VM 中预配 Microsoft Monitoring Agent。 若要了解有关数据收集的详细信息，请参阅[启用数据收集](security-center-enable-data-collection.md)。

强烈建议使用自动设置，并且安全中心标准层上的订阅必须使用自动设置。 禁用自动设置会限制对资源的安全监视。

若要了解有关免费定价层和标准定价层的详细信息，请参阅[安全中心定价](security-center-pricing.md)。

以下步骤介绍了如何访问和使用安全中心内的组件。

> [!NOTE]
> 本文借助示例部署介绍该服务。 本文并非分步指南。
>
>

## <a name="access-security-center"></a>访问安全中心
在门户中，按照以下步骤访问安全中心：

1. 在 **Microsoft Azure** 菜单上选择“安全中心”。

   ![Azure 菜单][1]
2. 如果是首次访问安全中心，将打开“欢迎”边栏选项卡。 选择“启动安全中心”以打开“安全中心”。
   ![欢迎屏幕][10]
3. 从“欢迎”边栏选项卡中启动安全中心或从 Microsoft Azure 菜单中选择安全中心后，“安全中心”会随即打开。 为了便于将来访问“安全中心”边栏选项卡，请选择“将边栏选项卡固定到仪表板”选项（右上方）。
   ![将边栏选项卡固定到仪表板选项][2]

## <a name="use-security-center"></a>使用安全中心
可以配置 Azure 订阅和资源组的安全策略。 配置订阅的安全策略：

1. 在“安全中心”主菜单上，选择“安全策略”。
2. 在“安全中心 - 安全策略”下，选择一个订阅。
3. 在“安全策略 - 数据收集”下，打开“自动预配”。 安全中心可在所有受支持的现有 Azure VM 以及任何新创建的 Azure VM 中预配 Microsoft Monitoring Agent。

    ![安全策略][12]

4. 在“安全策略”边栏选项卡上，选择策略组件“安全策略”。

     ![安全策略][11]

5. 在“显示推荐：”下，打开想要作为安全策略的一部分查看的建议。 示例:

   * 将“系统更新”设置为“打开”会扫描受支持的所有 VM，查看是否缺少 OS 更新。
   * 将“OS 漏洞”设置为“打开”会扫描受支持的所有 VM，识别可能使 VM 更易受到攻击的 OS 配置。

6. 选择“保存”。

### <a name="view-recommendations"></a>查看建议
1. 返回到“安全中心”边栏选项卡，并选择“建议”磁贴。 安全中心定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会在“建议”边栏选项卡上显示建议。
   ![Azure 安全中心中的建议][5]
2. 选择“建议”边栏选项卡上的建议，查看详细信息和/或采取操作来解决问题。

### <a name="view-the-security-state-of-your-resources"></a>查看资源的安全状态
1. 返回“安全中心”边栏选项卡。 仪表板的“预防”部分包含 VM、网络、数据和应用程序的安全状态指示器。
2. 选择“计算”以查看详细信息。 “计算”边栏选项卡将打开，显示三个选项卡：

  - **概述** - 包含有关监视和 VM 的建议。
  - **VM 和计算机** - 列出所有 VM 和计算机的当前安全状态。
  - **云服务** - 列出安全中心监视的 Web 角色和辅助角色。

    ![计算安全运行状况][6]

3. 在“概述”选项卡上，选择一个建议来查看详细信息并/或执行操作来配置必要的控件。
4. 在“VM 和计算机”选项卡上，选择一个资源来查看更多详细信息。

### <a name="view-security-alerts"></a>查看安全警报
1. 返回到“安全中心”边栏选项卡，并选择“安全警报”磁贴。 “安全警报”边栏选项卡随即打开并显示警报列表。 这些警报通过安全中心对安全日志和网络活动的分析生成。 同时包括来自集成合作伙伴解决方案的警报。
   ![Azure 安全中心中的安全警报][7]

2. 选择一个警报以查看附加信息。 在此示例中，我们选择“在转储筛选器中发现的修改后系统二进制文件”。 这会打开相应边栏选项卡，提供警报的额外详细信息。
   ![Azure 安全中心中的安全警报详细信息][8]

### <a name="view-the-health-of-your-partner-solutions"></a>查看合作伙伴解决方案的运行状况
1. 返回“安全中心”边栏选项卡。 通过“安全解决方案”磁贴，可轻松监视与 Azure 订阅相集成的合作伙伴解决方案的运行状态。
2. 选择“安全解决方案”磁贴。 随即打开相应的边栏选项卡，显示连接到安全中心的合作伙伴解决方案的列表。
   ![合作伙伴解决方案][9]
3. 选择一个合作伙伴解决方案。 随即打开相应的边栏选项卡，显示合作伙伴解决方案的状态和该方案的关联资源。 选择“解决方案控制台”，打开该方案的合作伙伴管理体验。

   ![合作伙伴解决方案][13]

## <a name="next-steps"></a>后续步骤
本文介绍了安全中心的安全监视和策略管理组件。 现在已熟悉安全中心，请尝试以下步骤：

* 为 Azure 订阅配置安全策略，请参阅[在 Azure 安全中心设置安全策略](security-center-policies.md)。
* 使用安全中心中的建议帮助你保护 Azure 资源，请参阅[在 Azure 安全中心管理安全建议](security-center-recommendations.md)。
* 查看和管理当前的安全警报，请参阅[管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)。
- 详细了解如何与合作伙伴集成以提升整体安全性，请参阅[合作伙伴与解决方案集成](security-center-partner-integration.md)。
- 了解如何在安全中心管理数据和确保数据安全性，请参阅 [Azure 安全中心的数据安全](security-center-data-security.md)。
* 深入了解随附安全中心[标准层](security-center-pricing.md)提供的[高级威胁检测功能](security-center-detection-capabilities.md)。 标准层在前 60 天免费提供。
* 如果存在使用安全中心方面的问题，请参阅 [Azure 安全中心常见问题解答](security-center-faq.md)。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
