---
title: include 文件
description: include 文件
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 9965e4c856fdef2af17b116264ad5344ebc97eb2
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466910"
---
创建披萨应用。

1. 选择 [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json)，打开 `pizza-app-for-luis.json` 文件的 GitHub 页面。
1. 右键单击或长按“原始”按钮，然后选择“将链接另存为”，将 `pizza-app-for-luis.json` 保存到计算机 。
1. 登录到 [LUIS 门户](https://www.luis.ai)。
1. 选择[我的应用](https://www.luis.ai/applications)。
1. 在“我的应用”页面上，选择“+ 新建用于对话的应用” 。
1. 选择“导入为 JSON”。
1. 在“导入新应用”对话框中，选择“选择文件”按钮 。
1. 选择下载的 `pizza-app-for-luis.json` 文件，然后选择“打开”。
1. 在“导入新应用”对话框的“名称”字段中，输入 Pizza 应用的名称，然后选择“完成”按钮  。

随即导入应用。

如果看到一个对话框“如何创建有效的 LUIS 应用”，关闭该对话框。

## <a name="train-and-publish-the-pizza-app"></a>训练并发布 Pizza 应用

Pizza 应用中应会显示“意向”页面，其中显示了一个意向列表。

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>向披萨应用添加创作资源

1. 选择“管理”。
1. 选择“Azure 资源”。
1. 选择“创作资源”。
1. 选择“更改创作资源”。

如果你有创作资源，请输入你的创作资源的租户名称、订阅名称以及 LUIS 资源名称。

如果你没有创作资源：

1. 选择“新建资源”。
1. 输入租户名称、资源名称、订阅名称和 Azure 资源组名称。

现在 Pizza 应用可以使用了。

## <a name="record-the-access-values-for-your-pizza-app"></a>记录 Pizza 应用的访问值

若要使用新的披萨应用，你需要该披萨应用的应用 ID、授权密钥和授权终结点。

若要查找这些值：

1. 在“意向”页面，选择“管理” 。
1. 在“应用程序设置”页面，记录“应用 ID” 。
1. 选择“Azure 资源”。
1. 选择“创作资源”。
1. 从“创作资源”选项卡上，记录主密钥。 此值是你的创作密钥。
1. 记录“终结点 URL”。 此值是你的创作终结点。
