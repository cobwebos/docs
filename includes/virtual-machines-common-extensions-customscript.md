

自定义脚本扩展自推出后已广泛应用于在 Windows 和 Linux VM 上配置工作负荷。 随着 Azure Resource Manager 模板的引入，用户现在可以创建单个模板，该模板不只可以预配 VM，还可以配置 VM 上的工作负荷。

## <a name="about-azure-resource-manager-templates"></a>关于 Azure Resource Manager 模板
Azure Resource Manager 模板允许用户通过定义资源之间的依赖关系，使用 JSON 语言以声明方式指定 Azure IaaS 基础结构。 有关 Azure Resource Manager 模板的详细概述，请参阅以下文章：

* [资源组概述](../articles/azure-resource-manager/resource-group-overview.md)
* [使用 Azure PowerShell 部署模板](../articles/virtual-machines/windows/ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>先决条件
1. 从[此处](https://azure.microsoft.com/downloads/)下载适合自己操作系统的 Azure 命令行工具。
2. 如果脚本将在现有 VM 上运行，请确保已在该 VM 上启用了 VM 代理，如果没有启用，请根据 [Linux](../articles/virtual-machines/linux/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 或 [Windows](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 指南安装一个。
3. 将你要在 VM 上运行的脚本上载到 Azure 存储。 脚本可以来自单个或多个存储容器。
4. 或者，也可以将脚本上载到 GitHub 帐户。
5. 脚本应当以下述方式编写：使用扩展启动入口脚本，然后入口脚本再调用其他脚本。

## <a name="using-the-custom-script-extension"></a>使用自定义脚本扩展
若要使用模板部署，我们需要使用 Azure 服务管理 API 中可用的同一版本的自定义脚本扩展。 该扩展支持用于将文件上载到 Azure 存储帐户或 GitHub 位置的相同参数和方案。 配合模板使用时，主要差异在于应该指定正确版本的扩展，而不是以 majorversion.* 格式指定版本。

