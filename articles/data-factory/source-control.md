---
title: 源代码管理
description: 了解如何在 Azure 数据工厂中配置源代码管理
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/30/2020
ms.openlocfilehash: f327844be57d7f8e177f3bf72b1e3b56c5147e00
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629314"
---
# <a name="source-control-in-azure-data-factory"></a>Azure 数据工厂中的源代码管理
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

默认情况下，Azure 数据工厂用户界面体验（UX）作者直接针对数据工厂服务。 此体验具有以下限制：

- 数据工厂服务不包括存储所做的更改的 JSON 实体的存储库。 保存更改的唯一方法是通过 "**全部发布**" 按钮，并将所有更改直接发布到数据工厂服务。
- 数据工厂服务未针对协作和版本控制进行优化。

为了提供更好的创作体验，Azure 数据工厂允许使用 Azure Repos 或 GitHub 配置 Git 存储库。 Git 是一种版本控制系统，可用于更轻松地进行更改跟踪和协作。 本教程概述了如何在 git 存储库中配置和工作，并突出显示了最佳实践和故障排除指南。

> [!NOTE]
> Azure 政府版云中未提供 azure 数据工厂 git 集成。

## <a name="advantages-of-git-integration"></a>Git 集成的优点

下面列出了 git 集成为创作体验提供的一些优点：

-   **源代码管理：** 随着数据工厂工作负荷变得至关重要，你需要将工厂与 Git 集成，以利用以下几个源代码管理的优点：
    -   跟踪/审核更改的功能。
    -   还原导致 bug 的更改的功能。
-   **部分保存：** 针对数据工厂服务进行创作时，无法将更改保存为草稿，所有发布都必须通过数据工厂验证。 无论你的管道是否未完成，或者你不希望在计算机崩溃的情况下丢失更改，git 集成都允许对数据工厂资源进行增量更改，而不管它们处于何种状态。 通过配置 git 存储库，你可以保存更改，使你能够仅在测试了你对你的满意度所做的更改时发布。
-   **协作和控制：** 如果有多个参与同一工厂的团队成员，则可能需要让你的团队成员通过代码审阅过程相互合作。 你还可以设置工厂，以便不是每个参与者都具有相同的权限。 某些团队成员可能只允许通过 Git 进行更改，并且仅允许团队中的某些人员将更改发布到工厂。
-   **更好的 CI/CD：** 如果要部署到具有[持续交付过程](continuous-integration-deployment.md)的多个环境，git 集成使某些操作更容易。 部分此类操作包括：
    -   将发布管道配置为在对 "dev" 工厂进行任何更改后自动触发。
    -   自定义工厂中可用作资源管理器模板中的参数的属性。 仅将所需属性集保留为参数并将其他所有内容进行硬编码可能很有用。
-   **更好的性能：** 带有 git 集成的平均工厂负载比针对数据工厂服务的一次创作快10倍。 性能改进是因为资源是通过 Git 下载的。

> [!NOTE]
> 如果配置了 Git 存储库，则在 Azure 数据工厂 UX 中，直接使用数据工厂服务进行创作是禁用的。 可以通过 PowerShell 或 SDK 直接对服务进行更改。

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 集成进行创作

使用 Azure Repos Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 Azure Repos Git 组织存储库相关联，以进行源代码管理、协作和版本控制等。 单个 Azure Repos Git 组织可以有多个存储库，但 Azure Repos Git 存储库只能与一个数据工厂相关联。 如果没有 Azure Repos 组织或存储库，请按照[这些说明](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)创建资源。

> [!NOTE]
> 可以在 Azure Repos Git 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 Azure Repos Git 存储库中存储的脚本或数据文件上传到 Azure 存储。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 Azure Repos Git 存储库

用户可通过两种方法使用数据工厂配置 Azure Repos Git 存储库。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>配置方法1： Azure 数据工厂主页

在 "Azure 数据工厂" 主页上，选择 "**设置代码存储库**"。

