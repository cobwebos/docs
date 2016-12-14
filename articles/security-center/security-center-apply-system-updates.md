---
title: "在 Azure 安全中心应用系统更新 | Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**应用系统更新**和**在系统更新后重启**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51501cc5e344c321652982a3a7f448d84f892392


---
# <a name="apply-system-updates-in-azure-security-center"></a>在 Azure 安全中心应用系统更新
Azure 安全中心每天对 Windows 和 Linux 虚拟机 (VM) 进行监控以找出缺少的操作系统更新。 安全中心从 Windows Update 或 Windows Server Update Services (WSUS) 检索可用的安全更新和关键更新的列表，具体取决于 Windows VM 上配置的服务。  安全中心还可以在 Linux 系统中检查最新更新。 如果你的 VM 缺少系统更新，安全中心将建议你应用系统更新

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。
> 
> 

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“应用系统更新”。
   ![应用系统更新][1]
2. “应用系统更新”边栏选项卡将打开，显示缺少系统更新的 VM 列表。 选择 VM。
   ![选择 VM][2]
3. 边栏选项卡将打开，显示该 VM 缺少的更新的列表。 选择系统更新。 在此示例中，选择 KB3156016。
   ![缺少的安全更新][3]
4. 按照“安全更新”边栏选项卡中的步骤来应用缺少的更新。
   ![安全更新][4]

## <a name="reboot-after-system-updates"></a>在系统更新后重启
1. 返回到“建议”边栏选项卡。 应用系统更新后，将生成名为**在系统更新后重启**的新项。 此项提醒你需要重启 VM 才能完成应用系统更新的过程。
   ![在系统更新后重启][5]
2. 选择“在系统更新后重启”。 这将打开“重启处于挂起状态，以完成系统更新”边栏选项卡，显示需要重新启动以完成应用系统更新过程的 VM 列表。
   ![重新启动挂起][6]

从 Azure 重新启动 VM 以完成此过程。

## <a name="see-also"></a>另请参阅
若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png



<!--HONumber=Nov16_HO3-->


