---
title: 监视测试与 Azure Stack 验证即服务 |Microsoft Docs
description: 监视 Azure Stack 验证作为服务的已知问题的测试。
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
ms.openlocfilehash: 0bc45ea23cfb3f9b8cf7b117b5c3427d9945878e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235295"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>监视测试与作为服务的 Azure Stack 验证

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

可以通过查看监视测试的执行**操作**正在进行或已完成的测试套件的页。 本页详细介绍了测试和其操作的状态。

## <a name="monitor-a-test"></a>监视测试

1. 选择的解决方案。

2. 选择**管理**的任意工作流磁贴。

3. 单击以打开其测试摘要页的工作流。

4. 展开上下文菜单 **[...]** 任何测试套件实例。

5. 选择**查看操作**

![Alt text](media\image4.png)

对于已完成运行的测试，日志可以从下载测试摘要页上单击**下载日志**测试的上下文菜单中 **[...]**.Azure Stack 合作伙伴可以使用这些日志来调试失败的测试的问题。

## <a name="open-the-test-pass-summary"></a>打开测试阶段摘要

1. 打开门户。 
2. 选择包含以前或计划运行测试的现有解决方案的名称。

    ![管理测试通过](media/managetestpasses.png)

3. 选择**管理**中**测试轮次**面板。
4. 选择测试阶段，若要打开测试阶段摘要。 你可以查看测试名称，创建日期，运行测试所用时间和结果 （成功或失败）。
5. 选择 [ **... .** ].

### <a name="test-pass-summary"></a>测试阶段摘要

| 列 | 说明 |
| --- | --- |
| 测试名称 | 测试的名称。 引用验证数。 |
| 已创建 | 测试轮次的创建时间。 |
| 已启动 | 在过去的测试运行时间。 |
| Duration | 时间测试通过运行所花费的时间长度。 |
| 状态 | 其余部分传递结果 （成功或失败）。 |
| 代理名称 | 代理的完全限定的域名。 |
| 总操作数 | 在测试轮次中尝试的操作的总数。 |
| 传递的操作 | 测试轮次中传递的操作数目。 |
|  失败的操作 | 失败的操作数目。 |

### <a name="group-columns-in-the-test-pass-summary"></a>在测试中的组列传递摘要

可以选择并将列拖入要在列的值创建一个组的标头。

## <a name="reschedule-a-test"></a>重新计划测试

1. [打开测试阶段摘要](#open-the-test-pass-summary)。
2. 选择**重新计划**重新计划测试通过。
3. 输入你的 Azure Stack 实例的云管理员密码。
4. 输入定义时设置你的帐户的诊断存储连接字符串。
5. 重新计划测试。

## <a name="cancel-a-test"></a>取消测试

1. [打开测试阶段摘要](#open-the-test-pass-summary)。
2. 选择“取消”。

## <a name="get-test-information"></a>获取测试信息

1. [打开测试阶段摘要](#open-the-test-pass-summary)。
2. 选择**查看信息**重新计划测试通过。

**有关测试的信息**

| 名称 | 说明 |
| -- | -- |
| 测试名称 | 测试，例如，在 Azure Stack 1806 RC 验证 OEM 更新的名称。 |
| 测试版本 | 测试，例如，5.1.4.0 的版本。 |
| 发布者 | 测试发布者，如 Microsoft。 |
| 类别 | 类别的测试，如**功能**或**可靠性**。 |
| 目标服务 | 要测试，如 VirtualMachines 服务 |
| 说明 | 测试的说明。 |
| 估计持续时间 （分钟） | 以分钟为单位运行测试所花的时间长度。 |
| 链接 | 指向 GitHub 问题跟踪程序的链接。 |

## <a name="get-test-parameters"></a>获取测试参数

1. [打开测试阶段摘要](#open-the-test-pass-summary)。
2. 选择**查看参数**重新计划测试通过。

**参数**

| 名称 | 说明 |
| -- | -- |
| 测试名称 | 测试，例如，oemupdate1806test 的名称。 |
| 测试版本 | 其余部分，例如，5.1.4.0 版本。 |
| 测试实例 ID | 标识特定的测试，例如，实例的 GUID 20b20645-b400-4f0d-bf6f-1264d866ada9。 |
| cloudAdminUser | 作为云管理员，例如，使用的帐户的名称**cloudadmin**。 |
| DiagnosticsContainerName | 诊断容器，例如，ID 04dd3815-5f35-4158-92ea-698027693080。 |

## <a name="get-test-operations"></a>获取测试操作

1. [打开测试阶段摘要](#open-the-test-pass-summary)。
2. 选择**查看操作**重新计划测试通过。 摘要窗格将打开操作。

## <a name="get-test-logs"></a>获取测试日志

1. [打开测试阶段摘要](#open-the-test-pass-summary)。
2. 选择**下载日志**重新计划测试通过。  
    Zip 文件的名称包含的日志下载的 ReleaseYYYY-MM-DD.zip。

## <a name="next-steps"></a>后续步骤

- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。