---
title: 在 Azure Active Directory 中添加新的 Azure Stack 租户帐户 | Microsoft Docs
description: 部署 Microsoft Azure Stack 开发工具包之后, 将需要创建至少一个租户用户帐户，以便浏览租户门户。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: c0f457955bd8df7fe07e2a96469fde0a751417bc
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240313"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>在 Azure Active Directory 中添加新的 Azure Stack 租户帐户

[部署 Azure Stack 开发工具包](azure-stack-run-powershell-script.md)后，需要租户用户帐户，以便浏览租户门户并测试套餐和计划。 可以[使用 Azure 门户](#create-an-azure-stack-tenant-account-using-the-azure-portal)或[使用 PowerShell](#create-an-azure-stack-tenant-account-using-powershell) 创建租户帐户。

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>使用 Azure 门户创建 Azure Stack 租户帐户

必须具有 Azure 订阅才能使用 Azure 门户。

1. 登录到 [Azure](https://portal.azure.com)。
2. 在左侧导航栏中，选择“Active Directory”并切换到要用于 Azure Stack 的目录，或者创建一个新目录。
3. 选择“Azure Active Directory” > “用户” > “新建用户”。

    ![用户 - 突出显示新用户的所有用户页面](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. 在“用户”页中，填写必填信息。

    ![添加新用户，具有用户信息的用户页](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **名称（必需）。** 新用户的姓氏和名字。 例如，Mary Parker。
    - **用户名（必需）。** 新用户的用户名。 例如，mary@contoso.com。
        用户名的域名部分必须是初始默认域名“<yourdomain name>.onmicrosoft.com”，或自定义域名（例如“contoso.com”）。 若要了解有关如何创建自定义域名的详细信息，请参阅[如何将自定义域名添加到 Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md)。
    - **配置文件。** （可选）可添加有关用户的详细信息。 也可稍后添加用户信息。 有关添加用户信息的详细信息，请参阅[如何添加或更改用户配置文件信息](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。
    - **目录角色。**  选择“用户”。

5. 选中“显示密码”并复制“密码”框中提供的自动生成的密码。 在初始登录过程中需要此密码。

6. 选择“创建”。

    用户已创建并添加到 Azure AD 租户。

7. 登录到 Microsoft Azure 门户中使用新的帐户。 出现提示时更改密码。
8. 使用新帐户登录到 `https://portal.local.azurestack.external`，以查看租户门户。

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>使用 PowerShell 创建 Azure Stack 租户帐户

如果没有 Azure 订阅，则无法使用 Azure 门户添加租户用户帐户。 在这种情况下，可以改用适用于 Windows PowerShell 的 Azure Active Directory 模块。

> [!NOTE]
> 如果使用 Microsoft 帐户 (Live ID) 部署 Azure Stack 开发工具包，则无法使用 AAD PowerShell 创建租户帐户。 
> 
> 

1. 安装[适用于 IT 专业人员 RTW 的 Microsoft Online Services 登录助手](https://www.microsoft.com/en-us/download/details.aspx?id=41950)。
2. 安装[适用于 Windows PowerShell 的 Azure Active Directory 模块（64 位版本）](https://go.microsoft.com/fwlink/p/?linkid=236297)并将其打开。
3. 运行以下 cmdlet：

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 使用新的帐户登录到 Microsoft Azure。 出现提示时更改密码。
2. 使用新帐户登录到 `https://portal.local.azurestack.external`，以查看租户门户。

