# A0 TS Server Collection

**Generated:** 2026-04-07 12:59

## OVERVIEW

翻訳サーバー（TS Server）の SOAP API を直接テストするコレクション。最大規模（ルート29ファイル + 4サブディレクトリ）。

## STRUCTURE

```
A0 TS Server/
├── Translate *.yml        # SOAP翻訳API（HTML/CSS/XML/JS/Text）
├── TranslateMulti HTML.yml # 複数HTML一括翻訳
├── Get Engine Info.yml    # エンジン情報取得
├── Get User.yml           # TSユーザー情報取得
├── SplitSentences.yml     # 文分割API
├── ParseText.yml          # テキスト解析API
├── テストEcho.yml         # 疎通確認
├── 翻訳メモリ Translate.yml # 翻訳メモリ経由の翻訳
├── 辞書/                  # 辞書CRUD（AddEntry, GetEntry, DeleteEntry, Import, Export）
├── 辞書 ブリッジ/         # 辞書ブリッジ経由（多言語: en/fr + 本番確認）
├── cross-biz/             # クロスビズ向けリクエスト
├── laplus/                # La Plus エンジン（JP-ES翻訳）
│   └── JP-ES/
└── 本番/                  # 本番データを使った検証（大容量ファイルあり）
```

## WHERE TO LOOK

| タスク | 場所 |
|--------|------|
| HTMLページ翻訳テスト | `Translate HTML *.yml` |
| 辞書エントリ追加・削除 | `辞書/AddDictionaryEntry.yml`, `辞書/DeleteDictionaryEntry.yml` |
| 辞書の一括インポート | `辞書/DictionaryImport.yml`（14KB、大きなリクエストボディ） |
| 翻訳メモリの動作確認 | `翻訳メモリ *.yml` |
| 本番環境での大容量HTML検証 | `本番/[本番]Translate HTML hpedit.yml`（105KB） |
| 多言語辞書ブリッジ確認 | `辞書 ブリッジ/` |

## CONVENTIONS

- SOAP エンドポイント: `{{ts_host}}/clsoap/clsoap.cgi` （POST）
- リクエストボディは `type: text` で生のSOAP XMLを記述
- ライセンスキーはリクエスト内に直接記述（環境変数化されていない）
- 本番検証リクエストは `本番/` サブディレクトリまたは `[本番]` プレフィックス
- `辞書 ブリッジ/` の命名規則: `add-01.`, `del-01.` など順序プレフィックス

## ANTI-PATTERNS

- 本番データ（実際のHTMLコンテンツ等）を `本番/` 以外に置かない
- `DictionaryImport.yml` はボディが大きいため、不用意に本番で実行しない
- `[本番]TranslateMulti HTML Copy.yml` は191KB — ファイルサイズに注意

## NOTES

- `DELETE User (STGで実行済み).yml` は実行済みの履歴として残存
- `Get Engine Info Copy.yml`, `SplitSentences Copy.yml` などコピーリクエストが複数存在（整理対象）
- `laplus/JP-ES/` は La Plus エンジン向け（通常のTranserエンジンとは別）
