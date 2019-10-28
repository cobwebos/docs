---
title: 如何为 Azure AD SSPR 配置密码写回-Azure Active Directory
description: 使用 Azure AD 和 Azure AD Connect 将密码写回本地目录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71a16ad3c571086a73a2aae192fb2d00bce4d5f9
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808150"
---
# <a name="how-to-configure-password-writeback"></a>操作说明：配置密码写回

若要执行以下步骤，需要已在环境中使用[快速](../hybrid/how-to-connect-install-express.md)或[自定义](../hybrid/how-to-connect-install-custom.md)设置配置了 Azure AD Connect。

1. 若要配置和启用密码写回服务，请登录 Azure AD Connect 服务器，并启动“Azure AD Connect”配置向导。
2. 在“欢迎”页上，选择“配置”。
3. 在“其他任务”页上，依次选择“自定义同步选项”和“下一步”。
4. 在“连接到 Azure AD”页上，输入全局管理员凭据，再选择“下一步”。
5. 在“连接目录”和“域/组织单位”筛选页上，选择“下一步”。
6. 在“可选功能”页上，选中“密码写回”旁边的框，并选择“下一步”。
   ![在 Azure AD Connect 中启用密码写回][Writeback]
7. 在“已准备好进行配置”页上，选择“配置”，并等待进程完成。
8. 在配置完成后，选择“退出”。

有关与密码写回服务相关的疑难解答任务，请参阅疑难解答文章中的[排查密码写回问题](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)部分。

> [!WARNING]
> 当 [Azure 访问控制服务 (ACS) 于 2018 年 11 月 7 日停用](../develop/active-directory-acs-migration.md)时，密码写回将停止为使用 Azure AD Connect 版本 1.0.8641.0 及更早版本的客户工作。 届时，Azure AD Connect 版本 1.0.8641.0 及更早版本将不再允许进行密码写回，因为它们依赖于 ACS 来实现该功能。
>
> 若要避免服务中断，请从以前版本的 Azure AD Connect 升级到较新版本，请参阅文章 [Azure AD Connect：从旧版升级到最新版本](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>密码写回的许可要求

**通过本地写回实现自助密码重置/更改/解锁是 Azure AD 的一项高级功能**。 有关许可的详细信息，请参阅 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)。

若要使用密码写回，必须在租户中分配以下许可证之一：

* Azure AD Premium P1
* Azure AD Premium P2
* 企业移动性 + 安全性 E3 或 A3
* 企业移动性 + 安全性 E5 或 A5
* Microsoft 365 E3 或 A3
* Microsoft 365 E5 或 A5
* Microsoft 365 F1
* Microsoft 365 商业版

> [!WARNING]
> 独立 Office 365 许可计划不支持“通过本地写回实现自助密码重置/更改/解锁”，要使此功能正常工作，需要使用上述计划之一。
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory 权限和本地密码复杂性策略 

若要纳入 SSPR 范围，在 Azure AD Connect 实用工具中指定的帐户必须设置以下各项：

* **重置密码** 
* **更改密码** 
* 对 `lockoutTime` 的写入权限
* 对 `pwdLastSet` 的写入权限
* 对以下任一项的扩展权限：
   * 相应林中各个域的根对象
   * 要纳入 SSPR 范围的用户组织单位 (OU)

如果不确定上述帐户指的是哪个帐户，请打开 Azure Active Directory Connect 配置 UI，并选择“查看当前配置”选项。 需要向其授予权限的帐户列在“已同步的目录”下。

设置这些权限后，每个林的 MA 服务帐户都可以代表相应林中的用户帐户管理密码。 

> [!IMPORTANT]
> 如果疏忽了分配这些权限，即使写回服务似乎已正确配置，但用户仍会在尝试在云中管理本地密码时看到错误。
>

> [!NOTE]
> 将这些权限复制到目录中的所有对象可能需要一小时或更长时间才能完成。
>

若要设置密码写回服务正常运行所需的相应权限，请完成以下步骤：

1. 使用具有适当域管理权限的帐户，打开“Active Directory 用户和计算机”。
2. 在“视图”菜单中，确保“高级功能”处于启用状态。
3. 在左侧面板中，右键单击表示域根的对象，并依次选择“属性” > “安全性” > “高级”。
4. 在“权限”选项卡中，选择“添加”。
5. 选取要将权限应用到的帐户（通过 Azure AD Connect 安装程序）。
6. 在“应用到”下拉列表中，选择“后代用户对象”。
7. 在“权限”下，选中以下选项所对应的框：
    * **更改密码**
    * **重置密码**
8. 在“属性”下，选中以下选项所对应的框：
    * **写入 lockoutTime**
    * **写入 pwdLastSet**
9. 选择“应用”/“确定”以应用更改，并退出所有打开的对话框。

由于授权来源位于本地，因此，密码复杂性策略将应用于相同的连接数据源。 请确保已更改 "最小密码长度" 的现有组策略。 不应将 "组策略" 设置为 "1"，这意味着密码应至少为一天，然后才能更新。 需要确保将其设置为0。 这些设置可在 "计算机配置" > 策略 "下的 `gpmc.msc` 中找到， **> Windows 设置" > 安全设置 "> 帐户策略**。 运行 `gpupdate /force` 以确保更改生效。 

## <a name="next-steps"></a>后续步骤

[什么是密码写回？](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中启用密码写回"
