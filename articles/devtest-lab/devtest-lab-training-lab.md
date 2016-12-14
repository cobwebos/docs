---
title: "使用 Azure 开发测试实验室培训 | Microsoft Docs"
description: "了解如何使用 Azure 开发测试实验室培训的方案。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: steved0x
manager: douge
editor: 
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a68cdf246559fa5261aa4744a3f3839c4d8052d8


---
# <a name="use-azure-devtest-labs-for-training"></a>使用 Azure 开发测试实验室培训
除开发/测试外，Azure 开发测试实验室可用于执行许多关键方案。 这些方案之一是为培训设置实验室。 Azure 开发测试实验室允许你创建实验室，你可在其中提供自定义模板，每个学员都可使用该模板创建相同和隔绝的培训环境。 你可确保为每个学员提供培训环境（需要时）并包含培训所需的足够资源（例如虚拟机）。 最后，可轻松与学员共享实验室（学员通过单击即可访问）。   

![使用开发测试实验室进行培训](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure 开发测试实验室满足在任意虚拟环境中进行培训所需的以下要求： 

* 学员无法看到由其他学员创建的 VM
* 每台培训计算机都应相同
* 学员可快速设置其培训环境
* 通过确保学员无法获取多于培训所需的资源和在不使用时关闭虚拟机来控制成本
* 轻松与学员共享培训实验室
* 反复重新使用培训实验室

你将在本文中了解各种 Azure 开发测试实验室功能，这些功能可用于满足上述培训要求和设置实验室进行培训所需的详细步骤。  

## <a name="implementing-training-with-azure-devtest-labs"></a>使用 Azure 开发测试实验室执行培训
1. **实验室** 
   
    实验室是 Azure 开发测试实验室的起点。 创建实验室后，可执行多种任务，如将用户（学员）添加到实验室、设置策略来控制成本、定义 VM 映像（可快速创建）等。   
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中创建实验室](devtest-lab-create-lab.md) |了解如何在 Azure 门户中的 Azure 开发测试实验室中创建实验室。 |
2. **使用现成应用商店映像和自定义映像在几分钟内创建培训 VM** 
   
    可在 Azure 应用商店从多种映像中选择现成映像并将其提供给实验室中的学员。 如果现成的映像不能满足你的需求，可使用 Azure 应用商店中的现成映像创建实验室 VM、在培训所需的所有软件上安装和在实验室中将 VM 保存为自定义映像来创建自定义映像。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [配置 Azure 应用商店映像](devtest-lab-configure-marketplace-images.md) |了解如何将 Azure 应用商店映像列入允许列表；仅将培训所需的映像选择为可用。 |
   | [创建自定义映像](devtest-lab-create-template.md) |通过预先安装培训所需的软件创建自定义映像，让学员能够使用自定义映像快速创建 VM。 |
3. **为培训计算机创建可重复使用的模板** 
   
    Azure 开发测试实验室中的公式是用于创建 VM 的默认属性值的列表。 可通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络在实验室中创建公式。 每个学员可在实验室中查看公式并将其用于创建 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [管理用于创建 VM 的开发测试实验室公式](devtest-lab-manage-formulas.md) |了解如何通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络创建公式。 |
4. **控制成本**
   
    Azure 开发测试实验室允许你在实验室中设置策略以指定可在实验室中由学员创建的 VM 的最大数量。 
   
    如果要进行多天的培训，需要在一天的某个时间点停止所有 VM，然后自动在第二天重新启动它们，则可通过在实验室中设置自动关机和自动启动策略轻松完成。 
   
    最后，完成培训后，可通过运行单一 PowerShell 脚本立即删除所有 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [定义实验室策略](devtest-lab-set-lab-policy.md) |通过在实验室中设置策略来控制成本。 |
   | [使用 PowerShell 脚本删除所有实验室 VM](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |培训完成后在一次操作中删除所有实验室。 |
5. **与每个学员共享实验室**
   
    可使用与你的学员共享的链接直接访问实验室。 你的学员甚至不需要具有 Azure 帐户，只要它们具有 [Microsoft 帐户](devtest-lab-faq.md#what-is-a-microsoft-account)即可。 学员无法看到由其他学员创建的 VM。  
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中将学员添加到实验室](devtest-lab-add-devtest-user.md) |使用 Azure 门户将学员添加到你的培训实验室。 |
   | [使用 PowerShell 脚本将学员添加到实验室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |使用 PowerShell 自动将学员添加到你的培训实验室。 |
   | [获取实验室的链接](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |了解如何通过超链接直接访问实验室。 |
6. **反复重新使用实验室** 
   
    可通过创建Resource Manager 模板自动化实验室创建（包括自定义设置）并将其用于反复创建相同实验室。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [使用 Resource Manager 模板创建实验室](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) |使用 Resource Manager 模板在 Azure 开发测试实验室中创建实验室。 |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]




<!--HONumber=Nov16_HO3-->


