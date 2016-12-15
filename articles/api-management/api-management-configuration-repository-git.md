---
title: "如何使用 Git 保存和配置 API 管理服务"
description: "了解如何使用 Git 保存和配置 API 管理服务。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b3cec0fd2547b68ff3795fd7a4c22fe927eb2a4f


---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>如何使用 Git 保存和配置 API 管理服务
> [!IMPORTANT]
> API 管理的 Git 配置当前处于预览中。 它具备完整功能，但处于预览中是因为我们正在积极寻求关于此功能的反馈。 为了响应客户反馈，我们可能进行重大更改，因此我们建议在生产环境中使用时不要依赖此功能。 如有任何反馈或疑问，请在 `apimgmt@microsoft.com` 告知我们。
> 
> 

每个 API 管理服务实例都保留一个配置数据库，该数据库包含关于服务实例的配置和元数据的信息。 可能通过在发布者门户中更改设置、使用 PowerShell cmdlet 或进行 REST API 调用对服务实例进行更改。 除了这些方法，还可以使用 Git 管理服务实例配置，从而支持下列服务管理方案：

* 配置版本控制 - 下载和存储不同版本的服务配置
* 批量配置更改 - 通过单个操作，在本地存储库中对服务配置的多个部分进行更改，并将更改集成回服务器
* 熟悉的 Git 工具链和工作流 - 使用已经熟悉的 Git 工具和工作流

下图显示配置 API 管理服务实例的不同方式的概述。

![Git 配置][api-management-git-configure]

使用发布者门户、PowerShell cmdlet 或 REST API 对服务进行更改时，即表示正在使用 `https://{name}.management.azure-api.net` 终结点管理服务配置数据库，如图右侧所示。 图左侧说明了如何针对位于 `https://{name}.scm.azure-api.net` 的服务使用 Git 和 Git 存储库管理服务配置。

以下步骤提供了使用 Git 管理 API 管理服务实例的概述。

1. 在服务中启用 Git 访问
2. 将服务配置数据库保存到 Git 存储库
3. 将 Git 存储库克隆到本地计算机
4. 将最新的存储库提取到本地计算，并将更改提交并推送回存储库
5. 将更改从存储库部署到服务配置数据库中

本文介绍如何启用和使用 Git 管理服务配置，并提供 Git 存储库中的文件和文件夹的参考。

## <a name="to-enable-git-access"></a>启用 Git 访问
可通过查看发布者门户右上角的 Git 图标快速查看 Git 配置的状态。 在此示例中，尚未启用 Git 访问。

![Git 状态][api-management-git-icon-enable]

若要查看和配置 Git 配置设置，可单击 Git 图标，或者单击“安全”菜单并导航到“配置存储库”选项卡。

![启用 Git][api-management-enable-git]

若要启用 Git 访问，请选中“启用 Git 访问”复选框。

片刻之后，将保存更改并显示确认消息。 请注意，Git 图标已更改为指示 Git 访问处于启用状态，并且状态消息现在指示存储库存在未保存的更改。 这是因为 API 管理服务配置数据库尚未保存到存储库。

![Git 已启用][api-management-git-enabled]

> [!IMPORTANT]
> 未定义为属性的任何机密都将保存在存储库中，并将保留在其历史记录中，直到你禁用并重新启用 Git 访问。 属性提供了管理所有 API 配置和策略的常量字符串值（包括机密）的安全位置，因此无需将它们直接存储在策略声明中。 有关详细信息，请参阅[如何在 Azure API 管理策略中使用属性](api-management-howto-properties.md)。
> 
> 

有关使用 REST API 启用或禁用 Git 访问的信息，请参阅[使用 REST API 启用或禁用 Git 访问](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit)。

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>将服务配置保存到 Git 存储库
克隆存储库之前的第一个步骤是将服务配置的当前状态保存到存储库。 单击“将配置保存到存储库”。

![保存配置][api-management-save-configuration]

在确认屏幕上进行任何所需的更改，然后单击“确定”保存。

![保存配置][api-management-save-configuration-confirm]

片刻后配置已保存，并显示存储库的配置状态，包括上次配置更改和服务配置与存储库之间上次同步的日期和时间。

![配置状态][api-management-configuration-status]

将配置保存到存储库后，可以克隆它。

有关使用 REST API 执行此操作的信息，请参阅[使用 REST API 提交配置快照](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot)。

## <a name="to-clone-the-repository-to-your-local-machine"></a>将存储库克隆到本地计算机
若要克隆存储库，需要存储库的 URL、用户名和密码。 用户名和 URL 显示在“配置存储库”选项卡顶部附近。

![Git 克隆][api-management-configuration-git-clone]

