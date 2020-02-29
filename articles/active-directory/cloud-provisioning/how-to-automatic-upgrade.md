---
title: Azure AD Connect 云预配代理：自动升级 |Microsoft Docs
description: 本文介绍 Azure AD Connect 云预配代理中的内置自动升级功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190307"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 云预配代理：自动升级

通过自动升级功能，确保 Azure Active Directory （Azure AD）连接云预配代理安装始终是最新的。

代理安装在以下位置： "Program files\Azure AD Connect 预配 Agent\AADConnectProvisioningAgent.exe"

若要验证你的版本，请右键单击该可执行文件，然后选择 "属性" 和 "详细信息"。

![代理文件版本](media/how-to-automatic-upgrade/agent1.png)

代理更新程序安装在以下位置： "Program files\Azure AD Connect 预配代理 Updater\AzureADConnectAgentUpdater.exe"

若要验证你的版本，请右键单击该可执行文件，然后选择 "属性" 和 "详细信息"。

![代理更新程序版本](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>卸载代理
若要删除代理，请转到**卸载或更改程序**并卸载以下各项：

- **Microsoft Azure AD 连接代理更新程序**
- **Microsoft Azure AD 连接设置代理**
- **Microsoft Azure AD 连接预配代理包**

![代理删除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

