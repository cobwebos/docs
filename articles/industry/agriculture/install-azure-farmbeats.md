---
title: 安装 Azure FarmBeats
description: 本文介绍如何在 Azure 订阅中安装 Azure FarmBeats
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 7d3c70695000ae62f374bc558d4b408733968b83
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069279"
---
# <a name="install-azure-farmbeats"></a>安装 Azure FarmBeats

本文介绍如何在 Azure 订阅中安装 Azure FarmBeats。

Azure FarmBeats 是 Azure Marketplace 中提供的企业到企业产品/服务。 它可跨提供商和可操作见解的生成来聚合农业数据集。 Azure FarmBeats 通过使你能够基于融合的数据集构建人工智能（AI）或机器学习（ML）模型，来实现此功能。 Azure FarmBeats 的两个主要组件是：

- **Datahub**：一种 API 层，可跨不同的提供程序跨各种农业数据集进行聚合、标准化和 contextualization。

- **加速器**：在 Datahub 上构建的 Web 应用程序。 它将快速启动模型开发和可视化。 该加速器使用 Azure FarmBeats Api 将引入传感器数据的可视化效果演示为作为地图的模型输出的图表和可视化效果。

## <a name="general-information"></a>常规信息

### <a name="components-installed"></a>已安装组件

安装 Azure FarmBeats 时，会在 Azure 订阅中预配以下资源：

| 已安装 Azure 资源  | Azure FarmBeats 组件  |
|---------|---------|
| Application Insights   |      Datahub & 加速器      |
| 应用服务     |     Datahub & 加速器     |
| 应用服务计划   | Datahub & 加速器  |
| API 连接    |  数据中心       |
| 用于 Redis 的 Azure 缓存       | 数据中心      |
| Azure Cosmos DB   |  数据中心       |
| Azure 数据工厂 V2       |     Datahub & 加速器      |
| Azure Batch 帐户    | 数据中心   |
| Azure 密钥保管库 |  Datahub & 加速器        |
| Azure Maps 帐户       |     加速器    |
| 事件中心命名空间    |     数据中心      |
| 逻辑应用      |  数据中心       |
| 存储帐户      |     Datahub & 加速器      |
| 时序见解     |    数据中心    |

### <a name="costs-incurred"></a>产生的成本

Azure FarmBeats 的成本是底层 Azure 服务的成本的聚合。 可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator)来计算 Azure 服务的定价信息。 总体安装的实际成本将根据使用情况而有所不同。 这两个组件的稳定状态为：

- Datahub-每天小于 $10
- 加速器-小于 $2/天

### <a name="regions-supported"></a>支持的区域

目前，Azure FarmBeats 在以下区域的公有云环境中受支持：

- 澳大利亚东部
- 美国中部
- 美国东部
- 美国东部 2
- 美国西部
- 美国西部 2
- 北欧
- 西欧
- 东亚
- 东南亚

### <a name="time-taken"></a>所用时间

Azure FarmBeats 的整个设置，包括准备和安装所需的时间不到1小时。

## <a name="prerequisites"></a>先决条件

开始 Azure FarmBeats 的实际安装之前，需要完成以下步骤：

### <a name="verify-permissions"></a>验证权限

若要安装 Azure FarmBeats，需要在 Azure 租户中具有以下权限：

- 租户-AAD 应用创建者
- 订阅-所有者
- 要在其中安装 FarmBeats 的资源组-所有者

