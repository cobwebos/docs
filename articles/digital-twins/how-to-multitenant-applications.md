---
title: 使用 Azure 数字孪生启用多租户应用程序 | Microsoft Docs
description: 如何为 Azure 数字孪生配置多租户 Azure Active Directory 应用程序。
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925962"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>使用 Azure 数字孪生启用多租户应用程序

借助 Azure 数字孪生进行生成的解决方案开发者可能希望通过单个服务或解决方案为多个客户提供支持。 事实上，多租户应用程序是最常见的 Azure 数字孪生配置。

本文档介绍如何配置 Azure 数字孪生应用以支持多个 Azure Active Directory 租户和客户。

## <a name="multitenancy"></a>多租户

多租户资源是支持多个客户的单个预配实例。 每个客户都有自己的独立数据和特权。 每个客户的体验都是相互独立的，如此应用程序为其呈现的内容也是截然不同的。

若要了解有关多租户的详细信息，请参阅 [Azure 中的多租户应用程序](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)。

## <a name="problem-scenario"></a>问题情景

在此情景中，假设有一位生成 Azure 数字孪生解决方案的开发人员（开发人员），和一位使用该解决方案的客户（客户）：

- 开发人员拥有包含 Azure Active Directory 租户的 Azure 订阅。
- 开发人员将 Azure 数字孪生实例部署到其 Azure 订阅中。 Azure Active Directory 在开发人员的 Azure Active Directory 租户中自动创建了一个服务主体。
- 随后，开发人员的 Azure Active Directory 租户中的用户可通过 Azure 数字孪生服务[获取 OAuth 2.0 令牌](./security-authenticating-apis.md)。
- 现在，开发人员创建了一个可以与 Azure 数字孪生管理 API 直接集成的移动应用。
- 开发人员允许客户使用该移动应用程序。
- 客户必须获得授权才能使用 开发人员应用程序中的 Azure 数字孪生管理 API。

问题：

- 当客户登录到开发人员的应用程序时，该应用将无法获取客户的用户用以对 Azure 数字孪生管理 API 进行身份验证的令牌。
- Azure Active Directory 中发生异常，指示 Azure 数字孪生未在 CUSTOMER 的目录中识别。

## <a name="problem-solution"></a>问题解决方案

要解决上述情况，需要执行以下操作，在 CUSTOMER 的 Azure Active Directory 租户中创建 Azure 数字孪生服务主体：

- 如果 CUSTOMER 尚不具有包含 Azure Active Directory 租户的 Azure 订阅，则：

  - CUSTOMER 的 Azure Active Directory 租户管理员必须获取[即用即付 Azure 订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。
  - 然后 CUSTOMER 的 Azure Active Directory 租户管理员必须[将其租户与新订阅进行链接](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)。

- 在 [Azure 门户](https://portal.azure.com)中，CUSTOMER 的 Azure Active Directory 租户管理员执行以下步骤：

  1. 打开“订阅”。
  1. 选择一个订阅，该订阅需具有要在开发人员的应用程序中使用的 Azure Active Directory 租户。

     ![Azure Active Directory 订阅][1]

  1. 选择“资源提供程序”。
  1. 搜索 Microsoft.IoTSpaces。
  1. 选择“注册”。

     ![Azure Active Directory 资源提供程序][2]
  
## <a name="next-steps"></a>后续步骤

- 若要深入了解如何将用户定义的函数与 Azure 数字孪生配合使用，请阅读[如何创建 Azure 数字孪生用户定义的函数](./how-to-user-defined-functions.md)。

- 若要了解如何使用基于角色的访问控制进一步保护具有角色分配的应用程序，请阅读[如何创建和管理 Azure 数字孪生基于角色的访问控制](./security-create-manage-role-assignments.md)。

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
