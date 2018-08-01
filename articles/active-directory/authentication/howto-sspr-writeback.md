---
title: 如何为 Azure AD SSPR 配置密码写回
description: 使用 Azure AD 和 Azure AD Connect 将密码写回本地目录
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e613ff742096077fe1765d4b855b6c7d409cc228
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158941"
---
# <a name="how-to-configure-password-writeback"></a>操作说明：配置密码写回

使用密码写回时，我们建议使用 [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) 的自动更新功能。

若要执行以下步骤，需要已在环境中使用[快速](./../connect/active-directory-aadconnect-get-started-express.md)或[自定义](./../connect/active-directory-aadconnect-get-started-custom.md)设置配置了 Azure AD Connect。

1. 若要配置和启用密码写回服务，请登录 Azure AD Connect 服务器，并启动“Azure AD Connect”配置向导。
2. 在“欢迎”页上，选择“配置”。
3. 在“其他任务”页上，依次选择“自定义同步选项”和“下一步”。
4. 在“连接到 Azure AD”页上，输入全局管理员凭据，再选择“下一步”。
5. 在“连接目录”和“域/OU 筛选”页上，选择“下一步”。
6. 在“可选功能”页上，选中“密码写回”旁边的框，并选择“下一步”。
   ![在 Azure AD Connect 中启用密码写回][Writeback]
7. 在“已准备好进行配置”页上，选择“配置”，并等待进程完成。
8. 在配置完成后，选择“退出”。

有关与密码写回服务相关的疑难解答任务，请参阅疑难解答文章中的[排查密码写回问题](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)部分。

## <a name="active-directory-permissions"></a>Active Directory 权限

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
6. 在“应用到”下拉列表中，选择“子用户”对象。
7. 在“权限”下，选中下面各项的框：
    * **重置密码**
    * **更改密码**
    * **写入 lockoutTime**
    * **写入 pwdLastSet**
8. 选择“应用”/“确定”以应用更改，并退出所有打开的对话框。

## <a name="next-steps"></a>后续步骤

[什么是密码写回？](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中启用密码写回"
