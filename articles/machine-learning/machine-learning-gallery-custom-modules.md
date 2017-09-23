---
title: "Cortana Intelligence 库自定义模块 | Microsoft Docs"
description: "在 Cortana Intelligence 库中发现自定义机器学习模块。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 56c308643ad6d20170174725f76f6ddbc76b3e22
ms.contentlocale: zh-cn
ms.lasthandoff: 04/15/2017

---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>在 Cortana Intelligence 库中发现自定义机器学习模块
[!INCLUDE [machine-learning-gallery-item-selector](../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>机器学习工作室的自定义模块
Cortana Intelligence 库提供了多个[自定义模块](https://gallery.cortanaintelligence.com/customModules)，可扩展 Azure 机器学习工作室的功能。 可导入这些模块供在实验中使用，以便开发更先进的预测分析解决方案。

目前，库提供*时序分析*、*关联规则*、*聚类分析算法*（k-means 除外）和*可视化效果*的模块，以及其他主力实用工具模块。


## <a name="discover"></a>发现
若要浏览[库中](http://gallery.cortanaintelligence.com)的自定义模块，请在“更多”下选择“自定义模板”。

![在库主页上选择“自定义模块”](media/machine-learning-gallery-custom-modules/select-custom-modules-in-gallery.png)

“[自定义模块](https://gallery.cortanaintelligence.com/customModules)”页显示最近添加的模块和热门模块列表。 若要查看所有自定义模块，请选择“查看全部”按钮。 若要搜索特定的自定义模块，请选择“查看全部”，然后选择筛选条件。 也可以在库页面顶部的“搜索”框中输入搜索项。

![选择“查看全部”以浏览所有自定义模板](media/machine-learning-gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>了解

若要了解已发布自定义模块的工作原理，请选择自定义模块以打开模块详细信息页。 详细信息页提供一致的信息性学习体验。 例如，详细信息页上突出显示模块用途，并列出预期的输入、输出和参数。 详细信息页还提供指向基础源代码的链接，以便可以进行检查和自定义。

### <a name="comment-and-share"></a>评论和共享
在自定义模板详细信息页的“评论”部分中，可对模块进行评论、提供反馈或提问。 甚至可以在 Twitter 或 LinkedIn 上与好友或同事分享模块。 还可以通过电子邮件发送指向模块详细信息页的链接，来邀请其他用户查看该页面。

![与朋友分享此项](media/machine-learning-gallery-how-to-use-contribute-publish/share-links.png)

![添加你自己的备注](media/machine-learning-gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>导入
可以将库中任意自定义模块导入自己的实验。

Cortana Intelligence 库提供两种方法来导入模块的副本：

* **从库中导入**。 当从库中导入自定义模块时，还可以获取演示如何使用模块的示例实验。
* **从机器学习工作室中导入**。 在机器学习工作室中工作时，可导入任何自定义模块（在此情况下，无法获取示例实验）。

### <a name="from-the-gallery"></a>从库中获取

1. 在库中打开模块详细信息页。 
2. 选择“在工作室中打开”。
   
    ![从库中打开自定义模块](media/machine-learning-gallery-custom-modules/open-custom-module-from-gallery.png)
   
每个自定义模块包含演示如何使用模块的示例实验。 当选择“在工作室中打开”时，示例实验会在机器学习工作室工作区中打开。 （如果尚未登录到工作室，系统会提示先使用 Microsoft 帐户登录。）

除了示例实验外，还会将自定义模块复制到工作区。 它还会与所有内置或自定义的机器学习工作室模块一放置在模块面板中。 此时可以在自己的实验中使用它，就像在工作区中使用其他任何模块一样。

### <a name="from-within-machine-learning-studio"></a>从机器学习工作室中获取

1. 在机器学习工作室中，选择“新建”。
2. 选择“模块”。 可以从库模块列表中选择，也可以使用“搜索”框查找特定模块。
3. 将鼠标指向模块，然后选择“导入模块”。 （若要获取有关模块的信息，请选择“在库中查看”。 这将转到库中的模块详细信息页。）
   
    ![将自定义模块导入机器学习工作室](media/machine-learning-gallery-custom-modules/add-custom-module-in-studio.png)

自定义模块将复制到工作区，并与内置或自定义机器学习工作室模块一起放置在模块面板中。 此时可以在自己的实验中使用它，就像在工作区中使用其他任何模块一样。

## <a name="use"></a>使用

无论选择哪种方法来导入自定义模块，在导入模块时，模块都会放置在机器学习工作室的模块面板中。 通过模块面板，可以像其他任何模块一样在工作区的任何实验中使用自定义模块。

若要使用导入的模块：

1. 创建实验或打开现有实验。
2. 若要在工作区中展开自定义模块列表，请在模块面板中选择“自定义”。 模块面板位于实验画布的左侧。
   
    ![工作室面板中的自定义模块列表](media/machine-learning-gallery-custom-modules/custom-module-in-studio-palette.png)
3. 选择导入的模块，并将其拖动到实验。


**[转到库](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


