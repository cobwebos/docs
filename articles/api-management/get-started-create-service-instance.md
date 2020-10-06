---
title: 快速入门 - 创建 Azure API 管理实例
description: 使用 Azure 门户新建 Azure API 管理服务实例。
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708200"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户新建 Azure API 管理服务实例

Azure API 管理 (APIM) 可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。 API 管理通过开发人员参与、商业洞察力、分析、安全性和保护提供了核心竞争力以确保成功的 API 程序。 使用 APIM 可以为在任何位置托管的现有后端服务创建和管理新式 API 网关。 有关详细信息，请参阅[概述](api-management-key-concepts.md)。

本快速入门介绍如何使用 Azure 门户创建新的 API 管理实例的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

API 管理实例

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-new-service"></a>创建新服务

1. 在 Azure 门户菜单中，选择“创建资源”****。 还可以在 Azure“主页”上选择“创建资源”********。 
   
   选择“创建资源”:::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="":::

   
1. 在“新建”页上，选择“集成” > “API 管理”。

   新建 Azure API 管理实例
   
1. 在“API 管理服务”页中，输入设置。

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="新建实例":::
   
   | 设置                 | DESCRIPTION   |                                                                     
   |-------------------------|-----------------------------------------------|
   | 名称 - **                | API 管理服务的唯一名称。 以后无法更改此名称。 服务名称引用服务和相应的 Azure 资源。 <br/> 服务名称用于生成默认域名：\<name\>.azure-api.net。 如果希望使用自定义域名，请参阅[配置自定义域名](configure-custom-domain.md)。 |
   | 订阅****：          | 要在其下创建此新服务实例的订阅。   |
   | **资源组**      |  选择新的或现有的资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 |
   | **位置**          | 从可用的 API 管理服务位置选择附近的地理区域。 | 
   | **** 组织名称   | 组织的名称。 许多位置，包括开发人员门户和通知电子邮件的发件人的标题中使用此名称。 |                                                         
   | **管理员电子邮件** | 来自“API 管理”的所有通知将发送到的电子邮件地址。   |  
   | **定价层**        | 选择“开发人员”层来评估服务。 此层不用于生产。 有关对 API 管理层进行缩放的详细信息，请参阅[升级和缩放](upgrade-and-scale.md)。 |

3. 选择“创建”****。

    > [!TIP]
    > 在此层中创建和激活 API 管理服务可能需要 30 到 40 分钟。 选择“固定到仪表板”可以更容易地查找新创建的服务。****

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

在“概览”页上查看服务的属性。

   API 管理实例

当 API 管理服务实例处于联机状态时，便可以使用它了。 从[导入并发布第一个 API](import-and-publish.md) 教程开始。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和所有相关资源，可以使用以下步骤将其删除：

1. 在 Azure 门户中，搜索并选择“资源组”****。 还可以在“主页”上选择“资源组”********。 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="新建实例":::

1. 在“资源组”页上，选择相应资源组****。

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="新建实例":::

1. 在资源组页上，选择“删除资源组”****。 
   
1. 键入资源组的名称，然后选择“删除”****。

   删除资源组

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入和发布第一个 API](import-and-publish.md)
