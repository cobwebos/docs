---
title: 使用 Azure 门户导入 OpenAPI 规范 | Microsoft Docs
description: 了解如何使用 API 管理导入 OpenAPI 规范。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 2e4dee74eb0c50e8e12d3f9ff0dccdd83271ea65
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202904"
---
# <a name="import-an-openapi-specification"></a>导入 OpenAPI 规范

本文介绍如何导入 https://conferenceapi.azurewebsites.net?format=json 中的“OpenAPI 规范”后端 API。 此后端 API 由 Microsoft 提供并托管在 Azure 上。 此外，介绍如何测试 APIM API。

在本文中，学习如何：

> [!div class="checklist"]
> * 导入“OpenAPI 规范”后端 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>导入和发布后端 API

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”  。
2. 从“添加新的 API”列表中选择“OpenAPI 规范”   。

    ![OpenAPI 规范](./media/import-api-from-oas/oas-api.png)
3. 输入 API 设置。 可以在创建过程中设置这些值，也可以稍后转到“设置”  选项卡来配置这些值。在[导入和发布第一个 API](import-and-publish.md#-import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
4. 选择“创建”  。

> [!NOTE]
> API 导入限制在[另一篇文章](api-management-api-import-restrictions.md)中介绍。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
