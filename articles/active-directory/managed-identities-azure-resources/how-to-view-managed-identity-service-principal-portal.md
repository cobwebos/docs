---
title: 在 Azure 门户中查看托管标识的服务主体 - Azure AD
description: 在 Azure 门户中查看托管标识的服务主体的分步说明。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611922"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>在 Azure 门户中查看托管标识的服务主体

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何在 Azure 门户中查看托管标识的服务主体。

 > [!NOTE] 
 > 服务主体是企业应用程序。 

## <a name="prerequisites"></a>必备条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。
- 如果还没有 Azure 帐户，请[注册免费帐户](https://azure.microsoft.com/free/)。
- 在[虚拟机](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)或[应用程序](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)上启用系统分配的标识。

## <a name="view-the-service-principal"></a>查看服务主体

该过程演示如何查看启用了系统分配标识的 VM 的服务主体（相同的步骤也适用于应用程序）。

1. 依次单击“Azure Active Directory”、“企业应用程序”   。
2. 在“应用程序类型”  下，选择“所有应用程序”  ，然后单击“应用”  。
3. 在 "搜索筛选器" 框中，键入启用了托管标识的 Azure 资源的名称，或者从显示的列表中选择它。

   ![在门户中查看托管标识服务主体](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>后续步骤

[Azure 资源的托管标识](./overview.md)