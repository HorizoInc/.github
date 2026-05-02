 # Time-gated Raman Manual Gate Comparison Report App

**Version:** v1.0 / Streamlit local app  
**Use:** Internal / demonstration use  
**Prepared for:** Photontrace / HORIZO Inc.

---

## 概要

本アプリは、Time-gated Raman分光器から出力されたCSVデータを読み込み、任意の時間ゲート範囲を指定してスペクトルを抽出・比較し、PDFレポートを生成するためのローカル解析アプリです。

Time profileに基づく自動初期ゲート推定、手動ゲート調整、ALSベースライン補正、Savitzky-Golay smoothing、SNV / Area正規化比較、Gate scan heatmap表示に対応しています。

本アプリは、時間ゲートを自動的に確定するものではなく、解析者がTime profile、スペクトル比較、Heatmapを確認しながら、妥当な時間ゲート範囲を判断するための支援ツールです。

---

## 主な機能

### 1. Time-gated Raman CSVデータの読み込み

2つのCSVファイルを `Sample A` / `Sample B` として読み込み、同一または異なる時間ゲート条件で比較できます。

同じCSVを `Sample A` / `Sample B` の両方に指定することで、同一測定データに対して、時間ゲート幅やベースライン補正条件のみを変えた比較も可能です。

---

### 2. Time profileに基づく初期ゲート推定

指定した波数範囲の信号強度からTime profileを作成し、立ち上がり領域を基準に初期ゲート範囲を自動推定します。

自動推定されたゲートは、解析者が画面上で手動調整できます。

| 項目 | 内容 |
|---|---|
| `Time profile signal window` | Time profileおよび自動ゲート推定に使用する波数範囲 |
| `Output wavenumber range` | 最終的なスペクトル表示・PDF出力に使用する波数範囲 |

---

### 3. 手動ゲート指定・A/B比較

`Sample A` / `Sample B` に対して、以下の条件を個別または共通で設定できます。

- Manual gate range
- Baseline correction
- Savitzky-Golay smoothing

これにより、以下の比較が可能です。

- 異なるサンプル間の比較
- 同一CSVに対する異なる時間ゲート範囲の比較
- 同一CSVに対するベースライン補正条件の比較
- ゲート幅・ゲート位置によるスペクトル変化の確認

---

### 4. ベースライン補正

以下のALSベースライン補正条件を選択できます。

| 設定 | 内容 |
|---|---|
| `none` | ベースライン補正なし |
| `medium` | 標準的なALS補正 |
| `strong` | 強めのALS補正 |

通常は `medium` を基準とし、蛍光バックグラウンドが強い場合やベースラインのうねりが大きい場合に `strong` を確認用として使用します。

`strong` はブロードな成分やピーク形状に影響する可能性があるため、最終判断では `none` または `medium` の結果も併せて確認します。

---

### 5. Savitzky-Golay smoothing

スペクトルのギザギザやノイズが目立つ場合に、Savitzky-Golay smoothingをオプションで適用できます。

| 項目 | 内容 |
|---|---|
| 用途 | 確認用・表示改善用 |
| 推奨初期設定 | `window=7`, `polyorder=2` |
| PDF表記 | 使用した場合は条件欄に明記 |

Smoothingはピーク形状に影響する可能性があるため、重要なピーク判断では、smoothing OFFのスペクトルも確認することを推奨します。

---

### 6. SNV / Area正規化スペクトル比較

抽出したゲート平均スペクトルに対して、以下の2種類の正規化結果を表示します。

| 正規化 | 用途 |
|---|---|
| SNV | スペクトル形状の比較 |
| Area normalization | 全体強度差を抑えた比較 |

PDFレポート2ページ目には、SNV比較とArea正規化比較を表示します。

---

### 7. Gate scan heatmap

固定幅の時間ゲートを時間方向にスライドさせ、各ゲート範囲で平均スペクトルを作成します。

例：

- Gate width: `0.50 ns`
- Gate step: `0.20 ns`

その結果をHeatmapとして表示します。

| 軸 | 内容 |
|---|---|
| X軸 | Wavenumber / Raman shift |
| Y軸 | Gate center time |
| 色 | Intensity |

これにより、どの時間ゲート範囲で目的ピークが明瞭になるか、蛍光バックグラウンドや遅延成分の影響がどの時間帯で強くなるかを視覚的に確認できます。

現在選択中のManual gate範囲もHeatmap上に表示されます。

---

### 8. PDFレポート生成

A4 PDFレポートを自動生成します。

#### 1ページ目

- Report title
- Photontraceロゴ
- Customer logo
- Method overview
- Customer company
- Technical contact
- Prepared by
- Notes
- Sample A / Sample B 条件
- Manual gate範囲
- Actual selected time points
- Baseline correction条件
- Savitzky-Golay smoothing条件
- Time profile signal window
- Output wavenumber range
- Reference spectrum
- Time profile

#### 2ページ目

- SNV正規化スペクトル比較
- Area正規化スペクトル比較
- Manual gate情報

