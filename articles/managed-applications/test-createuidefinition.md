---
title: 测试 Azure 托管应用程序的 UI 定义 | Microsoft Docs
description: 介绍如何测试通过门户创建 Azure 托管应用程序的用户体验。
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257588"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>为 Azure 托管应用程序测试门户界面

之后[创建 createUiDefinition.json 文件](create-uidefinition-overview.md)托管应用程序，您需要测试的用户体验。 若要简化测试，使用加载您在门户中的文件的沙盒环境。 不需要真正部署该托管应用程序。 沙盒提供了您当前的全屏幕的门户体验中的用户界面。 或者，可以使用 PowerShell 脚本，用于测试该接口，但它使用在门户的旧视图。 本文对这两种方法都进行了介绍。 沙盒是推荐的方法，以预览该接口。

## <a name="prerequisites"></a>必备组件

* 一个 **createUiDefinition.json** 文件。 如果没有此文件，复制[示例文件](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)。

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="use-sandbox"></a>使用沙盒

1. 打开[创建 UI 定义沙盒](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)。

   ![显示沙盒](./media/test-createuidefinition/show-sandbox.png)

1. 将 createUiDefinition.json 文件的内容替换为空定义。 选择**预览版**。

   ![选择预览](./media/test-createuidefinition/select-preview.png)

1. 显示您创建的窗体。 您可以单步执行用户体验并填充的值。

   ![显示窗体](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>故障排除

如果你的窗体不会显示在选择后**预览版**，可能会有语法错误。 找到右侧的滚动条上的红色指示灯并导航到它。

![显示语法错误](./media/test-createuidefinition/show-syntax-error.png)

如果不会显示你的窗体，并改为您看到的拆解放置的云图标，窗体具有出现错误，例如缺少属性。 在浏览器中打开 Web 开发人员工具。 “控制台”显示有关界面的重要消息。 

![显示错误](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>使用测试脚本

若要在门户中测试界面，请将以下脚本之一复制到本地计算机：

* [PowerShell side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

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

脚本会在浏览器中打开新选项卡。 浏览器中会显示门户，其中包含用于创建托管应用程序的界面。

![查看门户](./media/test-createuidefinition/view-portal.png)

提供字段值。 完成后，会看到这些值已传递到模板。

![显示值](./media/test-createuidefinition/show-json.png)

你可以使用这些值作为参数文件来测试部署模板。

如果在门户程序在摘要屏幕，可能有一个 bug 中的输出部分。 例如，你可能引用了不存在的控件。 如果输出中的参数为空，该参数可能引用不存在的属性。 例如，对控件的引用有效，但属性引用无效。

## <a name="test-your-solution-files"></a>测试解决方案文件

确认门户界面按预期方式工作后，可以验证 createUiDefinition 文件是否能够正常地与 mainTemplate.json 文件相集成。 可以运行验证脚本测试来测试解决方案文件的内容，包括 createUiDefinition 文件。 该脚本会验证 JSON 语法，检查文本字段中的正则表达式，并确保门户界面的输出值与模板参数相匹配。 有关运行此脚本的信息，请参阅[对模板运行静态验证检查](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)。

## <a name="next-steps"></a>后续步骤

验证门户界面之后，请了解如何[在市场中提供 Azure 托管应用程序](publish-marketplace-app.md)。
