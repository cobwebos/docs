---
title: 测试 Azure 托管应用程序的 UI 定义 | Microsoft Docs
description: 介绍如何测试通过门户创建 Azure 托管应用程序的用户体验。
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043275"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>测试托管应用程序的 Azure 门户界面
为 Azure 托管应用程序[创建 createUiDefinition.json 文件](create-uidefinition-overview.md)之后，需要测试用户体验。 为了简化测试，请使用一个可在门户中加载文件的脚本。 不需要真正部署该托管应用程序。

## <a name="prerequisites"></a>先决条件

* 一个 **createUiDefinition.json** 文件。 如果没有此文件，请复制[示例文件](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json)并将其保存在本地。

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="download-test-script"></a>下载测试脚本

若要在门户中测试界面，请将以下脚本之一复制到本地计算机：

* [PowerShell side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>运行脚本

若要在门户中查看界面文件，请运行已下载的脚本。 该脚本在 Azure 订阅中创建一个存储帐户，并将 createUiDefinition.json 文件上传到该存储帐户。 然后，它会打开门户，并从存储帐户加载该文件。

提供存储帐户的位置，并指定包含 createUiDefinition.json 文件的文件夹。 只有首次运行该脚本，或者删除了存储帐户时，才需要提供存储帐户的位置。

对于 PowerShell，请使用：

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

对于 Azure CLI，请使用：

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
```

## <a name="test-your-interface"></a>测试界面

脚本会在浏览器中打开新选项卡。 浏览器中会显示门户，其中包含用于创建托管应用程序的界面。

![查看门户](./media/test-createuidefinition/view-portal.png)

在填写字段之前，请在浏览器中打开“Web 开发人员工具”。 “控制台”显示有关界面的重要消息。

![选择控制台](./media/test-createuidefinition/select-console.png)

如果界面定义有错误，控制台中会显示说明。

![显示错误](./media/test-createuidefinition/show-error.png)

提供字段值。 完成后，会看到这些值已传递到模板。

![显示值](./media/test-createuidefinition/show-json.png)

## <a name="test-your-solution-files"></a>测试解决方案文件

确认门户界面按预期方式工作后，可以验证 createUiDefinition 文件是否能够正常地与 mainTemplate.json 文件相集成。 可以运行验证脚本测试来测试解决方案文件的内容，包括 createUiDefinition 文件。 该脚本会验证 JSON 语法，检查文本字段中的正则表达式，并确保门户界面的输出值与模板参数相匹配。 有关运行此脚本的信息，请参阅[对模板运行静态验证检查](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)。

## <a name="next-steps"></a>后续步骤

验证门户界面之后，请了解如何[在市场中提供 Azure 托管应用程序](publish-marketplace-app.md)。
