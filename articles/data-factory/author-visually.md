---
title: "在 Azure 数据工厂中创作视觉对象 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中使用视觉对象创作"
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
ms.openlocfilehash: 209afba99ac2b43c252d93c32930908ec1f957f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure 数据工厂中的视觉对象创作
Azure 数据工厂用户界面体验 (UX) 允许你以可视方式创作和部署资源为你的数据工厂而无需编写任何代码。 通过此无代码的界面，可将活动拖放到管道画布上、执行测试运行、以迭代方式进行调试，以及部署和监视管道运行。 可通过两种方法使用 UX 执行视觉对象创作：

- 直接使用数据工厂服务
- 通过协作、源代码管理或版本控制的 Visual Studio Team Services (VSTS) Git 集成的创作。

## <a name="author-directly-with-the-data-factory-service"></a>直接使用数据工厂服务
使用数据工厂服务可视方式创作不同于可视方式创作 VSTS 使用两种方式：

- 数据工厂服务不包括存储所做的更改的 JSON 实体的存储库。
- 数据工厂服务未优化协作或版本控制。

![配置数据工厂服务 ](media/author-visually/configure-data-factory.png)

当使用 UX **创作画布**创作直接与数据工厂服务，仅**发布**模式是可用。 所做的任何更改都会直接发布到数据工厂服务。

![发布模式](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>使用 VSTS Git 集成进行创作
使用 VSTS Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 VSTS Git 帐户存储库相关联，以进行源代码管理、协作和版本控制等。 单个的 VSTS Git 帐户可以有多个存储库，但 VSTS Git 存储库可以只有一个数据工厂与相关联。 如果你没有 VSTS 帐户或存储库，请按照[这些说明](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)创建你的资源。

> [!NOTE]
> 数据工厂管道无法访问 VSTS Git 存储库中存储的文件。 因此，不能在 VSTS Git 存储库中存储数据工厂管道活动使用的文件 - 例如，数据文件和脚本文件。

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 VSTS Git 存储库
用户可通过两种方法使用数据工厂配置 VSTS GIT 存储库。

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>配置方法 1：“开始使用”页面
在 Azure 数据工厂，转到**让我们开始吧**页。 选择“配置代码存储库”：

![VSTS 代码存储库配置](media/author-visually/configure-repo.png)

**存储库设置**配置窗格中显示：

![配置存储库设置](media/author-visually/repo-settings.png)

窗格将显示以下 VSTS 代码存储库设置：

| 设置 | 说明 | 值 |
|:--- |:--- |:--- |
| **存储库类型** | VSTS 代码存储库的类型。<br/>**注意**：当前不支持 GitHub。 | Visual Studio Team Services Git |
| **Visual Studio Team Services 帐户** | VSTS 帐户名称。 可以在 `https://{account name}.visualstudio.com` 找到 VSTS 帐户名称。 可以[登录到 VSTS 帐户](https://www.visualstudio.com/team-services/git/)并访问 Visual Studio 配置文件以查看存储库和项目 | \<你的帐户名> |
| **ProjectName** | VSTS 项目名称。 可以在 `https://{account name}.visualstudio.com/{project name}` 找到 VSTS 项目名称。 | \<你的 VSTS 项目名称> |
| **RepositoryName** | VSTS 代码存储库名称。 VSTS 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | \<你的 VSTS 代码存储库名称> |
| 确认选中“将现有的数据工厂资源导入存储库”选项。 | 指定是否从 UX 导入现有数据工厂资源**创作画布**入 VSTS Git 存储库中。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布
在 Azure 数据工厂 UX**创作画布**，找到你的数据工厂。 选择**数据工厂**下拉列表菜单，然后选择**配置代码存储库**。

此时将显示配置窗格。 有关配置设置的详细信息，请参阅中的说明<a href="#method1">配置方法 1</a>。

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>使用版本控制
版本控制（也称为_源代码管理_）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

与数据工厂关联后的每个 VSTS Git 存储库都有一个主分支。 可以访问到 VSTS Git 存储库，你可以通过选择更改代码**同步**或**发布**:

![通过同步或发布更改代码](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>同步代码更改
一旦单击“同步”，你可以将更改从主分支拉取到本地分支，或将更改从本地分支推送到主分支。

![同步代码更改](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>发布代码更改
选择**发布**手动在 master 分支到数据工厂服务中发布你的代码更改。

> [!IMPORTANT]
> 主分支不表示数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务。

## <a name="use-the-expression-language"></a>使用表达式语言
可使用 Azure 数据工厂支持的表达式语言，在定义属性值过程中指定表达式。 有关受支持的表达式的更多信息，请参阅 [Azure 数据工厂中的表达式和函数](control-flow-expression-language-functions.md)。

指定属性值的表达式使用 UX **创作画布**：

![使用表达式语言](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>指定参数
可以在 Azure 数据工厂“参数”选项卡中指定“管道和数据集”的参数。可以轻松地使用参数在属性中通过选择**添加动态内容**：

![添加动态内容](media/author-visually/dynamic-content.png)

可以使用现有的参数或属性值为指定新的参数：

![指定属性值的参数](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>提供反馈
选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题：

![反馈](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>后续步骤
若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
