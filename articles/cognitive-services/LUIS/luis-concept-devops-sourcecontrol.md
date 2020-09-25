---
title: 源代码管理和开发分支 - LUIS
description: 如何维护源代码管理下的语言理解 (LUIS) 应用。 如何在使用开发分支时将更新应用到 LUIS 应用。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 25f2c4f4698785326f80c24d3749e7585e85d5bb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309500"
---
# <a name="devops-practices-for-luis"></a>LUIS 的 DevOps 实践

正在开发语言理解 (LUIS) 应用的软件工程师可以通过遵循这些指南，对[源代码管理](luis-concept-devops-sourcecontrol.md)、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)应用 DevOps 实践。

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS 的源代码管理和分支策略

DevOps 成功的关键因素之一是[源代码管理](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)。 源代码管理系统允许开发人员围绕代码开展协作并跟踪更改。 使用分支可允许开发人员在不同版本的代码库之间进行切换，并允许独立于团队的其他成员开展工作。 开发人员提出[拉取请求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests)提议从一个分支更新到另一个分支时，或合并更改时，可能会触发[自动生成](luis-concept-devops-automation.md)进程生成并连续测试代码。

通过使用本文档所述的概念和指南，你可以在跟踪源代码管理系统中的更改的同时开发 LUIS 应用程序，然后按照以下软件工程最佳做法进行操作：

- **源代码管理**
  - LUIS 应用的源代码采用人类易于阅读的格式。
  - 可以反复从源生成模型。
  - 可以通过源代码存储库管理源代码。
  - 凭据和机密（例如创作和订阅密钥）永远不会存储在源代码中。

- **分支和合并**
  - 开发人员可以使用独立分支。
  - 开发人员可以同时使用多个分支。
  - 可以通过变基或合并将 LUIS 应用的更改从一个分支集成到另一个分支。
  - 开发人员可以将 PR 合并到父分支。

- **版本控制**
  - 应对大型应用程序中的每个组件单独进行版本控制，使开发人员可以通过直接查看版本号来检测重大更改或更新。

- **代码评审**
  - PR 中的更改显示为易于阅读的源代码，可以在接受 PR 之前查看该代码。

## <a name="source-control"></a>源代码管理

若要在源代码管理系统中维护 LUIS 应用的[应用架构定义](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition)，请使用应用的 [LUDown 格式 (`.lu`)](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0) 表示形式。 `.lu` 格式优于 `.json` 格式，因为前者易于阅读，这使开发人员可以更轻松地在 PR 中进行更改和查看更改。

### <a name="save-a-luis-app-using-the-ludown-format"></a>将 LUIS 应用另存为 LUDown 格式

若要将 LUIS 应用另存为 `.lu` 格式，并将其置于源代码管理下方：

