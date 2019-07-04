---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "67135054"
---
## <a name="create-a-spatial-anchors-resource"></a>创建空间定位点资源

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。

在 Azure 门户的左侧导航窗格中，选择“创建资源”  。

使用搜索框以搜索“空间定位点”  。

   ![搜索空间定位点](./media/spatial-anchors-get-started-create-resource/portal-search.png)

搜索“空间定位点”  。 在对话框中，选择“创建”  。

在“空间定位点帐户”对话框中  ：

- 使用常规字母数字字符输入唯一的资源名称。
- 选择想要将资源附加到的订阅。
- 选择“新建”可创建资源组  。 将其命名为 myResourceGroup 并选择“确定”   。
      [!INCLUDE [resource group intro text](resource-group.md)]
- 选择可在其中放置资源的位置（区域）。
- 选择“新建”开始创建资源  。

   ![创建资源](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

创建资源后，Azure 门户将显示部署已完成。 单击“转到资源”。 

![部署完毕](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

然后，可以查看资源属性。 将资源的“帐户 ID”值复制到文本编辑器，因为稍后需要用它  。

   ![资源属性](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

在“设置”下，选择“密钥”   。 将“主键”值复制到文本编辑器中  。 此值为 `Account Key`。 稍后需要用到此信息。

   ![帐户密钥](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
