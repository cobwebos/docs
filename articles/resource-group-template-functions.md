<properties
   pageTitle="Azure 资源管理器模板函数"
   description="介绍可在 Azure 资源管理器模板中用来将应用程序部署到 Azure 的函数。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.date="04/28/2015"
   wacn.date=""/>

# Azure 资源管理器模板函数

本主题介绍你可以在 Azure 资源管理器模板中使用的所有函数。

## base64

**base64 (inputString)**

返回输入字符串的 base64 表示形式。

| 参数 | 必选 | 说明
| :--------------------------------: | :------: | :----------
| inputString | 是 | 要以 base64 表示形式返回的字符串值。

以下示例演示了如何使用 base64 函数。

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat (arg1, arg2, arg3, ...)**

组合多个字符串值，并返回生成的字符串值。此函数可以结合任意数目的参数。

以下示例演示了如何组合多个值以返回一个值。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys (resourceName 或 resourceIdentifier, [apiVersion])**

返回存储帐户的密钥。可以使用 [resourceId 函数](#resourceid)或使用格式 **providerNamespace/resourceType/resourceName** 指定 resourceId。可以使用该函数来获取 primaryKey 和 secondaryKey。

| 参数 | 必选 | 说明
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier | 是 | 存储帐户的唯一标识符。
| apiVersion | 是 | 资源运行时状态的 API 版本。

以下示例演示了如何从 outputs 节中的存储帐户返回密钥。

    "outputs": {
      "exampleOutput": {
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
        "type" : "object"
      }
    }

## 参数

**parameters (parameterName)**

返回一个参数值。指定的参数名称必须已在模板的 parameters 节中定义。

| 参数 | 必选 | 说明
| :--------------------------------: | :------: | :----------
| parameterName | 是 | 要返回的参数名称。

以下示例演示了 parameters 函数的简化用法。

    "parameters": {
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## provider

**provider (providerNamespace, [resourceType])**

返回有关资源提供程序及其支持的资源类型的信息。如果未提供类型，则会返回所有支持的类型。

| 参数 | 必选 | 说明
| :--------------------------------: | :------: | :----------
| providerNamespace | 是 | 提供程序的命名空间
| resourceType | 否 | 指定的命名空间中的资源类型。

将使用以下格式返回支持的每个类型：

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

以下示例演示了如何使用 provider 函数：

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## reference

**reference (resourceName 或 resourceIdentifier, [apiVersion])**

使表达式能够从另一个资源的运行时状态派生其值。

| 参数 | 必选 | 说明
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier | 是 | 资源的名称或唯一标识符。
| apiVersion | 否 | 资源运行时状态的 API 版本。如果在相同的模板内未设置资源，则应使用参数。

**reference** 函数从运行时状态派生其值，因此不能在 variables 节中使用。可以在模板的 outputs 节中使用它。

如果在相同的模板内设置了引用的资源，则可使用 reference 表达式来声明一个资源依赖于另一个资源。

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup()**

返回表示当前资源组的结构化对象。该对象的格式如下：

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

以下示例使用资源组位置来分配网站的位置。

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

返回资源的唯一标识符。如果资源名称不确定或未设置在相同的模板内，请使用此函数。将使用以下格式返回标识符：

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

| 参数 | 必选 | 说明
| :---------------: | :------: | :----------
| resourceGroupName | 否 | 可选的资源组名称。默认值为当前资源组。如果要检索另一个资源组中的资源，请指定此值。
| resourceType | 是 | 资源类型，包括资源提供程序命名空间。
| resourceName1 | 是 | 资源的名称。
| resourceName2 | 否 | 下一个资源名称段（如果资源是嵌套的）。

以下示例演示了如何检索网站和数据库的资源 ID。网站存在于名为 **myWebsitesGroup** 的资源组中，而数据库存在于此模板的当前资源组中。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]

通常，在替代资源组中使用存储帐户或虚拟网络时，需要使用此函数。存储帐户或虚拟网络可能用于多个资源组中；因此，你不想要在删除单个资源组时删除它们。以下示例演示了如何轻松使用外部资源组中的资源：

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }


## 订阅

**subscription()**

将使用以下格式返回有关订阅的详细信息。

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

以下示例演示了在 outputs 节中调用的 subscription 函数。

    "outputs": {
      "exampleOutput": {
          "value": "[subscription()]",
          "type" : "object"
      }
    }

## variables

**variables (variableName)**

返回变量的值。指定的变量名称必须已在模板的 variables 节中定义。

| 参数 | 必选 | 说明
| :--------------------------------: | :------: | :----------
| variableName | 是 | 要返回的变量名称。


## 后续步骤
- [创作 Azure 资源管理器模板](resource-group-authoring-templates)
- [高级模板操作](resource-group-advanced-template)
- [使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy)
- [Azure 资源管理器概述](resource-group-overview)

<!---HONumber=61-->