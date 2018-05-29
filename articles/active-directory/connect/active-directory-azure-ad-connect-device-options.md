---
title: Azure AD Connect：设备选项 | Microsoft Docs
description: 本文档详细介绍 Azure AD Connect 中提供的设备选项
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354502"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect：设备选项

以下文档提供了有关 Azure AD Connect 中提供的各种设备选项的信息。 可使用 Azure AD Connect 配置以下两个操作： 
* **混合 Azure AD 加入**：如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备同时加入到本地 Active Directory 和 Azure Active Directory。
* **设备写回**：设备写回用于在设备上启用对 AD FS（2012 R2 或更高版本）保护设备的基于条件的访问

## <a name="configure-device-options-in-azure-ad-connect"></a>在 Azure AD Connect 中配置设备选项

1.  运行 Azure AD Connect。 在“其他任务”页中，选择“配置设备选项”。
    ![配置设备选项](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    单击“下一步”时，会显示“概述”页，其中详细介绍了可以执行的操作。
    ![概述](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > 新的配置设备选项仅在版本 1.1.819.0 及更新版本中可用。

2.  为 Azure AD 提供凭据后，可以选择要在“设备选项”页上执行的操作。
    ![设备操作](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>后续步骤

* [配置混合 Azure AD 加入](../device-management-hybrid-azuread-joined-devices-setup.md)
* [配置/禁用设备写回](./active-directory-aadconnect-feature-device-writeback.md)

