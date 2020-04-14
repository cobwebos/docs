---
title: 云合作伙伴门户 API 参考 |Azure 应用商店
description: 市场 API 操作的说明、要使用的先决条件和列表。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256410"
---
# <a name="cloud-partner-portal-api-reference"></a>云合作伙伴门户 API 参考

> [!NOTE]
> 云合作伙伴门户 API 与合作伙伴中心集成，在您的产品/服务迁移到合作伙伴中心后将继续工作。 集成引入了小更改。 查看本文档中列出的[对 CPP API 的更改](#changes-to-cpp-apis-after-the-migration-to-partner-center)，以确保代码在迁移到合作伙伴中心后继续工作。

云合作伙伴门户 REST API允许以编程方式检索和操作工作负载、产品/服务和发布者配置文件。 API 使用基于角色的访问控制 (RBAC) 在处理时强制实施正确的权限。

本参考提供云合作伙伴门户 REST API 的技术详细信息。 本文档中的有效负载示例仅供参考，随着新功能的添加可能会发生变化。

## <a name="prerequisites-and-considerations"></a>先决条件和注意事项

在使用 API 之前，应该查看：

- [先决条件](./cloud-partner-portal-api-prerequisites.md)一文，了解如何向帐户添加服务主体，并获取 Azure Active Directory (Azure AD) 访问令牌以进行身份验证。
- 可用于调用这些 API 的两[个并发控制](./cloud-partner-portal-api-concurrency-control.md)策略。
- 其他 API [注意事项](./cloud-partner-portal-api-considerations.md)，如版本控制和错误处理。

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>迁移到合作伙伴中心后对 CPP API 的更改

| **API** | **更改描述** | **影响** |
| ------- | ---------------------- | ---------- |
| POST 发布、GoLive、取消 | 对于迁移的要约，响应标头将具有不同的格式，但将继续以相同的方式工作，表示检索操作状态的相对路径。 | 发送任何相应的 POST 请求为产品/服务时，位置标头将具有两种格式之一，具体取决于产品/服务的状态：<ul><li>非迁移优惠<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>迁移优惠<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 获取操作 | 对于响应中以前支持"通知电子邮件"字段的产品/服务类型，此字段将被弃用，并且不再为迁移的优惠返回。 | 对于迁移的优惠，我们将不再向请求中指定的电子邮件列表发送通知。 相反，API 服务将与合作伙伴中心中的通知电子邮件进程一致发送电子邮件。 具体来说，通知将发送到合作伙伴中心中帐户设置的卖家联系信息部分中设置的电子邮件地址，以通知您操作进度。<br><br>请查看合作伙伴中心[中"帐户设置](https://partner.microsoft.com/dashboard/account/management)的卖家联系信息"部分中设置的电子邮件地址，以确保为通知提供正确的电子邮件。  |

## <a name="common-tasks"></a>常见任务

本参考详细介绍了用于执行以下常见任务的 API。

### <a name="offers"></a>产品

- [检索所有产品/服务](./cloud-partner-portal-api-retrieve-offers.md)
- [检索特定产品/服务](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [检索产品/服务状态](./cloud-partner-portal-api-retrieve-offer-status.md)
- [创建产品](./cloud-partner-portal-api-creating-offer.md)
- [发布产品/服务](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>操作

- [检索操作](./cloud-partner-portal-api-retrieve-operations.md)
- [取消操作](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>发布应用

- [投入使用](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>其他任务

- [设置虚拟机套餐的定价](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>故障排除

- [排查身份验证错误](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
