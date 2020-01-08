---
title: 安装 Azure FarmBeats
description: 本文介绍如何在 Azure 订阅中安装 Azure FarmBeats
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482447"
---
# <a name="install-azure-farmbeats"></a>安装 Azure FarmBeats

本文介绍如何在 Azure 订阅中安装 Azure FarmBeats。

Azure FarmBeats 是 Azure Marketplace 中提供的企业到企业产品。 它可跨提供商和可操作见解的生成来聚合农业数据集。 Azure FarmBeats 通过使你能够基于融合的数据集构建人工智能（AI）或机器学习（ML）模型，来实现此功能。 Azure FarmBeats 的两个主要组件是：

- **Datahub**：一种 API 层，可跨不同的提供程序跨各种农业数据集进行聚合、标准化和 contextualization。

- **加速器**：在 Datahub 之上构建的示例 web 应用程序。 它将快速启动模型开发和可视化。 该加速器使用 Azure FarmBeats Api 将引入传感器数据的可视化效果演示为作为地图的模型输出的图表和可视化效果。

## <a name="before-you-start"></a>开始之前
### <a name="components-installed"></a>已安装组件

安装 Azure FarmBeats 时，会在 Azure 订阅中预配以下资源：

| 已安装 Azure 资源  | Azure FarmBeats 组件  |
|---------|---------|
| Application Insights   |      Datahub & 加速器      |
| 应用服务     |     Datahub & 加速器     |
| 应用服务计划   | Datahub & 加速器  |
| API 连接    |  Datahub       |
| 用于 Redis 的 Azure 缓存       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure 数据工厂 V2       |     Datahub & 加速器      |
| Azure Batch 帐户    | Datahub   |
| Azure Key Vault |  Datahub & 加速器        |
| Azure Maps 帐户       |     加速器    |
| 事件中心命名空间    |     Datahub      |
| 逻辑应用程序      |  Datahub       |
| 存储器帐户      |     Datahub & 加速器      |
| 时序见解     |    Datahub    |

### <a name="costs-incurred"></a>产生的成本

Azure FarmBeats 的成本是底层 Azure 服务的成本的聚合。 可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator)来计算 Azure 服务的定价信息。 总体安装的实际成本将根据使用情况而有所不同。 这两个组件的稳定状态为：

* Datahub-每天小于 $10
* 加速器-小于 $2/天

### <a name="regions-supported"></a>支持的区域

目前，Azure FarmBeats 在以下区域的公有云环境中受支持：
* 澳大利亚东部
* 美国中部
* 美国东部
* 美国东部 2
* 美国西部
* 美国西部 2
* 欧洲北部
* 欧洲西部
* 亚洲东南部

### <a name="time-taken"></a>所用时间

Azure FarmBeats 的整个设置，包括准备和安装所需的时间不到1小时。

## <a name="prerequisites"></a>必备组件    

在开始实际安装 Azure FarmBeats 之前，需要完成以下步骤：

