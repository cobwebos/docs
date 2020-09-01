---
title: 在 Microsoft Azure Stack Edge 设备上创建和管理 Kubernetes 群集 |Microsoft Docs
description: 介绍如何通过 Windows PowerShell 界面在 Microsoft Azure Stack Edge 设备上创建和管理 Kubernetes 群集。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 74f86ed48f363031fcab5d9d89046c349a1c3667
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181386"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-device"></a>通过 Azure Stack 边缘设备上的 kubectl 连接到 Kubernetes 群集并对其进行管理

在 Azure Stack Edge 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，可以通过本机工具（如 *kubectl*），从客户端计算机本地连接和管理群集。

本文介绍如何连接到 Azure Stack 边缘设备上的 Kubernetes 群集，然后使用 *kubectl*对其进行管理。 


## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你可以访问 Azure Stack Edge 设备。

2. 已激活 Azure Stack Edge 设备，如 [激活 Azure Stack 边缘](azure-stack-edge-gpu-deploy-activate.md)中所述。

3. 已在设备上启用了计算角色。 根据在 [Azure Stack Edge 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)中的说明，在设备上配置计算时，还会在设备上创建 Kubernetes 群集。

4. 你已访问运行 PowerShell 5.0 或更高版本的 Windows 客户端系统来访问设备。 您也可以将任何其他客户端与 [支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 结合使用。 

5. 你具有本地 web UI 的 " **设备** " 页中的 Kubernetes API 终结点。 有关详细信息，请参阅[Get KUBERNETES API 终结点](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)中的说明


## <a name="connect-to-powershell-interface"></a>连接到 PowerShell 接口

创建 Kubernetes 群集后，可以访问此群集来创建命名空间和用户，并将用户分配到命名空间。 这将要求你连接到设备的 PowerShell 接口。 在运行 PowerShell 的 Windows 客户端上执行以下步骤。

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-rbac"></a>通过 RBAC 配置群集访问

创建 Kubernetes 群集后，可以使用 *kubectl* via cmdline 访问该群集。 

在此方法中，您将创建命名空间和用户。 然后，将用户与命名空间相关联。 还需要获取 *配置* 文件，该文件允许你使用 Kubernetes 客户端直接与你创建的 Kubernetes 群集通信，而无需连接到 Azure Stack Edge 设备的 PowerShell 接口。

1. 创建命名空间。 键入：

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > 对于命名空间和用户名，都适用 [DNS 子域命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) 。

    下面是示例输出：

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. 创建一个用户并获取一个配置文件。 键入：

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > 不能使用 *aseuser* 作为用户名，因为它是为与 IoT 命名空间相关联 Azure Stack Edge 的默认用户保留的。

    下面是配置文件的示例输出：
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. 配置文件以纯文本格式显示。 复制此文件，并将其另存为 *配置* 文件。 

    > [!IMPORTANT]
    > 不要将配置文件另存为 *.txt* 文件，而是在没有任何文件扩展名的情况下保存该文件。

4. 配置文件应位于 `.kube` 本地计算机上的用户配置文件的文件夹中。 将该文件复制到用户配置文件中的该文件夹。

    ![配置文件在客户端上的位置](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. 将命名空间与您创建的用户关联。 键入：

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    下面是示例输出：

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    有了配置文件后，就不需要对群集进行物理访问。 如果客户端可以 ping Azure Stack Edge 设备 IP，则应该可以使用 *kubectl* 命令定向群集。

6. 在客户端上启动新的 PowerShell 会话。 不需要连接到设备接口。 你现在可以 `kubectl` 使用以下命令在客户端上安装：

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    例如，如果 Kubernetes 主节点正在运行 v 1.15.2，请在客户端上安装 v 1.15.2。

    > [!IMPORTANT]
    > 下载客户端，该客户端不会从主节点中倾斜多个次要版本。 客户端版本，但最多只能有一个次要版本。 例如，v2.0 master 应该适用于1.1、1.2 和1.3 版节点，并且应该适用于 v2.0、v1.0 和 v2.0 客户端。 有关 Kubernetes 客户端版本的详细信息，请参阅 [Kubernetes 版本和版本歪斜支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)。 有关 Azure Stack Edge 上的 Kubernetes 服务器版本的详细信息，请转到获取 Kubernetes 服务器版本。<!-- insert link-->
    > 有时， `kubectl` 如果运行用于 Windows 的 Docker 或其他工具，则会在系统上预安装。 必须下载 `kubectl` 此部分中所述的特定版本，才能使用此 kubernetes 群集。 

    安装过程将花费几分钟时间。

7. 验证所安装的版本是否已下载。 应指定系统上安装位置的绝对路径 `kubectl.exe` 。
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    有关 `kubectl` 用于管理 Kubernetes 群集的命令的详细信息，请参阅 [kubectl 概述](https://kubernetes.io/docs/reference/kubectl/overview/)。

8. 将 DNS 条目添加到系统上的 hosts 文件中。 

    1. 以管理员身份运行记事本，然后打开 `hosts` 位于处的文件 `C:\windows\system32\drivers\etc\hosts` 。 
    2. 使用之前步骤中的 " **设备** " 页上保存的信息在 hosts 文件中创建条目。 

        例如，复制此终结点 `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` 以创建具有设备 IP 地址和 DNS 域的以下条目： 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. 若要验证是否可以连接到 Kubernetes，请键入：
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
你现在可以在命名空间中部署应用程序，然后查看这些应用程序及其日志。

> [!IMPORTANT]   
> 您无法运行的命令有很多，例如需要管理员访问权限的命令。 只能在命名空间上执行所允许的操作。


## <a name="remove-kubernetes-cluster"></a>删除 Kubernetes 群集

若要删除 Kubernetes 群集，你将需要删除计算配置。

有关详细说明，请参阅 [删除计算配置](azure-stack-edge-j-series-manage-compute.md#remove-compute-configuration)。
   

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 边缘部署无状态应用程序](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
