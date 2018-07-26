---
title: 使用 Ansible 将应用程序部署到 Azure 中的虚拟机规模集
description: 了解如何使用 Ansible 配置虚拟机规模集并将应用程序部署到 Azure 中的虚拟机规模集上
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, 虚拟机, 虚拟机规模集, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011495"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>使用 Ansible 将应用程序部署到 Azure 中的虚拟机规模集
使用 Ansible 可以在环境中自动部署和配置资源。 可以使用 Ansible 将应用程序部署到 Azure。 本文介绍如何将 Java 应用程序部署到 Azure 虚拟机规模集 (VMSS)。  

## <a name="prerequisites"></a>先决条件
- **Azure 订阅** - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **配置 Ansible** - [创建 Azure 凭据并配置 Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible 和 Azure Python SDK 模块** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **虚拟机规模集** - 如果还没有虚拟机规模集，可以[使用 Ansible 创建虚拟机规模集](ansible-create-configure-vmss.md)。 
- **git** - [git](https://git-scm.com) 用于下载本教程中使用的 Java 示例。
- **Java SE 开发工具包 (JDK)** - 该 JDK 用于生成示例 Java 项目。
- **Apache Maven 生成工具** - [Apache Maven 生成工具](https://maven.apache.org/download.cgi)用于生成示例 Java 项目。

> [!Note]
> 在本教程中运行以下示例 playbook 需要 Ansible 2.6。 

## <a name="get-host-information"></a>获取主机信息

本部分说明如何使用 Ansible 检索一组 Azure 虚拟机的主机信息。 下面是示例 Ansible playbook。 该代码获取指定资源组中的公共 IP 地址和负载均衡器，并在清单中创建名为 **saclesethosts** 的主机组。 

将以下示例 playbook 保存为 `get-hosts-tasks.yml`： 

  ```yaml
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

在本部分中，将使用 git 从 GitHub 克隆 Java 示例项目并生成该项目。 将以下 playbook 保存为 `app.yml`：

  ```yaml
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

ansible-playbook 命令的输出显示类似于以下内容，其中你可以看到它生成了从 GitHub 克隆的示例应用：

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>将应用程序部署到 VMSS

Ansible playbook 中的以下部分在名为 **saclesethosts** 的主机组上安装 JRE（Java 运行时环境），并将 Java 应用程序部署到名为 **saclesethosts** 的主机组： 

（将 `admin_password` 更改为你自己的密码。）

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

可以将前面的示例 Ansible playbook 保存为 `vmss-setup-deploy.yml`，或[下载整个示例 playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss)。 

若要将 ssh 连接类型与密码一起使用，必须安装 sshpass 程序。 
  - 对于 Ubunto 16.04，请运行命令 `apt-get install sshpass`。
  - 对于 CentOS 7.4，请运行命令 `yum install sshpass`。

你可能会看到如下错误：“不可以使用 SSH 密码而不是密钥，因为启用了主机密钥检查并且 sshpass 不支持此密码。请将此主机的指纹添加到 known_hosts 文件，以便管理此主机”。 如果看到此错误，可以通过将以下行添加到 `/etc/ansible/ansible.cfg` 文件或 `~/.ansible.cfg` 文件来禁用主机密钥检查：
  ```bash
  [defaults]
  host_key_checking = False
  ```

使用以下命令运行 playbook：

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

运行 ansible-playbook 命令的输出指示示例 Java 应用程序已安装到虚拟机规模集的主机组：

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

祝贺你！ 你的应用程序现在正在 Azure 中运行。 现在可以导航到虚拟机规模集的负载均衡器的 URL：

![在 Azure 的虚拟机规模集中运行的 Java 应用。](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [适用于 VMSS 的 Ansible 示例 playbook](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)