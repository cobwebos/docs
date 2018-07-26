---
title: 在 Azure 安全中心中修正安全配置 | Microsoft Docs
description: 本文档演示如何实现 Azure 安全中心建议“修正安全配置”。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: 3c56abcec37bb6abcb77ec8cc443b0656bd69932
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990789"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>在 Azure 安全中心中修正安全配置
Azure 安全中心每天分析虚拟机 (VM) 和计算机操作系统 (OS)，检查是否存在可能使 VM 和计算机更易受到攻击的配置。 当 OS 配置与建议的安全配置规则不匹配时，安全中心建议解决漏洞，并建议修复这些漏洞所需的配置更改。

若要详细了解受监视的具体配置，请参阅[建议的配置规则列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 若要了解如何自定义安全配置评估，请参阅[在 Azure 安全中心（预览）自定义 OS 安全配置](security-center-customize-os-security-config.md)。

## <a name="implement-the-recommendation"></a>实现该建议
安全中心中会显示“修正安全配置”建议。 建议显示在“建议” > “计算和应用”下。

此示例介绍“计算和应用”下的“修正安全配置”建议。
1. 在安全中心的左窗格中，选择“计算和应用”。  
  “计算和应用”窗口随即打开。

   ![修正安全配置][1]

2. 选择“修正安全配置”。  
  此时会打开“安全配置”窗口。

   ![“安全配置”窗口][2]

  仪表板的上半部分显示：

  - **按严重性划分的失败规则数**：VM 和计算机中按严重性细分的 OS 配置失败的规则总数。
  - **按类型划分的失败规则数**：VM 和计算机中按类型细分的 OS 配置失败的规则总数。
  - **失败的 Windows 规则数**：按 Windows OS 配置确认的失败规则总数。
  - **失败的 Linux 规则数**：按 Linux OS 配置确认的失败规则总数。

  仪表板底部列出 VM 和计算机的所有失败规则以及缺少更新的严重性。 此列表包含以下元素：

  - **CCEID**：规则的 CCE 唯一标识符。 安全中心使用通用配置枚举 (CCE) 将唯一标识符分配到配置规则。
  - **名称**：失败的规则的名称。
  - **规则类型**：“注册表项”、“安全策略”、“审核策略”或“IIS”规则类型。
  - **VM 和计算机数**：对其应用失败规则的 VM 和计算机的总数。
  - **规则严重性**：CCE 值（紧急、重要或警告）。
  - **状态**：该建议的当前状态：

    - **未解决**：建议尚未得到处理。
    - **正在进行**：目前已将建议应用到相关资源，不需用户采取行动。
    - **已解决**：已应用建议。 解决问题后，条目会变暗。

3. 若要查看失败规则的详细信息，请在列表中将其选中。

   ![失败的配置规则的详细视图][3]

   详细视图显示以下信息：

   - **名称**：规则的名称。
   - **CCIED**：规则的 CCE 唯一标识符。
   - **OS 版本**：VM 或计算机的 OS 版本。
   - **规则严重性**：CCE 值（紧急、重要或警告）。
   - **完整说明**：规则的说明。
   - **漏洞**：说明不应用规则时会出现哪些漏洞或风险。
   - **潜在影响**：应用规则后的业务影响。
   - **对策**：修正步骤。
   - **预期值**：安全中心针对规则分析 VM OS 配置时的预期值。
   - **实际值**：在针对规则分析 VM OS 配置后返回的值。
   - **规则操作**：安全中心在针对规则分析 VM OS 配置过程中使用的规则操作。

4. 在详细视图窗口顶部，选择“搜索”。  
  此时“搜索”会打开一个工作区列表，这些工作区的 VM 和计算机的所选安全配置不匹配。 仅在所选规则应用到多个 VM，而这些 VM 又连接到不同工作区时，才会显示工作区选择情况。

   ![列出的工作区][4]

5. 选择工作区。  
  Log Analytics 搜索查询将打开，并筛选到与安全配置不匹配的工作区。

   ![包含 OS 漏洞的工作区][5]

6. 在列表中选择一台计算机。  
  此时，将会打开新的搜索结果，其中筛选出了相应计算机的信息。

   ![所选计算机的详细信息][6]

## <a name="next-steps"></a>后续步骤
本文演示如何实现安全中心建议“修正安全配置”。 若要了解如何自定义安全配置评估，请参阅[在 Azure 安全中心（预览）自定义 OS 安全配置](security-center-customize-os-security-config.md)。

若要查看受监视的具体配置，请参阅[建议的配置规则列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 安全中心使用通用配置枚举 (CCE) 将唯一标识符分配到配置规则。 有关详细信息，请访问 [CCE](https://nvd.nist.gov/cce/index.cfm) 站点。

若要了解有关安全中心的详细信息，请参阅以下资源：

* 如需支持的 Windows 和 Linux VM 的列表，请参阅 [Azure 安全中心支持的平台](security-center-os-coverage.md)。
* 若要了解如何配置 Azure 订阅和资源组的安全策略，请参阅 [在 Azure 安全中心设置安全策略](security-center-policies.md)。
* 若要了解如何使用安全建议来保护 Azure 资源，请参阅[管理 Azure 安全中心安全建议](security-center-recommendations.md)。
* 若要了解如何监视 Azure 资源的运行状况，请参阅 [Azure 安全中心的安全运行状况监视](security-center-monitoring.md)。
* 若要了解如何管理和响应安全警报，请参阅[管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)。
* 若要了解如何监视合作伙伴解决方案的运行状态，请参阅[通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)。
* 若要获取服务使用方面的常见问题的答案，请参阅 [Azure 安全中心常见问题解答](security-center-faq.md)。
* 若要查找 Azure 安全性及符合性方面的博客文章，请查看 [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
