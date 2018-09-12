---
title: 管理 Azure Stack 中每个用户对资源的权限（服务管理员和租户）| Microsoft Docs
description: 作为服务管理员或租户，了解如何管理 RBAC 权限。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: ab61e1f892f46ad36df741b7a72afcfcbaa0ed87
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2018
ms.locfileid: "44376929"
---
# <a name="manage-role-based-access-control"></a>管理基于角色的访问控制

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 中的用户可以是订阅、资源组或服务的每个实例的读者、所有者或参与者。 例如，用户 A 可能对订阅 1 具有读者权限，但对虚拟机 7 则具有所有者权限。

 - 读者：用户可查看所有内容，但不能进行任何更改。
 - 参与者：用户可管理除对资源的访问权限以外的所有内容。
 - 所有者：用户可管理所有内容，包括对资源的访问权限。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在“资源”边栏选项卡中，单击“访问”图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在“用户”边栏选项卡中，单击“角色”。
4. 在“角色”边栏选项卡中，单击“添加”即可添加用户的权限。

## <a name="set-access-permissions-for-a-universal-group"></a>设置通用组的访问权限 

> [!Note]  
仅适用于 Active Directory 联合身份验证服务 (AD FS)。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在“资源”边栏选项卡中，单击“访问”图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在“用户”边栏选项卡中，单击“角色”。
4. 在“角色”边栏选项卡中，单击“添加”即可添加通用组 Active Directory 组的权限。

## <a name="next-steps"></a>后续步骤
[添加 Azure Stack 租户](azure-stack-add-new-user-aad.md)

