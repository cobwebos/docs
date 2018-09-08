---
title: 监视测试与 Azure Stack 验证即服务 |Microsoft Docs
description: 监视与作为服务的 Azure Stack 验证测试。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 2dc4d3f2855864ff80648b5b9635ff28c0dacbb7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163313"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>使用 Azure Stack 验证即服务监视测试

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

可以通过查看正在进行的或已完成的测试套件的“操作”页面来监视测试的执行。 该页详细说明了测试的状态及其操作。

## <a name="monitor-a-test"></a>监视测试

1. 选择一个解决方案。

2. 在任意工作流磁贴上选择“管理”。

3. 单击某个工作流以打开其测试摘要页面。

4. 展开任意测试套件实例的上下文菜单 **[...]**。

5. 选择“查看操作”。

![Alt text](media\image4.png)

对于已完成运行的测试，可以从测试摘要页面通过单击测试的上下文菜单 **[...]** 中的“下载日志”来下载日志。Azure Stack 合作伙伴可以使用这些日志来调试失败测试的问题。

## <a name="open-the-test-pass-summary"></a>打开测试轮次摘要

1. 打开门户。 
2. 选择包含以前运行的或已计划的测试的现有解决方案的名称。

    ![管理测试轮次](media/managetestpasses.png)

3. 选择“测试轮次”面板中的“管理”。
4. 选择要打开测试轮次摘要的测试轮次。 你可以查看测试名称、创建日期、运行、测试花费的时长，以及结果（成功或失败）。
5. 选择 [ **. .  .** ]。

### <a name="test-pass-summary"></a>测试轮次摘要

| 列 | 说明 |
| --- | --- |
| 测试名称 | 测试的名称。 引用验证编号。 |
| 已创建 | 测试轮次的创建时间。 |
| 已启动 | 测试轮次开始运行的时间。 |
| Duration | 运行测试轮次花费的时间长度。 |
| 状态 | 测试轮次的结果（成功或失败）。 |
| 代理名称 | 代理的完全限定域名。 |
| 总操作数 | 在测试轮次中尝试的操作的总数。 |
| 通过的操作数 | 在测试轮次中通过的操作的数目。 |
|  失败的操作数 | 失败的操作的数目。 |

### <a name="group-columns-in-the-test-pass-summary"></a>对测试轮次摘要中的列进行分组

可以选择一个列并将其拖到标题中，以便针对该列值创建组。

## <a name="reschedule-a-test"></a>重新计划测试

1. [打开测试轮次摘要](#open-the-test-pass-summary)。
2. 选择“重新计划”来重新计划测试轮次。
3. 输入你的 Azure Stack 实例的云管理员密码。
4. 输入你在设置帐户时定义的诊断存储连接字符串。
5. 重新计划测试。

## <a name="cancel-a-test"></a>取消测试

1. [打开测试轮次摘要](#open-the-test-pass-summary)。
2. 选择“取消”。

## <a name="get-test-information"></a>获取测试信息

1. [打开测试轮次摘要](#open-the-test-pass-summary)。
2. 选择“查看信息”来重新计划测试轮次。

**测试信息**

| 名称 | 说明 |
| -- | -- |
| 测试名称 | 测试的名称，例如“OEM Update on Azure Stack 1806 RC Validation”。 |
| 测试版本 | 测试的版本，例如 5.1.4.0。 |
| 发布者 | 测试发布者，如 Microsoft。 |
| 类别 | 测试的类别，例如“功能”或“可靠性”。 |
| 目标服务 | 所测试的服务，例如 VirtualMachines |
| 说明 | 对测试的说明。 |
| 估计持续时间(分钟) | 运行测试花费的时间长度，以分钟为单位。 |
| 链接 | 指向 GitHub 问题跟踪程序的链接。 |

## <a name="get-test-parameters"></a>获取测试参数

1. [打开测试轮次摘要](#open-the-test-pass-summary)。
2. 选择“查看参数”来重新计划测试轮次。

**参数**

| 名称 | 说明 |
| -- | -- |
| 测试名称 | 测试的名称，例如 oemupdate1806test。 |
| 测试版本 | 测试的版本，例如 5.1.4.0。 |
| 测试实例 ID | 一个标识测试的特定实例的 GUID，例如 20b20645-b400-4f0d-bf6f-1264d866ada9。 |
| cloudAdminUser | 用作云管理员的帐户的名称，例如 **cloudadmin**。 |
| DiagnosticsContainerName | 诊断容器的 ID，例如 04dd3815-5f35-4158-92ea-698027693080。 |

## <a name="get-test-operations"></a>获取测试操作

1. [打开测试轮次摘要](#open-the-test-pass-summary)。
2. 选择“查看操作”来重新计划测试轮次。 操作摘要窗格随即打开。

## <a name="get-test-logs"></a>获取测试日志

1. [打开测试轮次摘要](#open-the-test-pass-summary)。
2. 选择“下载日志”来重新计划测试轮次。  
    将下载包含日志的一个名为 ReleaseYYYY-MM-DD.zip 的 zip 文件。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。
