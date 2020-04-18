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
ms.date: 01/09/2019
ms.openlocfilehash: 3007865c15ceb03b104282c29179ec59a8196b38
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604586"
---
# <a name="source-control-in-azure-data-factory"></a>Azure 数据工厂中的源代码管理
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂用户界面体验 （UX） 有两种体验可用于可视化创作：

- 直接使用数据工厂服务
- 使用 Azure 存储库 Git 或 GitHub 集成编写

> [!NOTE]
> Azure 政府云中仅支持直接使用数据工厂服务进行创作。

## <a name="author-directly-with-the-data-factory-service"></a>直接使用数据工厂服务

在直接使用数据工厂服务创作时，保存更改的唯一方法是通过 **"全部发布"** 按钮。 单击后，您所做的所有更改将直接发布到数据工厂服务。 

![发布模式](media/author-visually/data-factory-publish.png)

直接使用数据工厂服务创作有以下限制：

- 数据工厂服务不包括存储所做的更改的 JSON 实体的存储库。
- 数据工厂服务未优化协作或版本控制。

> [!NOTE]
> 配置 Git 存储库时，Azure 数据工厂 UX 中将禁用直接使用数据工厂服务进行创作。 可通过 PowerShell 或 SDK 直接对服务进行更改。

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 集成进行创作

使用 Azure Repos Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 Azure Repos Git 组织存储库相关联，以进行源代码管理、协作和版本控制等。 单个 Azure Repos Git 组织可以有多个存储库，但 Azure Repos Git 存储库只能与一个数据工厂相关联。 如果没有 Azure Repos 组织或存储库，请按照[这些说明](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)创建资源。

> [!NOTE]
> 可以在 Azure Repos Git 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 Azure Repos Git 存储库中存储的脚本或数据文件上传到 Azure 存储。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 Azure Repos Git 存储库

用户可通过两种方法使用数据工厂配置 Azure Repos Git 存储库。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>配置方法 1：Azure 数据工厂主页

在 Azure 数据工厂主页上，选择 **"设置代码存储库**"。

