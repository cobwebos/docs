---
title: Azure Service Fabric 设置反向代理 | Microsoft Docs
description: 了解如何设置和配置 Service Fabric 的反向代理。
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 07/27/201
ms.author: v-jamebr
ms.openlocfilehash: c590c9d1ccbbb84a76ba09021a97464ec85c5784
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507208"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>在 Azure Service Fabric 中设置和配置反向代理
反向代理是一种可选的 Azure Service Fabric 服务，有助于在 Service Fabric 群集中运行的微服务发现包含 http 终结点的其他服务，并与之通信。 有关详细信息，请参阅 [Azure Service Fabric 中的反向代理](service-fabric-reverseproxy.md)。 本文介绍如何在群集中设置和配置反向代理。 

## <a name="enable-reverse-proxy-using-azure-portal"></a>使用 Azure 门户启用反向代理

在创建新的 Service Fabric 群集时，Azure 门户提供了一个启用反向代理的选项。 无法通过门户升级现有群集来使用反向代理。 

要在[使用 Azure 门户创建集群](./service-fabric-cluster-creation-via-portal.md)时配置反向代理，请确保执行以下操作：

1. 在“步骤 2：群集配置”中，在“节点类型配置”下，选择“启用反向代理”。

   ![在门户上启用反向代理](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. （可选）要配置安全反向代理，需要配置 SSL 证书。 在“步骤 3：安全性”中，在“配置群集安全设置”的“配置类型”下，选择“自定义”。 然后，在“反向代理 SSL 证书”下，选择“包括反向代理的 SSL 证书”并输入证书详细信息。

   ![在门户上配置安全反向代理](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   如果在创建群集时选择不使用证书配置反向代理，则可稍后通过群集资源组的资源管理器模板执行此操作。 有关详细信息，请参阅[通过 Azure 资源管理器模板启用反向代理](#enable-reverse-proxy-via-azure-resource-manager-templates)。

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>通过 Azure 资源管理器模板启用反向代理

对于 Azure 上的群集，可使用 Azure 资源管理器模板在 Service Fabric 中启用反向代理。 可在创建群集时启用反向代理，也可稍后通过更新群集来启用它。 

对于新群集，可[创建自定义资源管理器模板](service-fabric-cluster-creation-via-arm.md)，也可使用示例模板。 

可在 GitHub 上的[安全反向代理示例模板](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample)中找到可帮助你为 Azure 群集配置安全反向代理的示例资源管理器模板。 请参阅 README 文件中的[在安全群集中配置 HTTPS 反向代理](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster)，了解用于配置具有证书的安全反向代理和处理证书变换的说明和模板。

对于现有群集，可使用 [Azure 门户](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group)、[PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell#export-resource-group-as-template) 或 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli#export-resource-group-as-template) 导出群集资源组的资源管理器模板。

有了资源管理器模板后，可以通过以下步骤启用反向代理：

1. 在模板的[“参数”部分](../azure-resource-manager/resource-group-authoring-templates.md)定义反向代理的端口。

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. 在 [Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [资源类型部分](../azure-resource-manager/resource-group-authoring-templates.md)中为每个 nodetype 对象指定端口。

    端口由参数名称 reverseProxyEndpointPort 标识。

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. 要在反向代理的端口上配置 SSL 证书，请将证书添加到 Microsoft.ServiceFabric/clusters [资源类型部分](../resource-group-authoring-templates.md)中的 reverseProxyCertificate 属性。

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>支持不同于群集证书的反向代理证书
 如果反向代理证书不同于用于保护群集的证书，应将前面指定的证书安装在虚拟机上，并将其添加到访问控制列表 (ACL)，使 Service Fabric 能够访问它。 可在 [Microsoft.Compute/virtualMachineScaleSets](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [资源类型部分](../resource-group-authoring-templates.md)中执行此操作。 要安装，请将该证书添加到 osProfile。 模板的扩展节可以更新 ACL 中的证书。

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> 在现有群集上使用不同于群集证书的证书来启用反向代理时，请在启用反向代理之前在群集上安装反向代理证书并更新 ACL。 在执行步骤 1-3 开始部署以启用反向代理之前，请使用上述设置完成 [Azure 资源管理器模板](service-fabric-cluster-creation-via-arm.md)部署。

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>在独立群集上启用反向代理

对于独立群集，可在 ClusterConfig.json 文件中启用反向代理。 可在创建群集时启用反向代理，或通过升级现有群集的配置来启用。 要了解有关 ClusterConfig.json 文件中可用设置的详细信息，请参阅[独立群集设置](./service-fabric-cluster-manifest.md)。

以下步骤介绍了用于启用反向代理的设置，以及使用 X.509 证书（可选）保护反向代理的设置。 

1. 要启用反向代理，请在群集配置中的“属性”下为节点类型设置 reverseProxyEndpointPort 值。以下 JSON 说明，对于类型为“NodeType0”的节点，需将反向代理终结点端口设置为 19081：

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. （可选）对于安全反向代理，请在“属性”下的“安全”部分中配置证书。 
   - 对于开发环境或测试环境，可使用 ReverseProxyCertificate 设置：

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - 对于生产环境，建议使用 ReverseProxyCertificateCommonNames 设置：

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   要了解有关为独立群集配置和管理证书的详细信息，以及有关配置用于保护反向代理的证书的更多详细信息，请参阅 [X509 基于证书的安全性](./service-fabric-windows-cluster-x509-security.md)。

修改 ClusterConfig.json 文件以启用反向代理后，请按照[升级群集配置](./service-fabric-cluster-upgrade-windows-server.md#upgrade-the-cluster-configuration)中的说明，将更改推送到群集中。


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>通过 Azure 负载均衡器在公共端口上公开反向代理

要从 Azure 群集外部寻址反向代理，请为反向代理端口设置 Azure 负载均衡器规则和 Azure 运行状况探测。 创建群集后，可随时使用 Azure 门户或资源管理器模板执行这些步骤。 

> [!WARNING]
> 在负载均衡器中配置反向代理的端口后，可从群集外部访问群集中公开 HTTP 终结点的所有微服务。 这意味着微服务设计为内部的可能会被确定的恶意用户发现。 这潜在地提供可被利用的严重漏洞；例如：
>
> * 恶意用户可以通过反复调用没有足够强化的攻击面的内部服务来发起拒绝服务攻击。
> * 恶意用户可能会将格式错误的数据包传送到内部服务，从而导致意外行为。
> * 设计为内部的服务可能会返回不应公开给群集外部的服务的私有或敏感信息，从而将此敏感信息泄露给恶意用户。 
>
> 在公开反向代理端口之前，请确保完全了解并减轻对群集及其上运行的应用程序的潜在安全影响。 
>

如果要为独立群集公开反向代理，则执行此操作的方式将取决于托管群集的系统，本文未对此进行介绍。 然而，关于公开反向代理的前述警告仍然适用。

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>使用 Azure 门户公开反向代理 

1. 在 Azure 门户上，单击群集的资源组，然后单击群集的负载均衡器。
2. 要为反向代理端口添加运行状况探测，请在负载均衡器窗口的左窗格中的“设置”下，单击“运行状况探测”。 然后单击“运行状况探测”窗口顶部的“添加”并输入反向代理端口的详细信息，然后单击“确定”。 默认情况下，反向代理端口为 19081，除非在创建群集时更改了它。

   ![配置反向代理运行状况探测](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. 要添加负载均衡器规则以公开反向代理端口，请在“负载均衡器”窗口左窗格中的“设置”下，单击“负载均衡规则”。 然后单击“负载均衡规则”窗口顶部的“添加”并输入反向代理端口的详细信息。 确保将“端口”值设置为要在其上公开反向代理的端口，将“后端端口”值设置为启用反向代理时设置的端口，并将“运行状况探测”值设置为上一步中配置的运行状况探测。 根据需要设置其他字段，然后单击“确定”。

   ![配置反向代理的负载均衡器规则](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>通过资源管理器模板公开反向代理

以下 JSON 引用[通过 Azure 资源管理器模板启用反向代理](#enable-reverse-proxy-via-azure-resource-manager-templates)中使用的相同模板。 有关如何创建资源管理器模板或导出现有集群的模板的信息，请参阅文档的该部分。  其中对 [Microsoft.Network/loadBalancers](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [资源类型部分](../resource-group-authoring-templates.md)进行了更改。

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>使用结构设置自定义反向代理行为

可通过资源管理器模板中的结构设置为 Azure 中的托管群集或独立群集的 ClusterConfig.json 文件自定义反向代理的行为。 控制反向代理行为的设置位于群集“属性”部分下的 fabricSettings 部分中的 [ApplicationGateway/Http](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 部分。 

例如，可设置 DefaultHttpRequestTimeout 的值，以将对反向代理的请求的超时设置为 180 秒，如以下 JSON 中所示：

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

有关更新 Azure 群集的结构设置的详细信息，请参阅[使用资源管理器模板自定义群集设置](./service-fabric-cluster-fabric-settings.md#customize-cluster-settings-using-resource-manager-templates)。 对于独立群集，请参阅[自定义独立群集的群集设置](./service-fabric-cluster-fabric-settings.md#customize-cluster-settings-for-standalone-clusters)。 

可通过多个结构设置在反向代理和服务之间建立安全通信。 有关这些设置的详细信息，请参阅[使用反向代理连接到安全服务](service-fabric-reverseproxy-configure-secure-communication.md)。

## <a name="next-steps"></a>后续步骤
* [设置使用反向代理转发到安全的 HTTP 服务](service-fabric-reverseproxy-configure-secure-communication.md)
* 有关反向代理配置选项的信息，请参阅[自定义 Service Fabric 群集设置中的 ApplicationGateway/Http 部分](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)。
