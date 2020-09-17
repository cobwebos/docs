---
title: 什么是 Azure 单一登录 (SSO)？
description: 了解如何将单一登录 (SSO) 与 Azure Active Directory 配合使用。 使用 SSO，使用户无需记住每个应用程序的密码。 还可以使用 SSO 简化帐户管理。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperfq1
ms.openlocfilehash: 1eaef57f46bf6373fdd2a73575bb028904ef108b
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561644"
---
# <a name="what-is-single-sign-on-sso"></a>什么是单一登录 (SSO)？

单一登录意味着用户无需登录使用的每个应用程序。 用户登录一次，该凭据也可用于其他应用。

如果你是最终用户，可能并不关心 SSO 的详细信息。 你只想使用能提高工作效率而无需频繁输入密码的应用。 可在以下位置找到应用： https://myapps.microsoft.com 。
 
如果你是管理员或 IT 专业人员，请继续阅读，详细了解 SSO 以及如何在 Azure 中实现它。

## <a name="single-sign-on-basics"></a>单一登录基础知识
单一登录在用户登录和使用应用程序方面实现了巨大的飞跃。 基于单一登录的身份验证系统通常称为“新式身份验证”。 新式身份验证和单一登录属于称为标识和访问管理 (IAM) 的计算类别。 若要了解如何进行单一登录，请查看此视频。

身份验证基础知识：基础知识 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Web 应用程序的单一登录
Web 应用程序极为常用。 Web 应用由各种公司托管，并以一种服务的形式提供。 Web 应用的一些常见示例包括 Microsoft 365、GitHub 和 Salesforce 以及成千上万个其他应用。 用户使用计算机上的 Web 浏览器访问 Web 应用。 通过单一登录，用户可以在不同的 Web 应用之间导航，而无需多次登录。

若要了解单一登录如何与 Web 应用一起工作，请观看以下两个视频。

身份验证基础知识：Web 应用程序 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

身份验证基础知识：Web 单一登录 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>云托管应用与本地托管应用
如何实现单一登录取决于托管应用的位置。 由于路由网络流量来访问应用的方式不同，托管位置非常重要。 如果应用托管在本地网络上，并通过本地网络访问（称为本地应用），则用户无需访问 Internet 即可使用该应用。 如果应用托管在其他位置（称为云托管应用），则用户需要访问 Internet 才能使用该应用。

> [!TIP]
> 云托管应用也称为软件即服务 (SaaS) 应用。 

云托管应用的单一登录非常简单。 只需让标识提供者知道应用正在使用单一登录。 然后，将应用配置为信任标识提供者。 若要了解如何将 Azure AD 用作应用的标识提供者，请参阅[应用程序管理快速入门系列](add-application-portal.md)。

> [!TIP]
> 术语“云”和“Internet”通常可以互换。 这与网络图有关。 由于绘制每个组件是不可行的，因此通常会在图中将大型计算机网络表示为云的形状。 Internet 是最著名的网络，因此这两个术语很容易互换使用。 但是，任何计算机网络都可以被称为云。

你还可以对基于本地的应用使用单一登录。 用于实现本地 SSO 的技术称为应用程序代理。 若要了解它的详细信息，请参阅[单一登录选项](sso-options.md)。

## <a name="multiple-identity-providers"></a>多个标识提供者
将单一登录设置为在多个标识提供者之间工作时，这称为“联合身份验证”。 若要了解联合身份验证的工作原理，请观看以下视频。

身份验证基础知识：联合身份验证 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>后续步骤
* [应用程序管理的快速入门系列](view-applications-portal.md)
* [单一登录选项](sso-options.md)