![配置 Azure Repos 代码存储库](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布
在 Azure 数据工厂 UX 创作画布中，选择 **"数据工厂**下拉菜单"，然后选择 **"设置代码存储库**"。

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

这两种方法都打开存储库设置配置窗格。

![配置存储库设置](media/author-visually/repo-settings.png)

配置窗格显示以下 Azure 存储库设置：

| 设置 | 说明 | 值 |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。<br/> | Azure 开发人员计划 Git 或 GitHub |
| **Azure Active Directory** | Azure AD 租户的名称。 | `<your tenant name>` |
| **Azure Repos 组织** | Azure Repos 组织名称。 可以在 `https://{organization name}.visualstudio.com` 中找到 Azure Repos 组织名称。 可以[登录到 Azure Repos 组织](https://www.visualstudio.com/team-services/git/)来访问 Visual Studio 配置文件并查看存储库和项目。 | `<your organization name>` |
| **项目名称** | Azure Repos 项目名称。 可以在 `https://{organization name}.visualstudio.com/{project name}` 中找到 Azure Repos 项目名称。 | `<your Azure Repos project name>` |
| **存储库名称** | Azure Repos 代码存储库名称。 Azure Repos 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | `<your Azure Repos code repository name>` |
| **协作分支** | 将用于发布的 Azure Repos 协作分支。 该名称默认为 `master`。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch name>` |
| **根文件夹** | Azure Repos 协作分支中的根文件夹。 | `<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。**** | 指定是否从 UX 创作画布**** 将现有数据工厂资源导入 Azure Repos Git 存储库。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

> [!NOTE]
> 如果使用 Microsoft Edge，并且 Azure DevOps 帐户下拉列表中看不到任何值，请将 https：//.visualstudio.com 添加到受信任的网站列表中。

### <a name="use-a-different-azure-active-directory-tenant"></a>使用不同的 Azure Active Directory 租户

可在不同的 Azure Active Directory 租户中创建 Azure Repos Git 存储库。 若要指定不同的 Azure AD 租户，必须对所用 Azure 订阅拥有管理员权限。

### <a name="use-your-personal-microsoft-account"></a>使用 Microsoft 个人帐户

若要将 Microsoft 个人帐户用于 Git 集成，可以将你的 Azure 个人存储库链接到贵组织的 Active Directory。

1. 将你的 Microsoft 个人帐户添加到贵组织的 Active Directory 以作为来宾。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/b2b/add-users-administrator.md)。

2. 使用 Microsoft 个人帐户登录 Azure 门户。 然后切换到贵组织的 Active Directory。

3. 请转到 Azure DevOps 部分，现在你可以在其中看到个人存储库。 选择存储库并连接到 Active Directory。

执行这些配置步骤之后，在数据工厂 UI 中设置 Git 集成时个人存储库可用。

有关将 Azure 存储库连接到贵组织的 Active Directory 的详细信息，请参阅[将 Azure DevOps 组织连接到 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)。

## <a name="author-with-github-integration"></a>使用 GitHub 集成进行创作

使用 GitHub 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 GitHub 帐户存储库相关联，以进行源代码管理、协作和版本控制。 单个的 GitHub 帐户可以有多个存储库，但 GitHub 存储库可以只有一个数据工厂与相关联。 如果您没有 GitHub 帐户或存储库，请按照 [这些说明](https://github.com/join) 创建资源。

GitHub 与数据工厂的集成同时支持公共 GitHub（即[https://github.com](https://github.com)）和 GitHub 企业版。 只要你对 GitHub 中的存储库具有读写权限，就可以将公共和专用 GitHub 存储库与数据工厂一起使用。

要配置 GitHub 存储库，您必须具有正在使用的 Azure 订阅的管理员权限。

有关此功能的 9 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 GitHub 存储库

用户可通过两种方法使用数据工厂配置 GitHub 存储库。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>配置方法 1：Azure 数据工厂主页

在 Azure 数据工厂主页上，选择 **"设置代码存储库**"。

![配置 Azure Repos 代码存储库](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布

在 Azure 数据工厂 UX 创作画布中，选择 **"数据工厂**下拉菜单"，然后选择 **"设置代码存储库**"。

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

这两种方法都打开存储库设置配置窗格。

![GitHub 存储库设置](media/author-visually/github-integration-image2.png)

配置窗格显示以下 GitHub 存储库设置：

| **设置** | **说明**  | **值**  |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。 | GitHub |
| **使用 GitHub Enterprise** | 用于选择 GitHub Enterprise 的复选框 | 未选中（默认） |
| **GitHub Enterprise URL** | GitHub 企业根 URL（必须是本地 GitHub 企业服务器的 HTTPS）。 例如：https://github.mydomain.com。 仅当选择**使用 GitHub 企业版**时才必 | `<your GitHub enterprise url>` |                                                           
| **GitHub 帐户** | GitHub 帐户名。 可从 https:\//github.com/{account name}/{repository name} 找到此名称。 导航到此页时，系统会提示输入 GitHub 帐户的 GitHub OAuth 凭据。 | `<your GitHub account name>` |
| **存储库名称**  | GitHub 代码存储库名称。 GitHub 帐户包含用于管理源代码的 Git 存储库。 可以创建新存储库，或使用帐户中现有的存储库。 | `<your repository name>` |
| **协作分支** | 将用于发布的 GitHub 协作分支。 默认情况下，它是主控形状。 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch>` |
| **根文件夹** | GitHub 协作分支中的根文件夹。 |`<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。**** | 指定是否将现有数据工厂资源从 UX 创作画布导入 GitHub 存储库。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

### <a name="known-github-limitations"></a>已知的 GitHub 限制

- 可在 GitHub 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 GitHub 存储库中存储的脚本或数据文件上传到 Azure 存储。

- 2.14.0 以下版本的 GitHub Enterprise 在 Microsoft Edge 浏览器中无法正常运行。

- GitHub 与数据工厂可视化创作工具的集成仅适用于数据工厂的普遍可用版本。

- 每个资源类型最多可以获取 1，000 个实体（如管道和数据集），可以从单个 GitHub 分支获取。 如果达到此限制，建议将您的资源拆分为单独的工厂。 Azure DevOps Git 没有此限制。

## <a name="switch-to-a-different-git-repo"></a>切换到不同 Git 存储库

要切换到其他 Git 存储库，请单击"数据工厂"概述页面右上角的**Git 存储库设置**图标。 如果看不到该图标，请清除本地浏览器缓存。 选择该图标以删除与当前存储库的关联。

![“Git”图标](media/author-visually/remove-repo.png)

出现"存储库设置"窗格后，选择 **"删除 Git**"。 输入数据工厂名称，然后单击 **"确认"** 以删除与数据工厂关联的 Git 存储库。

![删除与当前 Git 存储库的关联](media/author-visually/remove-repo2.png)

删除与当前存储库的关联后，可以将 Git 设置配置为使用不同的存储库，然后将现有数据工厂资源导入到新的存储库。 

## <a name="version-control"></a>版本控制

版本控制（也称为_源代码管理_）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

### <a name="creating-feature-branches"></a>创建要素分支

与数据工厂关联的每个 Azure Repos Git 存储库都有一个协作分支。 （默认协作分支为 `master`）。 用户还可以通过单击分支下拉列表中**的新分支**来创建功能分支。 出现新分支窗格后，输入要素分支的名称。

![创建新分支](media/author-visually/new-branch.png)

当您准备好将更改从要素分支合并到协作分支时，单击分支下拉，然后选择 **"创建拉取请求**"。 此操作会将你转到 Azure Repos Git，可以在这里发起拉取请求、完成代码评审并将更改合并至协作分支。 （默认分支为 `master`）。 只能从协作分支发布至数据工厂服务。 

![创建新的拉取请求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>配置发布设置

若要配置发布分支（即在其中保存资源管理器模板的分支），将 `publish_config.json` 文件添加到协作分支的根文件夹中。 数据工厂读取此文件，查找字段 `publishBranch`，并使用所提供的值创建新的分支（如果尚不存在）。 然后它将所有资源管理器模板都保存到指定位置。 例如：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

当指定新的发布分支，数据工厂不会删除以前的发布分支。 如果要删除以前的发布分支，请手动删除它。

> [!NOTE]
> 加载工厂时数据工厂仅读取 `publish_config.json` 文件。 如果已在门户中加载工厂，请刷新浏览器，以便所做的更改生效。

### <a name="publish-code-changes"></a>发布代码更改

合并协作分支的更改（`master`默认值）后，单击 **"发布"** 以手动将主分支中的代码更改发布到数据工厂服务。

![将更改发布至数据更改服务](media/author-visually/publish-changes.png)

将在其中打开一个侧窗格，确认发布分支和挂起的更改是正确的。 验证更改后，单击"**确定"** 以确认发布。

![确认正确的发布分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主分支不表示数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务**。

## <a name="advantages-of-git-integration"></a>Git 集成的优点

-   **源代码管理**。 随着数据工厂工作负载变得至关重要，你可能需要将工厂与 Git 集成，以利用以下几种源代码管理优势：
    -   跟踪/审核更改的功能。
    -   还原导致 bug 的更改的功能。
-   **部分保存**。 当您在工厂中进行大量更改时，您会意识到，在常规 LIVE 模式下，无法将更改另存为草稿，因为您尚未准备好，或者不希望在计算机崩溃时丢失更改。 通过 Git 集成，可以继续以增量方式保存更改，并且仅在准备就绪后才发布到工厂。 Git 会充当工作的暂存位置，直到对更改进行测试并感到满意为止。
-   **协作和控制**。 如果有多个团队成员参与同一工厂，则可能需要通过代码评审流程让团队成员相互协作。 此外，还可以设置工厂，以免工厂的每一位参与者都有权部署到工厂。 可以仅允许团队成员通过 Git 进行更改，但只允许团队中的某些人将更改“发布”到工厂。
-   **显示差异**。 在 Git 模式下，您可以看到即将发布到工厂的有效负载的一个很好的差异。 此差异显示了自上次发布到工厂后已修改/添加/删除的所有资源/实体。 基于此差异，可以继续进行发布，也可以返回并检查更改，稍后再回来。
-   **更好的 CI/CD**。 如果使用的是 Git 模式，则可以将发布管道配置为开发工厂中发生任何更改后立即自动触发。 此外，还可以自定义工厂中可用作资源管理器模板的参数的属性。 仅将所需属性集保留为参数并将其他所有内容进行硬编码可能很有用。
-   **性能更好**。 在 Git 模式下，平均工厂加载速度比常规 LIVE 模式快十倍，因为资源是通过 Git 下载的。

## <a name="best-practices-for-git-integration"></a>Git 集成的最佳做法

### <a name="permissions"></a>权限

通常，您不希望每个团队成员都具有更新工厂的权限。 建议设置以下权限：

*   所有团队成员都应具有对数据工厂的读取权限。
*   只允许一组选定的人员发布到工厂。 为此，他们必须在工厂所位于的资源组中具有**数据工厂参与者**角色。 有关权限的详细信息，请参阅[Azure 数据工厂的角色和权限](concepts-roles-permissions.md)。
   
建议不允许直接签入协作分支。 此限制有助于防止 Bug，因为每次签入都将通过["创建功能分支](source-control.md#creating-feature-branches)"中描述的拉取请求审阅过程。

### <a name="using-passwords-from-azure-key-vault"></a>使用 Azure 密钥保管库的密码

建议使用 Azure 密钥保管库来存储数据工厂链接服务的任何连接字符串或密码。 出于安全原因，我们不会在 Git 中存储任何此类机密信息，因此对链接服务的任何更改将立即发布到 Azure 数据工厂服务。

使用密钥保管库还使持续集成和部署更容易，因为在资源管理器模板部署期间不必提供这些机密。

## <a name="troubleshooting-git-integration"></a>故障排除 Git 集成

### <a name="stale-publish-branch"></a>陈旧发布分支

如果发布分支与主分支不同步，并且包含最近发布的过期资源，请尝试执行以下步骤：

1. 删除当前 Git 存储库
1. 使用相同的设置重新配置 Git，但请确保选择**将现有数据工厂资源导入存储库**并选择 **"新建分支**"
1. 创建拉取请求以合并到协作分支的更改 

下面是可能导致发布分支陈旧的一些情况示例：
- 用户有多个分支。 在一个功能分支中，他们删除了非 AKV 关联的链接服务（无论它们是否在 Git 中，都会立即发布非 AKV 链接服务），并且从未将功能分支合并到协作 brnach 中。
- 用户使用 SDK 或 PowerShell 修改数据工厂
- 用户将所有资源移动到新分支，并首次尝试发布。 导入资源时，应手动创建链接的服务。
- 用户手动上载非 AKV 链接服务或集成运行时 JSON。 它们引用来自其他资源（如数据集、链接服务或管道）的资源。 通过 UX 创建的非 AKV 链接服务将立即发布，因为凭据需要加密。 如果上载引用该链接服务的数据集并尝试发布，UX 将允许它，因为它存在于 git 环境中。 它将在发布时被拒绝，因为它在数据工厂服务中不存在。

## <a name="provide-feedback"></a>提供反馈
选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题****：

![反馈](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>后续步骤

* 若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
* 要实现持续集成和部署，请参阅[Azure 数据工厂中的连续集成和交付 （CI/CD）。](continuous-integration-deployment.md)
