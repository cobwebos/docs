---
title: "Azure 安全中心快速入门指南 | Microsoft 文档"
description: "本文会引导你了解安全监视和策略管理组件并提供后续步骤的链接，帮助你快速使用 Azure 安全中心。"
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
ms.date: 02/22/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 56a61479971c4a03345bee8a044dbc17d16371ce
ms.openlocfilehash: 8e87f9bd8122ec3c80d11e8ac563849f540e0cc5
ms.lasthandoff: 02/23/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Azure 安全中心快速入门指南
本文会引导你了解安全中心的安全监视和策略管理组件，帮助你快速使用 Azure 安全中心。

> [!NOTE]
> 本文借助示例部署介绍该服务。 本文并非分步指南。
>
>

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

安全中心的免费层随附订阅自动启用，可显示 Azure 资源的安全状态。 它提供基本的安全策略管理和安全建议，并与 Azure 合作伙伴推出的安全产品进行集成。

从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问安全中心。 若要深入了解 Azure 门户，请参阅[门户文档](https://azure.microsoft.com/documentation/services/azure-portal/)。

## <a name="permissions"></a>权限
如果你分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与 Azure 资源相关的信息。 若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](security-center-permissions.md)。

## <a name="data-collection"></a>数据收集
安全中心会收集来自虚拟机 (VM) 的数据，进而评估其安全状态、提供安全建议并发出威胁警报。 首次访问安全中心时，即会在订阅的所有 VM 上启用数据收集。 建议收集数据，但可关闭安全中心策略中的数据收集以选择退出。

以下步骤介绍了如何访问和使用安全中心内的组件。 这些步骤展示了如何在选择退出时关闭数据收集。

## <a name="access-security-center"></a>访问安全中心
在门户中，按照以下步骤访问安全中心：

1. 在 **Microsoft Azure** 菜单上选择“安全中心”。

   ![Azure 菜单][1]
2. 如果是首次访问安全中心，将打开“欢迎”边栏选项卡。 选择“是!我想启动 Azure 安全中心”打开“安全中心”边栏选项卡并启用数据收集。
   ![欢迎屏幕][10]
3. 从“欢迎”边栏选项卡中启动安全中心或从 Microsoft Azure 菜单中选择安全中心后，即会打开“安全中心”边栏选项卡。 为了便于将来访问“安全中心”边栏选项卡，请选择“将边栏选项卡固定到仪表板”选项（右上方）。
   ![将边栏选项卡固定到仪表板选项][2]

## <a name="use-security-center"></a>使用安全中心
可以配置 Azure 订阅和资源组的安全策略。 配置订阅的安全策略：

1. 在“安全中心”边栏选项卡上，选择“策略”磁贴。
   ![安全策略][3]
2. 在“安全策略-定义每个订阅或资源组的策略”边栏选项卡上，选择一个订阅。
3. 在“安全策略”边栏选项卡上，启用“数据收集”以自动收集日志。 对订阅中所有当前的 VM 和新的 VM 都预配了监视扩展。 （可将“数据收集”设置为“关”来选择退出数据收集，但这会阻止安全中心提供安全警报和建议。）
4. 在“安全策略”边栏选项卡上，选择“选择每个区域的存储帐户”。 对于运行 VM 的每个区域，选择相应的存储帐户来存储从这些 VM 收集的数据。 如果未对每个区域选择一个存储帐户，则系统将为你创建存储帐户并将其置于 securitydata 资源组中。 出于安全性考虑，收集的数据在逻辑上独立于其他客户的数据。

   > [!NOTE]
   > 建议启用数据收集，并首先选择订阅级别的存储帐户。 虽然可在 Azure 订阅级别和资源组级别上设置安全策略，但只能在订阅级别上配置数据收集和存储帐户。
   >
   >
5. 在“安全策略”边栏选项卡上，选择“保护策略”。 这将打开“保护策略”边栏选项卡。
   ![保护策略][4]
