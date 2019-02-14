---
title: Azure AD Connect：设备选项 | Microsoft Docs
description: 本文档详细介绍 Azure AD Connect 中提供的设备选项
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f397401581cc1bc947f1b19ce248c17fba26f4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56160716"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect：设备选项

以下文档提供了有关 Azure AD Connect 中提供的各种设备选项的信息。 可使用 Azure AD Connect 配置以下两个操作： 
* **混合 Azure AD 加入**：如果环境具有本地 AD 占用情况并且希望利用 Azure AD 的优势，则可实现混合 Azure AD 加入的设备。 这些设备同时加入到本地 Active Directory 和 Azure Active Directory。
* **设备写回**：设备写回用于在设备上启用对 AD FS（2012 R2 或更高版本）保护设备的基于条件的访问

## <a name="configure-device-options-in-azure-ad-connect"></a>在 Azure AD Connect 中配置设备选项

1.  运行 Azure AD Connect。 在“其他任务”页中，选择“配置设备选项”。  单击“下一步”。
    ![配置设备选项](./media/how-to-connect-device-options/deviceoptions.png) 

    “概述”页显示详细信息。
    ![概述](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > 新的配置设备选项仅在版本 1.1.819.0 及较新版本中可用。

2.  为 Azure AD 提供凭据后，可以选择要在“设备选项”页上执行的操作。
    ![设备操作](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>后续步骤

* [配置混合 Azure AD 加入](../device-management-hybrid-azuread-joined-devices-setup.md)
* [配置/禁用设备写回](how-to-connect-device-writeback.md)

