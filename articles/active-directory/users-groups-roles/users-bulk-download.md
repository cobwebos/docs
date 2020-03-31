---
title: 在 Azure 活动目录门户中下载用户列表（预览 ） |微软文档
description: 在 Azure 活动目录中的 Azure 管理中心批量下载用户记录。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063826"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>在 Azure 活动目录门户中下载用户列表（预览）

Azure 活动目录 （Azure AD） 支持批量用户导入（创建）操作。

## <a name="required-permissions"></a>所需的权限

要从 Azure AD 管理中心下载用户列表，必须与在 Azure AD 中分配给一个或多个组织级管理员角色的用户登录。 来宾邀请者和应用程序开发人员不被视为管理员角色。

## <a name="to-download-a-list-of-users"></a>下载用户列表

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
2. 导航到 Azure 活动目录>用户。 然后，通过勾选每个用户旁边的左列中的框，选择要包含在下载中的用户。 注意：此时，无法选择要导出的所有用户。 必须单独选择每个选项。
3. 在 Azure AD 中，选择 **"用户** > **下载用户**"。
4. 在 **"下载用户"** 页上，选择 **"开始"** 以接收列出用户配置文件属性的 CSV 文件。 如果存在错误，则可以在"批量操作结果"页上下载并查看结果文件。 该文件包含每个错误的原因。

   ![选择要下载的用户列表的位置](./media/users-bulk-download/bulk-download.png)

   下载文件将包含筛选的用户列表。

   包括以下用户属性：

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
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
   - 身份验证电话号
   - 身份验证替代电话号码
   - 身份验证电子邮件
   - 备用电子邮件地址
   - ageGroup
   - 同意为次要提供
   - 法律年龄组分类

## <a name="check-status"></a>查看状态

您可以在**批量操作结果（预览）** 页中查看挂起的批量请求的状态。

   ![在"批量操作结果"页中检查上载状态](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>批量下载服务限制

创建用户列表的每个批量活动最多可运行一小时。 这支持创建和下载至少 500，000 个用户的列表。

## <a name="next-steps"></a>后续步骤

- [批量添加用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [批量还原用户](users-bulk-restore.md)
