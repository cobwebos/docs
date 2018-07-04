## <a name="export-an-api-definition"></a>导出 API 定义
[为函数创建 OpenAPI 定义](../articles/azure-functions/functions-openapi-definition.md)中已为函数创建了一个 OpenAPI 定义。 此过程的下一步是导出 API 定义，以便 PowerApps 和 Microsoft Flow 可以在自定义 API 中使用它。

> [!IMPORTANT]
> 请记住，登录到 Azure 所用的凭据必须与 PowerApps 和 Microsoft Flow 租户使用的凭据相同。 这样 Azure 才能创建自定义 API，并将其提供给 PowerApps 和 Microsoft Flow。

1. 在 [Azure 门户](https://portal.azure.com)中，单击函数应用名称（如“function-demo-energy”）>“平台功能” > API 定义”。

    ![API 定义](media/functions-export-api-definition/api-definition.png)

1. 单击“导出到 PowerApps + Flow”。

    ![API 定义源](media/functions-export-api-definition/export-api-1.png)

1. 在右侧窗格中，使用表中指定的设置。

    |设置|说明|
    |--------|------------|
    |**导出模式**|选择“快速”自动生成自定义 API。 选择“手动”导出 API 定义，但之后必选手动将其导入到 PowerApps 和 Microsoft Flow。 有关详细信息，请参阅[导出到 PowerApps 和 Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md)。|
    |**环境**|选择自定义 API 应保存到的环境。 有关详细信息，请参阅[环境概述 (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) 或[环境概述 (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/)。|
    |**自定义 API 名称**|输入名称（如 `Turbine Repair`）。|
    |**API 密钥名称**|输入应用和流生成者将在自定义 API UI 中看到的名称。 请注意，该示例包含有用信息。|
 
    ![导出到 PowerApps 和 Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. 单击“确定”。 现在自定义 API 已生成并添加到了指定的环境中。