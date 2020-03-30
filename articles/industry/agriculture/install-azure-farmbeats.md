---
title: 安装 Azure FarmBeats
description: 本文介绍如何在 Azure 订阅中安装 Azure 服务器场节拍
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479553"
---
# <a name="install-azure-farmbeats"></a>安装 Azure FarmBeats

本文介绍如何在 Azure 订阅中安装 Azure 服务器场节拍。

Azure FarmBeats 是 Azure Marketplace 中提供的企业到企业产品/服务。 它支持跨提供商聚合农业数据集并生成可操作的见解。 Azure FarmBeats 通过使您能够基于融合数据集构建人工智能 （AI） 或机器学习 （ML） 模型来实现此。 Azure FarmBeats 的两个主要组件是：

- **数据集器**：一个 API 层，用于跨不同提供程序对各种农业数据集进行聚合、规范化和上下文化。

- **加速器**：构建在 Datahub 之上的 Web 应用程序。 它启动模型开发和可视化。 加速器使用 Azure FarmBeats API 来演示引入传感器数据的可视化作为图表和模型输出的可视化作为地图。

## <a name="general-information"></a>常规信息

### <a name="components-installed"></a>已安装组件

安装 Azure 服务器场Beats 时，Azure 订阅中预配了以下资源：

| 已安装 Azure 资源  | Azure 服务器场节拍组件  |
|---------|---------|
| Application Insights   |      数据中心&加速器      |
| 应用服务     |     数据中心&加速器     |
| 应用服务计划   | 数据中心&加速器  |
| API 连接    |  数据中心       |
| 用于 Redis 的 Azure 缓存       | 数据中心      |
| Azure Cosmos DB   |  数据中心       |
| Azure 数据工厂 V2       |     数据中心&加速器      |
| Azure Batch 帐户    | 数据中心   |
| Azure Key Vault |  数据中心&加速器        |
| Azure 地图帐户       |     加速器    |
| 事件中心命名空间    |     数据中心      |
| 逻辑应用      |  数据中心       |
| 存储帐户      |     数据中心&加速器      |
| 时序见解     |    数据中心    |

### <a name="costs-incurred"></a>费用

Azure FarmBeats 的成本是基础 Azure 服务成本的聚合。 可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator)计算 Azure 服务的定价信息。 总安装的实际成本将因使用情况而异。 两个组件的稳定状态成本为：

- 数据集器 - 每天少于 10 美元
- 加速器 - 每天少于 2 美元

### <a name="regions-supported"></a>支持的区域

目前，Azure FarmBeats 在以下区域的公共云环境中受支持：

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

### <a name="time-taken"></a>所走的时间

Azure FarmBeats 的整个设置（包括准备和安装）需要不到一个小时。

## <a name="prerequisites"></a>先决条件

在开始实际安装 Azure FarmBeats 之前，您需要完成以下步骤：

### <a name="verify-permissions"></a>验证权限

安装 Azure FarmBeats 需要 Azure 租户中的以下权限：

- 租户 - AAD 应用程序创建者
- 订阅 - 所有者
- 正在其中安装服务器场Beats的资源组 - 所有者

