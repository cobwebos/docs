---
title: 部署在 Azure 开发测试实验室中的嵌套的资源管理器模板环境 |Microsoft Docs
description: 了解如何部署嵌套的 Azure 资源管理器模板，以提供与 Azure 开发测试实验室环境。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835280"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>将测试环境的嵌套的 Azure 资源管理器模板部署
嵌套的部署，可执行从主资源管理器模板中的其他 Azure 资源管理器模板。 它使您以一组针对性和特定用途的模板将部署分解成。 它提供了测试、 重用和可读性方面的好处。 文章[部署 Azure 资源时使用链接的模板](../azure-resource-manager/resource-group-linked-templates.md)好好了解一下此解决方案提供了几个代码示例。 本文提供的示例，是特定于 Azure 开发测试实验室。 

## <a name="key-parameters"></a>密钥参数
尽管您可以从头开始创建你自己的资源管理器模板，我们建议你使用[Azure 资源组项目](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)在 Visual Studio 中，这样就可以轻松开发和调试模板。 在您将某一嵌套的部署资源添加到 azuredeploy.json，Visual Studio 添加多个项，以使模板更灵活。 这些项包括辅助的模板和参数文件、 在主模板文件中，变量名称与新的文件的存储位置的两个参数的子文件夹。 **_ArtifactsLocation**并 **_artifactsLocationSasToken**是开发测试实验室使用的密钥参数。 

如果您不熟悉开发测试实验室环境的工作方式，请参阅[使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 链接到开发测试实验室中的实验室的存储库中存储模板。 当使用这些模板创建新的环境时，这些文件将移动到在实验室中的 Azure 存储容器。 若要能够识别并将嵌套的文件复制，开发测试实验室标识 _artifactsLocation 和 _artifactsLocationSasToken 参数，并将复制到的存储容器的子文件夹。 然后，它会自动插入的位置和共享访问签名 (SaS) 令牌参数。 

## <a name="nested-deployment-example"></a>嵌套的部署示例
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

包含此模板的存储库中的文件夹具有一个子文件夹`nestedtemplates`的文件**NestOne.json**并**NestOne.parameters.json**。 在中**azuredeploy.json**，URI 模板旨在使用项目的位置、 嵌套的模板文件夹的嵌套模板文件的名称。 同样，为嵌套模板中使用项目位置、 嵌套的模板文件夹和参数文件生成的参数的 URI。 

下面是 Visual Studio 中的相同项目结构的图像： 

![在 Visual Studio 中的项目结构](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

在主文件夹，但不是超过单个级别，可以添加其他文件夹。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关环境的详细信息： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [配置和在 Azure 开发测试实验室中使用公共环境](devtest-lab-configure-use-public-environments.md)
- [连接到实验室的虚拟网络中 Azure 开发测试实验室环境](connect-environment-lab-virtual-network.md)