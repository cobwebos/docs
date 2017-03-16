---
title: "在 Azure VM 上部署 SAP S/4HANA 或 BW/4HANA | Microsoft Docs"
description: "在 Azure VM 上部署 SAP S/4HANA 或 BW/4HANA"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 6f345bb816a3fd6f6fb8672b9a43a0d075bd94eb
ms.openlocfilehash: 939c051cad98590acffb6e550ca45bf5fec90d7e
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>在 Microsoft Azure 上部署 SAP S/4HANA 或 BW/4HANA
本文介绍如何通过 SAP Cloud Appliance Library (SAP CAL) 3.0 在 Microsoft Azure 上部署 S/4HANA。 部署其他基于 SAP HANA 的解决方案（如 BW/4HANA）的方式在过程上是相同的， 只是选择的解决方案不同而已。

> [!NOTE]
有关 SAP Cloud Appliance Library 的详细信息，请参阅[其站点的主页](https://cal.sap.com/)。 SAP 还提供了有关 [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) 的博客。

## <a name="step-by-step-process-to-deploy-the-solution"></a>部署解决方案的分步过程

以下屏幕截图显示如何在 Azure 上部署 S/4HANA。 其他解决方案（如 BW/4HANA）的部署过程相同。

第一张屏幕截图显示 Azure 上所有基于 SAP CAL HANA 的可用解决方案。 请注意底部的 **SAP S/4HANA 本地版本**。

![SAP Cloud Appliance Library“解决方案”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

首先创建一个新的 SAP CAL 帐户。 在“帐户”中，有两个 Azure 选项：Microsoft Azure 和 21Vianet 运营的 Azure 选项。 在本示例中，请选择“Microsoft Azure”。

![SAP Cloud Appliance Library“帐户”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

接着，输入可在 Azure 门户中找到的 Azure 订阅 ID。 然后下载 Azure 管理证书。

![SAP Cloud Appliance Library“帐户”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
若要查找 Azure 订阅 ID，应使用 Azure 经典门户，而不是较新的 Azure 门户。 这是因为 SAP CAL 尚未针对此新模型进行改编，仍需要在经典门户中使用管理证书。

下面的屏幕截图显示经典门户。 从“设置”中选择“订阅”选项卡，找到要在 SAP CAL“帐户”窗口中输入的订阅 ID。

![Azure 经典门户屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

从“设置”中，切换到“管理证书”选项卡。 上传管理证书后，SAP CAL 便有权在客户订阅中创建虚拟机。 （所要上传的是之前从 SAP CAL 下载的管理证书。）

![Azure 经典门户“管理证书”选项卡屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

此时会弹出一个对话框用于选择下载的证书文件。

![“上传管理证书”对话框屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

上传证书后，可在 SAP CAL 中测试 SAP CAL 与 Azure 订阅之间的连接。 此时应弹出一条消息，指示连接有效。

![SAP Cloud Appliance Library“帐户”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

接下来，选择要部署的解决方案并创建实例。
输入实例名称，选择 Azure 区域，定义解决方案的主要密码。

![SAP Cloud Appliance Library“解决方案”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

一段时间后，根据解决方案的大小和复杂性（SAP CAL 的估算值），解决方案将显示为“活动”并可供使用。

![SAP Cloud Appliance Library“实例”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

可以看到解决方案的一些详细信息，例如已部署的 VM 种类。 在本例中，已创建三个具有不同大小和用途的 Azure VM。

![SAP Cloud Appliance Library“实例”窗口屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

在 Azure 经典门户中，可以找到这些以 SAP CAL 中所提供的相同实例名称开头的虚拟机。

![在 Azure 经典门户中显示三个虚拟机的屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

现在，可以通过 SAP CAL 门户中的连接按钮连接到解决方案。 该对话框包含用户指南链接，其中描述了使用解决方案时所需的全部默认凭据。

![“连接到实例”对话框屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

另一种做法是登录到客户端 Windows VM，然后启动预配置的 SAP GUI 或其他界面。

![预配置 SAP GUI 屏幕截图](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

