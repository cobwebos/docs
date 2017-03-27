---
title: "为 Azure 构建的命令行 | Microsoft Docs"
description: "为 Azure 构建的命令行"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e189bf8241266443bee4dbbb5faf1cffa983bdb4


---
# <a name="command-line-build-for-azure"></a>为 Azure 构建的命令行
## <a name="overview"></a>概述
可以通过在命令提示符下运行 MSBuild 来为 Azure 部署创建包。 除了自动化某些生成过程外，还可以配置和定义用于调试、过渡和生产的生成。

## <a name="microsoft-build-engine-msbuild"></a>Microsoft 生成引擎 (MSBuild)
通过使用 Microsoft 生成引擎 (MSBuild)，可以在未安装 Visual Studio 的生成实验室环境中生成产品。 MSBuild 对可扩展且 Microsoft 完全支持的项目文件使用 XML 格式。 在此文件格式中，可以描述必须为一个或多个平台和配置生成的项目。

还可以在命令提示符下运行 MSBuild，本主题介绍了该方法。 通过在命令提示符下设置属性，可以生成项目的特定配置。 同样，还可以定义 MSBuild 命令将生成的目标。 有关命令行参数和 MSBuild 的详细信息，请参阅 [MSBuild 命令行参考](https://msdn.microsoft.com/library/ms164311.aspx)。

## <a name="installation"></a>安装
如以下过程所述，必须先在生成服务器上安装软件和工具，才能使用 MSBuild 创建 Azure 包：

1. 安装包括 MSBuild 的 .NET Framework 4 或更高版本。
2. 安装 [Azure 创作工具](http://go.microsoft.com/fwlink/?LinkId=394615)（查找 MicrosoftAzureAuthoringTools-x64.msi 或 MicrosoftAzureAuthoringTools-x86.msi）。
3. 安装 [Azure Libraries for .NET](http://go.microsoft.com/fwlink/?LinkId=394616)（查找 MicrosoftAzureLibsForNet-x64.msi 或 MicrosoftAzureLibs-x86.msi）。
4. 从另一台计算机上的 Visual Studio 复制 Microsoft.WebApplication.targets 文件。
   
    该文件位于目录 C:\Program Files (x86)\MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 for Visual Studio 2012) 中，应将其复制到生成服务器上的同一目录。
5. 安装 [Azure Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616)。
   
    查找 WindowsAzureTools.vs120.exe 以生成 Visual Studio 2013 项目。

## <a name="msbuild-parameters"></a>MSBuild 参数
创建包的最简单方法是运行 MSBuild 并使用 `/t:Publish` 选项。 默认情况下，此命令将创建相对于项目根文件夹的一个目录，例如 ProjectDir\bin\Configuration\app.publish\. 当生成 Azure 项目时，将生成两个文件，即包文件本身和伴随的配置文件：

* Project.cspkg
* ServiceConfiguration.TargetProfile.cscfg

默认情况下，每个 Azure 项目均包含两个服务配置文件，这两个文件分别针对本地（调试）生成和云（过渡或生产）生成，可根据需要添加或删除服务配置文件。 在 Visual Studio 中生成包时，系统会询问要与包一起包含哪个服务配置文件。 使用 MSBuild 生成包时，默认情况下将包含本地服务配置文件。 要包含其他配置文件，请设置 MSBuild 命令 (`MSBuild /t:Publish /p:TargetProfile=ProfileName`) 的 `TargetProfile` 属性。

如果希望为存储包和配置文件使用其他目录，请使用 `/p:PublishDir=Directory\` 选项（包括末尾的反斜杠）设置路径。

## <a name="deployment"></a>部署
生成包后，即可将其部署到 Azure。 有关演示该过程的教程，请参阅 Azure 网站。 有关如何使该流程自动执行的信息，请参阅 [Continuous Delivery for Cloud Services in Azure](cloud-services/cloud-services-dotnet-continuous-delivery.md)（在 Azure 中持续提供云服务）。




<!--HONumber=Nov16_HO3-->


