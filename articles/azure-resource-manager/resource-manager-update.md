---
title: "Azure Resource Manager 模板 - 更新资源 | Microsoft Docs"
description: "介绍了如何扩展 Azure Resource Manager 模板的功能来更新资源"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>用于扩展 Azure Resource Manager 模板功能的模式 - 更新资源

在某些情况下，在部署过程中你需要更新资源。 如果在创建某个资源依赖的其他资源之前无法指定该资源的所有属性，则会遇到此情况。 例如，如果你为负载均衡器创建后端池，则可以更新虚拟机 (VM) 上的网络接口 (NIC) 来将其包括在后端池中。 Resource Manager 支持在部署期间更新资源，但你必须正确设计模板以避免错误并确保将部署作为更新进行处理。

## <a name="understand-the-pattern"></a>了解模式

首先，你必须在模板中将资源引用一次来创建资源，但之后必须通过同一名称引用资源来对其进行更新。 不过，如果两个资源在模板中具有相同的名称，则 Resource Manager 将引发异常。 若要避免此错误，请在所链接或包括为子模板的另一个模板中使用 `Microsoft.Resources/deployments` 资源类型指定更新的资源。

其次，在第二个模板中，你必须指定要更改的现有属性的名称或者要添加的属性的新名称。 然后，还必须指定原始属性及其原始值。 如果没有提供原始属性和值，则 Resource Manager 会假定你要创建新资源，并且会删除原始资源。 它将使用仅包括你指定的新属性的新资源替换原始资源。

最后，你必须使资源依赖于你要部署的所有相关资源。 此依赖关系可确保以正确的顺序创建资源。 此顺序为：

1. 创建资源
2. 创建所依赖的资源
3. 使用所依赖的资源中的值（第 2 步中的）更新资源（第 1 步中的）

## <a name="example-template"></a>示例模板

以下示例模板演示了此模式。 它将部署名为 `firstVNet` 的虚拟网络 (VNet)，该虚拟网络具有一个名为 `firstSubnet` 的子网。 然后，它将部署一个名为 `nic1` 的虚拟网络接口 (NIC) 并将其与该子网相关联。 然后，一个名为 `updateVNet` 的部署资源包括引用了 `firstVNet` 资源的名称的嵌套模板。 

看一下此资源的 `addressSpace` 属性和 `subnets` 属性。 注意，`addressSpace` 值设置为 `firstVNet` 资源部署对象上的同一属性值。 在 `subnets` 数组中，`firstSubnet` 的值以类似方式设置。 因为已指定了所有原始 `firstVNet` 属性，所以 Resource Manager 会在 Azure 中更新该资源。 在本例中，更新操作是添加名为 `secondSubnet` 的新子网。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>尝试模板

如果要试验此模板，请按照下列步骤进行操作：

1.    转到 Azure 门户，选择“+”图标，然后搜索“模板部署”资源类型。 在搜索结果中找到后，请选择它。
2.    转到“模板部署”页后，选择“创建”按钮，这将打开“自定义部署”边栏选项卡。
3.    选择“编辑”图标。
4.    选择空模板。
5.    将前面的示例模板复制粘贴到右侧窗格。
6.    选择“保存”按钮。
7.    你将返回到“自定义部署”窗格，但是这一次窗格中会包含一些下拉框。 选择你的订阅，或者创建新订阅或使用现有资源组，然后选择位置。 查看条款和条件，然后选择“我同意”按钮。
8.    选择“购买”按钮。

在部署完成后，打开你在门户中指定的资源组。 你会看到一个名为 `firstVNet` 的 VNet 和一个名为 `nic1` 的 NIC。 单击 `firstVNet`，然后单击 `subnets`。 你会看到原来创建的 `firstSubnet`，你还会看到已在 `updateVNet` 资源中添加的 `secondSubnet`。 

![原始子网和更新后的子网](./media/resource-manager-update/firstVNet-subnets.png)

然后，返回到资源组并单击 `nic1`，然后单击 `IP configurations`。 在 `IP configurations` 部分中，`Subnet` 设置为 `firstSubnet (10.0.0.0/24)`。 

![nic1 IP 配置设置](./media/resource-manager-update/nic1-ipconfigurations.png)

原始 `firstVNet` 已更新而非重新创建。 如果 `firstVNet` 是重新创建的，则 `nic1` 不会与 `firstVNet` 相关联。

## <a name="next-steps"></a>后续步骤

可以在模板中使用此模式重新指定你要更新的资源的原始属性。 在所链接或嵌套的模板中使用 `Microsoft.Resources/deployments` 资源类型指定更新资源。

* 有关 `reference()` 函数的简介，请参阅 [Azure Resource Manager 模板函数](resource-group-template-functions.md)。
* 此模式还可在[模板构建块项目](https://github.com/mspnp/template-building-blocks)和 [Azure 参考体系结构](/azure/architecture/reference-architectures/)中实现。
