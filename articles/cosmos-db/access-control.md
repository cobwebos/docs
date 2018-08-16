---
title: 设置 Azure Cosmos DB 中的访问控制 | Microsoft Docs
description: 了解如何设置 Azure Cosmos DB 中的访问控制。
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037907"
---
# <a name="access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的访问控制

若要将 Azure Cosmos DB 帐户读者访问权限添加到用户帐户，请让订阅所有者在 Azure 门户执行以下步骤。

1. 打开 Azure 门户，并选择 Azure Cosmos DB 帐户。
2. 单击“访问控制(IAM)”选项卡，然后单击“+ 添加”。
3. 在“添加权限”窗格中的“角色”框中，选择“Cosmos DB 帐户读者角色”。
4. 在“分配其访问权限”框中，选择“Azure AD 用户、组或应用程序”。
5. 在你想要授予访问权限的目录中选择用户、组或应用程序。  可以通过显示名称、电子邮件地址或对象标识符搜索目录。
    所选用户、组或应用程序会显示在所选成员列表中。
6. 单击“ **保存**”。

实体现在便可以读取 Azure Cosmos DB 资源。
