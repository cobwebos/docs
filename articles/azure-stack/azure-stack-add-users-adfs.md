---
title: 为 Azure Stack ADFS 添加用户 | Microsoft Docs
description: 了解如何为 Azure Stack 的 ADFS 部署添加用户
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: f8abacbcb05d1346931b5c2e1097660cfbd8e594
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344160"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>在 AD FS 中添加 Azure Stack 用户
可以使用**Active Directory 用户和计算机**管理单元以将其他用户添加到利用其标识提供程序作为 AD FS 的 Azure Stack 环境。

## <a name="add-windows-server-active-directory-users"></a>添加 Windows Server Active Directory 用户
> [!TIP]
> 此示例使用默认请将 azurestack.local ASDK active directory。 

1.  提供对 Windows 管理工具访问权限的帐户登录到计算机并打开新的 Microsoft 管理控制台 (MMC)。
2.  单击“文件”>“添加或删除管理单元”。
3.  选择“Active Directory 用户和计算机” > “AzureStack.local” > “用户”。
4.  单击“操作” > “新建” > “用户”。
5.  在新建对象 – 用户窗口中，提供并确认密码
6.  单击“下一步”以最终确定值并单击“完成”创建用户。


## <a name="next-steps"></a>后续步骤
[创建服务主体](azure-stack-create-service-principals.md)