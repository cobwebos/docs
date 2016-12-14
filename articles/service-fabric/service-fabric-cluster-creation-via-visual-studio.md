---
title: "使用 Visual Studio 设置 Service Fabric 群集 | Microsoft 文档"
description: "描述如何使用 Visual Studio 中 Azure 资源组项目创建的 Azure Resource Manager 模板来设置 Service Fabric 群集"
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: adegeo
editor: 
ms.assetid: bd2c0511-36c9-4828-8dc3-69e4b6a70567
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2016
ms.author: karolz@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 01498faf6d150cb0a96af150d6c9ce1ed62c26dd


---
# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>使用 Visual Studio 设置 Service Fabric 群集
本文介绍如何通过使用 Visual Studio 和 Azure Resource Manager 模板来设置 Azure Service Fabric 群集。 我们将使用 Visual Studio Azure 资源组项目来创建模板。 创建模板后，它可以直接从 Visual Studio 部署到 Azure。 可以从脚本使用它，也可以将它作为持续集成 (CI) 工具的一部分。

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>使用 Azure 资源组项目创建 Service Fabric 群集模板
若要开始操作，请打开 Visual Studio 并创建 Azure 资源组项目（它在 **Cloud** 文件夹中提供）：

![已选中 Azure 资源组项目的“新建项目”对话框][1]

可以为此项目创建新的 Visual Studio 解决方案，或将它添加到现有解决方案。

