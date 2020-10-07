---
title: 使用 Azure 资源管理器模板创建应用服务应用
description: 使用 Azure 资源管理器模板在几秒钟时间内在 Azure 应用服务中创建你的第一个应用，这是将应用部署到应用服务的许多方式之一。
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653242"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建应用服务应用

通过使用 Azure 资源管理器模板和 Cloud Shell 中的 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 将应用部署到云，以便开始使用 [Azure 应用服务](overview.md)。 由于使用的是免费应用服务层，因此完成本快速入门不会产生费用。

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>创建 Azure 应用服务应用

### <a name="review-the-template"></a>查看模板

::: zone pivot="platform-windows"
本快速入门中使用的模板来自 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)。 它在 Windows 上部署应用服务计划和应用服务应用。 它与 .NET Core、.NET Framework、PHP、Node.js 和静态 HTML 应用兼容。 对于 Java，请参阅[创建 Java 应用](app-service-web-get-started-java.md)。 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

该模板中定义了两个 Azure 资源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：创建应用服务计划。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：创建应用服务应用。

为了便于你使用，此模板包含多个预定义的参数。 有关参数默认值及其说明，请参阅下表：

| 参数 | 类型    | 默认值                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | 字符串  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | 应用程序名称 |
| location   | 字符串  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | 应用区域 |
| sku        | 字符串  | "F1"                         | 实例大小（F1 = 免费层） |
| 语言   | 字符串  | ".net"                       | 编程语言堆栈（.net、php、node、html） |
| helloWorld | boolean | False                        | True = 部署“Hello World”应用 |
| repoUrl    | 字符串  |                             | 外部 Git 存储库（可选） |
::: zone-end
::: zone pivot="platform-linux"
本快速入门中使用的模板来自 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)。 它在 Linux 上部署应用服务计划和应用服务应用。 它与应用服务上所有受支持的编程语言兼容。

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

该模板中定义了两个 Azure 资源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：创建应用服务计划。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：创建应用服务应用。

为了便于你使用，此模板包含多个预定义的参数。 有关参数默认值及其说明，请参阅下表：

| 参数 | 类型    | 默认值                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | 字符串  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | 应用程序名称 |
| location   | 字符串  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | 应用区域 |
| sku        | 字符串  | "F1"                         | 实例大小（F1 = 免费层） |
| linuxFxVersion   | 字符串  | "DOTNETCORE&#124;3.0        | "编程语言堆栈 &#124; 版本" |
| repoUrl    | 字符串  |                             | 外部 Git 存储库（可选） |

---
::: zone-end


### <a name="deploy-the-template"></a>部署模板

此处使用 Azure CLI 来部署模板。 还可以使用 Azure 门户、Azure PowerShell 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-powershell.md)。 

以下代码将创建一个资源组、一个应用服务计划和一个 Web 应用。 已为你设置了默认资源组、应用服务计划和位置。 请将 `<app-name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。

::: zone pivot="platform-windows"
运行以下代码，在 Windows 上部署 .NET Framework 应用。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

若要部署其他语言堆栈，请使用相应的值更新 `linuxFxVersion`。 下面显示了示例。 若要显示当前版本，请在 Cloud Shell 中运行以下命令：`az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| 语言    | 示例：                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> 可以[在此处找到更多 Azure 应用服务模板示例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)。


## <a name="validate-the-deployment"></a>验证部署

浏览到 `http://<app_name>.azurewebsites.net/` 并验证它是否已创建。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，[可将其删除](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>从本地 Git 进行部署

> [!div class="nextstepaction"]
>将 ASP.NET Core 与 SQL 数据库配合使用

> [!div class="nextstepaction"]
>将 Python 与 Postgres 配合使用

> [!div class="nextstepaction"]
> [带 MySQL 的 PHP](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure SQL 数据库](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
>映射自定义域