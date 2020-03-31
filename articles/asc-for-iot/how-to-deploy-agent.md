---
title: 为 IoT 代理选择和部署 Azure 安全中心*微软文档
description: 了解如何在 IoT 设备上为 IoT 安全代理选择和部署 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d70f2f3ec87c8673013bcf7b6f70ebcbb8d06f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770010"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 设备上选择并部署安全代理

IoT Azure 安全中心为监视和从 IoT 设备收集数据的安全代理提供了参考体系结构。
要了解更多信息，请参阅[安全代理参考体系结构](security-agent-architecture.md)。

代理开发为开源项目，有两种类型： <br> [C](https://aka.ms/iot-security-github-c)和[C#](https://aka.ms/iot-security-github-cs)。

在本文中，学习如何： 
> [!div class="checklist"]
> * 比较安全代理风格
> * 发现支持的代理平台
> * 为您的解决方案选择合适的代理风格

## <a name="understand-security-agent-options"></a>了解安全代理选项

每个适用于 IoT 安全代理风格的 Azure 安全中心都提供相同的功能集，并支持类似的配置选项。 

基于 C 的安全代理的内存占用量较低，是可用资源较少的设备的理想选择。 

|     | 基于 C 的安全代理 | 基于 C# 的安全代理 |
| --- | ----------- | --------- |
| 开源 | 在[GitHub](https://aka.ms/iot-security-github-cs)中的[MIT 许可证](https://en.wikipedia.org/wiki/MIT_License)下提供 | 在[GitHub](https://aka.ms/iot-security-github-c)中的[MIT 许可证](https://en.wikipedia.org/wiki/MIT_License)下提供 |
| 开发语言    | C | C# |
| 支持 Windows 平台？ | 否 | 是 |
| 窗口先决条件 | --- | [Wmi](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| 支持的 Linux 平台？ | 是，x64 和 x86 | 是，仅限 x64 |
| Linux 系统先决条件 | libunwind8， libcurl3， uuid-运行时， 审核， 奥迪德-插件 | libunwind8， libcurl3， uuid-运行时， 审核， 奥迪斯pd-插件， sudo， netstat， iptables |
| 磁盘占用空间 | 10.5 MB | 90 MB |
| 内存占用量（平均） | 5.5 MB | 33 MB |
| [对](concept-security-agent-authentication-methods.md)IoT 中心的身份验证 | 是 | 是 |
| 安全[数据收集](how-to-agent-configuration.md#supported-security-events) | 是 | 是 |
| 事件聚合 | 是 | 是 |
| 通过[安全模块孪生](concept-security-module.md)远程配置 | 是 | 是 |
|

## <a name="security-agent-installation-guidelines"></a>安全代理安装指南

对于**Windows**：安装安全代理.ps1 脚本必须从管理员 PowerShell 窗口执行。 

对于**Linux：InstallSecurityAgent.sh**必须以超级用户身份运行。 我们建议将安装命令的前缀为"sudo"。


## <a name="choose-an-agent-flavor"></a>选择代理风味 

回答有关 IoT 设备的以下问题，以选择正确的代理：

- 是使用_Windows 服务器_还是_Windows IoT 核心_？ 

    [为 Windows 部署基于 C# 的安全代理](how-to-deploy-windows-cs.md)。

- 您是否使用带有 x86 体系结构的 Linux 发行版？ 

    [为 Linux 部署基于 C 的安全代理](how-to-deploy-linux-c.md)。

- 您是否使用带有 x64 体系结构的 Linux 发行版？

    可以使用两种代理口味。 <br>
    [为 Linux 部署基于 C 的安全代理](how-to-deploy-linux-c.md)和/或[为 Linux 部署基于 C# 的安全代理](how-to-deploy-linux-cs.md)。

两种代理类型都提供相同的功能集，并支持类似的配置选项。
请参阅[安全代理比较](how-to-deploy-agent.md#understand-security-agent-options)以了解更多信息。

## <a name="supported-platforms"></a>支持的平台

以下列表包括当前支持的所有平台。

|IoT 代理的 Azure 安全中心 |操作系统 |体系结构 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64， ARMv7|
|C|Debian 9 |   x64、x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64， ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 核心版 17763    |x64|
|

## <a name="next-steps"></a>后续步骤

要了解有关配置选项的详细信息，请继续执行代理配置操作指南。 
> [!div class="nextstepaction"]
> [如何指导代理配置](./how-to-agent-configuration.md)