> [!NOTE]
> 如果在“云”节点下面看不到 Azure 资源组项目，表示尚未安装 Azure SDK。 启动 Web 平台安装程序（如果尚未安装，请[立即安装](http://www.microsoft.com/web/downloads/platform.aspx)），然后搜索“用于 .NET 的 Azure SDK”，并安装与你的 Visual Studio 版本兼容的版本。
> 
> 

按“确定”按钮后，Visual Studio 将要求你选择想要创建的 Resource Manager 模板：

![已选中 Service Fabric 群集模板的“选择 Azure 模板”对话框][2]

选择“Service Fabric 群集”模板，然后再次点击“确定”。 现在已创建项目与 Resource Manager 模板。

## <a name="prepare-the-template-for-deployment"></a>准备用于部署的模板
在部署模板以创建群集之前，必须提供所需的模板参数值。 可从资源组项目的 `Templates` 文件夹中的 `ServiceFabricCluster.parameters.json` 文件读取这些参数值。 打开该文件并提供以下值：

| 参数名称 | 说明 |
| --- | --- |
| adminUserName |Service Fabric 计算机（节点）的管理员帐户的名称。 |
| certificateThumbprint |保护群集的证书的指纹。 |
| sourceVaultResourceId |存储保护群集的证书的密钥保管库的*资源 ID*。 |
| certificateUrlValue |群集安全证书的 URL。 |

Visual Studio Service Fabric Resource Manager 模板将创建一个受证书保护的安全群集。 此证书由最后三个模板参数（`certificateThumbprint`、`sourceVaultValue` 和 `certificateUrlValue`）标识，它必须存在于 **Azure 密钥保管库**中。 有关如何创建群集安全证书的详细信息，请参阅 [Service Fabric 群集安全性方案](service-fabric-cluster-security.md#x509-certificates-and-service-fabric)一文。

## <a name="optional-change-the-cluster-name"></a>可选：更改群集名称
每个 Service Fabric 群集都有一个名称。 在 Azure 中创建结构群集时，群集名称（连同 Azure 区域）确定了群集的域名系统 (DNS) 名称。 例如，如果将你的群集命名为 `myBigCluster`，且托管新群集的资源组位置（Azure 区域）为美国东部，则群集的 DNS 名称将为 `myBigCluster.eastus.cloudapp.azure.com`。

默认情况下，系统会自动生成群集名称，并在“群集”前缀后面附加一个随机后缀，使该名称唯一。 这非常便于将模板作为**持续集成** (CI) 系统的一部分使用。 如果想要对你的群集使用一个特定名称（有意义的名称），请将 Resource Manager 模板文件 (`ServiceFabricCluster.json`) 中的 `clusterName` 变量值设置为所选的名称。 该名称是该文件中定义的第一个变量。

## <a name="optional-add-public-application-ports"></a>可选：添加公共应用程序端口
在部署模板之前，你还可能想要更改群集的公共应用程序端口。 默认情况下，模板只打开两个公共 TCP 端口（80 和 8081）。 如果应用程序需要更多端口，请修改模板中的 Azure Load Balancer 定义。 该定义存储在主模板文件 (`ServiceFabricCluster.json`) 中。 打开该文件并搜索 `loadBalancedAppPort`。 每个端口与三个项目相关联：

1. 一个模板变量，用于定义端口的 TCP 端口值：
   
    ```json
    "loadBalancedAppPort1": "80"
    ```
2. *探测*，它定义在故障转移到另一个 Service Fabric 节点之前 Azure Load Balancer 尝试使用特定 Service Fabric 节点的频率和时间长度。 探测是负载平衡器资源的一部分。 下面是第一个默认应用程序端口的探测定义：
   
    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```
3. *负载平衡规则*，它将端口和探测器连在一起，支持在一组 Service Fabric 群集节点中实现负载平衡：
   
    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
   如果你要部署到群集的应用程序需要更多端口，可以创建额外的探测和负载平衡规则定义来添加端口。 有关如何通过 Resource Manager 模板使用 Azure Load Balancer 的详细信息，请参阅 [使用模板创建内部负载平衡器入门](../load-balancer/load-balancer-get-started-ilb-arm-template.md)。

## <a name="deploy-the-template-by-using-visual-studio"></a>使用 Visual Studio 部署模板
在 `ServiceFabricCluster.param.dev.json` 文件中保存所有必需的参数值后，你就可以部署模板和创建 Service Fabric 群集。 右键单击 Visual Studio 解决方案资源管理器中的资源组项目，并选择“**部署 | 新建部署...**”。 如果需要，Visual Studio 将显示“**部署到资源组**”对话框，要求你向 Azure 进行身份验证：

![“部署到资源组”对话框][3]

此对话框可让你选择群集的现有 Resource Manager 资源组，并提供用于创建新资源组的选项。 通常，有利的做法是对 Service Fabric 群集使用不同的资源组。

按“部署”按钮后，Visual Studio 将提示你确认模板参数值。 点击“**保存**”按钮。 有一个参数没有持久值：群集的管理帐户密码。 当 Visual Studio 提示你输入密码时，你需要提供密码值。

> [!NOTE]
> 从 Azure SDK 2.9 开始，Visual Studio 支持在部署过程中从 **Azure 密钥保管库**读取密码。 在模板参数对话框中，注意 `adminPassword` 参数文本框右侧带有一个小的“钥匙”图标。 此图标允许你选择现有密钥保管库密钥作为群集的管理密码。 只需确保首先允许 Azure Resource Manager 访问密钥保管库的高级访问策略中的模板部署。 
> 
> 

可以在 Visual Studio 的“输出”窗口中监视部署过程的进度。 一旦完成模板部署，新群集即可供使用！

> [!NOTE]
> 如果 PowerShell 未曾用来从现在使用的计算机管理 Azure，则你需要做一些调整。
> 
> 1. 通过运行 [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) 命令启用 PowerShell 脚本。 开发计算机通常可接受“不受限制的”策略。
> 2. 决定是否允许从 Azure PowerShell 命令收集诊断数据，并根据需要运行 [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) 或 [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx)。 这可以避免在模板部署期间不必要地出现提示。
> 
> 

如果有任何错误，请转到 [Azure 门户](https://portal.azure.com/)并打开已部署到的资源组。 单击“**所有设置**”，然后单击“设置”边栏选项卡上的“**部署**”。 失败的资源组部署会在那里留下详细的诊断信息。

> [!NOTE]
> Service Fabric 群集需要一定数量的节点处于开机状态，以维护可用性和保留状态（称为“维护仲裁”）。 因此，除非首先执行[状态的完整备份](service-fabric-reliable-services-backup-restore.md)，否则关闭群集中的所有计算机是很不安全的。
> 
> 

## <a name="next-steps"></a>后续步骤
* [了解如何使用 Azure 门户设置 Service Fabric 群集](service-fabric-cluster-creation-via-portal.md)
* [了解如何使用 Visual Studio 管理和部署 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png



<!--HONumber=Nov16_HO3-->


