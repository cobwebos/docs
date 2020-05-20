---
title: 创建 Azure API 管理实例 | Microsoft Docs
description: 按照本教程中的步骤创建新的 Azure API 管理实例。
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: acfa10ea506e063bf6b230386ca0722f1c959d18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75442561"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>创建新的 Azure API 管理服务实例

Azure API 管理 (APIM) 可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。 API 管理通过开发人员参与、商业洞察力、分析、安全性和保护提供了核心竞争力以确保成功的 API 程序。 使用 APIM 可以为在任何位置托管的现有后端服务创建和管理新式 API 网关。 有关详细信息，请参阅[概述](api-management-key-concepts.md)主题。

本快速入门介绍如何使用 Azure 门户创建新的 API 管理实例的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![API 管理实例](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-new-service"></a>创建新服务

1. 在 Azure 门户菜单中，选择“创建资源”  。 还可以在 Azure“主页”上选择“创建资源” 。 
   
   ![选择“创建资源”](./media/get-started-create-service-instance/00-CreateResource-01.png)
   
1. 在“新建”屏幕上，选择“集成”，然后选择“API 管理”    。
   
   ![新建 Azure API 管理实例](./media/get-started-create-service-instance/00-CreateResource-02.png)
   
1. 在“API 管理服务”屏幕中，输入设置  。
   
   ![新实例](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)
   
   | 设置                 | 建议的值                               | 说明                                                                                                                                                                                                                                                                                                                         |
|-------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**                | API 管理服务的唯一名称 | 以后无法更改此名称。 服务名称用来以 *{name}.azure-api.net* 形式生成默认域名。 如果希望使用自定义域名，请参阅[配置自定义域名](configure-custom-domain.md)。 <br/> 服务名称用来引用服务和相应的 Azure 资源。 |
| **订阅**        | 订阅                             | 要在其下创建此新服务实例的订阅。 在可以访问的不同 Azure 订阅中选择一个订阅。                                                                                                                                                            |
| **资源组**      | *apimResourceGroup*                           | 可以选择新的或现有的资源。 资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../azure-resource-manager/management/overview.md#resource-groups)了解更多信息。                                                                                                  |
| **位置**            | *美国西部*                                    | 选择最近的地理区域。 下拉列表中仅显示可用的 API 管理服务区域。                                                                                                                                                                                                          |
|  组织名称   | 组织的名称                 | 许多位置，包括开发人员门户和通知电子邮件的发件人的标题中使用此名称。                                                                                                                                                                                                             |
| **管理员电子邮件** | *admin\@org.com*                               | 设置要将来自 **API 管理**的所有通知发送到的电子邮件地址。                                                                                                                                                                                                                                              |
| **定价层**        | *开发人员*                                   | 设置“开发人员”层来评估服务。  此层不用于生产用途。 有关对 API 管理层进行缩放的详细信息，请参阅[升级和缩放](upgrade-and-scale.md)。                                                                                                                                    |

3. 选择“创建”。 

    > [!TIP]
    > 创建 API 管理服务通常需要花费 20 到 30 分钟。 选择“固定到仪表板”可以更容易地查找新创建的服务。 

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和所有相关资源，可以使用以下步骤将其删除：

1. 在 Azure 门户中，搜索并选择“资源组”  。 还可以在“主页”上选择“资源组” 。 

   ![资源组导航](./media/get-started-create-service-instance/00-DeleteResource-01.png)

1. 在“资源组”页上，选择相应资源组  。

   ![资源组导航](./media/get-started-create-service-instance/00-DeleteResource-02.png)

1. 在资源组页上，选择“删除资源组”  。 
   
1. 键入资源组的名称，然后选择“删除”  。

   ![删除资源组](./media/get-started-create-service-instance/00-DeleteResource-03.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入和发布第一个 API](import-and-publish.md)
