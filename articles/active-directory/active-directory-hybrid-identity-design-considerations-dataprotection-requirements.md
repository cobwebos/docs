---
title: "混合标识设计 - 数据保护需求 Azure | Microsoft Docs"
description: "规划混合标识解决方案时，确定适用于业务的数据保护要求以及哪些选项可用于最好地满足这些要求。"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 1a2feca8d24fabda4c191b5a181ab6606c912657
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>通过强标识解决方案增强数据安全性的计划
保护数据的第一步是确定谁可以访问该数据，作为此过程的组成部分，需要具有可与系统集成的标识解决方案，以便提供身份验证和授权功能。 身份验证与授权经常给人造成混淆，并且其角色容易令人误解。 事实上它们有很大的差别，如下图中所示：

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**移动设备管理生命周期阶段**

规划混合标识解决方案时，必须了解适用于业务的数据保护要求以及哪些选项可用于最好地满足这些要求。

> [!NOTE]
> 完成规划数据安全性后，查看[确定多重身份验证要求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)，确保与多重身份验证要求相关的选择不受你在此部分所做决定的影响。
> 
> 

## <a name="determine-data-protection-requirements"></a>确定数据保护要求
在移动性的时代，大多数公司都有一个共同目标：使用户无论身处何地都可以以本地或远程方式在其移动设备上高效工作，以便提高工作效率。 虽然这可能是共同目标，但具有此类要求的公司同样会担心：要确保公司数据的安全和保护用户的隐私，必须缓解大量威胁。 就这一方面而言，每个公司可能具有不同的要求；因公司所属行业不同而有所不同的合规性规则将导致不同的设计决策。 

但任何行业的公司都需要对安全性的一些层面进行探讨和验证；下一节将对这些层面进行介绍。

## <a name="data-protection-paths"></a>数据保护路径
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**数据保护路径**

在上述关系图中，标识组件将是访问数据之前要验证的第一个组件。 但该数据可能在访问期间处于不同状态。 此关系图上的每个数字表示数据在某一时刻可能位于其中的路径。 这些数字的解释如下所示：

1. 设备级别的数据保护。
2. 传输过程中的数据保护。
3. 静态本地的数据保护。
4. 静态云中的数据保护。

IT 人员借以在其中每个阶段保护数据本身的技术控制并非由混合标识解决方案直接提供，但混合标识解决方案仍然必须能够在授予数据访问权限之前，利用本地和云标识管理资源确定用户身份。 规划混合标识解决方案时，确保根据组织的要求回答以下问题：

## <a name="data-protection-at-rest"></a>静态数据保护
无论静态数据位于何处（设备、云或本地），务必执行评估来了解组织在这方面的需求。 对于此方面，确保询问以下问题：

* 贵公司是否需要保护静态数据？
  * 如果是，则混合标识解决方案是否能够与当前的本地基础结构集成？
  * 如果是，则混合标识解决方案是否能够与位于云中的工作负荷集成？
* 云标识管理是否能够保护云中存储的用户的凭据和其他数据？

## <a name="data-protection-in-transit"></a>传输中的数据保护
必须保护在设备和数据中心之间或设备与云之间传输的数据。 但传输中并不一定意味着与云服务之外的组件进行通信；它也可能在内部移动，例如在两个虚拟网络之间。 对于此方面，确保询问以下问题：

* 贵公司是否需要保护传输中的数据？
  * 如果是，则混合标识解决方案是否能够与 SSL/TLS 等安全控制集成？
* 云标识管理是否与已签名的目录存储通信并将通信保持在该存储内部（数据中心内以及数据中心之间）？

## <a name="compliance"></a>合规性
法律法规和监管遵从性要求因公司所属行业不同而有所不同。 属于严格管控行业的公司必须解决与合规性问题有关的标识管理。 萨班斯-奥克斯利法案 (SOX)、健康保险可携性和责任法案 (HIPAA)、金融服务现代化法案 (GLBA) 和支付卡行业数据安全标准 (PCI DSS) 等法规对于标识和访问的规定是非常严格的。 贵公司会采用的混合标识解决方案必须具有能够满足上述一个或多个法规要求的核心功能。 对于此方面，确保询问以下问题：

* 混合标识解决方案是否遵循针对业务的法规要求？
* 混合标识解决方案是否具有使贵公司遵循法规要求的内置功能？ 

> [!NOTE]
> 务必记下每个答案并了解答案背后的依据。 [定义数据保护策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)会检查可用选项以及每个选项的优点/缺点。  回答了这些问题之后，就会挑选出最适合业务需求的选项。
> 
> 

## <a name="next-steps"></a>后续步骤
 [确定内容管理要求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)

