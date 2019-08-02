---
title: 为 IoT 代理选择并部署 Azure 安全中心 |Microsoft Docs
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
ms.openlocfilehash: ffc6ea447ae90649be0455abbed6245c078e518d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596342"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 设备上选择并部署安全代理

用于 IoT 的 Azure 安全中心为监视和收集 IoT 设备中数据的安全代理提供参考体系结构。
若要了解详细信息, 请参阅[安全代理参考体系结构](security-agent-architecture.md)。

代理作为开放源代码项目开发, 提供两种风格: <br> [C](https://aka.ms/iot-security-github-c)和[C#](https://aka.ms/iot-security-github-cs)。

在本文中，学习如何： 
> [!div class="checklist"]
> * 比较安全代理风格
> * 发现支持的代理平台
> * 为解决方案选择正确的代理风格

## <a name="understand-security-agent-options"></a>了解安全代理选项

每个 Azure 安全中心的 IoT 安全代理风格提供一组相同的功能, 并支持类似的配置选项。 

基于 C 的安全代理具有更低的内存占用量, 并且对于具有较少可用资源的设备是理想选择。 

|     | 基于 C 的安全代理 | C#基于的安全代理 |
| --- | ----------- | --------- |
| 开源 | [GitHub](https://aka.ms/iot-security-github-cs)中的[MIT 许可证](https://en.wikipedia.org/wiki/MIT_License)下提供 | [GitHub](https://aka.ms/iot-security-github-c)中的[MIT 许可证](https://en.wikipedia.org/wiki/MIT_License)下提供 |
| 开发语言    | C | C# |
| 支持的 Windows 平台？ | 否 | 是 |
| Windows 先决条件 | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| 支持的 Linux 平台？ | 是、x64 和 x86 | 是, 仅 x64 |
| Linux 先决条件 | libunwind8、libcurl3、uuid-runtime、审核、audispd-插件 | libunwind8, libcurl3, uuid-runtime, 审核, audispd-插件, sudo, netstat, iptables |
| 磁盘占用量 | 10.5 MB | 90 MB |
| 内存占用量 (平均) | 5.5 MB | 33 MB |
| 向 IoT 中心进行[身份验证](concept-security-agent-authentication-methods.md) | 是 | 是 |
| 安全数据[收集](how-to-agent-configuration.md#supported-security-events) | 是 | 是 |
| 事件聚合 | 是 | 是 |
| 通过[安全模块](concept-security-module.md)克隆远程配置 | 是 | 是 |
|

## <a name="security-agent-installation-guidelines"></a>安全代理安装指南

对于 **Windows**：Install SecurityAgent 脚本必须从管理员 PowerShell 窗口执行。 

对于 **Linux**：必须以超级用户身份运行 InstallSecurityAgent.sh。 建议在安装命令前面加上 "sudo"。


## <a name="choose-an-agent-flavor"></a>选择代理风格 

回答以下有关 IoT 设备的问题, 以便选择正确的代理:

- 你使用的是_Windows Server_还是_windows IoT Core_？ 

    [为 Windows C#部署基于的安全代理](how-to-deploy-windows-cs.md)。

- 是否在使用 Linux 分发和 x86 体系结构？ 

    [部署基于 C 的安全代理 (适用于 Linux)](how-to-deploy-linux-c.md)。

- 你是否正在使用带有 x64 体系结构的 Linux 分发版？

    可以使用代理风格之一。 <br>
    [为 Linux 部署基于 C 的安全代理](how-to-deploy-linux-c.md)和/或[部署C#基于 C 的安全代理](how-to-deploy-linux-cs.md)。

两种代理风格都提供了相同的功能集, 并支持类似的配置选项。
有关详细信息, 请参阅[安全代理比较](how-to-deploy-agent.md#understand-security-agent-options)。

## <a name="supported-platforms"></a>受支持的平台

以下列表包含当前支持的所有平台。

|用于 IoT 代理的 Azure 安全中心 |操作系统 |体系结构 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 核心版 17763 |x64|
|

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息, 请继续阅读有关代理配置的操作方法指南。 
> [!div class="nextstepaction"]
> [代理配置操作指南](./how-to-agent-configuration.md)