- 任一方法：从 [LUIS 门户](https://www.luis.ai/)[将应用版本导出](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions)为 `.lu`，然后将其添加到源代码管理存储库

- 或：使用文本编辑器为 LUIS 应用创建 `.lu` 文件，然后将其添加到源代码管理存储库

> [!TIP]
> 如果 LUIS 应用是以 JSON 格式导出，则可以使用 [BotBuilder-Tools LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) [将其转换为 LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert)。 使用 `--sort` 选项可确保意向和言语按字母顺序排序。  
> 请注意，LUIS 门户中内置的 .LU 导出功能已对输出进行了排序。

### <a name="build-the-luis-app-from-source"></a>从源生成 LUIS 应用

对于 LUIS 应用，从源生成意味着[通过导入 `.lu` 源新建 LUIS 应用版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version)、[定型版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)以及[发布版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)。 可以在 LUIS 门户中或在命令行中执行此操作：

- 使用 LUIS 门户从源代码管理[导入应用的 `.lu` 版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version)，然后[定型](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)并[发布](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)应用。

- 在命令行或 CI/CD 工作流中使用[适用于 LUIS 的 Bot Framework 命令行接口](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)将应用的 `.lu` 版本从源代码管理[导入](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport)到 LUIS 应用程序，然后[定型](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun)并[发布](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)应用。

### <a name="files-to-maintain-under-source-control"></a>在源代码管理下维护的文件

LUIS 应用程序的以下类型的文件应在源代码管理下进行维护：

- LUIS 应用程序的 `.lu` 文件

- [单元测试定义文件](luis-concept-devops-testing.md#writing-tests)（言语和预期结果）

- 用于性能测试的[批处理测试文件](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format)（言语和预期结果）

### <a name="credentialsand-keys-are-not-checked-in"></a>未签入凭据和密钥

请勿在签入到存储库的文件中包含订阅密钥或类似的机密值，否则未经授权的人员可能会看到这些信息。 应阻止签入的密钥和其他值包括：

- LUIS 创作和预测密钥
- LUIS 创作和预测终结点
- Azure 订阅密钥
- 访问令牌，例如自动化身份验证中所使用的 Azure [服务主体](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)的令牌

#### <a name="strategies-for-securely-managing-secrets"></a>安全管理机密的策略

安全管理机密的策略包括：

- 如果使用的是 Git 版本控制，则可以将运行时机密存储到本地文件，然后通过添加将文件名与 [.gitignore](https://git-scm.com/docs/gitignore) 文件匹配的模式来阻止签入文件
- 在自动化工作流中，可以将机密安全地存储在该自动化技术提供的参数配置中。 例如，如果使用的是 [GitHub Actions](https://github.com/features/actions)，则可以将机密安全地存储在 [GitHub 机密](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。

## <a name="branching-and-merging"></a>分支和合并

Git 等分布式版本控制系统使团队成员可以灵活地通过与他人共享的开发分支来发布、共享、查看和迭代代码更改。 采用适用于团队的 [Git 分支策略](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance)。

无论采用哪种分支策略，所有策略的主要原则如下：团队成员可以独立于功能分支以外的其他分支中的工作，来使用该功能分支中的解决方案。

若要支持独立地使用 LUIS 项目中的分支，请执行以下操作：

- **主分支有自己的 LUIS 应用。** 该应用表示项目解决方案的当前状态，并且其当前活动版本应始终映射到主分支中的 `.lu` 源。 应检查和测试此应用中对 `.lu` 源的所有更新，以便随时部署该应用以生成环境（例如生成环境）。 `.lu` 的更新从功能分支合并到主分支时，你应在 LUIS 应用中新建版本，然后[增大版本号](#versioning)。

- **每个功能分支都必须使用自己的 LUIS 应用实例**。 开发人员可以在功能分支中使用该应用，不会影响使用其他分支的开发人员。 该“开发分支”应用是工作副本，应在删除功能分支时将其删除。

![Git 功能分支](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>开发人员可以使用独立分支

开发人员可以通过以下方式独立于其他分支在 LUIS 应用中进行更新：

1. 从主分支创建功能分支（取决于分支策略，通常是 master 或 develop）。

1. 仅[在 LUIS 门户中新建 LUIS 应用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app)（“开发分支应用”）以支持使用功能分支。

   * 如果分支中已存在解决方案的 `.lu` 源，则由于该源是在项目早期使用另一个分支后保存的，因此请通过导入 `.lu` 文件来创建开发分支 LUIS 应用。

   * 如果你是开始处理新项目，则存储库中尚没有主 LUIS 应用的 `.lu` 源。 你完成功能分支中的工作后将通过从门户中导出开发分支应用来创建 `.lu` 文件，然后将其作为 PR 的一部分提交。

1. 使用开发分支应用的活动版本以实现所需的更改。 对于所有功能分支工作，建议只使用开发分支应用的一个版本。 如果在开发分支应用中创建多个版本，请谨慎跟踪哪个版本包含要在提出 PR 时签入的更改。

1. 测试更新 - 有关测试开发分支应用的详细信息，请参阅 [LUIS DevOps 测试](luis-concept-devops-testing.md)。

1. 从[版本列表](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)中将开发分支应用的活动版本导出为 `.lu`。

1. 签入你的更新并邀请对你的更新进行同级评审。 如果使用的是 GitHub，你将提出[拉取请求](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)。

1. 更改获批后，将更新合并到主分支。 此时，你将使用 master 中更新的 `.lu` 创建新[版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)的主 LUIS 应用。 有关设置版本名称的注意事项，请参阅[版本控制](#versioning)。

1. 删除功能分支后，最好删除为功能分支工作创建的开发分支 LUIS 应用。

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>开发人员可以同时使用多个分支

如果遵循[开发人员可以使用独立分支](#developers-can-work-from-independent-branches)中的模式，则你将在每个功能分支中使用唯一的 LUIS 应用程序。 一个开发人员可以同时使用多个分支，只要能够为当前正在使用的分支切换到正确的开发分支 LUIS 应用即可。

建议对功能分支和为功能分支工作创建的开发分支 LUIS 应用使用同一名称，降低意外使用错误应用的可能性。

如上所述，为简单起见，建议在每个开发分支应用中使用一个版本。 如果使用的是多个版本，请在切换开发分支应用时注意使用正确的版本。

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>多个开发人员可以同时使用同一分支

你可以支持多个开发人员同时使用同一功能分支：

- 开发人员可以签出同一功能分支，并推送和拉取自己和其他开发人员提交的更改，同时工作将如常进行。

- 如果遵循[开发人员可以使用独立分支](#developers-can-work-from-independent-branches)中的模式，则该分支将使用唯一的 LUIS 应用程序支持开发。 该“开发分支”LUIS 应用将由开始使用功能分支的开发团队中的第一个成员创建。

- [将团队成员以参与者身份添加到](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)开发分支 LUIS 应用。

- 功能分支工作完成后，从[版本列表](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)中将开发分支 LUIS 应用的活动版本导出为 `.lu`，将更新的 `.lu` 文件保存到存储库，然后签入并 PR 更改。

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>通过变基或合并，将更改从一个分支合并到另一个分支

你团队中使用其他分支的其他一些开发人员可能已对 `.lu` 源进行了更新，并在你创建功能分支后将更新合并到了主分支。 你可能想要将他们的更改合并到你的工作版本中，然后继续在自己的功能分支内进行自己的更改。 你可以按照其他任何代码资产中的方式，通过[变基或合并到 master](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) 来实现这一目标。 由于 LUDown 格式的 LUIS 应用易于阅读，因此它支持使用标准合并工具进行合并。

如果要在功能分支中对 LUIS 应用进行变基，请遵循以下提示：

- 在变基或合并之前，请先从门户重新导出应用，确保应用的 `.lu` 源的本地副本具有你通过 LUIS 门户应用的所有最新更改。 这样可以确保在门户中进行的但尚未导出的所有更改都不会丢失。

- 在合并过程中，使用标准工具解决任何合并冲突。

- 完成变基或合并后，请勿忘记将应用重新导回门户，以便在继续应用自己的更改时使用更新的应用。

### <a name="merge-prs"></a>合并 PR

PR 获批后，可以将更改合并到主分支。 LUIS 应用的 LUDown 源没有特殊的注意事项：它易于阅读，因此它支持使用标准合并工具进行合并。 可以以其他源文件中的方式解决任何合并冲突。

合并 PR 后，建议将其清除：

- 删除存储库中的分支

- 删除为功能分支工作创建的“开发分支”LUIS 应用。

你应该以应用程序代码资产中的方式编写单元测试，为 LUIS 应用更新做补充。 你应采用持续集成工作流进行测试：

- PR 合并前 PR 中的更新
- PR 获批后且更改合并到 master 后的主分支 LUIS 应用。

有关 LUIS DevOps 测试的详细信息，请参阅 [LUIS 的 DevOps 测试](luis-concept-devops-testing.md)。 有关实现工作流的更多详细信息，请参阅 [LUIS DevOps 的自动化工作流](luis-concept-devops-automation.md)。

## <a name="code-reviews"></a>代码评审

LUDown 格式的 LUIS 应用易于阅读，它支持在适用于审查的 PR 中关于更改进行通信。 单元测试文件也是 LUDown 格式，也易于在 PR 中审查。

## <a name="versioning"></a>版本控制

应用程序由多个组件组成，其中可能包括 [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)、[QnA Maker](https://www.qnamaker.ai/)、[Azure 语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)等中运行的机器人等。 若要实现松散耦合的应用程序，请使用[版本控制](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control)以便独立地对应用程序的每个组件进行版本控制，使开发人员可以通过直接查看版本号来检测重大更改或更新。 如果在 LUIS 应用自己的存储库中对其进行维护，则可以独立于其他组件轻松地对其进行版本控制。

主分支的 LUIS 应用应该应用版本控制方案。 将 LUIS 应用的 `.lu` 的更新合并到 master 后，你会将更新后的该源导入到主分支的 LUIS 应用中的新版本。

建议对主 LUIS 应用版本使用数字版本控制方案，例如：

`major.minor[.build[.revision]]`

每次更新后，版本号的最后一位递增。

主要/次要版本可用于指示 LUIS 应用功能的更改范围：

* 主版本：重大更改，例如支持新[意向](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent)或[实体](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)
* 次版本：后向兼容的次要更改，例如在重要的新培训之后
* 内部版本：不会更改任何功能，只是另一个内部版本。

确定最新版的主 LUIS 应用的版本号后，你需要生成和测试新的应用版本，然后将其发布到可用于各种内部版本环境（例如质量保证或生产）的终结点。 强烈建议自动执行持续集成 (CI) 工作流中的所有这些步骤。

请参阅：
- [自动化工作流](luis-concept-devops-automation.md)，详细了解如何实现 CI 工作流以测试和发布 LUIS 应用。
- [发布管理](luis-concept-devops-automation.md#release-management)，了解如何部署 LUIS 应用。

### <a name="versioning-the-feature-branch-luis-app"></a>对“功能分支”LUIS 应用进行版本控制

使用为支持功能分支中的工作而创建的“开发分支”LUIS 应用时，你将在完成工作后导出你的应用，然后在 PR 中加入更新后的 `'lu`。 PR 合并到 master 后，应删除存储库中的分支和“开发分支”LUIS 应用。 由于该应用仅用于支持功能分支中的工作，因此无需在该应用中应用特定的版本控制方案。

PR 中的更改合并到 master 时，应该应用版本控制，以便独立地对 master 的所有更新进行版本控制。

## <a name="next-steps"></a>后续步骤

* 了解 [LUIS DevOps 测试](luis-concept-devops-testing.md)
* 了解如何[使用 GitHub 实现 DevOps for LUIS](luis-how-to-devops-with-github.md)
