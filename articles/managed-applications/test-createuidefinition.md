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
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: c88bdce64e88f8639da2c4ebb01f4594fccff8a0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747082"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>测试托管应用程序的 Azure 门户界面
为 Azure 托管应用程序[创建 createUiDefinition.json 文件](create-uidefinition-overview.md)之后，需要测试用户体验。 为了简化测试，请使用一个可在门户中加载文件的脚本。 不需要真正部署该托管应用程序。

## <a name="prerequisites"></a>先决条件

* 一个 **createUiDefinition.json** 文件。 如果没有此文件，请复制[示例文件](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)并将其保存在本地。

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="download-test-script"></a>下载测试脚本

若要在门户中测试界面，请将以下脚本之一复制到本地计算机：

* [PowerShell side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>运行脚本

若要在门户中查看界面文件，请运行已下载的脚本。 该脚本在 Azure 订阅中创建一个存储帐户，并将 createUiDefinition.json 文件上传到该存储帐户。 在你第一次运行脚本时会创建此存储帐户，如果此存储帐户已删除，运行该脚本也会创建此存储帐户。 如果 Azure 订阅中已存在存储帐户，则该脚本会重复使用它。 该脚本将打开门户并从存储帐户加载文件。

提供存储帐户的位置，并指定包含 createUiDefinition.json 文件的文件夹。

对于 PowerShell，请使用：

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

对于 Azure CLI，请使用：

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

如果 createUiDefinition.json 文件与该脚本位于同一文件夹中，并且你已创建了存储帐户，则无需提供这些参数。

对于 PowerShell，请使用：

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

对于 Azure CLI，请使用：

```azurecli
./sideload-createuidef.sh
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

你可以使用这些值作为参数文件来测试部署模板。

## <a name="troubleshooting-the-interface"></a>排查界面问题

你可能看到的一些常见错误如下：

* 门户无法加载界面， 而是显示了一个带有泪滴的云图标。 通常，当文件中有语法错误时，你会看到此图标。 在 VS Code（或其他具有架构验证的 JSON 编辑器）中打开该文件，并查找语法错误。

* 门户在摘要屏幕上挂起。 通常，当输出部分中存在 bug 时，会发生这种中断。 例如，你可能引用了不存在的控件。

* 输出中的参数为空。 该参数可能引用了不存在的属性。 例如，对控件的引用有效，但属性引用无效。

## <a name="test-your-solution-files"></a>测试解决方案文件

确认门户界面按预期方式工作后，可以验证 createUiDefinition 文件是否能够正常地与 mainTemplate.json 文件相集成。 可以运行验证脚本测试来测试解决方案文件的内容，包括 createUiDefinition 文件。 该脚本会验证 JSON 语法，检查文本字段中的正则表达式，并确保门户界面的输出值与模板参数相匹配。 有关运行此脚本的信息，请参阅[对模板运行静态验证检查](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)。

## <a name="next-steps"></a>后续步骤

验证门户界面之后，请了解如何[在市场中提供 Azure 托管应用程序](publish-marketplace-app.md)。
