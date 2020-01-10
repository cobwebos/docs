---
title: 使用门户将旧直接对等互连转换为 Azure 资源
titleSuffix: Azure
description: 使用门户将旧直接对等互连转换为 Azure 资源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775051"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>使用门户将旧直接对等互连转换为 Azure 资源

本文介绍如何使用门户将现有的旧式直接对等互连转换为 Azure 资源。

如果愿意，可以使用[PowerShell](howto-legacy-direct-powershell.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 开始配置之前，请查看[先决条件](prerequisites.md)和[直接对等互连演练](walkthrough-direct-all.md)。


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>将旧直接对等互连转换为 Azure 资源

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登录到门户并选择你的订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>转换旧直接对等互连

可以使用对**等互连**资源转换旧的对等互连连接。

#### <a name="launch-resource-and-configure-basic-settings"></a>启动资源并配置基本设置
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>配置连接并提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>验证直接对等互连
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[Internet 对等互连常见问题解答](faqs.md)

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)。
