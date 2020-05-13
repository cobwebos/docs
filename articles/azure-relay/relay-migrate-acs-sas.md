---
title: Azure 中继 - 迁移到共享访问签名授权
description: 介绍如何将 Azure 中继应用程序从使用 Azure Active Directory 访问控制服务迁移到共享访问签名授权。
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 59b9e734526c56016e2ddf59c2afb5b8f7b4ad09
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211770"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure 中继 - 从 Azure Active Directory 访问控制服务迁移到共享访问签名授权

过去，Azure 中继应用程序有两种不同的授权模型可以选择。一种是中继服务直接提供的[共享访问签名 (SAS)](../service-bus-messaging/service-bus-sas.md) 令牌模型。另一种是联合模型，其中授权规则是由 [Azure Active Directory](/azure/active-directory/) 访问控制服务 (ACS) 在内部进行管理，从 ACS 获取的令牌会传递到中继，以授予对相应功能的访问权限。

ACS 授权模型一直以来都被首选模型 [SAS 授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)取代。目前，所有文档、指南和示例都只使用 SAS。 此外，无法再新建与 ACS 配对的中继命名空间。

SAS 的优势在于，不直接依赖另一服务，但可以授予客户端对 SAS 规则名称和规则密钥的访问权限，因此可以直接从客户端使用，而无需使用任何中介。 SAS 还可以与下列方法轻松集成：客户端必须先使用另一服务通过授权检查，再颁发令牌。 后一种方法类似于 ACS 使用模式，不同之处在于允许根据很难在 ACS 中表达的应用程序专属条件颁发访问令牌。

对于所有依赖 ACS 的现有应用程序，要求客户将应用程序迁移为依赖 SAS。

## <a name="migration-scenarios"></a>迁移方案

ACS 和中继通过签名密钥  这一共用概念进行集成。 ACS 命名空间使用签名密钥对授权令牌进行签名；Azure 中继使用签名密钥验证令牌是否已由配对的 ACS 命名空间颁发。 ACS 命名空间包含服务标识和授权规则。 授权规则定义了哪个服务标识或外部标识提供者颁发的哪个令牌拥有对部分中继命名空间图的何种访问权限（采用最长前缀匹配形式）。

例如，ACS 规则可能会向服务标识颁发“发送”  声明（路径前缀为 `/`）。也就是说，ACS 根据此规则颁发的令牌授权客户端，允许其向命名空间中的所有实体发送内容。 如果路径前缀为 `/abc`，标识只能向名为 `abc` 的实体或整理到此前缀下的实体发送内容。 为了能够理解此迁移指南，读者必须先熟悉这些概念。

迁移方案分为三大类：

1.  **未更改默认值**： 一些客户使用 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) 对象，同时传递为 ACS 命名空间（与中继命名空间配对）自动生成的所有者  服务标识及其密钥，不添加新规则。

2.  **包含简单规则的自定义服务标识**。 一些客户添加新的服务标识，并授予每个新服务标识对一个特定实体的“发送”  、“侦听”  和“管理”  权限。

3.  **包含复杂规则的自定义服务标识**。 很少有客户使用复杂规则集。在这些集中，外部颁发的令牌映射到中继上的权限，或在多个命名空间路径上通过多个规则为一个服务标识分配不同的权限。

有关复杂规则集迁移方面的帮助，可以联系 [Azure 支持部门](https://azure.microsoft.com/support/options/)。 前两个方案启用的是直接迁移。

### <a name="unchanged-defaults"></a>未更改默认值

如果应用程序未更改 ACS 默认值，可以将使用的所有 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) 替换为 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 对象，并使用命名空间预配置的 RootManageSharedAccessKey  ，而不是 ACS 所有者  帐户。 请注意，即使使用 ACS 所有者  帐户，通常也都不建议使用这种配置（现在仍不建议），因为此帐户/规则提供对命名空间的完整管理权限，包括删除任何实体的权限。

### <a name="simple-rules"></a>简单规则

如果应用程序使用包含简单规则的自定义服务标识，那么在创建 ACS 服务标识以提供对特定中继的访问控制时，迁移非常简单。 SaaS 式解决方案通常会出现这种情况。在此类解决方案中，每个中继被用作与租户网站或分支机构的桥梁，并且会为特定网站创建服务标识。 在这种情况下，可以直接在中继上将各自的服务标识迁移到共享访问签名规则。 服务标识名称可能会变成 SAS 规则名称，服务标识密钥可能会变成 SAS 规则密钥。 然后，将 SAS 规则的权限配置为相当于实体的各自适用 ACS 规则。

可以在与 ACS 联合的任何现有命名空间上就地额外配置新 SAS，随后使用 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider)（而不是 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)）从 ACS 迁移。 命名空间不需要与 ACS 取消关联。

### <a name="complex-rules"></a>复杂规则

SAS 规则并不是帐户，而是与权限相关联的命名签名密钥。 因此，在应用程序创建多个服务标识并向其授予访问多个实体或整个命名空间权限的情况下，仍需要令牌颁发中介。 若要获取此类中介的相关指南，可以[联系支持部门](https://azure.microsoft.com/support/options/)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 中继身份验证，请参阅以下主题：

* [Azure 中继身份验证和授权](relay-authentication-and-authorization.md)
* [附有共享访问签名的服务总线身份验证](../service-bus-messaging/service-bus-sas.md)