6. 在“保护策略”边栏选项卡上，打开想要作为安全策略的一部分查看的建议。 示例:

   * 将“系统更新”设置为“开”会扫描受支持的所有虚拟机，以查看是否缺少 OS 更新。
   * 将“OS 漏洞”设置为“开”会扫描受支持的所有虚拟机，识别可能使虚拟机更易受到攻击的 OS 配置。

### <a name="view-recommendations"></a>查看建议
1. 返回到“安全中心”边栏选项卡，然后选择“建议”磁贴。 安全中心定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会在“建议”边栏选项卡上显示建议。
   ![Azure 安全中心中的建议][5]
2. 选择“建议”边栏选项卡上的建议，查看详细信息和/或采取操作来解决问题。

### <a name="view-the-health-and-security-state-of-your-resources"></a>查看资源的运行状况和安全状态
1. 返回“安全中心”边栏选项卡。 “资源安全运行状况”磁贴内附有虚拟机、网络、数据和应用程序的安全状态指示器。
2. 选择“虚拟机”查看详细信息。 “虚拟机”边栏选项卡随即打开，并显示反恶意软件程序、系统更新、重启和 VM OS 漏洞的状态摘要。
   ![Azure 安全中心中的资源运行状况磁贴][6]
3. 选择“虚拟机建议”下的建议，查看详细信息和/或采取操作来配置必备控件。
4. 在“虚拟机”下选择 VM 来查看额外的详细信息。

### <a name="view-security-alerts"></a>查看安全警报
1. 返回到“安全中心”边栏选项卡，然后选择“安全警报”磁贴。 “安全警报”边栏选项卡随即打开并显示警报列表。 这些警报通过安全中心对安全日志和网络活动的分析生成。 同时包括来自集成合作伙伴解决方案的警报。
   ![Azure 安全中心中的安全警报][7]

   > [!NOTE]
   > 仅在启用了安全中心的标准层后，才可使用安全中心。 可免费试用标准层 90 天。 请参阅[后续步骤](#next-steps)，了解如何获取标准层。
   >
   >
2. 选择一个警报以查看附加信息。 本例中，我们选择“发现修改的系统二进制文件”。 这将打开相应边栏选项卡，提供警报的额外详细信息。
   ![Azure 安全中心中的安全警报详细信息][8]

### <a name="view-the-health-of-your-partner-solutions"></a>查看合作伙伴解决方案的运行状况
1. 返回“安全中心”边栏选项卡。 通过“合作伙伴解决方案”磁贴，可轻松监视与 Azure 订阅相集成的合作伙伴解决方案的运行状态。
2. 选择“合作伙伴解决方案”磁贴。 随即打开相应的边栏选项卡，显示连接到安全中心的合作伙伴解决方案的列表。
   ![合作伙伴解决方案][9]
3. 选择一个合作伙伴解决方案。 本例中选择 **F5-WAF** 解决方案。  随即打开相应的边栏选项卡，显示合作伙伴解决方案的状态和该方案的关联资源。 选择“解决方案控制台”，打开该方案的合作伙伴管理体验。

## <a name="next-steps"></a>后续步骤
本文介绍了安全中心的安全监视和策略管理组件。 现在你已熟悉安全中心，请尝试以下步骤：

* 配置 Azure 订阅的安全策略： 有关详细信息，请参阅[在 Azure 安全中心设置安全策略](security-center-policies.md)。
* 利用安全中心内的建议帮助保护 Azure 资源。 有关详细信息，请参阅[管理 Azure 安全中心内的安全建议](security-center-recommendations.md)。
* 查看并管理当前的安全警报。 有关详细信息，请参阅[管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)。
* 深入了解随附安全中心[标准层](security-center-pricing.md)提供的[高级威胁检测功能](security-center-detection-capabilities.md)。 标准层在前 60 天免费提供。
* 如果存在使用安全中心方面的问题，请参阅 [Azure 安全中心常见问题解答](security-center-faq.md)。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

