---
title: "使用虚拟网络连接到 Kafka on HDInsight - Azure | Microsoft Docs"
description: "了解如何使用 kafka-python 客户端远程连接到 Kafka on HDInsight。 本文档中的配置使用了 Azure 虚拟网络内部的 HDInsight。 远程客户端通过点到站点 VPN 网关连接到虚拟网络。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9ddf19c008c35525419a357436b1a969a4b19205
ms.lasthandoff: 04/27/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>通过 Azure 虚拟网络连接到 Kafka on HDInsight（预览版）

了解如何使用 Azure 虚拟网络连接到 Kafka on HDInsight。 Kafka 客户端（生产者和使用者）可以直接在 HDInsight 上运行，也可在远程系统上运行。 远程客户端必须通过 Azure 虚拟网络连接到 Kafka on HDInsight。 通过此文档中的信息，了解如何使用 Azure 虚拟网络将远程客户端连接到 HDInsight。

> [!IMPORTANT]
> 本文档所讨论的几种配置适用于 Windows、macOS 或 Linux 客户端。 但是，其中点到站点的示例仅为 Windows 提供 VPN 客户端。
>
> 此示例还使用了 Python 客户端 ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) 来验证与 Kafka on HDInsight 的通信。

## <a name="architecture-and-planning"></a>架构与规划

HDInsight 群集在 Azure 虚拟网络内部是安全的，且仅允许传入 SSH 和 HTTPS 流量。 通过公共网关到达的流量不会从 Kafka 客户端路由流量。 要从远程客户端访问 Kafka，必须创建 Azure 虚拟网络，以便提供虚拟专用网 (VPN) 网关。 成功配置虚拟网络和网关后，请将 HDInsight 安装到虚拟网络，并使用 VPN 网关进行连接。

![Azure 虚拟网络中的 HDInsight 关系图，其中客户端通过 VPN 连接](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

以下列表包含在虚拟网络中使用 Kafka on HDInsight 的过程信息：

1. 创建虚拟网络。 有关通过 Azure 虚拟网络使用 HDInsight 的特定信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight](hdinsight-extend-hadoop-virtual-network.md) 文档。

