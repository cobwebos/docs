---
title: “部署到 Azure”按钮
description: 使用按钮从 GitHub 存储库部署 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109047"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>使用部署按钮从 GitHub 存储库部署模板

本文介绍如何使用 **"部署到 Azure"** 按钮从 GitHub 存储库部署模板。 您可以将该按钮直接添加到 GitHub 存储库中的README.md文件或引用存储库的网页。

## <a name="use-common-image"></a>使用通用图像

要将按钮添加到网页或存储库，请使用以下图像：

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

图像显示为：

![“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>创建用于部署模板的 URL

要为模板创建 URL，请从回购中模板的原始 URL 开始：

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

然后，URL 对其进行编码。 您可以使用联机编码器或运行命令。 下面的 PowerShell 示例演示如何对值进行 URL 编码。

```powershell
[uri]::EscapeDataString($url)
```

在 URL 编码时，示例 URL 具有以下值。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

每个链接都以相同的基本 URL 开头：

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

将 URL 编码的模板链接添加到基本 URL 的末尾。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

您拥有链接的完整 URL。

## <a name="create-deploy-to-azure-button"></a>创建部署到 Azure 按钮

最后，将链接和图像放在一起。

要在 GitHub 存储库或网页中的README.md文件中添加带有 Markdown 的按钮，请使用：

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

对于 HTML，请使用：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>部署模板

要测试完整解决方案，请选择以下按钮：

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

门户显示一个窗格，允许您轻松提供参数值。 参数预先填充模板中的默认值。

![使用门户进行部署](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>后续步骤

- 要了解有关模板的更多信息，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
