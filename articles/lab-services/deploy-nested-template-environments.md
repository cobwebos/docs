---
title: 在 Azure 开发测试实验室中部署嵌套模板环境
description: 了解如何部署嵌套的 Azure 资源管理器模板以提供 Azure 开发测试实验室环境。
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168824"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>部署用于测试环境的嵌套 Azure 资源管理器模板
嵌套部署允许您从主资源管理器模板内执行其他 Azure 资源管理器模板。 它使你能够将部署分解为一组目标和特定用途的模板。 它提供测试、重复使用和可读性方面的优势。 [部署 Azure 资源时使用链接模板](../azure-resource-manager/templates/linked-templates.md)一文提供了此解决方案的更好概述，其中包含多个代码示例。 本文提供了特定于 Azure 开发测试实验室的示例。 

## <a name="key-parameters"></a>关键参数
虽然你可以从头开始创建自己的资源管理器模板，但是我们建议你在 Visual Studio 中使用[Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)，这可以轻松地开发和调试模板。 将嵌套的部署资源添加到 azuredeploy.json 时，Visual Studio 会添加多个项，使模板更加灵活。 这些项包括具有辅助模板和参数文件的子文件夹、主模板文件中的变量名称，以及新文件的存储位置的两个参数。 **_ArtifactsLocation**和 **_ArtifactsLocationSasToken**是开发测试实验室使用的关键参数。 

如果你不熟悉开发测试实验室如何适用于环境，请参阅[使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 模板存储在链接到开发测试实验室中的实验室的存储库中。 使用这些模板创建新环境时，文件将移入实验室中的 Azure 存储容器。 为了能够识别和复制嵌套文件，开发测试实验室会标识 _artifactsLocation 和 _artifactsLocationSasToken 参数，并将子文件夹复制到存储容器。 然后，它会自动将位置和共享访问签名（SaS）令牌插入参数中。 

## <a name="nested-deployment-example"></a>嵌套部署示例
下面是一个简单的嵌套部署示例：

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

包含此模板的存储库中的文件夹的子文件夹 `nestedtemplates` 文件**NestOne**和**NestOne**。 在**azuredeploy.json**中，模板的 URI 是使用项目位置、嵌套模板文件夹和嵌套模板文件名生成的。 同样，使用嵌套模板的项目位置、嵌套模板文件夹和参数文件生成参数的 URI。 

下面是 Visual Studio 中同一个项目结构的图像： 

![Visual Studio 中的项目结构](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

你可以在主文件夹中添加其他文件夹，但不能将其添加到一个级别。 

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
- [在 Azure 开发测试实验室中将环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)