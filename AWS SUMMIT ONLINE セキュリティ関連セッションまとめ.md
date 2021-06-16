## 概要

[AWS SUMMIT ONLINE](https://summits-japan.virtual.awsevents.com/)のうち、セキュリティ関連セッションをまとめました。
(基本的には、資料からの抜粋ですが、サマリに関しては内容を変更しています)

視聴前・視聴後のご参考になれば幸いです。

なお、[AWS Summit Online 2021 は 5/31 までオンデマンドで公開中](https://aws.amazon.com/jp/events/summits/online/japan/)です！

(各セッションの資料も DL 可です)

また、アンケートに回答することで 25 USD の AWS クレジットを獲得できます。お忘れなく。

## 内容

### 【基本の AWS サービス】ユーザーエクスペリエンスとセキュリティを最適化する AWS エッジネットワークサービス(AWS-33)

Link: [【基本の AWS サービス】ユーザーエクスペリエンスとセキュリティを最適化する AWS エッジネットワークサービス(AWS-33) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/%E3%80%90%E5%9F%BA%E6%9C%AC%E3%81%AE%20AWS%20%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%80%91%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E3%82%A8%E3%82%AF%E3%82%B9%E3%83%9A%E3%83%AA%E3%82%A8%E3%83%B3%E3%82%B9%E3%81%A8%E3%82%BB%E3%82%AD%E3%83%A5%E3%83%AA%E3%83%86%E3%82%A3%E3%82%92%E6%9C%80%E9%81%A9%E5%8C%96%E3%81%99%E3%82%8B%20AWS%20%E3%82%A8%E3%83%83%E3%82%B8%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9(AWS-33)/1_vzvxak5u/207422693>)

#### 視聴対象者

> - 顧客向けアプリケーションの構築や AWS への移⾏を検討している IT 部⾨の意思決定者や開発者の⽅
> - AWS エッジネットワークの各サービスの基本を理解したい⽅

#### ゴール

> - ユーザー体験とアプリケーションのセキュリティ向上に AWS エッジネットワークサービスがどのように役⽴つかを理解できるようになる
> - アプリケーションをグローバルに展開するのに役⽴つ主な AWS エッジネットワークサービス・機能を活⽤できるようになる

### まとめ

> - AWS エッジネットワークサービスはインターネットの課題 (可⽤性, 安定性, 性能)
>   を解決するために AWS グローバルネットワークを有効活⽤したサービス
> - ⽇本国内のみ、⼩規模なユースケースでも利⽤可能
> - パフォーマンス: Amazon Route 53, Amazon CloudFront, AWS Global Accelerator
>   を適切に組み合わせることで様々なサイト, サービス, API に適⽤可能
> - セキュリティ: Amazon CloudFront のアクセス制御, 暗号化接続機能, AWS Shield,
>   AWS WAF などの AWS セキュリティサービスと連携
> - カスタマイズ: Lambda@Edge により柔軟な処理を実⾏可能
> - イノベーション: パフォーマンスとセキュリティに関連する機能を継続的に追加
> - コスト最適化: オンデマンド, キャパシティ予約, 料⾦クラスを活⽤

<details><summary>サマリ
</summary><div>

- AWS グローバルネットワーク を利⽤し、より⾼速で安定したユーザ体験を提供
- CloudFront のキャッシュも有効活⽤

- Amazon CloudFront
  - コンテンツデリバリーネットワーク (CDN)
  - AWS グローバルインフラストラクチャ を利⽤することによる⾮キャッシュコンテンツの⾼速化
  - Ref. [Improve your website performance with Amazon CloudFront | Networking & Content Delivery](https://aws.amazon.com/jp/blogs/networking-and-content-delivery/improve-your-website-performance-with-amazon-cloudfront/)
  - [Amazon CloudFront Origin Shield の使用 - Amazon CloudFront](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/origin-shield.html)
    - オリジンの負荷を最小限に抑え、可用性を向上させ、運用コストを削減
  - CloudFront エッジセキュリティ
    - ユーザーに近いロケーションでコードを実⾏
    - 特徴
      - AWS Shield Standard と DDoS 可視性
      - HTTP desync 攻撃からの保護
      - OCSP ステープリング
      - ドメインフロンティング攻撃からの保護
    - Ref. [署名付き URL と署名付き Cookie の選択 - Amazon CloudFront](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/private-content-choosing-signed-urls-cookies.html)
    - Ref. [署名付き URL と署名付き Cookie を使用したプライベートコンテンツの提供 - Amazon CloudFront](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/PrivateContent.html)
    - Ref. [CloudFront の署名付き Cookie でプライベートコンテンツの配信 | DevelopersIO](https://dev.classmethod.jp/articles/cloudfront-signed-cookie/)
- [Amazon CloudFront Lambda@Edge](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html)
  - エッジのイベント駆動型コード
  - タイミング
    - ビューワーリクエスト / レスポンス
    - オリジンリクエスト / レスポンス
  - カスタマイズ
    - レスポンス Header の操作
    - 認証
    - HTTP リダイレクト
    - A/B テスト
    - スマートコンテンツアセンブリ
    - 画像の最適化
    - アクセス制御
- CloudFront コスト最適化
  - Amazon CloudFront Security Savings Bundle
    - Ref. [Amazon CloudFront Security Savings Bundle のご紹介](https://aws.amazon.com/jp/about-aws/whats-new/2021/02/introducing-amazon-cloudfront-security-savings-bundle/)
    - 1 年間の⽉額使⽤量を確約する契約 (利⽤コミット)
    - Lambda@Edge を含む CloudFront の請求額を最⼤ 30% 節約可能
    - CloudFront を保護するために、確約した⽉間使⽤量の最⼤ 10% まで AWS WAF 使⽤料が含まれる
- AWS Shield Standard / AWS Shield Advanced
  - マネージド型の分散サービス妨害 (DDoS) に対する保護
- [AWS WAF（ウェブアプリケーションファイアウォール）| AWS](https://aws.amazon.com/jp/waf/)
  - ウェブアプリケーションファイアウォール
  - WEB アプリケーションの脆弱性を悪⽤した攻撃から WEB アプリケーションを保護
    - 悪意のあるリクエストのブロック
    - カスタムルールに基づいた Web トラフィックのフィルター
    - モニタリングとチューニング
- Amazon Route 53
  - ドメインネームシステム (DNS)
- [AWS Global Accelerator（アプリケーションの可用性とパフォーマンスを向上）| AWS](https://aws.amazon.com/jp/global-accelerator/)
  - AWS グローバルネットワークを使⽤し、アプリケーションのグローバルな可⽤性とパフォーマンスを向上
    - レイテンシーの影響を受けやすいアプリケーションを高速化
    - 簡素化したグローバル規模でのトラフィック管理
    - 回復性と可用性を向上する
    - アプリケーションを保護する

</div></details>

### 入門！ AWS アイデンティティサービス

Link:[【基本の AWS サービス】入門！ AWS アイデンティティサービス(AWS-37) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/%E3%80%90%E5%9F%BA%E6%9C%AC%E3%81%AE%20AWS%20%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%80%91%E5%85%A5%E9%96%80%EF%BC%81%20AWS%20%E3%82%A2%E3%82%A4%E3%83%87%E3%83%B3%E3%83%86%E3%82%A3%E3%83%86%E3%82%A3%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9(AWS-37)/1_yak73sxx/207422693>)

#### 視聴対象者

> - AWS の基本的な概念は知っている方
> - １つの AWS アカウントで利用しているが、だんだん管理が難しくなってきたと感じている方
> - 組織での AWS の利用を検討している方

#### ゴール

> - AWS のアイデンティティサービスで出来ることを知る
>   - 効率的なアクセス権限の管理方法
>   - マルチアカウントのメリットと、その実現方法
>   - 最小権限の原則にそったアクセス権限のアプローチ

### まとめ

> 1. AWS のアイデンティティ
>    - ３つの要素をベースにした IAM 管理の仕組み
> 2. マルチアカウント環境でのアイデンティティ管理
>    - マルチアカウントのメリット
>    - AWS Organizations、AWS SSO などで簡単に構築
> 3. 最小権限を実現するためのテクニック
>    - IAM アクセスアドバイザーによる確認
>
> AWS アイデンティティサービスで効率的で安全なアクセス権限の管理を！

<details><summary>サマリ
</summary><div>

- AWS のアイデンティティ
  - アイデンティティの３つの基本要素
    - `Who` `can access` `What`
  - AWS のアクセス管理
    - アイデンティティベースポリシー
    - リソースベースポリシー
    - タグベースのポリシー
      - Ref. [タグポリシー - AWS Organizations](https://docs.aws.amazon.com/ja_jp/organizations/latest/userguide/orgs_manage_policies_tag-policies.html)
      - Ref. [タグを使用した AWS リソースへのアク$$セスの制御 - AWS Identity and Access Management](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/access_tags.html)
- マルチアカウント環境でのアイデンティティ管理
  - AWS アカウントとは
    - 課金の分離単位
    - AWS 環境の分割単位、リソース管理の枠組み
    - サービスクォータ(サービス制限)の単位
    - セキュリティの境界
  - マルチアカウントを用いるメリット
    - Ref. [AWS におけるマルチアカウント管理の手法とベストプラクティス](https://d0.awsstatic.com/events/jp/2017/summit/slide/D4T2-2.pdf)
    - ガバナンス
      - Ref. [AWS クラウド上の PCI DSS 標準アーキテクチャ: クイックスタートリファレンスデプロイ](https://aws.amazon.com/jp/about-aws/whats-new/2016/05/pci-dss-standardized-architecture-on-the-aws-cloud-quick-start-reference-deployment/)
      - セキュリティ、及びガバナンス上の理由から開発環境、テスト環境、本番環境などでアカウントを分割
    - 課金
      - リソースの操作権限を特定の事業部に委譲し、その中でより自由に AWS プラットフォームを活用
    - 組織
      - 課金に関する可視性、責任、及びアカウントごとのコントロール
    - 運用
      - 構成変更時の影響範囲を小さくし、他の組織を気にすることなく自身固有の環境を利用
  - [AWS Organizations](https://aws.amazon.com/jp/organizations/)
    - 複数の AWS アカウントを 1 つの組織に統合できるマネージドサービス
      - AWS アカウントとリソースの集中プロビジョニング
      - 一括請求によるコスト最適化(ボリューム割引、RI ブレンドレート)
      - セキュリティの一元管理とコンプライアンス準拠
  - [AWS Single Sign-On](https://aws.amazon.com/jp/single-sign-on/)
    - AWS アカウントとビジネスアプリケーションへの SSO を提供
      - 複数 AWS アカウントのコンソールに SSO アクセス
      - ビジネスアプリケーションへの SSO アクセス
      - 既存の社内 ID 基盤の活用
        - Microsoft Active Directory、Okta Universal Directory、および Azure Active Directory (Azure AD) など
      - [AWS SSO Directory](https://aws.amazon.com/jp/blogs/security/how-to-create-and-manage-users-within-aws-sso/)の利用
        - Ref. [AWS Directory Service（Active Directory のホスティングと管理）| AWS](https://aws.amazon.com/jp/directoryservice/)
      - 追加料金なしで簡単に始められる
  - [AWS Control Tower](https://aws.amazon.com/jp/controltower/?control-blogs.sort-by=item.additionalFields.createdDate&control-blogs.sort-order=desc)
    - ベストプラクティスにそった AWS マルチアカウント環境を簡単にセットアップ
    - Control Tower のガードレール
      - 必須のガードレール
        - Control Tower を正常に稼働させるために必要な禁止事項を SCP で定義したもの
        - ログ保存や構成監視を停止させない実装など
      - 推奨されるガードレール
        - マルチアカウントのベストプラクティスに基づく制限事項
        - SCP の例：root ユーザでアクセスキーを作らない、root ユーザで操作しないなど
        - Config Rules の例：EBS ボリュームの暗号化、S3 のパブリック読み書き禁止など
      - 選択的ガードレール
        - AWS エンタープライズ環境で一般的に利用されている制限事項
        - SCP の例：MFA なしの S3 バケット削除禁止、S3 バケットのクロスリージョンレプリカ禁止など
        - Config Rules の例：MFA なしの IAM ユーザアクセス禁止、バージョニングのない S3 の禁止など
  - 最小権限を実現するためのテクニック
    1. IAM のアクセスアドバイザーで各 AWS サービスの最終利用時間をチェック
    2. 必要ないことを確認後、アクセス権限を削除する

</div></details>

### AWS におけるネットワーク＆アプリケーション保護のすすめ(AWS-38)

Link: [AWS におけるネットワーク＆アプリケーション保護のすすめ(AWS-38) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/AWS%20%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%EF%BC%86%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E4%BF%9D%E8%AD%B7%E3%81%AE%E3%81%99%E3%81%99%E3%82%81(AWS-38)/1_vgikbknq/207422693>)

#### 視聴対象者

> スタートアップからエンタープライズまで、AWS のセキュリティアーキテクチャに関わる⽅

### まとめ

> - AWS の境界防御サービスの概要
>   - AWS Network Firewall
>   - AWS WAF
>   - AWS Shield Advanced
> - アプリケーションの要件に応じた設計の勘所
>   - ⼀般的な Web アプリケーション
>   - 遅延にシビアなアプリケーション
>   - サーバーレスアプリケーション
>   - オンプレミスアプリケーション
> - 統制と運⽤をスケールさせる⽅法
>   - AWS Firewall Manager

<details><summary>サマリ
</summary><div>

- AWS の境界防御サービスの概要
  - ![alt](https://image.slidesharecdn.com/take-action-on-your-security-d9890f43-ce8f-4526-ab80-f89028642f9e-1157480807-190531182249/95/take-action-on-your-security-compliance-alerts-with-aws-security-hub-sec201-chicago-aws-summit-6-638.jpg?cb=1559327008)
    - Ref. [Take action on your security & compliance alerts with AWS Security Hub - SEC201 - Chicago AWS Summit](https://www.slideshare.net/AmazonWebServices/take-action-on-your-security-compliance-alerts-with-aws-security-hub-sec201-chicago-aws-summit)
  - 境界防御サービス
    - [AWS Network Firewall – アマゾン ウェブ サービス](https://aws.amazon.com/jp/network-firewall/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc)
      - 特徴
        - きめ細かい制御による柔軟な保護
        - VPC とアカウントにわたり⼀貫したポリシー管理
        - ⾼可⽤性のマネージド型インフラストラクチャ
      - 機能
        - パケットフィルタリング
          - 既存のネットワーク ACL、セキュリティグループで対応できなかった部分
        - 可視性とレポーティング
        - ⼀元管理
      - テクニカルユースケース
        - インターネットからのトラフィックをフィルタリング
        - アウトバウンドトラフィックをフィルタリング
        - VPC から VPC へのトラフィックを検査
        - AWS Direct Connect と VPN のトラフィックを保護
      - デザインパターン
        - 分散型セキュリティインスペクション
        - 中央集中型セキュリティインスペクション
      - 従来の機能との⽐較
        - 通信ペイロードの検査が可能
        - FQDN ベースフィルタが可能
        - ルートテーブルにて、通信がファイアウォールエンドポイントを通るように構成
        - 追加コストあり
    - [AWS WAF（ウェブアプリケーションファイアウォール）| AWS](https://aws.amazon.com/jp/waf/)
      - 特徴
        - 既存構成への影響なし
          - 既存のアーキテクチャを変更せずに展開し、TLS / SSL または DNS を構成する必要はありません
        - 簡単なデプロイとメンテナンス
          - AWS および AWSMarketplace のマネージドルール、すぐに使⽤できる CloudFormation テンプレート、組み込みの SQLi / XSS 検出
        - 柔軟にカスタマイズ可能なセキュリティ
          - 1 ミリ秒の遅延で着信要求の任意の部分を検査できる⾮常に柔軟なルールエンジン
        - サードパーティのルールは取り込むだけ
          - AWS WAF コンソール内で、AWS Marketplace にピボットして、業界をリードするセキュリティベンダールールを選択して AWS WAF に取り込むことが可能
      - AWS マネージドルール for AWS WAF
        - ⼀般的な攻撃ベクトルと脅威をカバー
        - SRT † によるキュレーションおよび保守
        - OWASP ‡ TOP 10 よりインフルエンスを受けて開発
        - 追加料⾦なし
      - AWS WAF に備わるボット緩和
    - AWS Shield Standard / Advanced
      - ⾃動化されたインテリジェントな検出による緩和
        - 独⾃の検出エンジンおよび緩和システムは、侵⼊のすべてのポイントで AWS を保護
        - 既知のイベントやボリューメトリック異常をブロックするための機械学習技術の広範な使⽤
        - トラフィックシェーピングは、最も疑わしいトラフィックを徐々にドロップし、誤遮断（False Positive)を抑制
        - アラートと軽減策がコンソールに表⽰され、シグナルが CloudWatch にプッシュ
- アプリケーションの要件に応じた設計の勘所
  - ⼀般的な Web アプリケーションの保護
  - サーバーレスアプリケーションの保護
  - 遅延にセンシティブなアプリケーションの保護
  - オンプレミスアプリケーションの保護
- 統制と運⽤をスケールさせる⽅法
  - [AWS Firewall Manager（ファイアウォールルールの一元管理）| AWS](https://aws.amazon.com/jp/firewall-manager/)
    - 組織（Organizations)全体で AWSNetwork Firewall、WAF、Shield、および VPC セキュリティグループを使⽤してベースラインセキュリティを⼀元的に有効
    - 新しいアプリケーションが作成された場合でも、⼀貫して保護を強制
    - AWS アカウント全体で境界防御の状況を⼀元的に可視化

</div></details>

### AWS 環境における脅威検知と対応(AWS-39)

Link: [AWS 環境における脅威検知と対応(AWS-39) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/AWS%20%E7%92%B0%E5%A2%83%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B%E8%84%85%E5%A8%81%E6%A4%9C%E7%9F%A5%E3%81%A8%E5%AF%BE%E5%BF%9C(AWS-39)/1_lk3uw22v/207422693>)

#### 視聴対象者

> - アマゾンウェブサービス (AWS) 環境のセキュリティ対策に関する設計・実装を管理する方
> - 組織のセキュリティ監視と運用を行い、インシデントの検知や対応を実施する方

#### ゴール

> - AWS サービスを用いて、高度な脅威検知とインシデント対応を実施方法を学び、組織全体のセキュリティ管理の仕方を理解する

### まとめ

> - 組織全体のセキュリティを保護するには
>   - 全ての AWS アカウントをセキュリティ監視対象とする
> - 脅威の検知、対応、調査を実施するには
>   - Amazon GuardDuty, AWS Security Hub, Amazon Detective など AWS で利用可能なセキュリティサービスを理解する
> - AWS セキュリティサービスを活用するには
>   - AWS マルチアカウント環境において、AWS セキュリティサービスを有効化するだけでスケーラブルに展開する

<details><summary>サマリ
</summary><div>

- AWS セキュリティサービスによる組織全体の保護
  - AWS アカウントの理解
    - AWS アカウントとは
      - AWS クラウドサービスのリソースコンテナ
      - 明示的なセキュリティ境界
      - コストの追跡と請求のためのコンテナ
      - 制限と閾値(サービスクォータや API 閾値など)を適用するメカニズム
  - セキュリティアカウントによる検知・対応・調査
- 変化する環境において脅威を検知するには (Amazon GuardDuty)
  - 脅威インテリジェンスと継続的監視により拡大していく AWS アカウントやリソースを効果的に保護
  - データソース
    - VPC フローログ
    - DNS ログ
    - CloudTrail イベント
    - S3 データイベント
  - Amazon GuardDuty が検出する既知の脅威
    - 様々なソースからの脅威インテリジェンスを活用
      - AWS 固有のインテリジェンス
      - AWS パートナーの脅威インテリジェンス（CrowdStrike, Proofpoint）
      - お客様が提供する脅威インテリジェンス
    - 脅威インテリジェンスを用いて GuardDuty が識別するもの
      - 既知のマルウェアに感染したホスト
      - 匿名プロキシ
      - マルウェアやハッカーツールをホストしているサイト
      - 暗号通貨マイニングプールとウォレット
  - Amazon GuardDuty が検出する未知の脅威
    - 異常な振る舞いを検出するためのアルゴリズム
      - ヒューリスティックのための信号パターンの検査
      - 正常なプロファイリングと偏差の確認
      - 機械学習による分類
- 検出結果の確認：Amazon EventBridge イベント
  - GuardDuty が送信する一つのイベントには、脅威を検出してから 5 分間の情報が集約される
  - EventBridge イベントは、グラフ化、保存、エクスポート、分析などに利用される
- 増大する脅威を監視し対応するには (AWS Security Hub)
  - 特徴
    - 調査結果を集約し時間を節約
    - 自動チェックでセキュリティ体制を改善
    - 厳選されたセキュリティベストプラクティス
    - 標準化された検出結果フォーマットとのシームレスな統合
    - マルチアカウントの統合
  - ユースケースの概要
    1. 検出結果の優先順位付けとアクションの実行:
       組織内の全ての AWS アカウントのセキュリティイベントを表示
    2. 統合と連携:
       正規化された共通形式で、イベントを SIEM またはログ管理ツールに送信
    3. 可視性:
       組織内の全ての AWS アカウントのコンプライアンス遵守状況を可視化
  - AWS セキュリティサービスとの統合
    - Amazon GuardDuty
      - 脅威検知に関する全ての検出結果
    - Amazon Inspector
      - セキュリティ評価による全ての検出結果
    - Amazon Macie
      - ポリシー違反時の検出結果
    - AWS IAM Access Analyzer
      - 自身のアカウント内のリソースに対して、外部からのアクセスを許可するポリシー記述を検出した時の検出結果
    - AWS Firewall Manager
      - AWS WAF ポリシーや Web ACL ルールのコンプライアンス非準拠時の検出結果
      - AWS Shield Advanced によりリソース保護されていない、または攻撃を検知した時の検出結果
    - AWS Systems Manager Patch Manager
      - EC2 インスタンスがパッチベースラインに基づくコンプライアンスルールに非準拠の時の検出結果
  - AWS Security Hub インサイト
    - グループ化条件(Group By)によってフィルターされる検出結果
  - セキュリティ評価の自動化
    - 150 以上のセキュリティ項目の継続的評価を自動化
    - 評価結果はダッシュボードに表示されすばやくアクセス可
    - コンプライアンス遵守に役立つベストプラクティス情報の提供
  - セキュリティ基準
    - AWS 基礎セキュリティのベストプラクティス
    - CIS AWS Foundations Benchmark
  - AWS ソリューション
    - [AWS Security Hub の自動化された応答と修復 | 実装 | AWS ソリューション](https://aws.amazon.com/jp/solutions/implementations/aws-security-hub-automated-response-and-remediation/)
- 対応すべき脅威を調査するには (Amazon Detective)
  - セキュリティ問題の根本原因を迅速に分析、調査、特定
    - Amazon Detective 利用フロー
    - マルチアカウントのテレメトリデータ収集
    - 継続的な集約とグラフモデルへの変換
    - セキュリティアカウントによる集中管理
      - Amazon GuardDuty, AWS Security Hub,Amazon Detective の権限移譲された管理アカウント (Delegated Administrator) にセキュリティアカウントを指定する
      - AWS Organizations 配下のメンバーアカウントに対して、Amazon GuardDuty,AWS Security Hub を一括自動有効化可能
      - Amazon Detective のメンバーアカウントは、セキュリティアカウントから招待することで関連付ける

</div></details>

### ISMAP に基づくクラウドコンプライアンスの向上(AWS-48)

Link: [ISMAP に基づくクラウドコンプライアンスの向上(AWS-48) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/ISMAP%20%E3%81%AB%E5%9F%BA%E3%81%A5%E3%81%8F%E3%82%AF%E3%83%A9%E3%82%A6%E3%83%89%E3%82%B3%E3%83%B3%E3%83%97%E3%83%A9%E3%82%A4%E3%82%A2%E3%83%B3%E3%82%B9%E3%81%AE%E5%90%91%E4%B8%8A(AWS-48)/1_453mvcej/207422693>)

#### 視聴対象者

政府情報システムのためのセキュリティ評価制度

（Information system Security Management and Assessment Program: 通称、ISMAP（イスマップ）)

> - ISMAP の調達に関連する担当者（調達府省庁などにおいてサービス選定に関わる方）
> - ISMAP を踏まえたサービスを展開しようとしている事業者（SaaS 事業者や SI 事業者のエンジニアや提案担当者）
> - クラウドにおける調達やコンプライアンスを考えているリスク管理部門やコンプライアンス担当者（監査人等）

### まとめ

> - コンプライアンスとクラウドバイデフォルト
>   - ISMAP はクラウドバイデフォルト実現のための道具
> - ISMAP がもたらすもの
>   - ISMAP によりクラウドサービス事業者のセキュリティに対する“信頼”を醸成
> - AWS の ISMAP コンプライアンスがお客様に届ける価値
>   - AWS の ISMAP コンプライアンスだけではなく必要な情報や機能提供による、エコシステム拡充を支援

<details><summary>サマリ
</summary><div>

- コンプライアンスとクラウドバイデフォルト
  - Ref. [政府情報システムにおけるクラウドサービスの利用に係る基本方針](https://cio.go.jp/sites/default/files/uploads/documents/cloud_policy_20210330.pdf)
  - Ref. [政府情報システムにおけるクラウドサービスの利用に係る基本方針（案）概要](https://www.kantei.go.jp/jp/singi/it2/cio/dai77/sankou.pdf)
  - クラウドサービスの利用を第一候補
    - 政府情報システムは、クラウドサービスの利用を第一候補として、その検討を行う（クラウドサービスにはパブリック及びプライベート（府省共通・府省内の提供する共通基盤等）を含める）
    - 情報システム化の対象となるサービス・業務、取扱う情報等を明確化した上で、メリット、開発の規模及び経費等を基に検討を行う
  - クラウドバイデフォルトは、オンプレミスを否定するものではない。
  - “選択”のために重要となるセキュリティ評価
    - クラウドバイデフォルトを実現する上での重要な要因は“セキュリティ”
    - 事業者を正しく評価するためには、第三者認証やバックアップや災害対策などのリスク対応が可能な点が選定ポイントに
    - パブリッククラウドのサービスによるプライベートクラウド環境の実現
- ISMAP がもたらすもの
  - “信頼”の基礎として ISMAP
    - Ref. [政府情報システムにおけるクラウドサービスのセキュリティ評価制度の基本的枠組みについて](https://www.nisc.go.jp/active/general/pdf/wakugumi2020.pdf)
    - “各政府機関は、クラウドサービスを調達する際は本制度において登録されたサービスから調達することを原則”
  - “言明”にもとづく説明責任
    - 管理基準は、ガバナンス基準、マネジメント基準、管理策基準の三要素から構成
    - ISO/IEC 27001, 27002, 27014, 27017,政府機関統一基準および NISTSP800-53 を参照して構成
    - クラウドサービス事業者は管理基準に対しての整備状況、運用状況を言明し、監査を通じて第三者評価を受ける
  - 制度への期待：コンプライアンスのこれから？
    - ISMAP の対象は府省庁における調達
    - [制度立ち上げ時の“特例”として、独立行政法人及び指定法人は含まない](https://www.ipa.go.jp/files/000082256.pdf)
    - [制度の設立において、公開される情報を踏まえた重要産業分野等をはじめとして民間での活用推進が期待されている](https://www.meti.go.jp/shingikai/mono_info_service/cloud_services/pdf/20200130_report.pdf)
  - ISMAP における“利用者”の責任
    - ISMAP の対象は“クラウドサービス事業者”だが、クラウドサービス利用者（SI 事業者による構築環境含む）の責任は必ず存在する
    - すべてがクラウドサービスではない（従来のようにクラウドサービス上にシステムインテグレーション型の調達を行うことも引き続き考えられる）
    - （SI 事業者にとって）サービス構築の提案においては、より自組織の提案が訴求する価値の深堀が差別化要因となる
- AWS の ISMAP コンプライアンスがお客様に届ける価値
  - お客様に必要なのは、価値の最大化
    - Ref. [政府情報システムにおけるクラウドサービスの利用に係る基本方針（案）概要](https://aws.amazon.com/jp/compliance/ismap)
    - **クラウド調達にとって最も大切なことは利用者の選択肢の拡充による、より柔軟かつ迅速な調達の実現**
    - AWS はお客様のフィードバックにもとづきサービスを継続的にアップデート
    - AWS のインフラストラクチャを活用することで、SaaS ベンダはより効率的に ISMAP に取り組むことが可能
  - クラウド事業者による情報や機能の提供
    - ISMAP の管理策には、利用者（AWS にとっては AWS 上にサービスを展開する SaaS 事業者等も該当）に対して、機能や情報を提供することを求める管理策が存在
    - 利用者は要件やニーズに応じて、適切な実装や運用を実現することが期待される（責任共有モデルの実現）
    - ISMAP のために特別なことではなく、様々なコンプライアンスプログラムと一貫性をもって実施
  - 調達の未来：より迅速なクラウド調達の実現へ
    - Ref. [速報！本日未明に終了した AWS 公共部門 サミットのハイライトを紹介します-前編【キーノート編】 | Amazon Web Services ブログ](https://aws.amazon.com/jp/blogs/news/aws-public-sector-washington-dc-summit-2020-highlight-keynote/)
    - 米国で開催されている公共部門サミットでは、Federal Risk and Authorization Management Program (FedRAMP)への AWS の取り組みを紹介
    - “AWS はパートナー各社との協力のもと、ユーザーのセキュリティ向上に注力
    - AWS には、他のどのクラウドプロバイダーよりも多くの「FedRAMP 認定ソリューション」が引き続き存在し、セキュリティ基盤の構築に取り組む各機関のミッションに貢献
    - 2020 年 5 月末時点で、110 ものサードパーティソリューションが AWS 上で FedRAMP 認証を取得

</div></details>

### 【基本の AWS サービス】AWS アカウントを守るためにおさえておきたいセキュリティ対策(AWS-52)

Link: [【基本の AWS サービス】AWS アカウントを守るためにおさえておきたいセキュリティ対策(AWS-52) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/%E3%80%90%E5%9F%BA%E6%9C%AC%E3%81%AE%20AWS%20%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%80%91AWS%20%E3%82%A2%E3%82%AB%E3%82%A6%E3%83%B3%E3%83%88%E3%82%92%E5%AE%88%E3%82%8B%E3%81%9F%E3%82%81%E3%81%AB%E3%81%8A%E3%81%95%E3%81%88%E3%81%A6%E3%81%8A%E3%81%8D%E3%81%9F%E3%81%84%E3%82%BB%E3%82%AD%E3%83%A5%E3%83%AA%E3%83%86%E3%82%A3%E5%AF%BE%E7%AD%96(AWS-52)/1_nrqk6uyt/207422693>)

#### 視聴対象者

> - AWS の基本的な概念は知っている方
> - AWS Well-Architected フレームワークをこれから学ぶ方
> - AWS アカウント保護に不安がある方

#### ゴール

> - このセッションを聞いたあと、自社のアカウントのセキュリティ対策をすぐに実施出来る
> - セキュリティにおける、AWS Well-Architected レビューが出来るようになること

### まとめ

> - AWS Well-Architected フレームワークとは
>   - 10 年以上の経験、数多くのお客様と作りあげたクラウド設計・運用のベストプラクティス集
> - AWS Well-Architected フレームワーク セキュリティの柱
>   - 10 個の質問とベストプラクティスを利用可能
> - AWS アカウント保護に有効なベストプラクティスをご紹介
>   - すぐに実践できるセキュリティ対策を検討する

<details><summary>サマリ
</summary><div>

- AWS Well-Architected フレームワークとは
  - システム設計・運用の”大局的な”考え方とベストプラクティス集
  - Ref. [AWS Well-Architected フレームワーク - AWS Well-Architected フレームワーク](https://docs.aws.amazon.com/ja_jp/wellarchitected/latest/framework/welcome.html)
- AWS Well-Architected フレームワーク セキュリティの柱について
- AWS Well-Architected フレームワークの活用
  - Well-Architected レビュー
    - Ref. [AWS Well-Architected レビュー (ISV)](https://aws.amazon.com/jp/partners/isv/well-architected-review/)
    - AWS Well-Architected フレームワークにおける各柱ごとの質問にワークロードを照らし合わせる作業のこと
    - 現在 (As is) のベストプラクティスの適用状況を理解し、将来 (To be) のアーキテクチャーを検討する
    - 設計、構築、運用等、ワークロードの特性が変わるタイミングで定期的に実施することを推奨
  - AWS Well-Architected Tool
    - Ref. [AWS Well-Architected Tool（アーキテクチャのレビューとベストプラクティス比較）| AWS](https://aws.amazon.com/jp/well-architected-tool/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc)
    - Well-Architected レビュー時に活用できる AWS のサービス
    - 各質問毎にベストプラクティスの適用状況を記録できる
    - レビューした結果をレポートとして出力でき、リスクレベルが可視化される
- AWS アカウントを守るために実施すべきセキュリティ対策
  - 一元化された ID プロバイダーを利用し、一時的な認証情報を使用する
    - AWS アカウントにおけるシングルサインオン
      - Ref. [AWS Single Sign-On（クラウドシングルサインオン (SSO) サービス）| AWS](https://aws.amazon.com/jp/single-sign-on/)
    - ID フェデレーション with SAML
      - Ref. [SAML 2.0 ベースのフェデレーションについて - AWS Identity and Access Management](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_roles_providers_saml.html)
  - 組織のアクセス許可ガードレールを定義する
    - 予防的ガードレール
      - 対象の操作を実施できないようにするガードレール
      - Organizations Service Control Policy （SCP）で実装する
        - AWS リソースや API へのアクセス制限を実現
    - 発見的ガードレール
      - 望ましくない操作を行なった場合、それを発見するガードレール
      - 管理しつつ開発のスピードを上げるために効果的
      - AWS Config Rules で実装する
        - 組織のルールから逸脱していないかどうかを評価
- AWS アカウント保護に必要なセキュリティ対策 ‐ AWS アカウント全般編 ‐
  - AWS アカウント保護における実施すべきベストプラクティス
    - 強力なサインインメカニズムを使用する
      - ルートユーザーの MFA を有効化し、アクセスキーを削除する
      - 管理権限を持つ IAM ユーザーに MFA を設定する
    - 一時的な認証情報を使用する
      - アクセスキーの代わりに IAM ロールを利用する
    - シークレットを安全に保存して使用する
      - [AWS Secrets Manager（シークレットのローテーション、管理、取得）| AWS](https://aws.amazon.com/jp/secrets-manager/)
      - [AWS Systems Manager パラメータストア - AWS Systems Manager](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-parameter-store.html)
    - 定期的に認証情報を監査およびローテーションする
      - AWS Config ルールによるパスワードポリシーとアクセスキーローテーションのチェック
  - AWS アカウント保護における実施すべきベストプラクティス
    - サービスとアプリケーションのログ記録を設定する
      - Ref. [AWS CloudTrail（ユーザーアクティビティと API 使用状況の追跡）| AWS](https://aws.amazon.com/jp/cloudtrail/)
      - Ref. [AWS Config（リソースのインベントリと変更の追跡）| AWS](https://aws.amazon.com/jp/config/)
    - ログ、結果、メトリクスを一元的に分析する
    - 検出結果に対してアクションをとる体制をつくる
      - 検出結果を生成
        - [Amazon Macie（機械学習による機密データの検出、分類、保護）| AWS](https://aws.amazon.com/jp/macie/)
          - 大規模な機密データを検出して保護する
        - [Amazon Inspector（アプリのセキュリティとコンプライアンスの改善をサポート）| AWS](https://aws.amazon.com/jp/inspector/)
          - 自動化されたセキュリティ評価サービスにより、 AWS にデプロイされたアプリケーションのセキュリティとコンプライアンスの改善をサポート
        - [AWS Config（リソースのインベントリと変更の追跡）| AWS](https://aws.amazon.com/jp/config/)
          - AWS リソースの設定を記録して評価
        - [Amazon GuardDuty（マネージド型脅威検出サービス）| AWS](https://aws.amazon.com/jp/guardduty/)
          - インテリジェントな脅威検出と継続的なモニタリングで AWS のアカウント、ワークロード、データを保護
        - [AWS IAM Access Analyzer を使用する - AWS Identity and Access Management](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/what-is-access-analyzer.html)
          - 論理ベースの推論を使用して AWS 環境内のリソースベースのポリシーを分析することにより、外部プリンシパルと共有されるリソースを識別します。
        - [AWS Firewall Manager（ファイアウォールルールの一元管理）| AWS](https://aws.amazon.com/jp/firewall-manager/)
          - 多くのアカウント、アプリケーションにわたって中央でファイアウォールを設定、管理
      - 検出結果を一元管理
        - [AWS Security Hub（統合されたセキュリティ & コンプライアンスセンター）| AWS](https://aws.amazon.com/jp/security-hub/?aws-security-hub-blogs.sort-by=item.additionalFields.createdDate&aws-security-hub-blogs.sort-order=desc)
          - セキュリティアラートの一元的な表示および管理を行い、セキュリティチェックを自動化する
      - 検出結果を通知
        - Ref. [Amazon CloudWatch Events を使用した GuardDuty 結果へのカスタムレスポンスの作成 - Amazon GuardDuty](https://docs.aws.amazon.com/ja_jp/guardduty/latest/ug/guardduty_findings_cloudwatch.html#guardduty_cloudwatch_severity_notification)

</div></details>

### AWS における安全な Web アプリケーションの作り方(AWS-55)

Link: [AWS における安全な Web アプリケーションの作り方(AWS-55) - Summits JP Production](<https://summits-japan.virtual.awsevents.com/media/AWS%20%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B%E5%AE%89%E5%85%A8%E3%81%AA%20Web%20%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E4%BD%9C%E3%82%8A%E6%96%B9(AWS-55)/1_ezyodv7a/207422693>)

#### 視聴対象者

> - Web アプリケーションエンジニア
> - 開発の責任者

#### ゴール

> - Web アプリケーションの脆弱性に対して AWS やサードパーティのサービスを使って対応する⽅法を知る
> - 開発のライフサイクルにおいて必要となるセキュリティの確認項⽬と対策項⽬を理解する

### まとめ

> - サービスにより責任の範囲は変化するが、常にアプリケーションのセキュリティ対策はお客様の責任範囲
> - Web アプリケーションのセキュリティガイドラインを読み返して脆弱性と対策を理解しましょう
> - アプリケーションの開発ライフサイクルにおいて必要となるセキュリティ対策が異なることを理解しましょう
> - 各開発フェーズにおけるセキュリティ対策のポイントを確認しましょう
>   1. 認証・認可とアクセス管理に注意
>   2. アプリケーションフレームワークを正しく利⽤
>   3. ログの取得とモニタリング
>   4. コードレビューの実施
>   5. セキュリティテストを実施
>   6. 必要に応じて WAF による追加的な保護を実施
>   7. 継続的なセキュリティテスト/脆弱性診断を実施

<details><summary>サマリ
</summary><div>

1. Web アプリケーションの脆弱性とセキュリティガイドライン
   - Web アプリケーション脆弱性と攻撃の代表例
     - SQL インジェクション
     - XSS
     - セッション管理の不備
     - アクセス制御や認可制御の⽋落
   - アプリケーションの保護はお客様の責任範囲
     - Ref. [AWS セキュリティのベストプラクティス](https://d1.awsstatic.com/whitepapers/ja_JP/Security/AWS_Security_Best_Practices.pdf)
   - 代表的なセキュリティガイドライン
     - [安全なウェブサイトの作り⽅](https://www.ipa.go.jp/files/000017316.pdf)
     - [OWASP Top 10](https://owasp.org/www-project-top-ten/)
       - そのほかの OWASP のガイドライン
         - [OWASP Serverless Top 10](https://owasp.org/www-project-serverless-top-10/)
         - [OWASP Top 10 Proactive Controls](https://owasp.org/www-project-proactive-controls/)
         - [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
         - [OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/index.html)
2. Web アプリケーションのセキュリティ対策
   - 要件定義
     - セキュリティ要件定義
     - リスク分析
   - システム設計
     - 認証⽅式の設計
     - ログ設計
     - セキュアプログラミング技法
   - 開発・実装
     - コーディング規約による制限
     - ソースコードレビュー
     - ソースコードセキュリティ検査
   - テスト・検証
     - セキュリティテスト
     - 脆弱性診断
   - 運⽤・保守
     - 脅威や脆弱性情報の収集
     - 定期的な脆弱性診断
3. 開発ライフサイクルとセキュリティ対策
   1. 認証・認可とアクセス管理に注意
      - 認証は利⽤者を確認することであり、認可はその利⽤者に特定のアクセス権限を与えること
      - AWS リソース⾃体に設定する認証・認可と、アプリケーションに実装するユーザーの認証・認可の機能に分けて考える
        - Amazon Cognito
        - SSO
        - IDaaS(Auth0, okta, onelogin, etc)
      - データベースへアクセスする認証情報や外部 API への認証情報の取り扱いに気をつける
        - [AWS Secrets Manager（シークレットのローテーション、管理、取得）| AWS](https://aws.amazon.com/jp/secrets-manager/)
        - [AWS Systems Manager パラメータストア - AWS Systems Manager](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-parameter-store.html)
   1. アプリケーションフレームワークを正しく利⽤
      - ガイドラインで指摘されているセキュリティ脆弱性に対する対策は、最新版のフレームワークやライブラリを正しく利⽤することで実施できる
      - 古いバージョンのフレームワークやライブラリは脆弱性を含むことがあるので必ず最新版を利⽤する
      - セッション管理などは独⾃実装せずに、フレームワークやライブラリで提供されている機能をフル活⽤する
      - サーバーレスのアプリケーションで Rest API を実装する際には Amazon API gateway を利⽤する
   1. ログの取得とモニタリング
      - ログの取得は発⾒的統制(問題が起こったことを検知して対策すること)の上で重要
      - ログの種類はアクセスログなどの AWS のサービスが出⼒するログとアプリケーション側で必要に応じて出⼒するログに分かれる
        - AWS CloudTrail
        - Elastic Load Balancing (ELB) アクセスログ
        - Amazon CloudFront アクセスログ
        - Amazon API Gateway
        - VPC Flow Logs
        - Amazon S3 バケット のアクセスログ
        - DNS Query Logs
      - フレームワークが出⼒するログは抑制せずにきちんと出⼒する
   1. コードレビューの実施
      - エンジニア同⼠のコードレビューで防げる脆弱性も多い
      - 必要に応じてコーディング規約を策定する
      - フレームワークに沿った正しい実装ができているかをチェックする
      - バグを減らすこともセキュリティ対策の⼀つ
      - Linter によるチェックも活⽤する
      - 必要に応じてソフトウェアを⽤いた静的アプリケーションセキュリティテスト (SAST) を活⽤する (次のセクションで詳しく説明)
   1. セキュリティテストを実施
      - アプリケーションのリリース・運⽤前にソフトウェアを⽤いたセキュリティテストを活⽤する
      - セキュリティテストには、ソースコードを実⾏せずに解析する静的アプリケーションセキュリティテスト (SAST) と実際にコードを実⾏して解析する動的アプリケーションセキュリティテスト (DAST) がある
      - SAST と DAST はテスト対象とテストの⽬的が異なるため⼀⻑⼀短があり、可能な限り両⽅を実施することが望ましい
        - 静的アプリケーションセキュリティテスト (SAST)
          - Amazon CodeGuru Reviewer Security Detector
          - GitHub Code Scanning によるセキュリティスキャン
        - 動的アプリケーションセキュリティテスト (DAST)
          - OWASP ZAP (Zed Attack Proxy)
        - 侵⼊テストについて、テストが許可されたサービス
          - Amazon EC2, NAT Gateway, Elastic Load Balancing
          - Amazon RDS
          - Amazon CloudFront
          - Amazon Aurora
          - Amazon API Gateway
          - AWS Lambda 及び Lambda Edge 関数
          - Amazon Lightsail
          - Amazon Elastic Beanstalk 環境
   1. 必要に応じて WAF による追加的な保護を実施
      - Ref. [Web Application Firewall 読本：IPA 独立行政法人 情報処理推進機構](https://www.ipa.go.jp/security/vuln/waf.html)
      - アプリケーションの実装において根本的なセキュリティ対策をした上で補⾜的に WAF を利⽤する
      - WAF は絶対的な防御⽅法ではなく、あくまで被害の緩和に有効な対策⽅法
      - WAF の導⼊が効果的な状況は限られる
      - [AWS WAF の AWS マネージドルール - AWS WAF、AWS Firewall Manager、AWS Shield Advanced](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/aws-managed-rule-groups.html)
   1. 継続的なセキュリティテスト/脆弱性診断を実施
      - アプリケーションは常に更新されるものであり⼀度のセキュリティテストだけでは不⼗分
      - アプリケーションが変更されるたびに繰り返しコードレビューとセキュリティテストを実施する
      - DevOps のパイプラインにセキュリティテストを組み込んだ DevSecOps も有効な⼿法の⼀つ

</div></details>

### AWS Lambda を攻撃してみた ~サーバレスのセキュリティの考え方~

Link: [AWS Lambda を攻撃してみた ~サーバレスのセキュリティの考え方~（パートナーセッション） - Summits JP Production](https://summits-japan.virtual.awsevents.com/media/AWS%20Lambda%20%E3%82%92%E6%94%BB%E6%92%83%E3%81%97%E3%81%A6%E3%81%BF%E3%81%9F%20~%E3%82%B5%E3%83%BC%E3%83%90%E3%83%AC%E3%82%B9%E3%81%AE%E3%82%BB%E3%82%AD%E3%83%A5%E3%83%AA%E3%83%86%E3%82%A3%E3%81%AE%E8%80%83%E3%81%88%E6%96%B9~%EF%BC%88%E3%83%91%E3%83%BC%E3%83%88%E3%83%8A%E3%83%BC%E3%82%BB%E3%83%83%E3%82%B7%E3%83%A7%E3%83%B3%EF%BC%89/1_2n9sztvo)

### まとめ

> - サーバレス（AWSLambda）でも責任共有モデルの考え方は重要
> - サーバレスの特徴に合わせて防御戦略をとる
>   - 稼働は短命、OS の脆弱性管理不要、自由度も限定的
> - サーバレスユーザが注意すべきはアプリケーションを狙う脅威
>   - 脆弱性からの侵害
>   - 過大な権限付与からの権限昇格
> - サーバレスでも基本のセキュリティ対策は有効
>   - FaaS 上で多層防御（脆弱性の検査、ロギング、可視化、データへのアクセス制御

<details><summary>サマリ
</summary><div>

1. セキュリティ専門企業から見たサーバレスの特徴
   - 「リスクが予想しにくい」ことが一番のリスク
     - **リスクの<発生頻度> と<影響度> が予測しにくいため『負けない設計』が重要**
     - サーバレスアーキテクチャは複雑系システムであるため、リスクを予測して対応するよりも、設計にセキュリティを組み込む方が有効
   - サーバレス環境でユーザは何を守るべきか？
     - アプリケーション
     - データ
     - 認証/認可
     - ログ
   - 【特徴 ①】イベントソースの種類が格段に多い
     - **攻撃対象が多い。WAF ではカバーしきれないレイヤーの保護が必要**
       - HTTP API
       - Queue
       - DB
       - IoT Device
   - 【特徴 ②】システム設計が複雑になる
     - **インシデントレスポンスのための可視化・検知が困難**
       - サーバに関わる業務を「意識する」必要がない
       - 言語に依存しない機能の連携が容易
       - スケーラビリティを前提とした設計が組める
       - アプリケーションが関数に分割される
       - 各関数がイベントをトリガーに指定タスクを実行
       - 出力は各関数の依存関係で定義される
   - 【特徴 ③】セキュリティテストだけでは不十分
     - **テストツールだけではなくアプリケーション防御ツールも必要**
2. 「攻撃ゼロ」ではなく「被害ゼロ」を目指す
   - 「サーバレス脅威 TOP12」「OWASP TOP10」両方でインジェクションが 1 位
     - Ref. [サーバーレスアプリケーションの最も危険なリスク 12 選 - Qiita](https://qiita.com/yuuhu04/items/ad38d6d35d358a90a60f)
   - 脆弱性を悪用する攻撃は公開事例の 1/4 を占める
   - 高度なスキルを持つ攻撃者を想定する
     - 標的型攻撃者を脅威モデルとして防御戦略を策定するべき
   - 侵入を前提としたセキュリティ設計が鉄則
     - サーバレス環境でも『各レイヤーでの防御』が効果大
   - 『内部を守る』ことが被害ゼロへの道
     - 改めて『RASP（Runtime Application Self-Protection）』の価値を考える
       - 企業内 NW は安全とみなす
       - FW の内外が信頼の基準
       - リクエストごとに検査
       - セキュリティの埋め込み改めて『RASP（Runtime Application Self-Protection）』の価値を考える
       - RASP とは、セキュリティをアプリケーションの機能の一部として組み込むことで、外部からアプリケーションに対する攻撃を検知・ブロックする保護手法
3. TrendMicroCloudOne™ のご紹介
4. AWS Lambda を狙った機密情報の搾取とその対策
   1. 脆弱性・設定不備等により Credential 流出
   2. AttachPolicy・PassRole で権限昇格
   3. 機密情報搾取・AWS アカウント乗っ取り等の AWS 環境の悪用

</div></details>
