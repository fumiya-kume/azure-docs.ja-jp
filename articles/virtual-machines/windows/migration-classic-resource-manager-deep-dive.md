---
title: "プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細 | Microsoft Docs"
description: "この記事では、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について技術的に詳しく説明します。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: 4898998fe27c48bab4dee3dbaed5a174e23dc83a
ms.lasthandoff: 03/31/2017


---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細
Azure クラシック デプロイメント モデルから、Azure Resource Manager デプロイメント モデルへの移行を詳しく見ていきましょう。 Azure Platform 上の 2 つのデプロイメント モデルの間で、どのようにリソースが移行されるかを理解できるように、リソースと機能レベルでリソースについて説明していきます。 詳細については、サービス告知記事の「 [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーを確認する)
* [クラシックから Azure リソース マネージャーへの IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

