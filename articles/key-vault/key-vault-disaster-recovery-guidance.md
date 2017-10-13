---
title: "发生影响 Azure 密钥保管库的 Azure 服务中断时该怎么办 | Microsoft Docs"
description: "了解发生影响 Azure 密钥保管库的 Azure 服务中断时该怎么办。"
services: key-vault
documentationcenter: 
author: adamglick
manager: mbaldwin
editor: 
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: sumedhb;aglick
ms.openlocfilehash: 6419d54c54e7d19103419262b79e7a5268b2268c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure 密钥保管库可用性和冗余
Azure 密钥保管库具有多层冗余功能，确保密钥和机密持续可供应用程序使用，即使服务的单个组件发生故障也是如此。

密钥保管库的内容会在区域中复制，并且会复制到至少 150 英里以外（但位于同一个地理位置）的次要区域。 这可以保持密钥和机密的高持久性。 有关特定区域对的详细信息，请参阅 [Azure 配对区域](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions)一文。

如果密钥保管库服务中的单独组件发生故障，则区域内的替代组件将继续处理请求，确保不会导致功能损失。 不需要采取任何措施即可触发此功能， 这种机制会以透明的方式自动发生。

在整个 Azure 区域不可用的情况下（这很少见），对该区域中的 Azure 密钥保管库发出的请求会自动路由（“故障转移”）到次要区域。 当主要区域再次可用时，请求将路由回（“故障回复”）到主要区域。 同样，不需要采取任何措施，因为这会自动发生。

应注意以下几个事项：

* 发生区域故障转移时，可能需要等待几分钟让服务故障转移。 在此期间发出的请求在故障转移完成之前可能失败。
* 故障转移完成后，密钥保管库处于只读模式。 在此模式下支持的请求包括：
  * 列出密钥保管库
  * 获取密钥保管库的属性
  * 列出机密
  * 获取机密
  * 列出密钥
  * 获取密钥（属性）
  * 加密
  * 解密
  * 包装
  * 解包
  * 验证
  * 签名
  * 备份
* 故障回复之后，所有请求类型（包括读取*和*写入请求）都将可用。

