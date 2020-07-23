---
title: 使用 Azure 门户导入 SOAP API | Microsoft Docs
description: 了解如何使用 API 管理导入 SOAP API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 7b1fd34824bec9641edc94ce278fa21a1b57b2c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202734"
---
# <a name="import-soap-api"></a>导入 SOAP API

本文介绍如何导入 SOAP API 的标准 XML 表示形式。 本文还介绍如何测试 API 管理 API。

在本文中，学习如何：

> [!div class="checklist"]
> * 导入 SOAP API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>导入和发布后端 API

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”  。
2. 从“添加新的 API”列表中选择“WSDL”   。

    ![Soap api](./media/import-soap-api/wsdl-api.png)
3. 在“WSDL 规范”，输入要将 SOAP API 放到的 URL。 
4. “SOAP 直通”选按钮默认处于选中状态  。 进行此选择，API 将作为 SOAP 公开。 使用者必须使用 SOAP 规则。 若要“REST 化”API，请按照[导入 SOAP API 并将其转换为 REST](restify-soap-api.md) 中的步骤进行操作。

    ![直通](./media/import-soap-api/pass-through.png)
5. 按 Tab 键。

    以下字段中填充了来自 SOAP API 的信息：显示名称、名称、说明。
6. 添加 API URL 后缀。 后缀是用于在该 API 管理实例中标识此特定 API 的名称。 在该 API 管理实例中，后缀必须唯一。
7. 通过关联 API 与产品来发布 API。 本例中使用了“无限制”产品  。  如果想要发布 API 并使其对开发人员可用，请将其添加到产品中。 可在 API 创建期间执行此操作，或稍后进行设置。

    产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果你创建了 API 管理实例，那么你已是管理员，因此默认情况下订阅了每个产品。

    默认情况下，每个 API 管理实例附带两个示例产品：

    * **入门**
    * **不受限制**   
8. 输入其他 API 设置。 可以在创建过程中设置这些值，也可以稍后转到“设置”  选项卡来配置这些值。在[导入和发布第一个 API](import-and-publish.md#-import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
9. 选择“创建”  。

### <a name="test-the-new-api-in-the-administrative-portal"></a>在管理门户中测试新的 API

可直接从管理门户调用操作，这样可以方便地查看和测试 API 的操作。  

1. 选择上一步中创建的 API。
2. 按“测试”选项卡  。
3. 选择某个操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，用于提供和此 API 关联的产品订阅密钥。 如果创建了 API 管理实例，那么你已是管理员，因此会自动填充该密钥。 
1. 按“发送”。 

    后端以“200 正常”和某些数据做出响应  。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)