<properties
	pageTitle="在 Azure 开发测试实验室中创建实验室 | Microsoft Azure"
	description="在 Azure 开发测试实验室中创建适用于虚拟机的实验室"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# 在 Azure 开发测试实验室中创建实验室

## 先决条件

若要创建实验室，需要：

- Azure 订阅。若要了解 Azure 购买选项，请参阅[如何购买 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。必须成为订阅所有者才可以创建实验室。

## 在 Azure 开发测试实验室中创建实验室的步骤

以下步骤演示了如何使用 Azure 门户在 Azure 开发测试实验室中创建实验室。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，然后从列表中选择“开发测试实验室”。

1. 在“开发测试实验室”边栏选项卡上，选择“添加”。

    ![添加实验室](./media/devtest-lab-create-lab/add-lab-button.png)

1. 在“创建 开发测试实验室”边栏选项卡上：

    1. 为新的实验室输入“实验室名称”。
    
	1. 选择要与实验室关联的“订阅”。
    
	1. 选择用于存储实验室的“位置”。
    
	1. 选择“自动关机”，指定是否要自动关闭所有实验室的 VM 以及为其指定参数。
	
	1. 选择“存储类型”，指示适用于实验室的 VM 的存储磁盘类型。
    
	1. 选择“创建”。

    ![创建实验室边栏选项卡](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 后续步骤

实验室创建完成后，以下是一些可以考虑执行的后续步骤：

- [保护对实验室进行的访问](devtest-lab-add-devtest-user.md)。

- [设置实验室策略](devtest-lab-set-lab-policy.md)。

- [创建实验室模板](devtest-lab-create-template.md)。

- [创建适用于 VM 的自定义项目](devtest-lab-artifact-author.md)。

- [将含有项目的 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0921_2016-->