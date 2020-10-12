---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471486"
---
条件访问允许基于每个应用程序进行细化的访问控制。 若要使用条件访问，应将 Azure AD Premium 1 或更高版本的许可应用于将受条件访问规则约束的用户。

1. 导航到“企业应用程序 - 所有应用程序”页，然后单击“Azure VPN” 。

   - 单击“条件访问”。
   - 单击“新建策略”打开“新建”窗格。 
2. 在“新建”窗格上，导航到“分配”->“用户和组” 。 在“用户和组”->“包括”选项卡上 ：

   - 单击“选择用户和组”。****
   - 选中“用户和组”。
   - 单击“选择”以选择受 MFA 影响的组或用户集。
   - 单击“Done”（完成） 。

   ![分配](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. 在“新建”窗格上，导航到“访问控制”->“授予”窗格 ：

   - 单击“授予访问权限”。
   - 单击“需要多重身份验证”。
   - 单击“需要所有已选控件”。
   - 单击“选择”。
   
   ![授予访问权限 - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. 在“启用策略”部分中：

   - 选择“启用”。
   - 单击“创建”。

   ![启用策略](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)