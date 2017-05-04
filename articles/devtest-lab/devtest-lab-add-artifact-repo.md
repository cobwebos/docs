---
title: "在 Azure 开发测试实验室中将 Git 项目添加到实验室 | Microsoft Docs"
description: "在 Azure 开发测试实验室中添加自定义项目源的 GitHub 或 Visual Studio Team Services Git 存储库"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 55b8b97a8cabedf86e2b92d9490be74c72a5fb09
ms.lasthandoff: 04/27/2017


---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates-for-use-in-azure-devtest-labs"></a>添加 Git 存储库来存储自定义项目和 Azure Resource Manager 模板以供在 Azure 开发测试实验室中使用

如果要在实验室中为 VM [创建自定义项目](devtest-lab-artifact-author.md)，或[使用 Azure Resource Manager 模板创建自定义测试环境](devtest-lab-create-environment-from-arm.md)，还必须添加专用 Git 存储库以将团队创建的项目或 Azure Resource Manager 模板包含在内。 存储库可以托管在 [GitHub](https://github.com) 或 [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上。

我们提供了[项目的 Github 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)，可按原样部署，或针对实验室进行自定义。 自定义或创建某个项目时，该项目无法存储在公共存储库中，必须创建自己的专用存储库。 

创建 VM 时，可以保存 Azure Resource Manager 模板，并根据需要对其进行自定义，然后在以后使用它轻松创建更多 VM。 必须创建自己的专用存储库来存储自定义 Azure Resource Manager 模板。  

* 若要了解如何创建 GitHub 存储库，请参见 [GitHub Bootcamp ](https://help.github.com/categories/bootcamp/)。
* ruo 要了解如何使用 Git 存储库创建Team Services 项目，请参阅[连接到 Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

以下屏幕截图显示了包含项目的存储库在 GitHub 中的显示方式示例：  
![示例 GitHub 项目存储库](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>获取存储库信息和凭据
若要将存储库添加到实验室，必须先从存储库获取某些信息。 以下部分将指导获取在 GitHub 和 Visual Studio Team Services 上托管的存储库的某些信息。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>获取 GitHub 存储库克隆 URL 和个人访问令牌
若要获取 GitHub 存储库克隆 URL 和个人访问令牌，请按照下列步骤操作：

1. 浏览到包含项目定义或 Azure Resource Manager 模板定义的 GitHub 存储库主页。
2. 选择“克隆或下载”。
3. 选择按钮将“HTTPS 克隆 URL”复制到剪贴板，并保存 URL 以备后用。
4. 选择 GitHub 右上角的配置文件图像，然后选择“设置”。
5. 在左侧的“个人设置”菜单中，选择“个人访问令牌”。
6. 选择“生成新令牌”。
7. 在“新的个人访问令牌”页上，输入“令牌说明”，并且接受“选择范围”默认项目，然后选择“生成令牌”。
8. 保存生成的令牌，以备之后使用。
9. 现在可以关闭 GitHub。   
10. 继续转到[将实验室连接到存储库](#connect-your-lab-to-the-repository)部分。

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>获取 Visual Studio Team Services 存储库克隆 URL 和个人访问令牌
若要获取 Visual Studio Team Services 存储库克隆 URL 和个人访问令牌，请按照下列步骤操作：

1. 打开团队集合主页（例如 `https://contoso-web-team.visualstudio.com`），然后选择项目。
2. 在项目主页上，选择“代码”。
3. 若要查看克隆 URL，在项目“代码”页上，选择“克隆”。
4. 保存 URL，以备之后使用。
5. 若要创建个人访问令牌，从用户帐户下拉列表菜单中选择“我的个人资料”。
6. 在个人资料信息页中，选择“安全性”。
7. 在“安全性”选项卡上，选择“添加”。
8. 在“创建个人访问令牌”页中：

   * 输入令牌的**说明**。
   * 从“有效期”列表选择“180 天”。
   * 从“帐户”列表选择“所有可访问的帐户”。
   * 选择“所有范围”选项。
   * 选择“创建令牌”。
9. 完成后，新的标记将出现在“个人访问令牌”列表中。 选择“复制令牌”，然后保存令牌值以备后用。
10. 继续转到[将实验室连接到存储库](#connect-your-lab-to-the-repository)部分。

## <a name="connect-your-lab-to-the-repository"></a>将实验室连接到存储库
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择所需的实验室。   
4. 在实验室的边栏选项卡中，选择“配置和策略”。
5. 在实验室的“配置和策略”边栏选项卡上，选择“存储库”。
6. 在“存储库”边栏选项卡上，选择“+ 添加”。

    ![添加“存储库”按钮](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. 在第二个“存储库”边栏选项卡上，指定以下内容：

   * **名称** - 输入存储库的名称.
   * **Git 克隆 URL** - 输入之前从 GitHub 或 Visual Studio Team Services 复制的 Git HTTPS 克隆 URL。
   * **分支** - 输入分支以获取定义。
   * **个人访问令牌** - 输入之前从 GitHub 或 Visual Studio Team Services 获取的个人访问令牌。
   * **文件夹路径** - 输入至少一个与包含项目定义或 Azure Resource Manager 模板定义的克隆 URL 有关的文件夹路径。 指定子目录时，请确保在文件夹路径中包含正斜杠。

     ![存储库边栏选项卡](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. 选择“保存”。

## <a name="next-steps"></a>后续步骤
创建专用的 Git 存储库后，可以根据需要执行以下一项或两项操作：
* 存储[自定义项目](devtest-lab-artifact-author.md)，可在以后使用它创建新 VM。
* [使用 Azure Resource Manager 模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)，然后将模板存储在专用存储库中。

创建 VM 时，可以验证各个项目或模板是否已添加到 Git 存储库。 它们将立即出现在项目或模板列表中，并且专用存储库的名称显示在指定源的列中。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>相关的博客文章
* [如何对 AzureDevTestLabs 中的失败项目进行故障排除](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [使用 Azure 开发人员测试实验室中的 ARM 模板将 VM 加入到现有的 AD 域](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

