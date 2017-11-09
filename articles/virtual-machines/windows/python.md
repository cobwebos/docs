---
title: "在 Azure 中使用 Python 创建和管理 Windows VM | Microsoft Docs"
description: "了解如何使用 Python 在 Azure 中创建和管理 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: davidmu
ms.openlocfilehash: bb777d41570d7b1dc97402d532519488912948e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>在 Azure 中使用 Python 创建和管理 Windows VM

[Azure 虚拟机](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) 需要多个支持性 Azure 资源。 本文涵盖使用 Python 创建、管理和删除 VM 资源。 学习如何：

> [!div class="checklist"]
> * 创建 Visual Studio 项目
> * 安装包
> * 创建凭据
> * 创建资源
> * 执行管理任务
> * 删除资源
> * 运行应用程序

完成这些步骤大约需要 20 分钟。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 如果尚未安装，请安装 [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。 在“工作负荷”页上选择“Python 开发”，然后单击“安装”。 在摘要中，能够看见自动为你选择了“Python 3 64 位(3.6.0)”。 如果已安装 Visual Studio，则可以使用 Visual Studio 启动器添加 Python 工作负荷。
2. 安装并启动 Visual Studio 之后，单击“文件” > “新建” > “项目”。
3. 单击“模板” > “Python” > “Python 应用程序”，输入“myPythonProject”作为项目的名称，选择项目的位置，然后单击“确定”。

## <a name="install-packages"></a>安装包

1. 在“解决方案资源管理器”的“myPythonProject”下，右键单击“Python 环境”，然后选择“添加虚拟环境”。
2. 在“添加虚拟环境”屏幕上，接受“env”的默认名称，确保选择“Python 3.6 (64 位)”作为基解释器，然后单击“创建”。
3. 右键单击创建的环境“env”，单击“安装 Python 包”，在搜索框中输入“azure”，然后按 Enter。

会在输出窗口中看见 Azure 包已成功安装。 

## <a name="create-credentials"></a>创建凭据

在开始此步骤之前，请确保拥有 [Active Directory 服务主体](../../azure-resource-manager/resource-group-create-service-principal-portal.md)。 还应该记录稍后步骤需要的应用程序 ID、身份验证秘钥和的租户 ID。

1. 打开已创建的 myPythonProject.py 文件，然后添加以下代码运行应用程序：

    ```python
    if __name__ == "__main__":
    ```

2. 若要导入所需代码，请将这些语句添加到 .py 文件顶部：

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. 接下来在 .py 文件中，在导入语句后添加变量以指定代码常用值：
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    将“subscription-id”替换为你的订阅标识符。

4. 若要创建进行请求所需的 Active Directory 凭据，请在 .py 文件中将此函数添加到变量之后：

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    将“application-id”、“authentication-key”和“tenant-id”替换为先前创建 Azure Active Directory 服务主体时搜集的值。

5. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>创建资源
 
### <a name="initialize-management-clients"></a>初始化管理客户端

需要管理客户端在 Azure 中通过 Python SDK 创建和管理资源。 若要创建管理客户端，将此代码添加在 .py 文件末尾的 if 语句下：

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>创建 VM 和支持资源

必须在[资源组](../../azure-resource-manager/resource-group-overview.md)中包含所有资源。

1. 若要创建资源组，请在 .py 文件中将此函数添加到变量之后：

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[可用性集](tutorial-availability-sets.md)可以方便你维护应用程序所使用的虚拟机。

1. 若要创建可用性集，请在 .py 文件中将此函数添加到变量之后：
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

与虚拟机通信需要[公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

1. 若要创建虚拟机的公共 IP 地址，请在 .py 文件中将此函数添加到变量之后：

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

虚拟机必须是[虚拟网络](../../virtual-network/virtual-networks-overview.md)的子网。

1. 若要创建虚拟网络，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. 若要添加虚拟网络的子网，请在 .py 文件中将此函数添加在变量之后：
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

虚拟机需要使用网络接口在虚拟网络上通信。

1. 若要创建网络接口，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

创建所有支持的资源后，可以创建虚拟机。

1. 若要创建虚拟机，请在 .py 文件中将此函数添加在变量之后：
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > 本教程创建运行 Windows Server 操作系统版本的虚拟机。 若要详细了解如何选择其他映像，请参阅 [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 Windows PowerShell 和 Azure CLI 来导航和选择 Azure 虚拟机映像）。
    > 
    > 

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>执行管理任务

在虚拟机生命周期中，可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，建议创建代码来自动执行重复或复杂的任务。

### <a name="get-information-about-the-vm"></a>获取有关 VM 的信息

1. 若要获取有关虚拟机的信息，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>停止 VM

可以停止虚拟机并保留其所有设置但继续支付其费用，也可以停止虚拟机并将其解除分配。 解除分配某个虚拟机也会解除分配与其关联的所有资源，并停止该虚拟机的计费。

1. 若要停止虚拟机但不解除分配，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    如果要解除虚拟机分配，请将 power_off 调用更改为以下代码：

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>启动 VM

1. 若要启动虚拟机，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>重设 VM 大小

决定虚拟机大小时应考虑部署的诸多方面。 有关详细信息，请参见 [VM 大小](sizes.md)。

1. 若要更改虚拟机大小，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>将数据磁盘添加到 VM

虚拟机可以有一个或多个存储为 VHD 的[数据磁盘](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

1. 若要将数据磁盘添加到虚拟机，请在 .py 文件中将此函数添加在变量之后： 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>删除资源

由于你需要为 Azure 中使用的资源付费，因此，删除不再需要的资源总是一种好做法。 如果要删除虚拟机和所有支持资源，只需删除资源组。

1. 若要删除资源组和所有资源，请在 .py 文件中将此函数添加到变量之后：
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. 若要调用先前添加的函数，请将此代码添加在 .py 文件末尾的 if 语句下：
   
    ```python
    delete_resources(resource_group_client)
    ```

3. 保存 myPythonProject.py。

## <a name="run-the-application"></a>运行应用程序

1. 若要运行控制台应用程序，在 Visual Studio 中单击“开始”。

2. 所有资源的状态返回后按 Enter。 在状态信息中会看到“成功”预配状态。 创建虚拟机后，就有机会删除创建的所有资源。 按 Enter 开始删除资源之前，可能需要花几分钟在 Azure 门户中验证创建。 如果已打开 Azure 门户，可能需要刷新边栏选项卡以查看新的资源。  

    控制台应用程序从头到尾完成运行大约需要五分钟时间。 在应用程序完成后可能需要几分钟才能看到所有资源和资源组被删除。


## <a name="next-steps"></a>后续步骤

- 如果部署出现问题，请查看[使用 Azure 门户对资源组部署进行故障排除](../../resource-manager-troubleshoot-deployments-portal.md)
- 了解有关 [Azure Python 库](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)的更多信息

