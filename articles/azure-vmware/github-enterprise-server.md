---
title: 在 Azure VMware 解决方案私有云上设置 GitHub Enterprise Server
description: 了解如何在 Azure VMware 解决方案私有云上设置 GitHub 企业服务器。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 53e5264eed761909217c2e3a902c9fee9faaffaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341313"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>在 Azure VMware 解决方案私有云上设置 GitHub Enterprise Server

本文逐步讲解如何在 Azure VMware 解决方案私有云上设置 GitHub Enterprise Server （即 "本地" 版本的 [GitHub.com](https://github.com/)）。 本演练中所述的方案适用于 GitHub 企业服务器实例，该实例可以为3000的开发人员提供最多每分钟在 GitHub 操作上运行最多25个作业的开发人员。 它包括编写) *预览版* 功能（如 GitHub 操作）时 (的设置。 若要根据特定需求自定义安装程序，请查看在 [VMware 上安装 GitHub 企业服务器](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)中列出的要求。

## <a name="before-you-begin"></a>在开始之前

GitHub Enterprise Server 需要有效的许可证密钥。 你可以注册 [试用版许可证](https://enterprise.github.com/trial)。 如果希望通过集成扩展 GitHub 企业服务器的功能，则可以使用免费的五位开发人员许可证。 通过 [GitHub 的合作伙伴计划](https://partner.github.com/)申请此许可证。

## <a name="installing-github-enterprise-server-on-vmware"></a>在 VMware 上安装 GitHub Enterprise Server

下载 [最新版本的 GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) for VMware ESXi/VSPHERE (.ova) 并 [部署下载的 .ova 模板](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) 。

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="选择在本地或云中运行 GitHub。":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="选择在本地或云中运行 GitHub。" (操作)  |
| --- | --- | --- |
| vCPU | 4 | 8 |
| 内存 | 32 GB | 61 GB |
| 附加存储 | 250 GB | 300 GB |
| 根存储 | 200 GB | 200 GB |

但是，您的需求可能有所不同。 请参阅在 [VMware 上安装 GitHub 企业服务器](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)中有关硬件注意事项的指南。 另请参阅 [添加 VMware 的 CPU 或内存资源](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) ，以根据你的情况自定义硬件配置。

## <a name="configuring-the-github-enterprise-server-instance"></a>配置 GitHub Enterprise Server 实例

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="选择在本地或云中运行 GitHub。":::  

新预配的虚拟机 (VM) 已打开，请 [通过浏览器进行配置](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance)。 需要上传许可证文件并设置管理控制台密码。 请确保将此密码写到安全的地方。

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="选择在本地或云中运行 GitHub。":::    

建议至少执行以下步骤：

1. 将公共 SSH 密钥上载到管理控制台，以便可以 [通过 SSH 访问命令行管理](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)程序。 

2. [在您的实例上配置 TLS](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) ，以便您可以使用由受信任的证书颁发机构签名的证书。

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="选择在本地或云中运行 GitHub。":::

应用您的设置。  实例重新启动时，可以继续执行下一步，即 **配置用于 GitHub 操作的 Blob 存储**。

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="选择在本地或云中运行 GitHub。":::

实例重新启动后，在该实例上创建一个新的管理员帐户。 请务必记下此用户的密码。

### <a name="additional-configuration-steps"></a>其他配置步骤

若要为生产用途强化实例，建议执行以下可选设置步骤：

1. 为保护配置 [高可用性](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) ：

    - 操作系统或应用程序级别 (的软件崩溃) 
    - 硬件故障 (存储、CPU、RAM 等) 
    - 虚拟化主机系统故障
    - 逻辑或物理上断开的网络

2. [配置](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance)[备份实用程序](https://github.com/github/backup-utils)，为灾难恢复提供版本化快照，这些快照在不同于主实例的可用性上托管。
3. 使用有效的 TLS 证书[设置子域隔离](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)，以减轻跨站点脚本和其他相关漏洞的影响。

## <a name="configuring-blob-storage-for-github-actions"></a>为 GitHub 操作配置 blob 存储

> [!NOTE]
> Github 操作 [当前作为 Github Enterprise Server 版本2.22 上的有限 beta 版本提供](https://docs.github.com/en/enterprise/admin/github-actions)。

若要启用 GitHub Enterprise Server (上的 GitHub 操作，可以使用外部 blob 存储) 。 此外部 blob 存储用于存储项目和日志的操作。 GitHub Enterprise Server 上 [的操作支持将 Azure Blob 存储作为存储提供程序](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (，一些其他) 。 我们将使用 [存储帐户类型](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#types-of-storage-accounts) BlobStorage 预配新的 Azure 存储帐户：

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="选择在本地或云中运行 GitHub。":::

完成新 BlobStorage 资源的部署后，复制并记下 "访问密钥")  (可用的连接字符串。 稍后我们将需要此字符串。

## <a name="setting-up-the-github-actions-runner"></a>设置 GitHub 操作运行程序

> [!NOTE]
> Github 操作 [当前作为 Github Enterprise Server 版本2.22 上的有限 beta 版本提供](https://docs.github.com/en/enterprise/admin/github-actions)。

此时，您应该具有运行 GitHub Enterprise Server 的实例，并创建了管理员帐户。 还应具有 GitHub 操作将用于持久性的外部 blob 存储。

现在，让我们在某个位置创建 GitHub 操作以运行;同样，我们将使用 Azure VMware 解决方案。

首先，让我们在群集上预配新的 VM。 我们会将 VM 基于 [Ubuntu 服务器的最新版本](http://releases.ubuntu.com/20.04.1/)。

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="选择在本地或云中运行 GitHub。":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="选择在本地或云中运行 GitHub。" 页](https://github.com/actions/runner/releases) 或通过运行以下快速脚本来识别和下载最新的操作运行程序的 Linux x64 版本。 此脚本要求在你的 VM 上同时出现卷曲和 [jq](https://stedolan.github.io/jq/) 。

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

现在，你应在 VM 上本地有一个文件，即 "操作-运行 arm64- \* gz"。 在本地提取此 tarball：

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

此提取在本地解压缩一些文件，其中包括 `config.sh` 和 `run.sh` 脚本，稍后我们将会回来。

## <a name="enabling-github-actions"></a>启用 GitHub 操作

> [!NOTE]
> Github 操作 [当前作为 Github Enterprise Server 版本2.22 上的有限 beta 版本提供](https://docs.github.com/en/enterprise/admin/github-actions)。

即将发生！ 让我们在 GitHub Enterprise Server 实例上配置和启用 GitHub 操作。 我们需要 [通过 SSH 访问 GitHub Enterprise Server 实例的命令行管理](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)程序，然后运行以下命令：

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

下次运行:

`ghe-actions-check -s blob`

应会看到输出： "Blob 存储状态良好"。

现在已配置 GitHub 操作，请为用户启用它。 以管理员身份登录 GitHub 企业服务器实例，并选择 " ![ 火箭" 图标。](media/github-enterprise-server/rocket-icon.png) 在任何页的右上角。 在左侧栏中，依次选择 " **企业概述**"、" **策略**" 和 " **操作**"，然后选择选项以 **启用所有组织的操作**。

接下来，从 " **自承载的流** " 选项卡配置运行程序。选择 " **新建** "，然后从下拉箭头中选择 " **新建运行** 程序"。

在下一页上，你会看到一组要运行的命令，我们只需复制该命令来 **配置** 运行程序，例如：

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

复制该 `config.sh` 命令，并将其粘贴到操作运行程序上的会话中， (之前创建) 。

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="选择在本地或云中运行 GitHub。":::

使用 run.sh 命令 *运行* 运行程序：

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="选择在本地或云中运行 GitHub。":::

若要使此运行程序可供企业中的组织使用，请编辑其组织访问权限：

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="选择在本地或云中运行 GitHub。":::

在这里，我们将其提供给所有组织，但你也可以将访问权限限制为组织的子集，甚至限制为特定的存储库。

## <a name="optional-configuring-github-connect"></a> (可选) 配置 GitHub Connect

尽管此步骤是可选的，但如果计划使用 GitHub.com 上提供的开源操作，建议使用此步骤。 这使你可以通过在工作流中引用这些可重用操作来基于其他人的工作进行构建。

若要启用 GitHub Connect，请遵循 [使用 GitHub Connect 启用对 GitHub.com 操作的自动访问](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)中的步骤。

启用 GitHub Connect 后，请在 " **工作流运行" 选项中选择要使用 GitHub.com 中的操作的服务器** 。

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="选择在本地或云中运行 GitHub。":::

## <a name="setting-up-and-running-your-first-workflow"></a>设置并运行你的第一个工作流

现在已设置了操作和 GitHub Connect，接下来让我们将所有这些工作投入使用。 下面是一个示例工作流，它引用了出色的 [octokit/请求-操作](https://github.com/octokit/request-action)，使我们能够通过使用 github API 的交互（由 github 操作提供支持）进行 "脚本" GitHub。

在此基本工作流中，我们将 `octokit/request-action` 只使用 API 在 GitHub 上打开问题。

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="选择在本地或云中运行 GitHub。":::

>[!NOTE]
>GitHub.com 承载操作，但当它在 GitHub Enterprise Server 上运行时，它 *会自动* 使用 Github ENTERPRISE server API。

如果选择不启用 GitHub Connect，则可以使用以下备用工作流。

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="选择在本地或云中运行 GitHub。":::

导航到实例上的存储库，并添加上述工作流作为： `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="选择在本地或云中运行 GitHub。" 选项卡中，等待工作流执行。

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="选择在本地或云中运行 GitHub。":::

还可以监视运行程序正在处理的程序。

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="选择在本地或云中运行 GitHub。" 的新问题。

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="选择在本地或云中运行 GitHub。":::

祝贺你！ 你刚完成了 GitHub 企业服务器上的第一个操作工作流，该工作流在 Azure VMware 解决方案私有云上运行。

我们只是外在优势了可通过 GitHub 操作来执行的操作。 有关更多的操作，请在 [GitHub 的 Marketplace](https://github.com/marketplace)上查看操作列表，或 [创建自己](https://docs.github.com/en/actions/creating-actions)的操作列表。

## <a name="next-steps"></a>后续步骤

在本文中，我们将在 Azure VMware 解决方案私有云的基础上设置 GitHub Enterprise Server 的一个新实例，该实例与 GitHub.com 的自承载等效。 此实例包括对 GitHub 操作的支持，并使用 Azure Blob 存储来保存日志和项目。 这是一个很好的组合，适用于最新的、协作和安全的软件开发体验。 它建立在坚实的 Azure VMware 解决方案基础之上，使你能够在熟悉的设置中利用云资源。

有关详细信息，请参阅以下资源：

- [GitHub 操作入门](https://docs.github.com/en/actions)
- [加入 beta 计划](https://resources.github.com/beta-signup/)
- [了解有关 GitHub Enterprise Server 管理的详细信息](https://githubtraining.github.io/admin-training/#/00_getting_started)
