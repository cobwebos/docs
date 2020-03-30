---
title: 设置实验室以教授关系数据库的数据库管理 |微软文档
description: 了解如何设置实验室来教授关系数据库的管理。
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469912"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>设置实验室以教授关系数据库的数据库管理

本文介绍如何在 Azure 实验室服务中为基本数据库管理类设置实验室。 数据库概念是大学的大多数计算机系讲授的入门课程之一。 结构化查询语言 （SQL） 是一种国际标准。 SQL 是关系数据库管理的标准语言，包括添加、访问和管理数据库中的内容。  它以其快速的处理、经验证的可靠性、易用性和使用灵活性而闻名。

在本文中，我们将演示如何在具有 MySQL 数据库服务器和 SQL Server 2019 服务器的实验室中设置虚拟机模板。  [MySQL](https://www.mysql.com/)是一个免费提供的开源关系数据库管理系统 （RDBMS）。  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019)是微软 RDBMS 的最新版本。

## <a name="lab-configuration"></a>实验室配置

要设置此实验，需要 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户的教程](tutorial-setup-lab-account.md)。  您还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中描述的设置。 有关如何启用市场映像的详细信息，请参阅[指定可供实验室创建者使用的市场映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用"Windows 服务器 2019 标准 2019 标准"映像，以便在您的实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。  有关如何创建教室实验室的详细信息，请参阅[设置教室实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 中。 此大小最适合关系数据库、内存缓存和分析。|
|虚拟机映像| SQL 服务器 2019 标准在 Windows 服务器 2019|

## <a name="template-machine-configuration"></a>模板机配置

要在 Windows 服务器 2019 上安装 MySQL，可以按照在[虚拟机上安装和运行 MySQL 社区服务器](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)中所述的步骤。

SQL Server 2019 预安装在创建新实验室时选择的虚拟机映像中。

## <a name="cost-estimate"></a>成本估算

让我们来介绍此类的可能成本估算。  我们将使用一个25名学生的班级。  有 20 小时的计划上课时间。  此外，每个学生在计划上课时间之外的家庭作业或作业将获得 10 小时的配额。  我们选择的虚拟机大小是中等的，即 42 个实验室单元。

下面是此类的可能成本估算的示例：

25\*名学生（20 个预定小时 + \* 10 个配额小时） 0.42 USD 每小时 = 315.00 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文介绍了使用 MySQL 和 SQL Server 为基本数据库管理概念创建实验室所需的步骤。 可以为其他数据库类使用类似的设置。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)
