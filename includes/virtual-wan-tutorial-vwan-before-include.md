---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896612"
---
在开始配置之前，请验证是否满足以下条件：

* 如果已有要连接到的虚拟网络，请验证本地网络的任何子网是否与该网络重叠。 虚拟网络不需要网关子网，并且不能有任何虚拟网络网关。 如果没有虚拟网络，则可以使用本文中的步骤创建虚拟网络。
* 获取中心区域的 IP 地址范围。 中心是虚拟网络，您为中心区域指定的地址范围不能与连接到的现有虚拟网络重叠。 它还不能与连接到本地的地址范围重叠。 如果您不熟悉本地网络配置中的 IP 地址范围，请与可以为您提供这些详细信息的人员进行协调。
* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。