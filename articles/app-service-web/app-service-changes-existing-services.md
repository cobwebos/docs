---
title: "Azure 应用服务及其对现有 Azure 服务的影响"
description: "介绍了新的 Azure 应用服务及其功能对 Azure 中现有服务的影响。"
services: app-service
documentationcenter: 
author: yochay
manager: nirma
editor: 
ms.assetid: 86c6a292-3c33-49f4-890c-89cc0321b397
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: yochaykk
ms.translationtype: Human Translation
ms.sourcegitcommit: 75d42f41e6218d83cda00f2ef8926d6ca1f0aacd
ms.openlocfilehash: 8f458884d0ea649dffa5f92fd459bbaaa9f5e4cf
ms.contentlocale: zh-cn
ms.lasthandoff: 12/07/2016


---
# <a name="azure-app-service-and-existing-azure-services"></a>Azure 应用服务和现有的 Azure 服务
本文概括介绍了对现有 Azure 服务的更改，这是将多个 Azure 服务组合为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)（新的集成产品）更改的一部分。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>概述
[Azure 应用服务](https://azure.microsoft.com/services/app-service/)是一种全新独特的的云服务，使开发人员能够创建适用于任何平台和任何设备的 Web 应用和移动应用。 应用服务是一个集成的解决方案，旨在简化重复编码函数、与企业和 SaaS 系统集成并自动执行业务流程，同时满足你对安全性、可靠性和可伸缩性的需要。

应用服务将以下现有 Azure 服务 - [网站](https://azure.microsoft.com/services/websites/)、[移动服务](https://azure.microsoft.com/services/mobile-services/)和 [Biztalk 服务](https://azure.microsoft.com/services/biztalk-services/)融合为单个综合服务，同时添加强大的新功能。  应用服务允许托管以下应用类型：

* Web 应用
* 移动应用
* API 应用
* Logic Apps

下表介绍了如何将现有的 Azure 服务映射到应用服务以及其中可用的应用类型。

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">现有的应用服务</th>
<th align="left", style="width:10%">Azure 应用服务</th>
<th align="left", style="width:80%">更改内容</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure 网站</td>
<td align="left">Web 应用</td>
<td align="left"><li>对于 Azure 网站，应用服务严格限制为将名称“网站”更改为“Web 应用”。
<p><li>现在，应用服务中的所有现有网站实例均已成为 Web 应用。</p>
<p><li>可以通过 <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure 门户</a>访问现有网站，该门户的 <em>Web 应用</em>下列出了所有现有站点。</p>
<p><li><em>Web 托管计划</em>现成为<em>应用服务计划</em>。 <em>应用服务计划</em>可以托管任何应用类型的应用服务，例如 Web 应用、移动应用、逻辑应用或 API 应用。</p>
<p><li>Azure 应用服务 Web 应用现已公开上市。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">了解有关 Web 应用的详细信息</a>。</p></td>
</tr>
<tr class="even">
<td align="left">Azure 移动服务</td>
<td align="left">移动应用</td>
<td align="left"><p><li>移动服务继续用作独立服务，并仍然受完全支持。</p>
<p><li>移动应用是应用服务中的应用类型，它融合了移动服务和其他服务的所有功能。</p>
<p><li>可以轻松地<a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">从移动服务迁移到移动应用</a>。</p>
<p><li>作为应用服务的一部分，移动应用的功能远胜于移动服务，例如，与本地和 SaaS 系统集成、暂存槽、Web 作业、更好的缩放选项，等等。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">了解有关移动应用的详细信息</a>。</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API 应用</td>
<td align="left">
<p><li>API 应用是应用服务中的全新应用类型，它让你能够轻松地构建并使用云中的 API。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">了解有关 API 应用的详细信息</a>。</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">逻辑应用</td>
<td align="left">
<p><li>逻辑应用是应用服务中的全新应用类型，它让你能够轻松地自动执行业务流程。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">了解有关逻辑应用的详细信息</a>。</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk 服务</td>
<td align="left">BizTalk API 应用</td>
<td align="left">
<li><p>BizTalk 服务继续用作独立服务，并仍然受完全支持。</p>
<li><p>BizTalk 服务的所有功能都将集成到应用服务，作为 API 应用使用户能够使用应用服务中的任何应用类型执行企业应用程序集成和 B2B 集成方案。</p>
<li><p>凭借逻辑应用，可以使用可视化设计体验自动执行业务流程，以创建工作流。</p></td>
</tr>
</tbody>
</table>

若要了解详细信息，请访问[应用服务文档](https://azure.microsoft.com/documentation/services/app-service/)。


