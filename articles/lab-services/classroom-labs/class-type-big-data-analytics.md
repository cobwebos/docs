---
title: 使用 Azure 实验室服务设置实验室来讲授大数据分析 |Microsoft Docs
description: 了解如何设置实验室以使用 Hortonworks 数据平台（HDP）的 Docker 部署来讲授大数据分析。
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
ms.openlocfilehash: 3035b9debdd46c1a8ba246882312e28d1a2087e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118424"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>使用 HortonWorks 数据平台的 Docker 部署将实验室设置为大数据分析

本文介绍如何设置实验室来讲授大数据分析类。  使用此类类，学员可以了解如何处理大量数据，并应用计算机和统计学习算法来获取数据见解。  学生的主要目标是了解如何使用数据分析工具，例如[Apache Hadoop 的开源软件包](https://hadoop.apache.org/)，该软件包提供存储、管理和处理大数据的工具。

在此实验室中，学生将使用由[Cloudera](https://www.cloudera.com/)提供的常用商业版 Hadoop，称为[Hortonworks 数据平台（HDP）](https://www.cloudera.com/products/hdp.html)。  具体而言，学生将使用[HDP 的沙箱 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) ，这是一个简单易用的平台版本，无需付费，也不能用于学习和试验。  尽管此类可以使用 Windows 或 Linux 虚拟机（VM）部署了 HDP 沙盒，但本文将介绍如何使用 Windows。

此实验的另一个有趣方面是，我们将使用[Docker](https://www.docker.com/)容器在实验室 vm 上部署 HDP 沙盒。  每个 Docker 容器都为内部运行的软件应用程序提供了自己的独立环境。  从概念上讲，Docker 容器类似于嵌套式 Vm，可用于基于[Docker 中心](https://www.docker.com/products/docker-hub)提供的容器映像轻松部署和运行各种软件应用程序。  HDP 沙盒的 Cloudera 部署脚本会自动从 Docker 中心拉取[HDP 沙盒 3.0.1 Docker 映像](https://hub.docker.com/r/hortonworks/sandbox-hdp)，并运行两个 docker 容器：
  - 沙盒-hdp
  - 沙盒-代理

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要一个 Azure 订阅和实验室帐户才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户教程](tutorial-setup-lab-account.md)。  你还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 有关如何启用 marketplace 映像的详细信息，请参阅[指定可用于实验室创建者的 marketplace 映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 实验室帐户设置 | 说明 |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版映像，以便在实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。  有关如何创建教室实验室的详细信息，请参阅[设置课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 中（嵌套虚拟化）。 此 VM 大小最适合用于关系数据库、内存中缓存和分析。  此大小还支持嵌套虚拟化。|  
|虚拟机映像| Windows 10 专业版|

> [!NOTE] 
> 需要使用 Medium （嵌套虚拟化），因为使用 Docker 部署 HDP 沙箱需要：
>   - 带有嵌套虚拟化的 Windows Hyper-v
>   - 至少 10 GB RAM

## <a name="template-machine-configuration"></a>模板计算机配置

若要设置模板计算机，我们将：
- 安装 Docker
- 部署 HDP 沙盒
- 使用 PowerShell 和 Windows 任务计划程序自动启动 Docker 容器

### <a name="install-docker"></a>安装 Docker

本部分中的步骤基于[Cloudera 有关部署 Docker 容器的说明](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)。 

若要使用 Docker 容器，你必须首先在模板 VM 上安装 Docker Desktop：

1. 按照 "[先决条件" 部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中的步骤安装[用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)。 

    > [!IMPORTANT] 
    > 确保 "**使用 Windows 容器而不是 Linux 容器**" 配置选项处于未选中状态。

1. 确保**Windows 容器和 hyper-v 功能**已打开。
   ![打开或关闭 Windows 功能](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. 按照适用于 Windows 的[内存](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows)部分中的步骤配置 Docker 的内存配置。

    > [!WARNING]
    > 如果在安装 Docker 时无意中选中了 "**使用 Windows 容器而不是 Linux 容器**" 选项，则不会看到内存配置设置。  若要解决此问题，可以通过[单击 Windows 系统托盘中的 Docker 图标](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)切换到使用 Linux 容器;打开 Docker 桌面菜单后，选择 "**切换到 Linux 容器**"。
 
### <a name="deploy-hdp-sandbox"></a>部署 HDP 沙盒

在本部分中，你将部署 HDP 沙盒，并使用浏览器访问 HDP 沙盒。

1. 请确保已安装了指南的 "[先决条件" 部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中列出的[Git Bash](https://gitforwindows.org/) ，因为建议完成后续步骤。

1. [对于 Docker，使用 Cloudera 的部署和安装指南](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)，完成以下部分中的步骤：
   
   -    部署 HDP 沙盒
   -    验证 HDP 沙盒

    > [!WARNING] 
    > 下载 HDP 的最新 .zip 文件时，请*确保不要将 .zip 文件保存在*包含空格的目录路径中。

    > [!NOTE] 
    > 如果在部署过程中收到例外，说明**驱动器尚未共享**，则需要与 Docker 共享 C 驱动器，以便 HDP 的 Linux 容器可以访问本地 Windows 文件。  若要解决此问题，请[单击 Windows 系统托盘中的 docker 图标](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)以打开 docker 桌面菜单并选择 "**设置**"。  当**Docker 的 "设置**" 对话框打开时，选择 "**资源" > 文件共享**"，然后检查**C**驱动器。  然后，你可以重复这些步骤来部署 HDP 沙盒。

1. 部署并运行适用于 HDP 沙箱的 Docker 容器后，可以通过启动浏览器并按照 Cloudera 的说明打开[沙盒欢迎页面](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)并启动 HDP 仪表板来访问该环境。

    > [!NOTE] 
    > 这些说明假定你已首先将沙盒环境的本地 IP 地址映射到模板 VM 上的主机文件中的 sandbox-hdp.hortonworks.com。  如果**未**执行此映射，则可以通过导航到来访问沙盒欢迎页面 `http://localhost:8080` 。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>学生登录时自动启动 Docker 容器

若要为学生提供易于使用的体验，我们将使用自动执行以下操作的 PowerShell 脚本：
  - 当学生启动并连接到其实验室 VM 时，启动 HDP 沙盒 Docker 容器。
  - 启动浏览器并导航到沙盒欢迎页面。
当学生登录到其 VM 时，我们还将使用 Windows 任务计划程序自动运行此脚本。
若要进行此设置，请执行以下步骤：[大数据分析脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>成本估算

如果你想要估计此实验室的成本，你可以使用以下示例。

对于具有20个小时的计划类时间和10小时配额（适用于家庭或分配）的25名学生，实验室的价格为：
  - 25名学生 * （20 + 10）小时 * 55 实验室单位 * 0.01 美元/小时 = 412.50 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结论

本文指导你完成使用部署了 Docker 的 Hortonworks 数据平台为大数据分析类创建实验室所需的步骤。  此类类型的设置可用于类似的数据分析类。  此设置还适用于其他类型的类，这些类使用 Docker 进行部署。

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
