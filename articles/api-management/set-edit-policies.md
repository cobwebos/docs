---
title: 如何设置或编辑 Azure API 管理策略 | Microsoft Docs
description: 本主题介绍如何设置或编辑 Azure API 管理策略。
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: aaf86a440328e27c8c47b809536951eeaf2104b9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936691"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>如何设置或编辑 Azure API 管理策略

策略定义是一个 XML 文档，用于描述一个入站和出站语句序列。 可以直接在定义窗口中编辑 XML。 也可以从策略窗口右侧提供的列表中选择预定义策略。 适用于当前范围的语句已启用并已突出显示。 单击启用的语句会在定义视图中的光标位置添加相应的 XML。 

有关策略的详细信息，请参阅 [Azure API 管理中的策略](api-management-howto-policies.md)。

## <a name="set-or-edit-a-policy"></a>设置或编辑策略

若要设置或编辑策略，请执行以下步骤：

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 浏览到自己的 APIM 实例。
3. 单击“API”选项卡。
4. 选择以前导入的某个 API。
5. 选择“设计”选项卡。
6. 选择要将该策略应用到的操作。 如果想要将策略应用到所有操作，请选择“所有操作”。
7. 单击“入站”或“出站”铅笔图标旁边的三角形。
8. 选择“代码编辑器”项。

    ![编辑策略](./media/set-edit-policies/set-edit-policies01.png)

9. 将所需的策略代码粘贴到某个相应的块中。
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>配置范围

可在全局范围或者在“产品”、“API”或“操作”范围内配置策略。 若要开始配置策略，必须首先选择该策略将应用的范围。

策略范围按以下顺序计算：

1. 全局范围
2. 产品范围
3. API 范围
4. 操作范围

策略中的语句按 `base` 元素（如果存在）的位置计算。 全局策略没有父策略，因此在其中使用 `<base>` 元素无效。

若要在策略编辑器中查看当前范围的策略，请单击“重新计算所选范围的有效策略”。

### <a name="global-scope"></a>全局范围

全局范围是针对 APIM 实例中的**所有 API** 配置的。

1. 登录到 [Azure 门户](https://portal.azure.com/)，并导航到 APIM 实例。
2. 单击“所有 API”。

    ![全局范围](./media/api-management-howto-policies/global-scope.png)

3. 单击三角形图标。
4. 选择“代码编辑器”。
5. 添加或编辑策略。
6. 按“保存”。 

    所做的更改会立即传播到 API 管理网关。

### <a name="product-scope"></a>产品范围

产品范围是针对选定的产品配置的。

1. 单击“产品”。

    ![产品范围](./media/api-management-howto-policies/product-scope.png)

2. 选择要将策略应用到的产品。
3. 单击“策略”。
4. 添加或编辑策略。
5. 按“保存”。 

### <a name="api-scope"></a>API 范围

API 范围是针对选定 API 的**所有操作**配置的。

1. 选择要将策略应用到的 **API**。

    ![API 范围](./media/api-management-howto-policies/api-scope.png)

2. 选择“所有操作”
3. 单击三角形图标。
4. 选择“代码编辑器”。
5. 添加或编辑策略。
6. 按“保存”。 

### <a name="operation-scope"></a>操作范围 

操作范围是针对选定的操作配置的。

1. 选择一个 **API**。
2. 选择要将策略应用到的操作。

    ![操作范围](./media/api-management-howto-policies/operation-scope.png)

3. 单击三角形图标。
4. 选择“代码编辑器”。
5. 添加或编辑策略。
6. 按“保存”。 

## <a name="next-steps"></a>后续步骤

请参阅以下相关主题：

+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)