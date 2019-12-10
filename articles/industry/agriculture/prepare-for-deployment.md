---
title: 部署 Azure FarmBeats
description: 本文介绍如何部署 Azure FarmBeats。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941570"
---
# <a name="deploy-azure-farmbeats"></a>部署 Azure FarmBeats

本文介绍如何设置 Azure FarmBeats。

Azure FarmBeats 是一个行业特定的可扩展解决方案，适用于数据驱动的场，可实现到 Azure 云、遥测数据收集和聚合的无缝预配和传感器设备连接。 Azure FarmBeats 包括多种传感器，如相机、无人机和土料传感器。 使用 Azure FarmBeats，你可以从云管理设备，其中包括在 Azure 中管理用于 IoT 的设备的基础结构和服务，以及使用可扩展的 web 和移动应用提供可视化、警报和见解。

> [!NOTE]
> Azure FarmBeats 仅在公有云环境中受支持。 有关详细信息，请参阅[什么是公有云？](https://azure.microsoft.com/overview/what-is-a-public-cloud/)。

Azure FarmBeats 包含以下两个组件：

- **Datahub**： Azure FarmBeats 的平台层，可让你生成、存储、处理数据，并从现有或新数据管道中提取见解。 此平台层可用于运行和生成农业数据管道和模型。

- **加速器**： Azure FarmBeats 的解决方案层，其中包含一个内置的应用程序，用于演示使用预先创建的农业模型的 Azure FarmBeats 的功能。 利用此解决方案层，你可以创建场边界，并在场边界上下文中绘制来自农业数据的见解。

Azure FarmBeats 的快速部署应花费不到一小时。 Datahub 和加速器的成本会有所不同，具体取决于使用情况。

## <a name="deployed-resources"></a>部署的资源

Azure FarmBeats 部署在订阅中创建以下资源：

| 串行. 编号  | 资源名称  | FarmBeats 组件  |
|---------|---------|---------|
|第  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/加速器     |
|3  |用于 Redis 的 Azure 缓存   |Datahub   |
|4  |       Azure 密钥保管库    |  Datahub/加速器        |
|5  |    Azure 时序见解       |     Datahub      |
|6 |      Azure 事件中心命名空间    |  Datahub       |
|7  |    Azure 数据工厂 V2       |     Datahub/加速器      |
|8  |批处理帐户    |Datahub   |
|9  |       存储帐户     |  Datahub/加速器        |
|10  |    逻辑应用        |     Datahub      |
|11  |    API 连接        |     Datahub      |
|12|      Azure App Service      |  Datahub/加速器       |
|13 |    应用服务计划        |     Datahub/加速器      |
|14 |Azure Maps 帐户     |加速器    |

可从 Azure Marketplace 下载 azure FarmBeats，可以直接从 Azure 门户访问。  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>在 Azure Marketplace 中创建 Azure FarmBeats 产品/服务

若要在 Azure Marketplace 中创建 Azure FarmBeats 产品/服务，请执行以下操作：

1. 登录到 Azure 门户，在右上角选择帐户，然后切换到要在其中部署 Azure FarmBeats 的 Azure Active Directory （Azure AD）租户。
1. Azure Marketplace 中提供了 azure FarmBeats。 在 " **Azure Marketplace** " 页上，选择 "**立即获取**"。
1. 选择**创建**。

> [!NOTE]
> Azure Marketplace 中的产品/服务的完成只是安装过程的一部分。 若要在 Azure 订阅中完成 Azure FarmBeats 的部署，请按照后续部分中的说明进行操作。

## <a name="prepare"></a>准备

在部署 Azure FarmBeats 之前，你需要具备以下权限：

- **租户**：读取访问权限
- **订阅**：参与者或所有者
- **资源组**：所有者

## <a name="before-you-begin"></a>开始之前

在开始部署之前，请确保满足以下先决条件：

- 一个 Sentinel 帐户
- Azure AD 应用注册

## <a name="create-a-sentinel-account"></a>创建一个 Sentinel 帐户    

带有 Sentinel 的帐户可帮助你从其官方网站下载指向你的设备的 Sentinel 卫星电视。 若要创建免费帐户，请执行以下操作：

1. 请参阅 " [Sentinel 帐户注册" 页](https://scihub.copernicus.eu/dhus/#/self-registration)。
1. 在注册窗体上，提供名字、姓氏、用户名、密码和电子邮件地址。

验证电子邮件将发送到已注册的电子邮件地址进行确认。 选择用于确认电子邮件地址的链接。 注册过程已完成。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

若要在 Azure FarmBeats 上进行身份验证和授权，必须注册 Azure AD 应用。 可以通过以下两种方式之一创建它：

* **选项 1**：安装程序可以自动创建注册，前提是你拥有所需的租户、订阅和资源组访问权限。 如果这些权限已准备就绪，请继续阅读["准备输入 json 文件"](#prepare-the-inputjson-file)部分。

* **选项 2**：可以在部署 Azure FarmBeats 之前手动创建和配置注册。 如果你没有在订阅中创建和配置 Azure AD 应用注册所需的权限，我们建议使用此方法。 要求管理员使用[自定义脚本](https://aka.ms/FarmBeatsAADScript)，这将帮助 IT 管理员在 Azure 门户上自动生成和配置 Azure AD 应用注册。 作为使用 PowerShell 环境运行此自定义脚本的输出，IT 管理员需要与你共享一个 Azure AD 应用程序客户端 ID 和密码机密。 记下这些值。

    若要运行 Azure AD 应用注册脚本，请执行以下操作：

    1. 下载[脚本](https://aka.ms/FarmBeatsAADScript)。
    1. 登录到 Azure 门户，并选择你的订阅和 Azure AD 租户。
    1. 从 Azure 门户顶部的工具栏启动 Cloud Shell。

        ![项目 FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

    1. 如果你是首次使用的用户，系统会提示你选择一个订阅来创建存储帐户，并 Microsoft Azure 文件共享。 选择“创建存储”。
    1. 首次使用用户时，还可以选择 shell 体验、Bash 或 PowerShell。 选择 "PowerShell"。
    1. 将脚本（从步骤1）上载到 Cloud Shell，并记下上传的文件的位置。

        > [!NOTE]
        > 默认情况下，该文件将上传到主目录。

        使用以下脚本：

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. 记下 Azure AD 的应用程序 ID 和客户端密码，与部署 Azure FarmBeats 的人员共享。

### <a name="prepare-the-inputjson-file"></a>准备输入 json 文件

在安装过程中，创建一个*输入 json*文件，如下所示：

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

此文件是要 Azure Cloud Shell 的输入文件。 它包含在安装过程中使用其值的参数。 JSON 文件中的所有参数都需要替换为适当的值或删除。 删除后，安装程序会在安装过程中提示您。

准备文件之前，请查看下表中的参数：

|命令 | 描述|
|--- | ---|
|sku  | 提供下载 Azure FarmBeats 的一个或两个组件的选项。 它指定要下载的组件。 若要仅安装 Datahub，请使用 "onlydatabhub"。 若要安装 Datahub 和加速器，请使用 "both"。|
|subscriptionId | 指定用于安装 Azure FarmBeats 的订阅。|
|datahubResourceGroup| Datahub 资源的资源组名称。|
|位置 |要在其中创建资源的位置。|
|acceleratorWebsiteName |命名你的 Datahub 实例的唯一 URL 前缀。|
|acceleratorResourceGroup  | 命名加速器网站的唯一 URL 前缀。|
|datahubWebsiteName  | 命名你的 Datahub 网站的唯一 URL 前缀。 |
|sentinelUsername | 用于登录的用户名（例如 `https://scihub.copernicus.eu/dhus/#/self-registration`）。|
|notificationEmailAddress  | 用于接收在 Datahub 实例中配置的任何警报的通知的电子邮件地址。|
|updateIfExists| 可有可无仅当你想要升级现有的 Azure FarmBeats 实例时，才在*输入 json*文件中包含参数。 对于升级，其他详细信息（如资源组名称和位置）必须相同。|
|aadAppClientId | 可有可无仅当 Azure AD 应用已存在时，才在*输入 json*文件中包含参数。  |
|aadAppClientSecret  | 可有可无仅当 Azure AD 应用已存在时，才在*输入 json*文件中包含参数。|

## <a name="deploy-the-app-within-cloud-shell"></a>在 Cloud Shell 中部署应用

作为前面讨论的 marketplace 工作流的一部分，你必须已创建一个资源组并签署了 "许可条款" 协议，该协议可以在实际部署中再次查看。 可以通过使用 Bash 环境通过基于 Cloud Shell 浏览器的命令行界面来部署应用。 若要通过 Cloud Shell 进行部署，请转到下一节。

> [!NOTE]
> 非活动 Cloud Shell 会话在20分钟后过期。 尝试在这段时间内完成部署。

1. 登录到 Azure 门户，并选择要使用的订阅和 Azure 租户。
1. 从 Azure 门户顶部的工具栏启动 Cloud Shell。
1. 如果你是首次使用 Cloud Shell，系统将提示你选择一个订阅来创建存储帐户和 Azure 文件共享。
1. 选择 "**创建存储**"。  
1. 对于环境，请选择 " **Bash** （非 PowerShell）"。

## <a name="deployment-scenario-1"></a>部署方案1

在这种情况下，在 "选项 1" 中，安装程序会自动创建 Azure AD 应用注册。 通过执行以下操作来部署 FarmBeats：

1. 复制下面的示例 JSON 模板，并将其另存为*输入. JSON*。 请确保记下本地计算机上的文件路径。

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. 打开 Azure Cloud Shell。 身份验证成功后，选择 "**上传**" 按钮（在下图中突出显示），然后将*输入的 json*文件上传到 Cloud Shell 存储。  

    ![项目 FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. 在 Cloud Shell 中转到您的主目录。 默认情况下，目录名称为 */home/\<用户名 >* 。
1. 在 Cloud Shell 中，输入以下命令。 务必修改*输入*文件的路径，然后选择 Enter 键。

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     安装程序会自动下载所有依赖项，并生成部署的。 系统将提示你同意 Azure FarmBeats 许可条款。

     - 如果同意，请输入**Y**以继续下一步。
     - 如果你不同意，请输入**N**，将终止部署。

1. 在提示符下，输入部署的访问令牌。 复制生成的代码，然后用你的 Azure 凭据登录到[设备登录页](https://microsoft.com/devicelogin)。

    > [!NOTE]
    > 令牌将在60分钟后过期。 如果它过期，你可以重新键入部署命令来重新启动。

1. 在提示符下，输入你的 Sentinel 帐户密码。

   安装程序将验证并启动部署，这可能需要大约20分钟的时间。

   部署成功完成后，将收到以下输出链接：

    - **DATAHUB URL**：用于尝试 Azure FarmBeats Api 的 Swagger 链接。
    - **快捷键 URL**：用于浏览 Azure FarmBeats 智能场加速器的用户界面。
    - 部署过程中创建的**日志文件。** 如果需要，可以使用它进行故障排除。

## <a name="deployment-scenario-2"></a>部署方案2

在这种情况下（在 "选项 2" 的前面部分中介绍），可以通过执行以下操作，使用现有 Azure Active Directory 应用注册来部署 FarmBeats：

1. 复制以下 JSON 文件，其中包含*输入*文件中的 Azure 应用程序客户端 ID 和密码，并保存。 请确保记下本地计算机上的文件路径。

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. 再次跳到 Azure Cloud Shell，你可以成功地进行身份验证。
1. 选择 "**上传**" 按钮（在下图中突出显示），然后将*输入的 json*文件上传到 Cloud Shell 存储。

    ![项目 FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. 在 Cloud Shell 中转到您的主目录。 默认情况下，目录名称为 */home/\<用户名 >* 。
1. 在 Cloud Shell 中，输入以下命令。 务必修改*输入*文件的路径，然后选择 Enter 键。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     安装程序会自动下载所有依赖项，并生成部署的。 系统将提示你同意 Azure FarmBeats 许可条款。

     - 如果同意，请输入**Y**以继续下一步。
     - 如果你不同意，请输入**N**，将终止部署。

1. 在提示符下，输入部署的访问令牌。 复制生成的代码，然后用你的 Azure 凭据登录到[设备登录页](https://microsoft.com/devicelogin)。

   安装程序将验证并启动部署，这可能需要大约20分钟的时间。

   部署成功完成后，将收到以下输出链接：

    - **DATAHUB URL**：用于尝试 Azure FarmBeats Api 的 Swagger 链接。
    - **快捷键 URL**：用于浏览 Azure FarmBeats 智能场加速器的用户界面。
    - 部署过程中创建的**日志文件。** 如果需要，可以使用它进行故障排除。

如果遇到任何问题，请查看[故障排除](troubleshoot-project-farmbeats.md)。


## <a name="validate-the-deployment"></a>验证部署

### <a name="datahub"></a>Datahub

Datahub 安装完成后，将收到 URL，以便通过 Swagger 接口访问 Azure FarmBeats Api，格式为 https://\<yourdatahub-name >. appname>.azurewebsites.net/Swagger。

1. 若要通过 Swagger 登录，请在浏览器中复制并粘贴该 URL。
2. 用 Azure 门户凭据登录。
3. 可以在 Azure FarmBeats Api 上查看 swagger 并执行所有 REST 操作。 这表示成功部署 Azure FarmBeats。

### <a name="accelerator"></a>加速器

加速器安装完成后，你将收到 URL，该 URL 用于访问 Azure FarmBeats 用户界面，格式为 https://\<加速器->。

1. 若要从加速器登录，请在浏览器中复制并粘贴该 URL。
1. 用 Azure 门户凭据登录。

## <a name="upgrade"></a>升级

升级说明类似于首次安装的说明。 请执行以下操作：

1. 登录到 Azure 门户，然后选择所需的订阅和 Azure 租户。
1. 打开 Azure 门户顶部导航 Cloud Shell。

   ![项目 FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

1. 在左侧的 "**环境**" 下拉列表中，选择 " **Bash**"。
1. 如有必要，请更改你的*输入 json*文件，然后将其上传到 Cloud Shell。 例如，你可以更新你要接收的通知的电子邮件地址。
1. 将以下两个命令键入或粘贴到 Cloud Shell 中。 务必修改*输入的 json*文件路径，然后选择 Enter 键。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   安装程序会在运行时自动提示输入所需的输入。

1. 输入用于部署的访问令牌。 复制生成的代码，并通过 Azure 凭据登录到[设备登录页](https://microsoft.com/devicelogin)。
1. 输入 "Sentinel 密码"。

   安装程序现在将验证并开始创建资源，这可能需要大约20分钟的时间。

   部署成功完成后，将收到以下输出链接：
    - **DATAHUB URL**：用于尝试 Azure FarmBeats Api 的 Swagger 链接。
    - **快捷键 URL**：用于浏览 Azure FarmBeats 智能场加速器的用户界面。
    - 部署过程中创建的**日志文件。** 如果需要，可以使用它进行故障排除。

> [!NOTE]
> 记下上述值以便将来使用。


## <a name="uninstall"></a>卸载

目前，我们不支持使用安装程序自动卸载 Azure FarmBeats。 若要删除 Datahub 或加速器，请在 Azure 门户中删除安装了这些组件的资源组，或手动删除这些资源。

例如，如果在两个不同的资源组中部署了 Datahub 和加速器，则可按如下所示删除这些资源组：

1. 登录到 Azure 门户。
1. 在右上角选择帐户，并切换到要在其中部署 Azure FarmBeats 的 Azure AD 租户。

   > [!NOTE]
   > 要使加速器正常工作，Datahub 是必需的。 不建议在不卸载加速器的情况下卸载 Datahub。

1. 选择 "**资源组**"，然后输入要删除的 Datahub 或加速器资源组的名称。
1. 选择资源组名称。 重新键入名称进行确认，然后选择 "**删除**" 以删除资源组及其所有基础资源。
   或者，您可以手动删除每个资源，这是我们不建议使用的方法。
1. 若要删除或卸载 Datahub，请直接在 Azure 上找到资源组，然后从此处删除资源组。

## <a name="next-steps"></a>后续步骤

已了解如何部署 Azure FarmBeats。 接下来，了解如何[创建 FarmBeats 场](manage-farms.md#create-farms)。
