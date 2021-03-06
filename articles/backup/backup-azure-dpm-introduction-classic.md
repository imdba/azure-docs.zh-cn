---
title: "将 DPM 工作负荷备份到 Azure 经典门户 | Microsoft 文档"
description: "使用 Azure 备份服务备份 DPM 服务器的简介"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: "System Center Data Protection Manager, Data Protection Manager, dpm 备份"
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: nkolli;giridham;markgal
ms.openlocfilehash: 7f13739c2d3f7fba700b649f27e02913481ca5e5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>使用 DPM 准备将工作负荷备份到 Azure
> [!div class="op_single_selector"]
> * [Azure 备份服务器](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure 备份服务器（经典）](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM（经典）](backup-azure-dpm-introduction-classic.md)
>
>

本文介绍如何使用 Microsoft Azure 备份来保护 System Center Data Protection Manager (DPM) 服务器和工作负载。 通过阅读本文，会了解：

* Azure DPM 服务器备份的工作原理
* 实现顺畅备份体验的先决条件
* 遇到的典型错误以及如何处理它们
* 支持的方案

System Center DPM 备份文件和应用程序数据。 备份到 DPM 的数据可以存储在磁带、磁盘上，或者使用 Microsoft Azure Backup 备份到 Azure。 DPM 可与 Azure 备份交互，如下所述：

* **部署为物理服务器或本地虚拟机的 DPM** — 如果 DPM 部署为物理服务器或本地 Hyper-V 虚拟机，则你除了磁盘和磁带备份外，还可以将数据备份到 Azure 备份保管库。
* **部署为 Azure 虚拟机的 DPM** — 通过 System Center 2012 R2 Update 3，可以将 DPM 部署为 Azure 虚拟机。 如果 DPM 部署为 Azure 虚拟机部署，则可以将数据备份到附加到 DPM Azure 虚拟机的 Azure 磁盘，也可以通过将数据备份到 Azure 备份保管库来卸载数据存储。

## <a name="why-backup-your-dpm-servers"></a>为何要备份 DPM 服务器？
使用 Azure 备份来备份 DPM 服务器所带来的业务好处包括：

* 对于本地 DPM 部署，可以使用 Azure 备份来取代长期的磁带部署。
* 对于 Azure 中的 DPM 部署，Azure 备份可卸载 Azure 磁盘中的存储，从而通过将较旧的数据存储在 Azure 备份上，并将较新的数据存储在磁盘上来实现纵向扩展。

## <a name="how-does-dpm-server-backup-work"></a>DPM 服务器备份的工作原理
若要备份虚拟机，首先需要获得数据的时间点快照。 Azure 备份服务在计划的时间启动备份作业，并触发要拍摄快照的备份扩展。 备份扩展会与来宾内部的 VSS 服务进行协调以确保一致性，并会在达成一致后调用 Azure 存储服务的 Blob 快照 API。 这样做可以获得虚拟机磁盘的一致性快照，不必关闭虚拟机。

拍摄快照后，数据由 Azure 备份服务传输到备份保管库中。 该服务负责确定并传输自上次备份以来进行了更改的块，使备份存储和网络更高效。 数据传输完成后，会删除快照并创建恢复点。 在 Azure 经典门户中，可以查看此恢复点。

> [!NOTE]
> Linux 虚拟机只能使用文件一致性备份。
>
>

## <a name="prerequisites"></a>先决条件
按如下所述让 Azure 备份做好备份 DPM 数据的准备：

1. **创建备份保管库**。 如果尚未在你的订阅中创建备份保管库，请参阅[为使用 DPM 将工作负荷备份到 Azure 做准备](backup-azure-dpm-introduction.md)一文的 Azure 门户版本。

  > [!IMPORTANT]
  > 从 2017 年 3 月份开始，无法再使用经典门户来创建备份保管库。
  > 现在可将备份保管库升级到恢复服务保管库。 有关详细信息，请参阅文章[将备份保管库升级到恢复服务保管库](backup-azure-upgrade-backup-to-recovery-services.md)。 Microsoft 鼓励将备份保管库升级到恢复服务保管库。<br/> 2017 年 11 月 30 日之后，将无法使用 PowerShell 创建备份保管库。 在 2017 年 11 月 30 日之前：
  >- 其余所有备份保管库都将自动升级到恢复服务保管库。
  >- 将无法在经典门户中访问备份数据。 而是使用 Azure 门户在恢复服务保管库中访问备份数据。
  >

2. **下载保管库凭据** — 在 Azure 备份中，将你创建的管理证书上传到保管库。
3. **安装 Azure 备份代理并注册服务器** — 通过 Azure 备份，在每个 DPM 服务器上安装代理，并在备份保管库中注册 DPM 服务器。

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>要求和限制
* DPM 可作为物理服务器或安装在 System Center 2012 SP1 或 System Center 2012 R2 上的 Hyper-V 虚拟机运行。 它也可以作为 System Center 2012 R2（至少包含 DPM 2012 R2 Update Rollup 3）上运行的 Azure 虚拟机，或 System Center 2012 R2（至少包含 Update Rollup 5）上运行的 VMWare 中的 Windows 虚拟机运行。
* 如果要随 System Center 2012 SP1 一起运行 DPM，则应安装 System Center Data Protection Manager SP1 的 Update Rollup 2。 只有满足此要求，才能安装 Azure 备份代理。
* DPM 服务器上应已安装 Windows PowerShell 和 .NET Framework 4.5。
* DPM 可将大多数工作负载备份到 Azure 备份。 有关所支持内容的完整列表，请参阅下面的 Azure 备份支持项。
* 使用“复制到磁带”选项无法恢复存储在 Azure 备份中的数据。
* 需要一个启用了 Azure 备份功能的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 阅读[Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)的相关信息。
* 若要使用 Azure 备份，应在要备份的服务器上安装 Azure 备份代理。 每台服务器上的可用本地存储必须至少为要备份的数据大小的 10%。 例如，如果要备份 100 GB 的数据，则暂存位置至少需要 10 GB 的可用空间。 尽管最低要求为 10%，但我们建议为缓存位置腾出 15% 的本地可用存储空间。
* 数据将存储在 Azure 保管库存储中。 可以备份到 Azure 备份保管库的数据量没有限制，但数据源（例如，虚拟机或数据库）的大小不应超过 54,400 GB。

支持将以下文件类型备份到 Azure：

* 加密（仅限完整备份）
* 压缩（支持增量备份）
* 稀疏（支持增量备份）
* 压缩和稀疏（视为稀疏）

以下内容不受支持：

* 不支持区分大小写的文件系统上的服务器。
* 硬链接（跳过）
* 重分析点（跳过）
* 加密和压缩（跳过）
* 加密和稀疏（跳过）
* 压缩流
* 稀疏流

> [!NOTE]
> 从 System Center 2012 DPM SP1 开始，可以使用 Microsoft Azure 备份将 DPM 保护的工作负载备份到 Azure。
>
>
