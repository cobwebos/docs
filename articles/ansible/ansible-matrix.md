---
title: 适用于 Azure 的 Ansible 模块和版本矩阵 |Microsoft Docs
description: 适用于 Azure 的 Ansible 模块和版本矩阵
keywords: ansible, 角色, 矩阵, 版本, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 275dca40ab20c222da2b9115f9a5dc141228c766
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385468"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模块和版本矩阵

Ansible 包括一套用于预配和配置 Azure 资源的模块。 这些资源包括虚拟机、规模集、网络服务和容器服务。 本文列出了 Azure 的各种 Ansible 模块及其随附的 Ansible 版本。

## <a name="ansible-modules-for-azure"></a>适用于 Azure 的 Ansible 模块

可以直接在远程主机上或通过行动手册来执行以下模块。  

可从 Ansible 官方版本和以下 Microsoft 操作手册角色获取这些模块。

> [!NOTE]
> 从 Ansible 2.9 开始，我们已将所有 * _facts 模块重命名为 * _info，遵循 Ansible 命名约定。 旧的和已重命名的模块会被链接起来，因为看不到弃用警告，所有模块都将像以前一样工作。

| 适用于 Azure 的 Ansible 模块                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2。8 | Ansible 2。9 | Ansible 角色 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **计算**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_availabilityset_info              | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_deployment                         | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_functionapp                        | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_functionapp_info                  | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_image                              | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_image_info                        | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_resource                           | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_resource_info                     | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_resourcegroup                      | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_resourcegroup_info                | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_virtualmachine                     | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachineextension            | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachineimage_info          | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescaleset             | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescaleset_info       | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | 是          | 是          | 是          |
| **网络**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_dnsrecordset                       | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_dnsrecordset_info                 | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_dnszone                            | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_dnszone_info                      | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_loadbalancer                       | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_loadbalancer_info                 | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_networkinterface                   | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_networkinterface_info             | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_publicipaddress                    | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_publicipaddress_info              | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_route                              | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_routetable                         | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_routetable_info                   | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_securitygroup                      | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_subnet                             | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetwork                     | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetwork_info               | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | 是          | 是          |
| **存储**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_manageddisk_info                  | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_storageaccount                     | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_storageaccount_info               | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_storageblob                        | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_webapp                             | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_webapp_info                       | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | 是          | 是          | 是          |
| **容器**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_aks                                | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_aks_info                          | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_containerinstance                  | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_containerregistry                  | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_containerregistry_info            | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | 是          |
| **数据库**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqldatabase                      | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlserver                        | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqldatabase                 | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqlserver                   | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_sqldatabase                        | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_sqlserver                          | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_sqlserver_info                    | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| **分析**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | 是          | 是          |
| **集成**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | 是          | 是          | 是          |
| **安全性**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | 是          | 是          | 是          |
| azure_rm_keyvaultkey                        | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_keyvaultsecret                     | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | 是          | 是          | 是          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | 是          | 是          | 是          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_autoscale_info                    | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | 是          | 是          |
| **管理和管理**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | 是        | 是          |
| **物联网**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | 是        | 是          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure 的 playbook 角色简介

[Azure_preview_module 操作手册角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)包括所有最新的 azure 模块。 更新和 Bug 修补程序比正式 Ansible 发布更及时完成。 如果使用 Ansible 进行 Azure 资源预配，则建议安装 `azure_preview_module` 操作手册角色。

每三周发布一次 `azure_preview_module` 操作手册角色。

## <a name="next-steps"></a>后续步骤

有关操作手册角色的详细信息，请参阅[创建可重用的行动手册](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)。 