---
title: 源代码管理和开发分支-LUIS
description: 如何维护源代码管理下的语言理解（LUIS）应用程序。 如何在开发分支中工作时将更新应用到 LUIS 应用。
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 2d060fefbd32ecea1f91e6b062da7606699a63c4
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783669"
---
# <a name="devops-practices-for-luis"></a>LUIS 的 DevOps 实践

正在开发语言理解（LUIS）应用程序的软件工程师可以遵循以下准则，在[源代码](luis-concept-devops-sourcecontrol.md)管理、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)方面应用 DevOps 做法。

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS 的源代码管理和分支策略

DevOps 的成功与否取决于[源代码管理](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)。 源代码管理系统允许开发人员协作处理代码并跟踪更改。 使用分支后，开发人员可以在不同版本的基本代码之间进行切换，并独立于团队的其他成员运行。 当开发人员引发[拉取请求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests)（PR）来向另一个分支提出更新建议时，或者当合并更改时，它们可以是[自动生成](luis-concept-devops-automation.md)的触发器，用于生成和持续测试代码。

通过使用本文档中所述的概念和指南，你可以在跟踪源代码管理系统中的更改时开发 LUIS 应用程序，并遵循以下软件工程最佳实践：

- **源代码管理**
  - LUIS 应用程序的源代码是用户可读的格式。
  - 可以通过可重复的方式从源中生成模型。
  - 源代码可以由源代码存储库管理。
  - 凭据和机密（例如创作和订阅密钥）永远不会存储在源代码中。

- **分支和合并**
  - 开发人员可以使用独立的分支。
  - 开发人员可以同时在多个分支中运行。
  - 可以通过变基或合并将更改从一个分支集成到另一个分支中的 LUIS 应用。
  - 开发人员可以将 PR 合并到父分支。

- **版本管理**
  - 大型应用程序中的每个组件都应该单独进行版本控制，从而仅允许开发人员通过查看版本号来检测重大更改或更新。

- **代码评审**
  - PR 中的更改以用户可读源代码的形式显示，可以在接受 PR 之前查看这些代码。

## <a name="source-control"></a>源代码管理

若要在源代码管理系统中维护 LUIS 应用的[应用架构定义](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition)，请使用应用的[LUDown 格式（ `.lu` ）](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)表示形式。 `.lu`format 是 `.json` 格式的首选，因为它是人工可读的，这样可以更轻松地在 pr 中进行更改和查看。

### <a name="save-a-luis-app-using-the-ludown-format"></a>使用 LUDown 格式保存 LUIS 应用

若要以格式保存 LUIS 应用 `.lu` 并将其置于源代码管理下：

