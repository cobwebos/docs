---
title: 向 Azure Maps 提供数据反馈 |Microsoft Azure 映射
description: 使用 Microsoft Azure 地图反馈工具提供数据反馈。
author: farah-alyasari
ms.author: v-faalya
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c4eb8932dda32ae6773e76dc7102f5afc6bc655
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209828"
---
# <a name="provide-data-feedback-to-azure-maps"></a>向 Azure Maps 提供数据反馈

自2018年5月起，Azure Maps 已可用。 Azure Maps 提供了新的映射数据、易于使用的 REST Api 和强大的 Sdk，以支持具有不同种类的业务用例的企业客户。 现实世界每秒都在发生变化，为我们的客户提供事实数字表示形式，这一点非常重要。 计划打开或关闭设施的客户需要我们的地图才能及时更新。 因此，他们可以在适当的设施有效地计划交付、维护或客户服务。 我们创建了 Azure Maps 数据反馈网站，以使我们的客户能够提供直接的数据反馈。 客户的数据反馈直接发送到我们的数据提供程序及其地图编辑器。 他们可以快速评估并将反馈纳入我们的地图产品。  

[Azure Maps 数据反馈网站](https://feedback.azuremaps.com)为我们的客户提供了一种简单的方法来提供地图数据反馈，尤其是在相关业务点和居民地址上。 本文将指导你了解如何使用 Azure Maps 反馈网站提供不同类型的反馈。

## <a name="add-a-business-place-or-a-residential-address"></a>添加办公地点或住宅地址 

你可能需要提供有关缺少兴趣点或住宅地址的反馈。 可通过两种方法来执行此操作。 打开 Azure 映射数据反馈网站，搜索缺少位置的坐标，然后单击 "添加位置"

  ![搜索缺少的位置](./media/how-to-use-feedback-tool/search-poi.png)

或者，您可以与地图进行交互。 单击 "位置"，在坐标处删除 pin，然后单击 "添加位置"。

  ![添加 pin](./media/how-to-use-feedback-tool/add-poi.png)

单击时，会将您定向到一个窗体，以便为该位置提供相应的详细信息。

  ![添加位置](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>修复业务场所或住宅地址 

你还可以通过反馈网站搜索和查找业务地点或地址。 如果地址或 pin 位置不正确，则可以提供反馈来修复。 若要提供反馈来修复地址，请使用搜索栏搜索企业位置或住宅地址。 在结果列表中单击你感兴趣的位置。 单击 "修复此位置"。

  ![要修复的搜索位置](./media/how-to-use-feedback-tool/fix-place.png)

若要提供反馈来修复地址，请填写 "修复位置" 窗体，然后单击 "提交" 按钮。

  ![修复窗体](./media/how-to-use-feedback-tool/fix-form.png)

如果位置的 pin 位置错误，请选中 "修复位置" 窗体上的复选框，该复选框显示 "pin 位置不正确"。 将 pin 移到正确的位置，然后单击 "提交" 按钮。

  ![移动 pin 位置](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>添加注释 

除了允许搜索位置之外，还可以使用反馈工具为与位置相关的详细信息添加自由格式的文本注释。 若要添加注释，请搜索位置或单击该位置。 单击 "添加评论"，编写注释，然后单击 "提交"。

  ![添加注释](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>跟踪状态 

还可以通过选中 "我想跟踪状态" 框并在发出请求时提供电子邮件，来跟踪请求的状态。 你将在电子邮件中收到跟踪链接，该链接提供请求的最新状态。 

  ![反馈状态](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>后续步骤

若要发布与 Azure Maps 相关的任何技术问题，请访问：

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure Maps 反馈论坛](https://feedback.azure.com/forums/909172-azure-maps)
