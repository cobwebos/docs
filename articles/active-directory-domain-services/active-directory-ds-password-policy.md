---
title: Azure Active Directory 域服务：密码策略 | Microsoft Docs
description: 了解托管域中的密码策略
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: ergreenl
ms.openlocfilehash: 5fc50aeeb2764f90a111c2466ff5a9deefe1e65c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167982"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>托管域中的密码和帐户锁定策略
本文介绍托管域中的默认密码策略。 此外，还介绍如何配置这些策略。

## <a name="fine-grained-password-policies-fgpp"></a>精细密码策略 (FGPP)
使用精细密码策略可在单个域中指定多个密码策略。 FGPP 允许将密码和帐户锁定策略的不同限制应用到域中不同的用户集。 例如，可对特权帐户应用严格的密码设置。

可以使用 FGPP 配置以下密码设置：
* 最短密码长度
* 密码历史记录
* 密码必须符合复杂性要求
* 最短密码期限
* 最长密码期限
* 帐户锁定策略
    * 帐户锁定持续时间
    * 允许的失败登录尝试次数
    * 多长时间后重置失败登录尝试计数


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>托管域中的默认精细密码策略设置
以下屏幕截图演示了在某个 Azure AD 域服务托管域中配置的默认精细密码策略。

![默认的精细密码策略](./media/how-to/default-fgpp.png)

> [!NOTE]
> 不能修改或删除默认的内置精细密码策略。 “AAD DC 管理员”组的成员可以创建自定义的 FGPP，并将其配置为覆盖（优先于）默认的内置 FGPP。
>
>

## <a name="password-policy-settings"></a>密码策略设置
在托管域中，默认会配置以下密码策略：
* 最短密码长度（字符数）：7
* 最长密码期限（生存期）：90 天
* 密码必须符合复杂性要求

### <a name="account-lockout-settings"></a>帐户锁定设置
在托管域中，默认会配置以下帐户锁定策略：
* 帐户锁定持续时间：30
* 允许的失败登录尝试次数：5
* 多长时间后重置失败登录尝试计数：30 分钟

如果在 2 分钟内使用了 5 个无效密码，则会将用户帐户有效锁定 30 分钟。 帐户将在 30 分钟后自动解锁。


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>在托管域中创建自定义的精细密码策略 (FGPP)
可以在托管域中创建自定义的 FGPP 并将其应用到特定的组。 此配置将有效地覆盖针对托管域配置的默认 FGPP。

> [!TIP]
> 只有“AAD DC 管理员”组的成员有权创建自定义的精细密码策略。
>
>

此外，还可以创建自定义的精细密码策略，并将其应用到在托管域中创建的任何自定义 OU。

可出于以下原因配置自定义的 FGPP：
* 设置不同的帐户锁定策略。
* 为托管域配置默认的密码生存期设置。

在托管域中创建自定义的 FGPP：
1. 登录到用于管理该托管域的 Windows VM。 如果没有该 VM，请遵照[管理托管域](active-directory-ds-admin-guide-administer-domain.md)中的说明操作
2. 在该 VM 上启动“Active Directory 管理中心”。
3. 单击域名（例如“contoso100.com”）。
4. 双击“系统”打开“系统容器”。
5. 双击“密码设置容器”。
6. 此时会看到托管域的名为 **AADDSSTFPSO** 的默认内置 FGPP。 不能修改此内置 FGPP。 但是，可以创建新的自定义 FGPP 来覆盖默认 FGPP。
7. 在右侧的“任务”面板中，依次单击“新建”、“密码设置”。
8. 在“创建密码设置”对话框中，指定要在自定义 FGPP 中应用的自定义密码设置。 请记得正确设置优先顺序，以覆盖默认 FGPP。

  ![创建自定义 FGPP](./media/how-to/custom-fgpp.png)

  > [!TIP]
  > **请记得取消选中“防止意外删除”选项。** 如果选中此选项，则无法保存 FGPP。
  >
  >

9. 在“直接应用到”中，单击“添加”按钮。 在“选择用户或组”对话框中，单击“位置”按钮。

  ![选择“用户和组”](./media/how-to/fgpp-applies-to.png)

10. 在“位置”对话框中，展开域名并单击“AADDC 用户”。 现在，可以从内置用户 OU 中选择要对其应用 FGPP 的组。

  ![选择该组所属的 OU](./media/how-to/fgpp-container.png)

11. 键入该组的名称，然后单击“检查名称”按钮以验证该组是否存在。

  ![选择要应用 FGPP 的组](./media/how-to/fgpp-apply-group.png)

12. 该组的名称将显示在“直接应用到”部分。 单击“确定”按钮保存这些更改。

  ![已应用 FGPP](./media/how-to/fgpp-applied.png)

> [!TIP]
> **对自定义 OU 中的用户帐户应用自定义密码策略：** 精细密码策略只能应用到组。 若要仅对自定义 OU 中的用户配置自定义密码策略，请创建一个包含该 OU 中用户的组。
>
>

## <a name="next-steps"></a>后续步骤
* [了解 Active Directory 精细密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置精细密码策略](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
