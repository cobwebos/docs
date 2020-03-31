---
title: Azure AD 连接云预配代理：自动升级 |微软文档
description: 本文介绍了 Azure AD Connect 云预配代理中的内置自动升级功能。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190307"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD 连接云预配代理：自动升级

通过自动升级功能，请确保 Azure 活动目录 （Azure AD） 连接云预配代理安装始终保持最新。

代理安装在此处："程序文件_Azure AD 连接预配代理_AADConnect配置代理.exe"

要验证版本，请右键单击可执行文件并选择属性，然后选择详细信息。

![代理文件版本](media/how-to-automatic-upgrade/agent1.png)

代理更新程序安装在此处："程序文件_Azure 连接预配代理更新程序_AzureADConnectAgentUpdater.exe"

要验证版本，请右键单击可执行文件并选择属性，然后选择详细信息。

![代理更新程序版本](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>卸载代理
要删除代理，请转到**卸载或更改程序**并卸载以下内容：

- **Microsoft Azure AD Connect 代理更新程序**
- **Microsoft Azure AD Connect 预配代理**
- **Microsoft Azure AD Connect 预配代理包**

![代理删除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

