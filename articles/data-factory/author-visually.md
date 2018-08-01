---
title: 在 Azure 数据工厂中创作视觉对象 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中使用视觉对象创作
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214844"
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

当使用 UX 创作画布借助数据工厂服务直接进行创作时，只能使用“全部发布”模式。 所做的任何更改都会直接发布到数据工厂服务。

![发布模式](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>使用 VSTS Git 集成进行创作
使用 VSTS Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 VSTS Git 帐户存储库相关联，以进行源代码管理、协作和版本控制等。 单个的 VSTS Git 帐户可以有多个存储库，但 VSTS Git 存储库可以只有一个数据工厂与相关联。 如果你没有 VSTS 帐户或存储库，请按照[这些说明](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)创建你的资源。

> [!NOTE]
> 可以在 VSTS GIT 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 VSTS GIT 存储库中存储的脚本或数据文件上传到 Azure 存储。

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 VSTS Git 存储库
用户可通过两种方法使用数据工厂配置 VSTS GIT 存储库。

#### <a name="method1"></a> 配置方法 1：“开始使用”页面

在 Azure 数据工厂，转到**让我们开始吧**页。 选择“配置代码存储库”：

![VSTS 代码存储库配置](media/author-visually/configure-repo.png)

**存储库设置**配置窗格中显示：

![配置存储库设置](media/author-visually/repo-settings.png)

窗格将显示以下 VSTS 代码存储库设置：

| 设置 | Description | 值 |
|:--- |:--- |:--- |
| **存储库类型** | VSTS 代码存储库的类型。<br/>**注意**：当前不支持 GitHub。 | Visual Studio Team Services Git |
| **Azure Active Directory** | Azure AD 租户的名称。 | <your tenant name> |
| **Visual Studio Team Services 帐户** | VSTS 帐户名称。 可以在 `https://{account name}.visualstudio.com` 找到 VSTS 帐户名称。 可以[登录到 VSTS 帐户](https://www.visualstudio.com/team-services/git/)并访问 Visual Studio 配置文件以查看存储库和项目 | <your account name> |
| **ProjectName** | VSTS 项目名称。 可以在 `https://{account name}.visualstudio.com/{project name}` 找到 VSTS 项目名称。 | <your VSTS project name> |
| **RepositoryName** | VSTS 代码存储库名称。 VSTS 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | <your VSTS code repository name> |
| **协作分支** | 将用于发布的 VSTS 协作分支。 默认为 `master`。 如果希望从其他分支发布资源，可对其进行更改。 | <your collaboration branch name> |
| **根文件夹** | VSTS 协作分支中的根文件夹。 | <your root folder name> |
| 确认选中“将现有的数据工厂资源导入存储库”选项。 | 指定是否从 UX 导入现有数据工厂资源**创作画布**入 VSTS Git 存储库中。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布
在 Azure 数据工厂 UX**创作画布**，找到你的数据工厂。 选择**数据工厂**下拉列表菜单，然后选择**配置代码存储库**。

此时将显示配置窗格。 有关配置设置的详细信息，请参阅中的说明<a href="#method1">配置方法 1</a>。

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>切换到不同 Git 存储库

若要切换到不同 Git 存储库，请在“数据工厂概述”页的右上角找到该图标，如以下屏幕截图所示。 如果看不到该图标，请清除本地浏览器缓存。 选择该图标以删除与当前存储库的关联。

删除与当前存储库的关联后，可以将 Git 设置配置为使用不同的存储库。 然后，可以将现有的数据工厂资源导入到新存储库。

![删除与当前 Git 存储库的关联。](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>使用版本控制
版本控制（也称为_源代码管理_）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

与数据工厂关联的每个 VSTS Git 存储库都有一个协作分支。 （默认协作分支为 `master`）。 用户还可以通过单击“+ 新建分支”创建功能分支，并在功能分支中进行开发工作。

![通过同步或发布更改代码](media/author-visually/sync-publish.png)

准备好功能分支的功能开发后，可以单击“创建拉取请求”。 这将引导你到 VSTS GIT，可以在这里发起拉取请求、完成代码评审并将更改合并至协作分支。 （默认分支为 `master`）。 只能从协作分支发布至数据工厂服务。 

![创建新的拉取请求](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>发布代码更改
将更改合并至协作分支（默认为 `master`）后，选择“发布”以将主分支中的代码更改手动发布至数据工厂服务。

![将更改发布至数据更改服务](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> 主分支不表示数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务。

## <a name="use-the-expression-language"></a>使用表达式语言
可使用 Azure 数据工厂支持的表达式语言，在定义属性值过程中指定表达式。 

通过选择“添加动态内容”，指定属性值的表达式：

![使用表达式语言](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>使用函数和参数

可以在数据工厂“表达式生成器”中使用函数或指定管道和数据集的参数：

有关受支持的表达式的更多信息，请参阅 [Azure 数据工厂中的表达式和函数](control-flow-expression-language-functions.md)。

![添加动态内容](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>提供反馈
选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题：

![反馈](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>后续步骤
若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
