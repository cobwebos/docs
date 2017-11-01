与对服务器终结点所做的更改不同，使用 Azure 门户、SMB 或 REST 对 Azure 文件共享所做的更改不会立即检测到并复制。 更改不会立即复制是因为 Azure 文件尚没有更改通知/日记，因此无法在文件更改时自动启动同步会话。 在 Windows Server 上，Azure 文件同步使用 [Windows USN 日记](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)可在文件更改时自动启动同步会话。

为了检测对 Azure 文件共享所做的更改，Azure 文件同步有一个称为“更改检测作业”的计划作业，该作业会枚举文件共享中的每个文件，并将它与该文件的同步版本进行比较。 当更改检测作业确定文件已更改时，Azure 文件同步会启动同步会话。 更改检测作业每 24 小时启动一次。 由于更改检测作业的工作原理是枚举 Azure 文件共享中的每个文件，因此大命名空间用时会长于较小的命名空间。 这意味着，对于这些命名空间，用时可能超过每 24 小时 1 次，才能确定哪些文件已更改。 

从长远来说，我们会在 Azure 文件中添加与 Windows Server 上的 USN 类似的更改检测功能。 请在 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上为此功能投票，帮助我们确定将来开发此功能的优先级。