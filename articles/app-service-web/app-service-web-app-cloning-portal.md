---
title: "使用 Azure 门户克隆 Web 应用"
description: "了解如何使用 Azure 门户 将 Web 应用克隆到新的 Web 应用。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 20b0ae4e-67e8-4bae-9d74-8a24dc445cce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 9ebfa91c7972ab3c264032ead8376c23c1197a4b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="azure-app-service-app-cloning-using-azure-portal"></a>使用 Azure 门户克隆 Azure 应用服务应用
[Azure 应用服务 Web 应用](http://go.microsoft.com/fwlink/?LinkId=529714)中的克隆功能可以轻松将现有 Web 应用克隆到位于不同或相同区域中的新建应用。 这样，客户就可以跨不同区域部署许多应用。

应用克隆目前仅支持高级层应用服务计划。 新功能使用与 Web 应用备份功能相同的限制，具体请参阅[在 Azure 应用服务中备份 Web 应用](web-sites-backup.md)。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>克隆现有应用
Web 应用必须在“高级”模式下运行，才能为 Web 应用创建副本。

1. 在 [Azure 门户](https://portal.azure.com/)中，打开 Web 应用的边栏选项卡。
2. 单击“工具”。 然后，在“工具”边栏选项卡中，单击“克隆应用”。
   
    ![][1]
   
   > [!NOTE]
   > 如果 Web 应用不是处于“高级”模式，用户会收到消息，指出应用克隆支持的模式。 此时可以选择“升级”。
   > 
   > 
3. “克隆应用”边栏选项卡中提供了新 Web 应用、资源组和应用服务计划的名称。 用户也可以选择是否克隆多个源 Web 应用设置。
   
    ![][2]
4. 单击“创建”后，平台将开始创建源 Web 应用的副本。

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>将现有应用克隆到应用服务环境
在“克隆应用”边栏选项卡中，客户可以选择现有应用服务环境中的应用池。

## <a name="current-restrictions"></a>当前限制
此功能目前以预览版提供，我们正在不断地添加新功能。以下是当前在 Azure 门户中支持应用克隆存在的已知限制：

* 不会克隆 Azure 流量管理器设置
* 不会克隆自动缩放设置
* 不会克隆备份计划设置
* 不会克隆 VNET 设置
* 不会自动在目标 Web 应用上设置 App Insights
* 不会克隆简易身份验证设置
* 不会克隆 Kudu 扩展
* 不会克隆 TiP 规则
* 将不会克隆数据库内容

### <a name="references"></a>参考
* [使用 PowerShell 克隆 Web 应用](app-service-web-app-cloning.md)
* [在 Azure 应用服务中备份 Web 应用](web-sites-backup.md)
* [如何创建应用服务环境](app-service-web-how-to-create-an-app-service-environment.md)
* [在应用服务环境中创建 Web 应用](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [应用服务环境简介](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png

