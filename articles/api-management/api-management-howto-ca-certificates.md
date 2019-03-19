---
title: 添加自定义 CA 证书 - Azure API 管理 | Microsoft Docs
description: 了解如何在 Azure API 管理中添加自定义 CA 证书。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 5161a35fd52b2f3d8374c76bdab60281e33dacf6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730641"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>如何在 Azure API 管理中添加自定义 CA 证书

Azure API 管理允许在受信任的根证书和中间证书存储中的计算机上安装 CA 证书。 如果服务需要自定义 CA 证书，则应使用此功能。

本文介绍如何在 Azure 门户中管理 Azure API 管理服务实例的 CA 证书。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>上传 CA 证书

![添加 CA 证书](media/api-management-howto-ca-certificates/00.png)

请按照以下步骤来上传新的 CA 证书。 如果尚未创建 API 管理服务实例，请参阅教程[创建 API 管理服务实例](get-started-create-service-instance.md)。

1. 在 Azure 门户中导航到 Azure API 管理服务实例。

2. 从菜单中选择“CA 证书”。

3. 单击“**+ 添加**”按钮。  

    ![添加 CA 证书](media/api-management-howto-ca-certificates/01.png)  

4. 浏览证书并选定证书存储。 仅需要公钥，因此不需要密码。

    ![添加 CA 证书](media/api-management-howto-ca-certificates/02.png)  

5. 单击“ **保存**”。 此操作可能需要几分钟的时间。

    ![添加 CA 证书](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> 可以使用 `New-AzApiManagementSystemCertificate` Powershell 命令上传 CA 证书。

## <a name="step1a"> </a>删除客户端证书

若要删除证书，请单击上下文菜单“...”并选择该证书旁边的“删除”。

![删除 CA 证书](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
