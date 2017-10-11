---
title: "为应用商店创建数据服务的技术先决条件 | Microsoft Docs"
description: "了解创建数据服务以在 Azure 应用商店中部署和出售的要求"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>为 Azure 应用商店创建数据服务产品/服务的技术先决条件
> [!IMPORTANT]
> **本次我们不再载入任何新的数据服务发布服务器。新的 dataservices 将不会获准出现在列表中。** 如果想要在 AppSource 上发布 SaaS 业务应用程序，可以在[此处](https://appsource.microsoft.com/partners)找到更多信息。 如果想要在 Azure 应用商店上发布 IaaS 应用程序或开发人员服务，可以在[此处](https://azure.microsoft.com/marketplace/programs/certified/)找到更多信息。
> 
> 

在开始之前，请仔细阅读过程，并了解执行每个步骤的位置和原因。 在开始产品/服务创建过程前，应尽可能多地准备公司信息和其他数据、下载必需的工具和/或创建技术组件。

在开始过程前，应已准备好以下项目：

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>决定发布数据服务产品/服务使用的技术
在 Azure 应用商店中发布数据服务时，发布者可在多项技术之间作出抉择。 支持的主要技术如下所述。 无论使用何种技术发布数据服务，最终用户均通过 Azure 应用商店服务公开的“OData 源”使用数据。 有关 OData 服务的完整信息可在 [http://www.odata.org/](http://www.odata.org/) 上找到

## <a name="sql-azure-database"></a>SQL Azure 数据库
在 SQL Azure 中准备好数据集是发布者的责任。 需要订阅 Azure、预配数据库的相应大小，并将数据上传到 SQL Azure DB。 发布者还负责使其数据始终处于最新状态。 有关订阅 Azure 服务的详细信息可在 [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/) 上找到

将数据移至 SQL Azure 时，Azure 应用商店将公开表和视图。 发布者可指定向最终用户公开的表/视图和列。 另外，内容提供商也可指定最终用户查询的列和仅在有效负载中返回的列。 这可高度灵活地决定应公开的数据库数据。 可查询的列需要一个或多个数据库索引的支持。

## <a name="rest-based-web-service"></a>基于 REST 的 Web 服务
支持的协议：**仅 HTTPS**

现有基于 REST 的服务可通过 Azure 应用商店公开。 由于数据集始终作为 OData 源向最终用户公开，所以 Azure 应用商店需能够将服务映射到基于 OData 的服务。 为此，基于 REST 的终结点需将所有参数公开为 HTTP 参数。

有效负载需要采用可映射到 ATOM 响应中的形式。 因此，服务响应需要使用 XML 格式，并且只能包含一个含有有效负载值的重复元素（例如记录集）。 Azure 应用商店服务将重复节点映射到 ATOM 中的条目节点，将有效负载值映射到条目节点中的属性节点。

需要以 HTTP 参数形式或在 HTTP 标头（键值对）中提供授权信息（如 API 密钥、身份验证令牌等），也支持基本身份验证。 需要提供有效密钥，并且所有通过 Azure 应用商店提出的请求均通过该密钥提出。 用户监视和计费在 Azure 应用商店层进行。

服务返回的错误需要映射到 HTTP 状态代码。 如果服务返回包含错误的 XML，这些错误由 Azure 应用商店服务映射到 HTTP 状态代码。

## <a name="soap-based-web-services"></a>基于 SOAP 的 Web 服务
协议：**仅 HTTPS**

要求与基于 REST 的服务部分中的要求相同。 唯一的区别在于参数也可在发布到发布者服务的 XML 正文中提供，并且该正文的所有请求均由 Azure 应用商店提出。 这意味着用户在前端提供的 HTTP 参数要转换为与请求一起发布到内容提供商 Web 服务的 XML 文档的 XML 元素。

## <a name="odata-based-web-services"></a>基于 OData 的 Web 服务
协议：**仅 HTTPS**

数据可公开为 Azure 应用商店的 OData 服务。 系统通过 Azure 应用商店服务根传递服务和替换服务根，以确保所有后续调用都会经过 Azure 应用商店。

OData 服务无需仅根据后端数据库运行。 OData 支持使用任何种类的存储或业务逻辑驱动服务。

## <a name="next-steps"></a>后续步骤
现在已查看先决条件并已完成必需的任务，可以继续创建数据服务产品/服务，如[数据服务发布指南](marketplace-publishing-data-service-creation.md)中所详述。

或者，如果要查看整体过程和每个发布阶段的相应文章，请访问文章[入门：如何将产品/服务发布到 Azure 应用商店](marketplace-publishing-getting-started.md)。

[link-acct]:marketplace-publishing-accounts-creation-registration.md
