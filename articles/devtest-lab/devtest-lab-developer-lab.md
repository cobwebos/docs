---
title: "使用面向开发人员的 Azure 开发测试实验室 | Microsoft Docs"
description: "了解如何使用面向开发人员的 Azure 开发测试实验室。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c187819e9392908c8979556f80e8c94739eb14d5
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="use-azure-devtest-labs-for-developers"></a>使用面向开发人员的 Azure 开发测试实验室
可使用 Azure 开发测试实验室来实现许多关键方案，但其中一个主要方案涉及使用开发测试实验室为开发人员托管开发计算机。 在此方案中，开发测试实验室具有以下优势：

- 开发人员可快速按需预配其开发计算机。
- 开发人员可在任何需要的时候轻松自定义其开发计算机。
- 管理员可以控制成本，只需确保：
  - 开发人员可获取的 VM 数量不超过开发所需的 VM 数量。
  - VM 在不使用时关闭。 

![使用开发测试实验室进行培训](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

在本文中可了解各种 Azure 开发测试实验室功能（可使用这些功能满足开发人员要求）和设置实验室可遵循的详细步骤。

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>使用 Azure 开发测试实验室实现开发人员环境
1. **实验室** 
   
    实验室是 Azure 开发测试实验室的起点。 创建实验室后，可以执行将用户（开发人员）添加到实验室、设置策略来控制成本、定义可快速创建的 VM 映像等任务。  
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中创建实验室](devtest-lab-create-lab.md) |了解如何在 Azure 门户中的 Azure 开发测试实验室中创建实验室。 |
2. 使用现成应用商店映像和自定义映像在几分钟内创建 VM 
   
    可在 Azure Marketplace 从多种映像中选择现成映像并使它们在实验室中可用。 如果现成的映像不能满足需求，可使用 Azure Marketplace 中的现成映像创建实验室 VM，安装所需的所有软件并在实验室中将 VM 保存为自定义映像来创建自定义映像。

    如果将使用自定义映像，请考虑使用映像工厂创建和分发映像。 映像工厂是一种配置即代码解决方案，自动定期生成并分发配置的映像。 这节约了使用基本 OS 创建 VM 后手动配置系统所需的时间。
  
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [配置 Azure 应用商店映像](devtest-lab-configure-marketplace-images.md) |了解如何将 Azure Marketplace 映像列入允许列表，仅使需用于开发人员的映像可供选择。|
   | [创建自定义映像](devtest-lab-create-template.md) |通过预安装所需的软件创建自定义映像，让开发人员能够使用自定义映像快速创建 VM。|
   | [了解映像工厂](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |观看介绍如何设置和使用映像工厂的视频。|

3. 为开发人员计算机创建可重复使用的模板 
   
    Azure 开发测试实验室中的公式是用于创建 VM 的默认属性值的列表。 可通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络在实验室中创建公式。 每个开发人员都可在实验室中查看公式并使用公式创建 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [管理用于创建 VM 的开发测试实验室公式](devtest-lab-manage-formulas.md) |了解如何通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络创建公式。|

4. 创建项目，实现灵活的 VM 自定义

   项目用于在预配 VM 后部署和配置应用程序。 项目可以是：

   - 想要在 VM 上安装的工具，例如，代理、Fiddler 和 Visual Studio。
   - 想要在 VM 上运行的操作，例如，克隆存储库。
   - 想要测试的应用程序。

   许多项目现成可用。 如果需要更多自定义来满足特定需求，可创建自己的自定义项目。

   在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | 为开发测试实验室 VM 创建自定义项目[](devtest-lab-artifact-author.md) |在实验室中为虚拟机创建自己的自定义项目。|
   | 添加 Git 存储库来存储自定义项目和 Azure Resource Manager 模板以供在 Azure 开发测试实验室中使用[](devtest-lab-add-artifact-repo.md) |了解如何在自己专用的 Git 存储库中存储自定义项目。|

5. **控制成本**
   
    Azure 开发测试实验室允许在实验室中设置策略，指定可在实验室中由开发人员创建的 VM 的最大数量。 
   
    如果开发人员团队有一个已定的工作计划，需要在一天的某个特定时间点停止所有 VM，然后自动在第二天重启它们，则可通过在实验室中设置自动关机和自动启动策略轻松完成。 
   
    最后，完成应用开发后，可通过运行单一 PowerShell 脚本一次性删除所有 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [定义实验室策略](devtest-lab-set-lab-policy.md) |通过在实验室中设置策略来控制成本。 |
   | [使用 PowerShell 脚本删除所有实验室 VM](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |开发完成后，在一次操作中删除所有实验室。|

1. 将虚拟网络添加到 VM 
   
    每当创建实验室时，开发测试实验室都会创建新的虚拟网络 (VNET)。 如果已配置自己的 VNET（例如，使用 ExpressRoute 或站点到站点 VPN 配置），可将此 VNET 添加到实验室的虚拟网络设置，使其在创建 VM 时可用。

    此外，存在一个 Azure Active Directory 域联接项目可用，它会在创建 VM 时将 VM 加入到某个域。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中配置虚拟网络](devtest-lab-configure-vnet.md) |了解如何使用 Azure 门户在 Azure 开发测试实验室中配置虚拟网络。|

6. 与每个开发人员共享实验室
   
    可使用与开发人员共享的链接直接访问实验室。 开发人员甚至不需要具有 Azure 帐户，只要他们具有 [Microsoft 帐户](devtest-lab-faq.md#what-is-a-microsoft-account)即可。 开发人员无法看到其他开发人员创建的 VM。  
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | 向 Azure 开发测试实验室中的实验室添加开发人员[](devtest-lab-add-devtest-user.md) |使用 Azure 门户将开发人员添加到实验室。|
   | [使用 PowerShell 脚本将开发人员添加到实验室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |使用 PowerShell 自动将开发人员添加到实验室。 |
   | [获取实验室的链接](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |了解开发人员如何通过超链接直接访问实验室。|

7. 自动为多个团队创建实验室 
   
    可通过创建Resource Manager 模板自动化实验室创建（包括自定义设置）并将其用于反复创建相同实验室。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [使用 Resource Manager 模板创建实验室](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) |使用 Resource Manager 模板在 Azure 开发测试实验室中创建实验室。 |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]


