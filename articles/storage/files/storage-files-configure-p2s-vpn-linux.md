---
title: 在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用 | Microsoft Docs
description: 如何在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126463"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用
你可以使用点到站点 (P2S) VPN 连接从 Azure 外部通过 SMB 装载 Azure 文件共享，而无需打开端口 445。 点到站点 VPN 连接是 Azure 与单个客户端之间的 VPN 连接。 若要将 P2S VPN 连接与 Azure 文件存储一起使用，需要为每个要连接的客户端配置 P2S VPN 连接。 如果有多个客户端需要从本地网络连接到 Azure 文件共享，则可以为每个客户端使用站点到站点 (S2S) VPN 连接，而不使用点到站点连接。 若要了解详细信息，请参阅[配置站点到站点 VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)。

我们强烈建议先阅读 [Azure 文件存储网络概述](storage-files-networking-overview.md)，然后再继续阅读本文章，以全面讨论 Azure 文件存储可用的网络选项。

本文详细介绍了在 Linux 上配置点到站点 VPN 以直接在本地装载 Azure 文件共享的步骤。 如果想要通过 VPN 路由 Azure 文件同步流量，请参阅[配置 Azure 文件同步代理和防火墙设置](storage-sync-files-firewall-and-proxy.md)。

## <a name="prerequisites"></a>先决条件
- 最新版本的 Azure CLI。 若要详细了解如何安装 Azure CLI，请参阅[安装 Azure PowerShell CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并选择操作系统。 如果你想要在 Linux 上使用 Azure PowerShell 模块，也可以使用，但是下面是针对 Azure CLI 的说明。

- 要在本地装载的 Azure 文件共享。 你可以将[标准](storage-how-to-create-file-share.md)或[高级 Azure 文件共享](storage-how-to-create-premium-fileshare.md)与点到站点 VPN 一起使用。

## <a name="install-required-software"></a>安装必需软件
Azure 虚拟网络网关可以使用多种 VPN 协议（包括 IPsec 和 OpenVPN）提供 VPN 连接。 本指南介绍如何使用 IPsec 以及使用 strongSwan 包在 Linux 上提供支持。 

> 已通过 Ubuntu 18.10 验证。

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>部署虚拟网络 
若要通过点到站点 VPN 从本地访问 Azure 文件共享和其他 Azure 资源，必须创建虚拟网络或 VNet。 将自动创建的 P2S VPN 连接是本地 Linux 计算机与此 Azure 虚拟网络之间的桥梁。

以下脚本将创建包含三个子网的 Azure 虚拟网络：一个用于存储帐户的服务终结点，一个用于存储帐户的专用终结点（用于访问本地存储帐户，而无需为可能更改的存储帐户的公共 IP 创建自定义传递），以及一个用于提供 VPN 服务的虚拟网络网关。 

请记得将 `<region>`、`<resource-group>` 和 `<desired-vnet-name>` 替换为适合你的环境的值。

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>仅限虚拟网络访问存储帐户
默认情况下，当你创建一个存储帐户时，只要有验证请求的方法（例如使用 Active Directory 标识或使用存储帐户密钥），就可以随时随地对其进行访问。 若要仅限刚创建的虚拟网络访问此存储帐户，则需要创建一个允许在虚拟网络中访问且拒绝所有其他访问的网络规则集。

仅限虚拟网络访问存储帐户时，需要使用服务终结点。 服务终结点是一种网络结构，通过该网络结构，只能从虚拟网络内部访问公共 DNS/公共 IP。 由于无法保证公共 IP 保持不变，因此我们最终希望对存储帐户使用专用终结点而不是服务终结点，但是，除非同时公开服务终结点，否则无法限制存储帐户。

请记得将 `<storage-account-name>` 替换为要访问的存储帐户。

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>创建专用终结点（预览）
为存储帐户创建专用终结点可为存储帐户提供虚拟网络 IP 地址空间内的 IP 地址。 使用此专用 IP 地址从本地装载 Azure 文件共享时，VPN 安装自动定义的传递规则将通过 VPN 将装载请求路由到存储帐户。 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>为 VPN 身份验证创建证书
为了对来自本地 Linux 计算机的 VPN 连接进行身份验证以访问虚拟网络，必须创建两个证书：根证书（将提供给虚拟机网关）和客户端证书（将使用根证书进行签名）。 以下脚本将创建所需的证书。

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>部署虚拟网络网关
Azure 虚拟网络网关是本地 Linux 计算机将连接到的服务。 部署此服务需要两个基本组件：一个公共 IP（无论客户端位于何处，都可用此 IP 来标识其网关），一个之前创建的根证书（将用于对客户端进行身份验证）。

请记得将 `<desired-vpn-name-here>` 替换为要为这些资源使用的名称。

> [!Note]  
> 部署 Azure 虚拟网络网关最多需要 45 分钟。 部署此资源时，此 bash script 脚本将阻止部署完成。 这是正常情况。

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>配置 VPN 客户端
Azure 虚拟网络网关将创建一个可下载的包，其中包含在本地 Linux 计算机上初始化 VPN 连接所需的配置文件。 下面的脚本会将你创建的证书放在正确的位置，并用可下载包中的配置文件中的正确值配置 `ipsec.conf` 文件。

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>装载 Azure 文件共享
设置点到站点 VPN 后，现在就可以装载 Azure 文件共享。 以下示例将非持久地装载共享。 若要永久装载，请参阅[将 Azure 文件共享与 Linux 配合使用](storage-how-to-use-files-linux.md)。 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>另请参阅
- [Azure 文件存储网络概述](storage-files-networking-overview.md)
- [在 Windows 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-p2s-vpn-windows.md)
- [配置站点到站点 (S2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)