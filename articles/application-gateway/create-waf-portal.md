---
title: 创建具有 Web 应用程序防火墙的应用程序网关 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建具有 Web 应用程序防火墙的应用程序网关。
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: fdddcfd6ac44a0675e33e7f389a03d6dbdcfb223
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>使用 Azure 门户创建具有 Web 应用程序防火墙的应用程序网关

可以使用 Azure 门户创建具有 [Web 应用程序防火墙](waf-overview.md) (WAF) 的[应用程序网关](overview.md)。 WAF 使用 [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 规则保护应用程序。 这些规则包括针对各种攻击（例如 SQL 注入、跨站点脚本攻击和会话劫持）的保护。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建启用 WAF 的应用程序网关
> * 创建用作后端服务器的虚拟机
> * 创建存储帐户和配置诊断

![Web 应用程序防火墙示例](./media/create-waf-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>登录 Azure

在 [http://portal.azure.com](http://portal.azure.com) 登录到 Azure 门户

## <a name="create-an-application-gateway"></a>创建应用程序网关

若要在创建的资源之间实现通信，需要设置虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 单击 Azure 门户左上角的“新建”。
2. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。
3. 输入应用程序网关的以下值：

    - *myAppGateway* - 应用程序网关的名称。
    - *myResourceGroupAG* - 作为新资源组。
    - 对于应用程序网关的层，选择 *WAF*。

    ![新建应用程序网关](./media/create-waf-portal/application-gateway-create.png)

4. 接受其他设置的默认值，然后单击“确定”。
5. 依次单击“选择虚拟网络”、“新建”，然后输入虚拟网络的以下值：

    - *myVNet* - 虚拟网络的名称。
    - *10.0.0.0/16* - 虚拟网络地址空间。
    - *myAGSubnet* - 子网名称。
    - *10.0.0.0/24* - 子网地址空间。

    ![创建虚拟网络](./media/create-waf-portal/application-gateway-vnet.png)

6. 单击“确定”创建虚拟网络和子网。
7. 依次单击“选择公共 IP 地址”、“新建”，然后输入公共 IP 地址的名称。 在本示例中，公共 IP 地址名为 *myAGPublicIPAddress*。 接受其他设置的默认值，然后单击“确定”。
8. 接受侦听器配置的默认值，让 Web 应用程序防火墙保留禁用状态，然后单击“确定”。
9. 复查摘要页上的设置，然后单击“确定”以创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后再转到下一部分。

### <a name="add-a-subnet"></a>添加子网

1. 单击左侧菜单中的“所有资源”，然后从资源列表中单击“myVNet”。
2. 单击“子网”，然后单击“子网”。

    ![创建子网](./media/create-waf-portal/application-gateway-subnet.png)

3. 输入 *myBackendSubnet* 作为子网的名称，然后单击“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

在此示例中，将创建两个虚拟机以用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，以验证是否已成功创建应用程序网关。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 单击“新建” 。
2. 单击“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。
3. 为虚拟机输入以下值：

    - *myVM* - 作为虚拟机的名称。
    - *azureuser* - 作为管理员用户名。
    - *Azure123456!* - 密码。
    - 选择“使用现有资源组”，然后选择“myResourceGroupAG”。

4. 单击“确定”。
5. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
6. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。 
7. 单击“禁用”以禁用启动诊断。
8. 创建“确定”，检查“摘要”页上的设置，然后单击“创建”。

### <a name="install-iis"></a>安装 IIS

1. 打开交互式 shell 并确保它已设置为 **PowerShell**。

    ![安装自定义扩展](./media/create-waf-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS： 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 使用刚刚完成的步骤创建第二个虚拟机并安装 IIS。 输入 *myVM2* 作为其名称，并将其用于 Set-AzureRmVMExtension 中的 VMName。

### <a name="add-backend-servers"></a>添加后端服务器

1. 单击“所有资源”，然后单击 **myAppGateway**。
2. 单击“后端池”。 默认池已随应用程序网关自动创建。 单击 **appGateayBackendPool**。
3. 单击“添加目标”将所创建的每个虚拟机添加到后端池。

    ![添加后端服务器](./media/create-waf-portal/application-gateway-backend.png)

4. 单击“ **保存**”。

## <a name="create-a-storage-account-and-configure-diagnostics"></a>创建存储帐户和配置诊断

## <a name="create-a-storage-account"></a>创建存储帐户

在本教程中，应用程序网关使用存储帐户来存储用于检测和防范目的的数据。 也可以使用 Log Analytics 或事件中心来记录数据。

1. 单击 Azure 门户左上角的“新建”。
2. 选择“存储”，然后选择“存储帐户 - blob、文件、表、队列”。
3. 输入存储帐户的名称，对于资源组选择“使用现有资源组”，然后选择 **myResourceGroupAG**。 在此示例中，存储帐户名称是 *myagstore1*。 接受其他设置的默认值，然后单击“创建”。

## <a name="configure-diagnostics"></a>配置诊断

配置诊断以将数据记录到 ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog 和 ApplicationGatewayFirewallLog 日志中。

1. 在左侧菜单中，单击“所有资源”，然后选择 *myAppGateway*。
2. 在“监视”下面，单击“诊断日志”。
3. 单击“添加诊断设置”。
4. 输入 *myDiagnosticsSettings* 作为诊断设置的名称。
5. 选择“存档到存储帐户”，然后单击“配置”以选择前面创建的 *myagstore1* 存储帐户。
6. 选择要收集和保留的应用程序网关日志。
7. 单击“ **保存**”。

    ![配置诊断](./media/create-waf-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在“概述”屏幕上找到应用程序网关的公共 IP 地址。 单击“所有资源”，然后单击 **myAGPublicIPAddress**。

    ![记录应用程序网关的公共 IP 地址](./media/create-waf-portal/application-gateway-record-ag-address.png)

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

    ![测试应用程序网关](./media/create-waf-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建启用 WAF 的应用程序网关
> * 创建用作后端服务器的虚拟机
> * 创建存储帐户和配置诊断

若要了解有关应用程序网关及其关联资源的详细信息，请继续阅读操作指南文章。