---
title: "修正 Azure 安全中心中的 OS 漏洞 | Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**修正 OS 漏洞**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 39879c22278a55f841e294cda5a89bec2bdf6988
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>修正 Azure 安全中心中的 OS 漏洞
Azure 安全中心每天分析虚拟机 (VM) 和计算机操作系统 (OS)，检查是否存在可能使 VM 和计算机更易受到攻击的配置。 当 OS 配置与建议的配置规则不匹配时，安全中心建议解决漏洞，并建议更改配置以修复这些漏洞。

> [!NOTE]
> 有关受监视的具体配置的详细信息，请参阅[建议的配置规则列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
安全中心中显示“修正 OS 漏洞”建议。 此建议将显示在“建议”和“计算”下。

在本示例中，我们将在“计算”下查看“修复操作系统漏洞(由 Microsoft 修复)”建议。
1. 选择安全中心主菜单下的“计算”。

   ![修正 OS 漏洞][1]

2. 在“计算”下，选择“修复操作系统漏洞(由 Microsoft 修复)”。 “OS 漏洞(Microsoft)不匹配”仪表板将打开。

   ![修正 OS 漏洞][2]

  仪表板顶部提供：

  - 按严重性分类的 VM 和计算机 OS 配置失败的规则总数。
  - 按类型分类的 VM 和计算机 OS 配置失败的规则总数。
  - Windows OS 配置和 Linux OS 配置失败的规则总数。

  仪表板底部列出 VM 和计算机中所有失败的规则以及缺少更新的严重性。 此列表包括：

  - CCEID：规则的 CCE 唯一标识符。 安全中心使用通用配置枚举 (CCE) 来分配配置规则的唯一标识符。
  - 名称：失败的规则的名称
  - 规则类型：注册表项、安全策略或审核策略
  - VM 和计算机数**：应用失败的规则的 VM 和计算机的总数
  - 规则严重性：CCE 严重性值（紧急、重要或警告）
  - **状态**：该建议的当前状态：

    - **未解决**：建议尚未得到处理
    - 正在进行：目前已将建议应用到相关资源，不需要用户采取行动
    - **已解决**：已完成建议。 （解决问题后，此条目将变暗）

3. 选择列表中的失败规则以查看详细信息。

   ![失败的配置规则][3]

  在此边栏选项卡上提供以下信息：

  - NAME - 规则的名称
  - CCIED - 规则的 CCE 唯一标识符
  - OS 版本 – VM 或计算机的 OS 版本
  - 规则严重性 -- CCE 严重性值（紧急、重要或警告）
  - 完整说明 - 规则的说明
  - VULNERABILITY - 如果不应用规则，对于出现的漏洞或风险的解释
  - 潜在影响 - 应用规则后产生的业务影响
  - 对策 – 修正步骤
  - EXPECTED VALUE - 安全中心针对规则分析 VM OS 配置时的预期值
  - ACTUAL VALUE - 在针对规则分析 VM OS 配置后返回的值
  - RULE OPERATION - 在安全中心针对规则分析 VM OS 配置过程中使用的规则操作

4. 选择顶部功能区中的“搜索”图标。 “搜索”打开，并列出包含具有所选 OS 漏洞的 VM 和计算机的工作区。 仅在所选规则应用到多个连接到不同工作区的 VM 时才会显示此工作区选择边栏选项卡。

  ![列出的工作区][4]

5. 选择工作区。 Log Analytics 搜索查询打开，并筛选到包含 OS 漏洞的工作区。

  ![包含 OS 漏洞的工作区][5]

6. 从列表中选择一个计算机以了解详细信息。 此时，将会打开另一个搜索结果，其中筛选出了相应计算机的信息。

  ![仅为该计算机筛选的信息][6]

## <a name="next-steps"></a>后续步骤
本文档演示如何实现安全中心建议“修正 OS 漏洞”。 可以在[此处](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)查看配置规则的集。 安全中心使用 CCE（通用配置枚举）来分配配置规则的唯一标识符。 请访问 [CCE](https://nvd.nist.gov/cce/index.cfm) 站点以了解详细信息。

若要了解有关安全中心的详细信息，请参阅以下资源：

* [Azure 安全中心支持的平台](security-center-os-coverage.md) - 提供支持的 Windows 和 Linux VM 列表。
* [在 Azure 安全中心设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心管理安全建议](security-center-recommendations.md) - 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png