2ページ目のスペクトル表示は、Gate scan heatmapとの比較がしやすいよう、波数軸の表示幅をHeatmapに近づけています。

#### 3ページ目

- Gate scan heatmap
- 選択中Manual gate範囲の表示
- Gate center time軸
- Intensityカラーバー

---

## 現状の制限事項

本アプリは、解析者の判断を支援するためのローカル解析アプリです。  
時間ゲートや補正条件を完全に自動決定するものではありません。

現時点では、以下の点に注意が必要です。

- 自動ゲート推定は初期値提案であり、最終判断は解析者が行う
- Smoothing使用時はピーク形状への影響を確認する必要がある
- ALS strongではブロードな成分や弱いピークが影響を受ける可能性がある
- Heatmap上の明るい領域がすべてラマンピークとは限らない
- 広い背景成分、蛍光、補正影響、低波数側の傾きなどを含む可能性がある
- 入力CSVの形式はTime-gated Raman出力データを前提とする

今後、測定データの蓄積に応じて、自動ゲート推定条件、Heatmap表示、ピーク強度トラッキング、既知ピーク位置表示などを改善予定です。

---

## 想定用途

- Time-gated Ramanデータの迅速な評価
- 手動ゲート範囲の検討
- ゲート位置・ゲート幅によるスペクトル変化の確認
- 蛍光バックグラウンドの時間依存性確認
- 受託測定・PoCデータのレポート作成
- 顧客説明用のPDFレポート作成
- 解析条件の記録・共有
- 同一CSVに対する補正条件・ゲート条件の比較

---

## 基本操作

### 1. CSVファイルを選択

`Sample A` / `Sample B` にCSVファイルを指定します。

- `data/raw` から選択
- CSVをアップロード

同じCSVをA/B両方に指定することも可能です。

---

### 2. レポート情報を入力

- Customer company
- Technical contact
- Prepared by
- Notes
- Customer logo

Customer logoをアップロードした場合、PDF 1ページ目に反映されます。

---

### 3. 波数範囲を設定

| 項目 | 内容 |
|---|---|
| Output wavenumber range | 最終スペクトル表示・PDF出力に使う範囲 |
| Time profile signal window | Time profileおよび自動ゲート推定に使う範囲 |

---

### 4. 時間ゲートを確認・調整

自動推定された初期ゲートを確認し、必要に応じて手動で調整します。

---

### 5. 補正条件を選択

| 項目 | 選択肢 |
|---|---|
| Baseline correction | `none` / `medium` / `strong` |
| Savitzky-Golay smoothing | 必要に応じてON / OFF |

---

### 6. 解析実行

`Run analysis` をクリックします。

画面上で以下を確認します。

- Reference spectrum
- Time profile
- SNV comparison
- Area comparison
- Gate scan heatmap

---

### 7. PDFレポート出力

解析完了後、PDFレポートをダウンロードします。

必要に応じて `Also save PDF to outputs/report` を有効にすると、ローカルフォルダにも保存できます。

---

## 開発メモ

本アプリは、PhotontraceにおけるTime-gated Raman受託測定、PoC、デモ測定後の解析、顧客向けレポート作成を効率化する目的で開発しました。

初期構想ではSNR指標に基づく時間ゲート探索を想定していましたが、現在のバージョンでは、SNR最大値による自動最適化ではなく、Time profile、手動ゲート、補正条件比較、Gate scan heatmapを組み合わせて、解析者が妥当な時間ゲートを判断する設計に変更しています。

したがって、本アプリは「自動決定ツール」ではなく、**解析者の判断を支援するための評価・可視化・レポート作成ツール**です。

特にTime-gated Ramanでは、SNR最大値が常に最適なゲート範囲を意味するとは限りません。ピークの明瞭性、背景成分、蛍光の時間変化、補正条件の影響を総合的に確認することが重要です。

---

## Short description

```text
Time-gated Raman Manual Gate Comparison Report App

This Streamlit local app supports manual time-gate selection, optional auto-estimated initial gates, ALS baseline correction, optional Savitzky-Golay smoothing, SNV/Area normalization, gate scan heatmap visualization, and PDF report generation for Time-gated Raman CSV data.

The tool is designed for Photontrace internal and demonstration use, supporting feasibility evaluation, PoC reporting, and comparison of time-gate and preprocessing conditions. It is not an automatic gate-decision tool; it provides visual and quantitative support for expert interpretation.
```

---

## Japanese short description

本アプリは、Time-gated Raman CSVデータに対して、手動時間ゲート指定、自動初期ゲート推定、ALSベースライン補正、Savitzky-Golay smoothing、SNV/Area正規化、Gate scan heatmap表示、PDFレポート生成を行うStreamlitローカルアプリです。

Photontraceの受託測定、PoC、デモ測定後の解析支援を目的としており、時間ゲートを自動決定するものではなく、解析者がTime profile、スペクトル比較、Heatmapを確認しながら妥当な条件を判断するための補助ツールです。
