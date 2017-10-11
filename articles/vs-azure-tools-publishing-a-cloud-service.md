---
title: "使用 Azure Tools 发布云服务 | Microsoft Docs"
description: "了解如何使用 Visual Studio 发布 Azure 云服务项目。"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/14/2017
ms.author: kraigb
ms.openlocfilehash: e617d600dbc8287eea737fc4969833e873365288
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>使用 Azure Tools 发布云服务
通过使用 Azure Tools for Microsoft Visual Studio，可直接从 Visual Studio 发布 Azure 应用程序。 Visual Studio 支持以集成方式将云服务发布到暂存或生产环境中。

必须具有 Azure 订阅，才能发布 Azure 应用程序。 此外还必须设置应用程序使用的云服务和存储帐户。 可以在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中完成这些设置。

> [!IMPORTANT]
> 发布时，可以选择云服务的部署环境。 还必须选择用来存储部署的应用程序包的存储帐户。 部署后，从存储帐户中删除应用程序包。
> 
> 

开发和测试 Azure 应用程序时，可以针对 Web 角色使用 Web 部署以增量方式发布更改。 在将应用程序发布到部署环境后，Web 部署允许部署运行 Web 角色虚拟机的直接更改。 不必在每次更新 Web 角色以测试更改时，都打包或发布整个 Azure 应用程序。 通过此方法，能够让 Web 角色更改可用于云中测试，而无需等待应用程序发布到部署环境。

请遵循以下过程来发布 Azure 应用程序，并使用 Web 部署来更新 Web 角色：

* 从 Visual Studio 发布或打包 Azure 应用程序
* 在开发和测试周期中更新 Web 角色

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>从 Visual Studio 发布或打包 Azure 应用程序
发布 Azure 应用程序时，可以执行以下任务之一：

* 创建服务包：可以使用此包和服务配置文件，从 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中，将应用程序发布到部署环境。
* 从 Visual Studio 中发布 Azure 项目：要将应用程序直接发布到 Azure，请使用发布向导。 有关信息，请参阅 [Publish Azure Application Wizard](vs-azure-tools-publish-azure-application-wizard.md)（发布 Azure 应用程序向导）。

### <a name="to-create-a-service-package-from-visual-studio"></a>从 Visual Studio 中创建服务包
1. 已准备好发布应用程序时，打开解决方案资源管理器，打开包含角色的 Azure 项目的快捷菜单，并选择“发布”。
2. 如果只是要创建服务包，请执行以下步骤：  
   
   1. 在 Azure 项目的快捷菜单上，选择“打包”。
   2. 在“打包 Azure 应用程序”对话框中，选择要为其创建包的服务配置，并选择生成配置。
   3. （可选）要在云服务发布之后为它启用远程桌面，请选择“为所有角色启用远程桌面”复选框，并选择“设置”链接以配置远程桌面。 如果要在发布云服务之后对其进行调试，请选中“为所有角色启用远程调试器”以启用远程调试。
      
      有关详细信息，请参阅 [Using Remote Desktop with Azure Roles](vs-azure-tools-remote-desktop-roles.md)（对 Azure 角色使用远程桌面）。
   4. 若要创建该包，请选择“打包”链接。
      
      文件资源管理器会显示新创建包的文件位置。 可以复制此位置，以便能够从 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)使用它。
   5. 要将此包发布到部署环境，必须在创建云服务时使用此位置作为“包位置”，且必须通过 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)将此包部署到环境中。
3. （可选）若要取消部署过程，请在活动日志中的行项目的快捷菜单中选择“取消并删除”。 这会停止部署过程，并从 Azure 中删除部署环境。
   
   > [!NOTE]
   > 若要在已完成部署后删除此部署环境，必须使用 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)。
   > 
   > 
4. （可选）启动角色实例后，Visual Studio 会自动在服务器资源管理器的“云服务”节点中显示部署环境。 可以从此位置查看单个角色实例的状态。 请参阅 [Managing Azure resources with Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md)（使用云资源管理器管理 Azure 资源）。下图显示了仍处于“正在初始化”状态中的角色实例：
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>在开发和测试周期中更新 Web 角色
如果应用程序的后端基础结构是稳定的，但 Web 角色需要更加频繁的更新，可以使用 Web 部署，仅更新项目中的一个 Web 角色。 不希望重新生成和重新部署后端辅助角色时，或者有多个 Web 角色但只希望更新其中一个 Web 角色时，此方法非常方便。

