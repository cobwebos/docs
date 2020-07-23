---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173632"
---
1. 登录到 [Azure 门户]。
2. 选择 **[新** > **Web +** > **移动移动应用**》，然后为您的移动应用提供后端的名称。
3. 在“资源组”下****，选择一个现有资源组，或创建一个新资源组（使用与应用相同的名称）。 
4. 对于“应用服务计划”****，请选择默认计划（在[标准层](https://azure.microsoft.com/pricing/details/app-service/)中）。 还可以选择不同的计划，或[创建一个新计划](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan)。 

   应用服务计划的设置确定与应用关联的[位置、功能、成本和计算资源](https://azure.microsoft.com/pricing/details/app-service/)。 有关应用服务计划以及如何在不同定价层和所需位置中创建新计划的详细信息，请参阅 [Azure 应用服务计划深入概述](../articles/app-service/overview-hosting-plans.md)。
   
5. 选择 **“创建”**。 此步骤创建移动应用后端。 
6. 在新的移动应用后端的“设置”窗格中，依次选择“快速启动”>客户端应用平台 >“连接数据库”。************ 
   
   ![用于连接数据库的选项选择](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. 在“添加数据连接”**** 窗格中，选择“SQL 数据库” > “创建新数据库”。******** 输入数据库名称，选择一个定价层，然后选择“服务器”****。 可以重复使用此新数据库。 如果在同一位置已有数据库，则可选择“使用现有数据库”。**** 不建议使用位于不同位置的数据库，因为有带宽成本且延迟较高。
   
   ![选择数据库](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. 在“新服务器”窗格中，在“服务器名称”框中输入唯一服务器名称，提供登录名和密码，选中“允许 Azure 服务访问服务器”，并选择“确定”。**************** 此步骤创建新数据库。
9. 回到“添加数据连接”窗格中，选择“连接字符串”，输入数据库的登录名和密码值，并选择“确定”。************ 

   等待几分钟，等数据库署成功后再继续操作。

<!-- URLs. -->
[Azure 门户]: https://portal.azure.com/
