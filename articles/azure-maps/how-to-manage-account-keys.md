---
title: 如何管理 Azure Maps 帐户和密钥 | Microsoft Docs
description: 可以使用 Azure 门户管理 Azure Maps 帐户和访问密钥。
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f5596f4c9bc827852c730d18856753e87de3c59c
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "42144760"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>如何管理 Azure Maps 帐户和密钥

可以通过 Azure 门户管理 Azure Maps 帐户和密钥。 获得帐户和密钥后，可以在你的网站或移动应用程序中实现 API。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-new-account"></a>创建新帐户

1. 登录到 [Azure 门户](http://portal.azure.com)。

1. 在 Azure 门户的左上角单击“创建资源”。

2. 搜索并选择“地图”，然后单击“创建”。

3. 输入新帐户的信息。 

![在门户中输入帐户信息](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>在帐户页上管理密钥

在创建帐户后，将会得到两个随机生成的密钥。 当希望检索地图数据或者创建新的 JavaScript 地图实例时，将使用这些密钥根据 Maps API 进行身份验证。 

可以在 Azure 门户中找到密钥。 导航到你的帐户，然后从菜单中选择“密钥”。

![在门户中管理帐户密钥](./media/how-to-manage-account-keys/account-keys-portal.png)

在此页中，可以复制密钥或生成新密钥。 

## <a name="delete-an-account"></a>删除帐户

可以通过 Azure 门户删除帐户。 导航到帐户概览页并选择“删除”。

![在门户中删除帐户](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>后续步骤

* 在[搜索地址](./how-to-search-for-address.md)中了解如何使用 Azure Maps 搜索 API。
* 了解如何使用 Azure Maps [JavaScript 地图控件](./how-to-use-map-control.md)。
