# kaggle-SpaceshipTitnic

## 1. コンペ進め方（ステップバイステップ）
※固定化/自動化できる部分があれば適宜改善していきたい

① コンペ内容の理解

データ概要・評価指標（MAE）を理解。回帰タスク。
- train.csv : トレーニング データとして使用される、乗客の約 3 分の 2 (約 8700 人) の個人記録。
  - PassengerId : 各乗客に固有のID。各IDは という形式をとり、 はgggg_pp乗客ggggが同行するグループ、ppはグループ内での乗客の番号を表します。グループのメンバーは多くの場合家族ですが、必ずしもそうとは限りません。
  - HomePlanet : 乗客が出発した惑星。通常は永住している惑星。
  - CryoSleep : 乗客が航海期間中、仮死状態を選択したかどうかを示します。冷凍睡眠中の乗客は客室内に留まります。
  - Cabin : 乗客が宿泊する客室番号。 の形式をとります。deck/num/sideは左舷または右舷をside表します。PS
  - Destination : 乗客が降りる惑星。
  - Age : 乗客の年齢。
  - VIP : 乗客が航海中に特別な VIP サービスに対して料金を支払ったかどうか。
  - RoomService : 宇宙船タイタニック号の多くの豪華なアメニティのFoodCourtそれぞれに対して乗客が請求した金額。ShoppingMallSpaVRDeck
  - Name : 乗客の氏名（名と姓）。
  - Transported : 乗客が別の次元に転送されたかどうか。これがターゲット、つまり予測しようとしている列です。
- test.csvTransported : 残りの3分の1（約4300人）の乗客の個人記録。テストデータとして使用します。このセットの乗客の の値を予測することが課題です。
  - sample_submission.csv : 正しい形式の提出ファイル。
  - PassengerId : テスト セット内の各乗客の ID。
  - Transported : 目標。各乗客について、 または を予測しTrueますFalse。

② 環境準備

SageMaker StudioでNotebook環境を用意し、S3とGitHub(最適な使い方が分からない)を接続。

③ EDA（探索的データ分析）

各特徴量の分布・相関・欠損値チェック。どのような前処理/モデリング/CV設計/アンサンブルが効果的か仮説を立てる。
ノートブック上で実行する。
※可視化にBIツールは利用する？
ノート命名規則：EDA.ipynb

④ 前処理(CV設計)

スケーリング、欠損補完、カテゴリ変数エンコード等の前処理を実施する。
前処理ごとに、CV設計を実施する。CV設計では、KFoldやTimeSeriesSplit(時系列性があるデータをデータリークしないようにする)、StratifiedKFold(目的変数の分布をfold間で揃える)、GroupKFold(同一系列のデータを同じfoldに入れる)等を利用し、LBと同等の性質を持つCVを設計できることをゴールとする。
sagemaker processing jobをノートブック上で実行する。
ノート命名規則：Preprocess.ipynb
スクリプト命名規則：{コンペ名}_Preprocess{前処理番号}_CV{CV番号}.py

⑤ モデリング

XGBoost / LightGBM / RandomForest / NNなどから試す。アンサンブル(複数モデルの平均・スタッキング)も適宜実施する。
sagemaker training jobをノートブック上で実行する。
ノート命名規則：Modeling.ipynb
スクリプト命名規則：{コンペ名}_Modeling{モデリング番号}.py

⑥ 評価と提出

モデリング時に、sagemaker experimentに登録するようにする。評価指標がある閾値を超えた場合に結果を提出するようにする。
評価結果を基に改善を繰り返す。

## 2. データ管理

本プロジェクトの各種データをGitHub + S3 を利用して、以下の構成
で管理します。

### ・GitHub（コード・設定ファイル管理）

#### Repository Structure

kaggle-Predict-Calorie-Expenditure/

data/  　軽量サンプルデータ or metadata（.gitignore推奨）

notebooks/   　分析用Notebook（EDA, Preprocess, Modeling）

scripts/    　モデル構築・学習・推論用スクリプト（.py）

config/       　ハイパーパラメータや環境設定（YAML/JSON）

outputs/      　ローカル検証用submitファイル、予測結果（.gitignore）

requirements.txt     　依存ライブラリ

README.md       　プロジェクト概要

#### GitHub Rules



### ・S3（中間生成物・成果物ストレージ）

s3://datascience-keigo/kaggle-Predict Calorie Expenditure/

raw/  　元データ（train.csv, test.csv）

processed/         　前処理後データ（train_proc.pklなど）

models/            　学習済モデル（.pkl, .joblib）

submissions/      　提出ファイル（.csv）

logs/        　学習ログ・評価結果

auth/　認証情報

## 3. メタデータ管理

AWS Glue Data Catalogを利用して、メタデータ管理を実施します。

## 4. ChatGPTチャットリンク

https://chatgpt.com/share/6820917f-d2e0-800c-968b-cf54c4bf7f3e
 