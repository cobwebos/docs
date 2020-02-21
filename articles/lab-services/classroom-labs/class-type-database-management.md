---
title: 设置实验室来讲授关系数据库的数据库管理 |Microsoft Docs
description: 了解如何设置实验室来讲授关系数据库的管理。
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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469912"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>设置实验室来讲授关系数据库的数据库管理

本文介绍如何在 Azure 实验室服务中为基本数据库管理类设置实验室。 数据库概念是在大学的大多数计算机科学部门中讲授的一个介绍性课程。 结构化查询语言（SQL）是一种国际标准。 SQL 是一种用于关系数据库管理的标准语言，包括在数据库中添加、访问和管理内容。  最值得注意的是，其快速处理、可靠的可靠性、易用性和灵活性。

本文介绍如何使用 MySQL 数据库服务器和 SQL Server 2019 服务器在实验室中设置虚拟机模板。  [MySQL](https://www.mysql.com/)是一种可自由使用的开源关系数据库管理系统（RDBMS）。  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019)是最新版本的 Microsoft RDBMS。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要一个 Azure 订阅和实验室帐户才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户教程](tutorial-setup-lab-account.md)。  你还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 有关如何启用 marketplace 映像的详细信息，请参阅[指定可用于实验室创建者的 marketplace 映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|Marketplace 映像| 启用 "Windows Server 2019 上的 SQL Server 2019 标准" 映像，以便在实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。  有关如何创建教室实验室的详细信息，请参阅[设置课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 中。 此大小最适合用于关系数据库、内存中缓存和分析。|
|虚拟机映像| Windows Server 2019 上的 SQL Server 2019 标准版|

## <a name="template-machine-configuration"></a>模板计算机配置

若要在 Windows Server 2019 上安装 MySQL，你可以按照在[虚拟机上安装和运行 Mysql 社区服务器](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)中所述的步骤进行操作。

在创建新实验室时选择的虚拟机映像中预安装了 SQL Server 2019。

## <a name="cost-estimate"></a>成本估算

我们来介绍此类的可能的成本估算。  我们将使用一类25名学生。  计划的类时间有20小时。  此外，每个学生在计划的类时间之外为家庭作业或分配获取10小时配额。  选择的虚拟机大小为 "中"，即 "42 实验室单位"。

下面是此类的可能的成本估算示例：

25名学生 \* （20个计划小时 + 10 个配额小时） \* 0.42 美元/小时 = 315.00 美元

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文指导你完成使用 MySQL 和 SQL Server 为基本数据库管理概念创建实验室所需的步骤。 您可以为其他数据库类使用类似的设置。

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