- 请：从 LUIS 门户[导出应用版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) `.lu` ， [LUIS portal](https://www.luis.ai/)并将其添加到你的源代码管理存储库

- 或：使用文本编辑器创建 `.lu` LUIS 应用的文件，并将其添加到源代码管理存储库

> [!TIP]
> 如果使用的是 LUIS 应用的 JSON 导出，则可以使用[BOTBUILDER LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)[将其转换为 LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) 。 使用 `--sort` 选项可确保意向和最谈话按字母顺序排序。  
> 请注意， **。** LUIS 门户中内置的 LU 导出功能已对输出进行排序。

### <a name="build-the-luis-app-from-source"></a>从源生成 LUIS 应用

对于 LUIS 应用，[通过导入 `.lu` 源来创建新的 LUIS 应用版本是通过导入源来创建新的应用版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version)，然后对[版本进行定型](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)并[发布](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)。 *build from source* 可在 LUIS 门户中或在命令行中执行此操作：

- 使用 LUIS 门户从源代码管理[导 `.lu` 入](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version)应用程序的版本，并[训练](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)和[发布](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)应用程序。

- 在命令行或 CI/CD 工作流中使用用于[LUIS 的机器人框架命令行接口](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)将应用程序的版本从源代码管理[导入](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) `.lu` 到 LUIS 应用程序，并[训练](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun)和[发布](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)应用程序。

### <a name="files-to-maintain-under-source-control"></a>要在源代码管理下维护的文件

LUIS 应用程序的以下类型的文件应在源代码管理下进行维护：

- `.lu`LUIS 应用程序的文件

- [单元测试定义文件](luis-concept-devops-testing.md#writing-tests)（最谈话和预期结果）

- 用于性能测试的[批处理测试文件](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format)（最谈话和预期结果）

### <a name="credentialsand-keys-are-not-checked-in"></a>凭据和密钥未签入

不要在签入到存储库的文件中包含订阅密钥或类似的机密值，在这些文件中，未经授权的人员可能会看到这些密钥。 你应禁止签入的键和其他值包括：

- LUIS 创作和预测密钥
- LUIS 创作和预测终结点
- Azure 订阅密钥
- 访问令牌，如用于自动化身份验证的 Azure[服务主体](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)的令牌

#### <a name="strategies-for-securely-managing-secrets"></a>安全管理机密的策略

安全管理机密的策略包括：

- 如果使用的是 Git 版本控制，则可以将运行时机密存储在本地文件中，并通过添加模式将文件名与[.gitignore](https://git-scm.com/docs/gitignore)文件进行匹配来阻止签入文件
- 在自动化工作流中，可以安全地将机密存储在该自动化技术所提供的参数配置中。 例如，如果你使用[Github 操作](https://github.com/features/actions)，则可以安全地在[GitHub 机密](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中存储机密。

## <a name="branching-and-merging"></a>分支和合并

Git 等分布式版本控制系统通过与他人共享的开发分支，使团队成员发布、共享、查看和迭代代码更改的方式具有灵活性。 采用适用于你的团队的[Git 分支策略](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance)。

无论采用哪种分支策略，都有一个关键原则，就是团队成员可以独立于在其他分支中进行的工作，在*功能分支*中处理解决方案。

若要支持在 LUIS 项目中独立工作，请执行以下操作：

- **主分支有自己的 LUIS 应用。** 此应用表示你的项目的解决方案的当前状态，其当前活动版本应始终映射到 `.lu` 主分支中的源。 应对此应用程序的源进行的所有更新都 `.lu` 应该进行评审和测试，以便可以将此应用程序部署到生产环境（例如，在生产环境中）。 当对的更新 `.lu` 从功能分支合并到 master 时，应在 LUIS 应用程序中创建新版本并增加[版本号](#versioning)。

- **每个功能分支都必须使用其自己的 LUIS 应用实例**。 开发人员在功能分支中使用此应用程序，而不会影响在其他分支中工作的开发人员。 此 "开发分支" 应用是在删除功能分支时应删除的工作副本。

![Git 功能分支](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>开发人员可以从独立分支运行

开发人员可以通过以下方式独立于其他分支在 LUIS 应用上处理更新：

1. 从主分支创建功能分支（这取决于你的分支策略，通常为 master 策略）。

1. 仅[在 LUIS 门户中创建新的 LUIS 应用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app)（"*开发分支应用*"），以支持功能分支中的工作。

   * 如果你的 `.lu` 解决方案的源已存在于你的分支中，因为它是在项目中的另一个分支完成后保存的，请通过导入该文件来创建开发分支 LUIS 应用 `.lu` 。

   * 如果正在开始处理新项目，则存储库中将不会有 `.lu` MASTER LUIS 应用程序的源代码。 `.lu`完成功能分支工作后，你将通过从门户导出开发分支应用来创建文件，并将其作为 PR 的一部分提交。

1. 使用开发分支应用的活动版本来实现所需的更改。 建议仅在开发分支应用的单个版本中工作，才能实现所有功能分支。 如果在开发分支应用中创建多个版本，请在提出 PR 时小心跟踪哪个版本包含要签入的更改。

1. 测试更新-有关测试 dev 分支应用的详细信息，请参阅[测试 LUIS DevOps](luis-concept-devops-testing.md) 。

1. `.lu`从 "[版本" 列表](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)中导出 dev 分支应用的活动版本。

1. 签入你的更新并邀请对等你的更新评审。 如果使用的是 GitHub，则会引发[拉取请求](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)。

1. 更改获得批准后，将更新合并到主分支中。 此时，你将使用 master 中的更新[版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)创建*master* LUIS 应用的新版本 `.lu` 。 有关设置版本名称的注意事项，请参阅[版本控制](#versioning)。

1. 删除功能分支后，最好删除为该功能分支工作创建的 LUIS 应用程序。

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>开发人员可以同时在多个分支中运行

如果遵循上述模式，[开发人员可以使用独立的分支](#developers-can-work-from-independent-branches)，则会在每个功能分支中使用唯一的 LUIS 应用程序。 一位开发人员可以同时处理多个分支，只要它们切换到当前正在处理的分支的正确开发分支 LUIS 应用即可。

建议为功能分支以及为功能分支工作创建的 dev branch LUIS 应用程序使用相同的名称，以避免意外使用错误的应用程序的可能性。

如上所述，我们建议为简单起见，在每个开发分支应用中使用单个版本。 如果使用多个版本，请在开发开发分支应用之间切换时小心激活正确的版本。

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>多个开发人员可以同时在同一个分支上工作

您可以支持多个开发人员同时使用同一功能分支：

- 开发人员签出相同的功能分支，并在工作继续时，按正常方式推送和拉取由其自身和其他开发人员提交的更改。

- 如果遵循上面所述的模式，[开发人员可以使用独立的分支](#developers-can-work-from-independent-branches)，则此分支将使用唯一的 LUIS 应用程序来支持开发。 开发团队中开始工作的第一个成员将创建该 "开发分支" LUIS 应用。

- [将团队成员作为参与者添加](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)到 DEV 分支 LUIS 应用。

- 当功能分支工作完成后，请从 "版本" 列表中导出 dev branch LUIS 应用的活动版本 `.lu` ，将更新后的文件保存在存储库[versions list](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) `.lu` 中，并签入并执行这些更改。

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>通过变基或合并将一个分支中的更改合并到另一个分支

你的团队中工作的其他一些开发人员可能已对源进行了更新 `.lu` ，并在创建功能分支后将它们合并到主分支。 你可能想要将其更改并入工作版本，然后再继续在功能分支中进行自己的更改。 为此，可以使用与任何其他代码资产相同的方式，通过[变基或合并到 master 来](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)实现此目的。 由于 LUDown 格式的 LUIS 应用是用户可读的，因此它支持使用标准合并工具进行合并。

如果要在功能分支中变基 LUIS 应用，请遵循以下提示：

- 在变基或合并之前，请确保应用的源的本地副本 `.lu` 包含使用 LUIS 门户应用的所有最新更改，方法是先从门户重新导出应用。 这样，你就可以确保已在门户中进行的任何更改不会丢失。

- 在合并过程中，使用标准工具解决任何合并冲突。

- 在变基或合并完成后，不要忘记重新将应用导入到门户，因此，在继续应用自己的更改时，可以使用更新的应用程序。

### <a name="merge-prs"></a>Merge Pr

获得批准后，可以将更改合并到主分支。 对于 LUIS 应用的 LUDown 源，无需特别注意：它是人类可读的，因此支持使用标准合并工具进行合并。 任何合并冲突都可以用与其他源文件相同的方式来解决。

合并 PR 后，建议清除以下内容：

- 删除存储库中的分支

- 删除为功能分支工作创建的 "开发分支" LUIS 应用。

与应用程序代码资产一样，你应该编写单元测试，以伴随 LUIS 应用更新。 应使用持续集成工作流进行测试：

- 合并 PR 前的 PR 中的更新
- 已批准 PR 并已将更改合并到主分支后的主分支 LUIS 应用。

有关测试 LUIS DevOps 的详细信息，请参阅为[LUIS 测试 DevOps](luis-concept-devops-testing.md)。 有关实现工作流的更多详细信息，请参阅[LUIS DevOps 自动化工作流](luis-concept-devops-automation.md)。

## <a name="code-reviews"></a>代码评审

LUDown 格式的 LUIS 应用程序是可读的，它支持与要评审的 PR 中的更改进行通信。 单元测试文件也以 LUDown 格式编写，还可以在 PR 中轻松 reviewable。

## <a name="versioning"></a>版本管理

应用程序由多个组件组成，其中可能包括在[Azure Bot 服务](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)中运行的机器人、 [QnA Maker](https://www.qnamaker.ai/)、 [Azure 语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)等。 若要实现松散耦合应用程序的目标，请使用[版本控制](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control)，以便使应用程序的每个组件单独进行版本控制，从而仅允许开发人员通过查看版本号来检测重大更改或更新。 如果你在自己的存储库中维护你的 LUIS 应用程序，则可以更轻松地独立于其他组件对其进行版本维护。

Master 分支的 LUIS 应用应应用版本控制方案。 将 LUIS 应用程序的更新合并 `.lu` 到 master 时，会将更新后的源导入到 master 分支的 LUIS 应用程序的新版本中。

建议为 master LUIS 应用版本使用数值版本控制方案，例如：

`major.minor[.build[.revision]]`

每个更新的版本号将以最后一个数字递增。

主要/次要版本可用于指示对 LUIS 应用功能的更改范围：

* 主要版本：重大更改，如对新[意向](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent)或[实体](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)的支持
* 次版本：向后兼容的次要更改，如重要的新培训之后
* Build：无功能更改，只是使用不同的版本。

确定了 master LUIS 应用的最新版本的版本号后，需要生成并测试新的应用版本，并将其发布到可在不同的生成环境（例如质量保证或生产）中使用的终结点。 强烈建议在持续集成（CI）工作流中自动执行所有这些步骤。

请参阅：
- [自动化工作流](luis-concept-devops-automation.md)，详细介绍了如何实现 CI 工作流来测试和发布 LUIS 应用。
- 有关如何部署 LUIS 应用的信息，请[Release Management](luis-concept-devops-automation.md#release-management) 。

### <a name="versioning-the-feature-branch-luis-app"></a>版本控制 "功能分支" LUIS 应用

当你使用已创建的 "开发分支" LUIS 应用以支持在功能分支中工作时，你将在工作完成时导出应用，并且将在你的 PR 中包含已更新的 `'lu` 。 在 PR 合并到 master 后，应删除存储库中的分支和 "开发分支" LUIS 应用。 由于此应用程序只是为了支持功能分支中的工作，因此，在此应用程序中不需要应用特定的版本控制方案。

当你的 PR 中的更改合并到 master 时，即应应用版本控制，以便对 master 的所有更新进行独立版本控制。

## <a name="next-steps"></a>后续步骤

* 了解[LUIS DevOps 的测试](luis-concept-devops-testing.md)
* 了解如何[通过 GitHub 为 LUIS 实现 DevOps](luis-how-to-devops-with-github.md)
