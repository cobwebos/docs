---
title: 如何在 Azure API 管理中创建和发布产品
description: 了解如何在 Azure API 管理中创建和发布产品。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 686d6e2ff01fd4d8283350ab8097e1f73c914af0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81260990"
---
# <a name="create-and-publish-a-product"></a>创建和发布产品  

在 Azure API 管理中，产品包含一个或多个 API 以及使用配额和使用条款。 一旦产品发布，开发人员可以订阅该产品，并开始使用产品的 API。  

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建和发布产品
> * 将 API 添加到产品

![添加产品教程](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

## <a name="create-and-publish-a-product"></a>创建和发布产品

![添加产品](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. 单击左侧菜单中的“产品”  显示“产品”  页。
2. 单击“+ 添加”。 

    添加产品时，需要提供以下信息： 

    | 名称                     | 说明                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 显示名称             | 要在**开发人员门户**中显示的名称。                                                                                                                                                                                                                                                        |
    | 名称                     | 产品的描述性名称。                                                                                                                                                                                                                                                                                      |
    | 说明              | 可在“说明”  字段中提供有关该产品的详细的信息，例如其用途，它提供访问权的 API 和其他有用的信息。                                                                                                                                               |
    | 状态                    | 如果想要发布该产品，请按“发布”。  在产品中的 API 可调用前，必须先发布该产品。 默认情况下新产品未发布，并仅对“管理员”  组可见。                                                                                      |
    | 需要订阅    | 如果需要用户订阅才能使用该产品，请选中“需要订阅”  。                                                                                                                                                                                                                                   |
    | 需要批准        | 如果希望管理员审查并接受或拒绝此产品的订阅尝试，请选中“需要审批”  。 如果未选中此框，则会自动批准订阅尝试。                                                                                                                         |
    | 订阅计数限制 | 若要限制多个同步订阅的计数，请输入订阅限制。                                                                                                                                                                                                                                |
    | 法律条款              | 可以包含产品的使用条款，订阅者必须接受这些条款才能使用该产品。                                                                                                                                                                                                             |
    | API                     | 产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 <br/> 可以在创建产品期间添加现有的 API。 之后可以通过产品的“设置”页或者在创建 API 时将 API 添加到产品  。 |

3. 单击“创建”创建新产品。 

### <a name="add-more-configurations"></a>添加更多配置

保存后，可以选择“设置”选项卡继续配置产品。  

通过“订阅”选项卡查看/添加产品的订阅者。 

通过“访问控制”选项卡为开发人员或来宾设置产品的可见性  。

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>将 API 添加到产品

产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 可以在创建产品期间添加现有的 API。 以后，可以通过产品的“设置”页上或者在创建 API 时将 API 添加到产品。 

开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。

### <a name="add-an-api-to-an-existing-product"></a>将 API 添加到现有产品

![添加产品 API](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. 从“产品”  选项卡选择一个产品。
2. 导航到“API”  选项卡。
3. 单击“+ 添加”。 
4. 选择 API 并单击“选择”。 

> [!TIP]
> 可以[通过 REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate) 或 PowerShell 命令使用自定义订阅密钥来创建用户的订阅或将其更新为某个产品。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建和发布产品
> * 将 API 添加到产品

转到下一教程：

> [!div class="nextstepaction"]
> [创建空白 API 和模拟 API 响应](mock-api-responses.md)
