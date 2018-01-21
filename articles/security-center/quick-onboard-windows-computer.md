---
title: "Azure 安全中心快速入门 - 将 Windows 计算机登记到安全中心 | Microsoft Docs"
description: "本快速入门演示如何在 Windows 计算机上预配 Microsoft Monitoring Agent。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: 50cbbca9181d67bc41632a4650c76b9636a72356
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>快速入门：将 Windows 计算机登记到安全中心
加入 Azure 订阅之后，可以通过预配 Microsoft Monitoring Agent 为 Azure 外部（例如，本地或在其他云中）运行的资源启用安全中心。

本快速入门演示如何在 Windows 计算机上安装 Microsoft Monitoring Agent。

## <a name="prerequisites"></a>系统必备
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

在开始学习本快速入门之前，你必须位于安全中心的“标准”定价层。 有关升级说明，请参阅[将 Azure 订阅加入到安全中心标准版](security-center-get-started.md)。 可以尝试安全中心标准版，头 60 天免费。

## <a name="add-new-windows-computer"></a>添加新的 Windows 计算机

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。
2. 在 **Microsoft Azure** 菜单上选择“安全中心”。 此时会打开“安全中心 - 概览”。

 ![安全中心概述][2]

3. 在“安全中心”主菜单下，选择“加入到高级安全”。
4. 选择“是否要添加非 Azure 计算机”。

   ![加入到高级安全][3]

5. 在“添加新的非 Azure 计算机”上，将显示 Log Analytics 工作区的列表。 该列表包含启用自动预配时为安全中心创建的默认工作区（如果适用）。 选择此工作区或要使用的其他工作区。

    ![添加非 Azure 计算机][4]

  将打开“直接代理”边栏选项卡，其中包含一个用于下载 Windows 代理的链接，以及在配置该代理时要使用的工作区 ID 的密钥。

6.  选择适用于计算机处理器类型的“下载 Windows 代理”链接，以下载安装程序文件。

7.  在“工作区 ID”右侧选择复制图标，并将该 ID 粘贴到记事本中。

8.  在“主密钥”右侧选择复制图标，并将该密钥粘贴到记事本中。

## <a name="install-the-agent"></a>安装代理
现在，必须在目标计算机上安装下载的文件。

1. 将该文件复制到目标计算机并运行安装程序。
2. 在“欢迎”页上，选择“下一步”。
3. 在“许可条款”页面上阅读许可协议，然后选择“我接受”。
4. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后选择“下一步”。
5. 在“代理安装选项”页上，选择将代理连接到 Azure Log Analytics (OMS)，然后选择“下一步”。
6. 在 **Azure Log Analytics** 页上，粘贴在前面步骤中复制到记事本的“工作区 ID” 和“工作区密钥(主密钥)”。
7. 如果计算机应向 Azure 政府云中的 Log Analytics 工作区报告，请从“Azure 云”下拉列表中选择**“Azure 美国政府”**。  如果计算机需要通过代理服务器来与 Log Analytics 服务通信，请选择“高级”并提供代理服务器的 URL 和端口号。
8. 提供所需的配置设置后，选择“下一步”。

  ![安装代理][5]

9. 在“准备安装”页上检查所做的选择，并选择“安装”。
10. 在“配置已成功完成”页上，选择“完成”

完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 可以在该处检查配置，并验证代理是否已连接。

有关安装和配置代理的详细信息，请参阅[连接 Windows 计算机](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup)。

现在可以在一个位置监视 Azure VM 和非 Azure 计算机。 在“计算”下，可以概览所有 VM 和计算机以及建议。 每一列代表一组建议。 颜色表示 VM 或计算机针对该建议的当前安全状态。 安全中心还显示安全警报中这些计算机的任何检测。

  ![“计算”边栏选项卡][6]

有两种类型的图标表示在“计算”边栏选项卡上：

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) 非 Azure 计算机

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>清理资源
如果不再需要使用该代理，可从 Windows 计算机中将其删除。

若要删除该代理，请执行以下操作：

1. 打开“控制面板”
2. 打开“程序和功能”。
3. 在“程序和功能”中选择“Microsoft Monitoring Agent”，单击“卸载”。

## <a name="next-steps"></a>后续步骤
在本快速入门中，已在 Windows 计算机上预配了 Microsoft Monitoring Agent。 若要详细了解如何使用安全中心，请继续阅读教程，了解如何配置安全策略和评估资源的安全性。

> [!div class="nextstepaction"]
> [教程：定义和评估安全策略](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
