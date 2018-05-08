---
title: 诊断 Azure 开发测试实验室虚拟机中的项目失败 | Microsoft Docs
description: 了解如何对开 Azure 测试实验室中的项目失败进行故障排除。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ebc64215683989ce07f4dd88dc352ecaefe184cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>对实验室中的项目失败进行诊断 
创建项目后，可检查其为成功还是失败。 Azure 开发测试实验室中的项目日志提供了可用于诊断项目失败的信息。 可通过几个选项查看 Windows VM 的项目日志信息：

* 在 Azure 门户中
* 在虚拟机中

> [!NOTE]
> 若要确保正确标识失败项目并对其进行解释，请务必确保项目结构正确。 有关如何正确构造项目的信息，请参阅[创建自定义项目](devtest-lab-artifact-author.md)。 若要查看结构正确的项目的示例，请查看此[测试参数类型](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)项目。

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>使用 Azure 门户对项目失败进行故障排除

1. 在 Azure 门户中的资源列表中选择实验室。
2. 选择包含想要调查的项目的 Windows VM。
3. 在左侧面板中，在“常规”下选择“项目”。 随即会出现与该 VM 关联的项目的列表。 其中显示了项目的名称和状态。

   ![项目状态](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. 选择状态显示为“失败”的项目。 项目随即打开。 此时会显示一条扩展消息，其中包含有关项目失败的详细信息。

   ![项目错误消息](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>从虚拟机中对项目失败进行故障排除

1. 登录到包含要诊断的项目的 VM。
2. 转到 C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\1.9\Status，其中 1.9 为 Azure 自定义脚本扩展版本号。

   ![状态文件](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. 打开状态文件。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [使用开发测试实验室中的资源管理器模板将 VM 加入到现有 Active Directory 域中](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>后续步骤
* 了解如何[将 Git 存储库添加到实验室](devtest-lab-add-artifact-repo.md)。

