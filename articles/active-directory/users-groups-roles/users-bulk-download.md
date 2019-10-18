---
title: 在 Azure Active Directory 门户中下载用户列表（预览） |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 管理中心中批量下载用户记录。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd0829afca05058892d3a0ceeb50c9955d792dc3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517056"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>在 Azure Active Directory 门户中下载用户列表（预览）

Azure Active Directory （Azure AD）支持批量用户导入（创建）操作。

## <a name="required-permissions"></a>所需的权限

若要从 Azure AD 管理中心下载用户列表，你必须使用分配给 Azure AD 中的一个或多个组织级管理员角色的用户登录。 来宾邀请者和应用程序开发人员不被视为管理员角色。

## <a name="to-download-a-list-of-users"></a>下载用户列表

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择 "**用户** > **下载用户**"。
1. 在 "**下载用户**" 页上，选择 "**启动**" 以接收列出用户配置文件属性的 CSV 文件。 如果有错误，则可以在 "批量操作结果" 页上下载并查看结果文件。 文件包含每个错误的原因。

   ![选择要将列表下载到的位置。](./media/users-bulk-download/bulk-download.png)

   下载文件将包含用户的筛选列表。

   将包括以下用户属性： 

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - manager
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>查看状态

你可以在 "**批量操作结果（预览版）** " 页中查看挂起的批量请求的状态。

   ![在大容量操作结果页中检查上传状态](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>大容量下载服务限制

创建用户列表的每个批量活动最多可运行一小时。 这允许创建和下载至少500000用户的列表。

## <a name="next-steps"></a>后续步骤

- [批量添加用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [大容量还原用户](users-bulk-restore.md)
