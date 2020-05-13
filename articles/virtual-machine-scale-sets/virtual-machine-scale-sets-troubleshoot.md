---
title: 排查虚拟机规模集的自动缩放问题
description: 疑难解答使用虚拟机规模集的自动缩放问题。 了解遇到的典型问题以及如何解决这些问题。
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 11/16/2017
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 2ef50704d96cc51881594c778d1a4b109a1eae82
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125139"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>疑难解答使用虚拟机规模集的自动缩放问题
**** 问题 - 已使用虚拟机规模集在 Azure 资源管理器中创建自动缩放基础结构（例如，通过部署一个与此类似的模板：https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale）。已定义了缩放规则，其效果良好，但无论在 VM 中施放多少负载，它都不会自动缩放。

## <a name="troubleshooting-steps"></a>疑难解答步骤
应考虑的一些事项包括：

* 每个 VM 有多少个 vCPU，是否加载了所有 vCPU？
  上面的 Azure 快速入门模板示例具有 do_work.php 脚本，它可以加载单个 vCPU。 如果正在使用比单 vCPU VM 大小（如 Standard_A1 或 D1）更大的 VM，则需要多次运行此加载过程。 通过查看 [Azure 中 Windows 虚拟机的大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)检查 VM 中有多少个 vCPU
* 虚拟机规模集中有多少个 VM，正在处理每个 VM 吗？
  
    仅当规模集中所有  VM 的平均 CPU 在自动缩放规则中定义的内部时间之内超出阈值时，才会发生横向扩展事件。
* 是否遗漏任何缩放事件？
  
    查看 Azure 门户中的审核日志以查找缩放事件。 或许遗漏了一个纵向扩展事件和一个纵向缩减事件。 可以通过“缩放”进行筛选。
  
    ![审核日志][audit]
* 缩小和扩大阈值的差值是否足够大？
  
    假设你设置了一个规则，指示当平均 CPU 在 5 分钟内超过 50% 时横向扩展，当平均 CPU 低于 50% 时横向缩减。 在 CPU 使用率接近此阈值时，此设置会导致“摇摆”问题，缩放操作不断地增加和减少集的大小。 由于此设置，自动缩放服务会尝试防止“摇摆”的发生，这可能表现为不缩放。 因此，请确保扩大和缩小阈值的差值要足够大，以允许在缩放之间存在一些空间。
* 是否编写过自己的 JSON 模板？
  
    编写时很容易犯错，因此可使用如上述的久经验证的模板来开始编写，并进行微小的增量更改。 
* 可以手动横向缩减或扩展吗？
  
    请尝试使用不同的“容量”设置重新部署虚拟机规模集资源，以手动更改 VM 的数目。 此处是一个示例模板： https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – 你可能需要编辑该模板以确保它与缩放集所用的计算机大小相同。 如果成功手动更改 VM 数目，则可知该问题与自动缩放无关。
* 检查 Microsoft.Compute/virtualMachineScaleSet 和 [Azure Resource Explorer](https://resources.azure.com/) 中的 Microsoft.Insights 资源
  
    Azure 资源浏览器是一个不可或缺的疑难解答工具，它显示 Azure 资源管理器资源的状态。 单击订阅并查看要对其进行故障排除的资源组。 在计算资源提供程序下查看创建的虚拟机规模集，并检查显示部署状态的实例视图。 此外，还应检查 VM 规模集中虚拟机的实例视图。 然后，转到 Microsoft.Insights 资源提供程序并检查自动缩放规则是否一切正常。
* 诊断扩展运行正常且可发出性能数据吗？
  
    **** 更新：已增强 Azure 自动缩放，以使用基于主机的指标管道，这将不再需要安装诊断扩展。 如果使用新管道创建自动缩放应用程序，则后续几个段落不再适用。 此处提供了一个已转换为使用主机管道的 Azure 模板的示例：https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale。 
  
    使用基于主机的指标进行自动缩放比较好的原因如下：
  
  * 由于没有诊断扩展，需要安装更少的移动部件。
  * 模板更简单。 只需将 insights 自动缩放规则添加到现有规模集模板。
  * 新 VM 的报告更可靠，并且启动更快。
    
    你可能想要继续使用诊断扩展的唯一原因是需要内存诊断报告/缩放。 基于主机的指标不会报告内存。
    
    考虑到这一点，如果在使用诊断扩展进行自动缩放，则只需按照本文的剩余部分进行操作即可。
    
    Azure 资源管理器中的自动缩放借助于（但不再必须借助于）称为“诊断扩展”的 VM 扩展才能正常工作。 它会向模板中定义的存储帐户发出性能数据。 然后此数据由 Azure Monitor 服务聚合。
    
    如果 Insights 服务无法从 VM 读取数据，它应向你发送电子邮件。 例如，如果 VM 关闭你应收到电子邮件。 请务必查看创建 Azure 帐户时在电子邮件地址中指定的电子邮件。
    
    此外，还可以自己查看数据。 使用云资源管理器查看 Azure 存储帐户。 例如，使用 [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2) 登录，并选择正在使用的 Azure 订阅。 然后，查看部署模板中的诊断扩展定义引用的诊断存储帐户名称。
    
    ![Cloud Explorer][explorer]
    
    你将看到许多表，其中存储着每个 VM 的数据。 以 Linux 和 CPU 指标为例，查看最近的行。 Visual Studio Cloud Explorer 支持查询语言，因此，你可以运行查询。 例如，可以针对“Timestamp gt datetime’2016-02-02T21:20:00Z’”运行查询，以确保获得最近的事件。 时区对应 UTC。 在此处看到的数据是否符合设置的缩放规则？ 在下面的示例中，虚拟机 20 的 CPU 在过去 5 分钟内开始增加到 100%。
    
    ![存储表][tables]
    
    如果数据不存在，则意味着问题与在 VM 中运行的诊断扩展相关。 如果数据存在，则意味着问题与缩放规则或 Insights 服务相关。 检查 [Azure 状态](https://azure.microsoft.com/status/)。
    
    完成这些步骤后，如果仍然存在自动缩放问题，则可以尝试使用以下资源： 
    * 访问 [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows) 或 [Stack overflow](https://stackoverflow.com/questions/tagged/azure) 论坛 
    * 记录支持人员电话。 准备共享模板和性能数据的视图。

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
