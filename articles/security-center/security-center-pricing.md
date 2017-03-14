---
title: "安全中心定价 | Microsoft 文档"
description: "本文提供有关 Azure 安全中心定价的信息。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 9c20ace9112c62e733b7b679e3f0be717144f3c2
ms.openlocfilehash: 933a485e36b3cf0f514f28fc069d49161605af3a
ms.lasthandoff: 02/23/2017


---
# <a name="azure-security-center-pricing"></a>Azure 安全中心定价
Azure 安全中心有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

## <a name="pricing-tiers"></a>定价层
安全中心分两个层提供：

* **免费层**在所有 Azure 订阅上自动启用。 免费层可查看 Azure 资源、基本安全策略、安全建议和与合作伙伴的安全产品和服务的集成的安全状态。
* **标准层**增添了高级威胁检测功能，其中包括威胁情报、行为分析、异常检测、安全事件和安全评估报告。 标准层在前 60 天免费提供。

有关详细信息，请参阅安全中心[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 安全中心使用 Azure 存储器保存从受保护节点中生成的安全数据。 与存储器关联的成本不包括在服务的价格中，并将单独按常规 [Azure 存储器费率](https://azure.microsoft.com/pricing/details/storage/blobs/)进行计费。 即使在 60 天免费试用期间也会收取存储费用。
>
>

## <a name="try-standard-free-for-60-days"></a>免费试用标准层 60 天
标准层在前 60 天免费提供。 60 天后，如果选择继续使用服务，将自动开始收取使用费用。

若要获取标准层，请执行以下操作：

1. 在“安全中心”边栏选项卡上选择“策略”磁贴。
2. 选择要升级到标准的订阅。
3. 在“安全策略”边栏选项卡上，选择“定价层”。
4. 在“选择定价层”边栏选项卡上，选择“标准”。
5. 单击“选择”。

![标准层][1]

## <a name="why-upgrade-to-standard"></a>为什么要升级到标准？
安全中心标准层提供免费层所有功能以及高级威胁检测。 高级威胁检测有助于识别攻击 Azure 资源的活动威胁，并提供快速响应所需的见解。

安全中心使用各种高级安全分析，远不止几种基于攻击特征的方法。 可利用大数据和机器学习技术的突破跨整个云结构对事件进行评估，检测那些使用手动方式不可能发现的威胁，并预测攻击的发展方式。

标准层附带的安全分析包括：

* **威胁情报** - 充分利用 Microsoft 产品和服务、Microsoft 数字犯罪部门、Microsoft 安全响应中心以及外部源提供的全球威胁情报，搜寻已知的行为不端的攻击者
* **行为分析** - 运用已知模式发现恶意行为
* **异常检测** - 使用统计分析生成历史基线。 如果出现与已知基线偏离的情况，并且这些情况符合潜在攻击载体的行为，则会发出警报

在“安全警报”边栏选项卡中，安全中心已检测到一个安全**事件**。 安全事件是对资源的所有警报汇总，与网络攻击链模式保持一致。 选择安全事件会显示关于此事件的更多详细信息以及列出相关警报。 选择警报会提供该事件的详细信息。

![安全事件][2]

下面的“网络通信”警报提供了该警报的详细信息。 详细信息包括其完整说明、严重性、当前状态（本例中已解除，标识用户采取操作将其解除）、受攻击资源以及补救措施。 此外，还有一列 Microsoft 威胁情报报告的链接。 这些报告可用作安全补救和防御用途。

![安全警报详细信息][3]

## <a name="enable-data-collection"></a>启用数据收集
若要启用虚拟机行为分析，必须启用数据收集。

若要验证是否已启用数据收集，请执行以下操作：

1. 选择“策略”磁贴。 “安全策略”边栏选项卡展开，列出 Azure 订阅。
2. 选择一个订阅。
3. 如果“数据收集”处于关闭状态，将其更改为开启并保存更改。

有关详细信息，请参阅[在 Azure 安全中心内启用数据收集](security-center-enable-data-collection.md)。

## <a name="next-steps"></a>后续步骤
* 本文档介绍安全中心定价。 有关其他定价信息，请参阅安全中心[定价页](https://azure.microsoft.com/pricing/details/security-center/)。
* 若要了解安全中心高级检测功能的详细信息，请参阅 [Azure 安全中心检测功能](security-center-detection-capabilities.md)。
* 如果存在使用安全中心方面的问题，请参阅 [Azure 安全中心常见问题解答](security-center-faq.md)。
* 如果使用安全中心方面仍存在问题，请访问 [Azure 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc)。

<!--Image references-->
[1]: ./media/security-center-pricing/standard.png
[2]: ./media/security-center-pricing/incident.png
[3]: ./media/security-center-pricing/network-alert.png

