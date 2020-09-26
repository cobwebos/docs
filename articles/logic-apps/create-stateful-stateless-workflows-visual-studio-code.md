---
title: '在 Visual Studio Code 中创建自动化工作流 (预览) '
description: 通过 Azure 逻辑应用 (预览) 扩展创建无状态或有状态自动化工作流，以在 Visual Studio Code 集成应用、数据、云服务和本地系统
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 1f67d7228da8529699a26539f20efd55f9a20c27
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370974"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>在 Azure 逻辑应用 Visual Studio Code 中创建有状态或无状态的工作流 (预览版) 扩展

> [!IMPORTANT]
> 此功能以公共预览版提供，不提供服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

若要创建跨应用、数据、云服务和系统集成的逻辑应用工作流，可以使用 Visual Studio Code 和 Azure 逻辑应用 (预览版) 扩展，在开发环境中生成和本地运行有 [*状态* 和 *无状态* 逻辑应用工作流](#stateful-stateless) 。

![显示 Visual Studio Code 和逻辑应用工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

使用公共预览版扩展创建的逻辑应用使用新的 **逻辑应用 (预览) ** 资源类型，并由本地环境中的 [Azure Functions](../azure-functions/functions-overview.md) 运行时提供支持。 此新资源类型可以包含多个工作流，并且在某些方面与 **Function App** 资源类型类似，其中可能包括多个函数。

同时，原始 **逻辑应用** 资源类型仍存在，你可以在 Visual Studio Code 和 Azure 门户中创建和使用。 不过，原始资源类型的体验与新资源类型不同。 此时， **逻辑应用** 和 **逻辑应用 (预览) ** 资源类型在 Visual Studio Code 和 Azure 门户中都可以同时存在。 你可以在 Azure 订阅中查看和访问所有已部署的逻辑应用，但它们会在其自己的类别和部分中单独保存并保存。

本文提供了 [有关此公共预览版](#whats-new)的高级概述，其中介绍了有关 **逻辑应用 (预览) ** 资源类型的各个方面，以及如何使用 Visual Studio Code 创建此资源：

* 有 [状态和无状态](#stateful-stateless) 逻辑应用之间的差异。

* 如何满足 [设置要求](#prerequisites) 并设置公共预览版扩展 [Visual Studio Code](#set-up) 。

* 如何通过[创建项目和选择工作流模板](#create-project)来构建新的**逻辑应用 (预览) **工作流。

* 如何在 Visual Studio Code 中本地运行和调试新的逻辑应用。

* 如何将这些新的逻辑应用直接从 Visual Studio Code 发布 [到 Azure](#publish-azure) ，或发布到可在任何位置运行的 [Docker 容器](#deploy-docker) 。 有关 Docker 的详细信息，请参阅 [什么是 docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)？

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>此公共预览版有哪些功能？

Azure 逻辑应用 (预览版) 扩展可在 Visual Studio Code 中为你的本地开发体验提供许多最新的和其他的逻辑应用功能，例如：

* 为软件即服务 (SaaS) 和平台即服务 (PaaS) 应用和服务以及用于本地系统的连接器创建集成和自动化工作 [流的逻辑](/connectors/connector-reference/connector-reference-logicapps-connectors) 应用。

  * 某些托管连接器（如 Azure 服务总线、Azure 事件中心和 SQL Server）的运行方式类似于内置本机触发器和操作，如 HTTP 操作。

  * 创建和部署可在任何位置运行的逻辑应用，因为 Azure 逻辑应用服务会生成 (SAS) 连接字符串的共享访问签名，这些逻辑应用可使用这些连接字符串向云连接运行时终结点发送请求。 逻辑应用服务会将这些连接字符串与其他应用程序设置一起保存，因此，在部署到 Azure 时，可以轻松地将这些值存储在 Azure Key Vault 中。

    > [!NOTE]
    > 默认情况下， **逻辑应用 (预览) ** 资源已自动启用 [系统分配的托管标识](../logic-apps/create-managed-service-identity.md) ，以在运行时对连接进行身份验证。 此标识不同于在创建连接时使用的身份验证凭据或连接字符串。 如果禁用此标识，则连接在运行时不起作用。

* 创建仅在内存中运行的无状态逻辑应用，以便这些应用程序能够更快地完成、响应速度更高、吞吐量更高且运行成本更高，因为在外部存储中不会保留运行历史记录和数据。 还可以选择启用运行历史记录，以便更轻松地进行调试。 有关详细信息，请参阅有 [状态和无状态逻辑应用](#stateful-stateless)。

* 在 Visual Studio Code 开发环境中本地测试逻辑应用。

* 将逻辑应用从 Visual Studio Code 直接发布和部署到各种宿主环境，如 [Azure App Service](../app-service/environment/intro.md) 和 [Docker 容器](/dotnet/core/docker/introduction)。

> [!NOTE]
> 有关当前已知问题的信息，请查看预览扩展的 [已知问题 GitHub 页面](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>有状态和无状态逻辑应用

* *有状态*

  如果需要保留、查看或引用以前事件中的数据，请创建有状态逻辑应用。 这些逻辑应用保留每个操作的输入和输出以及它们在外部存储中的工作流状态，从而在每次运行完成后查看运行详细信息和历史记录。 有状态逻辑应用在发生中断时提供高复原能力。 还原服务和系统后，可以从已保存状态重新构造中断的逻辑应用运行，并重新运行逻辑应用以完成。 有状态工作流的运行时间最多可达一年。

* *无状态*

  如果不需要保存、查看或引用外部存储中以前事件的数据以供以后查看，请创建无状态逻辑应用。 这些逻辑应用保留每个操作的输入和输出，并将其工作流状态仅保留在内存中，而不是将此信息传输到外部存储。 因此，无状态逻辑应用程序的运行时间通常不超过5分钟，更快的响应时间更快，吞吐量较高，降低了运行成本，因为运行详细信息和历史记录不会保存在外部存储中。 但是，如果发生中断，中断的运行将不会自动还原，因此调用方需要手动重新提交中断的运行。 这些逻辑应用只能以同步方式运行，若要更轻松地进行调试，你可以 [启用运行历史记录](#run-history)，这会对性能产生影响。

  无状态工作流目前只支持 [托管连接器](../connectors/apis-list.md#managed-api-connectors)的操作，而不支持触发器。 若要启动工作流，请选择 [内置请求、事件中心或服务总线触发器](../connectors/apis-list.md#built-ins)。 有关不支持的触发器、操作和连接器的详细信息，请参阅 [不支持的功能](#unsupported)。

有关嵌套的逻辑应用在有状态和无状态逻辑应用之间的行为方式的信息，请参阅有 [状态和无状态逻辑应用之间的嵌套行为差异](#nested-behavior)。

<a name="pricing-model"></a>

## <a name="pricing-model"></a>定价模型

当你部署新的 **逻辑应用 (预览) ** 资源类型时，系统将提示你选择一个托管计划，具体来说就是 [应用服务计划或高级计划](../azure-functions/functions-scale.md) 用作定价模型。 如果选择 "应用服务计划"，系统还会提示你选择 [定价层](../app-service/overview-hosting-plans.md)。 公共预览期间，在应用服务上运行逻辑应用不会在所选计划的顶部产生 *额外* 的费用。

有状态逻辑应用使用 [外部存储](../azure-functions/functions-scale.md#storage-account-requirements)，因此 azure 存储定价模型适用于 Azure 逻辑应用运行时执行的存储事务。 例如，队列用于计划，而表和 blob 用于存储工作流状态。

有关适用于此新资源类型的定价模型的详细信息，请参阅以下主题：

* [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)
* [向上缩放 Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions 定价详细信息](https://azure.microsoft.com/pricing/details/functions/)
* [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Azure 存储定价详细信息](https://azure.microsoft.com/pricing/details/storage/)

## <a name="prerequisites"></a>先决条件

### <a name="access-and-connectivity"></a>访问和连接

* 访问 internet，以便可以下载要求、从 Visual Studio Code 连接到 Azure 帐户，以及从 Visual Studio Code 发布到 Azure、Docker 容器或其他环境。

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 若要在本文中生成相同的示例逻辑应用，需要使用 Microsoft 工作或学校帐户登录的 Office 365 Outlook 电子邮件帐户。

  如果你选择使用 [Azure 逻辑应用支持的其他电子邮件连接器](/connectors/)（如 Outlook.com 或 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)），仍可以按照示例操作，一般的整体步骤相同，但你的用户界面和选项在某些方面可能会有所不同。 例如，如果你使用 Outlook.com 连接器，则使用你的个人 Microsoft 帐户改为登录。

### <a name="tools"></a>工具

* [Visual Studio Code 1.30.1 (2019 年1月) 或更高版本](https://code.visualstudio.com/)（免费）。 此外，为 Visual Studio Code 下载并安装这些其他工具（如果尚未安装）：

  * [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)，为 Visual Studio Code 中的所有其他 azure 扩展提供单个常见的 azure 登录和订阅筛选体验。

  * [C # for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)，使 F5 功能可以运行逻辑应用。

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md)（版本 [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) 或 [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936)），通过 Microsoft 安装程序 (MSI) 。 这些工具包括一个用于支持在 Visual Studio Code 中运行的 Azure Functions 运行时的版本。

    > [!IMPORTANT]
    > 如果安装的版本早于这些版本，请先卸载该版本，或者确保路径环境变量指向你下载和安装的版本。
    >
    > 如果要使用 [**内联代码** 操作](../logic-apps/logic-apps-add-run-inline-code.md) 来运行 JavaScript 代码，则需要使用 Azure Functions 运行时版本3，因为该操作不支持版本2。 此外，目前不支持在 Linux 操作系统上执行此操作。

  * [适用于 Visual Studio Code 的 Azure 逻辑应用 (预览) 扩展](https://go.microsoft.com/fwlink/p/?linkid=2143167)。 此公共预览版扩展提供了创建有状态和无状态逻辑应用的功能，并可在 Visual Studio Code 中本地对其进行测试。

    目前，可以在 Visual Studio Code 中同时安装原始 **Azure 逻辑应用** 扩展和新的 **azure 逻辑应用 (预览版) ** 扩展。 通过在 Visual Studio Code 工具栏上选择 "Azure" 图标，可以查看 Azure 中部署的所有逻辑应用，但每种资源类型都显示在其自己的扩展部分、 **逻辑应用** 和 **Azure 逻辑应用 (预览版) **中。

    > [!IMPORTANT]
    > 如果使用 **Azure 逻辑应用 (专用预览版) ** 扩展创建了逻辑应用，则这些逻辑应用将不会使用公共预览版扩展。 但是，你可以通过卸载专用预览扩展、执行所需的清除以及安装公共预览版扩展来迁移这些逻辑应用。 然后，你可以在 Visual Studio Code 中创建新的项目，并将以前创建的逻辑应用的 **工作流定义** 文件复制到你的新项目中。
    >
    > 因此，在安装公共预览版扩展之前，请确保卸载所有早期版本，并删除这些项目：
    >
    > * **ExtensionBundle**文件夹，其中包含以前的扩展捆绑，位于以下路径中的任一路径：
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * 作为专用预览扩展插件的[NuGet](/nuget/what-is-nuget)缓存，并位于此路径中，则为**microsoft.**
    >
    >   `C:\Users\{userName}\.nuget\packages`

    若要安装 **Azure 逻辑应用 (预览) ** 扩展，请执行以下步骤：

    1. 在 Visual Studio Code 的左侧工具栏中，选择 " **扩展**"。

    1. 在 "扩展" 搜索框中输入 `azure logic apps preview` 。 在结果列表中，选择 " **Azure 逻辑应用 (预览") ** " **>** **安装**"。

       安装完成后，公共预览版扩展将出现在 " **扩展：已安装** " 列表中。

       ![屏幕截图，显示 Visual Studio Code 安装的扩展列表，其中包含带下划线的 "Azure 逻辑应用 (预览版) " 扩展。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* 若要测试在本文中创建的示例逻辑应用，需要一个可将调用发送到请求触发器的工具，这是示例逻辑应用的第一个步骤。 如果没有此类工具，可以下载、安装和使用 [Postman](https://www.postman.com/downloads/)。

* 为了更轻松地进行诊断日志记录和跟踪功能，你可以添加和使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 资源。 可以在逻辑应用部署期间创建此资源，也可以在部署逻辑应用后在 Azure 门户中创建此资源。

### <a name="storage-requirements"></a>存储要求

目前， (预览中创建新的 **逻辑应用) ** 资源在 macOS 上不可用。 但是，对于 Windows 或其他 OS （如 Linux），请设置此存储要求。

1. 下载并安装 [Azure 存储模拟器 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179)。

1. 若要运行仿真程序，需要安装本地 SQL DB，如免费的 [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)。 有关详细信息，请参阅 [使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

   > [!IMPORTANT]
   > 在打开逻辑应用设计器以创建逻辑应用工作流之前，请确保启动模拟器。 否则，你将收到一条消息 `Workflow design time could not be started` 。
   >
   > ![显示运行 Azure 存储模拟器的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>设置 Visual Studio Code

1. 若要确保正确安装所有扩展，请 Visual Studio Code 重新加载或重新启动。

1. 启用或检查 Visual Studio Code 自动查找并安装扩展更新，以便公共预览扩展获取最新更新。

   若要检查此设置，请执行以下步骤：

   1. 在 " **文件** " 菜单上，中转到 " **首选项**" " **>** **设置**"。

   1. 在 " **用户** " 选项卡上，中转到 " **功能** **>** **扩展**"。

   1. 确认已选中 " **自动检查更新** 并 **自动更新** "。

1. 启用或检查是否已在 Visual Studio Code 中设置这些 **Azure 逻辑应用 (预览) ** 扩展设置：

   * **Azure 逻辑应用 V2： Panel 模式**
   * **Azure 逻辑应用 V2：项目运行时**

   1. 在 " **文件** " 菜单上，中转到 " **首选项**" " **>** **设置**"。

   1. 在 "**用户**" 选项卡上，中转到 " **>** **Extensions** **>** **Azure 逻辑应用 (预览") **。

   1. 在 **"Azure 逻辑应用 V2：面板模式**" 下，确认已选中 **"启用面板模式"** 。 在 " **Azure 逻辑应用 V2：项目运行时**" 下，根据你之前安装的[Azure Functions Core Tools 版本](#prerequisites)将版本设置为 **~ 3**或 **~ 2**。

      > [!IMPORTANT]
      > 如果要使用 [**内联代码** 操作](../logic-apps/logic-apps-add-run-inline-code.md) 来运行 JavaScript 代码，请确保使用项目运行时版本3，因为该操作不支持版本2。 此外，目前不支持在 Linux 操作系统上执行此操作。

      ![显示 "Azure 逻辑应用 (预览版) " 扩展 Visual Studio Code 设置的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

1. 在 Visual Studio Code 工具栏上，选择 Azure 图标。

   ![显示 Visual Studio Code 工具栏和所选 Azure 图标的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. 在 Azure 窗格中的 " **azure：逻辑应用 (预览") **中，选择 " **登录到 Azure**"。 出现 "Visual Studio Code 身份验证" 页面时，请用 Azure 帐户登录。

   ![显示 azure 窗格和 Azure 登录的所选链接的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   登录后，Azure 窗格将显示 Azure 帐户中的订阅。 如果预期的订阅未显示，或者您希望该窗格仅显示特定订阅，请执行以下步骤：

   1. 在 "订阅" 列表中，将指针移动到第一个订阅旁边，直到 " **选择订阅** " 按钮 (筛选器 "图标) 出现。 选择 "筛选器" 图标。

      ![显示 Azure 窗格和所选筛选器图标的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      或者，在 "Visual Studio Code" 状态栏中，选择 Azure 帐户。 

   1. 如果出现其他订阅列表，请选择所需的订阅，然后确保选择 **"确定"**。

<a name="create-project"></a>

## <a name="create-a-local-project"></a>创建本地项目

创建逻辑应用之前，请先创建一个本地项目，以便可以从 Visual Studio Code 管理和部署逻辑应用。 基础项目类似于 Azure Functions 项目，也称为函数应用项目。 但是，这些项目类型彼此独立，因此逻辑应用工作流和函数不能存在于同一项目中。

1. 在计算机上，创建一个 *空* 的本地文件夹以用于稍后在 Visual Studio Code 中创建的项目。

   如果你安装了 .NET Core SDK 5.0，请 ** 在文件上** 创建一个global.js，该文件引用的 .net Core 运行时版本3.x 低于3.1.201，例如：

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   稍后，在创建项目后，但在尝试在逻辑应用设计器中 **workflow.js** 打开文件之前，需要将此 **global.js** 文件添加到项目的根位置。

1. 在 Visual Studio Code 中，关闭所有打开的文件夹。

1. 在 Azure 窗格中的 " **azure：逻辑应用 (预览") **上，选择 " **新建项目** " (图标，该图标显示文件夹和闪电) 。

   ![显示选中了 "创建新项目" 的 Azure 窗格工具栏的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. 如果 Windows Defender 防火墙提示你授予的网络访问权限 `Code.exe` ，这是 Visual Studio Code 的，而对于 `func.exe` Azure Functions Core Tools，则选择 " **专用网络"，例如 "我的家庭" 或 "工作网络** **>** **允许访问**"。

1. 浏览到创建项目文件夹的位置，选择该文件夹，然后继续。

   ![显示 "选择文件夹" 对话框并选择新创建的项目文件夹和 "选择" 按钮的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 从显示的 "模板" 列表中，选择有 **状态工作流** 或 **无状态工作流**。 此示例选择有 **状态工作流**。

   ![显示工作流模板列表的屏幕截图，其中选择了 "有状态工作流"。](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. 提供逻辑应用工作流的名称，然后按 Enter。 此示例使用 `example-workflow` 作为名称。

   ![屏幕截图显示 "创建新的有状态工作流 (3/4) " 框和 "示例-workflow" 作为工作流名称。](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. 从显示的下一个列表中，选择 " **在当前窗口中打开**"。

   ![显示 "在当前窗口中打开" 列表的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code 重新加载，打开 "资源管理器" 窗格并显示项目，该项目现在包括自动生成的项目文件。 例如，项目有一个文件夹，其中显示了逻辑应用工作流的名称。 在此文件夹中，该 `workflow.json` 文件包含逻辑应用工作流的基础 JSON 定义。

   ![屏幕截图，显示包含项目文件夹、工作流文件夹和 "workflow.js" 文件的资源管理器窗口。](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. 如果已安装 .NET Core SDK 5.0，并且在文件中创建了一个引用 .NET Core 运行时版本3.x 的 **global.js** ，则需要将该 **global.js** 从文件添加到项目的根位置，Visual Studio Code。

   > [!NOTE]
   > 请确保在尝试在逻辑应用设计器中打开包含工作流的基础 JSON 定义的文件 **workflow.js** 之前完成此步骤。 否则，设计器将不会打开。

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>在逻辑应用设计器中打开工作流定义文件

1. 如果 Visual Studio Code 在 Windows 或 Linux 上运行，请确保 Azure 存储模拟器正在运行。 有关详细信息，请参阅 [先决条件](#prerequisites)。

1. 展开工作流的项目文件夹。 打开文件快捷菜单上的 " **workflow.js** ，然后选择" **在设计器中打开**"。

   ![显示 "在设计器中打开" 的文件上的 workflow.js的 "资源管理器" 窗格和快捷窗口的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   如果收到错误消息 `Workflow design time could not be started` ，请确保 Azure 存储模拟器正在运行。 否则，请尝试以下其他故障排除建议：

   在 Visual Studio Code 中，检查预览扩展的输出。

   1. 从 " **视图** " 菜单中选择 " **输出**"。

   1. 从 " **输出** " 标题栏上的列表中，选择 " **Azure 逻辑应用** "，以便可以查看预览扩展的输出，例如：

      ![显示 Visual Studio Code 的输出窗口的屏幕截图，其中选择了 "Azure 逻辑应用"。](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. 查看输出并检查是否显示此错误消息：

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      如果以前尝试打开设计器，然后停用或删除了项目，则会发生此错误。 若要解决此错误，请删除位于此位置的 **ExtensionBundles** 文件夹 **。 ..\Users \\ {用户名} \AppData\Local\Temp\Functions\ExtensionBundles**，然后重试在设计器中打开文件 ** 上的workflow.js** 。

1. 从 " **在 azure 中启用连接器** " 列表中，选择 " **使用 azure**中的连接器"，此功能适用于 Azure 门户中可用的所有托管连接器，而不是仅适用于 Azure 服务的连接器。

   ![屏幕截图显示 "在 Azure 中启用连接器" 列表，并选择 "使用 Azure 中的连接器"。](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. 从 "资源组" 列表中，选择 " **新建资源组**"。

   ![显示 "资源组" 列表并选择 "新建资源组" 的 "资源管理器" 窗格的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. 提供资源组的名称，然后按 Enter。 本示例使用 `example-logic-app-preview-rg`。

   ![显示 "资源管理器" 窗格和 "资源组名称" 框的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 从 "位置" 列表中，找到并选择要用于创建资源组和资源的 [受支持的 Azure 区域](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) 。 此示例使用 **美国西部**。

   > [!IMPORTANT]
   > 目前并非所有区域都受支持，但添加更多区域的更新正在进行。 选择不受支持的区域可能会导致问题，例如创建连接。 对于当前支持的区域，请查看预览扩展的 " [已知问题" GitHub 页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)。

   ![显示 "资源管理器" 窗格和 "位置" 列表并选择 "美国西部" 的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   执行此步骤后，Visual Studio Code 打开逻辑应用设计器。

   > [!NOTE]
   > 当 Visual Studio Code 启动工作流设计时 API 时，会出现一条消息，指示启动可能需要几秒钟时间。 您可以忽略此消息，也可以选择 **"确定"**。

   逻辑应用设计器出现后，设计器上会出现 " **选择操作** " 提示，并在默认情况下选中，其中显示了 " **添加操作** " 窗格。

   ![显示逻辑应用设计器的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 接下来，向工作流 [添加触发器和操作](#add-trigger-actions) 。

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>添加触发器和操作

从 **workflow.js在** 文件的快捷菜单中打开逻辑应用设计器后，设计器中将显示 " **选择操作** " 提示，并在默认情况下处于选中状态。 现在可以通过添加触发器和操作来开始创建工作流。

此示例中的逻辑应用工作流使用此触发器和以下操作：

* 内置 [请求触发器](../connectors/connectors-native-reqres.md)： **收到 HTTP 请求时**，接收入站调用或请求，并创建其他服务或逻辑应用可调用的终结点。

* [Office 365 Outlook 操作](../connectors/connectors-create-api-office365-outlook.md)**发送电子邮件**。

* 内置 [响应操作](../connectors/connectors-native-reqres.md)，用于发送答复并将数据返回给调用方。

### <a name="add-the-request-trigger"></a>添加“请求”触发器

1. 在设计器旁的 " **添加触发器** " 窗格的 " **选择操作** " 搜索框下，确保已选中 " **内置** "，以便可以选择本机运行的触发器。

1. 在 " **选择操作** " 搜索框中输入 `when a http request` ，并选择在 **收到 HTTP 请求时**命名的内置请求触发器。

   ![显示逻辑应用设计器的屏幕截图，并在选择 "收到 HTTP 请求时" 触发器 * * 添加触发器 * * 窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   当设计器上出现触发器时，触发器的详细信息窗格将打开，显示触发器的属性、设置和其他操作。

   ![显示逻辑应用设计器的屏幕截图，其中显示了 "收到 HTTP 请求时" 触发器，并打开了触发器详细信息窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 如果未显示详细信息窗格，请确保在设计器上选择了触发器。

1. 如果必须在设计器中删除项，请执行以下步骤：

   1. 在设计器中，选择项。

   1. 在将打开的项的详细信息窗格中，选择 "省略号 (**...** ") 按钮 " **>** **删除**"。 若要确认删除，请选择 **"确定"**。

      ![用 "打开详细信息" 窗格以及所选省略号按钮和 "删除" 选项在设计器上显示选定项的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>添加 Office 365 Outlook 操作

1. 在设计器的已添加的触发器下，选择 " **新建步骤**"。

   设计器上将显示 " **选择操作** " 提示，并重新打开 " **添加操作" 窗格** ，以便您可以选择下一个操作。

1. 在 " **添加操作** " 窗格中的 " **选择操作** " 搜索框下，选择 " **Azure** "，以便查找并选择在 azure 中部署的托管连接器的操作。

   此示例选择并使用 Office 365 Outlook 操作， ** (V2) 发送电子邮件 **。

   ![显示逻辑应用设计器的屏幕截图，并使用 Office 365 Outlook 的 "发送电子邮件" 操作选择 "添加操作" * * 窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. 在操作的详细信息窗格中，选择 " **登录** "，以便可以创建与电子邮件帐户的连接。

   ![显示逻辑应用设计器并 * * (V2 发送电子邮件的屏幕截图) * * 窗格，并选择 "登录"。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > 如果收到错误， `Failed to create connection...` 则可能是为逻辑应用选择了当前不受支持的区域。 添加更多区域的更新正在进行。 同时，对于目前支持的区域，请查看预览扩展的 " [已知问题" GitHub 页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)。

1. 如果 Visual Studio Code 提示你同意访问你的电子邮件帐户，请选择 " **打开**"。

   ![屏幕截图，显示允许访问的 Visual Studio Code 提示。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 若要防止将来出现提示，请选择 " **配置受信任的域** "，以便可以将 "身份验证" 页添加为受信任的域。

1. 按照后续提示进行登录、允许访问并允许返回 Visual Studio Code。

   > [!NOTE]
   > 如果在完成提示之前经过了太多的时间，则身份验证过程将超时并失败。 在这种情况下，请返回到设计器，然后重试登录以创建连接。

1. 当 Azure 逻辑应用预览扩展提示你同意访问你的电子邮件帐户时，请选择 " **打开**"。 遵循后续提示以允许访问。

   ![屏幕截图，显示允许访问的预览扩展提示。](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 若要防止将来出现提示，请选择 " **不再询问此扩展**"。

   Visual Studio Code 创建连接后，某些连接器会显示消息， `The connection will be valid for {n} days only.` 此时间限制仅适用于在 Visual Studio Code 中创作逻辑应用时的持续时间。 部署之后，此限制不再适用，因为逻辑应用可以在运行时通过使用其自动启用 [系统分配的托管标识](../logic-apps/create-managed-service-identity.md)进行身份验证。 此托管标识不同于在创建连接时使用的身份验证凭据或连接字符串。 如果禁用此系统分配的托管标识，则连接在运行时不起作用。

1. 在设计器中，如果 " **发送电子邮件** " 操作未显示选中状态，请选择该操作。

1. 在操作的详细信息窗格中的 " **参数** " 选项卡上，提供操作所需的信息，例如：

   ![显示逻辑应用设计器的屏幕截图，其中包含 Office 365 Outlook "发送电子邮件" 操作的详细信息。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | properties | 必须 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **收件人** | 是 | <*your-email-address*> | 电子邮件收件人，可以是你的电子邮件地址，用于测试目的。 此示例使用虚构电子邮件 `sophiaowen@fabrikam.com` 。 |
   | **主题** | 是 | `An email from your example workflow` | 电子邮件主题 |
   | **正文** | 是 | `Hello from your example workflow!` | 电子邮件正文内容 |
   ||||

   > [!NOTE]
   > 如果要在 " **设置**"、" **运行**" 或 " **静态结果** " 选项卡上的 "详细信息" 窗格中进行任何更改，请确保选择 " **完成** " 以在切换选项卡或将焦点更改为设计器之前提交这些更改。 否则，Visual Studio Code 不会保留你的更改。 有关详细信息，请查看预览扩展的 [已知问题 GitHub 页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

1. 在设计器中，选择 " **保存**"。

接下来，在 Visual Studio Code 中本地调试和测试工作流。

<a name="debug-test-locally"></a>

## <a name="debug-and-test-your-logic-app"></a>调试和测试逻辑应用

1. 为了帮助你更轻松地调试无状态逻辑应用工作流，你可以 [启用该工作流的运行历史记录](#run-history)。

1. 在 Visual Studio Code 工具栏上，打开 " **运行** " 菜单，然后选择 " **启动调试** (F5) "。

   此时将打开 **终端** 窗口，以便您可以查看调试会话。

1. 现在，在请求触发器上查找终结点的回调 URL。

   1. 重新打开 "资源管理器" 窗格，以便可以查看项目。

   1. 从文件快捷菜单上的 " **workflow.js** " 中，选择 " **概述**"。

      ![显示 "概述" 选定的文件上的 "资源管理器" 窗格和快捷 workflow.js窗口的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. 查找 " **回调 URL** " 值，该 url 与示例请求触发器的 url 类似：

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![显示工作流的 "概述" 页的屏幕截图，其中包含回叫 URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. 若要通过触发逻辑应用工作流来测试回调 URL，请打开 [Postman](https://www.postman.com/downloads/) 或用于创建和发送请求的首选工具。

   此示例通过使用 Postman 继续进行。 有关详细信息，请参阅 [Postman 入门](https://learning.postman.com/docs/getting-started/introduction/)。

   1. 在 Postman 工具栏上，选择 " **新建**"。

      ![显示 Postman 并选择 "新建" 按钮的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. 在 " **新建** " 窗格的 " **构建基块**" 下，选择 " **请求**"。

   1. 在 " **保存请求** " 窗口中的 " **请求名称**" 下，提供请求的名称，例如 `Test workflow trigger` 。

   1. 在 " **选择要保存到的集合或文件夹**" 下，选择 " **创建集合**"。

   1. 在 "**所有集合**" 下，提供要创建的集合的名称以组织你的请求，按 enter，然后选择 "**保存到 <*集合名称* > **"。 此示例使用 `Logic Apps requests` 作为集合名称。

      此时将打开 Postman 的请求窗格，以便可以将请求发送到请求触发器的回调 URL。

      ![用打开的请求窗格显示 Postman 的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. 返回 Visual Studio Code。 在工作流的 "概述" 页中，复制 " **回调 URL** " 属性值。

   1. 返回到 Postman。 在 "请求" 窗格中，单击 "方法列表"， **当前显示为** 默认请求方法，粘贴之前在 "地址" 框中复制的回调 URL，然后选择 " **发送**"。

      ![屏幕截图，显示已选中 "发送" 按钮的 "地址" 框中的 Postman 和回叫 URL](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      示例逻辑应用工作流发送的电子邮件类似于以下示例：

      ![显示 Outlook 电子邮件的屏幕截图，如示例中所述](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. 在 Visual Studio Code 中，返回到工作流的 "概述" 页。

   如果创建了有状态的工作流，则在发送的请求触发工作流后，"概述" 页将显示工作流的运行状态和历史记录。 有关运行状态的详细信息，请参阅 [查看运行历史记录](../logic-apps/monitor-logic-apps.md#review-runs-history)。

   ![显示运行状态和历史记录的工作流的 "概述" 页的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > 如果未显示运行状态，请尝试通过选择 " **刷新**" 来刷新 "概述" 页。

1. 若要查看特定运行中每个步骤的状态和步骤的输入和输出，请选择该运行的 "省略号 (**...** ") 按钮，然后选择 " **显示运行**"。

   ![显示工作流的运行历史记录行并选中 "显示运行" 的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code 显示每个操作的运行状态。

1. 若要查看每个步骤的输入和输出，请展开要检查的步骤。 若要进一步查看该步骤的原始输入和输出，请选择 " **显示原始输入** " 或 " **显示原始输出**"。

   ![屏幕截图，显示工作流中每个步骤的状态，以及展开的 "发送电子邮件" 操作中的输入和输出](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. 若要停止调试会话，请在 " **运行** " 菜单上，选择 " **停止调试** " (Shift + F5) 。

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>向调用方返回响应

若要将响应返回给向逻辑应用发送请求的调用方，可以将内置 [响应操作](../connectors/connectors-native-reqres.md) 用于以请求触发器开头的工作流。

1. 在逻辑应用设计器的 " **发送电子邮件** " 操作下，选择 " **新建步骤**"。

   设计器上将显示 " **选择操作** " 提示，并重新打开 " **添加操作" 窗格** ，以便您可以选择下一个操作。

1. 在 " **添加操作** " 窗格中的 " **选择操作** " 搜索框下，确保已选中 " **内置** "。 在搜索框中，输入 `response` ，然后选择 " **响应** " 操作。

   ![显示逻辑应用设计器并选择响应操作的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   当 **响应** 操作显示在设计器上时，操作的详细信息窗格将自动打开。

   ![显示逻辑应用设计器的屏幕截图，其中打开了 "响应" 操作的详细信息窗格并将 "正文" 属性设置为 "发送电子邮件" 操作的 "正文" 属性值。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. 在 " **参数** " 选项卡上，提供要调用的函数所需的信息。

   此示例返回 "**发送电子邮件**" 操作的输出的**正文**属性值。

   1. 在 " **正文** " 属性框中单击，以便显示动态内容列表，并显示前面的触发器中的可用输出值以及工作流中的操作。

      ![屏幕截图显示 "响应" 操作的详细信息窗格，其中鼠标指针位于 "Body" 属性内，以便显示动态内容列表。](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 在动态内容列表的 " **发送电子邮件**" 下，选择 " **正文**"。

      ![屏幕截图，显示打开的动态内容列表。 在列表中的 "发送电子邮件" 标头下，选择 "正文" 输出值。](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      完成后，"响应" 操作的 " **正文** " 属性将设置为 " **发送电子邮件** " 操作的 **正文** 输出值。

      ![屏幕截图，显示工作流中每个步骤的状态，以及展开的 "响应" 操作中的输入和输出。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. 在设计器中，选择 " **保存**"。

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>重新测试逻辑应用

对逻辑应用进行更新后，可以通过在 Visual Studio 中重新运行调试器并发送另一个请求来触发已更新的逻辑应用，以运行另一个测试，这与 [调试和测试逻辑应用](#debug-test-locally)中的步骤类似。

1. 在 Visual Studio Code 工具栏上，打开 " **运行** " 菜单，然后选择 " **启动调试** (F5) "。

1. 在 Postman 或用于创建和发送请求的工具中，发送另一个请求来触发工作流。

1. 如果创建有状态工作流，则在工作流的 "概述" 页上，检查最近运行的状态。 若要查看该运行中每个步骤的状态、输入和输出，请选择该运行的 "省略号 (**...** ") 按钮，然后选择 " **显示运行**"。

   例如，下面是在使用响应操作更新示例工作流之后运行的分步状态。

   ![屏幕截图，显示更新的工作流中每个步骤的状态，以及展开的 "响应" 操作中的输入和输出。](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. 若要停止调试会话，请在 " **运行** " 菜单上，选择 " **停止调试** " (Shift + F5) 。

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>发布到 Azure

从 Visual Studio Code 中，可以直接将项目部署到 Azure，这将使用新的 **逻辑应用 (预览版) ** 资源类型发布逻辑应用。 与 Azure Functions 中的函数应用资源类似，此新资源类型的部署要求你选择 [托管计划和定价层](../app-service/overview-hosting-plans.md)，你可以在部署期间进行设置。 有关托管计划和定价的详细信息，请参阅以下主题：

* [向上缩放 Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)

你可以将逻辑应用作为新资源发布，这将自动创建任何其他必要的资源，例如 [Azure 存储帐户，这与函数应用要求类似](../azure-functions/storage-considerations.md)。 或者，你可以将逻辑应用发布到以前部署的 **逻辑应用 (预览) ** 资源，这将覆盖该逻辑应用。

### <a name="publish-as-a-new-logic-app-preview-resource"></a>作为新的逻辑应用发布 (预览) 资源

1. 在 Visual Studio Code 工具栏上，选择 Azure 图标。

1. 在 **Azure：逻辑应用 (预览) ** 窗格工具栏上，选择 " **部署到逻辑应用**"。

   ![屏幕截图，显示 "Azure：逻辑应用 (预览") "窗格和窗格工具栏，并选中" 部署到逻辑应用 "。](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. 从 Visual Studio Code 打开的列表中，从以下选项中进行选择：

   * **在 Azure 中创建新的逻辑应用 (预览) ** (快速) 
   * **在 Azure Advanced 中创建新的逻辑应用 (预览) **
   * 以前部署的 **逻辑应用 (预览) ** 资源（如果存在）

   此示例将继续 **创建新的逻辑应用 (预览版) Azure Advanced**。

   ![屏幕截图显示 "Azure：逻辑应用 (预览) " 窗格，其中包含一个列表，其中列出了 "在 Azure 中创建新的逻辑应用 (预览) "。](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 若要创建新的 **逻辑应用 (预览) ** 资源，请执行以下步骤：

   1. 为新的逻辑应用提供全局唯一的名称，此名称是 **逻辑应用 (预览) ** 资源中使用的名称。 本示例使用 `example-logic-app-preview`。

      ![屏幕截图，显示 "Azure：逻辑应用 () 预览" 窗格，并提示提供要创建的新逻辑应用的名称。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. 为新逻辑应用选择托管计划，" [**应用服务计划**](../azure-functions/functions-scale.md#app-service-plan) " 或 " [**高级**](../azure-functions/functions-scale.md#premium-plan)"。 此示例选择 " **应用服务计划**"。

      ![屏幕截图显示 "Azure：逻辑应用 (预览") "窗格，并提示选择" 应用服务计划 "或" 高级 "。](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 创建新的应用服务计划或选择现有计划。 此示例选择 " **创建新的应用服务计划**"。

      ![屏幕截图，显示 "Azure：逻辑应用 () 预览" 窗格，并提示 "创建新应用服务计划" 或选择现有的应用服务计划。](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. 为应用服务计划提供名称，然后为该计划选择一个 [定价层](../app-service/overview-hosting-plans.md) 。 此示例选择 **F1 免费** 计划。

      ![屏幕截图，显示 "Azure：逻辑应用 (预览") "窗格，并提示选择定价层。](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 为了获得最佳性能，请查找并选择与部署项目相同的资源组。

      > [!NOTE]
      > 尽管可以创建或使用不同的资源组，但这样做可能会影响性能。 如果创建或选择其他资源组，但在出现确认提示后取消，则还会取消部署。

   1. 对于有状态工作流，选择 " **新建存储帐户** " 或 "现有存储帐户"。

      ![屏幕截图显示 "Azure：逻辑应用 (预览") "窗格，并提示创建或选择存储帐户。](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 为了更轻松地进行诊断日志记录和跟踪功能，你可以选择现有 Application Insights 资源。 否则，可以选择 " **创建新的 Application Insights 资源**"，或在部署应用后在 Azure 门户中设置 Application Insights。

      在部署之前，请确保将 `logLevel` 对象添加到位于 `logging` 项目根级别的 **host.js** 文件中的对象，并将设置 `Host.Triggers.Workflow` 为 `Information` ，例如：

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      下面是文件的 **host.js** 可能的外观：

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   完成后，Visual Studio Code 开始创建和部署发布逻辑应用所需的资源。

1. 若要查看和监视部署过程，请在 " **视图** " 菜单上选择 " **输出**"。 从 "输出" 窗口的工具栏列表中，选择 " **Azure 逻辑应用**"。

   ![屏幕截图，显示 "输出" 窗口，其中包含在工具栏列表中选择的 "Azure 逻辑应用" 以及部署进度和状态。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code 将逻辑应用工作流部署到 Azure 后，将显示以下消息：

   ![显示部署到 Azure 的消息已成功完成的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   恭喜，现在逻辑应用在 Azure 中处于活动状态，并在默认情况下启用。

接下来，你可以了解如何执行这些任务：

* 在 Azure 门户或[Visual Studio Code 中](#find-manage-deployed-workflows-vs-code)[找到已部署的逻辑应用](#find-manage-deployed-workflows-portal)。

* [启用无状态逻辑应用工作流的运行历史记录](#run-history)。

* [在已部署的逻辑应用上启用监视 ** (预览) ** 资源](#enable-monitoring)。

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>在 Visual Studio Code 中查找和管理已部署的逻辑应用

在 Visual Studio Code 中，可以查看 Azure 订阅中的所有已部署逻辑应用，无论它们是原始 **逻辑应用** 还是 **逻辑应用 (预览版) ** 资源类型，然后选择可帮助你管理这些逻辑应用的任务。 但是，若要访问这两种资源类型，需要使用 **Azure 逻辑应用** 和 **azure 逻辑应用 (预览) ** 扩展 Visual Studio Code。

1. 在左侧工具栏上，选择 "Azure" 图标。 在 " **Azure：逻辑应用 (预览") ** 窗格中，展开订阅，其中显示了该订阅的所有已部署的逻辑应用。

1. 找到并选择要管理的逻辑应用。 打开逻辑应用的快捷菜单，并选择要执行的任务。

   例如，可以选择停止、启动、重新启动或删除已部署逻辑应用等任务。

   ![显示 Visual Studio Code，其中显示了已打开的 "Azure 逻辑应用 (预览版) " 扩展窗格和部署的工作流。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. 若要查看逻辑应用中的所有工作流，请展开逻辑应用，然后展开 " **工作流** " 节点。

1. 若要查看特定工作流，请打开工作流的快捷菜单，然后选择 " **在设计器中打开**" （在只读模式下打开工作流）。

   若要编辑工作流，可以使用以下选项：

   * 在 Visual Studio Code 中，在逻辑应用设计器中打开项目的 **workflow.js** 文件，进行编辑，并将逻辑应用重新部署到 Azure。

   * 在 Azure 门户中，[找到并打开你的逻辑应用](#find-manage-deployed-workflows-portal)。 查找、编辑和保存工作流。

1. 若要在 Azure 门户中打开已部署的逻辑应用，请打开逻辑应用的快捷菜单，然后选择 " **在门户中打开**"。

   Azure 门户在浏览器中打开，如果登录到 Visual Studio Code 并显示逻辑应用，则会自动登录到门户。

   ![显示 Visual Studio Code 中逻辑应用的 "Azure 门户" 页的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   你还可以单独登录到 Azure 门户，使用门户搜索框查找逻辑应用，然后从结果列表中选择逻辑应用。

   ![显示所选逻辑应用的搜索结果的 Azure 门户和搜索栏的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>在门户中查找和管理已部署的逻辑应用

在 Azure 门户中，可以查看 Azure 订阅中的所有已部署逻辑应用，无论它们是原始 **逻辑应用** 资源类型还是 **逻辑应用 (预览版) ** 资源类型。 目前，在 Azure 中，每种资源类型都作为单独的类别进行组织和管理。

> [!NOTE]
> 对于公共预览版，只能在 Azure 门户 ** () 预览 ** 中查看已部署的逻辑应用，而不能 ** (预览) ** 资源创建新的逻辑应用。 只能在 Visual Studio Code 中创建这些逻辑应用。 但是，可以 [将工作流添加](#add-workflows) 到具有此资源类型的已部署逻辑应用。

若要查找逻辑 **应用 (预览) ** 资源类型的逻辑应用，请执行以下步骤：

1. 在 Azure 门户搜索框中输入 `logic app preview` 。 出现 "结果" 列表时，请在 " **服务**" 下选择 " **逻辑应用 (预览") **。

   ![屏幕截图，显示带有 "逻辑应用预览" 搜索文本的 Azure 门户搜索框。](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. 在 " **逻辑应用 (预览") ** 窗格中，查找并选择从 Visual Studio Code 部署的逻辑应用。

   ![显示在 Azure 中部署) 资源的 Azure 门户和逻辑应用 (预览的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure 门户打开所选逻辑应用的单个资源页。

   ![在 Azure 门户中显示逻辑应用工作流的资源页的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. 若要查看此逻辑应用的工作流，请在逻辑应用菜单上选择 " **工作流**"。

   " **工作流** " 窗格显示当前逻辑应用中的所有工作流。 此示例显示了你在 Visual Studio Code 中创建的工作流。

   ![屏幕截图，显示 "工作流" 窗格打开的 "逻辑应用 (预览) " 资源页和部署的工作流](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. 若要查看工作流，请在 " **工作** 流" 窗格中选择该工作流。

   "工作流" 窗格将打开，并显示可对该工作流执行的详细信息和任务。

   例如，若要查看工作流中的步骤，请选择 " **设计器**"。

   ![屏幕截图，显示所选工作流的 "概述" 窗格，而工作流菜单显示选定的 "设计器" 命令。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   逻辑应用设计器将打开并显示你在 Visual Studio Code 中生成的工作流。 你现在可以在 Azure 门户中对此工作流进行更改。

   ![显示 Visual Studio Code 部署的逻辑应用设计器和工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>将工作流添加到已部署的逻辑应用

通过 Azure 门户，您可以将空白工作流添加到 **逻辑应用中， (预览 ** 从 Visual Studio Code 部署) 资源并在 Azure 门户中生成这些工作流。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择已部署的 **逻辑应用 (预览版) ** 资源。

1. 在逻辑应用的菜单中，选择 " **工作流**"。 在 " **工作流** " 窗格上，选择 " **添加**"。

   ![屏幕截图，显示所选逻辑应用的 "工作流" 窗格和工具栏，并选中 "添加" 命令。](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. 在 " **新建工作流** " 窗格中，提供工作流的名称。 选择有 **状态** 或 **无状态** **>** **创建**。

   在 Azure 部署新工作流后（显示在 " **工作流** " 窗格上），选择该工作流以执行管理和其他任务，例如打开逻辑应用设计器或代码视图。

   ![显示具有管理和查看选项的所选工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   例如，打开新工作流的设计器将显示一个空白画布。 你现在可以在 Azure 门户中生成此工作流。

   ![显示逻辑应用设计器和空白工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>无状态逻辑应用工作流的运行历史记录

为了更轻松地调试无状态逻辑应用工作流，你可以在 Visual Studio Code 或 Azure 门户中启用该工作流的运行历史记录，然后在完成后禁用运行历史记录。

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Visual Studio Code 中的无状态逻辑应用工作流

如果在 Visual Studio Code 中本地处理并运行无状态逻辑应用工作流，请执行以下步骤：

1. 在项目中，找到并展开 **designtime** 文件夹。 查找并打开文件 ** 上的local.settings.js** 。

1. 添加 `Workflows.{yourWorkflowName}.operationOptions` 属性，并将值设置为 `WithStatelessRunHistory` ，例如：

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. 若要在完成后禁用运行历史记录，请删除 `Workflows.{yourWorkflowName}.OperationOptions` 属性及其值，或将属性设置为 `None` 。

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Azure 门户中的无状态逻辑应用工作流

如果已将项目部署到 Azure 门户，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，查找并打开 **逻辑应用 (预览) ** 资源。

1. 在逻辑应用的菜单的 " **设置**" 下，选择 " **配置**"。

1. 在 " **应用程序设置** " 选项卡上，选择 " **新建应用程序设置**"。

1. 在 " **添加/编辑应用程序设置** " 窗格上的 " **名称** " 框中，输入此操作选项名称： 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. 在 " **值** " 框中，输入以下值： `WithStatelessRunHistory`

   例如：

   ![屏幕截图，显示 Azure 门户和逻辑应用 (预览版) 资源，其中 "配置" > "新应用程序设置" < "添加/编辑应用程序设置" 窗格打开和 "工作流"。{yourWorkflowName}.OperationOptions "选项设置为" WithStatelessRunHistory "。](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. 完成后，请选择“确定”。 在 " **配置** " 窗格上，选择 " **保存**"。

若要在已部署的逻辑应用上启用监视 (预览) 资源，请转到下一部分。

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>为已部署的逻辑应用 (预览启用监视) 资源

若要在已部署的 **逻辑应用上启用监视 (预览) ** 资源，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择已部署的 **逻辑应用 (预览版) ** 资源。

1. 在该资源的菜单中的 " **API**" 下，选择 " **CORS**"。

1. 在 " **CORS** " 窗格的 " **允许的来源**" 下，将通配符添加 ( * ) 。

1. 完成后，在 " **CORS** " 工具栏上，选择 " **保存**"。

   ![屏幕截图，显示 (预览版) 资源的已部署逻辑应用的 Azure 门户。 在 "资源" 菜单中，选择 "CORS"，并将 "允许的来源" 的新条目设置为通配符 "*" 字符。](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>部署到 Docker 容器

通过使用 [.Net Core 命令行界面 (CLI) 工具](/dotnet/core/tools/)，可以生成项目，然后发布生成。 然后，你可以生成并使用 [Docker 容器](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) 作为逻辑应用工作流的部署目标。 有关详细信息，请查看以下主题：

* [容器和 Docker 简介](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET 和 Docker 简介](/dotnet/core/docker/introduction)
* [Docker 术语](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [教程：Docker 入门](/visualstudio/docker/tutorials/docker-tutorial)

1. 若要生成项目，请打开命令行提示符，并运行以下命令：

   `dotnet build -c release`

   有关详细信息，请参阅 [dotnet 生成](/dotnet/core/tools/dotnet-build/) 引用页。

1. 通过运行以下命令来发布生成：

   `dotnet publish -c release`

   有关详细信息，请参阅 " [dotnet publish](/dotnet/core/tools/dotnet-publish/) 引用" 页。

1. 使用用于 .NET 工作流的 Docker 文件生成 Docker 容器，并运行以下命令：

   `docker build --tag local/workflowcontainer .`

   例如，下面是一个示例 Docker 文件，用于部署有状态逻辑应用，并为用于将逻辑应用发布到 Azure 门户的 Azure 存储帐户指定连接字符串。 若要在 Azure 门户中查找和复制存储帐户的连接字符串，请参阅 [管理存储帐户密钥](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)。

   ![屏幕截图，显示具有存储帐户访问密钥和连接字符串的 Azure 门户。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   连接字符串类似于以下示例：

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   下面是 Docker 文件的格式：


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   有关详细信息，请参阅 [docker build](https://docs.docker.com/engine/reference/commandline/build/)。

1. 将字符串保存在一个安全的位置，以便稍后可以将该字符串添加到项目中的 **local.settings.js** 文件中，用于在 Visual Studio Code 中创建逻辑应用。

1. 使用以下命令在本地运行容器：

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   有关详细信息，请参阅 [docker run](https://docs.docker.com/engine/reference/commandline/run/)。

1. 若要获取请求触发器的回调 URL，请发送此请求：

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   在 Azure *master-key*存储帐户（在中为文件中的 " `AzureWebJobsStorage` **azure web 作业-密码/{部署名称}/host.js**" 设置）中定义 <主密钥> 值，可在其中找到此部分中的值：

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>有状态和无状态逻辑应用之间的嵌套行为差异

可以通过使用[请求](../connectors/connectors-native-reqres.md)触发器、 [HTTP Webhook](../connectors/connectors-native-webhook.md)触发器或具有[APICONNECTIONWEHOOK 类型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)并且可以接收 HTTPS 请求的托管连接器触发器，[使逻辑应用工作流](../logic-apps/logic-apps-http-endpoint.md)可从同一逻辑应用中存在的其他逻辑应用工作流调用， ** (预览) **资源。

下面是嵌套的逻辑应用工作流在父工作流调用子工作流之后可遵循的行为模式：

* 异步轮询模式

  父项不会等待对初始调用的响应，但会持续检查子的运行历史记录，直到子节点完成运行。 默认情况下，有状态工作流遵循此模式，这非常适合于可能超出 [请求超时限制](../logic-apps/logic-apps-limits-and-config.md)的长时间运行的子工作流。

* 同步模式 ( "火灾和遗忘" ) 

  子元素通过立即返回响应来确认调用 `202 ACCEPTED` ，而父级将继续执行下一个操作，而不会等待子中的结果。 相反，父项在子节点完成运行时接收结果。 不包括响应操作的子状态工作流始终遵循同步模式。 对于子状态工作流，可以使用运行历史记录查看。

  若要启用此行为，请在工作流的 JSON 定义中，将 `operationOptions` 属性设置为 `DisableAsyncPattern` 。 有关详细信息，请参阅 [触发器和操作类型-操作选项](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)。

* 触发器并等待

  对于子无状态工作流，父代等待返回子的结果的响应。 此模式的工作方式类似于使用内置 [HTTP 触发器或操作](../connectors/connectors-native-http.md) 来调用子工作流。 不包含响应操作的子无状态工作流立即返回 `202 ACCEPTED` 响应，但父代等待子项完成，然后继续下一个操作。 这些行为仅适用于子无状态工作流。

此表指定子工作流的行为，具体取决于父级和子级是有状态、无状态还是混合工作流类型：

| 父工作流 | 子工作流 | 子行为 |
|-----------------|----------------|----------------|
| 有状态 | 有状态 | 带有设置的异步或同步 `"operationOptions": "DisableAsyncPattern"` |
| 有状态 | 无状态 | 触发器并等待 |
| 无状态 | 有状态 | Synchronous |
| 无状态 | 无状态 | 触发器并等待 |
||||

## <a name="limits"></a>限制

虽然 [Azure 逻辑应用的许多现有限制](../logic-apps/logic-apps-limits-and-config.md) 对于此资源类型都是相同的，但以下是此公共预览版扩展的不同之处：

* 托管连接器：每个连接每分钟50请求

* 对于 JavaScript 操作的 [内联代码操作](../logic-apps/logic-apps-add-run-inline-code.md) ，这些限制已更改：

  * 代码的限制增加了1024个字符到100000个字符。

  * 运行代码所用的时间限制为5秒到15秒。

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>功能不可用或不受支持

对于此公共预览版，这些功能不可用或不受支持：

*  (预览创建新的 **逻辑应用) ** 资源当前在 macOS 上不可用。

* 若要启动工作流，请使用 [请求、HTTP、事件中心或服务总线触发器](../connectors/apis-list.md)。 目前，在此预览版中， [企业连接器](../connectors/apis-list.md#enterprise-connectors)、 [本地数据网关触发器](../connectors/apis-list.md#on-premises-connectors)、基于 Webhook 的触发器、滑动窗口触发器、 [自定义连接器](../connectors/apis-list.md#custom-apis-and-connectors)、集成帐户、项目和 [其连接器](../connectors/apis-list.md#integration-account-connectors) 不受支持。 "调用 Azure function" 功能不可用，因此目前请使用 HTTP *操作* 调用 Azure 函数的请求 URL。

  无状态逻辑应用工作流只能对 [托管连接器](../connectors/apis-list.md#managed-api-connectors)使用操作，而不能使用触发器。 除了之前指定的触发器，有状态工作流可以同时使用托管连接器的触发器和操作。

* 你可以将新的 **逻辑应用 (预览版仅) ** 资源类型部署到 [Azure 中的高级或应用服务托管计划](#publish-azure) ，或部署到 [Docker 容器](#deploy-docker)，而不是 [集成服务环境 (ISEs) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 **消耗** 托管计划不受支持，也不能用于部署此资源类型。

* 在 Azure 门户中，无法通过新 **逻辑应用 (预览版) ** 资源类型创建新的逻辑应用。 只能在 Visual Studio Code 中创建这些逻辑应用。 但是，将具有此资源类型的逻辑应用从 Visual Studio Code 部署到 Azure 后，可以 [将新工作流添加到这些逻辑应用](#add-workflows)。

## <a name="next-steps"></a>后续步骤

我们想要倾听你对此公共预览版扩展的体验！

* 对于 bug 或问题，请 [在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 对于问题、请求、评论和其他反馈，请 [使用此反馈表单](https://aka.ms/lafeedback)。
