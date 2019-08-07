---
title: Azure 安全中心中的补救建议 |Microsoft Docs
description: 本文档介绍如何修正 Azure 安全中心中的建议, 以帮助你保护 Azure 资源并保持符合安全策略。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778999"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure 安全中心的补救建议

建议提供有关如何更好地保护资源的建议。  按照建议中提供的补救步骤来实施建议。 

## 修正步骤<a name="remediation-steps"></a>

查看所有建议后, 首先确定要修正的建议。 建议你使用[安全分数影响](security-center-recommendations.md#monitor-recommendations)来帮助确定首先要执行的操作。

1. 从列表中单击建议。
1. 按照“修正步骤”部分中的说明进行操作。 每个建议都有其自己的一组指令。 下面显示了将应用程序配置为仅允许通过 HTTPS 的流量的补救步骤。

    ![建议详细信息](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成后, 将显示一条通知, 告知你更新是否成功。

## <a name="next-steps"></a>后续步骤

本文档演示了如何在安全中心修正建议。 若要了解有关安全中心的详细信息, 请参阅以下主题:

* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
