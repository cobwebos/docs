---
title: Azure Key Vault 将保管库移到不同的资源组 | Microsoft Docs
description: 有关将密钥保管库移到不同资源组的指南。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: ea152e1d78ab1ea610eee5420394c89524673993
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042376"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>在资源组之间移动 Azure Key Vault

## <a name="overview"></a>概述

在资源组之间移动密钥保管库是受支持的密钥保管库功能。 在资源组之间移动密钥保管库不会影响密钥保管库防火墙或访问策略配置。 已连接的应用程序和服务主体应该会继续按预期工作。

> [!IMPORTANT]
> **无法移动用于磁盘加密的密钥保管库。**
> 如果你使用的是 VM 的磁盘加密的密钥保管库，则在启用磁盘加密时，无法将密钥保管库移到其他资源组或订阅。 将密钥保管库移动到新的资源组或订阅之前，必须先禁用磁盘加密。 

## <a name="design-considerations"></a>设计注意事项

你的组织可能已通过资源组级别的强制实施或排除实施了 Azure 策略。 在密钥保管库当前所在的资源组与要将密钥保管库移到的资源组之间，可能存在一组不同的策略分配。 如果策略要求冲突，可能会破坏应用程序。

### <a name="example"></a>示例

你有一个连接到密钥保管库的应用程序，该密钥保管库创建的证书有效期为两年。 你要尝试将密钥保管库移到其中的资源组有一个策略分配，该策略分配阻止创建有效期超过一年的证书。 将密钥保管库移到新资源组后，创建有效期为两年的证书的操作会被 Azure 策略分配阻止。

### <a name="solution"></a>解决方案

请确保转到 Azure 门户上的“Azure Policy”页，查看当前资源组的策略分配以及要移到其中的资源组的策略分配，并确保没有不匹配项。

## <a name="procedure"></a>过程

1. 登录到 Azure 门户
2. 导航到你的密钥保管库
3. 单击“概览”选项卡
4. 选择“移动”按钮
5. 从下拉选项中选择“移到另一个资源组”
6. 选择要将密钥保管库移动到其中的资源组
7. 确认有关移动资源的警告
8. 选择“确定”

Key Vault 现在将评估资源移动的有效性，并提醒你存在错误。 如果没有发现错误，就会完成资源移动。 
