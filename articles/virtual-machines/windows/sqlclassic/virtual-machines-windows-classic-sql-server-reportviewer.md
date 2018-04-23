---
title: 在网站中使用 ReportViewer | Microsoft Docs
description: 本主题介绍了如何使用 Visual Studio ReportViewer 控件生成 Microsoft Azure 网站，该控件用于显示存储在 Microsoft Azure 虚拟机上的报表。
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: af8a4a9c25005925bed3ddb78ced618e669f7f09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>在 Azure 中托管的网站中使用 ReportViewer
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

可以使用 Visual Studio ReportViewer 控件构建 Microsoft Azure 网站，该控件用于显示存储在 Microsoft Azure 虚拟机上的报表。 ReportViewer 控件位于使用 ASP.NET Web 应用程序模板生成的 Web 应用程序中。

> [!IMPORTANT]
> ASP.NET MVC Web 应用程序模板不支持 ReportViewer 控件。

要将 ReportViewer 整合到 Microsoft Azure 网站，需要完成以下任务。

* 将程序集**添加**到部署包
* **配置**身份验证和授权
* 将 ASP.NET Web 应用程序**发布**到 Azure

## <a name="prerequisites"></a>先决条件
查看 [Azure 虚拟机中的 SQL Server Business Intelligence](../classic/ps-sql-bi.md) 中的“常规建议和最佳实践”部分。

> [!NOTE]
> ReportViewer 控件随 Visual Studio Standard Edition 或更高版本提供。 如果使用的是 Web Developer Express Edition，则必须安装 [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) 才能使用 ReportViewer 运行时功能。
> 
> 在 Microsoft Azure 中不支持在本地处理模式下配置的 ReportViewer。

## <a name="adding-assemblies-to-the-deployment-package"></a>将程序集添加到部署包
在本地托管 ASP.NET 应用程序时，在 Visual Studio 安装过程中 ReportViewer 程序集通常直接安装在 IIS 服务器的全局程序集缓存 (GAC) 中，可以由应用程序直接访问。 但是，当在云中托管 ASP.NET 应用程序时，Microsoft Azure 不允许将任何内容安装到 GAC 中，因此，必须确保 ReportViewer 程序集在本地可供应用程序使用。 可以通过在项目中添加它们的引用并将它们配置为以本地方式复制来实现此操作。

在远程处理模式下，ReportViewer 控件使用以下程序集：

* **Microsoft.ReportViewer.WebForms.dll**：包含 ReportViewer 代码，需要在页面中使用 ReportViewer。 将 ReportViewer 控件拖到项目中的一个 ASP.NET 页时，此程序集的引用会被添加到该项目中。
* **Microsoft.ReportViewer.Common.dll**：包含 ReportViewer 控件在运行时使用的类。 它不会自动添加到项目。

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>添加对 Microsoft.ReportViewer.Common 的引用
* 右键单击项目的“引用”节点，然后选择“添加引用”、在 .NET 选项卡中选择程序集并单击“确定”。

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>使程序集可由 ASP.NET 应用程序从本地访问
1. 在 **References** 文件夹中，单击 Microsoft.ReportViewer.Common 程序集，使其属性显示在“属性”窗格中。
2. 在“属性”窗格中，将“复制本地”设置为 True。
3. 为 Microsoft.ReportViewer.WebForms 重复步骤 1 和 2。

### <a name="to-get-reportviewer-language-pack"></a>获取 ReportViewer 语言包
1. 安装 [Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=317386)中的适当 Microsoft Report Viewer 2012 Runtime 可再发行组件包。
2. 从下拉列表中选择语言，页面将重定向到相应的下载中心页面。
3. 单击“下载”，开始下载 ReportViewerLP.exe。
4. 下载 ReportViewerLP.exe 后，单击“运行”立即安装，或单击“保存”将其保存到计算机。 如果单击“保存”，请记住保存该文件所在的文件夹的名称。
5. 找到保存该文件的文件夹。 右键单击 ReportViewerLP.exe、单击“以管理员身份运行”，并单击“是”。
6. 在运行 ReportViewerLP.exe 之后，会看到 c:\windows\assembly 具有资源文件 **Microsoft.ReportViewer.Webforms.Resources** 和 **Microsoft.ReportViewer.Common.Resources**。

### <a name="to-configure-for-localized-reportviewer-control"></a>为本地化 ReportViewer 控件进行配置
1. 按照上面的指定说明下载并安装 Microsoft Report Viewer 2012 Runtime 可再发行组件包。
2. 在项目中创建 <language> 文件夹并将关联的资源程序集文件复制到该位置。 要复制的资源程序集文件为：**Microsoft.ReportViewer.Webforms.Resources.dll** 和 **Microsoft.ReportViewer.Common.Resources.dll**。选择资源程序集文件，并在“属性”窗格中将“复制到输出目录”设置为“始终复制”。
3. 为 Web 项目设置“区域性和 UI区域性”。 有关如何为 ASP.NET 网页设置“区域性和 UI 区域性”的详细信息，请参阅[如何：为 ASP.NET 网页全球化设置区域性和 UI 区域性](http://go.microsoft.com/fwlink/?LinkId=237461)。

## <a name="configuring-authentication-and-authorization"></a>配置身份验证和授权
ReportViewer 需要使用正确的凭据向报表服务器进行身份验证，并且凭据必须经报表服务器授权才能访问所需的报表。 有关身份验证的信息，请查看白皮书 [Reporting Services 报表查看器控件和基于 Microsoft Azure 虚拟机的报表服务器](https://msdn.microsoft.com/library/azure/dn753698.aspx)。

## <a name="publish-the-aspnet-web-application-to-azure"></a>发布 ASP.NET Web 应用程序到 Azure
有关将 ASP.NET Web 应用程序发布到 Azure 的说明，请参阅[如何：从 Visual Studio 将 Web 应用程序迁移和发布到 Azure](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) 和 [Web 应用和 ASP.NET 入门](../../../app-service/app-service-web-get-started-dotnet.md)。

> [!IMPORTANT]
> 如果在解决方案资源管理器中的快捷菜单中未显示添加Azure 部署项目或添加 Azure 云服务项目命令，可能需要将该项目的目标框架更改为 .NET Framework 4。
> 
> 两个命令提供基本相同的功能。 其中一个命令会显示在快捷菜单中，这取决于已安装的 Microsoft Azure SDK 版本。
> 
> 

## <a name="resources"></a>资源
[Microsoft 报表](http://go.microsoft.com/fwlink/?LinkId=205399)

[Azure 虚拟机中的 SQL Server Business Intelligence](../classic/ps-sql-bi.md)

[使用 PowerShell 创建运行本机模式报表服务器的 Azure VM](../classic/ps-sql-report.md)

