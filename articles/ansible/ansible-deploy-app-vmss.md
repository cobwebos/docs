---
title: 教程 - 使用 Ansible 在 Azure 中将应用部署到虚拟机规模集 | Microsoft Docs
description: 了解如何使用 Ansible 配置 Azure 虚拟机规模集并在该规模集上部署应用程序
keywords: ansible, azure, devops, bash, playbook, 虚拟机, 虚拟机规模集, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a44fd06ace9b21122f5f4253ac7d9601b54e6b62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231042"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>教程：使用 Ansible 在 Azure 中部署虚拟机规模集

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 检索一组 Azure VM 的主机信息
> * 克隆并构建示例应用
> * 在规模集上安装 JRE（Java 运行时环境）
> * 将 Java 应用程序部署到规模集

## <a name="prerequisites"></a>先决条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) 用于下载本教程中使用的 Java 示例。
- **Java SE 开发工具包 (JDK)** - 此 [JDK](https://aka.ms/azure-jdks) 用于生成示例 Java 项目。
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) 用于构建示例 Java 项目。

## <a name="get-host-information"></a>获取主机信息

本部分中的 playbook 代码检索一组虚拟机的主机信息。 该代码获取指定资源组中的公共 IP 地址和负载均衡器，并在清单中创建名为 `scalesethosts` 的主机组。

将以下示例 playbook 保存为 `get-hosts-tasks.yml`：

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>准备用于部署的应用程序

本部分中的 playbook 代码使用 `git` 从 GitHub 克隆 Java 示例项目并构建项目。 

将以下 playbook 保存为 `app.yml`：

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

使用以下命令运行示例 Ansible playbook：

  ```bash
  ansible-playbook app.yml
  ```

运行 playbook 后，可看到类似于以下结果的输出：

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>将应用程序部署到规模集

本部分中的 playbook 代码用于：

* 在名为 `saclesethosts` 的主机组上安装 JRE
* 将 Java 应用程序部署到名为 `saclesethosts` 的主机组

可通过两种方式获取示例 playbook：

* [下载 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) 并将其保存到 `vmss-setup-deploy.yml`。
* 新建名为 `vmss-setup-deploy.yml` 的文件，并将以下内容复制到其中：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

运行 playbook 之前，请参阅以下说明：

* 在 `vars` 部分中，请将 `{{ admin_password }}` 占位符替换为你自己的密码。
* 若要将 ssh 连接类型与密码一起使用，请安装 sshpass 程序：

    Ubuntu：

    ```bash
    apt-get install sshpass
    ```

    CentOS：

    ```bash
    yum install sshpass
    ```

* 在某些环境中，可能会看到有关使用 SSH 密码而不是密钥的错误。 如果收到该错误，可通过将以下行添加到 `/etc/ansible/ansible.cfg` 或 `~/.ansible.cfg` 来禁用主机密钥检查：

    ```bash
    [defaults]
    host_key_checking = False
    ```

使用以下命令运行 playbook：

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

运行 ansible-playbook 命令的输出指示示例 Java 应用程序已安装到规模集的主机组：

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>验证结果

通过导航到规模集负载均衡器的 URL 来验证工作结果：

![在 Azure 的规模集中运行的 Java 应用。](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Ansible 在 Azure 中自动缩放虚拟机规模集](./ansible-auto-scale-vmss.md)