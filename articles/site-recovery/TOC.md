# 概要
## [Site Recovery とは](site-recovery-overview.md)
## Site Recovery のしくみ
### [Azure から Azure へのアーキテクチャ](site-recovery-azure-to-azure-architecture.md)
### [Hyper-V から Azure へのアーキテクチャ](site-recovery-architecture-hyper-v-to-azure.md)
### [セカンダリ サイトへのレプリケーションのアーキテクチャ](site-recovery-architecture-to-secondary-site.md)
## [保護できるワークロード](site-recovery-workload.md)
## Site Recovery のサポート マトリックス
### [Azure から Azure へのサポート](site-recovery-support-matrix-azure-to-azure.md)
### [オンプレミスから Azure へのサポート](site-recovery-support-matrix-to-azure.md)
### [オンプレミスからセカンダリ サイトへのサポート](site-recovery-support-matrix-to-sec-site.md)
## [FAQ](site-recovery-faq.md)
## [概要を見る](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# 作業の開始
## [Azure VM をレプリケートする (プレビュー)](site-recovery-azure-to-azure.md)
## [VMware VM を Azure にレプリケート](vmware-walkthrough-overview.md)
### [手順 1: アーキテクチャを確認する](vmware-walkthrough-architecture.md)
### [手順 2: 前提条件と制限事項を確認する](vmware-walkthrough-prerequisites.md)
### [手順3: 容量を計画する](vmware-walkthrough-capacity.md)
### [手順4: ネットワークを計画する](vmware-walkthrough-network.md)
### [手順 5: Azure を準備する](vmware-walkthrough-prepare-azure.md)
### [手順 6: VMware を準備する](vmware-walkthrough-prepare-vmware.md)
### [ステップ 7: コンテナーを作成する](vmware-walkthrough-create-vault.md)
### [手順 8: ソースとターゲットを設定する](vmware-walkthrough-source-target.md)
### [手順 9: レプリケーション ポリシーを作成する](vmware-walkthrough-replication.md)
### [手順 10: モビリティ サービスをインストールする](vmware-walkthrough-install-mobility.md)
### [ステップ 11: レプリケーションを有効にする](vmware-walkthrough-enable-replication.md)
### [ステップ 12: テスト フェールオーバーを実行する](vmware-walkthrough-test-failover.md)
## [Hyper-V VM を Azure にレプリケート](hyper-v-site-walkthrough-overview.md)
### [手順 1: アーキテクチャを確認する](hyper-v-site-walkthrough-architecture.md)
### [手順 2: 前提条件と制限事項を確認する](hyper-v-site-walkthrough-prerequisites.md)
### [手順3: 容量を計画する](hyper-v-site-walkthrough-capacity.md)
### [手順4: ネットワークを計画する](hyper-v-site-walkthrough-network.md)
### [手順 5: Azure を準備する](hyper-v-site-walkthrough-prepare-azure.md)
### [手順 6: Hyper-V ホストを準備する](hyper-v-site-walkthrough-prepare-hyper-v.md)
### [ステップ 7: コンテナーを作成する](hyper-v-site-walkthrough-create-vault.md)
### [手順 8: ソースとターゲットを設定する](hyper-v-site-walkthrough-source-target.md)
### [手順 9: レプリケーション ポリシーを作成する](hyper-v-site-walkthrough-replication.md)
### [手順 10: レプリケーションを有効にする](hyper-v-site-walkthrough-enable-replication.md)
### [手順 11: テスト フェールオーバーを実行する](hyper-v-site-walkthrough-test-failover.md)
## [Hyper-V VM を Azure にレプリケートする (VMM 使用)](site-recovery-vmm-to-azure.md)
## [物理サーバーを Azure にレプリケート](physical-walkthrough-overview.md)
### [手順 1: アーキテクチャを確認する](physical-walkthrough-architecture.md)
### [手順 2: 前提条件と制限事項を確認する](physical-walkthrough-prerequisites.md)
### [手順3: 容量を計画する](physical-walkthrough-capacity.md)
### [手順4: ネットワークを計画する](physical-walkthrough-network.md)
### [手順 5: Azure を準備する](physical-walkthrough-prepare-azure.md)
### [手順 6: 資格情報コンテナーを作成する](physical-walkthrough-create-vault.md)
### [手順 7: ソースとターゲットを設定する](physical-walkthrough-source-target.md)
### [手順 8: レプリケーション ポリシーを作成する](physical-walkthrough-replication.md)
### [手順 9: モビリティ サービスをインストールする](physical-walkthrough-install-mobility.md)
### [手順 10: レプリケーションを有効にする](physical-walkthrough-enable-replication.md)
### [手順 11: テスト フェールオーバーを実行する](physical-walkthrough-test-failover.md)
## [Hyper-V VM をセカンダリ サイトにレプリケートする (VMM 使用)](site-recovery-vmm-to-vmm.md)
## [VMware VM と物理サーバーをセカンダリ サイトにレプリケートする](site-recovery-vmware-to-vmware.md)
## [マルチテナント デプロイで VMware VM を Azure にレプリケートする (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# 方法
## プラン
### [Azure へのレプリケーションの前提条件](site-recovery-azure-to-azure-prereq.md)
### ネットワークを計画する
#### [Azure から Azure へのレプリケーションのネットワークを計画する (プレビュー)](site-recovery-azure-to-azure-networking-guidance.md)
#### [オンプレミス コンピューターのレプリケーションのネットワークを計画する](site-recovery-network-design.md)
#### [Azure VM レプリケーションのネットワーク マッピングを計画する](site-recovery-network-mapping-azure-to-azure.md)
#### [Hyper-V VM レプリケーションのネットワーク マッピングを計画する](site-recovery-network-mapping.md)
### 容量とスケーラビリティを計画する
#### [Azure への VMware レプリケーションの容量を計画する](site-recovery-plan-capacity-vmware.md)
#### [Azure への VMware レプリケーションのための Deployment Planner](site-recovery-deployment-planner.md)
#### [Hyper-V レプリケーションのための Capacity Planner](site-recovery-capacity-planner.md)
### [VM レプリケーションのロールベースのアクセスを計画する](site-recovery-role-based-linked-access-control.md)
## 構成
### ソース環境をセットアップする
#### [VMware から Azure への場合のソース環境](site-recovery-set-up-vmware-to-azure.md)
#### [物理から Azure への場合のソース環境](site-recovery-set-up-physical-to-azure.md)
### ターゲット環境をセットアップする
#### [VMware から Azure への場合のターゲット環境](site-recovery-prepare-target-vmware-to-azure.md)
#### [物理から Azure への場合のターゲット環境](site-recovery-prepare-target-physical-to-azure.md)
### [レプリケーションの設定を構成する](site-recovery-setup-replication-settings-vmware.md)
### [VMware のレプリケーション用にモビリティ サービスをデプロイする](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [System Center Configuration Manager を使用してモビリティ サービスをデプロイする](site-recovery-install-mobility-service-using-sccm.md)
#### [Azure Automation DSC を使用してモビリティ サービスをデプロイする](site-recovery-automate-mobility-service-install.md)
### Enable replication
#### [Azure から Azure へのレプリケーションを有効にする](site-recovery-replicate-azure-to-azure.md)
#### [VMware から Azure へのレプリケーションを有効にする](site-recovery-replicate-vmware-to-azure.md)
## フェールオーバーとフェールバック
### [復旧計画を設定する](site-recovery-create-recovery-plans.md)
#### [復旧計画に Azure Runbook を追加する](site-recovery-runbook-automation.md)
### テスト フェールオーバーの実行
#### [Azure へのテスト フェールオーバーの実行](site-recovery-test-failover-to-azure.md)
#### [VMM クラウド間でのテスト フェールオーバーの実行](site-recovery-test-failover-vmm-to-vmm.md)
### [保護されたマシンのフェールオーバー](site-recovery-failover.md)
### フェールオーバー後にマシンを再保護する
#### [Azure セカンダリ リージョンからプライマリへの再保護](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [Azure からオンプレミスへの再保護](site-recovery-how-to-reprotect.md)
### Azure からのフェールバック
#### [Azure から VMware へのフェールバック](site-recovery-failback-azure-to-vmware.md)
#### [Azure から Hyper-V へのフェールバック](site-recovery-failback-from-azure-to-hyper-v.md)
## 移行
### [Azure への移行](site-recovery-migrate-to-azure.md)
### [Azure リージョンの間で移行](site-recovery-migrate-azure-to-azure.md)
### [AWS Windows インスタンスの Azure への移行](site-recovery-migrate-aws-to-azure.md)
### [別の Azure リージョンへの移行マシンのレプリケート](site-recovery-azure-to-azure-after-migration.md)
## ワークロード
### [Active Directory と DNS](site-recovery-active-directory.md)
### [SQL Server のレプリケート](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [IIS ベースの Web アプリケーション](site-recovery-iis.md)
### [Citrix XenApp と XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [その他のワークロード](site-recovery-workload.md#workload-summary)
## レプリケーションの自動化
### [Azure への Hyper-V のレプリケーションの自動化 (VMM なし)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Azure への Hyper-V のレプリケーションの自動化 (VMM を使用)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [セカンダリ サイトへの Hyper-V のレプリケーションの自動化 (VMM を使用)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Manage
### [Azure でのプロセス サーバーの管理](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [構成サーバーの管理](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [スケールアウト プロセス サーバーの管理](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [vCenter サーバーの管理](site-recovery-vmware-to-azure-manage-vCenter.md)
### [サーバーの削除と保護の無効化](site-recovery-manage-registration-and-protection.md)

## 監視とトラブルシューティング
### [Azure から Azure へのレプリケーションの問題](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [オンプレミスから Azure へのレプリケーションの問題](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [ログの収集とオンプレミスの問題のトラブルシューティング](site-recovery-monitoring-and-troubleshooting.md)

# リファレンス
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell クラシック](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST ()](https://msdn.microsoft.com/en-us/library/mt750497)

# 関連項目
## [Azure Automation](/azure/automation/)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/)
## [ブログ](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [料金](https://azure.microsoft.com/pricing/details/site-recovery/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=site-recovery)
