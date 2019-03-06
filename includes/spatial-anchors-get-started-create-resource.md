---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752496"
---
## <a name="create-a-spatial-anchors-resource"></a>创建空间定位点资源

1. 导航到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。

2. 在 Azure 门户的左侧菜单中，选择“创建资源”。

3. 在搜索栏中搜索“空间定位点”。

   ![搜索空间定位点](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. 选择“空间定位点(预览)”以打开一个对话框，然后选择“创建”。

5. 在“空间定位点帐户”窗体中：

   1. 指定唯一的资源名称。
   2. 选择要将资源附加到的订阅。
   3. 通过选择“新建”创建资源组，将资源组命名为 myResourceGroup，然后选择“确定”。
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. 选择放置资源的位置（区域）。
   5. 选择“新建”开始创建资源。

   ![创建资源](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. 完成资源创建后，可以查看资源属性。 记下资源的帐户 ID 值，因为稍后需要使用此值。

   ![查看资源属性](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. 在“设置”下，选择“密钥”，记下“主密钥”值。 此值是 `Account Key`，稍后会使用此值。

   ![查看帐户密钥](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
