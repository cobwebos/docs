---
title: "将 Azure 开发测试实验室用于 VM 和 PaaS 测试环境 | Microsoft Docs"
description: "了解如何将 Azure 开发测试实验室用于 VM 和 PaaS 测试环境方案。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: tarcher
ms.openlocfilehash: 6926fe1be44c078482dd5073788e36d7ae77efef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>将 Azure 开发测试实验室用于 VM 和 PaaS 测试环境

Azure 开发测试实验室可用于实现许多关键方案，但其中一个主要方案涉及使用开发测试实验室为测试人员托管主机。 

在此方案中，开发测试实验室具有以下优势：

- 测试人员可使用可重用模板和项目快速预配 Windows 和 Linux 环境，从而测试最新版应用程序。
- 测试人员可以预配多个测试代理，从而纵向扩展负载测试。
- 管理员可以控制成本，只需确保：
  - 测试人员可获取的 VM 数量不超过所需。
  - VM 在不使用时关闭。

![使用开发测试实验室进行培训](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

本文介绍了各种可满足测试人员要求的 Azure 开发测试实验室功能，并详细介绍了如何设置实验室。

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>使用 Azure 开发测试实验室实现测试环境
1. **实验室** 
   
    实验室是 Azure 开发测试实验室的起点。 创建实验室后，便可以执行任务，如将用户（测试人员）添加到实验室、设置成本控制策略、定义可快速创建的 VM 映像等。  
   
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
   | [配置 Azure 应用商店映像](devtest-lab-configure-marketplace-images.md) |了解如何将 Azure Marketplace 映像列入白名单，同时仅提供对测试人员可用的映像以供选择。|
   | [创建自定义映像](devtest-lab-create-template.md) |通过预安装所需的软件创建自定义映像，让测试人员能够使用自定义映像快速创建 VM。|
   | [了解映像工厂](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |观看介绍如何设置和使用映像工厂的视频。|

3. **创建测试计算机的可重用模板** 
   
    Azure 开发测试实验室中的公式是用于创建 VM 的默认属性值的列表。 可通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络在实验室中创建公式。 每个测试人员都可以在实验室中查看公式，并使用公式创建 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [管理用于创建 VM 的开发测试实验室公式](devtest-lab-manage-formulas.md) |了解如何通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络创建公式。|

3. **创建多 VM 测试环境** 
   
    可使用 Azure 资源管理器模板，定义 Azure 解决方案的基础结构和配置，并重复部署多个状态一致的测试 VM。

    可以通过资源管理器模板在环境中预配 Azure PaaS 资源，并在成本跟踪中显示此类资源。 不过，VM 自动关闭不适用于 PaaS 资源。

    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [使用 Azure Resource Manager 模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md) |了解如何为测试环境部署多个状态一致的 VM。|

4. **创建项目，实现灵活的 VM 自定义**

   项目用于在预配 VM 后部署和配置应用程序。 项目可以是：

   - 想要在 VM 上安装的工具，例如，代理、Fiddler 和 Visual Studio。
   - 想要在 VM 上运行的操作，例如，克隆存储库。
   - 想要测试的应用程序。

   许多项目现成可用。 但如果需要通过执行更多自定义来满足特定需求，可创建自己的自定义项目。

   在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [为开发测试实验室 VM 创建自定义项目](devtest-lab-artifact-author.md) |在实验室中为虚拟机创建自己的自定义项目。|
   | [添加 Git 存储库来存储自定义项目和 Azure 资源管理器模板以供在 Azure 开发测试实验室中使用](devtest-lab-add-artifact-repo.md) |了解如何在自己专用的 Git 存储库中存储自定义项目。|

5. **控制成本**
   
    使用 Azure 开发测试实验室，可以在实验室中设置策略，以指定测试人员可以在实验室中创建的 VM 数量上限。 
   
    如果测试团队已制定工作计划，需要在一天的某个特定时间点停止所有 VM，然后自动在第二天重启它们，可以在实验室中轻松设置自动关闭和自动启动策略。 
   
    最后，完成应用开发后，可通过运行单一 PowerShell 脚本一次性删除所有 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [定义实验室策略](devtest-lab-set-lab-policy.md) |通过在实验室中设置策略来控制成本。 |
   | [使用 PowerShell 脚本删除所有实验室 VM](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |测试完成后，通过一次性操作删除所有实验室。|

1. **将虚拟网络添加到实验室** 
   
    每当创建实验室时，开发测试实验室都会创建新的虚拟网络 (VNET)。 如果已配置自己的 VNET（例如，使用 ExpressRoute 或站点到站点 VPN 配置），可将此 VNET 添加到实验室的虚拟网络设置，使其在创建 VM 时可用。

    此外，还有 Azure Active Directory 域加入项目可用，可在创建 VM 时将 VM 加入域。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中配置虚拟网络](devtest-lab-configure-vnet.md) |了解如何使用 Azure 门户在 Azure 开发测试实验室中配置虚拟网络。|

6. **与每个测试人员共享实验室**
   
    可使用与测试人员共享的链接，直接访问实验室。 测试人员甚至不需要具有 Azure 帐户，只要他们具有 [Microsoft 帐户](devtest-lab-faq.md#what-is-a-microsoft-account)即可。 测试人员看不到其他测试人员创建的 VM。  
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中将测试人员添加到实验室](devtest-lab-add-devtest-user.md) |使用 Azure 门户将测试人员添加到实验室。|
   | [使用 PowerShell 脚本将测试人员添加到实验室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |使用 PowerShell 自动将测试人员添加到实验室。 |
   | [获取实验室的链接](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |了解测试人员如何通过超链接直接访问实验室。|

7. **自动为多个团队创建实验室** 
   
    可通过创建Resource Manager 模板自动化实验室创建（包括自定义设置）并将其用于反复创建相同实验室。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [使用 Resource Manager 模板创建实验室](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |使用资源管理器模板在 Azure 开发测试实验室中创建实验室。 |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

