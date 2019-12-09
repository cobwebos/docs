---
title: Azure AD 的标识预配是什么？ | Microsoft Docs
description: 提供标识预配的概述。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acde7f30649e03d44fd891c959b53113b0e46f51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794933"
---
# <a name="what-is-identity-provisioning"></a>什么是标识预配？

如今，企业和公司越来越成为本地应用程序和云应用程序的混合体。  用户需要同时访问位于本地和云中的应用程序。 需要在这些不同的应用程序（本地和云）中使用一种标识。

预配是根据特定的条件创建对象，保持对象的最新状态，并在不再满足这些条件时删除对象的过程。 例如，当某个新用户加入组织时，会在人力资源 (HR) 系统中输入该用户。  此时，预配可以在云、Active Directory 以及用户需要访问的不同应用程序中创建相应的用户帐户。  这样，用户便可以开始工作，且在第一天就有权访问所需的应用程序和系统。 

![云预配](media/what-is-provisioning/cloud1.png)

对于 Azure Active Directory，预配可划分为以下关键场景。  

- **[HR 驱动的预配](#hr-driven-provisioning)**  
- **[应用预配](#app-provisioning)**  
- **[目录预配](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>HR 驱动的预配

![云预配](media/what-is-provisioning/cloud2.png)

从 HR 到云的预配涉及到根据 HR 系统中的信息创建对象（用户、角色、组等）。  

最常见的场景是，当新员工加入公司时，将其输入到 HR 系统中。  完成该过程后，即已将此员工预配到云中。  在这种情况下，云为 Azure AD。  从 HR 进行预配可能涵盖以下场景。 

- **招聘新员工** - 将新员工添加到云 HR 后，Active Directory、Azure Active Directory、（可选）Office 365 和 Azure AD 支持的其他 SaaS 应用程序中会自动创建一个用户帐户，并将电子邮件地址写回到云 HR。
- **员工属性和个人资料更新** - 在云 HR 中更新员工记录（例如其姓名、职称或上司）后，Active Directory、Azure Active Directory、（可选）Office 365 和 Azure AD 支持的其他 SaaS 应用程序中会自动更新相应员工的用户帐户。
- **员工离职** - 当某个员工在云 HR 离职时，Active Directory、Azure Active Directory、（可选）Office 365 和 Azure AD 支持的其他 SaaS 应用程序中会自动禁用其用户帐户。
- **员工返聘** - 当云 HR 返聘某位员工时，该员工的旧帐户可自动重新激活或重新预配到 Active Directory、Azure Active Directory、Office 365 以及 Azure AD 支持的其他 SaaS 应用程序（其中，具体操作由你的偏好而定，且后两类应用可选配）。


## <a name="app-provisioning"></a>应用预配

![云预配](media/what-is-provisioning/cloud3.png)

应用预配涉及到在用户需要访问的应用程序中预配用户和角色。  

最常见的场景是，将 Azure AD 中的用户预配到 O365 或 Salesforce。

## <a name="directory-provisioning"></a>目录预配

![云预配](media/what-is-provisioning/cloud4.png)

本地预配涉及到从本地源（例如 Active Directory）预配到 Azure AD。  

最常见的场景是，将 Active Directory (AD) 中的用户预配到 Azure AD。

Azure AD Connect sync、Azure AD Connect 云预配和 Microsoft Identity Manager 已实现此功能。 
 
## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
