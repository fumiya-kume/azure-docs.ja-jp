---
title: "Azure AD Connect: バージョンのリリース履歴 | Microsoft Docs"
description: "この記事では、Azure AD Connect と Azure AD Sync のすべてのリリースの一覧を示します"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 4703cd03db398d8dc59fb5f5c0cf71214c606bc8
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: バージョンのリリース履歴
Azure Active Directory (Azure AD) チームは、Azure AD Connect を定期的に更新し、新機能を追加しています。 すべての追加機能がすべてのユーザーに適用されるわけではありません。

この記事は、リリースされたバージョンを追跡し、最新バージョンに更新する必要があるかどうかを判断できるようにするためのものです。

以下は、関連トピックの一覧です。


トピック |  詳細
--------- | --------- |
Azure AD Connect からのアップグレード手順 | Azure AD Connect の [以前のバージョンから最新バージョンにアップグレード](active-directory-aadconnect-upgrade-previous-version.md) するさまざまな方法を説明しています。
必要なアクセス許可 | 更新プログラムの適用に必要なアクセス許可については、[アカウントとアクセス許可](./active-directory-aadconnect-accounts-permissions.md#upgrade)に関するページを参照してください。
ダウンロード| [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="115240"></a>1.1.524.0
リリース: 2017 年 5 月

> [!IMPORTANT]
> このビルドでは、スキーマと同期規則に変更が加えられています。 アップグレードの後、フル インポートの手順と完全同期の手順が Azure AD Connect 同期サービスによってトリガーされます。 変更の詳細は以下で説明しています。
>
>

**修正された問題:**

Azure AD Connect Sync

* ユーザーが Set-ADSyncAutoUpgrade コマンドレットを使用して自動アップグレード機能を無効にしたにもかかわらず、Azure AD Connect サーバーで自動アップグレードが実行される問題を修正しました。 この修正の適用後も、サーバー上の自動アップグレード プロセスで引き続きアップグレードが定期的にチェックされますが、ダウンロードされたインストーラーは、自動アップグレードの構成を忠実に守ります。
* DirSync のインプレース アップグレード中、Azure AD Connect は、Azure AD コネクタが Azure AD との同期に使用する Azure AD サービス アカウントを作成します。 アカウントの作成後、Azure AD Connect は、そのアカウントを使って Azure AD を認証します。 この認証が一時的な問題で失敗することがあり、それが原因で、DirSync のインプレース アップグレードも "*An error has occurred executing Configure AAD Sync task: AADSTS50034: To sign into this application, the account must be added to the xxx.onmicrosoft.com directory. (AAD 同期タスクの構成の実行中にエラーが発生しました: AADSTS50034: このアプリケーションにサインインするには、xxx.onmicrosoft.com ディレクトリにアカウントを追加する必要があります。)*" というエラーが発生して失敗することがあります。 DirSync アップグレードの回復性を高めるために、Azure AD Connect で認証ステップが再試行されるようになりました。
* ビルド 443 では、DirSync のインプレース アップグレードは成功するものの、ディレクトリの同期に必要な実行プロファイルが作成されない問題がありました。 このビルドの Azure AD Connect には、復旧ロジックが追加されています。 ユーザーがこのビルドにアップグレードするときに、不足している実行プロファイルが Azure AD Connect によって検出されて作成されます。
* パスワード同期処理が、イベント ID 6900 および "*同一のキーを含む項目が既に追加されています*" というエラーで起動に失敗する問題を修正しました。 この問題は、AD 構成パーティションを含めるように OU のフィルタリング構成を更新した場合に発生します。 この問題を修正するため、AD ドメイン パーティションからのパスワード変更のみを同期するようにパスワード同期処理を変更しました。 非ドメイン パーティション (構成パーティションなど) はスキップされます。
* AD コネクタでオンプレミス AD との通信に使用されるオンプレミス AD DS アカウントが、高速インストール中、Azure AD Connect によって作成されます。 以前のバージョンでは、このアカウントが、user-Account-Control 属性の PASSWD_NOTREQD フラグを設定した状態で作成され、アカウントにはランダムなパスワードが設定されます。 新しいバージョンでは、アカウントのパスワードが設定された後、PASSWD_NOTREQD フラグは Azure AD Connect によって明示的に削除されます。
* オンプレミス AD スキーマに mailNickname 属性が検出されたものの、AD ユーザー オブジェクト クラスにその属性がバインドされていないと、"*a deadlock occurred in sql server which trying to acquire an application lock (アプリケーション ロックの取得を試みるデッドロックが SQL Server で発生しました)*" というエラーが発生して DirSync のアップグレードが失敗する問題を修正しました。
* 管理者が Azure AD Connect ウィザードを使って Azure AD Connect Sync の構成を更新しているときに、デバイスの書き戻し機能が自動的に無効になる問題を修正しました。 この問題は、デバイスの書き戻し構成が既にオンプレミス AD に存在しているときに、ウィザードによってその前提条件チェックが実行され、失敗することが原因で発生します。 デバイスの書き戻しが既に有効になっている場合は、このチェックをスキップすることで問題を修正しました。
* OU のフィルタリングは、Azure AD Connect ウィザードを使用するか、または Synchronization Service Manager を使用して構成することができます。 以前のバージョンでは、Azure AD Connect ウィザードを使用して OU のフィルタリングを構成した場合、後で作成した新しい OU がディレクトリ同期の対象に含められます。 新しい OU を含めたくない場合は、Synchronization Service Manager を使って OU のフィルタリングを構成する必要があります。 新しいバージョンでは、同じ動作を Azure AD Connect ウィザードを使って実現できます。
* Azure AD Connect で必要なストアド プロシージャが、dbo スキーマにではなく、インストールしている管理者のスキーマに作成される問題を修正しました。
* Azure AD から返される TrackingId 属性が AAD Connect Server のイベント ログから抜け落ちる問題を修正しました。 この問題は、Azure AD Connect が Azure AD からリダイレクト メッセージを受信し、指定されたエンドポイントに Azure AD Connect が接続できない場合に発生します。 TrackingId は、トラブルシューティング時に、サービス側のログに関連付ける目的でサポート エンジニアが使用します。
* Azure AD Connect は、Azure AD から LargeObject エラーを受け取ると、EventID 6941 のイベントと "*提供されたオブジェクトが大きすぎます。このオブジェクト上の属性値の数を調整してください*" というメッセージを生成します。 その際、誤解を招くおそれのある EventID 6900 イベントと、"*Microsoft.Online.Coexistence.ProvisionRetryException: Windows Azure Active Directory サービスと通信できません*" というメッセージも Azure AD Connect から生成されます。 今後は混乱を防ぐために、Azure AD Connect で LargeObject エラーが発生しても、後者のイベントは生成されません。
* Generic LDAP コネクタの構成を更新しようとしているときに Synchronization Service Manager が無応答になる問題を修正しました。

**新機能/改善点:**

Azure AD Connect Sync
* 同期規則の変更 - 以下の同期規則に変更を加えました。
  * **userCertificate** 属性と **userSMIMECertificate** 属性に割り当てられている値が 15 個を超えている場合、これらの属性をエクスポートしないように既定の同期規則セットを更新しました。
  * AD 属性 **employeeID** と **msExchBypassModerationLink** は、既定の同期規則セットに追加しました。
  * AD 属性 **photo** は、既定の同期規則セットから削除しました。
  * メタバースのスキーマと AAD コネクタのスキーマに **preferredDataLocation** を追加しました。 Azure AD でいずれかの属性を更新する必要のあるユーザーは、カスタム同期規則を実装してそのようにすることができます。 この属性について詳しくは、「[Azure AD Connect Sync: 既定の構成を変更する方法」の「Enable synchronization of PreferredDataLocation (PreferredDataLocation の同期の有効化)](active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-preferreddatalocation)」を参照してください。
  * メタバースのスキーマと AAD コネクタのスキーマに **userType** を追加しました。 Azure AD でいずれかの属性を更新する必要のあるユーザーは、カスタム同期規則を実装してそのようにすることができます。

* 今後は Azure AD Connect で、オンプレミス AD オブジェクトのソース アンカー属性として ConsistencyGuid 属性の使用が自動的に有効になります。さらに、ConsistencyGuid 属性の値が空の場合、Azure AD Connect によって objectGuid 属性の値が自動的に設定されます。 この機能は新しいデプロイにのみ適用されます。 この機能について詳しくは、「[Azure AD Connect: 設計概念」の「sourceAnchor としての msDS-ConsistencyGuid の使用](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor)」を参照してください。
* トラブルシューティングのための新しいコマンドレット Invoke-ADSyncDiagnostics を追加しました。パスワード ハッシュ同期に関する問題の診断に役立てることができます。
* Azure AD Connect で新たに、オンプレミスの AD と Azure AD との間で "メールが有効なパブリック フォルダ" オブジェクトの同期がサポートされます。 この機能は、Azure AD Connect ウィザードのオプション機能から有効にできます。
* Azure AD Connect では、オンプレミス AD から同期するために AD DS アカウントが必要となります。 以前のバージョンでは、高速モードで Azure AD Connect をインストールする場合、エンタープライズ管理者アカウントの資格情報を指定できます。 必要な AD DS アカウントは、Azure AD Connect によって作成されます。 ただし、カスタム インストールを行う場合や、既存のデプロイにフォレストを追加する場合は、AD DS アカウントを自分で指定する必要があります。 今後は、カスタム インストールの際に、エンタープライズ管理者アカウントの資格情報を指定することで、必要な AD DS アカウントを Azure AD Connect で自動的に作成することができます。
* Azure AD Connect で新たに SQL AOA がサポートされます。 Azure AD Connect をインストールする前に SQL を有効にする必要があります。 インストール中、指定された SQL インスタンスで SQL AOA が有効であるかどうかが Azure AD Connect によって検出されます。 SQL AOA が有効である場合、Azure AD Connect はさらに、SQL AOA が、同期レプリケーションまたは非同期レプリケーションを使用するように構成されているかどうかを調べます。 可用性グループ リスナーを設定するときは、RegisterAllProvidersIP プロパティを 0 に設定することをお勧めします。 Azure AD Connect は現在、SQL Native Client を使用して SQL に接続していますが、SQL Native Client は、MultiSubNetFailover プロパティの使用をサポートしていないためです。
* Azure AD Connect サーバーのデータベースとして LocalDB を使用していて、サイズの上限である 10 GB に達した場合、それ以降、同期サービスは起動しません。 以前のバージョンでは、LocalDB で ShrinkDatabase 操作を実行し、同期サービスを起動できるだけの DB 空き領域を回収する必要があります。 その後は、Synchronization Service Manager を使用して実行履歴を削除し、DB 空き領域をさらに回収することができます。 新しいバージョンでは、Start-ADSyncPurgeRunHistory コマンドレットを使用して実行履歴データを LocalDB から消去し、DB 空き領域を回収することができます。 このコマンドレットは、同期サービスが実行されていないときに使用できるオフライン モードにも対応しています (-offline パラメーターを指定)。 注: オフライン モードは、同期サービスが実行されておらず、なおかつ使用されているデータベースが LocalDB である場合にのみ使用できます。
* 新しいバージョンの Azure AD Connect では、必要な記憶域スペースを小さくするために、同期エラーの詳細情報は、圧縮してから LocalDB/SQL データベースに格納されます。 以前のバージョンの Azure AD Connect からこのバージョンにアップグレードすると、既に存在している同期エラー情報に対して一回限りの圧縮が実行されます。
* 以前のバージョンでは、OU のフィルタリング構成を更新した後、フル インポートを手動で実行して、ディレクトリ同期の対象に既存のオブジェクトを適切に含めたり、対象から除外したりする必要があります。 新しいバージョンの Azure AD Connect では、次の同期サイクルの間にフル インポートが自動的にトリガーされます。 また、フル インポートは、更新の影響を受けた AD コネクタにのみ適用されます。 注: この機能強化が適用されるのは、Azure AD Connect ウィザードを使用して行われた OU のフィルタリングの更新だけです。 Synchronization Service Manager を使って行われた OU のフィルタリングの更新には適用されません。
* 以前のバージョンでは、グループベースのフィルターが、ユーザー オブジェクト、グループ オブジェクト、連絡先オブジェクトでしかサポートされていません。 新しいバージョンでは、グループベースのフィルターでコンピューター オブジェクトもサポートされます。
* 以前のバージョンでは、Azure AD Connect 同期スケジューラを無効にしなくても、コネクタ スペースのデータを削除することができます。 新しいバージョンでは、スケジューラが有効になっていることを Synchronization Service Manager が検出した場合、コネクタ スペースのデータは、削除できないようブロックされます。 さらに、コネクタ スペースのデータを削除するとデータが失われる可能性があるとして、ユーザーに警告が返されます。
* 以前のバージョンでは、Azure AD Connect ウィザードを正しく動作させるためには、PowerShell トランスクリプションを無効にする必要があります。 この問題は一部解決されています。 Azure AD Connect ウィザードを使用して同期構成を管理している場合は、PowerShell トランスクリプションを有効にすることができます。 Azure AD Connect ウィザードを使用して ADFS の構成を管理している場合は、PowerShell トランスクリプションを無効にする必要があります。



## <a name="114860"></a>1.1.486.0
リリース: 2017 年 4 月

**修正された問題:**
* Azure AD Connect がローカライズ版の Windows Server に正常にインストールされない問題を修正しました。

## <a name="114840"></a>1.1.484.0
リリース: 2017 年 4 月

**既知の問題:**

* 次の条件がすべて当てはまる場合、このバージョンの Azure AD Connect は正常にインストールされません。
   1. Azure AD Connect の DirSync インプレース アップグレードまたは新規インストールのどちらかを実行している。
   2. サーバー上の組み込みの管理者グループの名前が "Administrators" でないローカライズ版の Windows Server を使用している。
   3. 独自の完全な SQL を提供するのではなく、Azure AD Connect と共にインストールされた既定の SQL Server 2012 Express LocalDB を使用している。

**修正された問題:**

Azure AD Connect Sync
* 1 つ以上のコネクタに同期手順の実行プロファイルがない場合、同期スケジューラがその同期手順全体をスキップする問題を修正しました。 たとえば、差分インポート実行プロファイルを作成せずに、Synchronization Service Manager を使用してコネクタを手動で追加した場合です。 この解決策により、同期スケジューラが引き続き他のコネクタの差分インポートを実行することが保証されます。
* いずれかの実行手順で問題が発生した場合、同期サービスが直ちに実行プロファイルの処理を停止する問題を修正しました。 この解決策により、同期サービスがその実行手順をスキップし、残りの処理を続行することが保証されます。 たとえば、複数の実行手順 (オンプレミス AD ドメインごとに 1 つ) を含む AD コネクタ用の差分インポート実行プロファイルがあります。 そのいずれかにネットワーク接続に関する問題が発生した場合でも、同期サービスは他の AD ドメインの差分インポートを実行します。
* 自動アップグレード中に Azure AD Connector の更新がスキップされる問題を修正しました。
* セットアップ中にサーバーがドメイン コントローラであるかどうかを Azure AD Connect が誤って判定し、そのために DirSync アップグレードが失敗する問題を修正しました。
* DirSync インプレース アップグレードで Azure AD Connector の実行プロファイルが作成されない問題を修正しました。
* Generic LDAP コネクタを構成しようとしたときに Synchronization Service Manager ユーザー インターフェイスが無応答になる問題を修正しました。

AD FS の管理
* AD FS プライマリ ノードが別のサーバーに移動されている場合、Azure AD Connect ウィザードが失敗する問題を修正しました。

デスクトップ SSO
* 新規インストール中に [サインイン] オプションとして [パスワードの同期] を選択した場合、[サインイン] 画面で [デスクトップ SSO] 機能を有効にできない Azure AD Connect ウィザードの問題を修正しました。

**新機能/改善点:**

Azure AD Connect Sync
* Azure AD Connect 同期は現在、そのサービス アカウントとして仮想サービス アカウント、管理サービス アカウント、およびグループ管理サービス アカウントの使用をサポートしています。 これは、Azure AD Connect の新規インストールにのみ適用されます。 Azure AD Connect をインストールしている場合:
    * 既定では、Azure AD Connect ウィザードは仮想サービス アカウントを作成し、それをそのサービス アカウントとして使用します。
    * ドメイン コントローラ上にインストールしている場合、Azure AD Connect は、ドメイン ユーザー アカウントを作成する前の動作にフォールバックし、代わりにそれをそのサービス アカウントとして使用します。
    * 次のいずれかを指定することによって、既定の動作を上書きできます。
      * グループ管理サービス アカウント
      * 管理サービス アカウント
      * ドメイン ユーザー アカウント
      * ローカル ユーザー アカウント
* 以前は、コネクタの更新または同期規則の変更を含む Azure AD Connect の新しいビルドにアップグレードすると、Azure AD Connect は完全同期サイクルをトリガーしました。 現在、Azure AD Connect は、更新を含むコネクタに対してのみフル インポート手順を、同期規則の変更を含むコネクタに対してのみ完全同期手順を選択的にトリガーします。
* 以前は、エクスポート削除しきい値は、同期スケジューラからトリガーされたエクスポートにのみ適用されました。 現在、この機能は、顧客が Synchronization Service Manager を使用して手動でトリガーしたエクスポートを含むように拡張されています。
* Azure AD テナント上に、そのテナントで [パスワードの同期] 機能が有効になっているかどうかを示すサービス構成が存在します。 以前は、アクティブなステージング サーバーがある場合、Azure AD Connect はサービス構成を簡単に誤って構成しました。 現在、Azure AD Connect は、サービス構成をアクティブな Azure AD Connect サーバーだけと整合性のある状態に保持しようとします。
* Azure AD Connect ウィザードは現在、オンプレミス AD で AD のごみ箱が有効になっていないかどうかを検出し、警告を返します。
* 以前は、バッチ内のオブジェクトの合計サイズが特定のしきい値を超えている場合、Azure AD へのエクスポートはタイムアウトし、失敗しました。 現在、この問題が発生した場合、同期サービスは個別の、より小さなバッチでのオブジェクトの再送信を再度試みます。
* 同期サービス キー管理アプリケーションが Windows の [スタート] メニューから削除されました。 暗号化キーの管理は、miiskmu.exe を使用してコマンド ライン インターフェイス経由で引き続きサポートされます。 暗号化キーの管理については、[Azure AD Connect 同期の暗号化キーの破棄](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key)の記事を参照してください。
* 以前は、Azure AD Connect 同期サービス アカウントのパスワードを変更すると、暗号化キーを破棄し、Azure AD Connect 同期サービス アカウントのパスワードを再初期化するまで、同期サービスを正常に開始できなくなります。 現在、これは必要なくなりました。

デスクトップ SSO

* Azure AD Connect ウィザードでは、パススルー認証およびデスクトップ SSO を構成する場合、ネットワーク上でポート 9090 を開く必要がなくなりました。 ポート 443 のみが必要です。 

## <a name="114430"></a>1.1.443.0
リリース: 2017 年 3 月

**修正された問題:**

Azure AD Connect Sync
* Azure AD テナントに割り当てられた初期の onmicrosoft.com ドメインが Azure AD コネクタの表示名に含まれていない場合に Azure AD Connect ウィザードが失敗する原因となる問題が修正されました。
* 同期サービス アカウントのパスワードにアポストロフィ、コロン、スペースなどの特殊文字が含まれている場合に SQL Database への接続時に Azure AD Connect ウィザードが失敗する原因となる問題が修正されました。
* オンプレミスの ADオブジェクトを同期から一時的に除外してからもう一度同期に含めた後で、ステージング モードの Azure AD Connect サーバーで「The dimage has an anchor that is different than the image (dimage にイメージとは異なるアンカーがあります)」エラーが発生する原因となる問題が修正されました。
* オンプレミスの ADオブジェクトを同期から一時的に除外してからもう一度同期に含めた後で、ステージング モードの Azure AD Connect サーバーで「The object located by DN is a phantom (DN によって特定されたオブジェクトはファントムです)」エラーが発生する原因となる問題が修正されました。

AD FS の管理
* 代替ログイン ID が構成された後、Azure AD Connect ウィザードが AD FS の構成を更新せず、証明書利用者信頼に対する適切な要求を設定しない問題が修正されました。
* サービス アカウントが sAMAccountName 形式ではなく userPrincipalName 形式を使用して構成されている AD FS サーバーを Azure AD Connect ウィザードが正しく処理できない問題が修正されました。

パススルー認証
* パススルー認証が選ばれていてもそのコネクタの登録に失敗する場合に Azure AD Connect ウィザードが失敗する原因となる問題が修正されました。
* デスクトップ SSO 機能を有効にするときに選ばれたサインイン メソッドに対する検証チェックを Azure AD Connect ウィザードがバイパスする原因となる問題が修正されました。

パスワードのリセット
* ファイアウォールまたはプロキシにより接続が強制終了した場合に Azure AAD Connect サーバーによる再接続の試行が行われない問題が修正されました。

**新機能/改善点:**

Azure AD Connect Sync
* Get-ADSyncScheduler コマンドレットは、SyncCycleInProgress という名前の新しいブール値プロパティを返すようになりました。 戻り値が true の場合は、進行中のスケジュールされた同期サイクルがあることを意味します。
* Azure AD Connect のインストールおよびセットアップ ログを格納するための保存先フォルダーが %localappdata%\AADConnect から %programdata%\AADConnect に移動され、ログ ファイルへのアクセシビリティが改善されました。

AD FS の管理
* AD FS ファーム SSL 証明書の更新のサポートが追加されました。
* AD FS 2016 を管理するためのサポートが追加されました。
* AD FS のインストール中に既存の gMSA (グループ管理サービス アカウント) を指定できるようになりました。
* SHA-256 を Azure AD 証明書利用者信頼の署名ハッシュ アルゴリズムとして構成できるようになりました。

パスワードのリセット
* より厳格なファイアウォール規則の環境においても製品が機能するように機能強化が導入されました。
* Azure Service Bus への接続信頼性が向上しました。

## <a name="113800"></a>1.1.380.0
リリース: 2016 年 12 月

**修正された問題:**

* このビルドでの Active Directory Federation Services (AD FS) 用の issuerid 要求規則が欠落しているという問題を解決しました。

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

## <a name="113710"></a>1.1.371.0
リリース: 2016 年 12 月

**既知の問題:**

* このビルドには AD FS 用の issuerid 要求規則が欠落しています。 Azure Active Directory (Azure AD) で複数のドメインのフェデレーションを行っている場合は issuerid 要求規則が必要です。 Azure AD Connect を使用してオンプレミスの AD FS デプロイを管理している場合は、このビルドにアップグレードすると、既存の issuerid 要求規則が AD FS 構成から削除されます。 この問題は、インストール/アップグレード後に issuerid 要求規則を追加することで回避できます。 issuerid 要求規則の追加の詳細については、「[Azure AD とのフェデレーションに使用する複数ドメインのサポート](active-directory-aadconnect-multiple-domains.md)」を参照してください。

**修正された問題:**

* ポート 9090 が送信接続に開かれていない場合、Azure AD Connect のインストールまたはアップグレードは失敗します。

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

## <a name="113700"></a>1.1.370.0
リリース: 2016 年 12 月

**既知の問題:**

* このビルドには AD FS 用の issuerid 要求規則が欠落しています。 Azure AD で複数のドメインのフェデレーションを行っている場合は issuerid 要求規則が必要です。 Azure AD Connect を使用してオンプレミスの AD FS デプロイを管理している場合は、このビルドにアップグレードすると、既存の issuerid 要求規則が AD FS 構成から削除されます。 この問題は、インストール/アップグレード後に issuerid 要求規則を追加することで回避できます。 issuerid 要求規則の追加の詳細については、「[Azure AD とのフェデレーションに使用する複数ドメインのサポート](active-directory-aadconnect-multiple-domains.md)」を参照してください。
* インストールを完了するには、ポート 9090 が送信に対して開かれている必要があります。

**新機能:**

* パススルー認証 (プレビュー)。

>[!NOTE]
>このビルドは、Azure AD Connect の自動アップグレード機能では提供されません。

## <a name="113430"></a>1.1.343.0
リリース: 2016 年 11 月

**既知の問題:**

* このビルドには AD FS 用の issuerid 要求規則が欠落しています。 Azure AD で複数のドメインのフェデレーションを行っている場合は issuerid 要求規則が必要です。 Azure AD Connect を使用してオンプレミスの AD FS デプロイを管理している場合は、このビルドにアップグレードすると、既存の issuerid 要求規則が AD FS 構成から削除されます。 この問題は、インストール/アップグレード後に issuerid 要求規則を追加することで回避できます。 issuerid 要求規則の追加の詳細については、「[Azure AD とのフェデレーションに使用する複数ドメインのサポート](active-directory-aadconnect-multiple-domains.md)」を参照してください。

**修正された問題:**

* 組織のパスワード ポリシーで指定された複雑さのレベルを満たしているパスワードを持つローカル サービス アカウントを作成することができないため、Azure AD Connect のインストールが失敗することがあります。
* コネクタ スペースのオブジェクトが、1 つの結合規則ではスコープ外になり、別の結合規則ではスコープ内になった場合に、結合規則が再評価されない問題が修正されました。 この問題は、結合条件が相互に排他的になっている 2 つ以上の結合規則がある場合に発生することがあります。
* 結合規則のない (Azure AD からの) 受信同期規則の優先順位の値が、結合規則のある受信同期規則よりも低い場合、結合規則のない受信同期規則が処理されないという問題が修正されました。

**機能強化:**

* Windows Server 2016 Standard 以降に Azure AD Connect をインストールするためのサポートが追加されました。
* Azure AD Connect のリモートのデータベースとしての SQL Server 2016 の使用がサポートされるようになりました。

## <a name="112810"></a>1.1.281.0
リリース: 2016 年 8 月

**修正された問題:**

* 同期間隔の変更が、次の同期サイクルの完了後まで反映されません。
* Azure AD Connect ウィザードで、アンダースコア (\_) で始まるユーザー名を持つ Azure AD アカウントを使用できません。
* Azure AD Connect ウィザードで、アカウントのパスワードに含まれる特殊文字の数が多すぎると、指定した Azure AD アカウントの認証が失敗します。 "資格情報を検証できません。 予期しないエラーが発生しました" というエラー メッセージが 返されます。
* ステージング サーバーをアンインストールすると、Azure AD テナントでパスワード同期が無効になり、アクティブなサーバーでのパスワード同期が失敗します。
* ユーザーに対してパスワードのハッシュが格納されていない場合、例外的な状況でパスワード同期が失敗します。
* Azure AD Connect サーバーでステージング モードが有効になっている場合、パスワード ライトバックが一時的に無効になりません。
* サーバーがステージング モードの場合、Azure AD Connect ウィザードでパスワード同期とパスワード ライトバックの実際の構成が表示されません。 常に無効として表示されます。
* サーバーがステージング モードの場合、パスワード同期とパスワード ライトバックの構成への変更が Azure AD Connect ウィザードで保持されません。

**機能強化:**

* Start-ADSyncSyncCycle コマンドレットが更新され、新しい同期サイクルを正常に開始できるかどうかを示すようになりました。
* Stop-ADSyncSyncCycle コマンドレットが追加され、現在実行中の同期サイクルと操作を終了できるようになりました。
* Stop-ADSyncScheduler コマンドレットが更新され、現在実行中の同期サイクルと操作を終了できるようになりました。
* Azure AD Connect ウィザードで[ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)を構成するときに、"Teletex 文字列" 型の Azure AD 属性を選択できるようになりました。

## <a name="111890"></a>1.1.189.0
リリース: 2016 年 6 月

**修正された問題と機能強化:**

* Azure AD Connect を FIPS 準拠のサーバーにインストールできるようになりました。
  * パスワードの同期については、「[パスワード同期と FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)」を参照してください。
* Active Directory コネクタで、NetBIOS を FQDN に名前解決できないという問題が修正されました。

## <a name="111800"></a>1.1.180.0
リリース: 2016 年 5 月

**新機能:**

* Azure AD Connect 実行前にドメインの確認が行われなかった場合に、ドメインの確認について警告し、必要なヘルプ情報を提供します。
* [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany)のサポートが追加されました。
* 新しい URL 要件を含む最新の [Microsoft Azure Government クラウド](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) インフラストラクチャのサポートが追加されました。

**修正された問題と機能強化:**

* 同期規則を探しやすくするフィルターが同期規則エディターに追加されました。
* コネクタ スペースを削除するときのパフォーマンスが改善されました。
* 同じオブジェクトに対して削除と追加の両方が同一の実行で行われた場合 (削除/追加) の問題が修正されました。
* 無効にした同期規則で、含まれるオブジェクトや属性が、アップグレードまたはディレクトリ スキーマの更新時に再び有効にされることがなくなりました。

## <a name="111300"></a>1.1.130.0
リリース: 2016 年 4 月

**新機能:**

* [ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)に、複数値の属性のサポートが追加されました。
* アップグレードの対象と見なされる [自動アップグレード](active-directory-aadconnect-feature-automatic-upgrade.md) の構成バリエーションが増えました。
* [カスタム スケジューラ](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler)にコマンドレットがいくつか追加されました。

## <a name="111190"></a>1.1.119.0
リリース: 2016 年 3 月

**修正された問題:**

* Windows Server 2008 (R2 より前のバージョン) ではパスワード同期がサポートされないため、このオペレーティング システムでは高速インストールが使用できなくなりました。
* カスタム フィルター構成での DirSync からのアップグレードが予期したとおりに動作しません。
* 新しいリリースへのアップグレード時に構成に変更がない場合は、フル インポート/同期をスケジュールすることはできません。

## <a name="111100"></a>1.1.110.0
リリース: 2016 年 2 月

**修正された問題:**

* インストールが既定の C:\Program Files フォルダーにない場合、以前のリリースからのアップグレードが機能しません。
* インストール時に、インストール ウィザードの最後で **[...同期処理を開始してください]** をオフにした場合、2 回目にインストール ウィザードを実行したときにスケジューラが有効になりません。
* 日付と時刻の形式が US-en ではない場合、スケジューラはサーバーで予想どおりに機能しません。 また、正しい時刻を返す `Get-ADSyncScheduler` もブロックされます。
* サインイン オプションおよびアップグレードとして AD FS を使用して以前のリリースの Azure AD Connect をインストールした場合、インストール ウィザードを再度実行することはできません。

## <a name="111050"></a>1.1.105.0
リリース: 2016 年 2 月

**新機能:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) 機能。
* インストール ウィザードで Azure Multi-Factor Authentication および Privileged Identity Management を使用して全体管理者をサポート。
  * Multi-Factor Authentication を使用する場合は、https://secure.aadcdn.microsoftonline-p.com へのトラフィックも許可するように、プロキシを設定する必要があります。
  * Multi-Factor Authentication を正しく動作させるには、信頼済みサイトの一覧に https://secure.aadcdn.microsoftonline-p.com を追加する必要があります。
* 初期インストール後のユーザーのサインイン方法の変更を許可。
* インストール ウィザードでの[ドメインと OU のフィルター処理](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)を許可。 これによって、一部のドメインは使用できないフォレストへの接続も許可されます。
* 同期エンジンに組み込まれた[スケジューラ](active-directory-aadconnectsync-feature-scheduler.md)。

**プレビューから GA に昇格した機能:**

* [デバイスの書き戻し](active-directory-aadconnect-feature-device-writeback.md)。
* [ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)。

**新しいプレビュー機能:**

* 新しい既定の同期サイクル間隔は 30 分です。 以前のすべてのリリースでは、3 時間でした。 [スケジューラ](active-directory-aadconnectsync-feature-scheduler.md) の動作の変更がサポートされるようになりました。

**修正された問題:**

* DNS ドメインの検証ページが、ドメインを認識できない場合がありました。
* AD FS を構成するときに、ドメイン管理者の資格情報を求めるメッセージが表示されます。
* オンプレミス AD アカウントが、ルート ドメインとは異なる DNS ツリーを持つドメイン内にある場合、インストール ウィザードがそのアカウントを認識できません。

## <a name="1091310"></a>1.0.9131.0
リリース: 2015 年 12 月

**修正された問題:**

* Active Directory Domain Services (AD DS) でパスワードを変更するときにパスワードの同期が機能しない場合がありますが、パスワードの設定時には機能します。
* プロキシ サーバーがある場合、Azure AD に対する認証が、インストール中または構成ページでアップグレードが取り消された場合に失敗することがあります。
* SQL Server のシステム管理者 (SA) でない場合、完全な SQL Server インスタンスで以前のリリースの Azure AD Connect から更新すると失敗します。
* リモートの SQL Server で以前のリリースの Azure AD Connect から更新すると、"ADSync SQL データベースにアクセスできません" というエラーが表示されます。

## <a name="1091250"></a>1.0.9125.0
リリース: 2015 年 11 月

**新機能:**

* AD FS から Azure AD に対する信頼を再構成できるようになりました。
* Active Directory スキーマを更新し、同期規則を再生成できるようになりました。
* 同期規則を無効にできるようになりました。
* 同期規則の新しいリテラルとして "AuthoritativeNull" を定義できるようになりました。

**新しいプレビュー機能:**

* [Azure AD Connect Health for Sync](../connect-health/active-directory-aadconnect-health-sync.md)。
* [Azure AD Domain Services](../active-directory-passwords-update-your-own-password.md) のパスワード同期がサポートされました。

**新しくサポートされたシナリオ:**

* 複数のオンプレミス Exchange 組織がサポートされました。 詳細については、「[複数の Active Directory フォレストを伴うハイブリッド展開](https://technet.microsoft.com/library/jj873754.aspx)」を参照してください。

**修正された問題:**

* パスワード同期の問題:
  * スコープ外からスコープ内に移動されたオブジェクトのパスワードは同期されなくなります。 これには、OU と属性フィルターの両方が含まれます。
  * 同期に含める新しい OU を選択する際に、完全なパスワード同期は必要ありません。
  * 無効なユーザーが有効になっても、パスワードは同期されません。
  * パスワード再試行キューは無制限です。5,000 個のオブジェクトを上限として削除されるという以前の制限は削除されました。
<<<<<<< HEAD <<<<<<< HEAD
  * [トラブルシューティングが改善されました](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。
=======
  * [トラブルシューティングが改善されました](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。
>>>>>>> <a name="487b660b6d3bb5ce9e64b6fdbde2ae621cb91922"></a>487b660b6d3bb5ce9e64b6fdbde2ae621cb91922
=======
  * [トラブルシューティングが改善されました](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。
>>>>>>> 4b2e846c2cd4615f4e4be7195899de11e3957c83
* Windows Server 2016 フォレスト機能レベルを使用して Active Directory に接続することはできなくなりました。
* 最初のインストール後にグループ フィルターに使用したグループを変更できなくなりました。
* パスワード ライトバックを有効にしてパスワードを変更した各ユーザーについては、Azure AD Connect の新しいユーザー プロファイルを作成しなくなりました。
* 同期規則スコープに Long Integer 値を使用できなくなりました。
* 到達不能なドメイン コントローラーがある場合、[デバイスの書き戻し] チェック ボックスは無効なままです。

## <a name="1086670"></a>1.0.8667.0
リリース: 2015 年 8 月

**新機能:**

* Azure AD Connect インストール ウィザードが、すべての Windows Server 言語にローカライズされました。
* Azure AD パスワード管理を使用する場合のアカウント ロック解除のサポートが追加されました。

**修正された問題:**

* インストールを開始したユーザー以外のユーザーがインストールを続けると、Azure AD Connect インストール ウィザードがクラッシュします。
* 以前の Azure AD Connect のアンインストールで Azure AD Connect Sync を完全にアンインストールできなかった場合、再インストールすることができません。
* ユーザーがフォレストのルート ドメインに属していないか、英語以外のバージョンの Active Directory が使用されている場合、高速インストールを使用して Azure AD Connect をインストールすることはできません。
* Active Directory ユーザー アカウントの FQDN を解決できない場合、スキーマをコミットできなかったという誤ったエラー メッセージが表示されます。
* Active Directory Connector で使用されているアカウントがウィザードの外部で変更された場合、ウィザードのその後の実行が失敗します。
* ドメイン コントローラーで、Azure AD Connect のインストールが失敗することがあります。
* 拡張属性が追加されている場合、"ステージング モード" の有効化や無効化ができません。
* Active Directory Connector での正しくないパスワードのために、一部の構成ではパスワード ライトバックが失敗します。
* 属性フィルターで識別名 (DN) が使用されている場合、DirSync をアップグレードできません。
* パスワード リセットの使用時に CPU 使用量が過剰になります。

**削除されたプレビュー機能:**

* [ユーザーの書き戻し](active-directory-aadconnect-feature-preview.md#user-writeback) プレビュー機能は、プレビューを利用されているお客様からのフィードバックに基づいて一時的に削除されました。 このプレビュー機能は、提供されたフィードバックに対処した後で、再度追加されます。

## <a name="1086410"></a>1.0.8641.0
リリース: 2015 年 6 月

**Azure AD Connect の最初のリリースです。**

名前が Azure AD Sync から Azure AD Connect に変更されました。

**新機能:**

* [簡単設定](active-directory-aadconnect-get-started-express.md)を使用したインストール
* [AD FS の構成](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* [DirSync からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [誤って削除されないように保護する](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* [ステージング モード](active-directory-aadconnectsync-operations.md#staging-mode)

**新しいプレビュー機能:**

* [ユーザーの書き戻し](active-directory-aadconnect-feature-preview.md#user-writeback)
* [グループの書き戻し](active-directory-aadconnect-feature-preview.md#group-writeback)
* [デバイスの書き戻し](active-directory-aadconnect-feature-device-writeback.md)
* [ディレクトリ拡張機能](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
リリース: 2015 年 5 月

**新しい要件:**

* Azure AD Sync のインストールに .NET Framework 4.5.1 が必要になりました。

**修正された問題:**

* Azure AD からのパスワード ライトバックが、Azure Service Bus 接続のエラーで失敗します。

## <a name="104910413"></a>1.0.491.0413
リリース: 2015 年 4 月

**修正された問題と機能強化:**

* ごみ箱が有効になっていて、フォレスト内に複数のドメインがある場合、Active Directory Connector が削除を正しく処理しません。
* Azure Active Directory Connector で、インポート操作のパフォーマンスが改善されました。
* グループがメンバーシップの制限を超えた場合 (既定では、制限は 50,000 オブジェクトに設定)、Azure Active Directory でグループが削除されます。 新しい動作では、グループは削除されません。エラーがスローされ、新しいメンバーシップの変更はエクスポートされません。
* 同じ DN のステージングされた削除がコネクタ スペース内に既に存在する場合、新しいオブジェクトをプロビジョニングすることはできません。
* オブジェクトでステージングされている変更がなくても、差分同期中に一部のオブジェクトが同期のためにマークされます。
* パスワード同期を強制すると、優先 DC リストも削除されます。
* CSExportAnalyzer には、一部のオブジェクトの状態に関する問題があります。

**新機能:**

* 結合で、MV の "任意" のオブジェクト型に接続できるようになりました。

## <a name="104850222"></a>1.0.485.0222
リリース: 2015 年 2 月

**機能強化:**

* インポートのパフォーマンスが強化されました。

**修正された問題:**

* パスワード同期が、属性フィルターで使用される cloudFiltered 属性を受け取ります。 フィルター処理されたオブジェクトが、パスワード同期のスコープに含まれなくなります。
* トポロジが多くのドメイン コントローラーを持つまれな状況では、パスワード同期が機能しません。
* Azure AD/Intune でデバイス管理が有効化された後、Azure AD Connector からのインポート時に、"サーバーが停止" します。
* 同じフォレスト内の複数のドメインの外部セキュリティ プリンシパル (FSP) を結合すると、あいまい結合のエラーが発生します。

## <a name="104751202"></a>1.0.475.1202
リリース: 2014 年 12 月

**新機能:**

* 属性ベースのフィルターでのパスワード同期がサポートされるようになりました。 詳細については、[フィルターによるパスワード同期](active-directory-aadconnectsync-configure-filtering.md)に関するページを参照してください。
* msDS-ExternalDirectoryObjectID 属性が Active Directory に書き戻されます。 この機能により、Office 365 アプリケーションのサポートが追加されます。 OAuth2 を使用して、ハイブリッド Exchange デプロイのオンラインとオンプレミスのメールボックスへのアクセスが行われます。

**修正されたアップグレードの問題:**

* より新しいバージョンのサインイン アシスタントをサーバーで利用できます。
* Azure AD Sync をインストールするために、カスタム インストール パスが使用されていました。
* 無効なカスタム結合条件によって、アップグレードがブロックされます。

**その他の修正:**

* Office Pro Plus 用のテンプレートが修正されました。
* ダッシュで始まるユーザー名によって発生する、インストールの問題が修正されました。
* インストール ウィザードを 2 回目に実行しているときに sourceAnchor 設定が失われる問題を修正しました。
* パスワード同期の ETW トレースの問題が修正されました。

## <a name="104701023"></a>1.0.470.1023
リリース: 2014 年 10 月

**新機能:**

* 複数のオンプレミス Active Directory から Azure AD へのパスワードの同期。
* すべての Windows Server 言語にローカライズされたインストール UI。

**AADSync 1.0 GA からのアップグレード**

Azure AD Sync が既にインストールされている場合、標準の同期規則を変更したのであれば、追加の手順が 1 つ必要になります。 1.0.470.1023 リリースにアップグレードした後で、変更した同期規則は複製されます。 変更された各同期規則で、次の操作を行ってください。

1.  変更した同期規則を探して、変更内容をメモしておきます。
* 同期規則を削除します。
* Azure AD Sync によって作成された新しい同期規則を探して、変更を再適用します。

**Active Directory アカウントのアクセス許可**

Active Directory アカウントには、Active Directory からのパスワード ハッシュを読み取ることができるように、追加のアクセス許可を与える必要があります。 付与するアクセス許可の名前は、[ディレクトリの変更のレプリケート] と [ディレクトリの変更をすべてにレプリケート] です。 パスワード ハッシュを読み取るためには、両方のアクセス許可が必要です。

## <a name="104190911"></a>1.0.419.0911
リリース: 2014 年 9 月

**Azure AD Sync の最初のリリースです。**

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

