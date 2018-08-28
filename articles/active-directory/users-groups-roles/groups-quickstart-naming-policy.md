---
title: 为 Azure Active Directory 中的 Office 365 组添加命名策略 | Microsoft Docs
description: 介绍如何在 Azure Active Directory 中添加新用户或删除现有用户
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/08/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8ebdb22ba5ca04a5c811b3b368055f5f4371c75f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208867"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>快速入门：Azure Active Directory 中的组的命名策略

在本快速入门中，需在 Azure Active Directory (Azure AD) 租户中为用户创建的 Office 365 组设置命名策略，以便对租户的组进行排序和搜索。 例如，可以将命名策略用于：

* 传达组的功能、成员身份、地理区域或创建组的人员。
* 用于对通讯簿中的组分类。
* 阻止组名称和别名中使用特定字词。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="install-powershell-cmdlets"></a>安装 PowerShell cmdlet

请先确保卸载任意较早版本的适用于 Windows PowerShell 的 Azure Active Directory PowerShell for Graph 模块，并安装 [Azure Active Directory PowerShell for Graph - 公共预览版 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)，然后再运行 PowerShell 命令。 

1. 以管理员身份打开 Windows PowerShell 应用。
2. 卸载任何以前版本的 AzureADPreview。
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. 安装最新版本的 AzureADPreview。
  
  ````
  Install-Module AzureADPreview
  ````
如果系统提示访问的是不受信任的存储库，请键入 Y。安装新模块可能需要几分钟。

## <a name="set-up-naming-policy"></a>设置命名策略

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>步骤 1：使用 PowerShell cmdlet 登录

1. 打开 Windows PowerShell 应用。 不需要提升的权限。

2. 运行以下命令，准备运行 cmdlet。
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  在打开的“登录到你的帐户”屏幕上，输入管理员帐户和密码以连接到服务，然后选择“登录”。

3. 按照[用于配置组设置的 Azure Active Directory cmdlet](groups-settings-cmdlets.md) 中的步骤创建此租户的组设置。

### <a name="step-2-view-the-current-settings"></a>步骤 2：查看当前设置

1. 查看当前的命名策略设置。
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. 显示当前组设置。
  
  ````
  $Setting.Values
  ````
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>步骤 3：设置命名策略和任何自定义阻止字词

1. 在 Azure AD PowerShell 中设置组名前缀和后缀。
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. 设置要限制的自定义阻止字词。 下面的示例演示如何添加自己的自定义字词。
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. 保存设置，使新策略生效，如在下面的示例所示。
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
就这么简单。 现已设置了命名策略，并添加了自定义阻止字词。

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure AD PowerShell 中设置组名前缀和后缀。
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =""
  ````
  
2. 设置要限制的自定义阻止字词。 下面的示例演示如何添加自己的自定义字词。
  
  ````
  $Setting["CustomBlockedWordsList"]=""
  ````
  
3. 保存设置，使新策略生效，如在下面的示例所示。
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 PowerShell cmdlet 设置 Azure AD 租户的命名策略。

若要详细了解技术限制、如何添加自定义阻止单词的列表或跨 Office 365 应用的最终用户体验，请参阅下一文章，获取命名策略细节。
> [!div class="nextstepaction"]
> [命名策略全部详细信息](groups-naming-policy.md)