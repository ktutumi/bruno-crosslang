# PROJECT KNOWLEDGE BASE

**Generated:** 2026-04-07 12:59
**Commit:** bd06ca5
**Branch:** master

## OVERVIEW

CrossLanguage社のウェブサイト翻訳サービス「Transer」のAPI検証用 Bruno ワークスペース。
管理者API・翻訳サーバーAPI・辞書管理・ログ統計管理・Elasticsearch管理などのエンドポイントをテストする。

## STRUCTURE

```
./
├── workspace.yml          # Bruno ワークスペース定義（コレクション一覧はここで管理）
├── environments/          # 環境別変数定義（local, stg, stg-v2, prod, SSHトンネル版, 5050）
└── collections/           # APIコレクション（11グループ）
    ├── 10 顧客API/        # 管理者用REST API（認証・パスワード変更・共通ヘッダなど）
    ├── 50 ログ・統計管理/ # アクセスログ・エラーログ・統計管理
    ├── 60 バッチ系/       # バッチAPI（Kintoneインポート・ドメインマップ・統計管理など）
    ├── 90 サイトコントローラー/ # サイトコントローラー管理（GetInfo・ブラウザエラー通知など）
    ├── A0 TS Server/      # 翻訳サーバー直接SOAP API（最大コレクション）→ 専用CLAUDE.md参照
    ├── Elasticsearch管理/ # ESインデックス操作（一覧・Reindex・ドキュメント数確認）
    ├── Login, Users/      # ユーザー管理API（ログイン込み）
    ├── USC/               # USC向けリクエスト
    ├── Users/             # ユーザーCRUD
    ├── アクセス統計 ダミーログ/ # ダミーログ生成用
    └── 問い合わせ検証/    # 問い合わせフォーム検証用（未コミット状態）
```

## WHERE TO LOOK

| タスク | 場所 |
|--------|------|
| 翻訳APIの直接テスト（SOAP） | `collections/A0 TS Server/` |
| 顧客管理API（認証・設定） | `collections/10 顧客API/00_共通系API/` |
| Elasticsearchインデックス操作 | `collections/Elasticsearch管理/` |
| アクセスログ・エラーログ確認 | `collections/50 ログ・統計管理/` |
| 新しい環境の追加 | `environments/` に `.yml` を追加し `workspace.yml` の `activeEnvironmentUid` を設定 |
| コレクション登録 | `workspace.yml` の `collections` リストに追加 |

## ENVIRONMENTS

| 環境名 | url | ts_host | 用途 |
|--------|-----|---------|------|
| local | `https://api.local-transer.test/api/v1` | `http://localhost:12001` | ローカル開発 |
| 5050 | `http://localhost:5050/api/v1` | `http://localhost:12001` | ローカル開発（ポート5050） |
| stg | `https://stg-athp.transer.com/api/v1` | `http://hp-ts-dev02.transer.com:12000` | ステージング |
| stg-v2 | `https://stg2-athp.transer.com/api/v1` | `http://hp-ts-dev02.transer.com:12000` | ステージング v2 |
| stg-ssh-tunnel | stg URL + SSH経由 ts_host | `http://localhost:12001` | SSHトンネル経由でSTGのTS Serverを叩く |
| prod | `https://hpadmin.transer.com/api/v1` | `http://210.140.74.149:12000` | 本番 |
| prod-ssh-tunnel | `https://localhost:20443/api/v1` | `http://localhost:12002` | SSHトンネル経由で本番を叩く |

**環境変数**:
- `{{url}}` — 管理者REST API のベースURL
- `{{ts_host}}` — 翻訳サーバー（TS Server）のホスト（SOAPエンドポイント：`/clsoap/clsoap.cgi`）
- `{{es_url}}` — Elasticsearch のベースURL

## FILE FORMAT

このワークスペースは `.bru` 形式**ではなく** YAML形式（`.yml`）。Bruno の YAML API スキーマ `opencollection: 1.0.0` を使用。

各リクエストファイルの構造:
```yaml
info:
  name: <リクエスト名>
  type: http
  seq: <順序番号>
http:
  method: POST/GET/...
  url: "{{url}}/endpoint"
  body:
    type: json|text|form-data|...
    data: ...
```

フォルダーは `folder.yml`（`type: folder`）で定義。コレクションは `opencollection.yml` で定義。

## CONVENTIONS

- ファイル名の命名: `WT-API-<番号> <機能名>.yml`（API仕様番号に対応）
- 本番向けリクエストは `[本番]` プレフィックスまたは `本番/` サブディレクトリに格納
- 未実装・未検証のリクエストは `[未]` プレフィックス
- コピーリクエストは `Copy` サフィックス
- `seq` 番号でBruno上の表示順を制御

## ANTI-PATTERNS

- `workspace.yml` の `collections` リストに追加せずにコレクションディレクトリだけ作らない（Brunoが認識しない）
- `environments/` に追加しただけで `activeEnvironmentUid` を更新しない（選択されない）
- 本番データを含むリクエストを `[本番]` プレフィックスなしでルートに置かない

## NOTES

- `問い合わせ検証/` 配下の2ファイルは未コミット（git status で untracked）
- `A0 TS Server` が最大コレクション（29ファイル + 辞書/辞書ブリッジ/cross-biz/本番 の4サブディレクトリ）
- TS ServerのSOAP APIは `{{ts_host}}/clsoap/clsoap.cgi` に POST
- `laplus` サブディレクトリは翻訳エンジン「La Plus」用 JP-ES（日英）翻訳
