---
title: 在 Azure 安全中心应用系统更新 | Microsoft 文档
description: 本文档演示如何实现 Azure 安全中心建议**应用系统更新**和**在系统更新后重启**。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23040482"
---
# <a name="apply-system-updates-in-azure-security-center"></a>在 Azure 安全中心应用系统更新
Azure 安全中心每天对 Windows、Linux 虚拟机 (VM) 和计算机进行监控，以找出缺少的操作系统更新。 安全中心从 Windows 更新或 Windows Server Update Services (WSUS) 检索可用的安全更新和关键更新的列表，具体取决于 Windows 计算机上配置的服务。 安全中心还可以在 Linux 系统中检查最新更新。 如果 VM 或计算机缺少系统更新，安全中心将建议应用系统更新。

## <a name="implement-the-recommendation"></a>实现该建议
应用系统更新在安全中心中显示为建议。 如果 VM 或计算机缺少系统更新，此建议将在“建议”和“计算”下方显示。  选择该建议将打开“应用系统更新”仪表板。

本示例使用“计算”。

1. 选择安全中心主菜单下的“计算”。

   ![选择“计算”][1]

2. 在“计算”下，选择“缺少系统更新”。 “应用系统更新”仪表板将打开。

   ![“应用系统更新”仪表板][2]

   仪表板顶部提供：

    - 缺少系统更新的 Windows、Linux VM 以及计算机的总数。
    - VM 和计算机中缺少的关键更新的总数。
    - VM 和计算机中缺少的安全更新的总数。

  仪表板底部列出了 VM 和计算机中缺少的所有更新以及缺少更新的严重性。  此列表包括：

    - 名称：所缺更新的名称。
    - VM 和 计算机数：缺少此更新的 VM 和计算机的总数。
    - 状态：该建议的当前状态：

      - 未解决：建议尚未得到处理。
      - 正在进行：目前已将建议应用到相关资源，无需用户采取行动。
      - 已解决：已完成建议。 （解决问题后，此条目将变暗）。

    - 严重性：描述该特定建议的严重性：

      - 高：重要资源（应用程序、虚拟机或网络安全组）存在漏洞，需要引起注意。
      - 中：需要采取非关键步骤或额外步骤来完成某个过程或消除某个漏洞。
      - 低：漏洞需要解决，但不需立即处理。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。）

3. 选择列表中的缺少更新以查看详细信息。

   ![缺少的安全更新][3]

4. 选择顶部功能区中的“搜索”图标。  随即打开 Log Analytics 搜索查询，筛选出缺少更新的计算机。

   ![Log Analytics 搜索][4]

5. 选择列表中的计算机，以了解详细信息。 此时，将会打开另一个搜索结果，其中筛选出了相应计算机的信息。

    ![Log Analytics 搜索][5]

## <a name="reboot-after-system-updates"></a>在系统更新后重启
1. 返回到“建议”边栏选项卡。 应用系统更新后，将生成名为**在系统更新后重启**的新项。 此项提醒需要重启 VM 才能完成应用系统更新的过程。

   ![在系统更新后重启][6]
2. 选择“在系统更新后重启”。 这会打开“重启处于挂起状态，以完成系统更新”边栏选项卡，显示需要重新启动以完成应用系统更新过程的 VM 列表。

   ![重新启动挂起][7]

从 Azure 重新启动 VM 以完成此过程。

## <a name="next-steps"></a>后续步骤
若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
