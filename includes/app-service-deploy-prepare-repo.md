---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836805"
---
## <a name="prepare-your-repository"></a>准备存储库

若要从 Azure 应用服务 Kudu 生成服务器自动生成，请确保存储库根路径具有正确的文件在项目中。

| 运行时 | 根目录文件 |
|-|-|
| ASP.NET（仅限 Windows） | _\*.sln_、 _\*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _\*.sln_ 或 _\*.csproj_ |
| PHP | index.php  |
| Ruby（仅限 Linux） | Gemfile  |
| Node.js | server.js、app.js 或具有启动脚本的 package.json    |
| Python | _\*.py_ 、 _requirements.txt_ 或 _runtime.txt_ |
| HTML | default.htm、default.html、default.asp、index.htm、index.html 或 iisstart.htm       |
| Web 作业 | _\<job_name > / u。\<扩展 >_ 下_应用\_数据/作业/continuous_连续 web 作业，或_应用\_数据/作业/triggered_触发Web 作业。 有关详细信息，请参阅 [Kudu WebJobs 文档](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| 函数 | 请参阅 [Azure Functions 的连续部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

要自定义部署，可以在存储库根路径中添加 .deployment 文件  。 有关详细信息，请参阅[自定义部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果在 Visual Studio 中进行开发，让 [Visual Studio 创建存储库](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 该项目可立即通过 Git 进行部署。
>