[创建 AAD 应用程序](#create-an-aad-application)步骤需要前两个权限。 如果需要，可以获取具有适当权限的人员来创建 AAD 应用程序。 安装 FarmBeats 的人员需要是要在其中安装 FarmBeats 的资源组的所有者。

可以按照[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/check-access)上的说明验证 Azure 门户中的访问权限。

### <a name="decide-subscription-and-region"></a>确定订阅和区域

需要 Azure 订阅 ID 和要安装 Azure FarmBeats 的区域。 选择 "[支持的区域](#regions-supported)" 部分中列出的区域之一。

请记下**Azure 订阅 ID**和**azure 区域**。

### <a name="create-an-aad-application"></a>创建 AAD 应用程序

Azure FarmBeats 要求创建和注册 Azure Active Directory 应用程序。 若要成功运行 AAD 创建脚本，需要具备以下权限：

- 租户-AAD 应用创建者
- 订阅-所有者

使用 PowerShell 环境在 Cloud Shell 实例中运行以下步骤。 系统将提示首次用户选择订阅，并创建存储帐户。 按照说明完成安装。

1. 下载[AAD 应用生成器脚本](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 默认情况下，该文件将下载到主目录。 导航到目录。

    ```azurepowershell-interactive
        cd
    ```

3. 运行 AAD 脚本

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. AAD 脚本大约需要2分钟的时间来运行和输出屏幕上的值，以及在同一目录中的 json 文件。 如果你有其他人运行该脚本，请要求他们与你共享此输出。

### <a name="create-sentinel-account"></a>创建 Sentinel 帐户

你的 Azure FarmBeats 设置使你能够从你的场的欧洲空间代理商的[Sentinel](https://scihub.copernicus.eu/)卫星任务中获取卫星电视。 若要配置此设置，需要一个 Sentinel 帐户。

按照以下步骤使用 Sentinel 创建免费帐户：

1. 请参阅官方[注册](https://aka.ms/SentinelRegistration)页。
2. 提供所需的详细信息（名字、姓氏、用户名、密码和电子邮件 ID）并填写表单。
3. 验证链接将发送到已注册的电子邮件 ID。 选择电子邮件中提供的链接并完成验证。

注册过程已完成。 完成验证后，请记下**Sentinel 用户名**和**sentinel 密码**。

## <a name="install"></a>安装

现在可以安装 FarmBeats 了。 按照以下步骤开始安装：

1. 登录到 Azure 门户。 在右上角选择你的帐户，并切换到要在其中安装 Azure FarmBeats 的 Azure AD 租户。

2. 在门户中中转到 Azure Marketplace，并在 Marketplace 中搜索 " **Azure FarmBeats** "。

3. 此时将显示一个新窗口，其中显示了 Azure FarmBeats 的概述。 选择 **“创建”** 。

4. 新窗口随即出现。 通过选择正确的订阅、资源组和要将 Azure FarmBeats 安装到的位置来完成注册过程。

5. 提供电子邮件地址，该地址应在**FarmBeats Service 警报**部分收到与 Azure FarmBeats 相关的任何服务警报。 选择页面底部的 "**下一步**" 以转到 "**依赖项**" 选项卡。

    ![基本信息选项卡](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. 将[aad 脚本](#create-an-aad-application)的输出中的各个条目复制到 aad 应用程序部分中的输入。

7. 在 "Sentinel 帐户" 部分输入 " [sentinel 帐户](#create-sentinel-account)用户名" 和 "密码"。 选择 "**下一步**" 转到 "**评审 + 创建**" 选项卡。

    ![Dependencies Tab](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 验证输入的详细信息后，选择 **"确定"** 。 此时将显示 "使用条款" 页。 查看条款，然后选择 "**创建**" 以开始安装。 你将被重定向到可在其中执行安装进度的页面。

安装完成后，可以通过导航到在安装期间提供的网站名称来验证安装并开始使用 FarmBeats 门户： https://\<FarmBeats-name >. appname>.azurewebsites.net。 应该会看到 FarmBeats 用户界面，其中包含用于创建场的选项。

可在 https://\<FarmBeats-name >-api/swagger 找到**Datahub** 。 在这里，你将看到不同的 FarmBeats API 对象并对 Api 执行 REST 操作。

## <a name="upgrade"></a>升级

若要将 FarmBeats 升级到最新版本，请使用 PowerShell 环境在 Cloud Shell 实例中运行以下步骤。 用户需要是安装了 FarmBeats 的订阅的所有者。

系统将提示首次用户选择订阅，并创建存储帐户。 按照说明完成安装。

1. 下载[升级脚本](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./update-farmbeats.ps1
    ```

2. 默认情况下，该文件将下载到主目录。 导航到目录。

    ```azurepowershell-interactive
        cd
    ```

3. 运行升级脚本

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

输入的 json 文件的路径是可选的。 如果未指定，脚本将要求提供所有必需的输入。 升级应在30分钟内完成。

## <a name="uninstall"></a>卸载

若要卸载 Azure FarmBeats Datahub 或加速器，请完成以下步骤：

1. 登录到 Azure 门户并删除安装了这些组件**的资源组**。

2. 请参阅 Azure Active Directory & 删除链接到 Azure FarmBeats 安装**的 Azure AD 应用程序**。

## <a name="next-steps"></a>后续步骤

已了解如何在 Azure 订阅中安装 Azure FarmBeats。 现在，了解如何[将用户添加](manage-users-in-azure-farmbeats.md#manage-users)到 Azure FarmBeats 实例。
