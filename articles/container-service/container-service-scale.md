---
title: "使用 Azure CLI 缩放 ACS 群集| Microsoft Docs"
description: "如何使用 Azure CLI 缩放 Azure 容器服务群集。"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>缩放 Azure 容器服务
通过使用 Azure CLI 工具，可扩大 Azure 容器服务 (ACS) 的节点数。 使用 Azure CLI 进行缩放时，此工具将返回表示应用于容器的更改的新配置文件。

## <a name="about-the-command"></a>关于命令
Azure CLI 必须处于 Azure Resource Manager 模式下，以便你与 Azure 容器进行交互。 通过调用 `azure config mode arm` 切换到 Resource Manager 模式。 `acs` 命令具有执行所有容器服务缩放操作的名为 `scale` 的子命令。 可以通过运行输出类似于如下内容的 `azure acs scale --help`，获取有关缩放命令中使用的各种参数的帮助：

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>使用命令以缩放
若要缩放容器服务，首先需要了解**资源组**和 **Azure 容器服务 (ACS) 名称**，并指定新的代理计数。 使用较小或较高的计数，可分别向下或向上扩展。

缩放前，可能需了解容器服务的当前代理计数。 使用 `azure acs show <resource group> <ACS name>` 命令返回 ACS 配置。 请注意<mark>计数</mark>结果。

#### <a name="see-current-count"></a>查看当前计数
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>缩放到新的计数
由于它可能是不证自明的，因此可以通过调用 `azure acs scale` 并提供**资源组**、**ACS 名称**和**代理计数**，来缩放容器服务。 缩放容器服务时，Azure CLI 将返回表示容器服务新配置的 JSON 字符串，包括新的代理计数。

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>后续步骤
* [部署群集](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


