---
title: "Azure Active Directory 混合标识设计注意事项 - 确定混合标识管理任务 | Microsoft 文档"
description: "借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查选取的特定条件，然后才允许访问应用程序。 一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 921c8391fc18eca35d10c3ade158a98ae88df397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>规划混合标识生命周期
标识是企业移动性和应用程序访问策略的基石之一。 无论登录移动设备还是 SaaS 应用，标识都是能否进行访问的关键所在。 从最高层面讲，标识管理解决方案涉及到存储库的统合与同步，其中又包含预配资源过程的自动化和集中化。 标识解决方案应为跨本地与云的集中式标识功能，且应使用某种形式的标识联合，以维护集中式身份验证，安全地与外部用户和企业进行共享和协作。 资源的范围涵盖操作系统和应用程序，乃至组织中或隶属于组织的人员。 组织结构可以改变，以适应预配策略和过程。

为用户提供标识解决方案，使其体验自助式服务以提升能力从而保持生产力，这一点很重要。 标识解决方案如果能让用户在其需要访问的所有资源级别上进行的所有资源级别，将更加稳健。管理员可在各个级别上使用标准化过程来管理用户凭据。 根据预配管理解决方案的范围，可以减少或消除某些级别的管理。 此外，可以安全地在不同组织之间手动或自动分配管理功能。 例如，域管理员只能为该域中的人员和资源提供服务。 此用户可以执行管理和预配任务，但无权执行配置任务，例如创建工作流。

## <a name="determine-hybrid-identity-management-tasks"></a>确定混合标识管理任务
在组织中分配管理任务可以改善管理的精准度和效率，改善组织的工作负荷均衡性。 下面是定义稳健标识管理系统的要点。

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

若要定义混合标识管理任务，必须了解采用混合标识的组织有哪些基本特征。 必须了解当前用于标识源的存储库。 了解这些核心元素之后，即建立了基本要求，接下来需要据此提出更细微的问题，为标识解决方案做出更理想的设计决策。  

定义这些要求时，至少要回答以下问题

* 预配选项： 
  
  * 混合标识解决方案是否支持稳健的帐户访问管理和预配系统？
  * 以何种方式管理用户、组和密码？
  * 标识生命周期管理的响应度如何？ 
    * 密码更新帐户暂停需要多长时间？
* 许可证管理： 
  
  * 混合标识解决方案是否处理许可证管理？
    * 如果是，可以使用哪些功能？
* 解决方案是否处理基于组的许可证管理？ 
  
      - 如果是，是否可向它分配安全组？ 
       - 如果是，云目录是否自动将许可证分配到组中的所有成员？ 
        - 如果随后在组中添加或删除用户，将发生什么情况？会相应地自动分配或删除许可证吗？ 
* 与其他第三方标识提供者集成：
* 此混合解决方案是否可与第三方标识提供者集成以实现单一登录？
* 是否可将所有不同的标识提供者统合到一致的标识系统中？
* 如果是，有哪些系统？如何运行？可用的功能有哪些？

## <a name="synchronization-management"></a>同步管理
标识管理员的目标之一是启用所有标识提供者并保持其同步状态。 可以根据权威的主标识提供者保持数据的同步状态。 在混合标识方案中，可以通过同步的管理模型在本地服务器中管理所有用户和设备标识，并将帐户同步到云（以及选择性地同步密码）。 用户在本地中输入的密码与在云中相同，登录时，标识解决方案会验证密码。 此模型使用目录同步工具。

![](./media/hybrid-id-design-considerations/Directory_synchronization.png) 若要适当地设计混合标识解决方案的同步，请务必回答以下问题：• 适用于混合标识解决方案的同步解决方案是什么？
• 可用的单一登录功能是什么？
• B2B 和 B2C 之间的标识联合选项是什么？

## <a name="next-steps"></a>后续步骤
[确定混合标识管理采用策略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)

