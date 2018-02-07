---
title: "直观创作 Azure 数据工厂 | Microsoft Docs"
description: "了解如何直观创作 Azure 数据工厂"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>直观创作数据工厂
通过 Azure 数据工厂 UX 体验，用户可以在其数据工厂中直观创作并部署资源，而无需编写任何代码。 通过此无代码的界面，可将活动拖放到管道画布上、执行测试运行、以迭代方式进行调试，以及部署和监视管道运行。 可选择通过以下两种方式来使用 ADF UX 工具：

1. 直接使用数据工厂服务
2. 配置 VSTS Git 集成以进行协作、源代码管理或版本控制

## <a name="authoring-with-data-factory"></a>使用数据工厂进行创作
第一个选项为直接使用数据工厂模式进行创作。 此方法与通过 VSTS 代码存储库进行创作的方式不同，因为没有存储库来存储你的更改的 JSON 实体，也不会针对协作或版本控制进行优化。

![配置数据工厂](media/author-visually/configure-data-factory.png)

在数据工厂模式下，只有“发布”模式。 你所做的任何更改都会直接发布到数据工厂服务。

![数据工厂发布](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>使用 VSTS Git 集成进行创作
使用 VSTS Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 VSTS Git 帐户存储库相关联，以进行源代码管理、协作和版本控制等。单个 VSTS GIT 帐户可以有多个存储库。 但是，VSTS Git 存储库只能与单个数据工厂相关联。 如果你还没有 VSTS 帐户和存储库，请在[此处](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)创建。

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>使用 Azure 数据工厂配置 VSTS Git 存储库
用户可通过两种方法使用数据工厂配置 VSTS GIT 存储库。

#### <a name="method-1-lets-get-started-page"></a>方法 1：“开始使用”页面

转到“开始使用”页面，然后单击“配置代码存储库”

![配置代码存储库](media/author-visually/configure-repo.png)

这里将出现一个侧面板，可用于配置存储库设置。

![配置存储库设置](media/author-visually/repo-settings.png)
* **存储库类型**：Visual Studio Team Services Git（目前不支持 Github）。
* **Visual Studio Team Services 帐户**：帐户名称可以从 https://{account name}.visualstudio.com 中找到。在[此处](https://www.visualstudio.com/team-services/git/)登录到你的 VSTS 帐户并访问 Visual Studio 配置文件以查看存储库和项目
* **ProjectName：**项目名称可从 https://{account name}.visualstudio.com/{project name} 中找到
* **RepositoryName：**存储库名称。 VSTS 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。
* **将现有数据工厂资源导入存储库**：通过选中此框，可将 UX 画布上创作的当前数据工厂资源以 JSON 格式导入到相关联的 VSTS GIT 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。    如果清除此复选框，则现有资源不会导入到 Git 存储库中。

#### <a name="method-2-from-authoring-canvas"></a>方法 2：从创作画布

在“创作画布”中，单击数据工厂名称下的“数据工厂”下拉菜单。 然后，单击“配置代码存储库”。 类似于“方法 1”，将出现可用于配置存储库设置的侧面板。 有关此设置的信息，请参阅前面的部分。

![配置代码存储库 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>版本控制
版本控制（也称为源代码管理）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

与数据工厂关联后的每个 VSTS Git 存储库都有一个主分支。 在这里，每个有权访问 VSTS Git 存储库的用户在进行更改时都有两个选项：同步和发布。

![同步发布](media/author-visually/sync-publish.png)

#### <a name="sync"></a>同步

一旦单击“同步”，你可以将更改从主分支拉取到本地分支，或将更改从本地分支推送到主分支。

![正在同步更改](media/author-visually/sync-change.png)

#### <a name="publish"></a>发布
 将主分支中的更改发布到数据工厂服务。

> [!NOTE]
> 主分支不表示数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务。




## <a name="expression-language"></a>表达式语言

用户可使用 Azure 数据工厂支持的表达式语言，在定义属性值过程中指定表达式。 有关受支持的表达式的更多信息，请参阅 [Azure 数据工厂中的表达式和函数](control-flow-expression-language-functions.md)。

在 UX 中，像这样在属性值中指定表达式。

![表达式语言](media/author-visually/expression-language.png)

## <a name="parameters"></a>parameters
用户可以在“参数”选项卡中指定“管道和数据集”的参数。此外，通过按“添加动态内容”可轻松利用属性中的参数。

![动态内容](media/author-visually/dynamic-content.png)

在这里，可利用现有的参数或在你的属性值中指定一个新的参数。

![parameters](media/author-visually/parameters.png)

## <a name="feedback"></a>反馈
单击“反馈”图标可向我们 (Microsoft) 提供有关各种功能以及你可能遇到的任何问题的反馈。

![反馈](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)一文