### <a name="requirements"></a>要求
以下是使用 Web 部署来更新 Web 角色的要求：

* **仅限开发和测试用途**：更改直接作用于运行 Web 角色的虚拟机。 如果必须回收此虚拟机，则将丢失此更改，因为发布的原始包已用于重建角色虚拟机。 必须重新发布应用程序才能获取 Web 角色的最新更改。
* **只能更新 Web 角色：**无法更新辅助角色。 此外，无法在 web role.cs 中更新 RoleEntryPoint。
* **只能支持 Web 角色的单个实例：**不能在部署环境中使用任何 Ｗeb 角色的多个实例。 但是，支持仅含一个实例的多个 Web 角色。
* **必须启用远程桌面连接：**这是必备条件，以便 Web 部署可以使用该用户和密码来连接到虚拟机，从而将更改部署到运行 Internet Information Services (IIS) 的服务器。 此外，可能需要连接到虚拟机才能将可信证书添加到此虚拟机上的 IIS。 （这样可以确保 Web 部署使用的 IIS 远程连接是安全的。）

以下过程假设正在使用“发布 Azure 应用程序”  向导。

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>发布应用程序时启用 Web 部署
1. 若要启用“为所有 Web 角色启用 Web 部署”复选框，必须先配置远程桌面连接。 选择“为所有角色启用远程桌面”，然后在显示的“远程桌面配置”框中提供将用于远程连接的凭据。 有关详细信息，请参阅 [Using Remote Desktop with Azure Roles](vs-azure-tools-remote-desktop-roles.md)（对 Azure 角色使用远程桌面）。
2. 若要为应用程序中的所有 Web 角色启用 Web 部署，请选择“为所有 Web 角色启用 Web 部署”。
   
    此时会显示一个黄色的警告三角形。 默认情况下，Web 部署使用受信任的自签名证书，因此不建议上传敏感数据。 如果需要保护敏感数据的进程，可以添加 SSL 证书用于 Web 部署连接。 此证书需是可信证书。 有关如何执行此操作的信息，请参阅本主题下文中的**确保 Web 部署安全**部分。
3. 选择“下一步”显示“摘要”屏幕，并选择“发布”以部署云服务。
   
    随后将发布云服务。 创建的虚拟机为 IIS 启用了远程连接，以便能够使用 Web 部署来更新 Web 角色，而无需重新发布它们。
   
   > [!NOTE]
   > 如果为 Web 角色配置了多个实例，会显示一条警告消息，指出在为发布应用程序而创建的包中，每个 Web 角色都将限制为仅有一个实例。 选择“确定”以继续。 如“要求”部分中所述，可以有多个 Web 角色，但每个角色只能有一个实例。
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>使用 Web 部署来更新 Web 角色
1. 要使用 Web 部署，请在 Visual Studio 中，针对所有 Web 角色，对要发布的项目进行代码更改，并在解决方案中，右键单击此项目节点并指向“发布”。 此时显示“发布 Web”  对话框。
2. （可选）如果添加了受信任的 SSL 证书，以便使用 IIS 的远程连接，则可以清除“允许不受信任的证书”复选框。 有关如何添加证书以确保 Web 部署安全的信息，请参阅本主题下文中的**确保 Web 部署安全**部分。
3. 要使用 Web 部署，发布机制需要使用在首次发布包时为远程桌面连接设置的用户名和密码。
   
   1. 在“用户名”中输入用户名。
   2. 在“密码”中输入密码。
   3. （可选）如果要在此配置文件中保存此密码，请选择“保存密码”。
4. 若要发布对 Web 角色所做的更改，请选择“发布”。
   
    状态行会显示“发布已启动” 。 完成发布后，会显示“发布成功”  。 更改现已部署到虚拟机上的 Web 角色。 现在可以在 Azure 环境中启动 Azure 应用程序以测试更改。

