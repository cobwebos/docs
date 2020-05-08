---
title: Azure Key Vault 将保管库移动到不同的资源组 |Microsoft Docs
description: 有关将密钥保管库移动到不同资源组的指南。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: bbc27af9eb448911093473d6ab20fde8004c7b88
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783716"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>跨资源组移动 Azure Key Vault

## <a name="overview"></a>概述

跨资源组移动密钥保管库是一项受支持的密钥保管库功能。 在资源组之间移动 key vault 不会影响 key vault 防火墙或访问策略配置。 连接的应用程序和服务主体应继续按预期方式工作。

## <a name="design-considerations"></a>设计注意事项

你的组织可能已在资源组级别实施了强制或排除的 Azure 策略。 在资源组中，可能会有一组不同的策略分配，即密钥保管库当前所在的资源组和要将密钥保管库移动到的资源组。 策略要求冲突可能会破坏应用程序。

### <a name="example"></a>示例

已将应用程序连接到密钥保管库，该密钥保管库创建的证书有效期为两年。 尝试移动密钥保管库的资源组有一个策略分配，阻止创建有效期超过一年的证书。 将密钥保管库移动到新的资源组后，创建有效证书的操作将被 Azure 策略分配阻止。

### <a name="solution"></a>解决方案

请确保转到 Azure 门户上的 "Azure 策略" 页，并查看当前资源组的策略分配以及要移到的资源组，并确保不存在不匹配的情况。

## <a name="procedure"></a>过程

1. 登录到 Azure 门户
2. 导航到密钥保管库
3. 单击 "概述" 选项卡
4. 选择 "移动" 按钮
5. 从下拉选项中选择 "移动到另一个资源组"
6. 选择要在其中移动密钥保管库的资源组
7. 确认有关移动资源的警告
8. 选择“确定”

Key Vault 现在将评估资源移动的有效性，并向您发出任何错误的警报。 如果未找到任何错误，将完成资源移动。 
