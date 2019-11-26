---
title: Source control in Azure Data Factory
description: Learn how to configure source control in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 264c60c719ffdd94664ae3a85fc67894d14f394d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484465"
---
# <a name="source-control-in-azure-data-factory"></a>Source control in Azure Data Factory

The Azure Data Factory user interface experience (UX) has two experiences available for visual authoring:

- 直接使用数据工厂服务
- Author with Azure Repos Git or GitHub integration

> [!NOTE]
> Only authoring directly with the Data Factory service is supported in the Azure Government Cloud.

## <a name="author-directly-with-the-data-factory-service"></a>直接使用数据工厂服务

While authoring directly with the Data Factory service, the only way to save changes is via the **Publish All** button. Once clicked, all changes that you made are published directly to the Data Factory service. 

![发布模式](media/author-visually/data-factory-publish.png)

Authoring directly with the Data Factory service has the following limitations:

- 数据工厂服务不包括存储所做的更改的 JSON 实体的存储库。
- 数据工厂服务未优化协作或版本控制。

> [!NOTE]
> Authoring directly with the Data Factory service is disabled in the Azure Data Factory UX when a Git repository is configured. Changes can be made directly to the service via PowerShell or an SDK.

## <a name="author-with-azure-repos-git-integration"></a>使用 Azure Repos Git 集成进行创作

使用 Azure Repos Git 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 Azure Repos Git 组织存储库相关联，以进行源代码管理、协作和版本控制等。 单个 Azure Repos Git 组织可以有多个存储库，但 Azure Repos Git 存储库只能与一个数据工厂相关联。 如果没有 Azure Repos 组织或存储库，请按照[这些说明](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)创建资源。

> [!NOTE]
> 可以在 Azure Repos Git 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 Azure Repos Git 存储库中存储的脚本或数据文件上传到 Azure 存储。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>使用 Azure 数据工厂配置 Azure Repos Git 存储库

用户可通过两种方法使用数据工厂配置 Azure Repos Git 存储库。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuration method 1: Azure Data Factory home page

On the Azure Data Factory home page, select **Set up Code Repository**.