### <a name="create-sentinel-account"></a>创建 Sentinel 帐户
你的 Azure FarmBeats 设置使你可以从你的场的欧洲空间代理商的[Sentinel](https://scihub.copernicus.eu/)卫星任务中获取免费的卫星电视。 若要配置此设置，需要一个 Sentinel 帐户。

按照以下步骤使用 Sentinel 创建免费帐户：

1. 请参阅官方[注册](https://scihub.copernicus.eu/dhus/#/self-registration)页。
2. 提供所需的详细信息（名字、姓氏、用户名、密码和电子邮件 ID）并填写表单。
3. 验证链接将发送到已注册的电子邮件 ID。 选择电子邮件中提供的链接并完成验证。

完成验证后，注册过程即已完成。 记下**Sentinel 用户名**和**sentinel 密码**。

### <a name="decide-subscription-and-region"></a>确定订阅和区域

需要 Azure 订阅 ID 和要安装 Azure FarmBeats 的区域。 选择 "[支持的区域](#regions-supported)" 部分中列出的区域之一。

请记下**Azure 订阅 ID**和**azure 区域**。

### <a name="verify-permissions"></a>验证权限

你需要在想要安装 Azure FarmBeats 的 Azure 租户中验证是否有足够的权限。

可以按照[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/check-access)上的说明验证 Azure 门户中的访问权限

若要安装 Azure FarmBeats，需要具备以下权限：
- 租户-读取访问权限
- 订阅-参与者或所有者
- 资源组-所有者。

此外，Azure FarmBeats 需要 Azure Active Directory 的应用程序注册。 可以通过两种方法来完成所需的 Azure AD 安装程序：

**情况 1**：你在要安装到的 Azure 租户中具有**写入**权限。 这种情况意味着，在安装过程中，你拥有动态创建 AAD 应用注册所需的权限。

你可以直接转到[完整的 Marketplace 注册](#complete-azure-marketplace-sign-up)部分。


**案例 2**：在 Azure 租户中没有**写入**权限。 这种情况常见于您尝试在公司的 Azure 订阅中安装 Azure FarmBeats，而您的**写**访问仅限于您拥有的资源组。
在这种情况下，请求你的 IT 管理员按照以下步骤在 Azure 门户中自动生成并完成 Azure AD 应用注册。

1. 下载[AAD 应用生成器脚本](https://aka.ms/FarmBeatsAADScript)并将其提取到本地计算机。
2. 登录到 Azure 门户并选择你的订阅和 Azure AD 租户。
3. 从 Azure 门户顶部的工具栏启动 Cloud Shell。

    ![项目 FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. 选择 "PowerShell" 作为首选 shell 体验。 首次将提示用户选择订阅并创建存储帐户。 按照说明完成安装。
5. 将脚本（从步骤1）上载到 Cloud Shell，并记下上传的文件的位置。

    > [!NOTE]
    > 默认情况下，该文件将上传到主目录。

6. 使用 "cd" 命令转到主目录，并运行以下脚本：

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. 输入**Datahub 网站**名称和**加速器网站**名称。 记录脚本的输出，并将其与安装 Azure FarmBeats 的人员共享。

IT 管理员提供所需的详细信息后，请记下**Aad 客户端 ID、Aad 客户端密码、Datahub 网站名称 & 加速器网站名称**。

   > [!NOTE]
   > 如果按照情况2中的说明进行操作，请不要忘记添加 AAD 客户端 ID & AAD 客户端密钥作为[参数文件](#prepare-parameters-file)中的单独参数

现在，你已拥有继续下一部分所需的所有信息。

### <a name="complete-azure-marketplace-sign-up"></a>完成 Azure Marketplace 注册

必须先完成 Azure marketplace 中的 Azure FarmBeats 产品注册，然后才能开始基于云的安装过程。 按照以下步骤完成注册：

1.  登录到 Azure 门户。 在右上角选择你的帐户，并切换到要在其中安装 Azure FarmBeats 的 Azure AD 租户。

2.  在门户中中转到 Azure Marketplace，并在 Marketplace 中搜索**Azure FarmBeats**

3.  此时将显示一个新窗口，其中显示了 Azure FarmBeats 的概述。 选择“创建”。

4.  新窗口随即出现。 通过选择正确的订阅、资源组和要将 Azure FarmBeats 安装到的位置来完成注册过程。

5.  验证输入的详细信息后，选择 **"确定"** 。 此时将显示 "使用条款" 页。 查看条款，然后选择 "**创建**" 以完成注册过程。

此步骤完成 Azure Marketplace 中的注册过程。 你现在已准备好开始参数文件准备工作。

### <a name="prepare-parameters-file"></a>准备参数文件
必备阶段中的最后一步是创建一个 JSON 文件，该文件将在 Cloud Shell 安装期间用作输入。 必须将 JSON 文件中的参数替换为适当的值。

下面提供了一个示例 JSON 文件。 下载示例并更新所需的详细信息。

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

可以参考以下参数表来了解每个参数的详细信息。

| 参数 | 值|
|--- | ---|
|sku  | 为用户提供安装 Datahub 和加速器的选项，或仅安装 Datahub 的选项。 若要仅安装 Datahub，请使用 "Datahub"。 若要安装 Datahub 和加速器，请使用 "both"|
|subscriptionId | 指定 Azure 订阅以安装 Azure FarmBeats|
|datahubResourceGroup| 指定 Datahub 资源的资源组名称。 在此处输入在 Azure Marketplace 中创建的资源组名称|
|location |要在其中安装 Azure FarmBeats 的位置/Azure 区域|
|datahubWebsiteName  | Datahub web 应用程序的唯一 URL 前缀 |
|acceleratorResourceGroup  | 指定加速器资源的资源组名称|
|acceleratorWebsiteName |加速器 web 应用程序的唯一 URL 前缀|
|sentinelUsername | 用于获取 Sentinel 附属图像的用户名|
|notificationEmailAddress  | 用于接收在 Datahub 中配置的任何警报的通知的电子邮件地址|
|updateIfExists| 可有可无仅当要升级现有的 Azure FarmBeats 实例时，才在参数文件中包含参数。 对于升级，其他详细信息（如资源组名称和位置）必须相同|
|aadAppClientId | 可有可无仅当使用预先创建的 AAD 应用时，才在参数文件中包含参数。 有关更多详细信息，请参阅[验证权限](#verify-permissions)部分中的案例2 |
|aadAppClientSecret  | 可有可无可有可无仅当使用预先创建的 AAD 应用时，才在参数文件中包含参数。 有关更多详细信息，请参阅[验证权限](#verify-permissions)部分中的案例2|

根据上表和示例 JSON 文件，创建参数 JSON 文件并将其保存在本地计算机中。

## <a name="install"></a>安装

Azure FarmBeats 资源的实际安装在基于浏览器的命令行界面中使用 Bash 环境 Cloud Shell。 请按照下面的说明安装 Azure FarmBeats：

1. 登录到 Azure 门户。 选择要安装 Azure FarmBeats 的 Azure 订阅和租户。
2. 从 Azure 门户右上角的工具栏中启动**Cloud Shell** 。
3. 选择 Bash 作为首选 shell 体验。 选择 "**上传**" 按钮（在下图中突出显示），并上传已准备的参数 JSON 文件。

      ![项目 FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. **复制**以下命令，并将 **\<用户名 > 替换**为正确的值，以便该命令指向上传的文件的正确路径。

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. 运行修改后的命令来启动安装过程。 系统将提示你：
 - 同意**Azure FarmBeats 许可**条款。 如果你同意使用条款，请输入 "Y" 以继续下一步。 如果你不同意使用条款，请输入 "N" 终止安装。

 - 接下来，系统将提示你输入用于安装的访问令牌。 复制生成的代码，并通过**Azure 凭据**登录到[设备登录页](https://microsoft.com/devicelogin)。

 - 登录成功完成后，安装程序将提示你提供标记帐户密码。 输入 " **Sentinel 帐户密码**"。

6. 验证参数文件，并启动 Azure 资源的安装。 安装大约需要**25 分钟**才能完成。    
> [!NOTE]
> 非活动 Cloud Shell 会话在**20 分钟**后过期。 安装程序正在部署 Azure 资源时，请使 Cloud Shell 会话保持活动状态。 如果会话超时，则必须重新启动安装过程。

安装完成后，您将收到以下输出链接：
* **DATAHUB URL**：用于访问 Datahub Api 的 Swagger 链接。
* **快捷键 URL**：用于浏览 Azure FarmBeats 加速器的 web 应用程序。
* **安装程序日志文件**：包含安装详细信息的日志文件。 如果需要，此日志文件可用于对安装进行故障排除。

可以通过执行以下检查来验证 Azure FarmBeats 安装是否已完成：

**Datahub**
1. 用你的 Azure 凭据登录到提供的快捷键 URL （格式为**https://\<> yourdatahub/swagger**）。
2. 你应该能够看到不同的 FarmBeats API 对象并对 Api 执行 REST 操作。

**Accelerator**
1. 用你的 Azure 凭据登录到提供的快捷键 URL （格式为**https://\<> youraccelerator/swagger**）。
2. 你应该可以在浏览器中查看加速器用户界面，其中包含用于创建场的选项。

执行上述操作的能力指示成功安装 Azure FarmBeats。

## <a name="upgrade"></a>升级
在公共预览版中，若要升级 Azure FarmBeats 的现有安装，你必须在 Cloud Shell 中再次运行安装命令，并将 "参数" 文件中的 "**updateIfExists**" 参数设置为 "**true**"。 有关 update 参数，请参阅下面的 JSON 示例的最后一行。

```json
{
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```
此命令会将现有的 Azure FarmBeats 安装更新到最新版本，并向你提供输出链接。

## <a name="uninstall"></a>卸载

若要卸载 Azure FarmBeats Datahub 或加速器，请完成以下步骤：

1.  登录到 Azure 门户并删除安装了这些组件**的资源组**。

2.  请参阅 Azure Active Directory & 删除链接到 Azure FarmBeats 安装**的 Azure AD 应用程序**。 这会从 Azure 订阅中删除 Azure FarmBeats 安装。

## <a name="next-steps"></a>后续步骤
已了解如何在 Azure 订阅中安装 Azure FarmBeats。 现在，了解如何[将用户添加](manage-users-in-azure-farmbeats.md#manage-users)到 Azure FarmBeats 实例。
