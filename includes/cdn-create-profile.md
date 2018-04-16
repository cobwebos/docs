---
title: include 文件
description: include 文件
services: cdn
author: dksimpson
ms.service: cdn
ms.topic: include
ms.date: 04/04/2018
ms.author: rli; v-deasim
ms.custom: include file
ms.openlocfilehash: 692364e9b2e78b3bd1f63137148dfbc680364737
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
## <a name="create-a-new-cdn-profile"></a>创建新的 CDN 配置文件

CDN 配置文件是适用于 CDN 终结点的容器，用于指定定价层。

1. 在 Azure 门户的左上角，选择“创建资源”。
    
    此时会显示“新建”窗格。
   
2. 选择“Web + 移动”，然后选择“CDN”。
   
    ![选择 CDN 资源](./media/cdn-create-profile/cdn-new-resource.png)

    此时会显示“CDN 配置文件”窗格。

    使用图像后的表中指定的设置。
   
    ![新的 CDN 配置文件](./media/cdn-create-profile/cdn-new-profile.png)

    | 设置  | 值 |
    | -------- | ----- |
    | **Name** | 输入 *my-cdn-profile-123* 作为配置文件名称。 此名称必须全局唯一；如果此名称已在使用，则可输入其他名称。 |
    | **订阅** | 从下拉列表中选择一个 Azure 订阅。|
    | **资源组** | 选择“新建”，然后输入 *my-resource-group-123* 作为资源组名称。 此名称必须全局唯一；如果此名称已在使用，则可输入其他名称。 | 
    | **资源组位置** | 从下拉列表中选择“美国中部”。 |
    | **定价层** | 从下拉列表中选择“标准 Verizon”。 |
    | **立即创建新的 CDN 终结点** | 保持未选中状态。 |  
   
3. 选择“固定到仪表板”，以便在创建配置文件以后将其保存到仪表板。
    
4. 选择“创建”以创建该配置文件。 

