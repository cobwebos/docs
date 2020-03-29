---
title: 在 Azure 开发人员测试实验室中部署嵌套模板环境
description: 了解如何部署嵌套 Azure 资源管理器模板以使用 Azure 开发人员测试实验室提供环境。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168824"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>为测试环境部署嵌套 Azure 资源管理器模板
嵌套部署允许您从主资源管理器模板中执行其他 Azure 资源管理器模板。 它使您能够将部署分解为一组针对的目标和特定于目的的模板。 它在测试、重用和可读性方面提供了优势。 在[部署 Azure 资源时使用链接模板](../azure-resource-manager/templates/linked-templates.md)的文章提供了此解决方案的良好概述，并包含多个代码示例。 本文提供了特定于 Azure 开发人员测试实验室的示例。 

## <a name="key-parameters"></a>关键参数
虽然可以从头开始创建自己的资源管理器模板，但我们建议您在 Visual Studio 中使用[Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)，这样可以轻松地开发和调试模板。 将嵌套部署资源添加到 azuredeploy.json 时，Visual Studio 会添加多个项以使模板更加灵活。 这些项目包括包含辅助模板和参数文件的子文件夹、主模板文件中的变量名称以及新文件的存储位置的两个参数。 **_artifactsLocation**和 **_artifactsLocationSasToken**是 DevTest 实验室使用的关键参数。 

如果您不熟悉 DevTest Labs 如何使用环境，请参阅[使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 模板存储在链接到 DevTest 实验室实验室的存储库中。 使用这些模板创建新环境时，文件将移动到实验室中的 Azure 存储容器中。 为了能够识别和复制嵌套文件，DevTest Labs 标识_artifactsLocation，并_artifactsLocationSasToken参数并将子文件夹复制到存储容器。 然后，它会自动将位置和共享访问签名 （SaS） 令牌插入到参数中。 

## <a name="nested-deployment-example"></a>嵌套部署示例
下面是嵌套部署的简单示例：

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

存储库中包含此模板的文件夹有一个子文件夹`nestedtemplates`，其中包含文件**NestOne.json**和**NestOne.parameter.json**。 在**azuredeploy.json**中，模板的 URI 使用项目位置、嵌套模板文件夹、嵌套模板文件名生成。 同样，使用嵌套模板的项目位置、嵌套模板文件夹和参数文件构建参数的 URI。 

以下是 Visual Studio 中相同项目结构的图像： 

![Visual Studio 中的项目结构](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

您可以在主文件夹中添加其他文件夹，但不能比单个级别更深。 

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
- [在 Azure 开发人员测试实验室中将环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)