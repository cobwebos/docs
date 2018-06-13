---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d4075f644ed1ce42479e35544f0e1442488f78eb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
ms.locfileid: "29958905"
---
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 首先在左上角单击“创建资源” > “计算” > “Windows Server 2016 Datacenter”。

    ![在门户中导航到 Azure VM 映像](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. 在 Windows Server 2016 Datacenter 上，选择经典部署模型。 单击“创建”。

    ![显示在门户中可用的 Azure VM 映像的屏幕截图](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1.“基本信息”边栏选项卡

“基本信息”边栏选项卡请求虚拟机的管理信息。

1. 输入虚拟机的“名称”。 在本示例中，HeroVM 是虚拟机的名称。 名称必须为 1-15 个字符，不能包含特殊字符。

2. 输入用于在 VM 上创建本地帐户的“用户名”和强“密码”。 该本地帐户用于登录和管理 VM。 在本示例中，azureuser 是用户名。

 密码长度必须为 8 到 123 个字符，并且必须满足以下 4 个复杂性要求的其中 3 个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。 查看更多关于 [用户名和密码要求](../articles/virtual-machines/windows/faq.md)。

3. “订阅”可选。 常用的一个设置是“即用即付”。

4. 选择现有的**资源组**，或键入新资源组的名称。 在本示例中，HeroVMRG 是资源组的名称。

5. 选择想要在其中运行 VM 的 Azure 数据中心“位置”。 在本示例中，位置为“美国东部”。

6. 完成后，单击“下一步”转到下一个边栏选项卡。

    ![显示用于配置 Azure VM 的“基本信息”边栏选项卡上的设置的屏幕截图](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2.“大小”边栏选项卡

“大小”边栏选项卡标识 VM 的配置详情，并列出操作系统、处理器数量、磁盘存储类型和每月估计使用成本等各种选项。  

选择 VM 大小，并单击“选择”以继续。 在此示例中，_DS1_\__V2 标准_是 VM 的大小。

  ![显示可以选择的 Azure VM 大小的“大小”边栏选项卡的屏幕截图](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3.设置边栏选项卡

“设置”边栏选项卡请求存储和网络选项。 可接受默认设置。 必要时，Azure 会创建相应的条目。

如果选择了支持它的虚拟机大小，则可通过在“磁盘类型”中选择“高级(SSD)”来试用 Azure 高级存储。

完成更改后，单击“ **确定**”。

## <a name="4-summary-blade"></a>4.“摘要”边栏选项卡

“摘要”边栏选项卡列出在之前的边栏选项卡中指定的设置。 准备好制作映像时，请单击“确定”。

 ![列出虚拟机的指定设置的“摘要”边栏选项卡报表](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

创建虚拟机后，门户会在“所有资源”下列出新的虚拟机，并在仪表板上显示虚拟机的磁贴。 此外，还会创建和列出相应的云服务和存储帐户。 虚拟机和云服务都会自动启动，其状态会显示为“正在运行”。

 ![配置虚拟机的 VM 代理和终结点](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
