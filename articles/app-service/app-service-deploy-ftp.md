---
title: 使用 FTP/S 将应用部署到 Azure 应用服务| Microsoft Docs
description: 了解如何使用 FTP 或 FTPS 将应用部署到 Azure 应用服务。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariac
ms.openlocfilehash: 66d375022d200cc916c77c059fa64eb6dbbc17e2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308140"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>使用 FTP/S 将应用部署到 Azure 应用服务

本文演示如何使用 FTP 或 FTPS 将 Web 应用、移动应用后端或 API 应用部署到 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)。

应用的 FTP / S 终结点已处于活动状态。 无需配置即可启用 FTP / S 部署。

## <a name="open-ftp-dashboard"></a>打开 FTP 仪表板

在 [Azure 门户](https://portal.azure.com)中，打开应用的[资源页](../azure-resource-manager/resource-group-portal.md#manage-resources)。

若要打开 FTP 仪表板，请单击“持续交付(预览版)” > “FTP” > “仪表板”。

![打开 FTP 仪表板](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>获取 FTP 连接信息

在 FTP 仪表板中，单击“复制”以复制 FTPS 终结点和应用凭据。

![复制 FTP 信息](./media/app-service-deploy-ftp/ftp-dashboard.png)

建议你使用**应用凭据**部署到应用，因为它对每个应用都是唯一的。 但是，如果单击“用户凭据”，会将可用于 FTP/S 登录的用户级凭据设置到订阅中的所有应用服务应用。

## <a name="deploy-files-to-azure"></a>将文件部署到 Azure

1. 从 FTP 客户端（例如 [Visual Studio](https://www.visualstudio.com/vs/community/) 或 [FileZilla](https://filezilla-project.org/download.php?type=client)），使用收集到的连接信息连接到应用。
3. 将文件及其各自的目录结构复制到 Azure 中的 [**/site/wwwroot** 目录](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)（对于 Web 作业，复制到 **/site/wwwroot/App_Data/Jobs/** 目录）。
4. 浏览到应用的 URL，以验证该应用是否正在正常运行。 

> [!NOTE] 
> 与[基于Git的部署](app-service-deploy-local-git.md)不同，FTP 部署不支持以下部署自动化： 
>
> - 还原依赖项（如 NuGet、NPM、PIP 和 Composer 自动化）
> - 编译 .NET 二进制文件
> - 生成 web.config（此处有一个 [Node.js 示例](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)）
> 
> 在本地计算机上手动生成这些必要的文件，并将它们与应用一起部署。
>
>

## <a name="enforce-ftps"></a>强制实施 FTPS

为了增强安全性，只应启用基于 SSL 的 FTP。 如果不使用 FTP 部署，也可禁用 FTP 和 FTPS。

如果应用的资源页位于 [Azure 门户](https://portal.azure.com)中，请在左侧导航中选择“应用设置”。

若要禁用未加密的 FTP，请选择“仅 FTPS”。 若要将 FTP 和 FTPS 都禁用，请选择“禁用”。 完成后，单击“保存”。 如果使用“仅 FTPS”，则必须通过导航到 Web 应用的“SSL 设置”边栏选项卡来强制实施 TLS 1.1 或更高版本。 TLS 1.0 不支持“仅 FTPS”。

![禁用 FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>使用脚本自动化

若要使用 [Azure CLI](/cli/azure) 进行 FTP 部署，请参阅[创建 Web 应用并使用 FTP (Azure CLI) 部署文件](./scripts/app-service-cli-deploy-ftp.md)。

若要使用 [Azure PowerShell](/cli/azure) 进行 FTP 部署，请参阅[使用 FTP (PowerShell) 将文件上传到 Web 应用](./scripts/app-service-powershell-deploy-ftp.md)。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>排查 FTP 部署问题

- [如何排查 FTP 部署问题？](#how-can-i-troubleshoot-ftp-deployment)
- [我无法通过 FTP 来发布代码。如何解决此问题？](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [如何在 Azure 应用服务中通过被动模式连接到 FTP？](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>如何排查 FTP 部署问题？

若要排查 FTP 部署问题，第一步是厘清部署问题和运行时应用程序问题。

部署问题通常会导致无文件部署到应用，或者部署到应用的文件错误。 可以通过调查 FTP 部署情况，或者选择备用部署路径（例如源代码管理）来进行故障排除。

出现运行时应用程序问题时，通常部署到应用的文件集是正确的，但应用行为不正确。 可以通过重点查看运行时的代码行为，并调查具体的故障路径来进行故障排除。

若要确定问题是部署问题还是运行时问题，请参阅 [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)（部署问题和运行时问题）。

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>我无法通过 FTP 来发布代码。 如何解决此问题？
检查是否输入了正确的主机名和[凭据](#step-1--set-deployment-credentials)。 另请检查计算机上的以下 FTP 端口是否未被防火墙阻止：

- FTP 控制连接端口：21
- FTP 数据连接端口：989、10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>如何在 Azure 应用服务中通过被动模式连接到 FTP？
Azure 应用服务支持通过“主动”模式和“被动”模式进行连接。 首选“被动”模式，因为部署计算机通常位于防火墙后面（不管是在操作系统中，还是在家庭网络或企业网络中）。 请参阅 [WinSCP 文档中的示例](https://winscp.net/docs/ui_login_connection)。 

## <a name="next-steps"></a>后续步骤

有关更高级的部署方案，请参阅[使用 Git 部署到 Azure](app-service-deploy-local-git.md)。 使用 Git 部署到 Azure 支持版本控制、包还原、MSBuild 等。

## <a name="more-resources"></a>更多资源

* [Azure 应用服务部署凭据](app-service-deploy-ftp.md)
