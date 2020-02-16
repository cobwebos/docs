---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 0177f1ffbc21cf5508b86a9c0f3c625fead34faf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170365"
---
|名称 |说明 |作用 |版本 |
|---|---|---|---|
|[Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric 使用主要群集证书为节点之间的通信提供三个保护级别（None、Sign 和 EncryptAndSign）。 设置保护级别以确保所有节点到节点消息均已进行加密和数字签名 |Audit, 已禁用 |1.0.0 |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit, 已禁用 |1.0.0 |
