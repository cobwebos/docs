---
title: "使用 Azure 安全中心管理 Endpoint Protection 问题 | Microsoft Docs"
description: "了解如何在 Azure 安全中心内管理 Endpoint Protection 问题。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: b3b4a6df431ccdb882dd354aac9cb86a96a81b11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>使用 Azure 安全中心管理 Endpoint Protection 问题
Azure 安全中心监视反恶意软件保护状态，并在“Endpoint Protection 问题”边栏选项卡下报告此状态。 安全中心会突出显示此类问题，如检测到威胁和保护不充分，这可能会导致虚拟机 (VM) 和计算机容易受到反恶意软件威胁的攻击。 参考“Endpoint Protection 问题”下的信息，可以制定计划来解决发现的任何问题。

安全中心报告以下 Endpoint Protection 问题：

- 未在 Azure VM 上安装 Endpoint Protection - 未在 Azure VM 上安装支持的反恶意软件解决方案。
- 未在非 Azure 计算机上安装 Endpoint Protection - 未在非 Azure 计算机上安装支持的反恶意软件。
- Endpoint Protection 运行状况：

   - 签名过期 - 已在 VM 和计算机上安装反恶意软件解决方案，但解决方案没有最新的反恶意软件签名。
   - 无实时保护 - 已在 VM 和计算机上安装反恶意软件解决方案，但未配置实时保护。   可能是因为服务被禁用，也可能是因为安全中心由于解决方案不受支持而无法获取状态。 有关受支持的解决方案列表，请参阅[合作伙伴集成](security-center-partner-integration.md)。
   - 未报告 - 已安装反恶意软件解决方案，但其不报告数据。
   - 未知 - 已安装反恶意软件解决方案，但其状态未知或报告未知错误。

## <a name="implement-the-recommendation"></a>实现该建议
Endpoint Protection 问题在安全中心内显示为建议。  如果环境容易受到反恶意软件威胁的攻击，“建议”和“计算”下显示此建议。 若要查看“Endpoint Protection 问题”仪表板，需要遵循“计算”工作流。

此示例将使用“计算”。  下文将介绍如何在 Azure VM 和非 Azure 计算机上安装反恶意软件。

## <a name="install-antimalware-on-azure-vms"></a>在 Azure VM 上安装反恶意软件

1. 选择安全中心主菜单或“概览”下的“计算”。

   ![选择“计算”][1]

2. 在“计算”下，选择“Endpoint Protection 问题”。 此时，将打开“Endpoint Protection 问题”仪表板。

   ![选择“Endpoint Protection 问题”][2]

   仪表板顶部显示：

   - 已安装的 Endpoint Protection 提供程序 - 列出安全中心发现的各种提供程序。
   - 已安装的 Endpoint Protection 运行状况 - 显示已安装 Endpoint Protection 解决方案的 VM 和计算机的运行状况。 图表显示正常运行的 VM 和计算机数量，以及保护不充分的 VM 和计算机数量。
   - 检测到恶意软件 - 显示安全中心报告在其上检测到恶意软件的 VM 和计算机数量。
   - 受攻击的计算机 - 显示安全中心报告的遭到恶意软件攻击的 VM 和计算机数量。

   仪表板底部列出了 Endpoint Protection 问题，可了解下列信息：  

   - **总数** - 受问题影响的 VM 和计算机数量。
   - 汇总受问题影响的 VM 和计算机数量的条。 条中的颜色表示优先级：

      - 红色 - 优先级高，应立即处理
      - 橙色 - 优先级中等，应尽快处理

3. 选择“未在 Azure VM 上安装 Endpoint Protection”。

   ![选择“未在 Azure VM 上安装 Endpoint Protection”][3]

4. “未在 Azure VM 上安装 Endpoint Protection”下列出了没有安装反恶意软件的 Azure VM。  可以选择在列出的所有 VM 上安装反恶意软件，也可以逐个选择要安装反恶意软件的 VM，具体方法为单击特定的 VM。
5. 在“选择 Endpoint Protection”下，选择要使用的 Endpoint Protection 解决方案。 在此示例中，选择“Microsoft 反恶意软件”。
6. 将显示有关 Endpoint Protection 解决方案的其他信息。 选择“创建” 。

## <a name="install-antimalware-on-non-azure-computers"></a>在非 Azure 计算机上安装反恶意软件

1. 返回到“Endpoint Protection 问题”，并选择“未在非 Azure 计算机上安装 Endpoint Protection”。

   ![选择“未在非 Azure 计算机上安装 Endpoint Protection”][4]

2. 在“未在非 Azure 计算机上安装 Endpoint Protection”下，选择一个工作区。 此时，将打开筛选出工作区的 Log Analytics 搜索查询，并列出未安装反恶意软件的计算机。 选择列表中的计算机，以了解详细信息。

   ![Log Analytics 搜索][5]

此时，将会打开另一个搜索结果，其中筛选出了相应计算机的信息。

  ![Log Analytics 搜索][6]

> [!NOTE]
> 建议为所有 VM 和计算机预配 Endpoint Protection，这样有助于确定并删除病毒、间谍软件和其他恶意软件。
>
>

## <a name="next-steps"></a>后续步骤
本文档演示如何实现安全中心建议“安装终结点保护。” 若要了解有关在 Azure 中启用 Microsoft 反恶意软件的详细信息，请参阅以下文档：

* [适用于云服务和虚拟机的 Microsoft 反恶意软件](../security/azure-security-antimalware.md) - 了解如何部署 Microsoft 反恶意软件。

若要了解有关安全中心的详细信息，请参阅以下文档：

* [Setting security policies in Azure Security Center](security-center-policies.md)（在 Azure 安全中心设置安全策略）- 了解如何配置安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
