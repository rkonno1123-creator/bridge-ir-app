# 橋梁赤外線解析システム

橋梁コンクリートの赤外線サーモグラフィ調査用Webアプリケーション

## 概要

図面上にピンを配置し、各ポイントの可視画像・赤外線画像・8色画像を管理。
解析結果（判定・コメント）を記録し、PDFレポートを出力できる。

## 機能一覧

### プロジェクト管理
- プロジェクト切り替え（橋梁ごとに分離）
- 新規プロジェクト追加
- 初期プロジェクト：中根高架橋、ドミニカ高架橋、インダス川橋、眼鏡橋、星降り大橋

### 図面管理
- 図面アップロード（画像ファイル）
- 複数図面の切り替え
- 図面削除

### ピン管理
- 編集モードでピン追加（図面クリック）
- ピン移動（編集モードでドラッグ）
- ピン削除
- ピン名自動取得（画像ファイル名から）

### 画像管理
- 可視画像（VB）アップロード
- 赤外線画像（IR）アップロード
- 8色画像アップロード
- 複数画像一括アップロード＆自動振り分け
  - ファイル名に `VB` → 可視画像
  - ファイル名に `IR` → 赤外線画像
  - ファイル名に `(8)` or `_8` → 8色画像

### IR範囲描画
- 可視画像上に赤外線撮影範囲を矩形で描画
- 範囲の保存・削除

### 解析機能
- 判定：健全部 / 観察 / 浮きの可能性あり
- 判定フロー：第一〜第三条件
- コメント自動生成（判定×フローの組み合わせ）
- 追加コメント入力
- GSD自動計算（撮影距離から）
- 解析完了フラグ

### ピンの色分け
- 解析完了かつ健全部 → 緑
- 解析完了かつ観察 → 黄
- 解析完了かつ浮き → 赤
- 未解析 → グレー

### PDFエクスポート
- ピン選択（全選択/全解除）
- 番号順にソート
- 1ページ1ピンのレポート生成
- ブラウザ印刷機能でPDF保存

## 使い方

### 基本操作
1. プロジェクトを選択
2. 「＋ 図面追加」で図面をアップロード
3. 「📍 ピン編集」をクリックして編集モードON
4. 図面上をクリックしてピン追加
5. ピンをクリックして詳細パネルを開く
6. 画像をアップロード、判定を入力
7. 「解析完了にする」で確定

### 画像の一括アップロード
1. ピンを選択
2. 可視タブの「🎯 複数画像を自動振り分け」をクリック
3. VB/IR/8色の3枚を選択
4. 自動でタブに振り分けられる

### PDFエクスポート
1. 「📄 PDFエクスポート」をクリック
2. 出力するピンにチェック
3. 「PDFを生成」をクリック
4. 印刷ダイアログで「PDFとして保存」

## ファイル構成

```
.projects/bridge-ir-app/
├── index.html    ← メインファイル（これだけでOK）
└── README.md     ← このファイル
```

## 技術スタック

- HTML/CSS/JavaScript（Vanilla）
- Firebase Firestore（データベース）
- Firebase Storage（画像保存）
- Vercel（ホスティング）

## Firebase設定

- プロジェクト名：`infrared-cameramera`
- Firestore コレクション：
  - `projects` - プロジェクト情報
  - `drawings` - 図面情報
  - `pins` - ピン情報（画像URL、判定、コメント等）
- Storage：`drawings/`, `pins/` フォルダに画像保存

## デプロイ方法

### 初回セットアップ
```bash
# プロジェクトフォルダで
git init
git add .
git commit -m "first commit"

# GitHubにリポジトリ作成後
git remote add origin https://github.com/ユーザー名/bridge-ir-app.git
git branch -M main
git push -u origin main
```

### Vercel連携
1. https://vercel.com/dashboard を開く
2. 「Add New...」→「Project」
3. 「Import Git Repository」で `bridge-ir-app` を選択
4. そのまま「Deploy」

### 更新時
```bash
git add .
git commit -m "更新内容"
git push
```
→ Vercelが自動でデプロイ

## データ構造

### Firestore: pins コレクション
```
{
  drawingId: "図面のID",
  x: 50,              // X座標（%）
  y: 30,              // Y座標（%）
  name: "T00487",     // ピン名/写真番号
  images: {
    visible: { url: "...", filename: "..." },
    infrared: { url: "...", filename: "..." },
    color8: { url: "...", filename: "..." }
  },
  judgment: "healthy",      // healthy/observe/floating
  judgmentFlow: "flow2",    // flow1/flow2/flow3
  comment: "解析コメント",
  additionalComment: "追加コメント",
  distance: 8,              // 撮影距離(m)
  analyzed: true,           // 解析完了フラグ
  thermalArea: {            // IR範囲
    startX, startY, endX, endY
  }
}
```

## 今後の拡張予定

- [ ] 8色画像のトリミング機能
- [ ] 画像自動マッチング（フォルダ指定→一括紐づけ）
- [ ] Word形式でのレポート出力
- [ ] プロジェクト削除機能
- [ ] 図面名の編集機能

## 制限事項

- 1図面あたり100ピン程度を想定
- 5-6図面で500-600ピン程度なら問題なし
- それ以上はプロジェクト分割を推奨

## 作成

2024年12月 - Claude（うにちゃん）と共同開発
