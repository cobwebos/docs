---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "73903713"
---
但是，如果要在 Azure 租户外部大规模共享映像，则应创建一个应用注册以方便共享。  使用应用注册可实现更复杂的共享方案，例如： 

* 当一家公司收购另一家公司并且 Azure 基础结构分布在不同租户中时，管理共享映像。 
* Azure 合作伙伴会代表其客户管理 Azure 基础结构。 映像自定义会在合作伙伴租户中完成，但基础结构部署将在客户的租户中进行。 


## <a name="create-the-app-registration"></a>创建应用注册

创建一个应用程序注册，这两个租户将使用该注册来共享映像库资源。
1. 打开 [Azure 门户中的“应用注册(预览版)”](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)。    
1. 从页面顶部的菜单中选择“新建注册”。
1. 在“名称”中，键入“myGalleryApp”。
1. 在“支持的帐户类型”中，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
1. 在“重定向 URI”中键入 https://www.microsoft.com，然后选择“注册”。 创建应用注册后，会打开概览页。
1. 在概览页上，复制“应用程序(客户端) ID”并保存供以后使用。   
1. 选择“证书和机密”，然后选择“新建客户端机密” 。
1. 在“说明”中，键入“跨租户共享映像库应用机密”。
1. 在“到期”中，保留默认值“在 1 年内”，然后选择“添加”。
1. 复制机密的值并将其保存到安全位置。 离开此页后，将无法检索该值。


向应用注册授予使用共享映像库的权限。
1. 在 Azure 门户中，选择要与其他租户共享的共享映像库。
1. 选择“选择访问控制(标识和访问管理)”，然后在“添加角色分配”下选择“添加”。 
1. 在“角色”下，选择“读者”。
1. 在“将访问权限分配给:”下，将此项保留为“Azure AD 用户、组或服务主体”。
1. 在“选择”下键入“myGalleryApp”，当它出现在列表中时将其选中。 完成后，选择“保存”。


## <a name="give-tenant-2-access"></a>授予租户 2 访问权限

授予租户 2 访问应用程序的权限，允许其通过浏览器请求登录。 将 \<Tenant2 ID > 替换为要与之共享映像库的租户的租户 ID。 将 *\<Application (client) ID>* 替换为所创建的应用注册的应用程序 ID。 完成替换后，将 URL 粘贴到浏览器中，并按照登录提示登录到租户 2。

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

在 [Azure 门户](https://portal.azure.com)中以租户 2 身份登录，并向应用注册授予对特定资源组（需在其中创建 VM）的访问权限。

1. 选择该资源组，然后选择“访问控制(标识和访问管理)”。 在“添加角色分配”下，选择“添加”。 
1. 在“角色”下，键入“参与者” 。
1. 在“将访问权限分配给:”下，将此项保留为“Azure AD 用户、组或服务主体”。
1. 在“选择”下键入“myGalleryApp”，然后，当它出现在列表中时将其选中。 完成后，选择“保存”。

> [!NOTE]
> 需要等待映像版本的生成和复制完全完成，然后才能使用同一个托管映像来创建另一个映像版本。

