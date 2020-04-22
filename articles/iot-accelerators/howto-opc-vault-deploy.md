---
title: 如何部署 OPC 保管库证书管理服务 - Azure |微软文档
description: 如何从头开始部署 OPC 保管库证书管理服务。
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686943"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>构建和部署 OPC 保管库证书管理服务

本文介绍如何在 Azure 中部署 OPC 保管库证书管理服务。

> [!NOTE]
> 有关详细信息，请参阅 GitHub [OPC 保管库存储库](https://github.com/Azure/azure-iiot-opc-vault-service)。

## <a name="prerequisites"></a>先决条件

### <a name="install-required-software"></a>安装必需软件

目前，生成和部署操作仅限于 Windows。
这些示例都为 C# .NET 标准编写，您需要构建服务和示例以进行部署。
.NET 标准所需的所有工具都附带 .NET 核心工具。 请参阅[从 .NET 核心开始](https://docs.microsoft.com/dotnet/articles/core/getting-started)。

1. [安装 .NET 核心 2.1+][dotnet-install]。
2. [安装 Docker（][docker-url]可选，仅当需要本地 Docker 生成时）。
4. 安装[PowerShell 的 Azure 命令行工具][powershell-install]。
5. 注册 Azure[订阅][azure-free]。

### <a name="clone-the-repository"></a>克隆存储库

如果尚未这样做，则克隆此 GitHub 存储库。 打开命令提示符或终端，并运行以下内容：

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

或者，您也可以直接在 Visual Studio 2017 中克隆该回购。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>在 Windows 上生成和部署 Azure 服务

PowerShell 脚本提供了一种部署 OPC Vault 微服务和应用程序的简便方法。

1. 在回购根处打开 PowerShell 窗口。 
3. 转到部署文件夹`cd deploy`。
3. 选择不太可能与其他已`myResourceGroup`部署的网页发生冲突的名称。 请参阅本文后面的"已在使用的网站名称"部分。
5. 开始`.\deploy.ps1`部署以进行交互式安装，或输入完整的命令行：  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 如果计划使用此部署进行开发，则添加`-development 1`以启用斯瓦格 UI，并部署调试生成。
6. 按照脚本中的说明登录订阅并提供其他信息。
9. 成功生成和部署操作后，您应该会看到以下消息：
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > 如果出现问题，请参阅本文后面的"故障排除部署故障"部分。

8. 打开您最喜爱的浏览器，然后打开应用程序页面：`https://myResourceGroup.azurewebsites.net`
8. 为 Web 应用和 OPC Vault 微服务提供几分钟时间，在部署后预热。 网页主页可能会首次使用，最多一分钟，直到您得到第一个响应。
11. 要查看斯瓦格 API，请打开：`https://myResourceGroup-service.azurewebsites.net`
13. 要使用 dotnet 启动本地 GDS`.\myResourceGroup-gds.cmd`服务器，应启动 。 使用 Docker，`.\myResourceGroup-dockergds.cmd`开始。

可以重新部署具有相同设置的生成。 请注意，此类操作会更新所有应用程序机密，并可能重置 Azure 活动目录 （Azure AD） 应用程序注册中的某些设置。

也可以只重新部署 Web 应用二进制文件。 使用 参数`-onlyBuild 1`，服务和应用程序的新 zip 包部署到 Web 应用程序。

成功部署后，可以开始使用服务。 请参阅[管理 OPC 保管库证书管理服务](howto-opc-vault-manage.md)。

## <a name="delete-the-services-from-the-subscription"></a>从订阅中删除服务

以下是操作方法：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 转到部署服务的资源组。
3. 选择“删除资源组”并进行确认。****
4. 过一段时间后，将删除所有已部署的服务组件。
5. 转到**Azure 活动目录** > **应用注册**。
6. 每个已部署的资源组应列出三个注册。 注册具有以下名称： `resourcegroup-client`、 `resourcegroup-module`、 `resourcegroup-service`。 分别删除每个注册。

现在，所有已部署的组件都将被删除。

## <a name="troubleshooting-deployment-failures"></a>排除部署故障

### <a name="resource-group-name"></a>资源组名称

使用简短和简单的资源组名称。 该名称还用于命名资源和服务 URL 前缀。 因此，它必须符合资源命名要求。  

### <a name="website-name-already-in-use"></a>网站名称已在使用中

网站的名称可能已在使用中。 您需要使用不同的资源组名称。 部署脚本正在使用的主机名是：https：/resourcegroupname.azurewebsites.net\/和 https：/resourgroupname-service.azurewebsites.net。\/
其他服务名称由短名称哈希的组合构建，不太可能与其他服务冲突。

### <a name="azure-ad-registration"></a>Azure AD 注册 

部署脚本尝试在 Azure AD 中注册三个 Azure AD 应用程序。 根据所选 Azure AD 租户中的权限，此操作可能会失败。 存在两个选项：

- 如果从租户列表中选择了 Azure AD 租户，请重新启动脚本并从列表中选择其他脚本。
- 或者，在另一个订阅中部署专用 Azure AD 租户。 重新启动脚本，然后选择使用它。

## <a name="deployment-script-options"></a>部署脚本选项

该脚本采用以下参数：


```
-resourceGroupName
```

这可以是现有资源组或新资源组的名称。

```
-subscriptionId
```


这是将部署资源的订阅 ID。 可选。

```
-subscriptionName
```


或者，您可以使用订阅名称。

```
-resourceGroupLocation
```


这是资源组位置。 如果指定，此参数将尝试在此位置创建新的资源组。 此参数也是可选的。


```
-tenantId
```


这是要使用的 Azure AD 租户。 

```
-development 0|1
```

这是为开发而部署的。 使用调试生成，并将ASP.NET环境设置为"开发"。 在`.publishsettings`Visual Studio 2017 中创建用于导入，以允许它直接部署应用和服务。 此参数也是可选的。

```
-onlyBuild 0|1
```

这是重建和仅重新部署 Web 应用，并重建 Docker 容器。 此参数也是可选的。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何从头开始部署 OPC Vault，您可以：

> [!div class="nextstepaction"]
> [管理 OPC 保管库](howto-opc-vault-manage.md)
