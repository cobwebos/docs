---
title: Azure AD Connect 云预配代理：自动升级 |Microsoft Docs
description: 本主题介绍 Azure AD Connect 云预配代理中的内置自动升级功能。
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794453"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 云预配代理：自动升级

确保 Azure AD Connect 云预配代理的安装始终是最新的，并不是**自动升级**功能。 此功能在默认情况下是启用的，不能禁用。

代理安装在以下位置： **"Program FILES\AZURE AD Connect 预配 Agent\AADConnectProvisioningAgent.exe"**

可以通过右键单击该可执行文件，然后依次选择 "属性" 和 "详细信息" 来验证版本。

![代理文件版本](media/how-to-automatic-upgrade/agent1.png)

代理更新程序安装在以下位置： **"Program FILES\AZURE AD Connect 预配代理 Updater\AzureADConnectAgentUpdater.exe"**

可以通过右键单击该可执行文件，然后依次选择 "属性" 和 "详细信息" 来验证版本。

![代理更新程序版本](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>卸载代理
若要删除代理，请导航到 "**卸载或更改程序**" 并卸载以下各项：

- Microsoft Azure AD Connect 代理更新程序
- Microsoft Azure AD Connect 预配代理
- Microsoft Azure AD Connect 预配代理包

![代理删除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

