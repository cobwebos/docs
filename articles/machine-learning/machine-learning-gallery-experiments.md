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
ms.date: 03/31/2017
ms.author: roopalik;garye
translationtype: Human Translation
ms.sourcegitcommit: 262ee4f092efa11150de61168e6b03c0477f0543
ms.openlocfilehash: 66b61e4619518619aac644f6833c597cb9a7408e
ms.lasthandoff: 01/10/2017


---
# <a name="discover-experiments-in-the-cortana-intelligence-gallery"></a>在 Cortana Intelligence 库中发现实验
[!INCLUDE [machine-learning-gallery-item-selector](../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>机器学习工作室的实验
库包含在 [Azure 机器学习工作室](https://studio.azureml.net)中开发的多种多样的**[实验](https://gallery.cortanaintelligence.com/experiments)**。 涵盖了从演示特定机器学习技术的快速概念证明实验，到完全开发的解决复杂机器学习问题的解决方案。

> [!NOTE]
> ***实验***是 Azure 机器学习工作室中的一个画布，它允许你通过将数据与各种分析模块连接在一起来构建预测分析模型。 你可以尝试不同的想法，执行实验运行并最终以 Web 服务的形式在 Azure 中发布你的模型。 有关创建简单实验的示例，请参阅[机器学习教程：在 Azure 机器学习工作室中创建首个实验](machine-learning-create-experiment.md)。 有关创建预测分析解决方案的更完整演练，请参阅[演练：在 Azure 机器学习中开发信用风险评估的预测解决方案](machine-learning-walkthrough-develop-predictive-solution.md)。
> 
> 

## <a name="discover"></a>发现
  若要浏览库中的实验，请打开“[库](http://gallery.cortanaintelligence.com)”，然后单击库主页顶部的“**实验**”
。

 **“[实验](https://gallery.cortanaintelligence.com/experiments)”**
页显示最近添加的和最受欢迎的实验列表。
单击“**查看所有**”，以查看所有实验。
在本页中，可以浏览库中的所有试验。 还可在页面左侧选择筛选条件并在顶部输入搜索词进行搜索。

 单击任何实验，可打开实验的详细信息页，并阅读有关实验作用的信息。 你可以在此页进行评论、提供反馈，或通过评论部分提问。 甚至可以使用 LinkedIn 或 Twitter 的共享功能将其与朋友或同事共享。 同时，你也可以通过电子邮件发送指向该实验的链接，邀请其他用户查看此页面。

![与朋友分享此项](media/machine-learning-gallery-how-to-use-contribute-publish/share-links.png)

![添加你自己的备注](media/machine-learning-gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>下载
你可以从库中将任何实验副本下载到工作室工作区，然后修改副本来创建你自己的解决方案。
有两种方法可获取实验副本：

* **从库中获取** - 如果在库中发现你喜欢的一个实验，则可以轻松下载副本并在机器学习工作室工作区中打开它。
* **从机器学习工作室中获取** - 在工作室中，可以使用库中的任何实验作为模板来创建新实验。

### <a name="from-the-gallery"></a>从库中获取
从库中下载实验副本：

1. 在库中打开实验的详细信息页
2. 单击“**在工作室中打开**”
   
    ![从库中打开实验](media/machine-learning-gallery-experiments/open-experiment-from-gallery.png)

单击“**在工作室中打开**”时，实验将加载到机器学习工作室工作区并打开（如果尚未登录到工作室，在将实验复制到工作区前，系统会提示你使用 Microsoft 帐户进行登录）。

### <a name="from-within-machine-learning-studio"></a>从机器学习工作室中获取
在机器学习工作室中工作时，也可以打开相同的示例实验：

1. 在机器学习工作室中，单击“**+新建**”
2. 选择“**实验**” - 可以从由 Microsoft 提供的库实验列表中选择，或者可以使用搜索框找到特定实验
3. 将鼠标指向实验并选择“**在工作室中打开**” - 实验将复制到工作区中并打开（若要查看有关实验的信息，请选择“**在库中查看**”，这会转到该实验在库中的详细信息页）
   
    ![从机器学习工作室内部打开库实验](media/machine-learning-gallery-experiments/open-experiment-from-studio.png)

现在，你可以像对在机器学习工作室中创建的任何其他实验一样，对此实验进行自定义、循环访问和部署。

![在工作室中打开的实验](media/machine-learning-gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>参与
在登录到库时你即会成为库社区的一名成员。 这允许你提供你自己的实验，以便其他人可以从你发现的解决方案中受益。

### <a name="publish-your-experiment-to-the-gallery"></a>将你的实验发布到库
请按照下列步骤将你的实验加入 Cortana Intelligence 库：

1. 使用 Microsoft 帐户登录到机器学习工作室。
2. 创建实验并运行它。
3. 当准备好将实验发布到库中时，请单击实验画布下方的“**发布到库**”。
   
    ![单击“发布到库”](media/machine-learning-gallery-experiments/publish-experiment-to-gallery.png)
4. 填写标题和标记字段。 请为这些实验提供描述性的信息，强调使用的技术或所要解决的实际问题，例如，“二进制分类：Twitter 情绪分析”。
   
    ![在发布时填写标题和标记字段](media/machine-learning-gallery-experiments/experiment-description.png)
5. 编写内容所涵盖事项的摘要。 简要描述所要解决的问题和你的处理方式。
6. 使用详细的说明框逐步介绍实验的其他部分。 此处要包括的一些有用主题如下：
   
   * 实验图屏幕快照
   * 数据源和说明
   * 数据处理
   * 功能设计
   * 模型说明
   * 结果和模型性能的评估
     </br>
     </br>
     你可根据情况将 Markdown 用于格式化。 单击“**预览**”图标，可查看发布时的状态。
     </br>
     ![单击“预览”可查看文本外观](media/machine-learning-gallery-experiments/preview-markdown-text.png)
     
     <!-- -->
     > [!TIP]
     > 为 Markdown 编辑和预览框提供的框相当小。 因此，我们建议在 Markdown 编辑器中编写文档，然后将完成的文档粘贴到文本框中。  发布实验后，可在 Markdown 中使用基于 Web 标准工具进行编辑和预览，以进行必要的调整和更正。
     > 
     > 
7. 为你的库项上周缩略图。 在浏览库时，这将显示在项目页的顶部和项目磁贴中。 你可以从计算机中选择映像，或从库存映像选择一个。
    </br>
    ![为库上载或选择映像](media/machine-learning-gallery-experiments/select-gallery-image.png)
8. 选择是要公开发布你的内容，还是仅可由具有该链接的人员访问。
   
    ![选择是否公开发布或作为未列出发布](media/machine-learning-gallery-experiments/choose-public-or-unlisted.png)
   
    <!-- -->
   
   > [!TIP]
   > 如果想要在文档在公开发布之前先确保文档没有问题，可以先将其发布为为未列出状态，然后再从项目页上将其切换为公开。
   > 
   > 
9. 单击“**确定**”复选标记，将实验发布到库。
   
    ![单击“确定”复选标记以发布实验](media/machine-learning-gallery-experiments/ok-checkmark.png)

对于如何发布高品质的库实验小窍门，请参阅以下部分：**有关发布和制作高品质文档的建议**。

就这么简单 – 大功告成。

现在，你可以在库中查看实验，并与他人共享该链接。 如果公开发布，则实验将在浏览和搜索结果中显示。 此外，你也可以随时在登录后在项目页上编辑你的文档。

若要查看所发布实验的列表，请单击任何库页面右上角中的映像，然后单击你的名字以打开帐户页。

![单击帐户名](media/machine-learning-gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>更新实验
如果要对已发布到库的实验中的工作流（模块、参数等）进行更改，请返回到机器学习工作室中的实验进行更改，然后重新发布即可。 现有已发布的实验将随着更改而得到更新。

如果只需更改任何实验的以下信息，或者需要从库中删除此实验，可以在库中进行所有这些更改：

* 实验名称
* 摘要或说明文字
* 指定标记
* 所用图像
* 可见性设置（公开或未列出）
* 从库中删除实验

可以从实验的详细信息页或个人资料页中进行这些更改。

#### <a name="from-your-experiments-details-page"></a>从实验的详细信息页
在实验的详细信息页中，单击“编辑”可更改实验的详细信息。

![单击“编辑”以编辑实验](media/machine-learning-gallery-experiments/edit-button.png)

详细信息页进入编辑模式，并且可以单击实验名称、摘要、标记等旁边的“编辑”，以对其进行更改。 完成更改后，单击“完成”。

![单击“编辑”以编辑详细信息，完成后请单击“完成”](media/machine-learning-gallery-experiments/edit-details-page.png)

你也可以选择设置图标，以更改实验的可见性（公开或未列出），或从库中删除此实验。

![单击“设置”，更改可见性或删除此实验](media/machine-learning-gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>从个人资料页
从个人资料页中，可以单击实验上的向下箭头，并选择“编辑”。 这会转到处于编辑模式的实验的详细信息页。 完成更改后，单击“完成”。

另外，你也可以单击“删除”从库中删除此实验。

![单击“编辑”或“删除”](media/machine-learning-gallery-experiments/edit-delete-buttons.png)

### <a name="suggestions-for-publishing-and-for-quality-documentation"></a>有关发布和制作高品质文档的建议
* 虽然可以假定读者已具备之前的数据科学体验，但它仍有助于简化你的语言并尽可能详细地说明内容。
* 考虑到 Cortana Intelligence 套件相对较新，并非所有读者都会对其很熟悉。
  因此，请提供充分的信息和分步说明，帮助读者浏览你的工作。
* 视觉资料包括实验图表或数据的屏幕快照，对于读者理解和正确使用你的内容非常有用。 有关如何在文档中包含图像的详细信息，请参阅[发布指南和示例集合](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1)。
* 如果在实验中包含数据集（并非通过导入数据模块导入），它将成为实验的一部分，并被发布到库中。 因此，确保所发布的数据集具有适当的许可条款，用于任何人共享和下载。 库发表内容均遵循 Azure [使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)。

## <a name="frequently-asked-questions"></a>常见问题
**针对我的实验提交或编辑映像时，有哪些映像要求？**

与实验一起提交的映像将用于创建你所发布内容的实验磁贴。 建议将映像大小 < 500 Kb，纵横比为 3:2。 建议分辨率使用 960 x 640

**我在实验中使用的数据集会如何？数据集也会被发布到库吗？**

如果你的数据集包含在实验中，且未通过导入数据模块导入，它将成为实验的一部分，并且会和实验一同发布到库。 出于此原因，请确保与实验一同发布的数据集具有适当的许可条款，以允许任何人共享和下载。

**我有一个使用导入数据模块从 HDInsight 或 SQL 中提取数据的实验。它使用我的凭据来检索数据。我需要如何发布这种实验，并确保我的凭据不会被共享？**

目前我们不允许发布使用凭据的实验。

**如何输入多个标记？**

输入标记后，按 tab 键以输入另一个标记。

**[将我转到库 >>](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


