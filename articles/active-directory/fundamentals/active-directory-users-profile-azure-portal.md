---
title: 添加或更新用户的配置文件信息 - Azure Active Directory | Microsoft Docs
description: 有关如何在 Azure Active Directory 中向用户配置文件添加信息（包括图片和作业详细信息）的说明。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d710a86bb63765ea8a1a777818ca5f99e38d3a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60248105"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>使用 Azure Active Directory 添加或更新用户的配置文件信息
使用 Azure Active Directory (Azure AD) 来添加用户配置文件信息，包括配置文件图片、工作特定信息和某些设置。 有关添加新用户的详细信息，请参阅[如何添加或删除 Azure Active Directory 中的用户](add-users-azure-active-directory.md)。

## <a name="add-or-change-profile-information"></a>添加或更改配置文件信息
如你所见，用户配置文件中提供的信息比在创建用户时能够添加的信息多。 这些附加信息全都是可选信息，组织可以根据需要添加。

## <a name="to-add-or-change-profile-information"></a>添加或更改配置文件信息的步骤
1. 以组织的用户管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 依次选择“Azure Active Directory”、“用户”，然后选择一个用户   。 例如，Alain Charon  。

    Alain Charon - 配置文件页面随即出现  。

    ![用户的配置文件页面，其中包括可编辑信息](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. 选择“编辑”以选择性地添加或更新每个可用部分中包含的信息  。

    ![用户的配置文件页面，其中显示可编辑区域](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **配置文件图片。** 为用户帐户选择缩略图图像。 该图显示在 Azure Active Directory 和用户的个人页面上（如 myapps.microsoft.com 页面）。

    - **身份。** 添加或更新用户的其他标识值，例如结婚后的姓氏。 可以独立于姓氏和名称的值来设置此名称。 例如，可以使用它来包括首字母缩写、公司名称，或者更改所显示名称的顺序。 又比如，如果两个用户的姓名均为“Chris Green”，则可使用标识字符串将其姓名设置为“Chris B. Green”和“Chris R. Green (Contoso)”。

    - **工作信息。** 添加与工作相关的任何信息（如用户的职务、部门或经理）。

    - **设置。** 决定用户是否可以登录到 Azure Active Directory 租户。 此外可以指定用户的全局位置。

    - **联系信息。** 为用户添加任何相关的联系信息。 例如，街道地址或移动电话号码。

    - **身份验证联系人信息。** 验证此信息以确保用户的电话号码和电子邮件地址有效。 Azure Active Directory 使用此信息在登录期间确保用户为本人。 身份验证联系人信息只能由全局管理员更新。

4. 选择“保存”。 

    为用户保存所做的所有更改。

    >[!Note]
    >必须使用 Windows Server Active Directory 更新归属于 Windows Server Active Directory 的用户的身份、联系信息或工作信息。 完成更新后，必须等待下一个同步周期完成才能看到更改。

## <a name="next-steps"></a>后续步骤
更新用户的配置文件后，可以执行以下基本过程：

- [添加或删除用户](add-users-azure-active-directory.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

也可以执行其他用户管理任务，例如分配委托、使用策略以及共享用户帐户。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。
