---
title: "Cortana Intelligence 库实验 | Microsoft Docs"
description: "在 Cortana Intelligence 库中发现并共享实验。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: roopalik;garye
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a23c6419e7c4f478bfb1265a8e05c88c0b6af52b
ms.lasthandoff: 04/15/2017


---
# <a name="discover-experiments-in-cortana-intelligence-gallery"></a>在 Cortana Intelligence 库中发现实验
[!INCLUDE [machine-learning-gallery-item-selector](../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>机器学习工作室的实验
库具有在 [Azure 机器学习工作室](https://studio.azureml.net)中开发的各种[实验](https://gallery.cortanaintelligence.com/experiments)。 实验涵盖了从演示特定机器学习技术的快速概念证明实验，到完全开发的解决复杂机器学习问题的解决方案。

> [!NOTE]
> ***实验***是机器学习工作室中的画布，用于构建预测分析模型。 通过将数据与各种分析模块连接来创建模型。 可以尝试不同的想法，执行试运行并最终以 Web 服务的形式在 Azure 中部署模型。 有关如何创建基本实验的示例，请参阅[机器学习教程：在 Azure 机器学习工作室中创建首个实验](machine-learning-create-experiment.md)。 有关如何创建预测分析解决方案的更全面演练，请参阅[演练：在 Azure 机器学习中开发信用风险评估的预测分析解决方案](machine-learning-walkthrough-develop-predictive-solution.md)。
>
>

## <a name="discover"></a>发现
若要浏览[库中](http://gallery.cortanaintelligence.com)的实验，请选择库主页顶部的“实验”。

“[实验](https://gallery.cortanaintelligence.com/experiments)”页显示最近添加的实验和热门实验列表。 若要查看所有实验，请选择“查看全部”按钮。 若要搜索特定实验，请选择“查看全部”，然后选择筛选条件。 也可以在库页面顶部的“搜索”框中输入搜索项。

可在实验详细信息页上获取有关实验的详细信息。 若要打开实验详细信息页，请选择实验。 在实验详细信息页的“评论”部分中，可对实验进行评论、提供反馈或提问。 甚至可以在 Twitter 或 LinkedIn 上与好友或同事分享实验。 还可以通过电子邮件发送指向实验详细信息页的链接，来邀请其他用户查看该页面。

![与朋友分享此项](media/machine-learning-gallery-how-to-use-contribute-publish/share-links.png)

![添加你自己的备注](media/machine-learning-gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>下载
可以将库中任何实验的副本下载到机器学习工作室工作区。 然后，可以修改副本来创建自己的解决方案。

Cortana Intelligence 库提供两种方法来导入实验的副本：

* **从库中导入**。 如果在库中发现自己喜欢的实验，可下载副本并在机器学习工作室工作区中打开它。
* **从机器学习工作室中导入**。 在机器学习工作室中，可以使用库中的任何实验作为模板来创建新实验。

### <a name="from-the-gallery"></a>从库中获取

1. 在库中打开实验详细信息页。
2. 选择“在工作室中打开”。

    ![从库中打开实验](media/machine-learning-gallery-experiments/open-experiment-from-gallery.png)

当选择“在工作室中打开”时，实验会在机器学习工作室工作区中打开。 （如果尚未登录到机器学习工作室，系统会提示先使用 Microsoft 帐户登录。）

### <a name="from-within-machine-learning-studio"></a>从机器学习工作室中获取

1. 在机器学习工作室中，选择“新建”。
2. 选择“实验”。 可以从库实验列表中选择，也可以使用“搜索”框查找特定实验。
3. 将鼠标指向实验，然后选择“在工作室中打开”。 （若要查看有关实验的信息，请选择“在库中查看”。 这将转到库中的实验详细信息页。）

    ![从机器学习工作室内部打开库实验](media/machine-learning-gallery-experiments/open-experiment-from-studio.png)

可以像对在机器学习工作室中创建的任何其他实验一样，对下载的实验进行自定义、循环访问和部署。

![在工作室中打开的实验](media/machine-learning-gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>参与
在登录到库时将成为库社区的成员。 作为社区的成员，可以提供自己的实验，以便其他用户可以从已发现的解决方案中受益。

### <a name="publish-your-experiment-to-the-gallery"></a>将你的实验发布到库

1. 使用 Microsoft 帐户登录到机器学习工作室。
2. 创建实验，然后运行它。
3. 当准备好在库中发布实验时，在实验画布下方的操作列表中选择“发布到库”。

    ![选择“发布到库”](media/machine-learning-gallery-experiments/publish-experiment-to-gallery.png)
4. 在“实验说明”页上，输入标题和标记。 使标题和标记具有描述性。 突出显示使用过的技术或要解决的实际问题。 描述性实验标题的示例是“二元分类：Twitter 观点分析”。

    ![输入标题和标记以供发布](media/machine-learning-gallery-experiments/experiment-description.png)
5. 在“摘要”框中，输入实验的摘要。 简要描述实验解决的问题以及处理方式。
6. 在“详细说明”框中，介绍实验的各个部分所采取的步骤。 要包括的一些有用主题如下：
   * 实验图屏幕快照
   * 数据源和说明
   * 数据处理
   * 功能设计
   * 模型说明
   * 结果和模型性能的评估

   可将 Markdown 用于格式化说明。 若要在实验发布后查看实验说明页上的条目外观，请选择“预览”。

   ![选择“预览”可查看文本外观](media/machine-learning-gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > 为 Markdown 编辑和预览提供的文本框较小。 我们建议在 Markdown 编辑器中编写实验文档，复制该文档，然后将完成的文档粘贴到库的文本框中。 发布实验后，可使用基于 Web 的标准工具进行任何更正，这些工具使用 Markdown 进行编辑和预览。

7. 在“图像选择”页上，为实验选择缩略图。 缩略图显示在实验详细信息页的顶部和实验磁贴中。 当其他用户浏览库时，他们将看到缩略图。 可以从计算机中上传图像，或者从库中选择内置图像。
    </br>
    ![为库上载或选择映像](media/machine-learning-gallery-experiments/select-gallery-image.png)
8. 在“设置”页的“可见性”下，选择是公开发布内容（“公开”）还是仅具有该页面链接的人员才可以访问它（“未列出”）。

    ![选择是否公开发布或作为未列出发布](media/machine-learning-gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > 如果想要在公开发布文档前先确保它没有问题，可以先将实验发布为“未列出”。 以后可以在实验详细信息页上将可见性设置更改为“公开”。
   >
   >
9. 若要将实验发布到库，请选中“确定”复选标记。

    ![选中“确定”复选标记以发布实验](media/machine-learning-gallery-experiments/ok-checkmark.png)

有关如何发布优质库实验的提示，请参阅[记录和发布实验的提示](#tips-for-documenting-and-publishing-your-experiment)。

就这么简单 - 大功告成。

现在，可以在库中查看实验，并与他人共享该链接。 如果已使用“公开”可见性设置发布了实验，实验将显示在库的浏览和搜索结果中。 登录到库后，可随时在实验详细信息页上编辑实验文档。

若要查看所发布实验的列表，请选择任何库页面右上角处的图像。 接下来，选择名称以打开帐户页。

![选择帐户名](media/machine-learning-gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>更新实验
如果需要，可在已发布到库的实验中对工作流（模块、参数等）进行更改。 在机器学习工作室中，可对实验做任何所需的更改，然后重新发布。 已发布的实验将随更改进行更新。

可以直接在库中更改实验的以下任何信息：

* 实验名称
* 摘要或说明
* 标记
* 映像
* 可见性设置（“公开”或“未列出”）

还可以从库中删除实验。

可以在库中进行这些更改，还可以从实验详细信息页或个人资料页中删除实验。


#### <a name="from-the-experiment-details-page"></a>从实验详细信息页
在实验详细信息页上，若要更改实验的详细信息，请选择“编辑”。

![选择“编辑”以编辑实验](media/machine-learning-gallery-experiments/edit-button.png)

详细信息页将进入编辑模式。 若要进行更改，请选择实验名称、摘要或标记旁边的“编辑”。 完成更改后，选择“完成”。

![选择“编辑”以编辑详细信息，然后在完成后选择“完成”](media/machine-learning-gallery-experiments/edit-details-page.png)

若要更改实验的可见性设置（“公开”或“未列出”），或者从库中删除实验，请选择“设置”图标。

![选择“设置”以更改可见性或删除实验](media/machine-learning-gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>从个人资料页
在个人资料页上，选择实验的向下箭头，然后选择“编辑”。 这将转到处于编辑模式的实验的详细信息页。 完成更改后，选择“完成”。

若要从库中删除实验，请选择“删除”。

![选择“编辑”或“删除”](media/machine-learning-gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>记录和发布实验的提示
* 可以假定读者具有数据科学方面的经验，但使用简单的语言可能会很有帮助。 尽可能详细介绍相关内容。
* Cortana Intelligence 套件相对较新。 并非所有读者都熟悉如何使用它。 请提供充分的信息和分步说明，来帮助读者浏览实验。
* 对于需要正确理解和使用实验文档的读者而言，视觉对象会很有用。 视觉对象包括实验关系图和数据屏幕截图。 有关如何在实验文档中包含图像的详细信息，请参阅[发布指南和示例集合](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1)。
* 如果在实验中包含数据集（即，未通过“导入数据”模块导入数据集），数据集将成为实验的一部分并发布到库中。 确保发布的数据集具有相应的许可条款，以允许任何人共享和下载。 库发表内容均遵循 Azure [使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)。

## <a name="frequently-asked-questions"></a>常见问题
**为实验提交或编辑图像时有哪些要求？**

与实验一起提交的图像将用于创建所发布内容的实验磁贴。 我们建议图像应小于 500 KB，纵横比为 3:2，分辨率为 960 x 640。

**我在实验中使用的数据集会发生什么情况？数据集是否还会在库中发布？**

如果数据集包含在实验中，并且未通过“导入数据”模块导入，数据集将发布到库作为实验的一部分。 确保随实验一起发布的数据集具有相应的许可条款。 许可条款应允许任何人共享和下载数据。

**我有一个使用“导入数据”模块从 Azure HDInsight 或 SQL Server 中提取数据的实验。它使用我的凭据来检索数据。是否可以发布这类实验？如何确保我的凭据不共享？**

目前，无法发布使用库中凭据的实验。

**如何输入多个标记？**

输入标记之后，若要输入其他标记，请按 Tab 键。

**[转到库](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

