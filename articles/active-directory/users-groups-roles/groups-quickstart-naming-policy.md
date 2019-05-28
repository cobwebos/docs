---
title: 为 Office 365 组添加命名策略快速入门 - Azure Active Directory | Microsoft Docs
description: 介绍如何在 Azure Active Directory 中添加新用户或删除现有用户
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e600204479bc54a590df6bf1bbcd634eaac7fc
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605636"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>快速入门：为 Azure Active Directory 中的组命名策略

在本快速入门中，需在 Azure Active Directory (Azure AD) 租户中为用户创建的 Office 365 组设置命名策略，以便对租户的组进行排序和搜索。 例如，可以将命名策略用于：

* 传达组的功能、成员身份、地理区域或创建组的人员。
* 有助于对通讯簿中的组进行分类。
* 阻止组名和别名中使用特定字词。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal-preview"></a>使用 Azure 门户为租户配置组命名策略（预览）

1. 使用用户管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“组”，然后选择“命名策略”，以便打开“命名策略”页。  

    ![在管理中心打开“命名策略”页](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>查看或编辑前后缀命名策略

1. 在“命名策略”页上，选择“组命名策略”。  
1. 可以单独查看或编辑当前的前缀或后缀命名策略，只需选择需要在命名策略中强制实施的属性或字符串即可。
1. 若要从列表中删除某个前缀或后缀，请选择该前缀或后缀，然后选择“删除”。  可以同时删除多个项。
1. 选择“保存”，以便对策略所做的更改生效。 

### <a name="view-or-edit-the-custom-blocked-words"></a>查看或编辑自定义阻止字词

1. 在“命名策略”页上，选择“阻止字词”。  

    ![编辑并上传命名策略的阻止字词列表](./media/groups-naming-policy/blockedwords-preview.png)

1. 查看或编辑自定义阻止字词的当前列表，方法是选择“下载”。 
1. 上传自定义阻止字词的新列表，方法是选择文件图标。
1. 选择“保存”，以便对策略所做的更改生效。 

就这么简单。 现已设置了命名策略，并添加了自定义阻止字词。

## <a name="clean-up-resources"></a>清理资源

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>使用 Azure 门户删除命名策略（预览）

1. 在“命名策略”页上，选择“删除策略”。  
1. 确认删除之后，将会删除命名策略，包括所有前缀-后缀命名策略和任何自定义阻止字词。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何通过 Azure 门户设置 Azure AD 组织的命名策略。

请转到下一篇文章以获取详细信息，包括用于命名策略的 PowerShell cmdlet、技术限制、添加自定义阻止字词的列表，以及跨 Office 365 应用的最终用户体验。
> [!div class="nextstepaction"]
> [命名策略 PowerShell](groups-naming-policy.md)