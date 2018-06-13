---
title: 适用于 Azure 的 Ansible 模块和版本矩阵
description: 适用于 Azure 的 Ansible 模块和版本矩阵
ms.service: ansible
keywords: ansible, 角色, 矩阵, 版本, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 03/25/2018
ms.topic: article
ms.openlocfilehash: 011cb173ffdecc7a22c2e470209719ccaf6bda58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "30834140"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模块和版本矩阵

## <a name="ansible-modules-for-azure"></a>适用于 Azure 的 Ansible 模块
Ansible 配备了多个模块，这些模块可远程主机上直接执行或通过 Playbook 执行。
本文列出了适用于 Azure 的可以预配 Azure 云资源（如虚拟机、网络和容器服务）的 Ansible 模块。 可以从 Ansible 正式版或 Microsoft 发布的以下 playbook 角色获取这些模块。

| 适用于 Azure 的 Ansible 模块                   |  Ansible 2.4 |  Ansible 2.5 |  Playbook Role [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **计算**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | 是          | 是                         | 是                                 | 
| azure_rm_availabilityset_facts              | 是          | 是                         | 是                                 | 
| azure_rm_deployment                         | 是          | 是                         | 是                                 | 
| azure_rm_virtualmachine_scaleset_facts      | 是          | 是                         | 是                                 | 
| azure_rm_virtualmachineimage_facts          | 是          | 是                         | 是                                 | 
| azure_rm_resourcegroup                      | 是          | 是                         | 是                                 | 
| azure_rm_resourcegroup_facts                | 是          | 是                         | 是                                 | 
| azure_rm_virtualmachine                     | 是          | 是                         | 是                                 | 
| azure_rm_virtualmachine_extension           | 是          | 是                         | 是                                 | 
| azure_rm_virtualmachine_scaleset            | 是          | 是                         | 是                                 | 
| azure_rm_image                              |              | 是                         | 是                                 | 
| **网络**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | 是          | 是                         | 是                                 | 
| azure_rm_virtualnetwork_facts               | 是          | 是                         | 是                                 | 
| azure_rm_subnet                             | 是          | 是                         | 是                                 | 
| azure_rm_networkinterface                   | 是          | 是                         | 是                                 | 
| azure_rm_networkinterface_facts             | 是          | 是                         | 是                                 | 
| azure_rm_publicipaddress                    | 是          | 是                         | 是                                 | 
| azure_rm_publicipaddress_facts              | 是          | 是                         | 是                                 | 
| azure_rm_dnsrecordset                       | 是          | 是                         | 是                                 | 
| azure_rm_dnsrecordset_facts                 | 是          | 是                         | 是                                 | 
| azure_rm_dnszone                            | 是          | 是                         | 是                                 | 
| azure_rm_dnszone_facts                      | 是          | 是                         | 是                                 | 
| azure_rm_loadbalancer                       | 是          | 是                         | 是                                 | 
| azure_rm_loadbalancer_facts                 | 是          | 是                         | 是                                 | 
| azure_rm_appgw                              | -            | -                           | 是                                 | 
| azure_rm_appgwroute                         | -            | -                           | 是                                 | 
| azure_rm_appgwroute                         | -            | -                           | 是                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | 是                                 |
| azure_rm_appgwroutetable                    | -            | -                           | 是                                 |
| azure_rm_securitygroup                      | 是          | 是                         | 是                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | 是                                 | 
| **存储**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | 是          | 是                         | 是                                 | 
| azure_rm_storageaccount_facts               | 是          | 是                         | 是                                 | 
| azure_rm_storageblob                        | 是          | 是                         | 是                                 | 
| azure_rm_managed_disk                       | 是          | 是                         | 是                                 | 
| azure_rm_managed_disk_facts                 | 是          | 是                         | 是                                 | 
| **容器**                    |           |                          |                                  | 
| azure_rm_acs                                | 是          | 是                         | 是                                 | 
| azure_rm_containerinstance                  | -            | 是                         | 是                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | 是                                 | 
| azure_rm_containerregistry                  | -            | 是                         | 是                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | 是                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | 是                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | 是                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | 是                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | 是                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | 是          | 是                         | 是                                 | 
| azure_rm_functionapp_facts                  | 是          | 是                         | 是                                 | 
| **数据库**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | 是                         | 是                                 | 
| azure_rm_sqlserver_facts                    | -            | 是                         | 是                                 | 
| azure_rm_sqldatabase                        | -            | 是                         | 是                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | 是                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | 是                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | 是                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | 是                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | 是                                 | 
| azure_rm_mysqlserver                        | -            | 是                         | 是                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | 是                                 | 
| azure_rm_mysqldatabase                      | -            | 是                         | 是                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | 是                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | 是                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | 是                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | 是                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | 是                                 | 
| azure_rm_postgresqlserver                   | -            | 是                         | 是                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | 是                                 | 
| azure_rm_postgresqldatabase                 | -            | 是                         | 是                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | 是                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | 是                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | 是                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | 是                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | 是                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | 是                         | 是                                 |
| azure_rm_keyvault_facts                     | -            | -                           | 是                                 |
| azure_rm_keyvaultkey                        | -            | 是                         | 是                                 |
| azure_rm_keyvaultsecret                     | -            | 是                         | 是                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Azure 的 playbook 角色简介
[azure_preview_module playbook 角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)是最完整的角色，包括所有最新的 Azure 模块。 更新和 Bug 修补程序比正式 Ansible 发布更及时完成。 如果是将 Ansible 用于 Azure 资源预配目的，建议 azure_preview_module 角色。

azure_preview_module playbook 角色每三周发布一次。

## <a name="next-steps"></a>后续步骤
如需与 playbook 角色相关的详细信息，请参阅[创建可重用的 Playbook](http://docs.ansible.com/ansible/latest/playbooks_reuse.html)。 
