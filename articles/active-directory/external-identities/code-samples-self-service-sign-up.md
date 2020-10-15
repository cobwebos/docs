---
title: 用户流的 API 连接器代码示例 - Azure AD
description: Azure Active Directory 外部标识的自助注册流中的 API 连接器代码示例。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87906821"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>外部标识自助注册示例

下表提供了一些代码示例的链接，这些示例使用 [API 连接器](api-connectors-overview.md)在自助注册用户流中利用 Web API。

## <a name="api-connector-azure-function-quickstarts"></a>API 连接器 Azure Function 快速入门

| 示例                                                                                                                          | 说明                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 此 .NET Core Azure Function 示例演示如何将登录限制为特定租户域并验证用户提供的信息。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 此 Node.js Azure Function 示例演示如何将登录限制于特定租户域并验证用户提供的信息。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 此 Python Azure Function 示例演示如何将登录限制为特定租户域并验证用户提供的信息。    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>自定义审批工作流

| 示例 | 说明 |
|--------| ----------- |
| [手动审批工作流](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | 此示例演示用于在自助注册中管理来宾用户帐户创建的端到端审批工作流 |

## <a name="identity-verification"></a>身份验证

| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 此示例演示如何使用 API 连接器与 IDology 集成，以在自助注册过程中验证用户标识。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 此示例演示如何使用 API 连接器与 Experian 集成，以在自助注册过程中验证用户标识。 |
