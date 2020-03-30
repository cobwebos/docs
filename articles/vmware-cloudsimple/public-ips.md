---
title: Azure VMware 解决方案（按云简单 ） - 分配公共 IP 地址
description: 描述如何在私有云环境中为虚拟机分配公共 IP 地址
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024290"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>为私有云环境分配公共 IP 地址

打开"网络"页上的公共 IP 选项卡，为私有云环境中的虚拟机分配公共 IP 地址。

1. [访问云简单门户](access-cloudsimple-portal.md)，并在侧菜单上选择 **"网络**"。
2. 选择**公共 IP**。
3. 单击 **"新的公共 IP**"。

    ![公共 IP 页面](media/public-ips-page.png)

4. 输入名称以标识 IP 地址条目。
5. 保留默认位置。
6. 如果需要，使用滑块更改空闲超时。
7. 输入要为其分配公共 IP 地址的本地 IP 地址。
8. 输入关联的 DNS 名称。
9. 单击 **“提交”**。

![分配公共 IP](media/network-public-ip-allocate.png)

分配公共 IP 地址的任务开始。 您可以在"**活动>任务**"页上检查任务的状态。 分配完成后，新条目将显示在公共 IP 页上。
