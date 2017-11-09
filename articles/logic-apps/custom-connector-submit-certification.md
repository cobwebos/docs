---
title: "认证自定义连接器 - Azure 逻辑应用 | Microsoft Docs"
description: "使连接器可供 Azure 逻辑应用、Microsoft Flow 和 Microsoft PowerApps 中的所有用户使用"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>提交连接器以进行 Microsoft 认证

若要使 Azure 逻辑应用、Microsoft Flow 和 Microsoft PowerApps 中的所有用户可以公开使用自定义连接器，请将连接器提交到 Microsoft 进行评审、验证和审批，以便发布。 

## <a name="certification-criteria"></a>认证条件

| 功能 | 详细信息 | 必需还是建议 |
|------------|---------|-------------------------|
| 软件即服务 (SaaS) 应用 | 满足可在逻辑应用、Flow 和 PowerApps 中实施的用户方案。 | 必选 |
| 身份验证类型 | API 必须支持 OAuth2、API 密钥或基本身份验证。 | 必选 | 
| 支持 | 必须提供支持联系方法，使客户能够获取帮助。 | 必选 | 
| 可用性和正常运行时间 | 应用至少提供 99.9% 的正常运行时间。 | 建议 | 
|||| 

此外，如果你不是 Microsoft 合作伙伴或独立软件供应商 (ISV)，并想要认证和公开发布连接器，则必须具有基础服务或现有的显式权限才能使用 API。

## <a name="validation-phases"></a>验证阶段

Microsoft 使用以下验证阶段来评估连接器：

| 验证阶段 | 说明 | 
| ----- | ----------- |
| 功能 | Microsoft 使用逻辑应用、Flow 和 PowerApps 来测试连接器是否出现以下错误： <p>- 自定义连接器向导的“定义”部分中显示的 OpenAPI (Swagger) 无效错误或 JSON 错误 <p>- 自定义连接器向导的“测试”部分中显示的运行时和架构验证错误 | 
| 内容 | Microsoft 检查连接器是否为每个实体使用友好名称和说明。 确保连接器的 Swagger 中的每个操作、输入参数和响应属性包含以下元素： <p>- [摘要](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [说明](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [可见性信息](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>指定连接器并将其提交到 Microsoft 进行认证

若要申请认证，请遵循以下步骤：

1. **指定**

   1. [提交指定的连接器](https://go.microsoft.com/fwlink/?linkid=848754)。

      1. 在页面底部，选择“联系我们”。
      2. 填写表单，并选择“有关 ISV 连接器程序和联合营销的问题”。
      3. 选择“提交”。

   2. 签署收到的“相互保密协议”和“合作伙伴协议”。 

      Microsoft 只有在收到这些签署的合约之后才会继续下一阶段。 
      然后，我们可以检查连接器是否满足认证条件。 
   
   3. 如果连接器获批准，Microsoft 会告知登记说明。
    
2. **评审**

   1. 请将以下信息发送到指派联系人进行评审：

      * 用于描述 API 的 OpenAPI 文件
      * 显示连接器的图标文件（.png 或 .jpg） 
      
        图标应在 230 像素的方块中包含一个大约 160 像素的徽标。 
        最好在彩色背景上设计白色徽标。
      
      * 采用十六进制格式的图标品牌色，应与图标文件的彩色背景匹配

      * 用于验证的测试帐户
      * 支持联系人

   2. 如果我们需要更多信息，会与你取得联系。

3. **发布**

    验证连接器的功能和内容后，我们会暂存该连接器，等待在所有产品和区域中部署。
    
    所有连接器默认发布为“高级”连接器。 
    如果在 Azure 中生成了应用，可以申请将连接器列作可供拥有 Office 365 企业版计划的所有用户使用的“标准”连接器。 
    有关更多详细信息，请咨询指派联系人。

## <a name="next-steps"></a>后续步骤

* [自定义连接器常见问题解答](../logic-apps/custom-connector-faq.md)
* [自定义连接器概述](../logic-apps/custom-connector-overview.md)