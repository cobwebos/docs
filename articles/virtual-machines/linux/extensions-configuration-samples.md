---
title: "Linux VM 扩展的示例配置 | Microsoft Docs"
description: "使用扩展为 Linux VM 创作模板的示例配置"
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4f50e6b2-fce0-41ef-823d-df433957601a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 7bdc28328f29005ae48cc281a05fce7067c96556
ms.lasthandoff: 04/03/2017


---
# <a name="linux-vm-extension-configuration-samples"></a>Linux VM 扩展配置示例
> [!div class="op_single_selector"]
> * [PowerShell - 模板](../windows/extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [CLI - 模板](../windows/extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

本文提供为 Linux VM 配置 Azure VM 扩展的示例配置。

若要了解有关这些扩展的详细信息，请单击此处：[Azure VM 扩展概述](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

若要了解有关创作扩展模板的详细信息，请单击此处：[创作扩展模板](../windows/extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

本文列出了一些 Linux 扩展的预期配置值。

## <a name="sample-template-snippet-for-vm-extensions"></a>VM 扩展的示例模板代码段。
部署扩展的模板代码段如下所示：

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "autoUpgradeMinorVersion":true,
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

## <a name="sample-template-snippet-for-vm-extensions-with-vm-scale-sets"></a>适用于包含 VM 规模集的 VM 扩展的示例模板代码段。
          {
           "type":"Microsoft.Compute/virtualMachineScaleSets",
          ....
                 "extensionProfile":{
                 "extensions":[
                   {
                     "name":"extension Name",
                     "properties":{
                       "publisher":"Publisher Namespace",
                       "type":"extension Name",
                       "typeHandlerVersion":"extension version",
                       "autoUpgradeMinorVersion":true,
                       "settings":{
                       // Extension specific configuration goes in here.
                       }
                     }
                    }
                  }
                }

在部署此扩展之前，请检查最新的扩展版本，然后将“typeHandlerVersion”替换为当前最新版本。

本文剩余部分提供 Linux VM 扩展的示例配置。

### <a name="cloudlink-securevm-agent"></a>CloudLink SecureVM 代理
          {
            "publisher": "CloudLinkEMC.SecureVM",
            "type": "CloudLinkSecureVMLinuxAgent",
            "typeHandlerVersion": "4.0",
            "settings": {
              "CloudLinkCenter" : "specify valid IP/FQDN to CloudLinkCenter"
            }
          }

### <a name="customscript-extension-for-linux"></a>适用于 Linux 的 CustomScript 扩展。
    {
        "publisher": " Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
            ],
            "commandToExecute": "powershell.exe-ExecutionPolicyUnrestricted-Filestart.ps1"
        }
    }


### <a name="datadog-agent"></a>Datadog 代理
        {
          "publisher": "Datadog.Agent",
          "type": "DatadogLinuxAgent",
          "typeHandlerVersion": "0.4",
          "settings": {
            "api_key" : "API Key from https://app.datadoghq.com/account/settings#api"
          }
        }

### <a name="chef-agent"></a>Chef 代理
        {
          "publisher": "Chef.Bootstrap.WindowsAzure",
          "type": "CentosChefClient|LinuxChefClient",
          "typeHandlerVersion": "1210.12",
          "settings": {
            "validation_key" : " Validation key",
            "client_rb" : "client_rb file",
            "runlist" : "Optional runlist"
          }
        }

### <a name="vm-access-extension-password-reset"></a>VM 访问扩展（密码重置）
有关更新的架构，请参阅 [VMAccessForLinux 文档](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

        {
          "publisher": "Microsoft.OSTCExtensions",
          "type": "VMAccessForLinux",
          "typeHandlerVersion": "1.2",
          "protectedSettings": {
            "username": "(required, string) the name of the user",
            "password": "(optional, string) the password of the user",
            "reset_ssh": "(optional, boolean) whether or not reset the ssh",
            "ssh_key": "(optional, string) the public key of the user, base64 encoded pem",
            "remove_user": "(optional, string) the user name to remove"
          }
        }

### <a name="os-patching"></a>OS 修补
有关更新的架构，请参阅 [OSPatching 文档](https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching)

        {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "OSPatchingForLinux",
        "typeHandlerVersion": "2.9",
        "Settings": {
          "disabled": false,
          "stop": false,
          "rebootAfterPatch": "RebootIfNeed|Required|NotRequired|Auto",
          "category": "Important|ImportantAndRecommended",
          "installDuration": "<hr:min>",
          "oneoff": false,
          "intervalOfWeeks": "<number>",
          "dayOfWeek": "Sunday|Monday|Tuesday|Wednesday|Thursday|Friday|Saturday|Everyday",
          "startTime": "<hr:min>",
          "vmStatusTest": {
              "local": false,
              "idleTestScript": "<path_to_idletestscript>",
              "healthyTestScript": "<path_to_healthytestscript>"
          }
        }
        }

### <a name="docker-extension"></a>Docker 扩展
有关更新的架构，请参阅 [Docker 扩展文档](https://github.com/Azure/azure-docker-extension/blob/master/README.md#1-configuration-schema)

        {
          "publisher": "Microsoft.Azure.Extensions ",
          "type": "DockerExtension ",
          "typeHandlerVersion": "1.0",
          "Settings": {
            "docker":{
                "port": "2376",
                "options": ["-D", "--dns=8.8.8.8"]
            },
            "compose": {
                "cache" : {
                    "image" : "memcached",
                    "ports" : ["11211:11211"]
                },
                "blog": {
                    "image": "ghost",
                    "ports": ["80:2368"]
                }
            }
            }
        }

        ### Linux Diagnostics Extension
        {
        "storageAccountName": "storage account to receive data",
        "storageAccountKey": "key of the account",
        "perfCfg": [
        {
            "query": "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
            "table": "LinuxMemory"
        }
        ],
        "fileCfg": [
        {
            "file": "/var/log/mysql.err",
            "table": "mysqlerr"
        }
        ]
        }

在上述示例中，请将版本号替换为最新版本号。

以下是使用扩展创建 Linux VM 的完整 VM 模板：

[Linux VM 上的自定义脚本扩展](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)


