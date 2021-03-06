---
title: "Azure への物理サーバーのレプリケート | Microsoft Docs"
description: "Azure Portal を使用して Azure Site Recovery をデプロイし、オンプレミスの Windows/Linux 物理サーバーの Azure へのレプリケーション、フェールオーバー、復旧を調整する方法を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: physical-walkthrough-overview
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: a9655ce1540c788d02d178eb619d2051cddda1c2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017

---
---
# <a name="replicate-physical-machines-to-azure-by-using-site-recovery"></a>Site Recovery を使用して物理マシンを Azure にレプリケートする


この記事では、Azure Portal の Azure Site Recovery サービスを使用して、オンプレミスの物理マシンを Azure にレプリケートする方法について説明します。

物理マシンを Azure に移行する場合 (フェールオーバーのみ)、詳細については「[Site Recovery を使用した Azure への移行](site-recovery-migrate-to-azure.md)」を参照してください。

コメントや質問は、この記事の末尾または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="prerequisites"></a>前提条件

**サポート要件** | **詳細**
--- | ---
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を確認します。
**オンプレミスの構成サーバー** | Windows Server 2012 R2 以降が動作しているオンプレミスのマシン (物理または VMware VM)。 構成サーバーは、Site Recovery のデプロイの際に設定します。<br/><br/> 既定では、プロセス サーバーとマスター対象サーバーもこのマシンにインストールされます。 スケールアップする場合は、別のプロセス サーバーが必要になる可能性があります。この場合も、要件は構成サーバーと同じです。<br/><br/> これらのコンポーネントの詳細については、[ソース環境のセットアップ](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)に関する記事を参照してください。
**オンプレミスの VM** | レプリケートするマシンは、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)が実行され、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠している必要があります。
**URL** | 構成サーバーは以下の URL にアクセスできる必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (アクセスの制御と ID 管理に使用されます)。<br/><br/> MySQL をダウンロードするために、http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi を許可します。
**モビリティ サービス** | このサービスは、レプリケートする各マシンにインストールされます。

## <a name="limitations"></a>制限事項

