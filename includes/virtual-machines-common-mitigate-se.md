


**上次文档更新时间**：太平洋标准时间 3 月 6 日上午 10:00。

最近发现了一种称为推理执行旁道攻击的[新型 CPU 漏洞](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)，这使想要了解其详情的客户向我们提出了问题。  

运行 Azure 并将客户工作负荷相互隔离的基础结构是受保护的。  这意味着 Azure 上运行的其他客户无法使用这些漏洞攻击你的应用程序。

> [!NOTE] 
> 2018 年 2 月 下旬，Intel Corporation 发布了关于微代码发布状态的更新版 [Microcode Revision Guidance](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf)（微代码修订指南），这不仅提升了稳定性还缓解了 [Google Project Zero](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html) 披露的最新漏洞。 Azure 于 [2018 年 1 月 3 日](https://azure.microsoft.com/en-us/blog/securing-azure-customers-from-cpu-vulnerability/)实施的缓解措施不会受到 Intel 微代码更新的影响。 Microsoft 已经采取强有力的措施，保护 Azure 客户免受其他 Azure 租户的影响。  
>
> Intel 的微代码地址变体 2 Spectre ([CVE-2017-5715](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715)) 仅可避免在 Azure 的 VM 上运行共享或不受信任的工作负荷所造成的攻击。 在面向 Azure 客户推出之前，我们的工程师正在测试稳定性，最大程度地降低微代码对性能的影响。  由于只有极少数客户会在 VM 上运行不受信任的工作负荷，因此发布后大多数客户都不需要启用此功能。 
>
> 有更多信息可用时，我们会更新此页面。  






## <a name="keeping-your-operating-systems-up-to-date"></a>使操作系统保持最新

虽然无需操作系统更新也可将你在 Azure 上运行的应用程序与 Azure 上运行的其他客户隔离，但使操作系统版本保持最新始终是最佳做法。 

对于以下产品/服务，我们建议执行下面的操作来更新操作系统： 

<table>
<tr>
<th>产品/服务</th> <th>建议的操作 </th>
</tr>
<tr>
<td>Azure 云服务 </td>  <td>启用自动更新，或确保正在运行的是最新的来宾操作系统。</td>
</tr>
<tr>
<td>Azure Linux 虚拟机</td> <td>如果有来自操作系统系统提供方的更新可用，请进行安装。 </td>
</tr>
<tr>
<td>Azure Windows 虚拟机 </td> <td>在安装操作系统更新之前，请验证是否正在运行支持的防病毒应用程序。 有关兼容性信息，请与防病毒软件供应商联系。<p> 安装 [1 月安全性汇总](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)。 </p></td>
</tr>
<tr>
<td>其他 Azure PaaS 服务</td> <td>使用这些服务的客户不需要执行任何操作。 Azure 会自动使你的操作系统版本保持最新。 </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>附加指南（如果正在运行不受信任的代码） 

除非正在运行不受信任的代码，否则不需要客户执行更多操作。 如果允许不信任的代码（例如，允许某一客户上传你随后要在云中的应用程序内执行的二进制文件或代码片段），则应执行以下附加步骤。  


### <a name="windows"></a>Windows 
如果使用 Windows 且托管不受信任的代码，还应启用一种称为内核虚拟地址 (KVA) 映射的 Windows 功能，该功能提供额外的保护来防御推理执行旁道漏洞。 此功能在默认情况下处于关闭状态，如果启用，可能会影响性能。 请按照 [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中“在服务器上启用保护”的说明执行操作。 如果正在运行 Azure 云服务，请验证是否正在运行 WA-GUEST-OS-5.15_201801-01 或 WA-GUEST-OS-4.50_201801-01（从 2018 年 1 月 10 日起提供），并通过启动任务启用注册表项。


### <a name="linux"></a>Linux
如果使用 Linux 且托管不受信任的代码，还应将 Linux 更新到一个实现内核页表隔离 (KPTI) 的较新版本，内核页表隔离会将内核使用的页表与属于用户空间的页表分隔开来。 这些防御措施需要 Linux 操作系统更新，可以在推出后从分发版提供方获取。 操作系统提供方可告诉你保护在默认情况下是处于启用状态还是禁用状态。



## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)（确保 Azure 客户免受 CPU 漏洞影响）。
