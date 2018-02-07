

## <a name="what-is-happening"></a>发生了什么？

[1 月 3 日发现](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)了一个基于硬件的行业范围的安全漏洞。 客户安全始终是我们最关切的问题，我们正在采取积极措施，保障 Azure 客户不会受到这些漏洞影响。

随着安全漏洞的公开披露，我们[加快了计划维护的时间](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)，并开始自动重启仍需更新的 VM。


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>如何知道哪些 VM 已更新？ 

可以在 [Azure 门户的 VM 列表](https://aka.ms/T08tdc)中查看 VM 的状态以及重启是否已完成。 如果已应用更新，则会将 VM 列为“已更新”；如果仍需进行更新，则会将 VM 列为“已计划”。 如果只想查看将 VM 列为“已计划”的情况，请参阅 [Azure 服务运行状况](https://portal.azure.com/)。

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>如何知道 VM 重启的确切时间？

若要获取有关重启的警报，最好的方法是配置[计划事件](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events)。 这样就会提前 15 分钟发送 VM 因维护而停机的通知。

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>当前可否以手动方式重新部署，以执行所需的维护？ 

我们无法保证将重新部署的 VM 分配至更新的主机。 Azure 结构将尽可能把 VM 分配至已更新的主机。 重新部署的 VM 有可能分配至未更新主机，在此情况下，可能在计划性维护期间强制执行第二次重启。 因此，不推荐将手动重新部署作为解决办法。

## <a name="how-long-will-the-reboot-take"></a>重启需要多长时间？ 

大多数重启耗时约 30 分钟。

## <a name="does-the-guest-os-need-to-be-updated"></a>来宾 OS 是否需要更新？ 

此 Azure 基础结构更新解决了虚拟机监控程序级别公开的漏洞，且无需更新 Windows 或 Linux VM 映像。 但是，请按惯例继续为 VM 映像应用安全最佳实践。 有关更新和说明，请按需咨询操作系统的供应商。 Windows Server VM 客户的指南现已发布，在[此处](../articles/virtual-machines/windows/mitigate-se.md)提供。

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>解决此更新是否会造成性能影响？

大多数 Azure 客户不会看到此更新造成显著的性能影响。 我们一直致力于优化 CPU 和磁盘 I/O 路径，并未看到在应用修补程序后出现显著的性能影响。 少部分客户可能会体验到一些网络性能影响。 可以使用 Azure 加速网络（适用于 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 或 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)）来解决此问题，该功能是提供给所有 Azure 客户的免费功能。

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>若遵循高可用性建议，环境可否在重启过程中保持高可用性？

可以，可用性集或虚拟机规模集中部署的虚拟机具有更新域 (UD) 构造。 执行维护时，Azure 遵循 UD 约束，不会从不同 UD（在同一可用性集中）重新启动虚拟机。 有关高可用性的详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或[在 Azure 中管理 Linux 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>我使用了区域对来构建业务连续性/灾难恢复计划。 是否会同时在区域对中重启我的 VM？

通常情况下，在向配对的区域推出 Azure 计划内维护事件时，会一次推出一个事件，目的是尽量降低两个区域都出现中断的风险。 不过，由于这次的安全更新情况紧急，我们会向所有区域同时推出更新。

## <a name="what-about-paas-services-on-azure"></a>那 Azure 上的 PaaS 服务呢？  

包括 Web 和移动、数据服务、IoT 和无服务器等在内的 Azure 平台服务已解决该漏洞。 使用这些服务的客户不需要执行任何操作。

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Intel 已于 2018 年 1 月 22 日发布有关安全漏洞的额外指南。  此指南是否要求 Azure 进行其他维护活动？  

2018 年 1 月 3 日发布的 Azure 缓解不受 Intel [更新指南](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/)影响。 此指南不要求对客户 VM 进行额外维护活动。
 

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)（确保 Azure 客户免受 CPU 漏洞影响）。
