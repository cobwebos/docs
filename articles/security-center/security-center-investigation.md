---
title: "调查 Azure 安全中心的事件和警报 | Microsoft Docs"
description: "本文档介绍如何使用 Azure 安全中心的调查功能调查安全事件和警报。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: 818c257d1959936f0dc326486e372677aacb065a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>调查 Azure 安全中心的事件和警报（预览版）
本文档介绍如何使用 Azure 安全中心的调查功能调查安全事件和警报。

## <a name="what-is-investigation-in-security-center"></a>什么是安全中心调查？
使用安全中心的调查功能，可以对潜在的[安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)进行会审，了解其范围并跟踪其根本原因。
 
这样做的目的是将所有涉及所调查事件的实体（[安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)、用户、计算机和事件）关联到一起，加快调查过程。  为此，可以通过安全中心将相关数据和所涉及的实体关联在一起，并通过实时图公开这种关联性。你可以通过实时图来浏览对象并将相关信息可视化。


## <a name="how-investigation-works"></a>如何进行调查？
调查功能包含一个关系图，该图占据了调查仪表板的中心区域。 关系图始终聚焦在特定的实体上，并呈现与之相关的实体。 实体可能是安全警报、用户、计算机或事件。
 
![映射](./media/security-center-investigation/security-center-investigation-fig1.png)

用户可以从一个实体导航到另一个实体，只需在图中单击相应的实体即可。 关系图会自动聚焦在所选实体及其相关实体上。 可以从图中删除不再相关的实体。

### <a name="investigation-path"></a>调查路径
用户在导航到不同的实体时，可以通过调查路径来跟踪调查上下文并进行快速导航。 包含调查结果的事件始终是调查路径中最左端的事件。

![路径](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>常规信息
在关系图中呈现某个实体时，选项卡会显示该实体的其他信息。 “信息”选项卡提供实体的常规信息，这些信息来自不同的可用信息源。 

![常规信息](./media/security-center-investigation/security-center-investigation-fig3.png)

信息选项卡显示的信息与映射中选择的事件相关。 事件是一个容器，其中包括调查的结果。 每项调查均在事件上下文中进行。

事件仅在用户针对特定警报单击“开始调查”按钮时创建。 适用于调查者的基本功能是标记各种实体，例如用户、计算机或警报。 将某个实体标记为相关时，会提供原因。 自此以后，该实体就会直接显示在关系图和事件实体列表中的事件下。

### <a name="entities"></a>实体

“实体”选项卡显示按类型分组的所有相关实体。 它适用于两种情况：一是图中要呈现的实体过多，二是实体名称过长，以表格方式检查更容易。

![实体](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>搜索

“搜索”选项卡提供适用于实体的所有日志类型。 可以看到每个日志类型的可用记录数。 单击每个日志类型即可转到搜索屏幕。 可以在搜索屏幕中优化搜索，并使用各种搜索功能，例如设置警报。 在当前版本中，搜索选项卡仅适用于用户和计算机实体。

![搜索](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>浏览

“浏览”选项卡允许调查者检查与各种问题相关的数据，而这些问题又与实体相关。 例如，对某台计算机进行调查时，浏览选项卡中会提供在该计算机中执行的进程的列表。某些情况下，浏览选项卡提供的数据可能指示存在可疑问题。 调查者可以在选项卡中检查数据，也可以在搜索屏幕中将其打开，以便检查大型数据集并使用高级搜索选项，例如“筛选”和“导出到 Excel”。

![浏览](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>时间线

在关系图和各个选项卡中提供的大多数数据是在特定时间段内相关。 此时间范围是使用关系图左上方的时间范围选择器设置的。 调查者可以使用多种方法来选择时间范围。 

![时间线](./media/security-center-investigation/security-center-investigation-fig7.png)

以下各项对时间范围很敏感：

- 关系图中的用户-计算机关系：仅呈现在该时间范围内登录到计算机的用户。
- 检查计算机和用户时呈现哪些警报：仅呈现在时间范围内发生的警报。
- 实体选项卡与关系图遵循相同的逻辑。

以下各项在呈现时，不考虑所选时间范围：

- 呈现警报时，始终会呈现其中包含的所有实体，例如用户和计算机。
- 如果事件包含实体，则会呈现该实体。

> [!NOTE]
> “搜索”和“浏览”选项卡仅显示此时间范围内的记录。

## <a name="how-to-perform-an-investigation"></a>如何进行调查？

可以从安全事件或警报开始调查，所选选项视需求而变。 以下步骤用于从警报开始调查：

1.  打开“安全中心”仪表板。
2.  单击“安全警报”，选择要调查的事件。
3.  在事件的页面中单击“开始调查”按钮，此时会显示“调查”仪表板。

    ![警报](./media/security-center-investigation/security-center-investigation-fig8.png)

4. 可以通过此仪表板选择映射中的实体，然后就会在屏幕右侧显示该实体的相关信息。

    ![调查仪表板](./media/security-center-investigation/security-center-investigation-fig9.png)

此时可以浏览涉及此事件的实体，以及每个实体的更多详细信息。 

## <a name="see-also"></a>另请参阅
本文档介绍了如何使用安全中心的调查功能。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Managing and responding to security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。 
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

