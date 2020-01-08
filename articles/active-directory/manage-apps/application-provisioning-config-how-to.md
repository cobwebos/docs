---
title: 如何配置 Azure AD 库应用的用户预配
description: 如何为在 Azure AD 应用程序库中列出的应用程序快速配置丰富的用户帐户预配和取消预配
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8aa8265733128ed3b2ad5ddf16dcebb122b0f054
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443424"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>如何为 Azure AD 库应用程序配置用户预配

*用户帐户预配*是一种在应用程序的本地用户配置文件存储中，创建、更新和/或禁用用户帐户记录的操作。 大多数云和 SaaS 应用程序将用户角色和权限存储在用户自己的本地用户配置文件存储区中，并且用户本地存储中存在此类用户记录是单一登录和访问工作*所必需*的。

在 Azure 门户中，企业应用左侧导航窗格中的“预配”选项卡显示该应用支持哪些预配模式。 这可以是以下两个值的其中之一：

## <a name="configuring-an-application-for-manual-provisioning"></a>将应用程序配置为手动预配

*手动*设置是指必须使用应用提供的方法手动创建用户帐户。 这可能意味着登录该应用的管理门户，并使用基于 Web 的用户界面添加用户。 或者是使用该应用程序提供的机制，上传具有用户帐户详细信息的电子表格。 请查阅应用提供的文档，或联系应用开发人员来确定可用的机制。

如果 "*手动*" 是针对给定应用程序显示的唯一模式，则表示该应用尚未自动 Azure AD 预配连接器。 或者，这意味着该应用程序不支持 Microsoft 的用户管理 API 的先决条件，该 API 用于构建自动预配连接器。

如果想要为给定应用请求自动预配支持，可使用 [Azure Active Directory 应用程序请求](https://aka.ms/aadapprequest)填写请求。

## <a name="configuring-an-application-for-automatic-provisioning"></a>将应用程序配置为自动化预配

*自动化*意味着已为此应用程序开发了 Azure AD 预配连接器。 有关 Azure AD 预配服务及其工作原理的详细信息，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动化用户预配和取消预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)。

有关如何将特定用户和组预配到应用程序的详细信息，请参阅[管理为企业应用预配的用户帐户](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning)。

启用和配置自动预配所需的实际步骤因应用程序而异。

> [!NOTE]
> 首先，应找到特定于设置应用程序预配的设置教程，然后按照这些步骤操作配置应用和 Azure AD 以创建预配连接。 

可以在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)找到这些应用教程。

在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 这包括设置用于唯一标识和匹配两个系统之间的用户/组的 "匹配属性"。 有关属性映射的详细信息，请参阅*后续步骤*中的链接。

## <a name="next-steps"></a>后续步骤
[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

