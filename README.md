# 気候変動下における主要穀物の収量変動リスクの空間的評価

技術固定シナリオと機械学習（XGBoost）を用いて、米・トウモロコシ・小麦の収量リスクと気候ホットスポットを評価する研究用リポジトリです。論文原稿（LaTeX）、再現・探索用の Jupyter ノートブック、中間・最終結果の CSV、および補助スクリプトを含みます。

## 概要

- **目的変数:** FAOSTAT に基づく各国・各作物の単位面積当たり収量（1961–2013 年など、ノートブック・稿に準拠）
- **説明変数:** World Bank Group Climate Change Knowledge Portal の気候指標（例: ACCESS-CM2、TXX、GSL、SPEI、相対湿度 Hurs）および技術の代理変数としての年次（Year）
- **モデル:** XGBoost（ホールドアウト＋交差検証、ハイパーパラメータ探索）
- **将来側面:** 2100 年付近の気候入力は 2096–2099 年の 4 年平均などで平滑化（稿・特徴量 CSV のメタ情報に準拠）。シナリオ例: SSP2-4.5 / SSP3-7.0
- **政策・食料視点:** 実効供給補正係数（$\alpha$）により重量から利用可能カロリーへ統一し、ISI-MIP 等との比較枠組みを稿で整理

外挿や技術固定の解釈は論文（`paper_corrected_red.tex`）の記述に従ってください。

## リポジトリ構成（主要ファイル）

| 種別 | パス | 内容 |
|------|------|------|
| 論文 | `paper_corrected_red.tex` | 和文論文（LuaLaTeX 想定）。修正箇所は `\red{...}` で赤字表示 |
| 図表・評価ノート | `paper_evaluation_summary.ipynb`, `paper_table_fig_one_row.ipynb` | 稿用の表・図の整理 |
| データ読込・学習 | `load_regressionDF.ipynb`, `load_regressionDF_simple.ipynb` | 回帰用データの構築と XGBoost 学習まわり |
| モデル・閾値 | `model_and_region_thresholds.ipynb`, `regression_model9.ipynb` | 地域・閾値・モデル設定の分析 |
| 低精度国 | `low_accuracy_countries_analysis.ipynb` | 適合精度が低い国のクラスタリング（K-means 等） |
| リスク・ホットスポット | `climate_yield_risk_hotspots.ipynb` | 気候・収量リスクの空間的整理 |
| 2100 予測・不確実性 | `predict_2100_bootstrap.ipynb`, `predict_2100_model_shap.ipynb` | ブートストラップ要約、SHAP 値の出力・可視化 |
| 最適作物・マップ | `optimal_crop_maps.ipynb`, `optimal_crop_maps_rice088.ipynb`, `optimal_crop_comparison_2013_vs_2100.ipynb` | シナリオ別の作物比較・地図系 |
| 結果サマリ | `results/*.csv` | 作物別など集計済み結果 |
| 特徴量・閾値 CSV | `future_2100_features_*.csv`, `thresholds_*.csv`, `*_yield_thresholds.csv` 等 | 将来入力・閾値・ホットスポット用の表データ |
| SHAP | `shap_values_train2008_model.csv`, `shap_values_train_predict2100_model.csv` | モデル解釈用の出力 |
| 補助 | `extract_docx_text.py` | `.docx` から本文テキストを抽出（ノートブック用） |
| メモ | `calorie_conversion_notes.md`, `continuous_cropping_supply_coefficients.md`, `map_comparison_newplot_news.md` 等 | 係数・図のメモ |

想定質問・発表用メモは `想定質問_*.md` および `想定質問と回答_2100年食料安全保障.md` にあります。

## 環境・依存関係（Python）

ノートブックから読み取れる主なパッケージ例:

- `pandas`, `numpy`, `scipy`
- `xgboost`
- `scikit-learn`
- `plotly`（図のインタラクティブ表示）

仮想環境を作成し、必要に応じて上記をインストールしてください。バージョン固定が必要な場合は、実行環境から `pip freeze` 等で `requirements.txt` を生成することを推奨します。

```bash
python -m venv .venv
.venv\Scripts\activate
pip install pandas numpy scipy xgboost scikit-learn plotly jupyter
```

## Jupyter の起動

```bash
jupyter notebook
# または
jupyter lab
```

各ノートブックは**入力 CSV のパス**がローカル環境向けになっている場合があります。他マシンで開くときは、データの置き場所に合わせてパスを修正してください。

## 論文 PDF のビルド（LuaLaTeX）

`paper_corrected_red.tex` 先頭コメントのとおり、LuaLaTeX + `luatexja` を想定しています。

```bash
lualatex -synctex=1 -outdir=out paper_corrected_red.tex
```

図は**絶対パス**で `includegraphics` されている箇所があります。リポジトリをクローンした場合は、図ファイル（例: `low_accurate.png` 等）を同梱するか、TeX 内のパスを相対パスへ一括置換してください。

赤字を印刷用に消す場合は、稿内の `\newcommand{\red}[1]{...}` をコメントの指示どおり変更します。

## 補助スクリプト: Word からテキスト抽出

```bash
python extract_docx_text.py "path\to\document.docx"
```

未指定時はスクリプト内のデフォルトパスが使われます。出力はリポジトリ直下の `docx_extracted.txt` です。

## データ・利用上の注意

- FAOSTAT、World Bank Group のデータは**各提供元の利用条件**に従ってください。
- 大容量や個人情報を含むファイルを公開リポジトリに載せる場合は、`.gitignore` と Git LFS の利用を検討してください。
- 本リポジトリの成果は研究・教育目的の再現・追試を想定しています。政策判断は必ず一次データと最新文献で検証してください。

## 引用

公開論文が確定したら、ここに DOI・書誌情報を追記してください。

## ライセンス

未設定の場合は、リポジトリ公開前に `LICENSE` を追加し、データ・図の再配布可否を整理してください。
