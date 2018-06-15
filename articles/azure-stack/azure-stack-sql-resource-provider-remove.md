---
title: 删除 Azure 堆栈上的 SQL 资源提供程序 |Microsoft 文档
description: 了解如何从 Azure 堆栈部署中删除 SQL 资源提供程序。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294774"
---
# <a name="removing-the-mysql-resource-provider"></a>删除 MySQL 资源提供程序  
删除之前 SQL 资源提供程序，请务必首先删除任何依赖关系。

## <a name="remove-the-mysql-resource-provider"></a>删除 MySQL 资源提供程序 

1. 验证你已删除任何现有的 SQL 资源提供程序依赖关系。

  > [!NOTE]
  > 即使从属资源当前正在使用的资源提供程序，则卸载 SQL 资源提供程序将继续操作。 
  
2. 确保已保留针对此 SQL 资源提供程序适配器版本下载的原始部署包。
3. 重新运行部署脚本中使用以下参数：
    - 使用-卸载参数
    - 特权终结点的 IP 地址或 DNS 名称。
    - 访问特权终结点时所需的云管理员凭据。
    - Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。

## <a name="next-steps"></a>后续步骤
[作为 PaaS 产品/服务应用程序服务](azure-stack-app-service-overview.md)
