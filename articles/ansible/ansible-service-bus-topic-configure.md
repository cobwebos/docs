---
title: 教程 - 使用 Ansible 配置 Azure 服务总线主题 | Microsoft Docs
description: 了解如何使用 Ansible 创建 Azure 服务总线主题
keywords: ansible, azure, devops, bash, playbook, service bus, topics, subscriptions
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ca8d849796520ac260d888d772c064316db68a30
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230870"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>教程：使用 Ansible 配置 Azure 服务总线主题

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 创建主题
> * 创建订阅
> * 创建 SAS 策略
> * 检索命名空间信息
> * 检索主题和订阅信息
> * 撤销 SAS 策略

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>创建服务总线主题

示例 playbook 代码将创建以下资源：
- Azure 资源组
- 资源组中的服务总线命名空间
- 服务总线命名空间主题

将以下 playbook 保存为 `servicebus_topic.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>创建订阅

示例 playbook 代码在服务总线主题下创建订阅。 Azure 服务总线主题可有多个订阅。 主题的订阅者可以收到发送到该主题的每条消息的副本。 订阅是命名实体，可以采用持久方式来创建，但是也可过期。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

将以下 playbook 保存为 `servicebus_subscription.yml`：

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>创建 SAS 策略

[共享访问签名 (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 是使用令牌的基于声明的授权机制。 

示例 playbook 代码为一个服务总线队列创建具有不同权限的两个 SAS 策略。

将以下 playbook 保存为 `servicebus_topic_policy.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook servicebus_topic_policy.yml
```

## <a name="retrieve-namespace-information"></a>检索命名空间信息

示例 playbook 代码将查询命名空间信息。

将以下 playbook 保存为 `servicebus_namespace_info.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

运行 playbook 之前，请参阅以下说明：
- `show_sas_policies` 值指示是否在指定的命名空间下显示 SAS 策略。 默认情况下，值为 `False`，以避免额外的网络开销。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>检索主题和订阅信息

示例 playbook 代码将查询以下信息：
- 服务总线主题信息
- 主题的订阅详细信息列表
 
将以下 playbook 保存为 `servicebus_list.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

运行 playbook 之前，请参阅以下说明：
- `show_sas_policies` 值指示是否在指定的队列下显示 SAS 策略。 默认情况下，此值设置为 `False`，以避免额外的网络开销。

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook servicebus_list.yml
```

## <a name="revoke-the-queue-sas-policy"></a>撤销队列 SAS 策略

示例 playbook 代码将删除队列 SAS 策略。

将以下 playbook 保存为 `servicebus_queue_policy_delete.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook servicebus_topic_policy_delete.yml
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请将其删除。 

将以下代码保存为 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

使用 `ansible-playbook` 命令运行 playbook：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [Azure 上的 Ansible](/azure/ansible/)