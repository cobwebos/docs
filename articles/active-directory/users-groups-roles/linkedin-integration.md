---
title: LinkedIn 帐户连接-Azure Active Directory 的管理员同意 |Microsoft Docs
description: 介绍如何启用或禁用 LinkedIn 集成帐户连接 Azure Active Directory 中的 Microsoft 应用中
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368112"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>同意为 Azure Active Directory 组织的 LinkedIn 帐户连接

您可以允许用户在你的组织能够访问其某些 Microsoft 应用中的 LinkedIn 连接。 直到用户同意连接其帐户不共享任何数据。 您可以将你的组织中 Azure Active Directory (Azure AD) 集成[管理中心](https://aad.portal.azure.com)。

> [!IMPORTANT]
> LinkedIn 帐户连接设置是目前正在向 Azure AD 组织推出。 当它推出到你的组织时，它是默认情况下启用。
> 
> 异常：
> * 此设置不适用于使用以下软件的客户：美国政府版 Microsoft 云、德国 Microsoft 云或者在中国通过 21Vianet 运营的 Azure 和 Office 365。
> * 对于在德国预配的租户，此设置默认为关闭。 请注意，此设置不适用于使用德国 Microsoft 云的客户。
> * 对于在法国预配的租户，此设置默认为关闭。
>
> 一旦你的组织启用了 LinkedIn 帐户连接，帐户连接工作后用户同意应用代表他们的公司数据访问。 有关用户同意的情况下设置的信息，请参阅[如何删除用户的访问权限的应用程序](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>使用 Azure 门户中启用 LinkedIn 帐户连接

可以启用想要具有访问权限，只有你的组织中的所选用户到整个组织中用户的 LinkedIn 帐户连接。

1. 登录到[Azure AD 管理中心](https://aad.portal.azure.com/)与 Azure AD 组织的全局管理员帐户。
2. 选择“用户”。
3. 在“用户”边栏选项卡中，选择“用户设置”。
4. 下**LinkedIn 帐户连接**，允许用户连接其帐户以访问其某些 Microsoft 应用中的 LinkedIn 连接。 直到用户同意连接其帐户不共享任何数据。

  * 选择**是**同意在组织中的所有用户的服务
  * 选择**选定**以同意使用组织中的所选用户
  * 选择**否**要撤销许可你的组织中的用户

    ![将集成在组织中的 LinkedIn 帐户连接](./media/linkedin-integration/linkedin-integration.png)

5. 完成后，选择**保存**以保存设置。
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用组策略启用 LinkedIn 帐户连接

1. 下载 [Office 2016 管理模板文件 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 提取 ADMX 文件然后将其复制到中央存储。
3. 打开组策略管理。
4. 使用以下设置创建一个组策略对象：“用户配置” > “管理模板” > “Microsoft Office 2016” > “杂项” > “在 Office 应用程序中显示 LinkedIn 的功能”。
5. 选择“已启用”或“已禁用”。
  
   状态 | 效果
   ------ | ------
   **已启用** | Office 2016“选项”中的“在 Office 应用程序中显示领英功能”设置已启用。 你的组织中的用户可以在其 Office 2016 应用程序中使用 LinkedIn 功能。
   **已禁用** | Office 2016“选项”中的“在 Office 应用程序中显示领英功能”设置已禁用，最终用户不能更改此设置。 组织中的用户无法在其 Office 2016 应用程序中使用 LinkedIn 功能。

此组策略只会影响本地计算机上的 Office 2016 应用。 如果用户在其 Office 2016 应用中禁用 LinkedIn，他们仍可以看到在 Office 365 中的 LinkedIn 功能。

## <a name="next-steps"></a>后续步骤

* [用户同意和 linkedin 共享的数据](linkedin-user-consent.md)

* [Microsoft 应用中的 LinkedIn 信息和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 帮助中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 门户中查看当前的领英集成设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
