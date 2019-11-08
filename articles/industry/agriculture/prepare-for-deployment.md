---
title: 部署 Azure FarmBeats
description: 介绍如何部署 FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797720"
---
# <a name="deploy-farmbeats"></a>部署 FarmBeats

本文介绍如何设置 Azure FarmBeats。

Azure FarmBeats 是一个行业特定的可扩展解决方案，适用于数据驱动的场，可实现到 Azure 云、遥测数据收集和聚合的无缝预配和传感器设备连接。 Azure FarmBeats 具有各种传感器（如相机、无人机、土里传感器）以及设备与云的管理，其中包括 Azure 中的基础结构和服务，物联网用于可扩展 web 和移动应用程序，以提供可视化、警报和见解。

> [!NOTE]
> Azure FarmBeats 仅在公有云环境中受支持。 有关云环境的详细信息，请参阅[Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/)。

Azure FarmBeats 包含以下两个组件：

- **数据**中心-数据中心是 Azure FarmBeats 的平台层，可让你生成、存储、处理数据，并从现有或新数据管道中提取见解。 此平台层可用于运行和生成农业数据管道和模型。

- **加速器**-加速器是 azure FarmBeats 的解决方案层，它具有内置的应用程序，用于说明 Azure FarmBeats 使用预先创建的农业模型的功能。 利用此解决方案层，你可以创建场边界，并在场边界上下文中绘制来自农业数据的见解。

Azure FarmBeats 的快速部署应花费不到一小时。 数据中心和加速器的成本根据使用情况而有所不同。

## <a name="deployed-resources"></a>部署的资源

Azure FarmBeats 部署在订阅中创建以下列出的资源：

|S.No  |资源名称  |Azure FarmBeats 组件  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  数据中心       |
|2  |    Application Insights      |     数据中心/加速器     |
|3  |用于 Redis 的 Azure 缓存   |数据中心   |
|4  |       Azure KeyVault    |  数据中心/加速器        |
|5  |    时序见解       |     数据中心      |
|6 |      EventHub 命名空间    |  数据中心       |
|7  |    Azure 数据工厂 V2       |     数据中心/加速器      |
|8  |批处理帐户    |数据中心   |
|9  |       存储帐户     |  数据中心/加速器        |
|10  |    逻辑应用        |     数据中心      |
|11  |    API 连接        |     数据中心      |
|12|      应用服务      |  数据中心/加速器       |
|13 |    应用服务计划        |     数据中心/加速器      |
|14 |Azure Maps 帐户     |键    |
|15 |       时序见解      |  数据中心     |

可从 Azure Marketplace 下载 azure FarmBeats。 可以直接从 Azure 门户访问它。  

## <a name="prepare"></a>准备

需要以下权限才能部署 Azure FarmBeats：

- 租户：读取访问权限
- 订阅：所有者的参与者
- 资源组：所有者

## <a name="before-you-begin"></a>开始之前

在开始部署之前，请确保具备以下各项：

- Sentinel 帐户
- Azure Active Directory （应用注册）
- Azure FarmBeats

## <a name="create-a-sentinel-account"></a>创建一个 sentinel 帐户    

带有 sentinel 的帐户可帮助你从其官方网站下载指向你的设备的 sentinel 卫星电视。 按照以下步骤创建免费帐户：

1. 转到  https://scihub.copernicus.eu/dhus/#/self-registration 。 在注册页中，提供名字、姓氏、用户名、密码和电子邮件。
2. 在 "**选择域**" 下拉菜单中，选择 "**土地**" 选项。
3. 在 "**选择用法**" 下拉菜单中，选择 "**教育**" 选项。
4. 在 "**选择国家/地区**" 下拉菜单中，选择你所在的国家/地区。
5. 选择 "**注册**" 以完成注册过程。

验证电子邮件将发送到已注册的电子邮件地址进行确认。 选择该链接并确认。 注册过程已完成。

## <a name="create-azure-ad-app-registration"></a>创建 Azure AD 应用注册

