---
title: "疑难解答使用虚拟机规模集的自动缩放问题 | Microsoft Docs"
description: "疑难解答使用虚拟机规模集的自动缩放问题。 了解遇到的典型问题以及如何解决这些问题。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: guybo
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: bd45a0fb99a77851aa7b91d23bd4b830b6f5cc7b
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>疑难解答使用虚拟机规模集的自动缩放问题
**问题** - 你已使用 VM 规模集在 Azure Resource Manager 中创建自动缩放基础结构 - 例如，通过部署与此类似的模板：https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale - 你定义了缩放规则，其效果良好，只不过无论在 VM 中施放多少负载，它都不会自动缩放。

## <a name="troubleshooting-steps"></a>疑难解答步骤
应考虑的一些事项包括：

* 每个 VM 有多少个核心？您加载了所有核心吗？
  上面的 Azure 快速入门模板示例具有 do_work.php 脚本，它加载单个核心。 如果正在使用比单核 VM 大小（如 Standard_A1 或 D1）更大的 VM，则需要多次运行此负载。 通过查看 [Azure 中 Windows 虚拟机的大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)检查 VM 中有多少个核心
* VM 规模集中有多少个 VM？您正在处理每个 VM 吗？
  
    仅当规模集中**所有** VM 的平均 CPU 在自动缩放规则中内部定义的时间之内超出阈值时，才会发生扩大事件。
* 是否遗漏任何缩放事件？
  
    检查 Azure 门户中的审核日志以查找缩放事件。 或许遗漏了一个增加事件和一个减少事件。 可以通过“缩放”筛选...
  
    ![审核日志][audit]
* 您的缩小和扩大阈值的差值是否足够大？
  
    假设您设置了一个规则，该规则指示当平均 CPU 在 5 分钟内超过 50% 时扩大，当平均 CPU 低于 50% 则缩小。 在 CPU 使用率接近此阈值时，这将导致“摇摆”问题，缩放操作不断地增加和减少集的大小。 因此，自动缩放服务尝试防止“摇摆”的发生，这可能表现为不缩放。 因此请确保扩大和缩小阈值的差值足够大，以允许在缩放之间存在一些空间。
* 您是否编写过自己的 JSON 模板？
  
    编写时很容易犯错，因此可使用如上述的久经验证的模板来开始编写，然后进行微小的增量更改。 
* 您可以手动缩小或扩大吗？
  
    请尝试使用不同的“容量”设置重新部署 VM 规模集资源，以手动更改 VM 的数目。 此处是一个用于执行此操作的示例模板：https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – 可能需要编辑模板以确保其虚拟机大小和你的规模集正在使用的相同。 如果成功手动更改 VM 数目，则可知该问题与自动缩放无关。
* 检查 Microsoft.Compute/virtualMachineScaleSet 和 [Azure Resource Explorer](https://resources.azure.com/) 中的 Microsoft.Insights 资源
  
    这是一个不可或缺的疑难解答工具，它显示 Azure Resource Manager 资源的状态。 单击您的订阅并查看您要对其进行故障排除的资源组。 在计算资源提供程序下查看您创建的 VM 规模集并检查实例视图，它显示部署的状态。 此外还应检查 VM 规模集中 VM 的实例视图。 然后，转到 Microsoft.Insights 资源提供程序并检查自动缩放规则是否一切正常。
* 诊断扩展运行正常且可发出性能数据吗？
  
    **更新：**已增强 Azure 自动缩放，以使用基于主机的指标管道，这不再需要安装诊断扩展。 这意味着如果你使用新管道创建自动缩放应用程序，则后续几个段落将不再适用。 改为使用主机管道的 Azure 模板示例：https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale。 
  
    使用基于主机的指标进行自动缩放更好的原因如下：
  
  * 由于没有诊断扩展，需要安装更少的移动部件。
  * 模板更简单。 只需将 insights 自动缩放规则添加到现有规模集模板。
  * 新 VM 的报告更可靠，并且启动更快。
    
    你可能想要继续使用诊断扩展的唯一原因是如果需要内存诊断报告/缩放。 基于主机的指标不会报告内存。
    
    考虑到这一点，如果仍在使用诊断扩展进行自动缩放，只需按照本文的剩余部分进行操作即可。
    
    Azure Resource Manager 中的自动缩放借助于（但不再必须借助于）称为“诊断扩展”的 VM 扩展才能正常工作。 它会向模板中定义的存储帐户发出性能数据。 然后此数据由 Azure 监视器服务聚合。
    
    如果 Insights 服务无法从 VM 中读取数据，它会发送给你一封电子邮件 - 例如，如果 VM 已关闭，那么应查看你的电子邮件（创建 Azure 帐户时指定的电子邮件）。
    
    此外，还可以自己前往并查看数据。 使用云资源管理器查看 Azure 存储帐户。 例如使用 [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)，登录并选取正在使用的 Azure 订阅以及部署模板中诊断扩展定义中引用的诊断存储帐户名称...
    
    ![云资源管理器][explorer]
    
    将在此处看到许多表，其中存储着每个 VM 的数据。 以 Linux 和 CPU 指标为例，查看最近的行。 Visual Studio Cloud Explorer 支持查询语言，因此可以运行类似“Timestamp gt datetime'2016-02-02T21:20:00Z'”的查询以确保获取最近的事件（假设时间采用 UTC 格式）。 您在此处看到的数据是否符合您设置的缩放规则？ 在下面的示例中，虚拟机 20 的 CPU 在过去 5 分钟内开始增加到 100%...
    
    ![存储表][tables]
    
    如果数据不存在，则意味着问题与在 VM 中运行的诊断扩展相关。 如果数据存在，则意味着问题与缩放规则或 Insights 服务相关。 检查 [Azure 状态](https://azure.microsoft.com/status/)。
    
    完成这些步骤后，如果仍然存在自动缩放问题，可以尝试访问 [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp) 上的论坛或 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure)，或者电话联系支持人员。 准备共享模板和性能数据的视图。

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

