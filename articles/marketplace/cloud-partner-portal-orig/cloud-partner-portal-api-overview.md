---
title: 云合作伙伴门户 API 参考 | Microsoft Docs
description: 市场 API 操作的说明、要使用的先决条件和列表。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 116eb48330381c7560c55ea9535b3c1b7c6a6a70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094381"
---
<a name="cloud-partner-portal-api-reference"></a>云合作伙伴门户 API 参考
==================================

云合作伙伴门户 REST API允许以编程方式检索和操作工作负载、产品/服务和发布者配置文件。 API 使用基于角色的访问控制 (RBAC) 在处理时强制实施正确的权限。

本参考提供云合作伙伴门户 REST API 的技术详细信息。 本文档中的有效负载示例仅供参考，随着新功能的添加可能会发生变化。


<a name="prerequisites-and-considerations"></a>先决条件和注意事项
-------------------------------

在使用 API 之前，应该查看：

- [先决条件](./cloud-partner-portal-api-prerequisites.md)一文，了解如何向帐户添加服务主体，并获取 Azure Active Directory (Azure AD) 访问令牌以进行身份验证。 
- 两个[并发控制](./cloud-partner-portal-api-concurrency-control.md)
策略，可用来调用这些 API。
- 其他 API [注意事项](./cloud-partner-portal-api-considerations.md)，如版本控制和错误处理。


<a name="common-tasks"></a>常见任务
------------
本参考详细介绍了用于执行以下常见任务的 API。


### <a name="offers"></a>产品

-   [检索所有产品/服务](./cloud-partner-portal-api-retrieve-offers.md)
-   [检索特定产品/服务](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [检索产品/服务状态](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [创建产品/服务](./cloud-partner-portal-api-creating-offer.md)
-   [发布产品/服务](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>操作

-   [检索操作](./cloud-partner-portal-api-retrieve-operations.md)
-   [取消操作](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>发布应用

-   [推出](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>其他任务

-   [设置虚拟机产品/服务的定价](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>故障排除

-   [排查身份验证错误](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
