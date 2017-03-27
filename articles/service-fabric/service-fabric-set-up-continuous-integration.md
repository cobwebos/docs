---
title: "为 Azure 微服务设置持续集成 | Microsoft Docs"
description: "大致了解如何使用 Visual Studio Team Services (VSTS) 为 Service Fabric 应用程序设置持续集成和部署。"
services: service-fabric
documentationcenter: na
author: mthalman-msft
manager: timlt
editor: 
ms.assetid: 3e8c2290-9e7a-456a-9b2c-db44d1b3988d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2016
ms.author: mthalman;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: f7edee399717ecb96fb920d0a938da551101c9e1
ms.openlocfilehash: 437e343425da5c8cfe71d4ae67c423fcc2b794c2


---
# <a name="set-up-service-fabric-continuous-integration-and-deployment-with-visual-studio-team-services"></a>使用 Visual Studio Team Services 设置 Service Fabric 持续集成和部署
本文介绍使用 Visual Studio Team Services (VSTS) 为 Azure Service Fabric 应用程序设置持续集成和部署的步骤。

本文档反映当前过程，应随时间推移而更改。

## <a name="prerequisites"></a>先决条件
请按照以下步骤开始：

1. 确保你有权访问 Team Services 帐户，否则请自行[创建一个](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。
2. 确保你有权访问 Team Services 团队项目，否则请自行[创建一个](https://www.visualstudio.com/docs/setup-admin/create-team-project)。
3. 确保你有一个可以向其部署应用程序的 Service Fabric 群集，或者使用 [Azure 门户](service-fabric-cluster-creation-via-portal.md)、[Azure Resource Manager 模板](service-fabric-cluster-creation-via-arm.md) 或 [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md) 创建一个。
4. 确保已创建 Service Fabric 应用程序 (.sfproj) 项目。 必须具有使用 Service Fabric SDK 2.1 或更高版本创建或升级的项目（.sfproj 文件应包含 1.1 或更高的 ProjectVersion 属性值）。

> [!NOTE]
> 不再需要自定义生成代理。 Team Services 托管代理现在预装了 Service Fabric 群集管理软件，因此可以直接从这些代理部署应用程序。
> 
> 

## <a name="configure-and-share-your-source-files"></a>配置和共享源文件
首先要做的事就是准备一个发布配置文件，供要在 Team Services 中执行的部署进程使用。  应将发布配置文件配置为针对此前已准备好的群集：

1. 在应用程序项目中选择要用于持续集成工作流的发布配置文件。 阅读[发布说明](service-fabric-publish-app-remote-cluster.md)，了解如何将应用程序发布到远程群集。 不过，你实际上不需要发布你的应用程序。 正确地完成配置操作以后，单击发布对话框中的“保存”超链接即可。
2. 如果你希望每次在 Team Services 中进行部署时都升级应用程序，则需对发布配置文件进行配置，使之允许升级。 在步骤 1 所使用的同一个发布对话框中，确保已勾选“**升级应用程序**”复选框。  详细了解如何[配置其他升级设置](service-fabric-visualstudio-configure-upgrade.md)。 单击“**保存**”超链接，将设置保存到发布配置文件。
3. 确保将更改保存到发布配置文件以后，即可取消发布对话框。
4. 现在可以在 Team Services 中[共享应用程序项目源文件](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs)。 能够在 Team Services 中访问源文件以后，即可转到下一步，即创建生成。 

## <a name="create-a-build-definition"></a>创建生成定义
Team Services 生成定义所描述的工作流由一系列按顺序执行的生成步骤组成。 之所以要创建生成定义，其目的是生成一个 Service Fabric 应用程序包和其他项目，然后即可使用该程序包和这些项目部署应用程序。 详细了解 Team Services [生成定义](https://www.visualstudio.com/docs/build/define/create)。

### <a name="create-a-definition-from-the-build-template"></a>从生成模板中创建定义
1. 在 Visual Studio Team Services 中打开团队项目。
2. 选择“**生成**”选项卡。
3. 选择绿色的 **+** 号以创建新的生成定义。
4. 在打开的对话框中，选择“**生成**”模板类别中的“**Azure Service Fabric 应用程序**”。
5. 选择“**下一步**”。
6. 选择与 Service Fabric 应用程序关联的存储库和分支。
7. 选择要使用的代理队列。 支持托管代理。
8. 选择“创建” 。
9. 保存生成定义并为其命名。
10. 下面的段落是由此模板生成的生成步骤的说明：

| 生成步骤 | 说明 |
| --- | --- |
| Nuget 还原 |还原解决方案的 NuGet 包。 |
| 生成解决方案 \*.sln |生成整个解决方案。 |
| 生成解决方案 \*.sfproj |生成用于部署应用程序的 Service Fabric 应用程序包。 应用程序包位置已指定在生成的项目目录内。 |
| 更新 Service Fabric 应用版本 |更新应用程序包的清单文件中包含的版本值，使之支持升级。 有关详细信息，请参阅[任务文档页](https://go.microsoft.com/fwlink/?LinkId=820529)。 |
| 复制文件 |将发布配置文件和应用程序参数文件复制到生成的项目中，使之能够用于部署。 |
| 发布项目 |发布生成的项目。 允许发布定义使用生成的项目。 |

### <a name="verify-the-default-set-of-tasks"></a>验证任务的默认设置
1. 验证“**NuGet 还原**”和“**生成解决方案**”生成步骤的“**解决方案**”输入字段。  默认情况下，将对关联存储库中包含的所有解决方案文件执行这些生成步骤。  如果你只想在这其中的一个解决方案文件上运行生成定义，则需显式更新该文件的路径。
2. 验证“**打包应用程序**”生成步骤的“**解决方案**”输入字段。  默认情况下，此生成步骤假定存储库中只存在一个 Service Fabric 应用程序项目 (.sfproj)。  如果存储库中有多个这样的文件，而你只想针对其中一个来完成此生成定义，则需显式更新该文件的路径。  如果想将多个应用程序项目打包到存储库中，则需在生成定义中创建其他 **Visual Studio 生成**步骤，使每个步骤都对应一个应用程序项目。  然后，还需针对每个这样的生成步骤更新“**MSBuild 参数**”字段，使包位置对每个这样的生成步骤来说都是唯一的。
3. 验证“**更新 Service Fabric 应用版本**”生成步骤中定义的版本控制行为。  默认情况下，此生成步骤会将生成号追加到应用程序包的清单文件中的所有版本值。 有关详细信息，请参阅[任务文档页](https://go.microsoft.com/fwlink/?LinkId=820529)。 这对提供应用程序升级支持很有用，因为每个升级部署都需要前一部署中的不同版本值。 如果你不打算在工作流中使用应用程序升级，则可考虑禁用此生成步骤。 如果你的意图是生成可用于覆盖现有 Service Fabric 应用程序的内部版本，则必须禁用它。 如果编译生成的应用程序版本与群集中的应用程序版本不匹配，则部署将失败。
4. 如果解决方案包含 .NET Core 项目，则必须确保生成定义包含可还原依赖项的生成步骤：
   1. 选择“**添加生成步骤...**”。
   2. 找到“实用工具”选项卡中的“命令行”任务，单击其“添加”按钮。
   3. 对于任务的输入字段，请使用以下值：
   4. 工具：dotnet
   5. 参数：restore
   6. 拖动该任务，以便它在执行 **NuGet 还原**步骤后立即可用。
5. 保存对生成定义所做的任何更改。

### <a name="try-it"></a>试用
选择“**为生成排队**”以手动启动生成。 生成还会触发推送或签入。 验证生成可以成功执行以后，即可继续操作，以便定义将应用程序部署到群集的发布定义。

## <a name="create-a-release-definition"></a>创建发布定义
Team Services 发布定义所描述的工作流由一系列按顺序执行的任务组成。 之所以要创建发布定义，其目的是获取应用程序包并将其部署到群集。 一起使用时，生成定义和发布定义可以执行从开始到结束的整个工作流，即一开始只有源文件，而结束时群集中会有一个运行的应用程序。 详细了解 Team Services [发布定义](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

### <a name="create-a-definition-from-the-release-template"></a>从发布模板中创建定义
1. 在 Visual Studio Team Services 中打开你的项目。
2. 选择“**发布**”选项卡。
3. 选择绿色的 **+** 号以创建新的发布定义，然后在菜单中选择“**创建发布定义**”。
4. 在打开的对话框中，选择“**部署**”模板类别中的“**Azure Service Fabric 部署**”。
5. 选择“**下一步**”。
6. 选择生成定义，以便将其用作此发布定义的源。  发布定义将引用由所选生成定义生成的项目。
7. 如果希望在生成完成时 Team Services 能够自动创建新发布并部署 Service Fabric 应用程序，请选中“**连续部署**”复选框。
8. 选择要使用的代理队列。 支持托管代理。
9. 选择“创建” 。
10. 通过单击页面顶部的铅笔图标编辑定义名称。
11. 选择要将您的应用程序从任务的“**群集连接**”输入字段部署到其中的群集。 群集连接提供的必要信息可以让部署任务连接到群集。 如果尚未为群集设置群集连接，可选择字段旁的“**管理**”超链接添加一个。 在打开的页面上，执行以下步骤：
    1. 选择“**新建服务终结点**”，然后从菜单中选择“**Azure Service Fabric**”。
    2. 选择此终结点针对的群集所要使用的身份验证类型。
    3. 在“**连接名称**”字段中定义连接的名称。  通常情况下，你会使用群集的名称。
    4. 在“**群集终结点**”字段中定义客户端连接终结点 URL。  示例：https://contoso.westus.cloudapp.azure.com:19000。
    5. 对于 Azure Active Directory 凭据，可在“**用户名**”和“**密码**”字段中定义需要用来连接到群集的凭据。
    6. 对于基于证书的身份验证，可在“**客户端证书**”字段中定义客户端证书文件的 Base64 编码。  若要了解如何获取该值，请参阅有关该字段的弹出帮助。  如果证书受密码保护，可在“**密码**”字段中定义密码。
    7. 单击“**确定**”确认所做的更改。 导航回发布定义以后，单击“**群集连接**”字段的刷新图标即可查看刚添加的终结点。
12. 保存发布定义。

> [!NOTE]
> Azure Active Directory 身份验证不支持 Microsoft 帐户（例如，@hotmail.com 或 @outlook.com)）。
> 
> 

创建的定义包含类型 **Service Fabric 应用程序部署**的一个任务。 有关此任务的详细信息，请参阅[任务文档页](https://go.microsoft.com/fwlink/?LinkId=820528)。

### <a name="verify-the-template-defaults"></a>验证模板默认值
1. 验证“**部署 Service Fabric 应用程序**”任务的“**发布配置文件**”输入字段。 默认情况下，此字段引用包含在生成的项目中的名为 Cloud.xml 的发布配置文件。 如果你要引用其他发布配置文件，或者如果生成在其项目中包含多个应用程序包，则需对路径进行相应更新。
2. 验证“**部署 Service Fabric 应用程序**”任务的“**应用程序包**”输入字段。 默认情况下，此字段引用在生成定义模板中使用的默认应用程序包路径。  如果已在生成定义中修改默认应用程序包路径，则还需在此处对路径进行相应更新。

### <a name="try-it"></a>试用
从“**发布**”按钮菜单中选择“**创建发布**”，以便手动创建发布。 在打开的对话框中，按需要选择发布所基于的生成，然后单击“**创建**”。 如果已启用连续部署，则当关联的生成定义完成某个生成时，会自动创建发布。

## <a name="next-steps"></a>后续步骤
若要了解有关与 Service Fabric 应用程序持续集成的详细信息，请阅读以下文章：

* [Team Services 文档主页](https://www.visualstudio.com/docs/overview)
* [Team Services 中的生成管理](https://www.visualstudio.com/docs/build/overview)
* [Team Services 中的发布管理](https://www.visualstudio.com/docs/release/overview)




<!--HONumber=Jan17_HO4-->


