---
title: 使用 Azure 自动化垂直缩放 Windows 虚拟机 | Microsoft Docs
description: 使用 Azure 自动化垂直缩放 Windows 虚拟机以响应监视警报
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: abbbcce2ae72ac77ba6f6c21e626b34bcded7909
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>使用 Azure 自动化垂直缩放 Windows VM

垂直缩放是增加或减少计算机的资源以响应工作负荷的过程。 在 Azure 中，这可以通过更改虚拟机的大小来实现。 这种做法可为以下方案提供方便

* 如果不经常使用某个虚拟机，可以缩小其大小，以降低每月成本
* 如果虚拟机将来会出现负载高峰，可以增大其大小，以提高其容量

下面概述了实现此目的的步骤

1. 将 Azure 自动化设置为访问虚拟机
2. 将 Azure 自动化垂直缩放 Runbook 导入到订阅中
3. 将 Webhook 添加到 Runbook
4. 添加针对虚拟机的警报

> [!NOTE]
> 由于第一个虚拟机的大小有限制，它可以调整到的大小可能会由于当前虚拟机部署到的群集的其他大小而受到限制。 在本文中使用的已发布自动化 Runbook 中，我们将遵循这种限制，只在以下 VM 大小对的范围内进行缩放。 这意味着，Standard_D1v2 虚拟机不会突然间扩展到 Standard_G5，或者突然间缩减到 Basic_A0。
> 
> | VM 大小缩放对 |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1v2 |Standard_D5v2 |
> | Standard_D11v2 |Standard_D14v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>将 Azure 自动化设置为访问虚拟机
要做的第一件事就是创建一个 Azure 自动化帐户来托管用于缩放虚拟机的 Runbook。 最近，自动化服务引入了“运行方式帐户”功能，使用该功能可以很轻松地代表用户设置服务主体来自动运行 Runbook。 可以在以下文章中阅读有关此功能的详细信息：

* [Authenticate Runbooks with Azure Run As account（使用 Azure 运行方式帐户进行 Runbook 身份验证）](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>将 Azure 自动化垂直缩放 Runbook 导入到订阅中
垂直缩放虚拟机所需的 Runbook 已在 Azure 自动化 Runbook 库中发布。 需要将其导入订阅。 可以阅读以下文章来了解如何导入 Runbook。

* [Azure 自动化的 Runbook 和模块库](../../automation/automation-runbook-gallery.md)

下图显示了需要导入的 Runbook

![导入 Runbook](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>将 Webhook 添加到 Runbook
导入 Runbook 后，需在 Runbook 中添加一个 Webhook，以便虚拟机发出的警报可以触发 Runbook。 可在此处阅读有关如何为 Runbook 创建 Webhook 的详细信息

* [Azure 自动化 Webhook](../../automation/automation-webhooks.md)

在关闭 Webhook 对话框之前，请务必复制 Webhook，因为在下一部分将要用到它。

## <a name="add-an-alert-to-your-virtual-machine"></a>添加针对虚拟机的警报
1. 选择“虚拟机设置”
2. 选择“警报规则”
3. 选择“添加警报”
4. 选择用于触发警报的指标
5. 选择一个条件，满足此条件时会触发该警报
6. 为步骤 5 中要满足的条件 选择一个阈值
7. 选择一个时间段，超过该时间段时，监视服务将检查步骤 5 和 6 中的条件与阈值
8. 粘贴你在上一部分复制的 Webhook。

![将警报添加到虚拟机 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![将警报添加到虚拟机 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

