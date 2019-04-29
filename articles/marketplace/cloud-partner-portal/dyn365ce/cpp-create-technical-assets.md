---
title: 创建 Dynamics 365 for Customer Engagement 技术资产 - Azure 市场 | Microsoft Docs
description: 创建 Dynamics 365 for Customer Engagement 应用程序产品/服务的技术资产。
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472662"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>为 Azure 应用程序产品/服务创建技术资产

一般情况下，使用[适用于 Dynamics 365 for Customer Engagement 应用的 SDK](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk) 开发解决方案。  解决方案具有多种形式，如[适用于 Dynamics 365 for Customer Engagement 应用的编程模型](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models)中所述。  选择最符合你的解决方案要求的形式。  开发解决方案时，需要解决大量问题，例如扩展性选择、解决方案组件和版本兼容性。  有关详细信息，请参阅[解决方案简介](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions)。

大多数发布到 AppSource 的 Dynamics 365 解决方案都是作为包文件分发的托管应用程序。


## <a name="creating-and-storing-the-package"></a>创建和存储包

有关创建 Dynamics 365 for Customer Engagement 产品/服务的并行文档，请参阅[在 AppSource 上发布应用](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource)部分。  以下包含的主题详细介绍如何创建解决方案包文件并将其上传到 Azure 存储：

- [步骤 4：为应用创建 AppSource 包](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) - 介绍如何创建表示托管应用程序的压缩 (zip) 文件，该文件中应包含：解决方案资产文件夹、自定义代码 DLL、MIME 类型信息文件、AppSource 包图标、许可条款文件 (HTML) 和内容文件 (XML)。
- [步骤 5：在 Azure 存储中存储 AppSource 包，并使用 SAS 密钥生成 URL](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) - 介绍如何在 Microsoft Azure 存储 Blob 存储帐户中存储 AppSource 包文件，并使用共享访问签名 (SAS) 密钥共享包文件。 将从你的 Azure 存储位置检索该包文件进行认证，然后将其用于 AppSource 试用和发布。


## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请[创建 Dynamics 365 for Customer Engagement 产品/服务](./cpp-create-offer.md)。  然后，即可[发布产品/服务](./cpp-publish-offer.md)。
