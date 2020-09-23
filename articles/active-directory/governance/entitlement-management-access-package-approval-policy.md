---
title: 在 Azure AD 授权管理-Azure Active Directory 中更改访问包的批准设置
description: 了解如何在 Azure Active Directory 权限管理中更改访问包的批准和请求者信息设置。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e36b2d4576b43544bec89efd326363344b35be9
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994600"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>更改审批和请求者信息 (预览版中的访问包) 设置 Azure AD 授权管理

作为访问包管理器，你可以通过编辑现有策略或添加新策略，随时更改访问包的批准和请求方信息设置。

本文介绍如何更改现有访问包的批准和请求方信息设置。

## <a name="approval"></a>审批

在“审批”部分中，指定用户请求此访问包时是否需要审批。 审批设置的工作方式如下：

- 只有一个选定审批者或后备审批者需要批准单阶段审批的请求。 
- 每个阶段中只有一个选定审批者需要批准两阶段审批的请求。
- 审批者可以是管理员、内部发起人或外部发起人，具体取决于策略管理谁的访问权限。
- 单阶段或两阶段审批不需要每个选定审批者的批准。
- 审批决定以第一个评审请求的审批者为准。

有关如何向请求策略添加审批者的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

有关如何向请求策略添加多阶段审批的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>更改现有访问包的审批设置

请按照以下步骤指定请求访问包的审批设置：

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 选择要编辑的策略，或向访问包添加新策略
    1. 如果要创建新策略，请单击 " **策略** "，然后单击 " **添加策略** "。
    1. 单击要编辑的策略，然后单击 " **编辑**"。

1. 请参阅 **请求** 选项卡。

1. 若要要求对所选用户发起的请求进行审批，请将“需要审批”切换开关设置为“是”。******** 或者，若要自动审批请求，请将切换开关设置为“否”。

1. 如果需要用户提供对请求访问包的论证，请将“需要请求者论证”切换开关设置为“是”。 。
    
1. 现在确定请求是否需要单阶段或两阶段审批。 对于单阶段审批，将”阶段数“切换开关设置为“1”，或者对于两阶段审批，将切换开关设置为“2”  。

    ![访问包 - 请求 - 审批设置](./media/entitlement-management-access-package-approval-policy/approval.png)


选择所需的阶段数后，使用以下步骤添加审批者： 

### <a name="single-stage-approval"></a>单阶段审批

