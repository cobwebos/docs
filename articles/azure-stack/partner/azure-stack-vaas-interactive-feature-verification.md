---
title: 交互式功能验证测试中作为服务的 Azure Stack 验证 |Microsoft Docs
description: 了解如何为 Azure Stack 验证为服务创建交互式功能验证测试。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5b20e170388aa6e9b73b7c5c26a286ec51b52209
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245831"
---
# <a name="interactive-feature-verification-testing"></a>交互式功能验证测试  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

交互式功能验证测试框架可用于请求有关您的系统测试。 当请求在测试时，Microsoft 使用该框架准备需要交互式的手动步骤的测试。 Microsoft 可以使用框架链接在一起的多个独立自动测试。

本指南介绍了一个简单的手动方案。 该测试检查替换为 Azure Stack 中的磁盘。 框架收集诊断日志在每个步骤。 如您找到它们，您可以调试问题。 框架还允许在共享由其他工具或进程，生成的日志，并使你可以提供反馈的场景。

## <a name="overview-of-a-test-pass"></a>测试轮次的概述

有关磁盘更换的测试是一种常见方案。 在此示例中，测试具有七个步骤：

1.  创建一个新**测试轮次**工作流。
2.  选择**磁盘标识测试**。
3.  启动测试。
4.  在交互式验证方案选择的操作。
5.  检查的方案的结果。
6.  将测试结果发送给 Microsoft。
7.  检查 VaaS 门户中的状态。

## <a name="create-a-new-test-pass"></a>创建新的测试流程

1.  导航到[Azure Stack 验证门户](https://www.azurestackvalidation.com)并登录。

2.  创建新的解决方案，或选择一个现有证书。

3.  选择**启动**上**测试轮次**磁贴。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  输入的名称**测试轮次**工作流。

5.  输入的环境和常见的测试参数中的说明[工作流通用参数作为服务的 Azure Stack 验证](azure-stack-vaas-parameters.md)一文。

6.  诊断连接字符串必须采用中指定的格式[测试参数](azure-stack-vaas-parameters.md#test-parameters)主题中[工作流通用参数](azure-stack-vaas-parameters.md)一文。

7.  测试输入所需的参数。

8.  选择代理来运行交互式测试运行。

> [!Note]  
> 为磁盘标识交互式功能验证测试，必须指定域管理员用户和密码。

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>选择的测试

1.  选择**磁盘标识测试\<版本 >**。

    > [!Note]  
    > 随着所做的改进测试辅助资料，测试版本将递增。 除非 Microsoft 指示，否则，应始终使用最高版本。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  通过选择提供的域管理员用户和密码**编辑**。

3.  选择适当的测试执行代理/DVM 上启动测试。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  选择**提交**以启动测试。

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>启动测试

运行 VaaS 代理的计算机上显示的磁盘标识测试提示。 通常这是 DVM 或 Jumpbox 的 Azure Stack 实例。

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>选择操作

1.  请按照**文档**并**验证**链接，以查看如何执行这种情况下从 Microsoft 的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  选择“**下一步**”。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  按照运行预检查脚本的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  预检查脚本成功完成后，每运行一次手动方案 （磁盘更换） 作为**文档**并**验证**的链接从**信息**选项卡。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  执行手动方案时，不要关闭对话框。

6.  在完成执行的手动方案，请按照运行后检查脚本的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  手动方案 （磁盘更换） 的成功完成后，选择**下一步**。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> 如果关闭窗口，该测试将停止之前完成。

## <a name="check-the-status"></a>检查状态

1.  测试完成后，你将需要提供反馈。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  这些问题将有助于 Microsoft 评估成功速率和发布质量的方案。

## <a name="send-the-test-result"></a>发送测试结果

1.  附加你要向 Microsoft 提交任何日志文件。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  接受反馈提交最终用户许可协议。

3.  选择**提交**将结果发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