[创建 AAD 应用程序](#create-an-aad-application)步骤需要前两个权限。 如果需要，您可以让具有相应权限的人员创建 AAD 应用程序。

从市场运行 FarmBeats 安装的人员必须是正在其中安装 FarmBeats 的资源组的所有者。 对于订阅所有者，在创建资源组时，将自动发生这种情况。 对于其他人，请预先创建资源组，并要求订阅所有者使您成为资源组的所有者。

您可以按照[有关基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/check-access)的说明来验证 Azure 门户中的访问权限。

### <a name="decide-subscription-and-region"></a>决定订阅和地区

您需要 Azure 订阅 ID 和要安装 Azure FarmBeats 的区域。 选择"[区域支持"](#regions-supported)部分中列出的区域之一。

记下**Azure 订阅 ID**和**Azure 区域**。

### <a name="create-an-aad-application"></a>创建 AAD 应用程序

Azure 服务器场节拍需要 Azure 活动目录应用程序创建和注册。 要成功运行 AAD 创建脚本，需要以下权限：

- 租户 - AAD 应用程序创建者
- 订阅 - 所有者

使用 PowerShell 环境在云壳实例中运行以下步骤。 将提示首次使用用户选择订阅并创建存储帐户。 按照指示完成设置。

1. 下载[AAD 应用程序生成器脚本](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 默认情况下，该文件将下载到您的主目录。 导航到目录。

    ```azurepowershell-interactive
        cd
    ```

3. 运行 AAD 脚本

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. 该脚本要求提供以下三个输入：

    - **FarmBeats 网站名称**： 这是您的 FarmBeats Web 应用程序的唯一 URL 前缀。 如果已获取前缀，则脚本将出错。安装后，您的 FarmBeats 部署将从https://\<FarmBeats 网站名称>.azure 网站访问，而摇曳的 API 将在 https://\<FarmBeats 网站名称>api.azurewebsites.net

    - **Azure 登录 ID**：为要添加为 FarmBeats 管理员的用户提供 Azure 登录 ID。 然后，此用户可以向其他用户授予访问 FarmBeats Web 应用程序的权限。 登录 ID 通常是窗体john.doe@domain.com中的 。 Azure UPN 也受支持。

    - **订阅 ID**：这是要在其中安装 Azure FarmBeats 的订阅 ID

5. AAD 脚本大约需要 2 分钟才能运行并输出屏幕上的值以及同一目录中的 json 文件。 如果您让其他人运行该脚本，请让他们与您共享此输出。

### <a name="create-sentinel-account"></a>创建哨兵帐户

Azure FarmBeats 设置使您能够从欧洲航天局的[Sentinel-2](https://scihub.copernicus.eu/)卫星任务获取卫星影像。 要配置此设置，您需要一个 Sentinel 帐户。

按照以下步骤创建一个免费帐户与哨兵：

1. 转到官方[注册](https://aka.ms/SentinelRegistration)页面。
2. 提供所需的详细信息（名字、姓氏、用户名、密码和电子邮件 ID），并填写表单。
3. 验证链接将发送到已注册的电子邮件 ID。 选择电子邮件中提供的链接并完成验证。

您的注册过程已完成。 记下您的**哨兵用户名和密码****，一**旦验证也完成。

## <a name="install"></a>安装

现在，您已准备好安装 FarmBeats。 按照以下步骤开始安装：

1. 登录到 Azure 门户。   在右上角选择您的帐户，然后切换到要安装 Azure FarmBeats 的 Azure AD 租户。

2. 转到门户中的 Azure 应用商店，并在应用商店中搜索**Azure FarmBeats。**

3. 将显示一个具有 Azure FarmBeats 概述的新窗口。 选择 **“创建”**。

4. 此时将显示新窗口。 通过选择要安装 Azure FarmBeats 的正确订阅、资源组和位置来完成注册过程。

5. 在 **"服务器场节拍服务警报**"部分中提供应接收与 Azure FarmBeats 相关的任何服务警报的电子邮件地址。 在页面底部选择 **"下一步**"以移动到 **"依赖项"** 选项卡。

    ![基础知识选项卡](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. 将单个条目从[AAD 脚本](#create-an-aad-application)的输出复制到 AAD 应用部分中的输入。

7. 在["哨兵帐户"部分输入"哨兵"帐户](#create-sentinel-account)用户名和密码。 选择 **"下一步**"以移动到"**审阅和创建**"选项卡。

    ![Dependencies Tab](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 验证输入的详细信息后，选择 **"确定**"。 将显示"使用条款"页。 查看术语并选择 **"创建**"以启动安装。 您将被重定向到可以跟踪安装进度的页面。

安装完成后，您可以通过导航到安装期间提供的网站名称来验证安装并开始使用 FarmBeats 门户：https://\<FarmBeats 网站名称>.azure 网站.net。 您应该会看到 FarmBeats 用户界面，并带有创建服务器场的选项。

**数据库**可以在https://\<FarmBeats 网站名称>-api.azure 网站.net/wagger 中找到。 在这里，您将看到不同的服务器场节拍 API 对象，并在 API 上执行 REST 操作。

## <a name="upgrade"></a>升级

要将 FarmBeats 升级到最新版本，请使用 PowerShell 环境在云壳实例中运行以下步骤。 用户需要是安装 FarmBeats 的订阅的所有者。

将提示首次使用用户选择订阅并创建存储帐户。 按照指示完成设置。

1. 下载[升级脚本](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 默认情况下，该文件将下载到您的主目录。 导航到目录。

    ```azurepowershell-interactive
        cd
    ```

3. 运行升级脚本

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

输入.json 文件的路径是可选的。 如果未指定，脚本将要求所有必需的输入。 升级应在大约 30 分钟内完成。

## <a name="uninstall"></a>卸载

要卸载 Azure 服务器场数据集或加速器，请完成以下步骤：

1. 登录到 Azure 门户并删除安装这些组件**的资源组**。

2. 转到 Azure 活动目录&删除链接到 Azure FarmBeats 安装的**Azure AD 应用程序**。

## <a name="next-steps"></a>后续步骤

您已经了解如何在 Azure 订阅中安装 Azure FarmBeats。 现在，了解如何[将用户添加到](manage-users-in-azure-farmbeats.md#manage-users)Azure FarmBeats 实例。
