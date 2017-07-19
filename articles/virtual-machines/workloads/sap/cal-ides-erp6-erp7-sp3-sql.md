---
title: "在 Azure 上为 SAP ERP 6.0 部署 SAP IDES EHP7 SP3 | Microsoft Docs"
description: "在 Azure 上为 SAP ERP 6.0 部署 SAP IDES EHP7 SP3"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017


---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>在 Azure 上为 SAP ERP 6.0 部署 SAP IDES EHP7 SP3
本文介绍如何通过 SAP Cloud Appliance Library (SAP CAL) 3.0 在 Azure 上部署与 SQL Server 和 Windows 操作系统一起运行的 SAP IDES 系统。 屏幕截图将逐步显示此过程。 若要部署另一种解决方案，请按照相同的步骤操作。

若要开始使用 SAP CAL，请转到 [SAP Cloud Appliance Library](https://cal.sap.com/)网站。 有关新版 [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)，SAP 提供了博客介绍。 

> [!NOTE]
自 2017 年 5 月 29 日起，除了优先级较低的经典部署模型外，还可使用 Azure Resource Manager 部署模型来部署 SAP CAL。 建议使用新的 Resource Manager 部署模型，并忽略经典部署模型。

如果已创建使用经典模型的 SAP CAL 帐户，则需要创建另一个 SAP CAL 帐户。 需使用 Resource Manager 模型以独占方式将此帐户部署到 Azure。

登录到 SAP CAL 后，首页通常会引导用户转至“解决方案”页。 SAP CAL 上提供的解决方案稳步增加，因此可能需要滚动查看大量内容才能找到所需解决方案。 Azure 上专供的基于 Windows 的 SAP IDES 解决方案（突出显示）演示了部署过程：

![SAP CAL 解决方案](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>在 SAP CAL 中创建帐户
1. 首次登录 SAP CAL，请使用 SAP S-User 或 SAP 中注册的其他用户。 然后定义 SAP CAL 用于在 Azure 上部署设备的 SAP CAL 帐户。 在帐户定义中，需要：

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 选择 Azure 上的部署模型（Resource Manager 或经典）。

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 输入 Azure 订阅。 一个 SAP CAL 帐户仅可分配到一个订阅。 如果需要多个订阅，则需要另外创建 SAP CAL 帐户。
    
    c. 授予 SAP CAL 权限，以便部署到 Azure 订阅中。

    > [!NOTE]
    后续步骤演示如何创建 Resource Manager 部署的 SAP CAL 帐户。 如果已有链接到经典部署模型的 SAP CAL 帐户，则需要按照这些步骤创建新的 SAP CAL 帐户。 需要在 Resource Manager 模型中部署新的 SAP CAL 帐户。

2. 若要创建新的 SAP CAL 帐户，“帐户”页显示 Azure 的两个选项： 

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 Microsoft Azure（经典）是经典部署模型，已不再是首选项。

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 Microsoft Azure 是新的 Resource Manager 部署模型。

    ![SAP CAL 帐户](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    若要部署在 Resource Manager 模型中，请选择“Microsoft Azure”。

    ![SAP CAL 帐户](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. 输入可在 Azure 门户中找到的 Azure 订阅 ID。 

    ![SAP CAL 订阅 ID](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. 若要授权将 SAP CAL 部署到定义的 Azure 订阅，请单击“授权”。 浏览器选项卡中将显示以下页面：

    ![Internet Explorer 云服务登录](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. 如果列出多个用户，则选择链接到所选择的 Azure 订阅共同管理者的 Microsoft 帐户。 浏览器选项卡中将显示以下页面：

    ![Internet Explorer 云服务确认](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. 单击“接受”。 如果授权成功，则将再次显示 SAP CAL 帐户定义。 稍后，用户将收到一条消息，确认授权过程成功。

7. 若要将新创建的 SAP CAL 帐户分配到你的用户，请在右侧的文本框中输入你的用户 ID 并单击“添加”。 

    ![帐户到用户的关联](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. 若要将你的帐户与你用于登录到 SAP CAL 的用户相关联，请单击“评审”。 

9. 若要创建用户和新建的 SAP CAL 帐户之间的关联，请单击“创建”。

    ![用户到帐户的关联](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

已成功创建 SAP CAL 帐户，该帐户能够：

- 使用 Resource Manager 部署模型。
- 将 SAP 系统部署到 Azure 订阅。

> [!NOTE]
需要先注册 SAP CAL 订阅，才能部署基于 Windows 和 SQL Server 的 SAP IDES 解决方案。 否则，该解决方案可能在“概述”页上显示为“锁定”。

### <a name="deploy-a-solution"></a>部署解决方案
1. 设置 SAP CAL 帐户后，选择“Windows 和 SQL Server 上的 SAP IDES 解决方案”解决方案。 单击“创建实例”，并确认使用情况以及条款和条件。 

2. 在“基本模式：创建实例”页上，需要：

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 输入实例“名称”。

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 选择 Azure“区域”。 可能需要 SAP CAL 订阅才能获取提供的多个 Azure 区域。

    c.  输入解决方案的主“密码”，如下所示：

    ![SAP CAL 基本模式：创建实例](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. 单击“创建” 。 一段时间后，根据解决方案的大小和复杂性（SAP CAL 提供估算值），状态将显示为“活动”并可供使用： 

    ![SAP CAL 实例](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. 若要查找由 SAP CAL 创建的资源组及其所有对象，请转到 Azure 门户。 可以找到以 SAP CAL 中提供的相同实例名称开头的虚拟机。

    ![资源组对象](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. 在 SAP CAL 门户中，转到已部署的实例，然后单击“连接”。 将出现以下弹出窗口： 

    ![连接到实例](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. 在能够使用其中一个方法连接到已部署的系统之前，请单击“入门指南”。 此文档将为用户的每个连接方法命名。 用户密码设置为部署过程开始时定义的主密码。 文档中列出了可用于登录到已部署系统的其他更多功能用户和其密码。

    ![SAP 欢迎文档](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

几个小时内即可在 Azure 中部署一个完善的 SAP IDES 系统。

如果购买了 SAP CAL 订阅，则 SAP 完全支持通过 Azure 上的 SAP CAL 进行部署。 支持队列是 BC-VCM-CAL。


