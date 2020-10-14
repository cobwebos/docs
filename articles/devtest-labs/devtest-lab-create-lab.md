---
title: 在 Azure 开发测试实验室中创建实验室 | Microsoft Docs
description: 本文将指导你完成使用 Azure 门户和 Azure 开发测试实验室创建实验室的过程。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058303"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室

Azure 开发测试实验室中的实验室是包含一组资源（例如，虚拟机 (VM)）的基础结构，通过指定限制和配额可以更好地管理这些资源。 本文介绍使用 Azure 门户创建实验室的过程。

## <a name="prerequisites"></a>先决条件

若要创建实验室，需要：

* Azure 订阅。 若要了解 Azure 购买选项，请参阅[如何购买 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。 必须成为订阅所有者才可以创建实验室。

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Azure 开发测试实验室快速入门

通过单击以下链接，你将转到 "Azure 门户" 页面，该页面允许你开始在 Azure 开发测试实验室中创建新的实验室。

[Azure 开发测试实验室快速入门](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>填写新帐户的设置

在 " **创建开发测试实验室** " 页上，填写以下设置。

> [!TIP]
> 在每个页面的底部，你会看到一个链接，可用于 **下载自动化的模板**。

### <a name="basic-settings"></a>基本设置

默认情况下，你会看到 " **基本设置** " 选项卡。 

![基本设置](./media/devtest-lab-create-lab/basic-settings.png)

填写以下值：

|名称|说明|
|---|---|
|**订阅** | 必需。 选择要与实验室关联的“订阅” **** 。|
|**资源组**| 必需。 为实验室输入**资源组的名称**。 如果不存在，则创建一个新的。|
|**实验室名称**| 必需。 输入实验室的 **名称** 。|
|**位置**|必需。 选择用于存储实验室的位置。|
|**公共环境**| 请参阅 [配置和使用公共环境](devtest-lab-configure-use-public-environments.md)。

### <a name="auto-shutdown-settings"></a>自动关闭设置

切换到 **自动关闭** 页面，查看其设置。 使用自动关闭，可以在每天的计划时间内自动关闭实验室中的所有计算机。

![自动关闭选项卡](./media/devtest-lab-create-lab/auto-shutdown.png)

在页面上，可以启用 **自动关闭** ，并为所有实验室的 vm 自动关闭定义参数。 自动关机功能主要是一种节省成本的功能，可以根据需要通过该功能指定何时自动关闭 VM。 可以在创建实验室之后更改自动关机设置，只需按[管理 Azure 开发测试实验室中某个实验室的所有策略](./devtest-lab-set-lab-policy.md#set-auto-shutdown)一文所述步骤操作即可。

### <a name="networking"></a>网络

创建实验室时，将为您创建一个默认网络。 切换到 " **网络** " 选项卡以更改/配置所需设置。 例如，选择现有虚拟网络。

![“网络”选项卡 ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Tags

如果要创建自定义标记（将添加到要在实验室中创建的每个资源上），请输入**标记**的**名称**和**值**信息。 标记用于帮助你按类别管理和组织实验室资源。 有关标记的详细信息（包括如何在创建实验室中后添加标记），请参阅[将标记添加到实验室](devtest-lab-add-tag.md)。

![标记选项卡 ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>查看 + 创建

完成后，选择 " **创建**"。 可以通过在门户页的右上角查看 " **通知** " 区域来监视实验室创建过程的状态。 

![创建选项卡](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>已完成创建

完成后，会在页面底部和通知窗口中显示 " **中转到资源** " 按钮。 或者，刷新“开发测试实验室”**** 页面以在实验室列表中查看新创建的实验室。  

![创建服务](./media/devtest-lab-create-lab/create-2.png)

按 " **转到资源** " 按钮，你将转到新的开发测试实验室帐户的主页。

![资源](./media/devtest-lab-create-lab/go-to-resource.png)

你还可以在 Azure 门户中搜索 **开发测试实验室** 。 从列表中选择新帐户，并转到主页。 

![已创建服务](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>后续步骤

实验室创建完成后，以下是一些可以考虑执行的后续步骤：

* [保护对实验室的访问](devtest-lab-add-devtest-user.md)
* [设置实验室策略](devtest-lab-set-lab-policy.md)
* [创建实验室模板](devtest-lab-create-template.md)
* [为 Vm 创建自定义项目](devtest-lab-artifact-author.md)
* [将 VM 添加到实验室](devtest-lab-add-vm.md)