**制限事項** | **詳細**
--- | ---
**Azure** | ストレージ アカウントとネットワーク アカウントは、コンテナーと同じリージョンに存在する必要があります。<br/><br/> Premium ストレージ アカウントを使用する場合は、レプリケーション ログを格納するために Standard ストア アカウントも必要になります。<br/><br/> インド中部およびインド南部では Premium アカウントにレプリケートすることはできません。
**オンプレミスの構成サーバー** | VMware VM に構成サーバーをインストールする場合、VM のアダプターの種類は VMXNET3 である必要があります。 そうでない場合は、[この更新プログラムをインストールしてください](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/> VMware VM を使用している場合は、vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br> このマシンをドメイン コントローラーにすることはできません。<br/><br/> このマシンには、静的 IP アドレスが必要です。<br/><br/> ホスト名は 15 文字以下で指定し、オペレーティング システムは英語にする必要があります。
**レプリケートされたマシン** | [Azure VM の制限事項](site-recovery-prereq.md#azure-requirements)を確認してください。<br/><br/> 同じワークロードを実行するマシンを整合性データ ポイントに同時に復旧できるようにマルチ VM の整合性を有効にする場合は、マシンのポート 20004 を開いてください。<br/><br/> 特定の種類の [Linux ストレージ](site-recovery-support-matrix-to-azure.md#support-for-storage)がサポートされています。
**フェールバック** | Azure から物理マシンにはフェールバックできません。 フェールオーバー後にオンプレミスにフェールバックできるようにするには、VMware VM にフェールバックできる VMware 環境が必要です。


## <a name="set-up-azure"></a>Azure をセットアップする

1. [Azure ネットワークをセットアップ](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)します。

      a. Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。

      b. Azure [Resource Manager](../resource-manager-deployment-model.md) モードまたはクラシック モードでネットワークをセットアップすることができます。

2. レプリケートされるデータ用に [Azure ストレージ アカウント](../storage/storage-create-storage-account.md#create-a-storage-account)をセットアップします。

    a. このアカウントには、Standard または [Premium](../storage/storage-premium-storage.md) を使用できます。

    b. Resource Manager モードまたはクラシック モードでアカウントをセットアップすることができます。

## <a name="prepare-the-configuration-server"></a>構成サーバーを準備

1. オンプレミスの物理サーバーまたは VMware VM に、Windows Server 2012 R2 以降をインストールします。

2. 「[前提条件](#prerequisites)」に記載されている URL にマシンからアクセスできることを確認します。

## <a name="prepare-for-mobility-service-installation"></a>モビリティ サービスのインストールを準備する

モビリティ サービスを物理マシンにプッシュする場合は、マシンにアクセスするためにプロセス サーバーで使用できるアカウントが必要になります。 このアカウントは、プッシュ インストールにのみ使用されます。 ドメイン アカウントまたはローカル アカウントを使用することができます。

  - Windows の場合、ドメイン アカウントを使用していなければ、ローカル マシンでリモート ユーザー アクセス コントロールを無効にする必要があります。 これを行うには、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** にあるレジストリに、値 1 を指定した DWORD エントリ **LocalAccountTokenFilterPolicy** を追加します。 コマンド ライン インターフェイスから Windows 用のレジストリ エントリを追加する場合は、次のように入力します。

        ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Linux の場合、アカウントは、ソース Linux サーバーの root ユーザーである必要があります。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-the-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** > **[コンテナー]** の順にクリックします。
2. **[リソース]** メニューで、**[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)

3. **[保護の目標]** で、**[To Azure]\(Azure へ\)** > **[非仮想化/その他]** の順に選択します。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]** > **[インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は、**[+ 構成サーバー**] をクリックします。

    ![Set up source](./media/site-recovery-vmware-to-azure/set-source1.png)

3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. **Site Recovery 統合セットアップ** インストール ファイルをダウンロードします。
5. **コンテナー登録キー**をダウンロードします。 統合セットアップを実行するときにこのキーが必要です。 キーは生成後 5 日間有効です。

   ![Set up source](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Site Recovery 統合セットアップを実行する

開始する前に、以下を行います。

- 簡単なビデオ概要を見ます  (ビデオでは VMware VM をレプリケートする方法について説明していますが、物理マシンのレプリケーションでもプロセスは同様です)。

    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

- 構成サーバー マシンのシステム クロックが[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期していることを確認します。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。
- 構成サーバー マシンのローカル管理者としてセットアップを実行します。
- マシンで TLS 1.0 が有効になっていることを確認します。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーは[コマンド ラインから](http://aka.ms/installconfigsrv)インストールすることもできます。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット環境をセットアップする前に、[Azure ストレージ アカウントとネットワーク](#set-up-azure)が確実に存在することを確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲット デプロイ モデルを Resource Manager とクラシックのどちらにするかを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あることが確認されます。

   ![ターゲット](./media/site-recovery-vmware-to-azure/gs-target.png)

4. ストレージ アカウントまたはネットワークを作成していない場合は、**[+ ストレージ アカウント]** または **[+ ネットワーク]** をクリックして、Resource Manager アカウントまたはネットワークをインラインで作成します。

## <a name="set-up-replication-settings"></a>レプリケーション設定をセットアップする

始める前に、簡単なビデオ概要を見ます  (ビデオでは VMware VM をレプリケートする方法について説明していますが、物理マシンのレプリケーションでもプロセスは同様です)。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. 新しいレプリケーション ポリシーを作成するには、**[Site Recovery インフラストラクチャ]** > **[レプリケーション ポリシー]** > **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、RPO の制限を指定します。 この値で、データの復旧ポイントを作成する頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium ストレージにレプリケートされたマシンでは、最大 24 時間のリテンション期間がサポートされます。 Standard ストレージにレプリケートされたマシンでは、最大 72 時間のリテンション期間がサポートされます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。 **[OK]** をクリックしてポリシーを作成します。

    ![Replication policy](./media/site-recovery-vmware-to-azure/gs-replication2.png)

6. 新しいポリシーを作成すると、自動的に構成サーバーに関連付けられます。 既定でフェールバックの照合ポリシーが自動的に作成されます。 たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシーは **rep-policy-failback** になります。 このポリシーは、Azure からフェールバックを開始するまで使用されません。  


## <a name="plan-capacity"></a>容量を計画する

1. 基本的なインフラストラクチャをセットアップできたので、容量計画を立案し、追加のリソースが必要かどうかを検討できます。 [詳細情報](site-recovery-plan-capacity-vmware.md)。
2. 容量計画の作業が完了したら、**[容量計画は完了していますか?]** で **[はい]** を選択します。

   ![容量計画](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>レプリケーション用の VM を準備する

レプリケートするすべてのマシンにモビリティ サービスをインストールする必要があります。 モビリティ サービスはさまざまな方法でインストールできます。

- プロセス サーバーからのプッシュ インストールを使用してサービスをインストールします。 このメソッドを使用するには、事前にマシンを準備しておく必要があります。
- System Center Configuration Manager や Azure Automation Desired State Configuration などのデプロイ ツールを使用してサービスをインストールします。
- サービスを手動でインストールします。

[詳細情報](site-recovery-vmware-to-azure-install-mob-svc.md)。


## <a name="enable-replication"></a>Enable replication

開始する前に次の操作を実行してください。

- Azure ユーザー アカウントには、新しい仮想マシンを Azure にレプリケートできるようにするための特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。
- VM を追加または変更するときに、変更が有効になってポータルに表示されるまでに 15 分以上かかることがあります。
- VM の最終検出時刻は、**[構成サーバー]** > **[前回のアクセス]** で確認できます。
- 定期検出を待たずに VM を追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** をクリックします。
- VM をプッシュ インストール用に準備した場合は、レプリケーションを有効にすると、プロセス サーバーでモビリティ サービスが自動的にインストールされます。
- 簡単なビデオ概要を見ます  (ビデオでは VMware VM をレプリケートする方法について説明していますが、物理マシンのレプリケーションでもプロセスは同様です)。

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]


### <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。

### <a name="replicate-vms"></a>VM をレプリケートする

1. **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。
2. **[ソース]** で **[オンプレミス]** を選択します。
3. **[ソースの場所]** で、構成サーバー名を選択します。
4. **[マシンの種類]** で、**[物理マシン]** を選択します。
5. **[プロセス サーバー]** でプロセス サーバーを選択します。 追加のプロセス サーバーを作成していない場合、このサーバーは構成サーバーになります。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-physical-to-azure/chooseVM.png)

6. **[ターゲット]** で、フェールオーバー後に Azure VM を作成する**サブスクリプション**と**リソース グループ**を選択します。 フェールオーバー対象の VM に Azure で使用するデプロイ モデル (クラシックまたは Resource Manager) を選択します。

7. データのレプリケーションに使用する Azure Storage アカウントを選択します。 既にセットアップしたアカウントを使用しない場合は、新しいアカウントを作成できます。

8. フェールオーバー後に Azure VM が接続する **Azure ネットワーク**と**サブネット**を選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 既存のネットワークを使用しない場合は、ネットワークを作成することができます。

    ![Enable replication](./media/site-recovery-physical-to-azure/targetsettings.png)

9. **[物理マシン]** で **[+ 物理マシン]** をクリックし、**名前**と **IP アドレス**を入力します。 レプリケートするマシンのオペレーティング システムを選択します。 マシンが検出されて一覧に表示されるまでには数分かかります。

    ![Enable replication](./media/site-recovery-physical-to-azure/machineselect.png)

10. **[プロパティ]** > **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。
11. 既定では、すべてのディスクがレプリケートされます。 **[すべてのディスク]** をクリックし、レプリケートしないディスクをオフにします。 次に、 **[OK]**をクリックします 後で追加の VM プロパティを設定できます。

    ![Enable replication](./media/site-recovery-physical-to-azure/configprop.png)

12. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 ポリシーを変更する場合、変更はレプリケートしているマシンと新しいマシンに適用されます。
13. マシンをレプリケーション グループにまとめる場合は、**[マルチ VM 整合性]** を有効にし、グループの名前を指定します。 次に、 **[OK]**をクリックします 以下の点に注意してください。

    a. レプリケーション グループのマシンはまとめてレプリケートされ、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを共有します。

    b. VM と物理サーバーがワークロードをミラー化できるように、これらをまとめることをお勧めします。 マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響する場合があります。 これは、複数のマシンが同じワークロードを実行していて、整合性を持たせる必要がある場合にのみ使用してください。

    ![Enable replication](./media/site-recovery-physical-to-azure/policy.png)

14. **[レプリケーションを有効にする]**をクリックします。 **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。

レプリケーションを有効にした後、プッシュ インストールをセットアップした場合はモビリティ サービスがインストールされます。 モビリティ サービスがマシンでプッシュ インストールされると、保護ジョブが開始されて失敗します。 失敗後、各マシンを手動で再起動する必要があります。 その後、保護ジョブが再び開始され、最初のレプリケーションが実行されます。


### <a name="view-and-manage-azure-vm-properties"></a>Azure VM プロパティを表示して管理する

VM プロパティを確認し、必要な変更を加えることをお勧めします。

1. **[レプリケートされたアイテム]** をクリックし、マシンを選択します。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。
2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。
3. **[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。 必要に応じて、 [Azure の要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) に準拠するように名前を変更します。
4. Azure VM に割り当てられているターゲット ネットワーク、サブネット、および IP アドレスの設定を変更することができます。

    a. ターゲット IP アドレスを設定できます。

    b.  アドレスを指定しなかった場合、フェールオーバーされたマシンでは DHCP が使用されます。

    c. フェールオーバーで使用できないアドレスを設定した場合、フェールオーバーは機能しません。

    d. テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。

    e. ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。

     - ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     - ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     - たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズでは 4 つまでサポートされている場合、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあり、サポートされているターゲット サイズでは 1 つしかサポートされていない場合、ターゲット マシンのアダプターは 1 つだけになります。     
   - 仮想マシンにネットワーク アダプターが複数ある場合、これらのアダプターはすべて同じネットワークに接続されます。
   - 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure VM の "*既定*" のネットワーク アダプターとなります。
5. **[ディスク]** で、レプリケートされた VM のオペレーティング システム ディスクとデータ ディスクを確認できます。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

すべてのセットアップが完了したら、テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。 始める前に、簡単なビデオ概要を見ます。

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. 1 つのマシンをフェールオーバーする場合は、**[設定]** > **[レプリケートされたアイテム]** の順にクリックし、**[テスト フェールオーバー]** をクリックします。

    ![[テスト フェールオーバー]](./media/site-recovery-vmware-to-azure/TestFailover.png)

2. 復旧計画をフェールオーバーする場合は、**[設定]** > **[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。  
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、VM をクリックしてそのプロパティを開くか、コンテナー名 > **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックし、**[テスト フェールオーバー]** ジョブをクリックします。
5. フェールオーバーの完了後、Azure Portal の **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行されていることを確認してください。
6. [フェールオーバー後の接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)が完了したら、Azure VM に接続できるようになります。
7. 完了したら、復旧計画の **[テスト フェールオーバーのクリーンアップ]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 この手順により、テスト フェールオーバー中に作成された仮想マシンが削除されます。

詳しくは、[Azure へのテスト フェールオーバー](site-recovery-test-failover-to-azure.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

レプリケーションの準備を完了して実行した後に障害が発生したら、Azure にフェールオーバーし、レプリケートされたデータから Azure VM が作成されます。 通常の運用に戻るときは、プライマリ ロケーションにフェールバックするまで、Azure でワークロードとアプリにアクセスできます。

- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- レプリケートやフェールバックではなく、マシンを移行する場合は、[詳細](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)を確認してください。
- 物理マシンをレプリケートする場合、可能なフェールバック先は VMware 環境のみです。 [フェールバックの詳細](site-recovery-failback-azure-to-vmware.md)を確認してください。

## <a name="third-party-software-notices-and-information"></a>サード パーティ製ソフトウェアに関する通知および情報
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third-Party Code”). Microsoft is not the original author of the Third-Party Code. The original copyright notice and license, under which Microsoft received such Third-Party Code, are set forth below.

The information in Section A is regarding Third-Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third-Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file can be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel, or otherwise.

