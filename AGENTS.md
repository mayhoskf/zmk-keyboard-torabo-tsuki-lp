# Repository Guidelines

## プロジェクト構成

このリポジトリは、分割キーボード torabo-tsuki LP 用の ZMK ファームウェアを提供します。
シールド定義、Kconfig、Devicetree オーバーレイ、既定のキーマップは `boards/shields/torabo_tsuki_lp/` にあります。
再利用可能な機能構成は `snippets/`（例: `input-trackball/`、`split-central/`）、ボード固有の C 実装は `src/` に配置します。
`config/` にはユーザー向けキーマップと West マニフェストがあります。
ビルド対象の正式な一覧は `build.yaml` で管理し、`.github/workflows/build.yml` が CI でこの一覧をビルドします。

## ビルド・テスト・開発コマンド

このモジュールを含む ZMK/West ワークスペースから実行してください。

```powershell
west update
west build -s zmk/app -b bmp_boost -- -DSHIELD=torabo_tsuki_lp_left -DSNIPPET="studio-rpc-usb-uart split-central input-trackball input-listener" -DZMK_CONFIG="$PWD/config"
west build -t pristine
```

`west update` は `config/west.yml` で固定されたリビジョンを取得します。
上記のビルド例は左側をセントラルにした構成です。別構成を確認するときは、`build.yaml` の各エントリを省略せず指定してください。
Kconfig、シールド、snippet の変更後にキャッシュの影響が疑われる場合は `west build -t pristine` を実行します。
push と pull request では GitHub Actions の全ビルド対象が検証されます。

## コーディング規約と命名

既存の Zephyr/ZMK の書式に合わせます。
C はスペース 4 個でインデントし、関数・変数には `snake_case`、設定シンボルには大文字の `CONFIG_*` を使用します。
関数や制御文の開始波括弧は同じ行に置いてください。
シールド名と snippet 名は、既存ディレクトリに合わせて小文字のハイフンまたはアンダースコアを使います。
ソースの SPDX ヘッダーは維持し、YAML はスペース 2 個でインデントします。
関係のないキーマップやマニフェストの整形は避けてください。

## テスト方針

独立したユニットテストはありません。
変更が影響する `build.yaml` の全構成が正常にコンパイルできることを必須とします。
入力デバイスを変更した場合は、左右両方のセントラル構成と、該当する double-ball 構成を確認してください。
実機ではペアリング、キー入力、ポインター、スクロール、左右分割の再接続を検証し、結果を pull request に記載します。

## コミットと Pull Request

コミット件名は、履歴に合わせて短く、命令形で、変更を一つに絞ります（例: `Add zmk-driver-iqs7211e to west.yml manifest`）。
英語と日本語のどちらでも構いません。
依存リビジョンの更新と挙動変更は別コミットにしてください。
Pull request には、対象ハードウェアと構成、実施したビルド・実機確認、関連 issue を記載します。
生成される UF2 の artifact 名が変わる場合は明記し、keymap-editor や ZMK Studio の見た目が変わる場合のみスクリーンショットを添付してください。