![配置 Azure Repos 代码存储库](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布
在 Azure 数据工厂 UX 创作画布中，选择 "**数据工厂**" 下拉菜单，然后选择 "**设置代码存储库**"。

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

这两种方法都将打开 "存储库设置" 配置窗格。

![配置存储库设置](media/author-visually/repo-settings.png)

"配置" 窗格显示以下 Azure Repos 代码存储库设置：

| 设置 | 说明 | 值 |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。<br/> | Azure DevOps Git 或 GitHub |
| **Azure Active Directory** | Azure AD 租户的名称。 | `<your tenant name>` |
| **Azure Repos 组织** | Azure Repos 组织名称。 可以在 `https://{organization name}.visualstudio.com` 中找到 Azure Repos 组织名称。 可以[登录到 Azure Repos 组织](https://www.visualstudio.com/team-services/git/)来访问 Visual Studio 配置文件并查看存储库和项目。 | `<your organization name>` |
| **ProjectName** | Azure Repos 项目名称。 可以在 `https://{organization name}.visualstudio.com/{project name}` 中找到 Azure Repos 项目名称。 | `<your Azure Repos project name>` |
| **Event.pushnotification.repositoryname** | Azure Repos 代码存储库名称。 Azure Repos 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | `<your Azure Repos code repository name>` |
| **协作分支** | 将用于发布的 Azure Repos 协作分支。 默认情况下， `master`它的。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch name>` |
| **根文件夹** | Azure Repos 协作分支中的根文件夹。 | `<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。**** | 指定是否从 UX 创作画布**** 将现有数据工厂资源导入 Azure Repos Git 存储库。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

> [!NOTE]
> 如果你使用的是 Microsoft Edge，而在 Azure DevOps 帐户下拉列表中看不到任何值，请将 https：//visualstudio 添加到受信任的站点列表。

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租户

Azure Repos Git 存储库可以位于不同的 Azure Active Directory 租户中。 若要指定不同的 Azure AD 租户，必须对所用 Azure 订阅拥有管理员权限。

### <a name="use-your-personal-microsoft-account"></a>使用 Microsoft 个人帐户

若要将 Microsoft 个人帐户用于 Git 集成，可以将你的 Azure 个人存储库链接到贵组织的 Active Directory。

1. 将你的 Microsoft 个人帐户添加到贵组织的 Active Directory 以作为来宾。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/b2b/add-users-administrator.md)。

2. 使用 Microsoft 个人帐户登录 Azure 门户。 然后切换到贵组织的 Active Directory。

3. 请转到 Azure DevOps 部分，现在你可以在其中看到个人存储库。 选择存储库并连接到 Active Directory。

执行这些配置步骤之后，在数据工厂 UI 中设置 Git 集成时个人存储库可用。

有关将 Azure 存储库连接到贵组织的 Active Directory 的详细信息，请参阅[将 Azure DevOps 组织连接到 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="author-with-github-integration"></a>使用 GitHub 集成进行创作

使用 GitHub 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 GitHub 帐户存储库相关联，以进行源代码管理、协作和版本控制。 单个的 GitHub 帐户可以有多个存储库，但 GitHub 存储库可以只有一个数据工厂与相关联。 如果没有 GitHub 帐户或存储库，请按照 [以下说明](https://github.com/join) 创建资源。

GitHub 与数据工厂的集成支持公共 GitHub （即[https://github.com](https://github.com)）和 GitHub 企业。 只要你对 GitHub 中的存储库具有读写权限，就可以将公共和专用 GitHub 存储库与数据工厂一起使用。

若要配置 GitHub 存储库，你必须对所使用的 Azure 订阅具有管理员权限。

有关此功能的 9 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 GitHub 存储库

用户可通过两种方法使用数据工厂配置 GitHub 存储库。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>配置方法1： Azure 数据工厂主页

在 "Azure 数据工厂" 主页上，选择 "**设置代码存储库**"。

![配置 Azure Repos 代码存储库](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布

在 Azure 数据工厂 UX 创作画布中，选择 "**数据工厂**" 下拉菜单，然后选择 "**设置代码存储库**"。

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

这两种方法都将打开 "存储库设置" 配置窗格。

![GitHub 存储库设置](media/author-visually/github-integration-image2.png)

"配置" 窗格显示以下 GitHub 存储库设置：

| **设置** | **说明**  | **值**  |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。 | GitHub |
| **使用 GitHub Enterprise** | 用于选择 GitHub Enterprise 的复选框 | 未选择（默认值） |
| **GitHub Enterprise URL** | GitHub 企业根 URL （对于本地 GitHub 企业服务器必须为 HTTPS）。 例如：`https://github.mydomain.com`。 仅当选择了 "**使用 GitHub Enterprise** " 时才是必需的 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帐户** | GitHub 帐户名。 可从 https:\//github.com/{account name}/{repository name} 找到此名称。 导航到此页时，系统会提示输入 GitHub 帐户的 GitHub OAuth 凭据。 | `<your GitHub account name>` |
| **存储库名称**  | GitHub 代码存储库名称。 GitHub 帐户包含用于管理源代码的 Git 存储库。 可以创建新存储库，或使用帐户中现有的存储库。 | `<your repository name>` |
| **协作分支** | 将用于发布的 GitHub 协作分支。 默认情况下，它的主节点。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch>` |
| **根文件夹** | GitHub 协作分支中的根文件夹。 |`<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。**** | 指定是否将现有数据工厂资源从 UX 创作画布导入到 GitHub 存储库中。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

### <a name="known-github-limitations"></a>已知 GitHub 限制

- 可在 GitHub 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 GitHub 存储库中存储的脚本或数据文件上传到 Azure 存储。

- 2.14.0 以下版本的 GitHub Enterprise 在 Microsoft Edge 浏览器中无法正常运行。

- 与数据工厂视觉创作工具的 GitHub 集成仅适用于数据工厂的公开版本。

- 每个资源类型（如管道和数据集）最多可以从单个 GitHub 分支提取1000个实体。 如果达到此限制，则建议将资源拆分为单独的工厂。 Azure DevOps Git 没有此限制。

## <a name="version-control"></a>版本控制

版本控制（也称为_源代码管理_）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

### <a name="creating-feature-branches"></a>创建功能分支

与数据工厂关联的每个 Azure Repos Git 存储库都有一个协作分支。 （默认协作分支为 `master`）。 用户还可以通过在 "分支" 下拉列表中单击 " **+ 新建分支**" 来创建功能分支。 显示 "新分支" 窗格后，输入功能分支的名称。

![创建新分支](media/author-visually/new-branch.png)

准备好将功能分支中的更改合并到协作分支时，请单击分支下拉列表并选择 "**创建拉取请求**"。 此操作会将你转到 Azure Repos Git，可以在这里发起拉取请求、完成代码评审并将更改合并至协作分支。 （默认分支为 `master`）。 只能从协作分支发布至数据工厂服务。 

![创建新的拉取请求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>配置发布设置

默认情况下，数据工厂生成已发布工厂的资源管理器模板，并将其保存到名`adf_public`为的分支中。 若要配置自定义发布分支，请`publish_config.json`将文件添加到协作分支的根文件夹中。 发布时，ADF 会读取此文件，查找字段`publishBranch`，并将所有资源管理器模板保存到指定位置。 如果该分支不存在，数据工厂会自动创建它。 下面是此文件的外观示例：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure 数据工厂一次只能有一个发布分支。 当指定新的发布分支，数据工厂不会删除以前的发布分支。 如果要删除以前的发布分支，请手动将其删除。

> [!NOTE]
> 加载工厂时数据工厂仅读取 `publish_config.json` 文件。 如果已在门户中加载工厂，请刷新浏览器，以便所做的更改生效。

### <a name="publish-code-changes"></a>发布代码更改

将更改合并到协作分支（`master`默认值）后，单击 "**发布**" 以将主分支中的代码更改手动发布到数据工厂服务。

![将更改发布至数据更改服务](media/author-visually/publish-changes.png)

此时会打开一个侧窗格，你可以在其中确认发布分支和挂起的更改是正确的。 验证更改后，单击 **"确定"** 以确认发布。

![确认正确的发布分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主分支不表示数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务**。

## <a name="best-practices-for-git-integration"></a>Git 集成的最佳做法

### <a name="permissions"></a>权限

通常不希望每个团队成员都有权更新工厂。 建议使用以下权限设置：

*   所有团队成员都应具有对数据工厂的读取权限。
*   只应允许一组选择的人员发布到工厂。 为此，它们必须具有工厂所在的资源组的 "**数据工厂参与者**" 角色。 有关权限的详细信息，请参阅[Azure 数据工厂的角色和权限](concepts-roles-permissions.md)。
   
建议不允许直接签入到协作分支。 此限制可帮助防止 bug，因为每次签入都会经历[创建功能分支](source-control.md#creating-feature-branches)中所述的拉取请求审核过程。

### <a name="using-passwords-from-azure-key-vault"></a>使用 Azure Key Vault 中的密码

建议使用 Azure Key Vault 为数据工厂链接服务存储任何连接字符串或密码或托管标识身份验证。 出于安全原因，数据工厂不会在 Git 中存储机密。 对包含机密（如密码）的链接服务所做的任何更改都会立即发布到 Azure 数据工厂服务。

使用 Key Vault 或 MSI 身份验证还可以更轻松地进行持续集成和部署，因为在资源管理器模板部署期间不必提供这些机密。

## <a name="troubleshooting-git-integration"></a>Git 集成故障排除

### <a name="stale-publish-branch"></a>过时发布分支

如果发布分支与主分支不同步并且包含过期资源（尽管最近发布），请尝试执行以下步骤：

1. 删除当前的 Git 存储库
1. 用相同的设置重新配置 Git，但请确保已选择 "**将现有的数据工厂资源导入存储库**"，然后选择 "**新建分支**"
1. 创建用于将更改合并到协作分支的拉取请求 

下面是可能导致过时发布分支的一些情况示例：
- 用户有多个分支。 在一个功能分支中，它们删除了未 AKV 关联的链接服务（将立即发布非 AKV 链接服务，而不考虑它们是否在 Git 中，而不会立即发布，而不会将功能分支合并到协作分支中）。
- 用户使用 SDK 或 PowerShell 修改了数据工厂
- 用户将所有资源移至新的分支，并尝试第一次发布。 导入资源时应手动创建链接服务。
- 用户手动上传非 AKV 链接服务或 Integration Runtime JSON。 它们从另一个资源（例如数据集、链接服务或管道）引用资源。 通过 UX 创建的非 AKV 链接服务立即发布，因为需要对凭据进行加密。 如果上传引用此链接服务的数据集，并尝试发布，则 UX 将允许其，因为它存在于 git 环境中。 它将在发布时被拒绝，因为它在数据工厂服务中不存在。

## <a name="switch-to-a-different-git-repository"></a>切换到另一个 Git 存储库

若要切换到另一个 Git 存储库，请单击 "数据工厂概述" 页右上角的 "Git 存储库**设置**" 图标。 如果看不到该图标，请清除本地浏览器缓存。 选择该图标以删除与当前存储库的关联。

![“Git”图标](media/author-visually/remove-repo.png)

显示 "存储库设置" 窗格后，选择 "**删除 Git**"。 输入数据工厂名称，并单击 "**确认**" 以删除与数据工厂关联的 Git 存储库。

![删除与当前 Git 存储库的关联](media/author-visually/remove-repo2.png)

删除与当前存储库的关联后，可以将 Git 设置配置为使用不同的存储库，然后将现有的数据工厂资源导入到新的存储库。

> [!IMPORTANT]
> 从数据工厂中删除 Git 配置不会从存储库中删除任何内容。 工厂将包含所有已发布的资源。 你可以继续直接针对服务编辑工厂。

## <a name="next-steps"></a>后续步骤

* 若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
* 若要实现持续集成和部署，请参阅[Azure 数据工厂中的持续集成和交付（CI/CD）](continuous-integration-deployment.md)。
