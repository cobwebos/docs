---
title: 使用 Azure API 管理为函数创建 OpenAPI 定义
description: 创建一个 OpenAPI 定义，使其他应用和服务可以在 Azure 中调用函数。
services: functions
keywords: OpenAPI, Swagger, 云服务, 云应用,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3ad304bc8f038d4009352dae72d70079828c26ba
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141559"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>使用 Azure API 管理为函数创建 OpenAPI 定义

通常使用 OpenAPI 定义描述 REST API。 此定义中包含的信息涉及 API 中哪些操作可用，以及 API 的请求和响应数据应采用怎样的结构。

本教程将创建确定风力涡轮机上的紧急修复是否经济高效的函数。 然后使用 [Azure API 管理](../api-management/api-management-key-concepts.md)为该函数应用创建一个 OpenAPI 定义，使该函数可使用其他应用和服务进行调用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建一个函数
> * 使用 Azure API 管理生成 OpenAPI 定义
> * 通过调用函数测试定义

## <a name="create-a-function-app"></a>创建函数应用

必须使用 Function App 托管函数的执行。 函数应用可将函数分组为逻辑单元，以便更轻松地管理、部署、缩放和共享资源。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>创建函数

本教程使用 HTTP 触发的函数，该函数采用两个参数：

* 预计进行涡轮机修复的时间，以小时为单位。
* 涡轮机的容量，以千瓦为单位。 

然后函数计算修复的费用和涡轮机 24 小时可以产生的收入。 在 [Azure 门户](https://portal.azure.com)中创建 HTTP 触发的函数。

1. 展开 Function App，选择“Functions”旁边的 + 按钮。 选择“门户中” > “继续”。

1. 选择“更多模板...”，然后选择“完成并查看模板”

1. 选择 HTTP 触发器，键入 `TurbineRepair` 作为函数**名称**，选择 `Function` 作为[身份验证级别](functions-bindings-http-webhook.md#http-auth)，然后选择“创建”。  

    ![创建用于 OpenAPI 的 HTTP 函数](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. 将 run.csx C# 脚本文件的内容替换为以下代码，然后选择“保存”：

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    此函数代码返回 `Yes` 或 `No` 的消息，指示紧急修复是否经济高效以及涡轮机产生的收入机会和修复涡轮机的费用。

1. 若要测试该函数，请单击最右边的“测试”展开“测试”选项卡。在“请求正文”中输入以下值，然后单击“运行”。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![在 Azure 门户中测试函数](media/functions-openapi-definition/test-function.png)

    响应正文中将返回以下值。

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

现在已经有了确定紧急修复是否经济高效的函数。 接下来，为该函数应用生成 OpenAPI 定义。

## <a name="generate-the-openapi-definition"></a>生成 OpenAPI 定义

现在即可生成 OpenAPI 定义。

1. 依次选择函数应用、“平台功能”、“所有设置”

    ![在 Azure 门户中测试函数](media/functions-openapi-definition/select-all-settings-openapi.png)

1. 向下滚动，然后选择“API 管理” > “新建”，创建新的 API 管理实例。

    ![链接函数](media/functions-openapi-definition/link-apim-openapi.png)

1. 使用图像下的表中指定的 API 管理设置。

    ![创建新的 API 管理服务](media/functions-openapi-definition/new-apim-service-openapi.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 全局唯一名称 | 根据函数应用的名称生成一个名称。 |
    | **订阅** | 订阅 | 在其下创建此新资源的订阅。 |  
    | [资源组](../azure-resource-manager/resource-group-overview.md) |  myResourceGroup | 与函数应用相同的资源，系统会为你设置。 |
    | **位置** | 美国西部 | 选择位置“美国西部” |
    | 组织名称 | Contoso | 在开发人员门户中使用的组织名称，也用于电子邮件通知。 |
    | **管理员电子邮件** | 你的电子邮件 | 从 API 管理接收了系统通知的电子邮件。 |
    | **定价层** | 消耗（预览） | 如需完整的定价详细信息，请参阅 [API 管理定价页](https://azure.microsoft.com/pricing/details/api-management/) |
    | **Application Insights** | 你的实例 | 使用函数应用所使用的 Application Insights。 |

1. 选择“创建”以创建 API 管理实例，这可能需要几分钟的时间。

1. 选择“启用 Application Insights”，将日志发送到函数应用所在的位置，然后接受余下的默认设置并选择“链接 API”。

1. 此时会打开“导入 Azure Functions”，其中突出显示了“TurbineRepair”函数。 选择“选择”，继续操作。

    ![将 Azure Functions 导入 API 管理](media/functions-openapi-definition/import-function-openapi.png)

1. 在“从 Function App 创建”页中接受默认设置，然后选择“创建”

    ![从 Function App 创建](media/functions-openapi-definition/create-function-openapi.png)

现已为函数创建 API。

## <a name="test-the-openapi-definition"></a>测试 OpenAPI 定义

在使用 API 定义之前，应验证它是否可以使用。

1. 在函数的“测试”选项卡上，选择 **POST** 操作

1. 输入“小时”和“容量”的值

```json
{
"hours": "6",
"capacity": "2500"
}
```

1. 单击“发送”，然后查看 HTTP 响应。

    ![测试函数 API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建一个函数
> * 使用 Azure API 管理生成 OpenAPI 定义
> * 通过调用函数测试定义

转到下一主题，了解 API 管理。

> [!div class="nextstepaction"]
> [API 管理](../api-management/api-management-key-concepts.md)
