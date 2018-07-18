---
title: 注册应用程序 - Azure 认知服务 | Microsoft Docs
description: 有关如何向 Azure 自定义决策服务注册新应用的分步指南
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366439"
---
# <a name="register-your-application"></a>注册应用程序

若要将自定义决策服务用于应用程序，请在门户中注册该应用程序。 本文介绍了如何执行此操作。

1. 转到自定义决策服务的[前端页](https://ds.microsoft.com/)。 在功能区上，单击“我的门户”，如图像中所突出显示：

    ![我的门户](./media/portal.png)

    如果尚未登录，门户将提示你使用 [Microsoft 帐户](https://account.microsoft.com/account)进行登录。 在登录后，门户将在页面右上角显示你的 Microsoft 帐户。

2. 若要注册应用程序，请单击“新建应用”按钮。

3. 在对话框中，为你的应用程序选择一个应用 ID。 自定义决策服务要求每个应用程序具有唯一的 ID。 如果其他人已占用了此 ID，则系统会要求你另外选择一个。

4. 指定一个操作集 API。 此设置是一个 RSS 或 Atom 馈送，它将你的应用程序的可用内容传送到自定义决策服务。 为该馈送输入一个名称，并输入提供它的 URL。 若要稍后执行此步骤，请单击“馈送”按钮，然后单击“新建馈送”按钮。 下文中介绍了一个创建 RSS 馈送的示例。

5. 若要注册应用程序，请选择左下角的“自定义应用”复选框。 输入记录你的应用程序数据的 Azure 存储帐户的[连接字符串](../../storage/common/storage-configure-connection-string.md)。 有关如何创建存储帐户的详细信息，请参阅[如何创建、管理或删除存储帐户](../../storage/common/storage-create-storage-account.md)。

### <a name="next-steps"></a>后续步骤

* 开始优化[网页](custom-decision-service-get-started-browser.md)或[智能手机应用](custom-decision-service-get-started-app.md)。
* 通读[教程](custom-decision-service-tutorial-news.md)以获取更深入的示例。
* 参阅 [API 参考](custom-decision-service-api-reference.md)来详细了解所提供的功能。