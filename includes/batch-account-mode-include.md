---
title: include 文件
description: include 文件
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38942546"
---
> [!NOTE]
> 创建 Batch 帐户时，可在两种“池分配”模式间进行选择：“用户订阅”和“Batch 服务”。 在大部分情况下，应使用默认的 Batch 服务模式，使用此模式时，池在 Azure 托管的订阅中以幕后方式分配。 在备用的“用户订阅”模式下，会在创建池后直接在订阅中创建 Batch VM 和其他资源。 如果想用 [Azure 虚拟机预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)创建 Batch 池，则需要使用“用户订阅”模式。 若要在用户订阅模式下创建 Batch 帐户，还需将订阅注册到 Azure Batch 中，并将该帐户与 Azure Key Vault 相关联。