---
title: 选择和部署 IoT 代理预览版 ASC |Microsoft Docs
description: 了解有关如何选择并将 ASC 部署为 IoT 设备上的 IoT 安全代理。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541957"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>选择并部署 IoT 设备上的安全代理

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


ASC 为 IoT 提供参考体系结构的安全代理，监视和从 IoT 设备收集数据。

安全代理作为开放源代码项目开发的有两种类型： <br> [C](https://aka.ms/iot-security-github-c)，并[ C# ](https://aka.ms/iot-security-github-cs)。

## <a name="supported-platforms-for-asc-for-iot-agents"></a>支持的平台为 ASC 的 IoT 代理

以下列表包含所有当前支持的平台。  

|ASC IoT 代理 |操作系统 |体系结构 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64、 x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 核心版 17763 |x64|


## <a name="which-flavor-should-i-use"></a>应使用哪种版本？

请考虑以下问题方面 IoT 设备：

- 您是否正在使用_Windows Server_或_Windows IoT Core_？ 

    使用[ASC IoT 安装与 Windows 的C#-基于安全代理](tutorial-deploy-windows-cs.md)。

- 您使用的是 Linux 版本具有 x86 体系结构？ 

    使用[ASC 为与基于 C 的安全代理适用于 Linux 的 IoT 安装](tutorial-deploy-linux-c.md)。
- 您使用的 Linux 分发带有 x64 体系结构？

    可以使用这两种风格。 <br>
    [与基于 C 的安全代理适用于 Linux 的 IoT 安装 ASC](tutorial-deploy-linux-c.md)和/或[ASC IoT 安装使用 Linux 的C#-基于安全代理](tutorial-deploy-linux-cs.md)。

这两种类型具有一组相同的功能，并支持类似的配置选项。 基于 C 的安全代理具有较低的内存占用量。


## <a name="next-steps"></a>后续步骤
- [概述](overview.md)
- [安全代理](security-agent-architecture.md)
- [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- [ASC iot 常见问题](resources-frequently-asked-questions.md)