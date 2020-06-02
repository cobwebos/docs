---
title: include 文件
description: include 文件
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 7ab6aa5f830e335a30502207d3a49a528d03f7fd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654262"
---
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

现在 Pizza 应用可以使用了。

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>记录 Pizza 应用的应用 ID、预测键和预测终结点

若要使用新 Pizza 应用，需要使用该 Pizza 应用的应用 ID、预测键和终结点。

若要查找这些值：

1. 在“意向”页面，选择“管理” 。
1. 在“应用程序设置”页面，记录“应用 ID” 。
1. 选择“Azure 资源”。
1. 在“Azure 资源”页面，记录“主键” 。 此值是预测键。
1. 记录“终结点 URL”。 此值是预测终结点。
