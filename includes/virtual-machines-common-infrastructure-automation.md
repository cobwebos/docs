# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>在 Azure 中将基础结构自动化工具与虚拟机配合使用
若要以一致的方式大规模创建和管理 Azure 虚拟机 (VM)，通常需要某种形式的自动化。 可以通过许多工具和解决方案来自动完成整个 Azure 基础结构部署和管理生命周期。 本文介绍了一些可以在 Azure 中使用的基础结构自动化工具。 这些工具通常适合以下某个方法：

- 自动执行 VM 的配置
    - 工具包括 [Ansible](#ansible)、[Chef](#chef) 和 [Puppet](#puppet)。
    - 特定于 VM 自定义的工具包括适用于 Linux VM 的 [cloud-init](#cloud-init)、[PowerShell Desired State Configuration (DSC)](#powershell-dsc)，以及适用于所有 Azure VM 的 [Azure 自定义脚本扩展](#azure-custom-script-extension)。
 
- 自动化基础结构管理
    - 工具包括用于自动完成自定义 VM 映像生成的 [Packer](#packer)，以及用于自动完成基础结构生成过程的 [Terraform](#terraform)。
    - [Azure 自动化](#azure-automation)可以跨 Azure 和本地基础结构执行操作。

- 自动执行应用程序部署和交付
    - 示例包括 [Visual Studio Team Services](#visual-studio-team-services) 和 [Jenkins](#jenkins)。


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) 是适用于配置管理、VM 创建或应用程序部署的自动化引擎。 Ansible 通常将无代理的模型与 SSH 密钥配合使用，对目标计算机进行身份验证和管理。 配置任务在 playbook 中定义，可以使用多个 Ansible 模块来执行特定任务。 有关详细信息，请参阅 [How Ansible works](https://www.ansible.com/how-ansible-works)（Ansible 工作原理）。

了解如何：

- [在 Linux 上安装和配置与 Azure 配合使用的 Ansible](../articles/virtual-machines/linux/ansible-install-configure.md)。
- [创建基本 VM](../articles/virtual-machines/linux/ansible-create-vm.md)。
- [创建包括支持资源在内的完整 VM 环境](../articles/virtual-machines/linux/ansible-create-complete-vm.md)。


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) 是一个自动化平台，用于定义基础结构的配置、部署和管理方式。 其他组件包括：Chef Habitat，用于应用程序生命周期自动化而不是基础结构；Chef InSpec，用于自动遵循安全和策略要求。 Chef 客户端安装在目标计算机上，通过一个或多个中心 Chef 服务器来存储和管理配置。 有关详细信息，请参阅 [An Overview of Chef](https://docs.chef.io/chef_overview.html)（Chef 概述）。

了解如何：

- 
  [从 Azure 市场部署 Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)。
- [在 Windows 上安装 Chef 并创建 Azure VM](../articles/virtual-machines/windows/chef-automation.md)。


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) 是可供企业使用的自动化平台，用于处理应用程序交付和部署过程。 代理安装在目标计算机上，因此 Puppet Master 可以通过运行清单来定义 Azure 基础结构和 VM 的所需配置。 Puppet 可以通过集成其他解决方案（例如 Jenkins 和 GitHub）来改进 DevOps 工作流。 有关详细信息，请参阅 [How Puppet works](https://puppet.com/product/how-puppet-works)（Puppet 工作原理）。

了解如何：

- 
  [从 Azure 市场部署 Puppet](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview)。


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置 `#cloud-config` 文件格式的详细信息，请参阅 [cloud-init 文档站点](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

Cloud-init 还支持不同的分发。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 Cloud-init 将为所选发行版自动使用本机包管理工具。

 我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。 下表概述了当前启用了 cloud-init 的映像在 Azure 平台上的可用性：

| 发布者 | 产品/服务 | SKU | 版本 | cloud-init 就绪
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |最新 |是 | 
|Canonical |UbuntuServer |14.04.5-LTS |最新 |是 |
|CoreOS |CoreOS |Stable |最新 |是 |
|OpenLogic |CentOS |7-CI |最新 |预览 |
|RedHat |RHEL |7-RAW-CI |最新 |预览 |

了解有关 Azure 上的 cloud-init 的更多详细信息：

- [Cloud-init 对 Azure 中 Linux 虚拟机的支持](../articles/virtual-machines/linux/using-cloud-init.md)
- [试着学习使用 cloud-init 自动进行 VM 配置的教程](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md)。


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) 是一个管理平台，用于定义目标计算机的配置。 也可通过 [Open Management Infrastructure (OMI) 服务器](https://collaboration.opengroup.org/omi/)在 Linux 上使用 DSC。

DSC 配置定义要在计算机上安装的内容，以及如何配置主机。 本地配置管理器 (LCM) 引擎在每个目标节点上运行，此类节点根据推送的配置处理请求的操作。 拉取服务器是一项在中心主机上运行的 Web 服务，用于存储 DSC 配置和关联的资源。 该拉取服务器与每个目标主机上的 LCM 引擎通信，提供所需的配置并报告符合性情况。

了解如何：

- [创建基本的 DSC 配置](https://msdn.microsoft.com/powershell/dsc/quickstart)。
- [配置 DSC 拉取服务器](https://msdn.microsoft.com/powershell/dsc/pullserver)。
- [使用适用于 Linux 的 DSC](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted)。


## <a name="azure-custom-script-extension"></a>Azure 自定义脚本扩展
适用于 [Linux](../articles/virtual-machines/linux/extensions-customscript.md) 或 [Windows](../articles/virtual-machines/windows/extensions-customscript.md) 的 Azure 自定义脚本扩展在 Azure VM 上下载和执行脚本。 可以在创建 VM 时使用该扩展，也可以在 VM 处于使用状态后随时使用该扩展。 

可以从 Azure 存储或任何公共位置（例如 GitHub 存储库）下载脚本。 使用自定义脚本扩展时，可以通过在源 VM 上运行的任何语言来编写脚本。 可以根据需要使用这些脚本来安装应用程序或配置该 VM。 若要确保凭据的安全，可将密码之类的敏感信息存储在受保护配置中。 这些凭据只在 VM 内解密。

了解如何：

- [通过 Azure CLI 创建 Linux VM 并使用自定义脚本扩展](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json)。
- [通过 Azure PowerShell 创建 Windows VM 并使用自定义脚本扩展](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json)。


## <a name="packer"></a>Packer
在 Azure 中创建自定义 VM 映像时，[Packer](https://www.packer.io) 会自动完成生成过程。 可以使用 Packer 来定义 OS 并运行配置后脚本，根据具体需求来自定义 VM。 配置完成后，会将 VM 作为托管磁盘映像捕获。 Packer 自动完成创建源 VM、网络和存储资源，运行配置脚本，然后创建 VM 映像这一过程。

了解如何：

- [在 Azure 中使用 Packer 创建 Linux VM 映像](../articles/virtual-machines/linux/build-image-with-packer.md)。
- [在 Azure 中使用 Packer 创建 Windows VM 映像](../articles/virtual-machines/windows/build-image-with-packer.md)。


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) 是一项自动化工具，用于通过 HashiCorp Configuration Language (HCL) 这个单一模板格式语言来定义和创建整个 Azure 基础结构。 可以使用 Terraform 来定义模板，自动完成为给定应用程序解决方案创建网络、存储和 VM 资源这一过程。 可以将适合其他平台的现有 Terraform 模板用于 Azure 以确保一致性并简化基础结构部署，不需转换为 Azure 资源管理器模板。

了解如何：

- [使用 Azure 安装和配置 Terraform](../articles/virtual-machines/linux/terraform-install-configure.md)。
- [使用 Terraform 创建 Azure 基础结构](../articles/virtual-machines/linux/terraform-create-complete-vm.md)。


## <a name="azure-automation"></a>Azure 自动化
[Azure 自动化](https://azure.microsoft.com/services/automation/)使用 Runbook 在目标 VM 上处理一组任务。 Azure 自动化用于管理现有 VM，而不是创建基础结构。 Azure 自动化可以跨 Linux 和 Windows VM 运行，还可以通过混合 Runbook 辅助角色在本地虚拟机或物理计算机上运行。 可以将 Runbook 存储在源代码管理存储库（例如 GitHub）中。 然后即可手动运行或按定义的计划运行这些 Runbook。

Azure 自动化还提供 Desired State Configuration (DSC) 服务，用于针对给定的一组 VM 的配置方式创建定义。 然后，DSC 就可以确保所需配置得到应用且 VM 保持一致。 Azure 自动化 DSC 可以在 Windows 和 Linux 计算机上运行。

了解如何：

- [创建 PowerShell Runbook](../articles/automation/automation-first-runbook-textual-powershell.md)。
- [使用混合 Runbook 辅助角色管理本地资源](../articles/automation/automation-hybrid-runbook-worker.md)。
- [使用 Azure 自动化 DSC](../articles/automation/automation-dsc-getting-started.md)。


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Team Services](https://www.visualstudio.com/team-services/) 是一套工具，用于共享和跟踪代码、使用自动化生成，以及创建完整的持续集成和开发 (CI/CD) 管道。 Team Services 集成 Visual Studio 和其他编辑器，简化了使用过程。 Team Services 还可以创建和配置 Azure VM，然后向其部署代码。

了解如何：

- [使用 Team Services 创建持续集成管道](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md)。


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) 是一种持续集成服务器，用于部署和测试应用程序，以及创建进行代码交付的自动化管道。 可以通过成百上千的插件来扩展核心 Jenkins 平台，还可以通过 Webhook 集成许多其他的产品和解决方案。 可以在 Azure VM 上手动安装 Jenkins，从 Docker 容器内部运行 Jenkins，或者使用预建的 Azure 市场映像。

了解如何：

- [使用 Jenkins、GitHub 和 Docker 在 Azure 中的 Linux VM 上创建开发基础结构](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md)。


## <a name="next-steps"></a>后续步骤
可以通过许多不同的选项，在 Azure 中使用基础结构自动化工具。 可以自由使用最适合自己的需求和环境的解决方案。 若要开始尝试一些内置到 Azure 的工具，请了解如何自动完成 [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) 或 [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM 的自定义。
