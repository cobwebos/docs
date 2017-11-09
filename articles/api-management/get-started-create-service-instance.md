---
title: "创建 Azure API 管理实例 | Microsoft Docs"
description: "按照本教程中的步骤创建新的 Azure API 管理实例。"
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>创建新的 Azure API 管理服务实例

本教程介绍了使用 [Azure 门户](https://portal.azure.com/)创建新的 API 管理实例的步骤。

## <a name="prerequisites"></a>先决条件

+ 一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>创建新服务

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“新建” > “企业集成” > “API 管理”。

    或者，选择“新建”，在搜索框中键入 `API management`，然后按 Enter。 单击“创建” 。

2. 在“API 管理服务”窗口中，为 API 管理服务输入一个唯一的名称。 以后无法更改此名称。

    > [!TIP]
    > 服务名称用来以 *{name}.azure-api.net* 形式生成默认域名。 如果希望使用自定义域名，请参阅[配置自定义域名](configure-custom-domain.md)。 <br/>
    > 服务名称用来引用服务和相应的 Azure 资源。

5. 在你有权访问的不同 Azure 订阅中选择一个订阅。
6. 在“资源组”中，选择新的或现有的资源。  资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../azure-resource-manager/resource-group-overview.md#resource-groups)了解更多信息。
7. 在“位置”中，选择在其中创建 API 管理服务的地理区域。 下拉列表中仅显示可用的 API 管理服务区域。 
9. 输入一个组织名称。 此名称在许多位置中使用。 例如，开发人员门户的标题和通知电子邮件的发件人。
10. 在“管理员电子邮件”中，设置要将来自 **API 管理**的所有通知发送到的电子邮件地址。
11. 在“定价层”中，设置“开发人员”层来评估服务。 此层不用于生产用途。 有关对 API 管理层进行缩放的详细信息，请参阅[升级和缩放](upgrade-and-scale.md)。
12. 选择“创建”。

    > [!TIP]
    > 创建 API 管理服务通常需要花费 20 到 30 分钟。 选择“固定到仪表板”可以更容易地查找新创建的服务。

## <a name="next-steps"></a>后续步骤

[使用 Azure API 管理发布 API](#api-management-getstarted-publish-api.md)