![配置 Azure Repos 代码存储库](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布
In the Azure Data Factory UX authoring canvas, select the **Data Factory** drop-down menu, and then select **Set up Code Repository**.

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

Both methods open the repository settings configuration pane.

![配置存储库设置](media/author-visually/repo-settings.png)

The configuration pane shows the following Azure Repos code repository settings:

| 设置 | 描述 | Value |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。<br/> | Azure DevOps Git or GitHub |
| **Azure Active Directory** | Azure AD 租户的名称。 | `<your tenant name>` |
| **Azure Repos 组织** | Azure Repos 组织名称。 可以在 `https://{organization name}.visualstudio.com` 中找到 Azure Repos 组织名称。 可以[登录到 Azure Repos 组织](https://www.visualstudio.com/team-services/git/)来访问 Visual Studio 配置文件并查看存储库和项目。 | `<your organization name>` |
| **ProjectName** | Azure Repos 项目名称。 可以在 `https://{organization name}.visualstudio.com/{project name}` 中找到 Azure Repos 项目名称。 | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 代码存储库名称。 Azure Repos 项目包含 Git 存储库，随着项目的发展来管理源代码。 要么创建一个新的存储库，要么使用项目中已有的存储库。 | `<your Azure Repos code repository name>` |
| **协作分支** | 将用于发布的 Azure Repos 协作分支。 By default, it’s `master`. 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch name>` |
| **根文件夹** | Azure Repos 协作分支中的根文件夹。 | `<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。 | 指定是否从 UX 创作画布将现有数据工厂资源导入 Azure Repos Git 存储库。 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

> [!NOTE]
> If you are using Microsoft Edge and do not see any values in your Azure DevOps Account dropdown, add https://*.visualstudio.com to the trusted sites list.

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

使用 GitHub 集成进行创作允许在创作数据工厂管道时进行源代码管理和协作。 用户可以选择将数据工厂与 GitHub 帐户存储库相关联，以进行源代码管理、协作和版本控制。 单个的 GitHub 帐户可以有多个存储库，但 GitHub 存储库可以只有一个数据工厂与相关联。 如果没有 GitHub 帐户或存储库，请按照 [这些说明](https://github.com/join) 创建资源。

GitHub 与数据工厂的集成支持公共 GitHub（即 [https://github.com](https://github.com)）和 GitHub Enterprise。 只要你对 GitHub 中的存储库具有读写权限，就可以将公共和专用 GitHub 存储库与数据工厂一起使用。

To configure a GitHub repo, you must have administrator permissions for the Azure subscription that you're using.

有关此功能的 9 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configure a GitHub repository with Azure Data Factory

用户可通过两种方法使用数据工厂配置 GitHub 存储库。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuration method 1: Azure Data Factory home page

On the Azure Data Factory home page, select **Set up Code Repository**.

![配置 Azure Repos 代码存储库](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>配置方法 2：UX 创作画布

In the Azure Data Factory UX authoring canvas, select the **Data Factory** drop-down menu, and then select **Set up Code Repository**.

![配置 UX 创作的代码存储库设置](media/author-visually/configure-repo-2.png)

Both methods open the repository settings configuration pane.

![GitHub 存储库设置](media/author-visually/github-integration-image2.png)

The configuration pane shows the following GitHub repository settings:

| **设置** | **说明**  | **值**  |
|:--- |:--- |:--- |
| **存储库类型** | Azure Repos 代码存储库的类型。 | GitHub |
| **使用 GitHub Enterprise** | 用于选择 GitHub Enterprise 的复选框 | unselected (default) |
| **GitHub Enterprise URL** | GitHub Enterprise 根 URL。 例如： https://github.mydomain.com 。 Required only if **Use GitHub Enterprise** is selected | `<your GitHub enterprise url>` |                                                           
| **GitHub 帐户** | GitHub 帐户名。 This name can be found from https:\//github.com/{account name}/{repository name}. 导航到此页时，系统会提示输入 GitHub 帐户的 GitHub OAuth 凭据。 | `<your GitHub account name>` |
| **Repository Name**  | GitHub 代码存储库名称。 GitHub 帐户包含用于管理源代码的 Git 存储库。 可以创建新存储库，或使用帐户中现有的存储库。 | `<your repository name>` |
| **协作分支** | 将用于发布的 GitHub 协作分支。 By default, its master. 如果希望从其他分支发布资源，可更改此设置。 | `<your collaboration branch>` |
| **根文件夹** | GitHub 协作分支中的根文件夹。 |`<your root folder name>` |
| 确认选中“将现有的数据工厂资源导入存储库”选项。 | Specifies whether to import existing data factory resources from the UX authoring canvas into a GitHub repository. 选择相应的框以将你的数据工厂资源导入 JSON 格式关联的 Git 存储库。 此操作单独导出每个资源（即，链接的服务和数据集导出到单独的 JSON）。 如果未选中此框，不能导入现有的资源。 | 已选择（默认） |
| **要将资源导入到的分支** | 指定要将数据工厂资源（管道、数据集、链接服务等）导入哪个分支。 可将资源导入以下分支之一：a. 协作；b. 新建；c. 使用现有项 |  |

### <a name="known-github-limitations"></a>Known GitHub limitations

- 可在 GitHub 存储库中存储脚本和数据文件。 但是，必须手动将文件上传到 Azure 存储。 数据工厂管道不会自动将 GitHub 存储库中存储的脚本或数据文件上传到 Azure 存储。

- 2\.14.0 以下版本的 GitHub Enterprise 在 Microsoft Edge 浏览器中无法正常运行。

- GitHub integration with the Data Factory visual authoring tools only works in the generally available version of Data Factory.

## <a name="switch-to-a-different-git-repo"></a>切换到不同 Git 存储库

To switch to a different Git repo, click the **Git Repo Settings** icon in the upper right corner of the Data Factory overview page. 如果看不到该图标，请清除本地浏览器缓存。 选择该图标以删除与当前存储库的关联。

![Git icon](media/author-visually/remove-repo.png)

Once the Repository Settings pane appears, select **Remove Git**. Enter your data factory name and click **confirm** to remove the Git repository associated with your data factory.

![删除与当前 Git 存储库的关联](media/author-visually/remove-repo2.png)

After you remove the association with the current repo, you can configure your Git settings to use a different repo and then import existing Data Factory resources to the new repo. 

## <a name="version-control"></a>版本控制

版本控制（也称为_源代码管理_）系统允许开发人员协作处理代码并跟踪对基本代码所做的更改。 源代码管理是用于多开发人员项目的重要工具。

### <a name="creating-feature-branches"></a>Creating feature branches

与数据工厂关联的每个 Azure Repos Git 存储库都有一个协作分支。 （默认协作分支为 `master`）。 Users can also create feature branches by clicking **+ New Branch** in the branch dropdown. Once the new branch pane appears, enter the name of your feature branch.

![Create a new branch](media/author-visually/new-branch.png)

When you are ready to merge the changes from your feature branch to your collaboration branch, click on the branch dropdown and select **Create pull request**. 此操作会将你转到 Azure Repos Git，可以在这里发起拉取请求、完成代码评审并将更改合并至协作分支。 （默认分支为 `master`）。 只能从协作分支发布至数据工厂服务。 

![创建新的拉取请求](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>配置发布设置

若要配置发布分支（即在其中保存资源管理器模板的分支），将 `publish_config.json` 文件添加到协作分支的根文件夹中。 数据工厂读取此文件，查找字段 `publishBranch`，并使用所提供的值创建新的分支（如果尚不存在）。 然后它将所有资源管理器模板都保存到指定位置。 例如：

```json
{
    "publishBranch": "factory/adf_publish"
}
```

当指定新的发布分支，数据工厂不会删除以前的发布分支。 If you want to remove the previous publish branch, delete it manually.

> [!NOTE]
> 加载工厂时数据工厂仅读取 `publish_config.json` 文件。 如果已在门户中加载工厂，请刷新浏览器，以便所做的更改生效。

### <a name="publish-code-changes"></a>发布代码更改

After you have merged changes to the collaboration branch (`master` is the default), click **Publish** to manually publish your code changes in the master branch to the Data Factory service.

![将更改发布至数据更改服务](media/author-visually/publish-changes.png)

A side pane will open where you confirm that the publish branch and pending changes are correct. Once you verify your changes, click **OK** to confirm the publish.

![确认正确的发布分支](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 主分支不表示数据工厂服务中部署的内容。 必须将主分支手动发布到数据工厂服务。

## <a name="advantages-of-git-integration"></a>Git 集成的优点

-   **源代码管理**。 随着数据工厂工作负载变得至关重要，你可能需要将工厂与 Git 集成，以利用以下几种源代码管理优势：
    -   跟踪/审核更改的功能。
    -   还原导致 bug 的更改的功能。
-   **部分保存**。 在工厂中进行大量更改后，便将发现，在常规实时模式下，无法将更改保存为草稿，这是因为你尚未准备就绪，或者是因为你不希望因电脑发生故障而丢失更改。 通过 Git 集成，可以继续以增量方式保存更改，并且仅在准备就绪后才发布到工厂。 Git 会充当工作的暂存位置，直到对更改进行测试并感到满意为止。
-   **协作和控制**。 如果有多个团队成员参与同一工厂，则可能需要通过代码评审流程让团队成员相互协作。 此外，还可以设置工厂，以免工厂的每一位参与者都有权部署到工厂。 可以仅允许团队成员通过 Git 进行更改，但只允许团队中的某些人将更改“发布”到工厂。
-   **显示差异**。 在 Git 模式下，可以看到即将发布到工厂的有效负载的精细差异。 此差异显示了自上次发布到工厂后已修改/添加/删除的所有资源/实体。 基于此差异，可以继续进行发布，也可以返回并检查更改，稍后再回来。
-   **更好的 CI/CD**。 如果使用的是 Git 模式，则可以将发布管道配置为开发工厂中发生任何更改后立即自动触发。 此外，还可以自定义工厂中可用作资源管理器模板的参数的属性。 仅将所需属性集保留为参数并将其他所有内容进行硬编码可能很有用。
-   **性能更好**。 An average factory loads ten times faster in Git mode than in regular LIVE mode, because the resources are downloaded via Git.

## <a name="best-practices-for-git-integration"></a>Git 集成的最佳做法

### <a name="permissions"></a>权限

Typically you don’t want every team member to have permissions to update the factory. The following permissions settings are recommended:

*   所有团队成员都应具有对数据工厂的读取权限。
*   Only a select set of people should be allowed to publish to the factory. To do so, they must have the **Data Factory contributor** role on the factory. For more information on permissions, see [Roles and permissions for Azure Data Factory](concepts-roles-permissions.md).
   
It's recommended to not allow direct check-ins to the collaboration branch. This restriction can help prevent bugs as every check-in will go through a pull request review process described in [Creating feature branches](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Using passwords from Azure Key Vault

its recommended to use Azure Key Vault to store any connection strings or passwords for Data Factory Linked Services. For security reasons, we don’t store any such secret information in Git, so any changes to Linked Services are published immediately to the Azure Data Factory service.

Using Key Vault also makes continuous integration and deployment easier as you will not have to provide these secrets during Resource Manager template deployment.

## <a name="troubleshooting-git-integration"></a>Troubleshooting Git integration

### <a name="stale-publish-branch"></a>Stale publish branch

If the publish branch is out of sync with the master branch and contains out-of-date resources despite a recent publish, try following these steps:

1. Remove your current Git repository
1. Reconfigure Git with the same settings, but make sure **Import existing Data Factory resources to repository** is selected and choose **New branch**
1. Delete all resources from your collaboration branch
1. Create a pull request to merge the changes to the collaboration branch 

## <a name="provide-feedback"></a>提供反馈
选择“反馈”可发表有关功能的看法，或者告知 Microsoft 出现了工具问题：

![反馈](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>后续步骤

* 若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
* To implement continuous integration and deployment, see [Continuous integration and delivery (CI/CD) in Azure Data Factory](continuous-integration-deployment.md).
