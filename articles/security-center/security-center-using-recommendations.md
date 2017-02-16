---
title: "使用 Azure 安全中心建议增强安全性 | Microsoft 文档"
description: " 了解如何使用 Azure 安全中心内的安全策略和建议来帮助减少安全攻击. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 313801fc3b845cd30506b1a26918660e77d2a986
ms.openlocfilehash: 67095e0d8752fb3a2bca692f65e77cde5fd1a966


---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>使用 Azure 安全中心建议增强安全性
可以通过配置安全策略，然后实施 Azure 安全中心提供的建议，降低发生重大安全事件的可能性。 本文说明了如何使用安全中心内的安全策略和建议来帮助减少安全攻击。

> [!NOTE]
> 本文基于安全中心[规划和操作指南](security-center-planning-and-operations-guide.md)中引入的的角色和概念。 在继续阅读本文之前，最好先查看该规划指南。
>
>

## <a name="managing-security-recommendations"></a>管理安全建议
安全策略用于定义一组控制，这些控制是针对指定订阅或资源组中的资源建议的。 在安全中心内定义策略时，需考虑到公司的安全要求。 若要了解详细信息，请参阅[在安全中心设置安全策略](security-center-policies.md)。

资源组的安全策略从订阅级别继承。

![安全策略继承][1]

若需在特定资源组中应用自定义策略，可以在资源组中禁用继承。 若要禁用，请将“安全策略”边栏选项卡上的“继承”设置为“唯一”，并且对安全中心将为哪些控件显示建议进行自定义。

例如，如果有不需要 SQL 数据库透明数据加密 (TDE) 策略的工作负荷，请在订阅级别关闭该策略，仅在需要 SQL TDE 的资源组中启用。

> [!NOTE]
> 在订阅级别策略与资源组级别策略冲突时，以资源组级别策略为准。
>
>

安全中心将分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会基于安全策略中设置的控件创建建议。 这些建议将指导用户完成配置所需安全控件的过程。

安全中心内的当前策略建议聚焦于系统更新、操作系统配置、有关子网和虚拟机 (VM) 的网络安全组、SQL 数据库审核、SQL 数据库 TDE，以及 Web 应用程序防火墙。 有关安全中心建议的最新覆盖范围，请参阅[管理安全中心内的安全建议](security-center-recommendations.md)。

## <a name="scenario"></a>方案
此方案显示如何通过监视安全中心建议并采取措施，来使用安全中心帮助降低发生重大安全事件的可能性。 本方案使用虚构的公司 Contoso，以及安全中心[规划和操作指南](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)中提供的角色。 这些角色代表了可以使用安全中心执行不同的安全相关任务的个人和团队。 这些角色为：

![方案角色][2]

Contoso 最近将其某些本地资源迁移到了 Azure。 Contoso 希望实施并维护保护措施，减少对其云中的资源发起的安全攻击可利用的漏洞。

## <a name="recommended-solution"></a>建议的解决方案
一种解决方案是使用安全中心来预防并检测安全漏洞。 Contoso 有权通过其 Azure 订阅访问安全中心。 所有 Azure 订阅上均自动启用安全中心的[免费层](security-center-pricing.md)，并且其订阅中的所有 VM 上均会启用数据收集。

Contoso 的 IT 安全部门的 David 使用安全中心配置**安全策略**。 安全中心将分析 Contoso 的 Azure 资源安全状态。 安全中心识别到潜在的安全漏洞时，会基于安全策略中设置的控件创建**建议**。

云工作负荷所有者 Jeff 负责根据 Contoso 的安全策略实施和维护各种保护措施。 Jeff 可监视安全中心创建的建议，以应用保护措施。 这些建议将指导 Jeff 完成配置所需安全控件的过程。

为了使 Jeff 可以实施和维护保护措施并消除安全漏洞，他需要：

- 监视安全中心提供的安全建议
- 评估安全建议，并决定他是应当应用还是取消
- 应用安全建议

让我们按照 Jeff 的步骤，了解他如何使用安全中心建议来指导他完成配置控件以消除安全漏洞的过程。

## <a name="how-to-implement-this-solution"></a>如何实施该解决方案
Jeff 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)，然后打开安全中心控制台。 作为其每日监视活动的一部分，他会通过执行以下步骤来查看是否有安全建议：

1. Jeff 选择“建议”磁贴以打开“建议”边栏选项卡。
   ![选择“建议”磁贴][3]
2. Jeff 查看建议列表。 他会看到安全中心已按优先级顺序（从最高优先级到最低优先级）提供了建议列表。 他决定处理列表上的第一个高优先级建议。 他在“建议”边栏选项卡中选择“安装终结点保护”。
3. “安装终结点保护”边栏选项卡将打开，显示未启用反恶意软件的 VM 列表。 Jeff 查看 VM 列表，选择所有 VM，然后选择“在 3 个 VM 上安装”。
   ![安装终结点保护][4]
4. “选择终结点保护”边栏选项卡将打开，向 Jeff 提供两个反恶意软件解决方案。 Jeff 选择“Microsoft 反恶意软件”解决方案。
5. 将显示有关反恶意软件解决方案的其他信息。 Jeff 选择“创建”。
   ![Microsoft 反恶意软件][5]
6. Jeff 在“安装”边栏选项卡上输入所需的配置设置，然后选择“确定”。

[Microsoft 反恶意软件](../security/azure-security-antimalware.md)目前在所选 VM 上处于活动状态。

Jeff 继续浏览高优先级和中优先级建议，进行有关实施的决策。 Jeff 参考[管理安全建议](security-center-recommendations.md)文章，了解建议以及实施每个建议后的效果。

Jeff 获悉 [Microsoft 安全响应中心 (MSRC)](../security/azure-security-response-center.md) 会执行 Azure 网络和基础结构的选择安全监视，并接收来自第三方的威胁情报和滥用行为投诉。 如果 Jeff 为 Contoso 的 Azure 订阅提供安全联系人详细信息，那么在 MSRC 发现 Contoso 的客户数据已被非法的一方或未经授权方访问时，Microsoft 将联系 Contoso。 当 Jeff 应用“提供安全联系人详细信息”建议（上面的建议列表中严重性为“中”的建议）时，让我们按照他的步骤执行操作。

1. Jeff 选择“建议”边栏选项卡上的“提供安全联系人详细信息”，这将打开“提供安全联系人详细信息”边栏选项卡。
2. Jeff 选择要提供联系人信息的 Azure 订阅。 会再打开一个“提供安全联系人详细信息”边栏选项卡。
   ![安全联系人详细信息][6]
3. 在第二个“提供安全联系人详细信息”边栏选项卡上，Jeff 输入：

  - 由逗号分隔的安全联系人电子邮件地址（他可以输入的电子邮件地址数没有限制）
  - 一个安全联系人电话号码

4. Jeff 也会启用选项“向我发送有关警报的电子邮件”，以便接收有关高严重性警报的电子邮件。
5. Jeff 选择“确定”，将安全联系人信息应用到 Contoso 的订阅。

最后，Jeff 查看低优先级建议“修正 OS 漏洞”并确定此建议不适用。 他想要取消该建议。 Jeff 选择显示在右侧的三个点，然后选择“取消”。
   ![取消建议][7]

## <a name="conclusion"></a>结束语
监视安全中心内的建议可有助于在发生攻击之前消除安全漏洞。 可以通过使用安全中心内的安全策略来实施和维护保护措施，从而防止安全事件发生。

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png



<!--HONumber=Jan17_HO2-->


