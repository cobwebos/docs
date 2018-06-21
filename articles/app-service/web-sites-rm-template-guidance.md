---
title: 使用模板部署 Azure Web 应用的指南 | Microsoft Docs
description: 有关创建 Azure 资源管理器模板来部署 Web 应用的建议。
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: 8c29cf5a65e9587b281a6000b5b4eff47f11da91
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807316"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板部署 Web 应用的指南

本文提供创建 Azure 资源管理器模板以部署 Azure 应用服务解决方案的建议。 这些建议可以帮助你避免常见问题。

## <a name="define-dependencies"></a>定义依赖项

为 Web 应用定义依赖项需要了解 Web 应用中的资源如何进行交互。 如果以错误的顺序指定依赖项，则可能会导致部署错误或者创建造成停止部署的争用条件。

> [!WARNING]
> 如果在模板中包括了 MSDeploy 站点扩展，则必须将任何配置资源设置为依赖于 MSDeploy 资源。 配置更改会导致站点以异步方式重新启动。 通过使配置资源依赖于 MSDeploy，可确保 MSDeploy 在站点重新启动前完成。 如果没有这些依赖关系，则站点可能会在 MSDeploy 的部署过程中重新启动。 有关示例模板，请参阅[具有 Web 部署依赖项的 WordPress 模板](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)。

下图显示了各种应用服务资源的依赖顺序：

![Web 应用依赖项](media/web-sites-rm-template-guidance/web-dependencies.png)

请按以下顺序部署资源：

**第 1 层**
* 应用服务计划。
* 任何其他相关资源，例如数据库或存储帐户。

**第 2 层**
* Web 应用 - 依赖于应用服务计划。
* 以服务器场为目标的 Azure Application Insights 实例 - 依赖于应用服务计划。

**第 3 层**
* 源代码管理 - 依赖于 Web 应用。
* MSDeploy 站点扩展 - 依赖于 Web 应用。
* 以服务器场为目标的 Application Insights 实例 - 依赖于 Web 应用。

**第 4 层**
* 应用服务证书 - 依赖于存在的源代码管理或 MSDeploy； 若都不存在，则依赖于 Web 应用。
* 配置设置（连接字符串、web.config 值、应用设置）- 依赖于存在的源代码管理或 MSDeploy； 若都不存在，则依赖于 Web 应用。

**第 5 层**
* 主机名绑定 - 依赖于如果存在的证书； 若不存在，则依赖于较高级别的资源。
* 站点扩展 - 依赖于存在的配置设置； 若不存在，则依赖于较高级别的资源。

通常，你的解决方案仅包括这些资源和层中的一部分。 对于缺少的层，请将较低的资源映射到下一个较高的层。

下面的示例显示了模板的一部分。 连接字符串配置值依赖于 MSDeploy 扩展。 MSDeploy 扩展依赖于 Web 应用和数据库。 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

有关使用上述代码的现成示例，请参阅[模板：构建简单的 Umbraco Web 应用](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)。

## <a name="find-information-about-msdeploy-errors"></a>查找有关 MSDeploy 错误的信息

如果你的资源管理器模板使用了 MSDeploy，则部署错误消息可能难以理解。 若要在部署失败后获取更多信息，请尝试以下步骤：

1. 转到站点的 [Kudu 控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)。
2. 浏览到 D:\home\LogFiles\SiteExtensions\MSDeploy 上的文件夹。
3. 查找 appManagerStatus.xml 和 appManagerLog.xml 文件。 第一个文件记录了状态。 第二个文件记录了有关错误的信息。 如果你不明白该错误，可以将它发布到论坛上来寻求帮助。

## <a name="choose-a-unique-web-app-name"></a>选择唯一的 Web 应用名称

Web 应用的名称必须全局唯一。 可以使用某个可能唯一的命名约定，也可以使用 [uniqueString 函数](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring)来帮助生成唯一名称。

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>后续步骤

* 有关使用模板部署 Web 应用的教程，请参阅[按可预见的方式在 Azure 中预配和部署微服务](app-service-deploy-complex-application-predictably.md)。