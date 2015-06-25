<properties
   pageTitle="Azure 资源管理器高级模板操作"
   description="介绍如何在向 Azure 部署应用程序时，使用 Azure 资源管理器模板中的嵌套模板和复制操作。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.date="04/28/2015"
   wacn.date=""/>

# 高级模板操作

本主题介绍当你在向 Azure 部署资源时，可以用来执行更高级任务的复制操作和嵌套模板。

## 复制

可让你在部署资源时循环访问数组并使用每个元素。

以下示例将部署名称分别为 examplecopy-Contoso、examplecopy-Fabrikam 和 examplecopy-Coho 的三个网站。

    "parameters": {
      "org": {
         "type": "array",
             "defaultValue": [
             "Contoso",
             "Fabrikam",
             "Coho"
          ]
      },
      "count": {
         "type": "int",
         "defaultValue": 3
      }
    },
    "resources": [
      {
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]",
          "type": "Microsoft.Web/sites",
          "location": "China East",
          "apiVersion": "2014-06-01",
          "copy": {
             "name": "websitescopy",
             "count": "[parameters('count')]"
          },
          "properties": {}
      }
    ]

## 嵌套模板

你有时可能需要合并两个模板，或者需要从父级启动子系模板。你可以通过使用主模板内的部署资源来部署子模板，以实现此目的。需要按如下所示提供嵌套模板的 URI。

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"},
    "resources": [
      {
         "apiVersion": "2015-01-01",
         "name": "nestedTemplate",
         "type": "Microsoft.Resources/deployments",
         "properties": {
           "mode": "incremental",
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"},
           "parameters": {
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"}
           }
         }
      }
    ]

## 后续步骤
- [创作 Azure 资源管理器模板](resource-group-authoring-templates)
- [Azure 资源管理器模板函数](resource-group-template-functions)
- [使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy)
- [Azure 资源管理器概述](resource-group-overview)

<!---HONumber=61-->