### <a name="to-make-web-deploy-secure"></a>确保 Web 部署安全
1. 默认情况下，Web 部署使用受信任的自签名证书，因此不建议上传敏感数据。 如果需要保护敏感数据的进程，可以添加 SSL 证书用于 Web 部署连接。 此证书必须是从证书颁发机构 (CA) 获取的受信任证书。
   
    要针对每个 Web 角色的每台虚拟机确保 Web 部署安全，必须将用于 Web 部署的可信证书上传到 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)。 这可以确保在发布应用程序时，将证书添加到为 Web 角色创建的虚拟机。
2. 要将受信任的 SSL 证书添加到 IIS 以用于远程连接，请遵循以下步骤：
   
   1. 要连接到正在运行 Web 角色的虚拟机，请单击“云资源管理器”或“服务器资源管理器”中的 Web 角色实例，并选择“使用远程桌面连接”。 有关如何连接到虚拟机的详细步骤，请参阅 [Using Remote Desktop with Azure Roles](vs-azure-tools-remote-desktop-roles.md)（对 Azure 角色使用远程连接）。
      
      浏览器会提示下载 .RDP 文件。
   2. 若要添加 SSL 证书，请在 IIS 管理器中打开管理服务。 在 IIS 管理器中，通过在“操作”窗格中打开“绑定”链接来启用 SSL。 此时会显示“添加站点绑定”  对话框。 选择“添加”，并在“类型”下拉列表中选择“HTTPS”。 在 SSL 证书列表中，选择已上传到 Azure 经典门户并已由 CA 签名的 SSL 证书。 有关详细信息，请参阅 [Configure Connection Settings for the Management Service](http://go.microsoft.com/fwlink/?LinkId=215824)（配置管理服务的连接设置）。
      
      > [!NOTE]
      > 如果添加了受信任的 SSL 证书，“发布向导”中将不再显示黄色警告三角形。
      > 
      > 

## <a name="include-files-in-the-service-package"></a>在服务包中包含文件
可能需要在服务包中包括特定文件，以便这些文件在为角色创建的虚拟机上可用。 例如，你可能希望将启动脚本使用的 .exe 或 .msi 文件添加到服务包。 或者，可能需要添加 Web 角色或辅助角色项目所需的程序集。 要包括文件，必须将文件添加到 Azure 应用程序的解决方案。

### <a name="to-include-files-in-the-service-package"></a>在服务包中包含文件
1. 要将程序集添加到服务包，请使用以下步骤：
   
   1. 在“解决方案资源管理器”中，打开缺少已引用程序集的项目的项目节点。
   2. 若要将程序集添加到项目，请打开“引用”文件夹的快捷菜单，然后选择“添加引用”。 此时会显示“添加引用”对话框。
   3. 选择要添加的引用，并选择“确定”按钮。
      
      该引用会添加到“引用”  文件夹下的列表中。
   4. 打开所添加的程序集的快捷菜单，并选择“属性”。 此时会显示“属性”  窗口。
      
      若要将此程序集包含在服务包中，请在“本地复制”列表中选择“True”。
2. 在“解决方案资源管理器”中，打开缺少已引用程序集的项目的项目节点。
3. 若要将程序集添加到项目，请打开“引用”文件夹的快捷菜单，然后选择“添加引用”。 此时会显示“添加引用”  对话框。
4. 选择要添加的引用，并选择“确定”按钮。
   
    该引用会添加到“引用”  文件夹下的列表中。
5. 打开所添加的程序集的快捷菜单，并选择“属性”。 此时会显示“属性”窗口。
6. 若要将此程序集包含在服务包中，请在“本地复制”列表中选择“True”。
7. 要将文件包括在已添加到 Web 角色项目的服务包中，请打开文件的快捷菜单，然后选择“属性”。 从“属性”窗口中的“生成”操作列表框内选择“内容”。
8. 要将文件包括在已添加到辅助角色项目的服务包中，请打开文件的快捷菜单，然后选择“属性”。 从“属性”窗口中的“复制到输出目录”列表框内选择“如果较新则复制”。

## <a name="next-steps"></a>后续步骤
若要详细了解如何从 Visual Studio 发布到 Azure，请参阅 [Publish Azure Application Wizard](vs-azure-tools-publish-azure-application-wizard.md)（发布 Azure 应用程序向导）。

