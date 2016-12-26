---
title: "Azure Functions 的连续部署 | Microsoft Docs"
description: "使用 Azure App Service 连续部署功能来发布 Azure Functions。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 66753c80223e33c3add9d37aa4d81656472391ca
ms.openlocfilehash: e1a869d3c3512f32eab7840f5abc17abd8ce8004


---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署
Azure Functions 让用户轻松地配置函数应用的连续部署。 Functions 使用 Azure 应用服务与 BitBucket、Dropbox、GitHub 和 Visual Studio Team Services (VSTS) 的集成，实现一种连续部署工作流，函数代码的更新发布到其中一种服务时，Azure 可在工作流中拉取这些更新。 如果不熟悉 Azure Functions，请从[Azure Functions 概述](functions-overview.md)开始。

连续部署选项非常适合用于频繁集成多个分发内容的项目。 它还允许保持对函数代码的源控件。 目前支持以下部署源：

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://bitbucket.org/)
* [Git 本地存储库](../app-service-web/app-service-deploy-local-git.md)
* Git 外部存储库
* [GitHub]
* Mercurial 外部存储库
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

在每个函数应用基础上配置部署。 启用连续部署后，将门户中的函数代码访问设置为*只读*。

## <a name="continuous-deployment-requirements"></a>连续部署要求

在设置连续部署之前，必须具备配置的部署源和其中的函数代码。 在给定的 Function App 部署中，每个函数位于已命名的子目录中，其中目录名即为函数名。 此文件夹结构实质上是站点代码。 

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>设置连续部署
使用以下过程来配置现有函数应用程序的连续部署：

1. 在 [Azure Functions 门户](https://functions.azure.com/signin)的函数应用中，单击“Function App 设置” > “配置连续集成” > “设置”。
   
    ![设置连续部署](./media/functions-continuous-deployment/setup-deployment.png)
   
    ![设置连续部署](./media/functions-continuous-deployment/setup-deployment-1.png)
   
    单击“从源代码管理开始”，可从函数快速入门转到部署边栏选项卡。
2. 在“部署源”边栏选项卡中，单击“选择源”，然后为所选部署源填写信息，单击“确定”。
   
    ![选择部署源](./media/functions-continuous-deployment/choose-deployment-source.png)

配置连续部署后，部署源中的所有更改文件都会复制到该函数应用程序，并会触发完整的站点部署。 当源中的文件更新时重新部署该网站。

## <a name="deployment-options"></a>部署选项

以下是一些典型的部署方案：

- [创建过渡部署](#staging)
- [将现有函数移至连续部署](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>创建分阶段部署

Function App 尚不支持部署槽。 但是，仍可以通过使用持续集成管理单独的分阶段部署和生产部署。

配置和使用分阶段部署的过程通常是这样的：

1. 在订阅中，分别为生产代码何分阶段部署创建两个函数应用程序。 

2. 如果还没有部署源，创建一个。 本示例使用 [GitHub]。

3. 对于生产 Function App，在“设置连续部署”中完成上述步骤，并将部署分支设置为 GitHub 存储库的主分支。
   
    ![选择部署分支](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. 针对过渡 Function App 重复此步骤，但在 GitHub 存储库中改为选择过渡分支。 如果部署的源不支持分支，请使用不同的文件夹。

5. 更新临时分支或文件夹中的代码，然后验证这些更改是否反映在过渡部署中。

6. 测试完成后，将更改从临时分支合并到主分支。 这将触发生产函数应用的部署。 如果部署的源不支持分支，请使用临时文件夹中的文件覆盖生产文件夹中的文件。

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>将现有函数移至连续部署
如果门户中具有已创建和维护的现有函数，则在可如上文所述设置连续部署前，需要使用 FTP 或本地 Git 存储库下载现有函数代码文件。 可以在应用程序服务设置为函数应用完成以上操作。 文件会下载完后，可以将它们上载到选择的连续部署源中。

> [!NOTE]
> 配置持续集成后，不能再对函数门户中的源文件进行编辑。

- [如何：配置部署凭据](#credentials)
- [如何：使用 FTP 下载文件](#downftp)
- [如何：使用本地 Git 存储库下载文件](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>如何配置部署凭据
在可使用 FTP 或本地 Git 存储库从 Function App 下载文件之前，必须配置凭据以访问站点（可从门户中执行此操作）。 在函数应用级别上设置凭据。

1. 在 [Azure Functions 门户](https://functions.azure.com/signin)的 Function App 中，单击“Function App 设置” > “转到应用服务设置” > “部署凭据”。
   
    ![设置本地部署凭据](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. 键入用户名和密码，然后单击“保存”。 现在可以使用这些凭据来访问 FTP 或内置 Git 存储库内的函数应用。

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>使用 FTP 下载文件的方法

1. 在 [Azure 函数门户](https://functions.azure.com/signin)的应用函数中，请单击“函数应用设置” > “转到应用程序服务设置” > “属性”，复制 **FTP/部署用户**的值、**FTP 主机名**和 **FTPS 主机名**。  

    根据门户显示，必须输入“FTP/部署用户”（包括应用名称），以便为 FTP 服务器提供适当的上下文。
   
    ![获取部署信息](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. 从 FTP 客户端，使用收集到的连接信息连接到应用，下载函数的源文件。

<a name="downgit"></a>
#### <a name="how-to-download-files-using-the-local-git-repository"></a>如何使用本地 Git 存储库下载文件

1. 在 [Azure Functions 门户](https://functions.azure.com/signin)的函数应用中，单击“Function App 设置” > “配置连续集成” > “设置”。

2. 在“部署”边栏选项卡中，单击“选择源”>“本地 Git 存储库”，然后单击“确定”。

3. 单击“转到应用程序服务设置” > “属性”，记下 Git URL 的值。 
   
    ![设置连续部署](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. 使用 Git 感知的命令行或最喜欢的 Git 工具克隆本地计算机上的存储库。 Git 克隆命令如下所示：
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. 将文件从函数应用提取到本地计算机上的克隆，如以下示例所示：
   
        git pull origin master
   
    如果出现请求，提供函数应用部署的用户名和密码。  

[GitHub]: https://github.com/



<!--HONumber=Nov16_HO4-->


