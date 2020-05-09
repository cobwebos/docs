---
title: 启用 Azure Active Directory 密码写回
description: 本教程介绍如何使用 Azure AD Connect 启用 Azure AD 自助式密码重置写回，以将更改同步回到本地 Active Directory 域服务环境。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4815187e829cff56893988874e4dcac3b8985e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143754"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>教程：启用到本地环境的 Azure Active Directory 自助式密码重置写回

借助 Azure Active Directory (Azure AD) 自助式密码重置 (SSPR)，用户可以使用 Web 浏览器更新其密码或解锁其帐户。 在 Azure AD 连接到本地 Active Directory 域服务 (AD DS) 环境的混合环境中，此方案可能会导致两个目录的密码不同。

可以使用密码写回将 Azure AD 中的密码更改同步回到本地 AD DS 环境。 Azure AD Connect 提供一种安全机制用于将这些密码更改从 Azure AD 发回到现有本地目录。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为密码写回配置所需的权限
> * 在 Azure AD Connect 中启用密码写回选项
> * 在 Azure AD SSPR 中启用密码写回

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个至少启用了试用版许可证的有效 Azure AD 租户。
    * 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
    * 有关详细信息，请参阅 [Azure AD SSPR 的许可要求](concept-sspr-licensing.md)。
* 一个拥有全局管理员特权的帐户。 
* 为自助式密码重置配置的 Azure AD。
    * 如果需要，请[完成上一篇教程来启用 Azure AD SSPR](tutorial-enable-sspr.md)。
* 配置有最新 Azure AD Connect 版本的现有本地 AD DS 环境。
    * 如果需要，请使用“[快速](../hybrid/how-to-connect-install-express.md)”或“[自定义](../hybrid/how-to-connect-install-custom.md)”设置配置 Azure AD Connect。
    * 若要使用密码写回，域控制器必须是 Windows Server 2008 R2 或更高版本。

## <a name="configure-account-permissions-for-azure-ad-connect"></a>为 Azure AD Connect 配置帐户权限

Azure AD Connect 可用于在本地 AD DS 环境与 Azure AD 之间同步用户、组和凭据。 我们通常会在已加入本地 AD DS 域的 Windows Server 2012 或更高版本的计算机上安装 Azure AD Connect。

若要正常使用 SSPR 写回，必须为 Azure AD Connect 中指定的帐户设置适当的权限和选项。 如果不确定当前使用的是哪个帐户，请打开 Azure AD Connect 并选择“查看当前配置”选项。  需要向其添加权限的帐户列在“已同步的目录”下。  必须为该帐户设置以下权限和选项：

* **重置密码**
* 对 `lockoutTime` 的写入权限 
* 对 `pwdLastSet` 的写入权限 
*  “不过期密码”的扩展权限，针对于：
   * 相应林中各个域  的根对象
   * 要纳入 SSPR 范围的用户组织单位 (OU)

如果不分配这些权限，写回功能看上去已正确配置，但用户在从云管理其本地密码时会遇到错误。 必须对此对象和所有后代对象  应用权限，才会显示“使密码不过期”。  

若要设置密码写回服务正常运行所需的相应权限，请完成以下步骤：

1. 在本地 AD DS 环境中，使用拥有相应域管理员权限的帐户打开“Active Directory 用户和计算机”。  
1. 在“视图”菜单中，确保“高级功能”已启用。  
1. 在左侧面板中，右键单击表示域根的对象，并选择“属性” > “安全性” > “高级”。   
1. 在“权限”  选项卡中，选择“添加”  。
1. 对于“主体”，请选择权限应该应用到的帐户（Azure AD Connect 使用的帐户）。 
1. 在“应用到”  下拉列表中，选择“后代用户对象”  。
1. 在“权限”  下，选中以下选项所对应的框：
    * **重置密码**
1. 在“属性”下，选中以下选项对应的框。  需要滚动列表才能看到这些选项，默认情况下可能已设置这些选项：
    * **写入 lockoutTime**
    * **写入 pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. 准备就绪时，选择“应用”/“确定”以应用更改，并退出所有打开的对话框。 

