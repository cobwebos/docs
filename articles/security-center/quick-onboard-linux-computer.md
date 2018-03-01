---
title: "Azure 安全中心快速入门 - 将 Linux 计算机载入到安全中心 | Microsoft Docs"
description: "本快速入门展示了如何将 Linux 计算机载入到安全中心。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 05e4bed0f9b4dfb6d1879408085447ef53db8655
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>快速入门：将 Linux 计算机载入到安全中心
载入 Azure 订阅之后，可以通过预配 Linux 代理为在 Azure 外部（例如，在本地或在其他云中）运行的 Linux 资源启用安全中心。

本快速入门展示了如何在 Linux 计算机上安装 Linux 代理。

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

在开始学习本快速入门之前，你必须位于安全中心的“标准”定价层。 有关升级说明，请参阅[将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)。 可以试用安全中心标准版，前 60 天免费。

## <a name="add-new-linux-computer"></a>添加新的 Linux 计算机

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。
2. 在 **Microsoft Azure** 菜单上选择“安全中心”。 此时会打开“安全中心 - 概览”。

 ![安全中心概述][2]

3. 在“安全中心”主菜单下，选择“载入高级安全”。
4. 针对“要添加非 Azure 计算机吗?”做出选择。
   ![载入到高级安全][3]

5. 在“添加新的非 Azure 计算机”上，将显示 Log Analytics 工作区的列表。 该列表包含启用自动预配时由安全中心创建的默认工作区（如果适用）。 选择此工作区或要使用的其他工作区。

    ![添加非 Azure 计算机][4]

6.  在“直接代理”页面上，在“下载和板载 Agent for Linux”下，选择“复制”按钮以复制 *wget* 命令。

7.  打开记事本并粘贴此命令。 将此文件保存到可以从你的 Linux 计算机访问的位置。

## <a name="install-the-agent"></a>安装代理

1.  在你的 Linux 计算机上，打开前面保存的文件。 选择整个内容，进行复制，打开一个终端控制台并粘贴该命令。
2.  在安装完成后，可以通过运行 *pgrep* 命令验证 *omsagent* 是否已安装。 该命令将返回 *omsagent* PID（进程 ID），如下所示：

  ![安装代理][5]

可以在以下位置找到适用于 Linux 的安全中心代理的 日志：*/var/opt/microsoft/omsagent/<workspace id>/log/*

  ![代理的日志][6]

在一段时间后（可能需要多达 30 分钟），新的 Linux 计算机将显示在安全中心内。

现在，可以从单个位置监视 Azure VM 和非 Azure 计算机了。 在“计算”下，可以概览所有 VM 和计算机以及建议。 每一列代表一组建议。 颜色表示 VM 或计算机针对该建议的当前安全状态。 安全中心还会在“安全警报”中显示针对这些计算机的任何检测。

  ![计算边栏选项卡][7] -“计算”边栏选项卡上提供了两种类型的图标：

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 非 Azure 计算机

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>清理资源
如果不再需要使用该代理，可从 Linux 计算机中将其删除。

若要删除该代理，请执行以下操作：

1. 将 Linux 代理[通用脚本](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)下载到计算机。
2. 在计算机上在使用 *--purge* 参数的情况下运行 bundle .sh 文件，这将彻底删除该代理及其配置。

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>后续步骤
在此快速入门中，你已在 Linux 计算机上预配了代理。 若要详细了解如何使用安全中心，请继续阅读相关教程，了解如何配置安全策略和评估资源的安全性。

> [!div class="nextstepaction"]
> [教程：定义和评估安全策略](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
