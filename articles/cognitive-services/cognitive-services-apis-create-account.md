---
title: 在 Azure 门户中创建认知服务 API 帐户 | Microsoft 文档
description: 如何在 Azure 门户中创建 Microsoft 认知服务 API 帐户。
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036146"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>在 Azure 门户中创建认知服务 API 帐户

若要使用 Microsoft 认知服务 API，首先需要在 Azure 门户中创建帐户。

1. 登录到 [Azure 门户](http://portal.azure.com)。

2. 单击“+ 创建资源”。

3. 在“Azure 市场”下，选择“AI + 认知服务”并发现可用 API 的列表。 单击“查看全部”以查看认知服务 API 的完整列表。 单击所选 API 继续操作。

    ![选择认知服务 API](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. 在“创建”页中提供以下信息： 

   - **帐户名：** 帐户的名称。 我们建议使用描述性的名称，例如 *AFaceAPIAccount*。

   - **订阅：** 选择一个可用的至少具有参与者权限的 Azure 订阅。

   - **API 类型：** 选择想要使用的认知服务 API。 有关可用的各种认知服务 API 的详细信息，请访问[认知服务](https://azure.microsoft.com/services/cognitive-services/)站点。

   - **定价层：** 认知服务帐户的费用取决于实际使用情况和你所选的选项。 有关每个 API 的定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/)。

   - **资源组：** 资源组是具有共同生命周期、权限和策略的资源的集合。 若要了解有关资源组的详细信息，请参阅[通过门户管理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

   - **资源组位置：** 仅当所选 API 是全局的（未绑定到一个位置），才需要填写此信息。 但是，如果 API 是全局的且未绑定到一个位置，则必须为资源组指定一个位置，与认知服务 API 帐户关联的元数据将驻留于此位置。 此位置对帐户的运行时可用性没有任何影响。 若要了解有关资源组的详细信息，请参阅[通过门户管理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

   - **明确承认联机服务条款：** 若要创建帐户，订阅所有者或参与者（由 [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)定义）需要明确承认[联机服务条款](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx)中适用于认知服务的条款。 

     订阅所有者可以按照[使用 Azure 门户分配和管理资源策略](../azure-policy/assign-policy-definition.md)一文中的说明操作，分配“不允许的资源类型“策略定义，并将 **Microsoft.CognitiveServices/accounts** 指定为目标资源类型，从而通过 [Azure 策略](../azure-policy/azure-policy-introduction.md)禁止为特定资源组或订阅创建认知服务帐户。

     如果禁用了帐户创建功能，则在创建帐户时会显示以下错误：

     ![帐户创建错误](media/cognitive-services-apis-create-account/error-message.png)

5. 要将帐户固定到 Azure 门户仪表板，请单击“固定到仪表板”。

6. 单击“创建”  以创建帐户。

成功部署认知服务帐户后，单击仪表板中的磁贴以查看帐户信息。

可以使用“概览”部分的“终结点 URL”和“密钥”部分的密钥在应用程序中进行 API 调用。

![显示帐户信息](media/cognitive-services-apis-create-account/display-account.png)

![显示帐户密钥](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>后续步骤

有关所有可用的 Microsoft 认知服务的详细信息，请参阅[认知服务](https://azure.microsoft.com/services/cognitive-services/)。

有关使用某些认知服务 API 示例的快速入门指南：

 - [计算机视觉 C# 快速入门](computer-vision/quickstarts/csharp.md)
 - [使用 Python 进行文本分析](text-analytics/quickstarts/python.md)
 - [面向 JavaScript 的人脸 API](face/quickstarts/javascript.md)
