---
title: "在 Visual Studio 中安装 Emulator Express 调试云服务应用程序 | Microsoft Docs"
description: "介绍如何安装 C++ Redistributable，在 Visual Studio 中启用 Emulator Express"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>使用 Emulator Express 在 VS 2017 中调试云服务应用程序
本文介绍了如何使用 Emulator Express 在 VS 2017 中调试云服务应用程序。

## <a name="background-context"></a>后台上下文
Emulator Express 默认用于在 Visual Studio 中调试云服务 Web 和辅助角色。 在云服务项目属性页中指定此设置。

![打开项目属性][0]

![默认选择 Emulator Express][1]

Emulator Express 需要适用于 Visual Studio 的 [安装 C++ Redistributable][安装 C++ Redistributable]。 当前未通过 Azure 工作负荷进行安装。 使用 F5 手势调试云服务应用程序时，Visual Studio 会提示安装此组件，并继续调试。

![提示安装 C++ Redistributable][2]

单击“是”安装 C++ Redistributable。

![安装 C++ Redistributable][3]

再次按 F5 将启动调试会话。

![开始调试][4]

![调试成功][5]

> 注意：安装 Visual C++ Redistributable 是一次性的工作。 如果已从较旧版本的 Azure SDK 升级，并已安装 Emulator Express，则不会遇到此问题。
> 
> 

## <a name="manual-workaround"></a>手动解决方法
还可以手动安装 [安装 C++ Redistributable][安装 C++ Redistributable]，其效果与 Visual Studio 在系统上安装它的效果相同。

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>后续步骤
了解有关使用 Azure 计算模拟器在 Visual Studio 中调试云服务应用程序的详细信息：[使用 Emulator Express 在本地计算机上运行和调试云服务][使用 Emulator Express 在本地计算机上运行和调试云服务]

[安装 C++ Redistributable]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[使用 Emulator Express 在本地计算机上运行和调试云服务]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


