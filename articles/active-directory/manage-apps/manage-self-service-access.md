---
title: 如何配置自助应用程序分配 | Microsoft Docs
description: 启用自助服务应用程序访问以允许用户查找自己的应用程序
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409110"
---
# <a name="how-to-configure-self-service-application-assignment"></a>如何配置自助应用程序分配

在用户可以从其"我的应用"访问面板中自行发现应用程序之前，您需要启用**自助服务应用程序**对您希望允许用户自行发现和请求访问的任何应用程序的访问权限。 此功能可用于从[Azure AD 库](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)[、Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)添加或通过[用户或管理员同意](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)添加的应用程序。 

对于 IT 组，此功能是节省时间和金钱的绝佳方式，我们强烈建议将其作为 Azure Active Directory 的新式应用程序部署的一部分。

使用此功能，可以：

-   允许用户从["我的应用"访问面板](https://myapps.microsoft.com/)中自行发现应用程序，而无需打扰 IT 组。

-   将这些用户添加到预配置组，以便查看请求访问的用户、删除访问权限以及管理分配给用户的角色。

-   （可选）允许业务审批人批准应用程序访问请求，从而为 IT 组省去麻烦。

-   （可选）最多配置 10 个可以批准访问此应用程序的人员。

-   （可选）允许业务审批者直接从其[应用程序访问面板](https://myapps.microsoft.com/)设置用户可用于登录应用程序的密码。

-   （可选）自动将自助服务分配的用户直接分配到应用程序角色。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>启用自助服务应用程序访问以允许用户查找自己的应用程序

自助服务应用程序访问是允许用户自行发现应用程序以及允许业务组批准对这些应用程序的访问的好方法。 对于应用程序上的密码单一签名，您还可以允许业务组管理从他们自己的"我的应用"访问面板分配给这些用户的凭据。

若要启用应用程序的自助应用程序访问，请执行以下步骤：

1. 以全局管理员身份登录到[Azure 门户](https://portal.azure.com)。

2. 选择**Azure 活动目录**。 在左侧导航菜单中，选择**企业应用程序**。

3. 从列表中选择应用程序。 如果看不到应用程序，请开始在搜索框中键入其名称。 或者使用筛选器控件选择应用程序类型、状态或可见性，然后选择 **"应用**"。

4. 在左侧导航菜单中，选择**自助服务**。

5. 要对此应用程序启用自助应用程序访问，请将“允许用户请求对此应用程序的访问权限?”**** 切换到“是”****。

6. 旁边**应向哪个组分配用户？** **Select group** 选择组，然后单击 **"选择**"。 当用户的请求获得批准时，他们将被添加到此组。 查看此组的成员身份时，您将能够看到谁已被授予通过自助服务访问访问应用程序。
  
    > [!NOTE]
    > 此设置不支持从本地同步的组。

7. **可选：** 要在允许用户访问之前需要业务批准，请先在**授予对此应用程序访问权限之前设置"需要审批"？** 切换为 **"是**"。

8. **可选：对于仅使用密码单一符号的应用程序，** 允许业务审批者为已批准的用户指定发送到此应用程序的密码，请设置 **"允许审批者"以为此应用程序设置用户的密码？** **Yes**

9. **可选：** 要指定允许批准访问此应用程序的业务审批人，请在**允许谁批准访问此应用程序旁边，** 单击 **"选择审批者**"，然后选择最多 10 个单独的业务审批者。 然后单击“选择”****。

    >[!NOTE]
    >不支持组。 您最多可以选择 10 个单独的业务审批人。 如果指定多个审批人，则任何单个审批人都可以批准访问请求。

10. **可选：****对于公开角色的应用程序**，要将自助服务批准的用户分配给角色，**在此应用程序中应分配用户到哪个角色旁边？，** 单击 **"选择角色**"，然后选择这些用户应为其分配的角色。 然后单击“选择”****。

11. 单击窗格顶部的“保存”**** 按钮以完成操作。

完成自助服务应用程序配置后，用户可以导航到其["我的应用"访问面板](https://myapps.microsoft.com/)，然后单击"**添加自助服务应用"** 按钮，以查找通过自助服务访问启用的应用。 业务审批者还会在其["我的应用"访问面板](https://myapps.microsoft.com/)中看到通知。 可以启用电子邮件，在用户请求需要审批人批准的应用程序的访问权限时，向审批人发送电子邮件通知。

## <a name="next-steps"></a>后续步骤
[为自助组管理设置 Azure Active Directory](../users-groups-roles/groups-self-service-management.md)
