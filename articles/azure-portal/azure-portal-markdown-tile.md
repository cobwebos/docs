---
title: 在 Azure 仪表板上使用自定义 Markdown 磁贴
description: 了解如何向 Azure 仪表板添加 Markdown 磁贴，以便显示静态内容
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551549"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>在 Azure 仪表板上使用 Markdown 磁贴显示自定义内容

可以向 Azure 仪表板添加 Markdown 磁贴，以便显示自定义的静态内容。 例如，可以通过 Markdown 磁贴显示基本的说明、图像或一组超链接。

## <a name="add-a-markdown-tile-to-your-dashboard"></a>向仪表板添加 Markdown 磁贴

1. 在 Azure 门户边栏中选择“仪表板”。  如果已创建任何自定义仪表板，请在仪表板视图中通过下拉列表选择会显示自定义 Markdown 磁贴的仪表板。 选择用于打开**磁贴库**的编辑图标。

   ![屏幕截图，显示仪表板编辑视图](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. 在“磁贴库”中找到名为 **Markdown** 的磁贴，然后单击“添加”。   此时会将磁贴添加到仪表板，而“编辑 Markdown”窗格也会打开。 

1. 编辑“标题”、“子标题”和“内容”字段，对磁贴进行自定义。    在下面显示的示例中，Markdown 磁贴已进行编辑，可以显示自定义的支持人员信息。

   ![屏幕截图，显示 Markdown 磁贴的编辑视图](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. 选择“完成”，关闭“编辑 Markdown”窗格。   内容会显示在 Markdown 磁贴上，该磁贴随后可以通过在右下角拖动图柄来重设大小。

   ![屏幕截图，显示自定义 Markdown 磁贴](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown 内容功能和限制

可以在 Markdown 磁贴上使用纯文本、Markdown 语法和 HTML 内容的任意组合。 Azure 门户使用名为 _marked_ 的开源库将内容转换为在磁贴上显示的 HTML。 通过 _marked_ 生成的 HTML 在呈现之前先由门户进行预处理。 此步骤有助于确保自定义不会影响门户的安全性或布局。 在该预处理期间，HTML 中任何带有潜在威胁的内容都会被删除。 门户不允许以下类型的内容：

* JavaScript - `<script>` 标记以及内联的 JavaScript 评估会被删除。
* iframe - `<iframe>` 标记会被删除。
* 样式 - `<style>` 标记会被删除。 HTML 元素上的内联样式属性没有获得正式支持。 你可能会发现，某些内联样式属性可以使用，但如果它们干扰门户的布局，系统可能会随时停用它们。 Markdown 磁贴适用于基本的静态内容，该内容使用门户的默认样式。

## <a name="next-steps"></a>后续步骤

* 若要创建自定义仪表板，请参阅[如何在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)
