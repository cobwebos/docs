---
title: "在 Azure AD 中配置 LinkedIn 集成 | Microsoft Docs"
description: "说明如何为 Azure Active Directory 中的 Microsoft 应用启用或禁用 LinkedIn 集成。"
services: active-directory
author: jeffgilb
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 15c161542d6826e6aeb5f708a0d9c3fa1f1885e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>在 Azure Active Directory 中启用 LinkedIn 集成
启用 LinkedIn 集成，可让用户从 Microsoft 应用中访问公共 LinkedIn 数据及其个人 LinkedIn 网络（如果用户选择）。 每个用户可以独立地选择将其工作帐户连接到其 LinkedIn 帐户。

### <a name="linkedin-integration-from-your-end-users-perspective"></a>从最终用户的角度来看 LinkedIn 集成
当组织中的最终用户将其 LinkedIn 帐户连接到其工作帐户时，他们可以更好地识别组织内外与其协作的人员。 连接两个帐户允许在组织的 Microsoft 应用中使用用户的 LinkedIn 连接和配置文件数据，但你始终可以通过删除 LinkedIn 共享该数据的权限来选择退出。 该集成还使用公开提供的配置文件信息，并且用户可以选择是否通过 LinkedIn 隐私设置与 Microsoft 应用程序共享其公共配置文件和网络信息。

>[!NOTE]
> 在 Azure AD 中启用 LinkedIn 集成可让应用和服务访问最终用户的一些信息。 在 Azure AD 中启用 LinkedIn 集成时，阅读 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement/)，了解有关隐私注意事项的详细信息。 

## <a name="enable-linkedin-integration"></a>启用 LinkedIn 集成
在 Azure AD 中，面向企业的 LinkedIn 集成默认情况下已启用。 因此，当此功能可供租户使用时，组织中的所有用户都可看到 LinkedIn 功能和配置文件。 启用 LinkedIn 集成允许组织中的用户使用 Microsoft 服务中的 LinkedIn 功能，例如，在 Outlook 中收到电子邮件时查看配置文件。 禁用此功能将阻止访问 LinkedIn 功能，并停止通过 Microsoft 服务在 LinkedIn 和组织之间进行用户帐户连接和数据共享。

> [!IMPORTANT]
> 此功能不可用于本地化租户、主权租户和政府租户。 此外，Azure AD 服务更新（如 LinkedIn 集成功能）不会同时部署到所有 Azure 租户。 在此功能部署到 Azure 租户之前，无法启用 LinkedIn 与 Azure AD 的集成。

1. 使用属于目录全局管理员的帐户登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)。
2. 选择“用户和组”。
3. 在“用户和组”边栏选项卡中，选择“用户设置”。
4. 在“LinkedIn 集成”下，选择“是”或“否”，以启用或禁用 LinkedIn 集成。
   ![启用 LinkedIn 集成](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>了解详细信息 
* [Microsoft 应用中的 LinkedIn 信息和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 帮助中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>后续步骤
可以使用以下链接从 Azure 门户启用或禁用 LinkedIn 与 Azure AD 的集成：

[配置 LinkedIn 集成](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 