---
title: "载入到 Azure 安全中心标准层以增强安全性 | Microsoft Docs"
description: " 了解如何载入到 Azure 安全中心标准层以增强安全性。 "
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
ms.date: 09/14/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>载入到 Azure 安全中心标准层以增强安全性
升级到安全中心标准层后，可以将增强的安全管理和威胁防护功能用于混合云工作负载。  可以免费试用标准层 60 天。 请参阅安全中心[定价页](https://azure.microsoft.com/pricing/details/security-center/)了解详细信息。

安全中心标准层包括：

- 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自各种来源（包括防火墙和其他合作伙伴解决方案）的安全数据。
- 高级威胁检测 - 使用高级分析和 Microsoft Intelligent Security Graph，获得针对不断演变的网络攻击的优势。  利用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
- 访问和应用程序控件 - 通过应用适合特定工作负载且由机器学习提供支持的白名单建议，阻止恶意软件和其他不需要的应用程序。 通过对 Azure VM 上管理端口的实时控制访问减小网络攻击面，显著减小在暴力和其他网络攻击下的曝光面。

## <a name="detecting-unprotected-resources"></a>检测未受保护的资源     
安全中心将自动检测未启用安全中心标准层的所有 Azure 订阅或工作区。 其中包括使用安全中心免费层的 Azure 订阅和未启用安全解决方案的工作区。

可以将整个 Azure 订阅升级到标准层，这样此订阅中的所有资源均将继承此层，或者可以定义唯一的策略来仅升级特定的资源组。 如果该资源组的策略设置是唯一的，则将订阅升级到标准层时安全中心不会重写定价策略。 将标准层应用到订阅仅适用于订阅中向安全中心创建的工作区报告的 VM。 将标准层应用到工作区适用于向工作区报告的所有资源。

> [!NOTE]
> 你可能希望管理成本，并通过将解决方案的应用范围限制为特定的一组代理来限制为解决方案收集的数据量。 [解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)使用户可以向解决方案应用一个范围，并可将目标设定为工作区中的一个计算机子集。  如果使用解决方案目标功能，安全中心会将工作区列为没有解决方案。
>
>

## <a name="upgrade-an-azure-subscription"></a>升级 Azure 订阅
将所有订阅升级到标准层：
1. 在“安全中心”主菜单下，选择“载入”。
2. 在“载入高级安全”下，安全中心将列出符合载入条件的订阅。 可以通过选择“应用标准层计划”升级所有列出的订阅。

  ![升级所有订阅][1]

若要将单个订阅升级到标准层：可以通过选择“应用标准层”从“载入”升级某个订阅。 若要将订阅下的某个资源组升级到标准层，请选择此订阅：
1. 选择一个订阅。  “安全策略”将提供有关此订阅中包含的资源组的信息。
2. 选择此订阅或某个资源组。

  ![升级所有订阅][2]

3. 选择“标准层”，从“免费层”升级到“标准层”。
4. 选择“保存”。

> [!NOTE]
> 如果之前禁用了[自动设置](security-center-enable-data-collection.md)，将订阅升级到标准层时将启用此功能。 建议启用监视代理的自动设置功能。
>
>

## <a name="upgrade-a-workspace"></a>升级工作区
将标准层应用到工作区适用于向工作区报告的所有资源。

1. 返回到“载入”边栏选项卡。
2. 选择工作区。

  ![升级工作区][8]

3. 选择“标准层”以进行升级。  
4. 选择“保存”。

   > [!NOTE]
   > 有一种情况是你未将免费层或标准层应用到工作区。 如果选择免费层，则安全中心的免费层功能将仅应用到 Azure VM。 免费层功能不会应用到非 Azure 计算机。 如果选择标准层，标准层功能将应用到向工作区报告的所有 Azure VM 和非 Azure 计算机。 建议应用标准层，以便为 Azure 和非 Azure 资源提供高级安全功能。
   >
   >

## <a name="onboard-non-azure-computers"></a>载入非 Azure 计算机
安全中心可以监视非 Azure 计算机的安全状态，但首先需要载入这些资源。 可以从“载入”边栏选项卡或“计算”边栏选项卡添加非 Azure 计算机。 我们将逐步介绍这两种方法。

### <a name="add-new-non-azure-computers-from-onboarding"></a>从“载入”添加新的非 Azure 计算机

1. 返回到“载入”。   
2. 选择“是否要添加新的非 Azure 计算机”。

  ![添加非 Azure 计算机][3]

如果已有工作区，则会将它们列在“添加新的非 Azure 计算机”下。 可以将计算机添加到现有的工作区，也可以新建一个工作区。 若要新建一个工作区，请选择“添加新工作区”链接。

我们将逐步介绍以下两种方法：

- 新建一个工作区并添加计算机
- 选择现有的工作区并添加计算机

**新建一个工作区并添加计算机**

1. 在“添加新的非 Azure 计算机”下，选择“添加新工作区”。

   ![添加新工作区][4]

2. 在“安全和审核”下，选择“OMS 工作区”以新建工作区。
3. 在“OMS 工作区”下，输入工作区的相关信息。
4. 在“OMS 工作区”下，选择“确定”。  选择“确定”后，将获取一个用于下载 Windows 或 Linux 代理的链接和工作区 ID 的密钥（用于配置此代理）。
5. 在“安全和审核”下，选择“确定”。

**选择现有的工作区并添加计算机**

可以按照“载入”中的工作流操作，添加计算机，如下所示。 也可以按照“计算”中的工作流来添加计算机。 本示例使用“计算”。

1. 返回到安全中心的主菜单，并转到“概述”仪表板。

   ![概述][5]

2. 选择“计算”磁贴。
3. 在“计算”下，选择“添加计算机”。

   ![“计算”边栏选项卡][6]

4. 在“添加新的非 Azure 计算机”下，选择要将计算机连接到的工作区，然后单击“添加计算机”。

   ![添加计算机][7]

 “直接代理”边栏选项卡会提供一个用于下载 Windows 或 Linux 代理的链接，并提供工作区 ID 密钥（用于配置此代理）。   

## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何载入 Azure 和非 Azure 资源，以便利用安全中心的高级安全功能的优势。  若要对载入资源执行更多操作，请参阅

- [启用数据收集](security-center-enable-data-collection.md)
- [威胁智能报告](security-center-threat-report.md)
- [实时访问 VM](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png

