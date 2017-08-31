---
title: "诊断 Azure 开发测试实验室 VM 中的项目失败 | Microsoft 文档"
description: "了解如何解决开发测试实验室中的项目失败问题"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e4f2946d0ba0756f36622aded0e8594acabb9527
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="diagnose-artifact-failures-in-the-lab"></a>对实验室中的项目失败进行诊断 
创建项目后，可以检查以了解它成功还是失败。 开发测试实验室中的项目日志提供了可用于诊断项目失败的信息。 可以通过多种方式查看针对 Windows VM 的项目日志信息。

> [!NOTE]
> 若要确保正确标识失败项目并对其进行解释，请务必正确构造项目。 有关如何正确构造项目的信息，请参阅[创建自定义项目](devtest-lab-artifact-author.md)。 若要查看正确构造的项目示例，请查看此[测试参数类型](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)项目。

## <a name="troubleshoot-artifact-failures-using-the-azure-portal"></a>使用 Azure 门户解决项目失败问题
若要使用 Azure 门户在项目创建期间诊断故障，请按照下列步骤操作：

1. 在资源列表中，选择你的实验室。

2. 选择包含你想要调查的项目的 Windows VM。

3. 在“常规”下的左侧面板中，选择“项目”。 将显示与该 VM 关联的项目列表，指示项目的名称及其状态。

   ![项目 git 存储库示例](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. 选择状态显示为“失败”的项目。 项目将打开并显示扩展消息，其中包含有关项目失败的详细信息。

   ![项目 git 存储库示例](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-vm"></a>从 VM 中解决项目失败问题
若要查看虚拟机中的项目日志，请按照下列步骤操作：

1. 登录到包含你要诊断的项目的 VM。

2. 导航到 C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.9\Status，其中“1.9”为 CSE 版本号。

   ![项目 git 存储库示例](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. 打开“状态”文件，以查看可帮助诊断此 VM 项目失败的信息。




[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [使用 Azure 开发测试实验室中的资源管理器模板将 VM 加入到现有 AD 域](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>后续步骤
* 了解如何[将 Git 存储库添加到实验室](devtest-lab-add-artifact-repo.md)。