2. （可选）在虚拟网络内部创建 Azure 虚拟机，并在其上安装自定义 DNS 服务器。 此 DNS 服务器用于在站点到站点或 VNet 到 VNet 配置中为远程客户端启用名称解析。 有关详细信息，请参阅 [VM 和云服务的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文档。

3. 为虚拟网络创建 VPN 网关。 有关 VPN 网关配置的详细信息，请参阅[关于 VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)一文。

4. 在虚拟网络中创建 HDInsight。 如果为网络配置了自定义 DNS 服务器，则将自动配置 HDInsight 以使用该服务器。

5. （可选）如果未使用自定义 DNS 服务器，且在客户端和虚拟网络之间缺少名称解析，则必须为 IP 播发配置 Kafka。 有关详细信息，请参阅本文的[为 IP 播发配置 Kafka](#configure-kafka-for-ip-advertising)部分。

## <a name="create-using-powershell"></a>创建：使用 PowerShell

本部分中的步骤使用 [Azure PowerShell](/powershell/azure/overview) 创建下列配置：

* Azure 虚拟网络
* 点到站点 VPN 网关
* Azure 存储帐户（由 HDInsight 使用）
* Kafka on HDInsight

1. 按[为点到站点连接使用自签名证书](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)文档中的步骤来创建网关所需的证书。

2. 打开 PowerShell 提示符，然后使用下列代码登录 Azure 订阅：

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. 使用下列代码创建包含配置信息的变量：

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. 使用下列代码创建 Azure 资源组和虚拟网络：

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > 这个过程可能需要几分钟才能完成。

5. 使用下列代码创建 Azure 存储帐户和 BLob 容器：

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. 使用下列代码创建 HDInsight 群集：

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > 此过程完成时间大约为 20 分钟。

8. 使用下列 cmdlet 为虚拟网络的 Windows VPN 客户端检索 URL：

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    要下载 Windows VPN 客户端，请在 Web 浏览器中使用返回的 URI。

## <a name="configure-kafka-for-ip-advertising"></a>为 IP 播发配置 Kafka

默认情况下，Zookeeper 向客户端返回 Kafka 中转站的域名。 此配置不适用于 VPN 客户端，因为它无法为虚拟网络中的实体使用名称解析。 按下列步骤来配置 Kafka on HDInsight，以播发 IP 地址，而不是域名：

1. 使用 Web 浏览器，并转到 https://CLUSTERNAME.azurehdinsight.net。 将 CLUSTERNAME 替换为 Kafka on HDInsight 群集的名称。

    出现提示时，使用群集的 HTTPS 用户名称密码。 将显示群集的 Ambari Web UI。

2. 要查看 Kafka 的相关信息，请从左侧列表中选择“Kafka”。 

    ![Kafka 突出显示的服务列表](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. 要查看 Kafka 配置，请在顶端的中间位置选择“配置”。

    ![Kafka 的配置链接](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. 要查找“kafka-env” 配置，请在右上方的“筛选器”字段中输入 `kafka-env`。

    ![Kafka 配置，适用于 kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. 要配置 Kafka 来播发 IP 地址，请将下列文本添加到“kafka-env-template”字段的底部：

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. 要配置 Kafka 侦听的接口，请在右上方的“筛选器”字段中输入 `listeners`。

7. 要将 Kafka 配置为侦听所有网络接口，请将“侦听器”字段的值更改为 `PLAINTEXT://0.0.0.0:92092`。

8. 单击“保存”按钮保存配置。 输入描述更改的文本消息。 保存更改后，请选择“确定”。

    ![保存配置按钮](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. 要防止在重启 Kafka 时出错，请使用“服务操作”按钮，并选择“打开维护模式”。 选择“确定”完成操作。

    ![服务操作，其中已突出显示“打开维护”](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. 要重启 Kafka，请使用“重启”按钮，然后选择“重启所有受影响的项”。 确认重启，在操作完成后再使用“确定”按钮。

    ![重启按钮，其中突出显示了所有受影响的重启项](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. 要禁用维护模式，请使用“服务操作”按钮，然后选择“关闭维护模式”。 选择“确定”完成操作。

## <a name="connect-to-the-vpn-gateway"></a>连接到 VPN 网关

要从 Windows 客户端连接到 VPN 网关，请按[配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameconnectapart-7---connect-to-azure)文档中“连接到 Azure”部分进行操作。

## <a name="remote-kafka-client"></a>远程 Kafka 客户端

要从客户端计算机连接到 Kafka，必须使用 Kafka 中转站或 Zookeeper 节点的 IP 地址（以客户端要求为准）。 使用以下步骤检索 Kafka 中转站的 IP 地址，然后从 Python 应用程序中使用它们

1. 使用下列脚本检索群集中节点的 IP 地址：

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    此脚本假设 `$resourceGroupName` 是包含虚拟网络的 Azure 资源组的名称。 此脚本的输出类似于以下文本：

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > 如果 Kafka 客户端使用的是 Zookeeper 节点而非 Kafka 中转站，请在 PowerShell 脚本中将 `*workernode*` 替换为 `*zookeepernode*`。

    > [!WARNING]
    > 如果缩放群集，或节点因发生故障而被替换，则 IP 地址有可能更改。 目前，无法为 HDInsight 群集中的节点预分配特定的 IP 地址。

2. 使用下列命令安装 [kafka-python](http://kafka-python.readthedocs.io/) 客户端：

        pip install kafka-python

3. 要将数据发送到 Kafka，请使用下列 Python 代码：

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    使用本节步骤 1 中返回的 IP 地址替换 `'ip_address'` 条目。
    
    > [!NOTE]
    > 此代码将字符串 `test message` 发送给主题 `testtopic`。 Kafka on HDInsight 的默认配置是创建主题（如果它尚不存在）。

4. 要从 Kafka 检索消息，请使用下列 Python 代码：

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    使用本节步骤 1 中返回的 IP 地址替换 `'ip_address'` 条目。 输出包含在上一步骤中发送给生产者的文本消息。

## <a name="troubleshooting"></a>故障排除

如果在连接到虚拟网络，或通过网络连接到 HDInsight 时遇到问题，请参阅[对虚拟网络网关和连接进行故障排除](../network-watcher/network-watcher-troubleshoot-manage-powershell.md)文档以供参考。

## <a name="next-steps"></a>后续步骤

有关使用点到站点 VPN 网关创建 Azure 虚拟网络的详细信息，请参阅下列文档：

* [使用 Azure 门户配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [使用 Azure PowerShell 配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

有关使用 Kafka on HDInsight 的详细信息，请参阅以下文档：

* [Kafka on HDInsight 入门](hdinsight-apache-kafka-get-started.md)
* [通过 Kafka on HDInsight 使用镜像](hdinsight-apache-kafka-mirroring.md)