1. 添加“第一位审批者”：
    
    如果策略设置为管理目录中的用户访问，可以选择“管理员充当审批者”。 或者，在下拉菜单中选择“选择特定审批者”后，单击“添加审批者”，以添加特定用户。
    
    ![访问包 - 请求 - 目录中的用户 - 第一位审批者](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    如果将此策略设置为管理不在目录中的用户的访问，则可以选择“外部发起人”或“内部发起人” 。 或者，通过单击“选择特定审批者”下的“添加审批者”或组来添加特定用户。
    
    ![访问包 - 请求 - 目录外的用户 - 第一位审批者](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. 如果你选择了“管理员”作为第一位审批者，请单击“添加后备审批者”，以选择目录中的一个或多个用户或组作为后备审批者 。 如果权利管理找不到请求访问权限的用户的管理员，后备审批者将收到请求。

    权利管理使用“管理员”属性找到管理员。 该属性位于 Azure AD 中的用户配置文件中。 有关详细信息，请参阅[使用 Azure Active Directory 添加或更新用户的配置文件信息](../fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果选择了“选择特定审批者”，请单击“添加审批者”以选择目录中的一个或多个用户或组作为审批者 。

1. 在“必须在多少天内作出决定？”框下，指定审批者审阅对此访问包的请求的允许天数。

    如果请求在这段时间内未获批准，将自动被拒绝。 用户必须再提交一个访问包的请求。

1. 如果需要审批者提供其决策论证，请将“需要审批者论证”设置为“是”。

    其他审批者和请求者都可以看到该论证。

### <a name="2-stage-approval"></a>两阶段审批

如果选择了两阶段审批，则需要添加第二位审批者。

1. 添加“第二位审批者”： 
    
    如果用户在目录中，可以通过单击“选择特定审批者”下的“添加审批者”添加特定用户作为第二位审批者。

    ![访问包 - 请求 - 目录中的用户 - 第二位审批者](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    如果用户不在目录中，请选择“内部发起人”或“外部发起人”作为第二位审批者 。 选择审批者后，添加后备审批者。

    ![访问包 - 请求 - 目录外的用户 - 第二位审批者](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. 在“必须在多少天内做出决策？”下的框中指定第二位审批者审批请求的允许天数。 

1. 将“需要审批者论证”切换开关设置为“是”或“否” 。

### <a name="alternate-approvers"></a>后备审批者

可以指定后备审批者，类似于指定可以审批请求的第一位和第二位审批者。 指定后备审批者将有助于确保请求在到期（超时）之前被批准或拒绝。 针对两阶段审批，可以为后备审批者列出第一位审批者和第二位审批者。 

指定后备审批者后，在第一位或第二位审批者无法批准或拒绝请求的情况下，待处理的请求将根据你在策略设置期间指定的转发计划转发给后备审批者。 他们会收到一封批准或拒绝待处理请求的电子邮件。

请求被转发给后备审批者后，第一位或第二位审批者仍可批准或拒绝该请求。 后备审批者使用同一个“我的访问权限”网站来批准或拒绝待处理的请求。

我们可以列出要成为审批者和后备审批者的人员或组。 请确保列出不同的人员集作为第一位、第二位和后备审批者。
例如，如果将 Alice 和 Bob 列为第一位审批者，则将 Carol 和 Dave 列为后备审批者。 通过以下步骤将后备审批者添加到访问包中：

1. 在“第一位审批者”和/或“第二位审批者”下，单击“显示高级请求设置”。

    ![访问包 - 策略 - 显示高级请求设置](./media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png)

1. 将“若没有采取任何行动，转发给后备审批者们？”切换开关设置为“是” 。

1. 单击“添加后备审批者”，然后从列表中选择后备审批者。

    ![访问包 - 策略 - 添加后备审批者](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

1. 在“多少天后转发给后备审批者？”框中，输入审批者批准或拒绝请求的允许天数。 如果在请求持续期间没有审批者批准或拒绝请求，则请求将过期（超时），用户必须再提交一个访问包请求。 

    只有在请求持续时间已过一半后，请求才会被转发给后备审批者，主审批者的决策必须在至少 4 天后超时。 如果请求超时小于或等于 3，则没有足够的时间将请求转发给后备审批者。 在本例中，请求的持续时间为 14 天。 因此，请求持续时间在第 7 天已达到一半。 所以请求不能早于第 8 天转发。 此外，请求不能在请求持续时间的最后一天转发。 因此，在本例中，可以转发请求的最晚时间是第 13 天。

## <a name="enable-requests"></a>启用请求

1. 如果希望访问包立即可供请求策略中的用户使用以进行请求，请将“启用”切换开关移动到“是”。

    创建完访问包后，将来始终可以启用该策略。

    如果选择了“无（仅限管理员直接分配）”，并且将“启用”设置为“否”，则管理员无法直接分配此访问包 。

    ![访问包 - 策略 - 启用策略设置](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. 单击“下一步”  。

## <a name="collect-additional-requestor-information-for-approval-preview"></a>收集其他请求程序信息以供审批 (预览) 

为了确保用户能够访问正确的访问包，你可以要求请求者在请求时应答自定义文本字段或多个选择问题。 每个策略限制为20个问题，多个选择问题的答案限制为25个。 然后，会向审批者显示问题，以帮助他们做出决定。

1. 请参阅 " **请求者信息** " 选项卡，并单击 " **问题** " 子选项卡。
 
1. 在 " **问题** " 框中键入要向请求者请求的内容，也称为 "显示字符串"。

    ![访问包-策略-启用请求程序信息设置](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. 如果将需要访问该访问包的用户社区没有共同的首选语言，则可以改善在 myaccess.microsoft.com 上请求访问权限的用户体验。 若要改善体验，可以为不同的语言提供备用的显示字符串。 例如，如果用户的 web 浏览器设置为西班牙语，并且已配置西班牙语显示字符串，则这些字符串将显示给请求用户。 若要为请求配置本地化，请单击 " **添加本地化**"。
    1. 进入 " **添加本地化 for 试题** " 窗格后，选择要在其中本地化问题的语言的 **语言代码** 。
    1. 在配置的语言中，在 " **本地化" 文本框** 中键入问题。
    1. 添加完所需的所有本地化后，单击 " **保存**"。

    ![访问包-策略-配置本地化文本](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. 选择要在其中回答请求方的 **答案格式** 。 答案格式包括： *短文本*、 *多选*和 *长文本*。
 
    ![访问包-策略-选择 "查看和编辑多个选择答案格式"](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. 如果选择多个选项，请单击 " **视图" 和 "编辑** " 按钮以配置答案选项。
    1. 选择 "查看和编辑" 后，将打开 " **查看/编辑问题** " 窗格。
    1. 键入 **回答 "答案值** " 框中的问题时要为请求者指定的响应选项。
    1. 键入所需的任意多个响应，并单击 " **保存**"。
    
    ![访问包-策略-输入多个选择选项](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. 若要要求请求者在请求访问访问包时回答此问题，请单击 " **必需**" 下的复选框。

1. 根据需要填写剩余的选项卡 (例如，生命周期) 。

在访问包策略中配置请求程序信息后，可以查看请求方对问题的响应。 有关查看请求方信息的指导，请参阅 [查看请求者的问题答案 (预览) ](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview)。

## <a name="next-steps"></a>后续步骤
- [更改访问包的生命周期设置](entitlement-management-access-package-lifecycle-policy.md)
- [查看访问包的请求](entitlement-management-access-package-requests.md)
