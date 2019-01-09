---
title: 为 Azure Stack ADFS 添加用户 | Microsoft Docs
description: 了解如何为 Azure Stack 的 ADFS 部署添加用户
services: azure-stack
documentationcenter: ''
author: PatAltimore
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
ms.openlocfilehash: ee571ec14a93653b524401d1d304021178ecd794
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120806"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>在 AD FS 中添加 Azure Stack 用户
可以使用“Active Directory 用户和计算机”管理单元将其他用户添加到 Azure Stack 环境中，并利用 AD FS 作为其标识提供者。

## <a name="add-windows-server-active-directory-users"></a>添加 Windows Server Active Directory 用户
> [!TIP]
> 此示例使用默认的 azurestack.local ASDK Active Directory。 

1.  使用允许访问 Windows 管理工具的帐户登录计算机，然后打开新的 Microsoft 管理控制台 (MMC)。
2.  单击“文件”>“添加或删除管理单元”。
3.  选择“Active Directory 用户和计算机” > “AzureStack.local” > “用户”。
4.  单击“操作” > “新建” > “用户”。
5.  在新建对象 – 用户窗口中，提供并确认密码
6.  单击“下一步”以最终确定值并单击“完成”创建用户。


## <a name="next-steps"></a>后续步骤
[创建服务主体](azure-stack-create-service-principals.md)