密码在“配置存储库”选项卡底部生成。

![生成密码][api-management-generate-password]

若要生成密码，先确保“到期”设置为所需的到期日期和时间，然后单击“生成令牌”。

![密码][api-management-password]

> [!IMPORTANT]
> 记下此密码。 离开此页面后，不会再次显示该密码。
> 
> 

以下示例使用 [Git for Windows](http://www.git-scm.com/downloads) 中的 Git Bash 工具，但你可以使用你熟悉的任何 Git 工具。

使用发布者门户提供的命令，在所需文件夹中打开 Git 工具并运行以下命令以将 Git 存储库克隆到本地计算机。

    git clone https://bugbashdev4.scm.azure-api.net/ 

出现提示时，输入用户名和密码。

如果收到任何错误，请尝试将 `git clone` 命令修改为包含用户名和密码，如以下示例所示。

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

如果这提供了一个错误，请尝试对命令的密码部分进行 URL 编码。 执行此操作的一个快速方法是打开 Visual Studio，然后在“即时窗口”中发出以下命令。 若要打开“即使窗口”，请在 Visual Studio 中打开任意解决方案或项目（或创建新的空白控制台应用程序），然后从“调试”菜单中依次选择“Windows”、“即时”。

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

将编码密码与用户名和存储库位置一起用于构造 Git 命令。

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

克隆存储库后，可在本地文件系统中查看和处理它。 有关详细信息，请参阅[本地 Git 存储库的文件和文件夹结构参考](#file-and-folder-structure-reference-of-local-git-repository)。

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>使用最新服务实例配置更新本地存储库
如果在发布者门户中或使用 REST API 对 API 管理服务实例进行更改，必须先将这些更改保存到存储库，然后才能使用更新更改更新本地存储库。 若要执行此操作，请单击发布者门户中的“配置存储库”选项卡上的“将配置保存到存储库”，然后在本地存储库中发布以下命令。

    git pull

运行 `git pull` 之前，请确保自己位于本地存储库的文件夹中。 如果刚刚完成 `git clone` 命令，则必须通过运行如下命令将目录更改为存储库。

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>将更改从本地存储库推送到服务器存储器
若要将更改从本地存储库推送到服务器存储库，必须提交更改，然后将它们推送到服务器存储库。 若要提交更改，请打开 Git 命令工具、切换到本地存储库的目录，然后发出以下命令。

    git add --all
    git commit -m "Description of your changes"

若要将所有提交推送到服务器，请运行以下命令。

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>将任何服务配置更改部署到 API 管理服务实例
将本地更改提交并推送到服务器存储库后，可将它们部署到 API 管理服务实例。

![部署][api-management-configuration-deploy]

有关使用 REST API 执行此操作的信息，请参阅[使用 REST API 将 Git 更改部署到配置数据库](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges)。

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>本地 Git 存储库的文件和文件夹结构参考
本地 Git 存储库中的文件和文件夹包含有关服务实例的配置信息。

| 项目 | 说明 |
| --- | --- |
| 根 api-management 文件夹 |包含服务实例的顶级配置 |
| apis 文件夹 |包含服务实例中的 API 的配置 |
| groups 文件夹 |包含服务实例中的组的配置 |
| policies 文件夹 |包含服务实例中的策略 |
| portalStyles 文件夹 |包含服务实例中的开发人员门户自定义的配置 |
| products 文件夹 |包含服务实例中产品的配置 |
| templates 文件夹 |包含服务实例中电子邮件模板的配置 |

每个文件夹都可包含一个或多个文件，并且在某些情况下可包含一个或多个文件夹，例如，每个 API、产品或组对应一个文件夹。 每个文件夹内的文件都特定于文件夹名称所述的实体类型。

| 文件类型 | 目的 |
| --- | --- |
| json |关于相应实体的配置信息 |
| html |关于实体的说明，通常显示在开发人员门户中 |
| xml |策略语句 |
| css |用于开发人员门户自定义的样式表 |

可在本地文件系统中创建、删除、编辑和管理这些文件，并将更改部署回 API 管理服务实例。

> [!NOTE]
> 以下实体不包含在 Git 存储库中，并且无法使用 Git 进行配置。
> 
> * 用户
> * 订阅
> * 属性
> * 样式以外的开发人员门户实体
> 
> 

### <a name="root-api-management-folder"></a>根 api-management 文件夹
根 `api-management` 文件夹包含 `configuration.json` 文件，该文件包含采用以下格式的关于服务器实例的顶级信息。

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

前四个设置（`RegistrationEnabled`、`UserRegistrationTerms`、`UserRegistrationTermsEnabled` 和 `UserRegistrationTermsConsentRequired`）映射到“安全”部分中的“标识”选项卡上的以下设置。

| 标识设置 | 映射到 |
| --- | --- |
| RegistrationEnabled |“将匿名用户重定向到登录页”复选框 |
| UserRegistrationTerms |“用户登录时的使用条款”文本框 |
| UserRegistrationTermsEnabled |“显示用户登录时的使用条款”复选框 |
| UserRegistrationTermsConsentRequired |“需要同意”复选框 |

![标识设置][api-management-identity-settings]

接下来的四个设置（`DelegationEnabled`、`DelegationUrl`、`DelegatedSubscriptionEnabled` 和 `DelegationValidationKey`）映射到“安全”部分中的“委派”选项卡上的以下设置。

| 委派设置 | 映射到 |
| --- | --- |
| DelegationEnabled |“委派登录和注册”复选框 |
| DelegationUrl |“委派终结点 URL”文本框 |
| DelegatedSubscriptionEnabled |“委派产品订阅”复选框 |
| DelegationValidationKey |“委派验证密钥”文本框 |

![委派设置][api-management-delegation-settings]

最后的设置 `$ref-policy` 映射到服务实例的全局策略声明文件。

### <a name="apis-folder"></a>apis 文件夹
`apis` 文件夹针对服务实例中的每个 API 都包含一个文件夹，该文件夹包含以下项目。

* `apis\<api name>\configuration.json` - 这是 API 的配置，包含关于后端服务 URL 和操作的信息。 这是使用 `export=true` 以 `application/json` 格式调用[获取特定 API](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) 时将返回的相同信息。
* `apis\<api name>\api.description.html` - 这是 API 的说明，对应于 [API 实体](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties)的 `description` 属性。
* `apis\<api name>\operations\` - 此文件夹包含映射到 API 中的操作的 `<operation name>.description.html` 文件。 每个文件包含 API 中单个操作的说明，该说明映射到 REST API 中[操作实体](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties)的 `description` 属性。

### <a name="groups-folder"></a>groups 文件夹
`groups` 文件夹针对服务实例中定义的每个组都包含一个文件夹。

* `groups\<group name>\configuration.json` - 这是组的配置。 这是调用[获取特定组](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup)操作时将返回的相同信息。
* `groups\<group name>\description.html` - 这是组的说明，对应于[组实体](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties)的 `description` 属性。

### <a name="policies-folder"></a>policies 文件夹
`policies` 文件夹包含服务实例的策略声明。

* `policies\global.xml` - 包含在服务实例的全局范围内定义的策略。
* `policies\apis\<api name>\` - 如果有任何在 API 范围内定义的策略，它们包含在此文件夹中。
* `policies\apis\<api name>\<operation name>\` 文件夹 - 如果有任何在操作范围内定义的策略，它们以映射到每个操作的策略声明的 `<operation name>.xml` 文件形式包含在此文件夹中。
* `policies\products\` - 如果有任何在产品范围内定义的策略，它们包含在此文件夹中，该文件夹包含映射到每个产品的策略声明的 `<product name>.xml` 文件。

### <a name="portalstyles-folder"></a>portalStyles 文件夹
`portalStyles` 文件夹包含用于服务实例的开发人员门户自定义的配置和样式表。

* `portalStyles\configuration.json` - 包含开发人员门户使用的样式表名称
* `portalStyles\<style name>.css` - 每个 `<style name>.css` 文件都包含开发人员门户的样式（默认为 `Preview.css` 和 `Production.css`）。

### <a name="products-folder"></a>products 文件夹
`products` 文件夹针对服务实例中定义的每个产品都包含一个文件夹。

* `products\<product name>\configuration.json` - 这是产品的配置。 这是调用[获取特定产品](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct)操作时将返回的相同信息。
* `products\<product name>\product.description.html` - 这是产品的说明，对应于 REST API 中[产品实体](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product)的 `description` 属性。

### <a name="templates"></a>模板
`templates` 文件夹包含服务实例的[电子邮件模板](api-management-howto-configure-notifications.md)配置。

* `<template name>\configuration.json` - 这是电子邮件模板的配置。
* `<template name>\body.html` - 这是电子邮件模板的正文。

## <a name="next-steps"></a>后续步骤
有关管理服务实例的其他方法的信息，请参阅：

* 使用以下 PowerShell cmdlet 管理服务实例
  * [服务部署 PowerShell cmdlet 参考](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [服务管理 PowerShell cmdlet 参考](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* 在发布者门户中管理服务实例
  * [管理第一个 API](api-management-get-started.md)
* 使用 REST API 管理服务实例
  * [API 管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>观看视频概述
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Configuration-over-Git/player]
> 
> 

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png







<!--HONumber=Nov16_HO3-->


