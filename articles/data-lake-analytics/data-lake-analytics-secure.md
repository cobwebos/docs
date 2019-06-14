---
title: 适用于多个用户的安全 Azure Data Lake Analytics
description: 了解如何在 Azure Data Lake Analytics 中配置多个用户以运行作业。
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813366"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>在 Azure Data Lake Analytics 中配置对作业信息的用户访问权限 

在 Azure Data Lake Analytics 中，你可以使用多个用户帐户或服务主体运行作业。 

为了使相同用户查看详细的作业信息，用户需要能够读取作业文件夹的内容。 作业文件夹位于 `/system/` 目录中。 

如果未配置所需权限，用户可能会看到以下错误：`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>配置对作业信息的用户访问权限

你可以使用“添加用户向导”  在文件夹上配置 ACL。 有关详细信息，请参阅[添加新用户](data-lake-analytics-manage-use-portal.md#add-a-new-user)。

如果需要更细化控制或需要为权限编写脚本，则按如下方式保护文件夹：

1. 在根文件夹上授予**执行**权限（通过访问 ACL）：
   - /
   
2. 在包含作业文件夹的文件夹上授予**执行**和**读取**权限（通过访问 ACL 和默认 ACL）。 例如，对于在 2018 年 5 月 25 日运行的特定作业，需要访问以下文件夹：
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>后续步骤
[添加新用户](data-lake-analytics-manage-use-portal.md#add-a-new-user)
