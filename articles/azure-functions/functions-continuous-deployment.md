---
title: Azure Functions 的连续部署 | Microsoft Docs
description: 使用 Azure 应用服务连续部署功能来发布 Azure Functions。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: db10cd957f4dc59f787e2ac625355a96c888356e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735697"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署
使用 Azure Functions，可轻松使用应用服务持续集成部署 Function App。 Functions 可与 BitBucket、Dropbox、GitHub 和 Visual Studio Team Services (VSTS) 集成。 这样，通过使用其中一项集成服务实现的函数代码更新所在的工作流将触发到 Azure 的部署。 如果不熟悉 Azure Functions，请从[Azure Functions 概述](functions-overview.md)开始。

连续部署选项非常适合用于频繁集成多个分发内容的项目。 它还允许保持对函数代码的源控件。 目前支持以下部署源：

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* 外部存储库（Git 或 Mercurial）
* [Git 本地存储库](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

在每个 Function App 基础上配置部署。 启用连续部署后，将门户中的函数代码访问设置为*只读*。

## <a name="continuous-deployment-requirements"></a>连续部署要求

在设置连续部署之前，必须具备配置的部署源和其中的函数代码。 在给定的 Function App 部署中，每个函数位于已命名的子目录中，其中目录名即为函数名。  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

为了能够从 VSTS 进行部署，必须先将 VSTS 帐户与 Azure 订阅关联起来。 有关详细信息，请参阅[为 VSTS 帐户设置计费](https://docs.microsoft.com/vsts/billing/set-up-billing-for-your-account-vs?view=vsts#set-up-billing-via-the-azure-portal)。

## <a name="set-up-continuous-deployment"></a>设置连续部署
使用此过程来配置现有 Function App 的连续部署。 这些步骤演示了与 GitHub 存储库的集成，但类似的步骤适用于 Visual Studio Team Services 或其他部署服务。

1. 在 [Azure 门户](https://portal.azure.com) 的 Function App 中，单击“平台功能”和“部署选项”。 
   
    ![设置连续部署](./media/functions-continuous-deployment/setup-deployment.png)
 
2. 然后在“部署”边栏选项卡中单击“设置”。
 
    ![设置连续部署](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. 在“部署源”边栏选项卡中，单击“选择源”，并为所选部署源填写信息，单击“确定”。
   
    ![选择部署源](./media/functions-continuous-deployment/choose-deployment-source.png)

配置连续部署后，部署源中所有的文件更改都会复制到该 Function App，并会触发完整的站点部署。 当源中的文件更新时重新部署该网站。

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
    
5. 更新临时分支或文件夹中的代码，并验证这些更改是否反映在过渡部署中。

6. 测试完成后，将更改从临时分支合并到主分支。 此合并会触发生产 Function App 的部署。 如果部署的源不支持分支，请使用临时文件夹中的文件覆盖生产文件夹中的文件。

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>将现有函数移至连续部署
如果门户中具有已创建和维护的现有函数，则在可如上文所述设置连续部署前，需要使用 FTP 或本地 Git 存储库下载现有函数代码文件。 可以在应用服务设置中为函数应用完成以上操作。 文件会下载完后，可以将它们上传到选择的连续部署源中。

> [!NOTE]
> 配置持续集成后，不能再对函数门户中的源文件进行编辑。

- [如何：配置部署凭据](#credentials)
- [如何：使用 FTP 下载文件](#downftp)
- [如何：使用本地 Git 存储库下载文件](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>如何配置部署凭据
在可使用 FTP 或本地 Git 存储库从 Function App 下载文件之前，必须配置凭据以访问站点。 在函数应用级别上设置凭据。 执行以下步骤，在 Azure 门户中设置部署凭据：

1. 在 [Azure 门户](https://portal.azure.com) 的 Function App 中，单击“平台功能”和“部署凭据”。
   
    ![设置本地部署凭据](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. 键入用户名和密码，并单击“保存”。 现在可以使用这些凭据来访问 FTP 或内置 Git 存储库内的函数应用。

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>使用 FTP 下载文件的方法

1. 在 [Azure 门户](https://portal.azure.com)的 Function App 中，单击“平台功能”和“属性”，并复制 FTP/部署用户的值、FTP 主机名和 FTPS 主机名。  

    根据门户显示，必须输入“FTP/部署用户”（包括应用名称），以便为 FTP 服务器提供适当的上下文。
   
    ![获取部署信息](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. 从 FTP 客户端，使用收集到的连接信息连接到应用，下载函数的源文件。

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>如何：使用本地 Git 存储库下载文件

1. 在 [Azure 门户](https://portal.azure.com) 的 Function App 中，单击“平台功能”和“部署选项”。 
   
    ![设置连续部署](./media/functions-continuous-deployment/setup-deployment.png)
 
2. 然后在“部署”边栏选项卡中单击“设置”。
 
    ![设置连续部署](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. 在“部署源”边栏选项卡中，单击“本地 Git 存储库”，并单击“确定”。

3. 在“平台功能”中，单击“属性”，并记录 Git URL 值。 
   
    ![设置连续部署](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. 使用 Git 感知的命令提示符或最喜欢的 Git 工具克隆本地计算机上的存储库。 Git 克隆命令如下所示：
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. 将文件从函数应用提取到本地计算机上的克隆，如以下示例所示：
   
        git pull origin master
   
    如果已请求，请提供[已配置的部署凭据](#credentials)。  

[GitHub]: https://github.com/

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 最佳实践](functions-best-practices.md)
