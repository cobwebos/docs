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
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903713"
---
但是，若要在 Azure 租户外部大规模共享映像，应创建一个应用注册来方便共享。  使用应用注册可以实现更复杂的共享方案，例如： 

* 当一家公司并购了另一家公司，并且 Azure 基础结构分散在不同的租户之间时管理共享的映像。 
* Azure 合作伙伴代表其客户管理 Azure 基础结构。 映像的自定义在合作伙伴租户内部完成，但基础结构部署都在客户的租户中发生。 


## <a name="create-the-app-registration"></a>创建应用注册

创建由两个租户用来共享映像库资源的应用程序注册。
1. [在 Azure 门户中打开“应用注册(预览版)”](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)。    
1. 从页面顶部的菜单中选择“新建注册”。
1. 在“名称”中键入 **myGalleryApp**。
1. 在 "**支持的帐户类型**" 中，选择 "**帐户"** 。
1. 在“重定向 URI”中键入 *，然后选择“注册”。 https://www.microsoft.com* 创建应用注册后，概述页将会打开。
1. 在概述页上，复制“应用程序(客户端) ID”并保存供稍后使用。   
1. 依次选择“证书和机密”、“新建客户端机密”。
1. 在“说明”中，键入“共享映像库跨租户应用机密”。
1. 在“过期”中，保留默认值“1 年内”，然后选择“添加”。
1. 复制机密值并将其保存到某个安全位置。 退出页面后无法检索该值。


为应用注册授予使用共享映像库的权限。
1. 在 Azure 门户中，选择要与另一租户共享的共享映像库。
1. 选择“选择访问控制(IAM)”，然后在“添加角色分配”下选择“添加”。 
1. 在“角色”下，选择“读取者”。
1. 在“将访问权限分配给:”下，保留现有的“Azure AD 用户、组或服务主体”。
1. 在“选择”下键入 **myGalleryApp**，并在此应用显示在列表中时将其选中。 完成后，选择“保存”。


## <a name="give-tenant-2-access"></a>授予租户 2 访问权限

通过使用浏览器请求登录，授予租户 2 对应用程序的访问权限。 请将 *Tenant2 ID>\<* 替换为要与其共享映像库的租户的租户 ID。 请将 *Application (client) ID> \<* 替换为创建的应用注册的应用程序 ID。 完成替换后，将 URL 粘贴到浏览器中，然后遵照登录提示登录到租户 2。

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

在 [Azure 门户](https://portal.azure.com)中以租户 2 的身份登录，并允许应用注册访问要在其中创建 VM 的资源组。

1. 选择该资源组，然后选择“访问控制(IAM)”。 在“添加角色分配”下，选择“添加”。 
1. 在“角色”下，键入“参与者”。
1. 在“将访问权限分配给:”下，保留现有的“Azure AD 用户、组或服务主体”。
1. 在“选择”下键入 **myGalleryApp**，并在此应用显示在列表中时将其选中。 完成后，选择“保存”。

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。

