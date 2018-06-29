---
title: 在 Azure 开发测试实验室中将 Git 存储库添加到实验室 | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中添加自定义项目源的 GitHub 或 Visual Studio Team Services Git 存储库。
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 80724a7d8d2b5cec19bdbce27cdafd4a9c09eb47
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781866"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>添加 Git 存储库以存储自定义项目和资源管理器模板

可在实验室中为 VM [创建自定义项目](devtest-lab-artifact-author.md)，或[使用 Azure 资源管理器模板创建自定义测试环境](devtest-lab-create-environment-from-arm.md)。 必须为团队创建的项目或资源管理器模板添加专用 Git 存储库。 存储库可以托管在 [GitHub](https://github.com) 或 [Visual Studio Team Services](https://visualstudio.com) 上。

我们提供[项目的 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)，可按原样部署，或针对实验室进行自定义。 自定义或创建项目时，不能将项目存储在公共存储库中。 必须为自定义项目和所创建的项目创建专用存储库。 

创建 VM 时，可以保存资源管理器模板，根据需要对其进行自定义，并在以后使用它创建更多 VM。 必须创建自己的专用存储库来存储自定义资源管理器模板。  

* 若要了解如何创建 GitHub 存储库，请参见 [GitHub Bootcamp ](https://help.github.com/categories/bootcamp/)。
* 要了解如何创建含 Git 存储库的 Team Services 项目，请参阅[连接到 Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

下图是一个示例，展示了包含项目的存储库在 GitHub 中的显示方式：  

![示例 GitHub 项目存储库](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>获取存储库信息和凭据
要将存储库添加到实验室，必须先从存储库获取某些信息。 以下部分介绍如何获取在 GitHub 和 Visual Studio Team Services 上托管的存储库的相关必要信息。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>获取 GitHub 存储库克隆 URL 和个人访问令牌

1. 转到包含项目定义或资源管理器模板定义的 GitHub 存储库主页。
2. 选择“克隆或下载”。
3. 要将 URL 复制到剪贴板，请选择“HTTPS 克隆 URL”按钮。 保存该 URL 以供将来使用。
4. 选择 GitHub 右上角的配置文件图像，然后选择“设置”。
5. 在左侧的“个人设置”菜单中，选择“个人访问令牌”。
6. 选择“生成新令牌”。
7. 在“新建个人访问令牌”页上，在“令牌描述”下输入相关描述。 接受“选择范围”下的默认项，然后选择“生成令牌”。
8. 保存生成的令牌。 稍后会用到该令牌。
9. 关闭 GitHub。   
10. 继续转到[将实验室连接到存储库](#connect-your-lab-to-the-repository)部分。

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>获取 Visual Studio Team Services 存储库克隆 URL 和个人访问令牌

1. 转到团队集合主页（例如 https://contoso-web-team.visualstudio.com) ） ，并选择项目。
2. 在项目主页上，选择“代码”。
3. 若要查看克隆 URL，在项目“代码”页上，选择“克隆”。
4. 保存 URL。 稍后会用到该 URL。
5. 若要创建个人访问令牌，在用户帐户下拉列表菜单中选择“我的个人资料”。
6. 在个人资料信息页中，选择“安全性”。
7. 在“安全性”选项卡上，选择“添加”。
8. 在“创建个人访问令牌”页中：
   1. 输入令牌的**说明**。
   2. 选择“有效期”列表中的“180 天”。
   3. 选择“帐户”列表中的“所有可访问的帐户”。
   4. 选择“所有范围”选项。
   5. 选择“创建令牌”。
9. 随后“个人访问令牌”列表中会出现新的令牌。 选择“复制令牌”，并保存令牌值以备后用。
10. 继续转到[将实验室连接到存储库](#connect-your-lab-to-the-repository)部分。

## <a name="connect-your-lab-to-the-repository"></a>将实验室连接到存储库
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，并从服务列表中选择“开发测试实验室”。
3. 在实验室列表中，选择实验室。 
4. 依次选择“配置和策略” > “存储库” > “+ 添加”。

    ![“添加存储库”按钮](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 在第二个“存储库”页上，指定以下信息：
  1. **名称**。 输入存储库的名称。
  2. **Git 克隆 URL**。 输入之前从 GitHub 或 Visual Studio Team Services 复制的 Git HTTPS 克隆 URL。
  3. **分支**。 输入分支以获取定义。
  4. **个人访问令牌**。 输入之前从 GitHub 或 Visual Studio Team Services 获取的个人访问令牌。
  5. **文件夹路径**。 输入至少一个与包含项目定义或资源管理器模板定义的克隆 URL 有关的文件夹路径。 指定子目录时，请确保在文件夹路径中包含正斜杠。

     ![“存储库”区域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. 选择“保存”。

### <a name="related-blog-posts"></a>相关的博客文章
* [对开发测试实验室中的失败项目进行故障排除](devtest-lab-troubleshoot-artifact-failure.md)
* [使用开发测试实验室中的资源管理器模板将 VM 加入到现有 Active Directory 域中](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
创建专用的 Git 存储库后，可以根据需要执行以下一项或两项操作：
* 存储[自定义项目](devtest-lab-artifact-author.md)。 稍后可用其创建新 VM。
* [使用资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 然后，可将模板存储在专用存储库中。

创建 VM 时，可以验证各个项目或模板是否已添加到 Git 存储库。 它们将立即出现在项目或模板列表中。 专用存储库的名称显示在用于指定源的列中。 
