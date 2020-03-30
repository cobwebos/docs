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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836805"
---
## <a name="prepare-your-repository"></a>准备存储库

若要从 Azure 应用服务 Kudu 生成服务器获取自动生成，请确保项目中存储库根路径具有正确的文件。

| 运行时 | 根目录文件 |
|-|-|
| ASP.NET（仅限 Windows） | _\*.sln_、 _\*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _\*.sln_ 或 _\*.csproj_ |
| PHP | index.php__ |
| Ruby（仅限 Linux） | Gemfile__ |
| Node.js | server.js、app.js 或具有启动脚本的 package.json______ |
| Python | .py ，_要求.txt_， 或_运行时.txt_ _ \*_ |
| HTML | default.htm、default.html、default.asp、index.htm、index.html 或 iisstart.htm____________ |
| Web 作业 | _\<job_name>/运行。扩展\<>"__应用\_数据/作业/_ 连续 Web 作业"或 _"\_应用数据/作业/_ 为触发 Web 作业触发"的扩展。 有关详细信息，请参阅[库杜 Web作业文档](https://github.com/projectkudu/kudu/wiki/WebJobs)。 |
| 函数 | 请参阅 [Azure Functions 的连续部署](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)。 |

要自定义部署，可以在存储库根路径中添加 .deployment 文件**。 有关详细信息，请参阅[自定义部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 如果在 Visual Studio 中进行开发，让 [Visual Studio 创建存储库](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio)。 该项目可立即通过 Git 进行部署。
>