若要在 Azure FarmBeats 上进行身份验证和授权，必须具有 Azure active directory 应用程序注册：

- 情况1：安装程序可以自动创建（前提是你有必需的租户、订阅和资源组访问权限）。
- 案例2：可以在部署 Azure FarmBeats 之前创建和配置（需要手动步骤）。

**情况 1**：安装程序假定您有权在所需的订阅中创建 Azure active directory 应用程序注册。 若要注册，请登录到门户，并在 > **新注册** > **应用注册**中转到**Azure active directory** 。

如果你已有订阅，则可以直接移到下一个过程。

**案例 2**：如果你没有足够的权限在你的订阅中创建和配置 Azure AD 应用注册，则此方法是首选步骤。 请求管理员使用[自定义脚本](https://aka.ms/FarmBeatsAADScript)，这将帮助 IT 管理员在 Azure 门户上自动生成和配置 Azure AD 应用注册。 作为使用 PowerShell 环境运行此自定义脚本的输出，IT 管理员需要与你共享一个 Azure Active Directory 应用程序客户端 ID 和密码机密。 记下这些值。

使用以下步骤运行 Azure AD 应用程序注册脚本：

1. 获取注册[脚本](https://aka.ms/FarmBeatsAADScript)。
2. 登录到 Azure 门户并选择你的订阅和 AD 租户。
3. 从 Azure 门户的顶部导航栏启动 Cloud Shell。

    ![项目场节拍](./media/prepare-for-deployment/navigation-bar-1.png)


4. 系统将提示首次用户选择订阅，以创建存储帐户和 Microsoft Azure 文件共享。 选择“创建存储”。
5. 首次提示用户选择首选 shell 体验-Bash 或 PowerShell。 选择 "PowerShell"。
6. 在 Cloud Shell 输入以下命令以运行脚本。

    ```powershell
    ./create_aad_script.ps1
    ```
7. 记下 Azure AD 的应用程序 ID 和客户端密码以与部署 Azure FarmBeats 的人员共享。

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>在 marketplace 上创建 Azure FarmBeats 优惠

使用以下步骤在 marketplace 中创建 Azure FarmBeats 产品/服务：

1. 登录到**Azure 门户**并在右上角选择你的帐户，并切换到要在其中部署 Microsoft Azure FarmBeats 的 Azure AD 租户。
2. 选择 "**搜索/Marketplace** " 或 "**创建资源**"。 键入**FarmBeats**以查看产品/服务详细信息。 在继续执行后续步骤之前，请下载通过 aka.ms 超链接提供的指南。
3. 选择 "**创建**"，并输入以下信息：

   - 输入订阅名称。
   - 输入现有的资源组名称（仅限空资源组），或创建新的资源组用于部署 Azure FarmBeats。 请记下此资源组，以便在后面的输入 json 文件中输入。
   - 输入要将 Azure FarmBeats 安装到的区域。 FarmBeats 当前可以安装在以下区域：美国中部、西欧、美国东部2、北欧、美国西部、东南亚、美国东部、澳大利亚东部、美国西部2。
4. 选择 **"确定"** ，这会将您重定向到使用条款页。 查看标准市场条款，或选择超链接以查看使用条款。
5. 选择 "**关闭**"，然后选择 "我同意" 复选框，然后选择 "**创建**"。
6. 现已成功在 marketplace 上签署 Azure FarmBeats 的最终用户许可协议（EULA）。 若要继续进行部署，请按照本指南中的后续步骤进行操作。

### <a name="prepare-inputjson-file"></a>准备输入 Json 文件

此文件是要 Azure Cloud Shell 的输入文件，以及在部署期间不提示上载之前在此文件中指定其值的参数，因此请节省一些时间。  

> [!NOTE]
> 此文件输入要 Azure Cloud Shell 的值。  为了节省时间，在部署过程中，不会提示你输入添加到此文件的参数。 系统将提示你输入缺少的参数。

准备文件前，请检查参数。

|命令 | 说明|
|--- | ---|
|限量  | 提供一个选项，用于下载 Azure FarmBeats 的其中一个组件或两个组件。 指定要下载的组件。 若要仅安装数据中心，请使用 "onlydatabhub"。 若要安装数据中心和加速器，请使用 "both"。|
|订阅  | 指定用于安装 FarmBeats 的订阅|
|"datahubResourceGroup"  | 数据中心资源的资源组名称。|
|"datahubLocation" | 要存储数据中心资源的位置。|
|"acceleratorWebsiteName"  |命名你的数据中心的唯一 URL 前缀
Swagger 网站。 默认值为数据中心资源组名称。 按 enter 继续默认值。|
|"acceleratorResourceGroup"  | 数据中心资源的资源组名称。 |
|"acceleratorLocation"  | 数据中心资源的存储位置
"acceleratorWebsiteName"  | 用于命名加速器网站的唯一 URL 前缀。 默认值为加速器。 按 enter 继续默认值。|
|''sentinelUsername'' | 用于登录的用户名： https://scihub.copernicus.eu/dhus/#/self-registration。|
|"sentinelPassword"  | 用于登录的密码： https://scihub.copernicus.eu/dhus/#/self-registration。|
|"farmbeatsAppId"  | 要由团队 Azure FarmBeats 共享的值。  |
|"farmbeatsPassword"  | 要由团队 Azure FarmBeats 共享的值。|
|"notificationEmailAddress"  | 用于接收你在数据中心内配置的任何警报的通知的电子邮件地址。|
|"更新" aadAppClientId ""  |[**可选**]要包含在输入中的参数。仅当 Azure AD 应用程序已存在时才包含在 Json 中。  -True/False。 如果是首次安装，则为 False; 对于升级方案为 True。|
|"aadAppClientId"  | [**可选**]要包含在输入中的参数。仅当 Azure AD 应用程序已存在时才包含在 Json 中。  |
|"aadAppClientSecret"   | [**可选**]要包含在输入中的参数。仅当 Azure AD 应用程序已存在时才包含在 Json 中。|


示例 JSON 输入：

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>在 Cloud Shell 基于浏览器的命令行中部署

作为 marketplace 工作流的一部分，你已经创建了一个资源组并签署了最终用户许可协议，该协议可以在实际部署中再次检查一次。 部署将通过使用 Bash 环境 Azure Cloud Shell （基于浏览器的命令行）来完成。  

> [!NOTE]
> 非活动 Cloud Shell 会话在20分钟后过期。 尝试在这段时间内完成部署。

1. 登录到**Azure**门户，并选择所需的订阅和 AD 租户。
2. 从**Azure**门户的顶部导航开始**Cloud Shell** 。

   ![项目场节拍](./media/prepare-for-deployment/navigation-bar-1.png)

3. 选择用于创建存储帐户和 Microsoft Azure 文件共享的订阅。
4. 选择“创建存储”。
5. 从 shell 窗口左侧选择环境下拉框（Bash）。

   ![项目场节拍](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>部署方案1

安装程序将创建 Azure AD （你具有 AD 租户读取权限）。  

1. 下载[输入的 json](https://aka.ms/PPInputJsonTemplate)模板。 将 Azure 应用程序的客户端 ID 和密码包括在输入的 json 文件中，并保存该文件。
2. 在记事本中打开下载的文件，并通过输入值填充文件。

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
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > 你可以跳过此步骤，并且输入将在运行时提示。

3. 保存该文件，并记下该路径（在本地计算机上）。  
4. 请访问 Azure Cloud Shell 并在身份验证成功后，选择 "上传" （请参阅下图中突出显示的图标），并将输入的 json 文件上传到 Cloud Shell 存储。 如果安装程序将为你创建和配置 Azure AD 应用注册，则不需要在 json 中传递 Azure AD 参数。

   ![项目场节拍](./media/prepare-for-deployment/bash-2-1.png)

5. 将 "部署命令" 键入或粘贴到 Cloud Shell 中。 请确保修改输入的路径。 Json 文件，然后按 enter。  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   此脚本会自动下载所有依赖项，并生成部署的。 然后，系统会提示同意 Azure FarmBeats 最终用户许可协议（EULA）。

   - 如果同意，请输入 "Y"，然后继续下一步。
   - 如果你不同意这些条款并且部署将终止，请输入 "N"。

   然后，系统将提示您输入部署的访问令牌。 复制生成的代码，并以你的 Azure 凭据登录 https://microsoft.com/devicelogin。

   > [!NOTE]
   > 代码将在60分钟后过期。 过期后，你可以重新启动，方法是再次键入部署命令。

6. 在下一个提示符下，输入 "sentinel 帐户密码"。
7. 安装程序现在将验证并开始部署，这可能需要大约20分钟的时间。
8. 部署成功后，将收到以下输出链接：
    - **数据中心 URL**： Swagger 链接，用于试用 Azure FarmBeats api。
    - **快捷键 URL**：用于浏览 Azure FarmBeats 智能场加速器的用户界面。
    - 部署期间创建的部署**日志**文件日志文件。 它可用于故障排除。

    - 如果同意，请输入 "Y"，然后继续下一步。
    - 如果你不同意这些条款并且部署将终止，请输入 "N"。

   然后，系统将提示您输入部署的访问令牌。 复制生成的代码，并通过 Azure 凭据登录到 https://microsoft.com/devicelogin。

   > [!NOTE]
    > 请记下这些更改，并保留部署日志文件路径，以便将来使用。


### <a name="deployment-scenario-2"></a>部署方案2

现有 Azure Active Directory 应用注册用于部署。

1. 下载[输入 json](https://aka.ms/PPInputJsonTemplate)在输入中包括 Azure 应用程序的客户端 ID 和密码，保存。

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

     按照以下步骤进行操作：

2. 记下输入 json 文件的路径（在本地计算机上）。
3. 再次跳到 "Azure Cloud Shell"，并成功进行身份验证，选择 "上传" 按钮（请参阅下图中突出显示的图标），并将输入的 json 文件上传到 Cloud Shell 存储。

    ![项目场节拍](./media/prepare-for-deployment/bash-2-1.png)

4. 将*部署命令*键入或粘贴到 Cloud Shell 中。 请确保修改输入的路径。 Json 文件，然后按 enter。

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   按照以下步骤进行操作：

5. 此脚本会自动下载所有依赖项，并生成部署的。
6. 系统将提示你阅读并同意 Azure FarmBeats 最终用户许可协议（EULA）。

   - 如果同意，请输入 "Y"，然后继续下一步。
   - 如果你不同意这些条款并且部署将终止，请输入 "N"。

7. 系统将提示你输入部署的访问令牌。 复制生成的代码，并通过 Azure 凭据登录到 https://microsoft.com/devicelogin。
8. 安装程序现在将验证并开始创建资源，这可能需要大约20分钟的时间。 在此期间将 Cloud Shell 上的会话保持为活动状态。
9. 成功完成部署后，你会收到以下输出链接：
   - **数据中心 URL**： Swagger 链接，用于试用 FarmBeats api。
   - **快捷键 URL**：用于浏览 FarmBeats 智能场加速器的用户界面。
   - 部署人员**日志文件**：在部署期间保存日志，可用于故障排除。

如果遇到任何问题，请查看[故障排除](troubleshoot-project-farmbeats.md)。


## <a name="validate-deployment"></a>验证部署

### <a name="data-hub"></a>数据中心

数据中心安装完成后，将收到 URL，以便通过 Swagger 接口访问 Azure FarmBeats Api，格式为： https://\<yourdatahub > appname>.azurewebsites.net

1. 若要通过 Swagger 登录，请在浏览器中复制并粘贴该 URL。
2. 用 Azure 门户凭据登录。
3. 稳定测试（可选）

     - 能够使用数据中心链接成功登录 Swagger 门户，并将其作为输出发送到成功的部署。
     - 扩展类型获取 API-选择 "试用/Execute"
     - 你应收到服务器响应代码200，而不是一个例外，如 403 "未授权用户"。

### <a name="accelerator"></a>键

加速器安装完成后，你将收到 URL 来访问 FarmBeats 用户界面，格式为： https://\<加速器->. appname>.azurewebsites.net

1. 若要从加速器登录，请在浏览器中复制并粘贴该 URL。
2. 用 Azure 门户凭据登录。
3. 运行可选的健全测试。

    - 检查是否能够使用作为成功部署的输出收到的 "加速器" 链接成功登录到加速器门户。
    - 选择 "**创建场**"。
    - 在 "？" 图标下，使用 "**入门**" 按钮打开 FarmBeats 指南。

## <a name="upgrade"></a>升级

升级步骤类似于首次安装。 执行以下步骤:

1. 登录到 Azure 门户，并选择所需的订阅和 AD 租户。
2. 从 Azure 门户的顶部导航栏启动 Cloud Shell。

   ![项目场节拍](./media/prepare-for-deployment/navigation-bar-1.png)

3. 选择用于创建存储帐户和 Azure 文件共享的订阅。
4. 选择“创建存储”。
5. 从 shell （Bash）左侧的下拉框中选择环境。
6. 仅在需要时对输入的 json 文件进行更改并上载到 Azure Cloud Shell。 例如，你可以更新你要接收的通知的电子邮件地址。
7. 记下输入的 json 文件路径（在本地计算机上）。
8. 请参阅 Azure Cloud Shell。 成功通过身份验证后，选择 "上传" 按钮（请参阅下图中突出显示的图标），并将输入的 json 文件上传到 Cloud Shell 存储。

   ![项目场节拍](./media/prepare-for-deployment/bash-2-1.png)

9. 将**部署命令**键入或粘贴到 Cloud Shell 中。 请确保修改输入的路径。 Json 文件，然后按 enter。

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    按照以下步骤进行操作：

10. 安装程序会在运行时自动提示所需的输入：

    - 输入用于部署的访问令牌。 复制生成的代码，并通过 Azure 凭据登录到 https://microsoft.com/devicelogin。

     > [!NOTE]
     > 代码将在60分钟后过期。 过期后，你可以重新启动，方法是再次键入部署命令。

     - Sentinel 密码

11. 安装程序现在将验证并开始创建资源，这可能需要大约20分钟的时间。
12. 部署成功后，将收到以下输出链接：

    - **数据中心 URL**： Swagger 链接，用于试用 FarmBeats api。
    - **快捷键 URL**：用于浏览 FarmBeats 智能场加速器的用户界面。
    - 部署后的**日志文件**：保存日志。 它可用于故障排除。

    > [!NOTE]
    > 记下上述链接，并保留部署日志文件路径以便将来使用。

## <a name="uninstall"></a>卸载

目前，我们不支持使用安装程序自动卸载 FarmBeats。 若要删除数据中心或加速器，请在 Azure 门户中删除安装了这些组件的资源组，或手动删除资源。

例如，如果在两个不同的资源组中部署了数据中心和加速器，则按如下所述删除这些资源组：

1. 登录到 Azure 门户。
2. 在右上角选择帐户，并切换到要在其中部署 Microsoft FarmBeats 的所需 Azure AD 租户。

   > [!NOTE]
   > 数据中心是加速器正常工作所必需的。 不建议卸载数据中心，无需卸载加速器。

3. 选择 "资源组"，然后键入要删除的数据中心或加速器资源组的名称。
4. 选择资源组名称。 再次键入名称进行双重检查，然后单击 "删除" 以删除资源组及其所有基础资源。
5. 或者，您可以手动删除每个资源，这是不推荐的。
7. 若要删除/卸载数据中心，请直接在 Azure 上切换到资源组，并删除其中的资源组。

## <a name="next-steps"></a>后续步骤

已部署 Azure FarmBeats。 现在，了解如何[创建场](manage-farms.md#create-farms)。
