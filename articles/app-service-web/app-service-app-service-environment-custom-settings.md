<properties
	pageTitle="Azure 环境的自定义设置"
	description="Azure 环境的自定义配置设置"
	services="app-service"
	documentationCenter=""
	authors="stefsch"
	manager="nirma"
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="04/08/2016"
	wacn.date=""/>

# Azure 环境的自定义配置设置

## 概述 ##
因为 Azure 环境对单个客户是隔离的，所以有一些专门应用于 Azure 环境的配置设置。本文介绍各种可用于 Azure 环境的特定自定义项。

你可以使用新 **clusterSettings** 属性中的数组存储 Azure 环境自定义项。此属性位于 hostingEnvironments Azure Resource Manager 实体的“属性”字典中。

下列缩写的 Resource Manager 模板代码段显示了 **clusterSettings** 属性：


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

**clusterSettings** 属性可以包含在 Resource Manager 模板中，以更新 Azure 环境。

## 使用 Azure 资源浏览器更新 Azure 环境
或者，你可以使用 [Azure 资源浏览器](https://resources.azure.com)更新 Azure 环境。

1. 在资源浏览器中，转到 Azure 环境的节点（“订阅”>“resourceGroups”>“提供程序”>“Micrososft.Web”>“hostingEnvironments”）。然后单击你要更新的特定 Azure 环境。

2. 在右窗格中，单击上方工具栏中的“读/写”，允许在资源浏览器中进行交互式编辑。

3. 单击蓝色的“编辑”按钮，使 Resource Manager 模板可供编辑。

4. 滚动到右窗格底部。**clusterSettings** 属性位于最底部，你可以在此输入或更新其值。

5. 键入（或复制并粘贴）你希望在 **clusterSettings** 属性中出现的配置值数组。

6. 单击右窗格顶部的绿色“PUT”按钮，以提交对 Azure 环境的更改。

不过，提交更改后，约需 30 分钟乘以 Azure 环境中前端数量的时间，更改才会生效。
例如，如果 Azure 环境有四个前端，大约需要两个小时才能完成配置更新。实行配置更改时，就无法在 Azure 环境中进行其他缩放操作或配置更改操作。

## 禁用 TLS 1.0 ##
客户的常见问题是，尤其是处理 PCI 合规性审核的客户，如何显式禁用其应用的 TLS 1.0。

通过下列 **clusterSettings** 条目可以禁用 TLS 1.0：

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## 入门
Azure 快速入门 Resource Manager 模板站点包含具有[创建 Azure 环境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)基本定义的模板。


<!-- LINKS -->

<!-- IMAGES -->

<!---HONumber=Mooncake_0509_2016-->