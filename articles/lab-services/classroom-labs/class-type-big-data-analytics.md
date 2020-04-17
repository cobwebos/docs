---
title: 设置实验室，使用 Azure 实验室服务教授大数据分析 |微软文档
description: 了解如何使用霍顿工程数据平台 （HDP） 的 Docker 部署设置实验室来教授大数据分析。
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538773"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>使用 Docker 部署霍顿工程数据平台，为大数据分析设置实验室

本文介绍如何设置实验室来教授大数据分析类。  通过此类课程，学生学习如何处理大量数据，并应用机器和统计学习算法来获取数据见解。  学生的关键目标是学习使用数据分析工具，例如[Apache Hadoop 的开源软件包](https://hadoop.apache.org/)，它提供了用于存储、管理和处理大数据的工具。

在这个实验室中，学生将使用[由Cloudera](https://www.cloudera.com/)提供的广受欢迎的商业版本Hadoop，称为[霍顿工程数据平台（HDP）。](https://www.cloudera.com/products/hdp.html)  具体来说，学生将使用[HDP 沙盒 3.0.1，](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html)这是一个简化、易于使用的平台版本，是免费的，用于学习和实验。  尽管此类在部署 HDP 沙盒时可以使用 Windows 或 Linux 虚拟机 （VM），但本文将介绍如何使用 Windows。

本实验的另一个有趣的方面是，我们将使用[Docker](https://www.docker.com/)容器在实验室 VM 上部署 HDP 沙盒。  每个 Docker 容器都为软件应用程序提供了自己的隔离环境，以便内部运行。  从概念上讲，Docker 容器类似于嵌套 VM，可用于根据[Docker Hub](https://www.docker.com/products/docker-hub)上提供的容器映像轻松部署和运行各种软件应用程序。  Cloudera 用于 HDP 沙盒的部署脚本会自动从 Docker 集线器中拉出[HDP 沙盒 3.0.1 Docker 映像](https://hub.docker.com/r/hortonworks/sandbox-hdp)并运行两个 Docker 容器：
  - 沙盒-hdp
  - 沙盒代理

## <a name="lab-configuration"></a>实验室配置

要设置此实验，需要 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户的教程](tutorial-setup-lab-account.md)。  您还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中描述的设置。 有关如何启用市场映像的详细信息，请参阅[指定可供实验室创建者使用的市场映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版映像，以便在实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。  有关如何创建教室实验室的详细信息，请参阅[设置教室实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 中等（嵌套虚拟化）。 此 VM 大小最适合关系数据库、内存缓存和分析。  此大小还支持嵌套虚拟化。|  
|虚拟机映像| Windows 10 专业版|

> [!NOTE] 
> 我们需要使用介质（嵌套虚拟化），因为使用 Docker 部署 HDP 沙盒需要：
>   - 具有嵌套虚拟化的 Windows Hyper-V
>   - 至少 10 GB 的 RAM

## <a name="template-machine-configuration"></a>模板机配置

要设置模板计算机，我们将：
- 安装 Docker
- 部署 HDP 沙盒
- 使用 PowerShell 和 Windows 任务计划程序自动启动 Docker 容器

### <a name="install-docker"></a>安装 Docker

本节中的步骤基于[Cloudera 使用 Docker 容器进行部署的说明](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)。 

要使用 Docker 容器，必须首先在模板 VM 上安装 Docker 桌面：

1. 按照["先决条件"部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中的步骤安装 Windows 的[Docker。](https://docs.docker.com/docker-for-windows/install/) 

    > [!IMPORTANT] 
    > 确保未选中 **"使用 Windows 容器"而不是 Linux 容器**配置选项。

1. 确保**打开 Windows 容器和超 V 功能**。
   ![打开或关闭 Windows 功能](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. 按照["内存为 Windows"](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows)部分中的步骤配置 Docker 的内存配置。

    > [!WARNING]
    > 如果在安装 Docker 时无意中选中 **"使用 Windows 容器"而不是 Linux 容器**选项，则看不到内存配置设置。  要解决此问题，您可以通过[单击 Windows 系统托盘中的 Docker 图标](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)切换到使用 Linux 容器;打开 Docker 桌面菜单时，选择 **"切换到 Linux 容器**"。
 
### <a name="deploy-hdp-sandbox"></a>部署 HDP 沙盒

在本节中，您将部署 HDP 沙盒，然后使用浏览器访问 HDP 沙盒。

1. 请确保您已安装本指南["先决条件"部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中列出的[Git Bash，](https://gitforwindows.org/)因为建议完成后续步骤。

1. 使用[Cloudera 的 Docker 部署和安装指南](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)，完成以下各节中的步骤：
   
   -    部署 HDP 沙盒
   -    验证 HDP 沙盒

    > [!WARNING] 
    > 下载 HDP 的最新 .zip 文件时，请确保*不要*将 .zip 文件保存在包含空白的目录路径中。

    > [!NOTE] 
    > 如果您在部署期间收到异常，指出**驱动器尚未共享**，则需要与 Docker 共享 C 驱动器，以便 HDP 的 Linux 容器可以访问本地 Windows 文件。  要解决此问题，[请单击 Windows 系统托盘中的 Docker 图标](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)以打开 Docker 桌面菜单并选择 **"设置**"。  打开**Docker 的"设置"** 对话框时，选择 **"资源>文件共享**"并检查**C**驱动器。  然后，您可以重复部署 HDP 沙盒的步骤。

1. 部署并运行 HDP 沙盒的 Docker 容器后，您可以通过启动浏览器并按照 Cloudera 的说明访问环境，以打开[沙盒欢迎页面](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)并启动 HDP 仪表板。

    > [!NOTE] 
    > 这些说明假定您首先将沙盒环境的本地 IP 地址映射到模板 VM 上的主机文件中的sandbox-hdp.hortonworks.com。  **如果不**执行此操作映射，可以通过导航到[http://localhost:8080](http://localhost:8080)访问"沙盒欢迎"页。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>学生登录时自动启动 Docker 容器

为了为学生提供易于使用的体验，我们将使用自动使用 PowerShell 脚本：
  - 当学生启动并连接到其实验室 VM 时，启动 HDP 沙盒 Docker 容器。
  - 启动浏览器并导航到沙盒欢迎页面。
我们还将使用 Windows 任务计划程序在学生登录到其 VM 时自动运行此脚本。
要设置此设置，请按照以下步骤操作：[大数据分析脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>成本估算

如果要估计本实验的成本，可以使用以下示例。

对于有 25 名学生的班级，有 20 小时的上课时间和 10 小时的家庭作业或作业配额，实验室的价格是：
  - 25 名学生 = （20 + 10） 小时 = 55 个实验室单位 = 每小时 0.01 USD = 412.50 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文介绍了为使用 Docker 部署的 Hortonworks 数据平台为大数据分析类创建实验室所需的步骤。  此类类型的设置可用于类似的数据分析类。  此设置还可能适用于使用 Docker 进行部署的其他类型的类。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)
