---
title: 将 AppSource 包存储到 Azure 存储中并使用 SAS 密钥生成 URL | Microsoft Docs
description: 详细介绍了上传和保护 AppSource 包所需执行的步骤。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 335590cc616035020f66cfc5208e21d4c5128fe6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805784"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>将 AppSource 包存储到 Azure 存储中并使用 SAS 密钥生成 URL
=============================================================================

为了维护文件的安全性，所有合作伙伴必须将其 AppSource 包文件存储在 Azure blob 存储帐户中，并使用 SAS 密钥共享该文件。 我们将从你的 Azure 存储位置检索该包文件进行认证，并将其用于 AppSource 试用。

使用以下步骤将你的包上传到 Blob 存储：

1.  转到 <http://azure.microsoft.com> 并创建一个免费试用版或收费帐户。

2.  登录到 [Azure 门户](http://portal.azure.com/)。

3.  通过单击“+ 新建”并转到“数据 + 存储”帐户来创建一个新的存储帐户。

  ![Microsoft Azure 门户中的“数据 + 存储”边栏选项卡](media/CRMScreenShot7.png)

4. 输入**名称**和**资源组**名称，然后单击“创建”按钮。

  ![在 Microsoft Azure 门户中创建存储帐户](media/CRMScreenShot8.png)

5. 导航到新创建的资源组并创建一个新的 blob 容器。

  ![使用 Microsoft Azure 门户将包上传为 blob](media/CRMScreenShot9.png)

6.  下载并安装 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)（如果尚未执行此操作）。

7.  打开存储资源管理器，并使用图标来连接到你的 Azure 存储帐户。

8.  导航到你创建的 blob 容器，然后单击“上传”来添加包 zip 文件。

  ![使用 Microsoft 存储资源管理器上传包](media/CRMScreenShot10.png)

9.  右键单击你的文件，并选择“获取共享访问签名”。

  ![获取 Azure 文件的共享访问签名](media/CRMScreenShot11.png)

10.  修改“到期时间”以使 SAS 在一个月内保持活动状态，然后单击“创建”。

  ![修改 Azure 文件的 SAS 到期日期](media/CRMScreenShot12.png)

11.  复制 URL 字段并保存它供以后使用。 在创建关联的产品/服务时需要输入此 URL。 

  ![复制 Azure 文件的 SAS URL](media/CRMScreenShot13.png)

