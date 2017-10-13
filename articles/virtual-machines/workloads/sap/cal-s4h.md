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
ms.openlocfilehash: 4788fa14a6c49d39b5a3096a69b6738f4a5d8cca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>在 Azure 上部署 SAP S/4HANA 或 BW/4HANA
本文介绍如何通过 SAP Cloud Appliance Library (SAP CAL) 3.0 在 Azure 上部署 S/4HANA。 若要部署其他基于 SAP HANA 的解决方案（如 BW/4HANA），请按照相同的步骤执行。

> [!NOTE]
有关 SAP CAL 的详细信息，请转到 [SAP Cloud Appliance Library](https://cal.sap.com/) 网站。 SAP 还提供了有关 [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) 的博客。

> [!NOTE]
自 2017 年 5 月 29 日起，除了优先级较低的经典部署模型外，还可使用 Azure Resource Manager 部署模型来部署 SAP CAL。 建议使用新的 Resource Manager 部署模型，并忽略经典部署模型。

## <a name="step-by-step-process-to-deploy-the-solution"></a>部署解决方案的分步过程

下面的屏幕截图序列介绍了如何使用 SAP CAL 在 Azure 上部署 S/4HANA。 其他解决方案（如 BW/4HANA）的部署过程相同。

“解决方案”页显示 Azure 上某些基于 SAP CAL HANA 的可用解决方案。 SAP S/4HANA 1610 FPS01，完全激活的设备在中间行：

![SAP CAL 解决方案](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>在 SAP CAL 中创建帐户
1. 首次登录 SAP CAL，请使用 SAP S-User 或 SAP 中注册的其他用户。 然后定义 SAP CAL 用于在 Azure 上部署设备的 SAP CAL 帐户。 在帐户定义中，需要：

    a. 选择 Azure 上的部署模型（Resource Manager 或经典）。

    b. 输入 Azure 订阅。 一个 SAP CAL 帐户仅可分配到一个订阅。 如果需要多个订阅，则需要另外创建 SAP CAL 帐户。

    c. 授予 SAP CAL 权限，以便部署到 Azure 订阅中。

    > [!NOTE]
    后续步骤演示如何创建 Resource Manager 部署的 SAP CAL 帐户。 如果已有链接到经典部署模型的 SAP CAL 帐户，则需要按照这些步骤创建新的 SAP CAL 帐户。 需要在 Resource Manager 模型中部署新的 SAP CAL 帐户。

2. 创建一个新的 SAP CAL 帐户。 “帐户”页显示 Azure 的三个选项： 

    a. Microsoft Azure（经典）是经典部署模型，已不再是首选项。

    b. Microsoft Azure 是新的 Resource Manager 部署模型。

    c. 21Vianet 运营的 Windows Azure 是在中国使用经典部署模型的一个选项。

    若要部署在 Resource Manager 模型中，请选择“Microsoft Azure”。

    ![SAP CAL 帐户详细信息](./media/cal-s4h/s4h-pic-2a.png)

3. 输入可在 Azure 门户中找到的 Azure 订阅 ID。

   ![SAP CAL 帐户](./media/cal-s4h/s4h-pic3c.png)

4. 若要授权将 SAP CAL 部署到定义的 Azure 订阅，请单击“授权”。 浏览器选项卡中将显示以下页面：

   ![Internet Explorer 云服务登录](./media/cal-s4h/s4h-pic4c.png)

5. 如果列出多个用户，则选择链接到所选择的 Azure 订阅共同管理者的 Microsoft 帐户。 浏览器选项卡中将显示以下页面：

   ![Internet Explorer 云服务确认](./media/cal-s4h/s4h-pic5a.png)

6. 单击“接受”。 如果授权成功，则将再次显示 SAP CAL 帐户定义。 稍后，用户将收到一条消息，确认授权过程成功。

7. 若要将新创建的 SAP CAL 帐户分配到你的用户，请在右侧的文本框中输入你的用户 ID 并单击“添加”。

   ![帐户到用户的关联](./media/cal-s4h/s4h-pic8a.png)

8. 若要将你的帐户与你用于登录到 SAP CAL 的用户相关联，请单击“评审”。 
 
9. 若要创建用户和新建的 SAP CAL 帐户之间的关联，请单击“创建”。

   ![用户到 SAP CAL 帐户的关联](./media/cal-s4h/s4h-pic9b.png)

已成功创建 SAP CAL 帐户，该帐户能够：

- 使用 Resource Manager 部署模型。
- 将 SAP 系统部署到 Azure 订阅。

现在可以开始将 S/4HANA 部署到 Azure 中的用户订阅。

> [!NOTE]
在继续之前，请确定是否具有用于 Azure H 系列 VM 的 Azure 核心配额。 目前，SAP CAL 使用 Azure H 系列 VM 部署某些基于 SAP HANA 的解决方案。 你的 Azure 订阅中可能没有任何用于 H 系列的 H 系列核心配额。 如果是这样，则可能需要联系 Azure 支持部门以获取至少 16 个 H 系列核心配额。

> [!NOTE]
在 SAP CAL 部署 Azure 上的解决方案时，可能只能选择一个 Azure 区域。 若要部署到除 SAP CAL 建议的区域之外的其他 Azure 区域，则需要从 SAP 购买 CAL 订阅。 你可能还需要通过 SAP 打开一条消息，使你的 CAL 帐户能够发送至最初建议的区域之外的其他 Azure 区域。

### <a name="deploy-a-solution"></a>部署解决方案

从 SAP CAL 的“解决方案”页部署解决方案。 SAP CAL 有两个序列要部署：

- 用一页定义要部署的系统的基本序列
- 在 VM 大小上可供选择的高级序列 

我们在此演示部署的基本路径。

1. 在“帐户详细信息”页上，需要执行以下操作：

    a. 选择 SAP CAL 帐户。 （使用通过 Resource Manager 部署模型部署时关联的帐户。）

    b. 输入实例“名称”。

    c. 选择 Azure“区域”。 SAP CAL 建议一个区域。 如果需要另一个 Azure 区域并且没有 SAP CAL 订阅，则需要通过 SAP 订购 CAL 订阅。

    d. 输入一个用于解决方案的八个或九个字符的主密码。 密码用于不同组件的管理员。

   ![SAP CAL 基本模式：创建实例](./media/cal-s4h/s4h-pic10a.png)

2. 单击“创建”，然后在出现的消息框中单击“确定”。

   ![SAP CAL 支持的 VM 大小](./media/cal-s4h/s4h-pic10b.png)

3. 在“私钥”对话框中，单击“存储”将私钥存储在 SAP CAL 中。 若要使用私钥密码保护，请单击“下载”。 

   ![SAP CAL 私钥](./media/cal-s4h/s4h-pic10c.png)

4. 阅读 SAP CAL“警告”消息，并单击“确定”。

   ![SAP CAL 警告](./media/cal-s4h/s4h-pic10d.png)

    现在正在进行部署。 一段时间后，根据解决方案的大小和复杂性（SAP CAL 提供的估算值），状态将显示为活动并可供使用。

5. 若要查找一个资源组中使用其他相关资源收集的虚拟机，请转到 Azure 门户： 

   ![新门户中部署的 SAP CAL 对象](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. 在 SAP CAL 门户上，状态将显示为“活动”。 若要连接到解决方案，请单击“连接”。 在此解决方案内部署需连接到不同组件的不同选项。

   ![SAP CAL 实例](./media/cal-s4h/active_solution.png)

7. 在能够使用其中一个方法连接到已部署的系统之前，请单击“入门指南”。 

   ![连接到实例](./media/cal-s4h/connect_to_solution.png)

    此文档将为用户的每个连接方法命名。 用户密码设置为部署过程开始时定义的主密码。 文档中列出了可用于登录到已部署系统的其他更多功能用户和其密码。 

    例如，如果使用 Windows 远程桌面计算机上已预装的 SAP GUI，S/4 系统可能如下所示：

   ![预安装的 SAP GUI 中的 SM50](./media/cal-s4h/gui_sm50.png)

    或者，如果使用 DBACockpit，实例可能如下所示：

   ![在 DBACockpit SAP GUI 中的 SM50](./media/cal-s4h/dbacockpit.png)

几个小时内即可在 Azure 中部署一个完善的 SAP S/4 设备。

如果购买了 SAP CAL 订阅，则 SAP 完全支持通过 Azure 上的 SAP CAL 进行部署。 支持队列是 BC-VCM-CAL。







