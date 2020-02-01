---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: ba1bdd60a3363cfab694bae9b8ee3cf63e24b054
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907573"
---
## <a name="steps-to-enable-single-sign-on"></a>启用单一登录的步骤
云设置适用于单一登录。  在安装代理时，当前没有启用 SSO 的选项，但你可以使用以下步骤启用 SSO 并使用它。 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>步骤1：下载并提取 Azure AD Connect 文件
1.  首先，下载最新版本的[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  使用管理权限打开命令提示符，并导航到刚刚下载的 msi。
3.  运行以下内容： `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. 更改 filepath 和 extractfolder，使其与你的文件路径和提取文件夹的名称相匹配。  内容现在应在提取文件夹中。

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>步骤2：导入无缝 SSO PowerShell 模块

1. 下载并安装[Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
2. 浏览到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>步骤3：获取已在其中启用了无缝 SSO 的 Active Directory 林的列表

1. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 出现提示时，输入租户的全局管理员凭据。
2. 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 Active Directory 林列表（请查看“域”列表）。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>步骤4：为每个 Active Directory 林启用无缝 SSO

1. 调用 `Enable-AzureADSSOForest`。 出现提示时，输入目标 Active Directory 林的域管理员凭据。

   > [!NOTE]
   >必须以 SAM 帐户名称格式（contoso\johndoe 或 com\johndoe）输入域管理员凭据用户名。 我们使用用户名的域部分通过 DNS 查找域管理员的域控制器。

   >[!NOTE]
   >使用的域管理员帐户不得是受保护用户组的成员。 如果是这样，则操作将失败。

2. 为你要在其中设置该功能的每个 Active Directory 林重复上述步骤。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>步骤 5。 在租户上启用此功能

若要在租户上启用此功能，请调用 `Enable-AzureADSSO -Enable $true`。