更新权限时，将这些权限复制到目录中的所有对象可能需要一小时或更长时间才能完成。

本地 AD DS 环境中的密码策略可能会导致无法正确处理密码重置。 要使密码写回最有效地工作，必须将“最短密码期限”的组策略设置为 0。  可在 `gpedit.msc` 中的“计算机配置”>“策略”>“Windows 设置”>“安全设置”>“帐户策略”下找到此设置。 

如果更新组策略，请等待更新的策略复制完成，或使用 `gpupdate /force` 命令。

> [!Note]
> 要使密码立即更改，必须将密码写回设置为 0。 但是，如果用户遵守本地策略，并且将“最短密码期限”  设置为大于零的值，则在评估本地策略后，密码写回仍将工作。 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>在 Azure AD Connect 中启用密码写回

Azure AD Connect 中有一个配置选项用于密码写回。 启用此选项后，密码更改事件会导致 Azure AD Connect 将更新的凭据同步回到本地 AD DS 环境。

若要启用自助式密码重置写回，请先在 Azure AD Connect 中启用写回选项。 在 Azure AD Connect 服务器中完成以下步骤：

1. 登录到 Azure AD Connect 服务器，并启动“Azure AD Connect”配置向导。 
1. 在“欢迎”  页上，选择“配置”  。
1. 在“其他任务”  页上，依次选择“自定义同步选项”  和“下一步”  。
1. 在“连接到 Azure AD”页上，输入 Azure 租户的全局管理员凭据，然后选择“下一步”。  
1. 在“连接目录”  和“域/组织单位”  筛选页上，选择“下一步”  。
1. 在“可选功能”  页上，选中“密码写回”  旁边的框，并选择“下一步”  。

    ![为密码写回配置 Azure AD Connect](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. 在“已准备好进行配置”  页上，选择“配置”  ，并等待进程完成。
1. 在配置完成后，选择“退出”  。

## <a name="enable-password-writeback-for-sspr"></a>为 SSPR 启用密码写回

在 Azure AD Connect 中启用密码写回后，接下来请为写回配置 Azure AD SSPR。 启用 SSPR 来使用密码写回时，更改或重置其密码的用户的已更新密码也会同步回到本地 AD DS 环境。

若要在 SSPR 中启用密码写回，请完成以下步骤：

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，选择“密码重置”，然后选择“本地集成”。   
1. 将“将密码写回到本地目录?”选项设置为“是”。  
1. 将“允许用户在不重置密码的情况下解锁帐户?”选项设置为“是”。  

    ![为密码写回启用 Azure AD 自助式密码重置](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. 准备就绪后，选择“保存”。 

## <a name="clean-up-resources"></a>清理资源

如果你不再想要使用本教程中配置的 SSPR 写回功能，请完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，选择“密码重置”，然后选择“本地集成”。   
1. 将“将密码写回到本地目录?”选项设置为“否”。  
1. 将“允许用户在不重置密码的情况下解锁帐户?”选项设置为“否”。  

如果你不再想要使用任何密码功能，请在 Azure AD Connect 服务器中完成以下步骤：

1. 登录到 Azure AD Connect 服务器，并启动“Azure AD Connect”配置向导。 
1. 在“欢迎”  页上，选择“配置”  。
1. 在“其他任务”  页上，依次选择“自定义同步选项”  和“下一步”  。
1. 在“连接到 Azure AD”页上，输入 Azure 租户的全局管理员凭据，然后选择“下一步”。  
1. 在“连接目录”  和“域/组织单位”  筛选页上，选择“下一步”  。
1. 在“可选功能”页上，取消选中“密码写回”旁边的框，然后选择“下一步”。   
1. 在“已准备好进行配置”  页上，选择“配置”  ，并等待进程完成。
1. 在配置完成后，选择“退出”  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用到本地 AD DS 环境的 Azure AD SSPR 写回。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 为密码写回配置所需的权限
> * 在 Azure AD Connect 中启用密码写回选项
> * 在 Azure AD SSPR 中启用密码写回

> [!div class="nextstepaction"]
> [在登录时评估风险](tutorial-risk-based-sspr-mfa.md)
