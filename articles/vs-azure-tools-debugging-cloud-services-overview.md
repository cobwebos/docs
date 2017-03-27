---
title: "调试 Azure 云服务 | Microsoft Docs"
description: "调试 Azure 云服务"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f10ede0a6c30c5a8ae593a5dba0f3db77134d50b


---
# <a name="debugging-cloud-services"></a>调试云服务
可以使用 Azure Tools for Microsoft Visual Studio 和 Azure SDK，以不同的方法调试 Azure 应用程序：

* 在开发 Azure 应用程序时，可以从 Visual Studio 对其进行调试，就像调试任何 Visual C# 或 Visual Basic 应用程序一样。 有关详细信息，请参阅 [Debug your cloud service on your local computer](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)（在本地计算机上调试云服务）。
* 可使用 Azure 诊断以记录在角色内运行的代码的详细信息，角色是否在开发环境或 Azure 中运行。 有关详细信息，请参阅[使用 Azure 诊断收集日志记录数据](http://go.microsoft.com/fwlink/p/?LinkId=400450)。
* 如果使用 Visual Studio Enterprise 来编写以 .NET Framework 4 或.NET Framework 4.5 为目标的角色，则从 Visual Studio 部署 Azure 云服务时，可以启用 IntelliTrace。 IntelliTrace 提供一个日志，可将该日志与 Visual Studio 一起使用以调试应用程序，就如同应用程序在 Azure 中运行一样。 有关详细信息，请参阅 [Debugging a published cloud service with IntelliTrace and Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016)（使用 IntelliTrace 和 Visual Studio 调试已发布的云服务）。
* 从 Visual Studio 部署云服务时，可以在云服务上启用远程调试。 如果选择为部署的项目启用远程调试，将在运行每个角色实例的虚拟机上安装远程调试服务。 这些服务（如 msvsmon.exe）不影响性能，也不额外收费。 有关详细信息，请参阅 [Debug a cloud service in Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)（调试 Azure 中的云服务）。




<!--HONumber=Nov16_HO3-->


