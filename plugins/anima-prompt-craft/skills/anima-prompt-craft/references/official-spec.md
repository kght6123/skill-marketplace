# references/official-spec.md — Anima 公式仕様まとめ

出典：circlestone-labs/Anima 公式モデルカード（HuggingFace）。SKILL.md に載せきらない仕様の全量をここに置く。

## 目次

1. モデル概要
2. バージョン（Base / Aesthetic / Turbo）
3. インストールと生成設定
4. プロンプト仕様（タグ体系）
5. データセットタグ（ye-pop / deviantart）
6. 自然文プロンプト
7. 制限
8. ファインチューニング / LoRA学習
9. 対応オンラインプラットフォーム
10. ライセンス

---

## 1. モデル概要

- **Anima**：CircleStone Labs と Comfy Org の共同開発による **20億パラメータ**の text-to-image モデル
- ベースモデル：`nvidia/Cosmos-Predict2-2B-Text2Image`
- アニメの概念・キャラクター・画風が主眼。ただし**非写実の絵全般**を幅広く生成できる（イラスト・アート向け）
- 学習データ：数百万枚のアニメ画像 ＋ 約80万枚の非アニメ系アート画像。**合成データ不使用**
- **アニメ学習データの知識カットオフは 2025年9月**

## 2. バージョン

### Anima-Base
- 未調整の事前学習モデル。**柔軟性・多様性・画風追従が最大**
- **LoRA はこのバージョンで学習する**

### Anima-Aesthetic
- 一貫性と既定画風の品質を上げたファインチューン
- v1.0b は「美的ファインチューンのみ」の別版（1.0 のような画風調整・安定化LoRAのマージ無し）。作者は **1.0 の方が良い**としている
- **プロンプト上の注意**：高品質画像のみで学習し、キャプションから品質タグを除去してある。ポジに品質タグは不要（`masterpiece, best quality, ` は残しても無害）。**`score_*` タグはポジ・ネガ双方で使わないことを推奨**（品質側に押し込みすぎて slop 化する）

### Anima-Turbo
- 蒸留版。**CFG 1・8〜12 ステップ**で使う
- 蒸留の副作用で安定性が増し、既定の画風が強くなる代わりに**多様性は低下**
- 作者の推奨：**まず Turbo から始める**。平均すると Aesthetic よりわずかに劣る程度で、生成が非常に速い（ステップ課金のオンラインサービスでは安上がり）。安定性ゆえに Aesthetic より良い結果になる場合もある

## 3. インストールと生成設定

ComfyUI がネイティブ対応。公式の example.png にワークフローが埋め込まれており、ComfyUI にドラッグ&ドロップで読み込める。

**ファイル配置**

| ファイル | 置き場所 |
| --- | --- |
| `anima-base-v1.0.safetensors` | `ComfyUI/models/diffusion_models` |
| `qwen_3_06b_base.safetensors` | `ComfyUI/models/text_encoders` |
| `qwen_image_vae.safetensors` | `ComfyUI/models/vae`（Qwen-Image の VAE。既に持っている場合あり） |

**生成設定**

- 解像度：**512² 〜 1536²** ピクセルで動作
- ステップ **30〜50**、CFG **4〜5**（Turbo は 8〜12 / CFG 1）
- サンプラー（作者の好み）
  - `er_sde` … ニュートラルな画風、フラットな色、シャープな線。**妥当な既定**
  - `euler_a` … 柔らかく細い線。ときどき2.5D寄り。CFGを他より高めにしても焼けにくい
  - `dpmpp_2m_sde_gpu` … `er_sde` に近い画風だが、より多様で「創造的」。プロンプト次第で暴れすぎることも
  - `euler` … 基本のサンプラーで `er_sde` よりやや創造的。Turbo / Aesthetic と相性が良い（元々安定しているため）
- 絵画的な質感を強めたいなら **`beta57` スケジューラ**（ComfyUI RES4LYF カスタムノードパック）。低ノイズ領域に重みを置くのでテクスチャ表現が良くなる（写実にはならない。Animaは写実が苦手なモデル）

**モデル比較ワークフロー**

`anima_comparison.json` が公式提供。列＝モデル、行＝シードのグリッド画像を生成する。出力ノードを変更すれば任意数のモデルを比較可能。対応アーキ：Anima / SDXL / Lumina / Chroma / Newbie-Image。既定構成は Anima・NetaYume・Newbie-Image の比較。

## 4. プロンプト仕様（タグ体系）

学習構成：**Danbooruスタイルのタグ ＋ 自然文キャプション ＋ その混合**。

**基本ルール**
- タグは小文字、アンダースコアではなく**スペース**区切り。アンダースコアを使うのは**スコアタグのみ**
- 推奨ポジ接頭：`masterpiece, best quality, score_7, safe, `
- 推奨ネガ：`worst quality, low quality, score_1, score_2, score_3, artist name, blurry, jpeg artifacts, chromatic aberration`
- Danbooru と Gelbooru でタグが異なる場合は **Gelbooru 版**を優先
- 強調構文は機能するが、**SDXL で一般的な値より高い重みが必要**。例：`(chibi:2)`
- **タグドロップアウト**を入れて学習しているため、関連タグを全部書く必要はない

**タグ順序**
```
[品質/メタ/年代/安全タグ] [1girl/1boy/1other 等] [character] [series] [artist] [一般タグ]
```
各セクション内は順不同。

**品質タグ**
- 人間スコア系：`masterpiece, best quality, good quality, normal quality, low quality, worst quality`
- PonyV7 aesthetic モデル系：`score_9, score_8, ... score_1`
- どちらか／両方／どちらも使わない、いずれの組み合わせでも動く

**年代タグ**
- 特定年：`year 2025`, `year 2024`, ...
- 時期：`newest, recent, mid, early, old`

**メタタグ**
`highres`, `absurdres`, `anime screenshot`, `jpeg artifacts`, `official art` など

**安全タグ**
`safe` / `sensitive` / `nsfw` / `explicit`

**アーティストタグ**
`@` を前置する。例：`@big chungus`。**@ を付けないと効果は非常に弱い**

**公式のフルタグ例**
```
year 2025, newest, normal quality, score_5, highres, safe, 1girl, oomuro sakurako, yuru yuri, @nnn yryr, smile, brown hair, hat, solo, fur-trimmed gloves, open mouth, long hair, gift box, fang, skirt, red gloves, blunt bangs, gloves, one eye closed, shirt, brown eyes, santa costume, red hat, skin fang, twitter username, white background, holding bag, fur trim, simple background, brown skirt, bag, gift bag, looking at viewer, santa hat, ;d, red shirt, box, gift, fur-trimmed headwear, holding, red capelet, holding box, capelet
```

## 5. データセットタグ（ye-pop / deviantart）

画風・題材の多様性を上げるため、非アニメの2データセットを追加学習している：**LAION-POP（ye-pop 版）**と **DeviantArt**（いずれも写真を除外してフィルタ済み）。これらはアニメ系データと質的に異なるため、キャプションに「データセットタグ」が付けられている。

**書式**：プロンプトの**先頭行**にデータセットタグを置き、改行する。2行目には任意で alt-text（ye-pop）または作品タイトル（DeviantArt）を書ける。3行目以降が説明文。

```
ye-pop
For Sale: Others by Arun Prem
Abstract, oil painting of three faceless, blue-skinned figures. Left: white, draped figure; center: yellow-shirted, dark-haired figure; right: red-veiled, dark-haired figure carrying another. Bold, textured colors, minimalist style.
```

```
deviantart
Flame
Digital painting of a fiery dragon with glowing yellow eyes, black horns, and a long, sinuous tail, perched on a glowing, molten rock formation. The background is a gradient of dark purple to orange.
```

→ **アニメ以外の画風（油彩・デジタルペイント・抽象・イラストレーション等）を狙うときの切り札**。アニメ系タグ体系のままだと寄りにくい領域に入れる。

## 6. 自然文プロンプト

- キャラ名・シリーズ名は**標準的な英語の大文字ルール**に従う
- 純自然文なら**記述的なほど良い。最低2文**を目安に。極端に短いと予期しない結果になる
- **タグと自然文は任意の順で混在可**
- 品質タグ・アーティストタグを自然文の先頭に置いてもよい
  - `masterpiece, best quality, @big chungus. An anime girl with medium-length blonde hair is...`
- **キャラ名を出したら、続けて基本的な外見を描写する**
  - `Digital artwork of Fern from Sousou no Frieren, with long purple hair and purple eyes, wearing a black coat over a white dress with puffy sleeves...`
  - 複数キャラのときは特に重要。名前を並べるだけだとモデルが混乱する

## 7. 制限

- **写実は苦手**（意図的な設計。アニメ／イラスト／アート特化）
- プロンプトが短い・詳細に欠けると**望まない内容が出やすい** → 安全タグをポジ・ネガ双方に入れ、十分に詳細なプロンプトを書くことで回避
- **テキスト描画は不得意**。単語1つ、ときに短いフレーズ程度。長文は破綻する
- Base は真の素体。curated データでの美的調整をしていないため、**アーティストタグ・品質タグを使わないと既定の画風は非常に地味でニュートラル**

## 8. ファインチューニング / LoRA学習

- **LLMアダプタを学習させない**。作者の学習スクリプト diffusion-pipe では `llm_adapter_lr=0` で完全に無効化でき、サンプル設定でもこれが既定。sd-scripts など他のトレーナーにも同等のオプションがあるので必ず使う
  - 理由：LLMアダプタは拡散モデルに届く前のテキスト埋め込みを処理するため、生成画像への影響が過大。アダプタ自体が驚くほど多くの知識を持っており、学習させると容易に劣化する
- **学習率は低めに**。rank 32 の LoRA なら **2e-5 から始めて**上下に調整
  - 素体モデルなので、ファインチューン時に打ち消すべき強い美的調整や RLHF が無い
  - 視覚概念が非常に大量かつ多様に入っているので、軽いタッチで足りる
- LoRA は **Base 版**で学習する（§2）
- 公式が style LoRA の例をデータセット・設定込みで CivitAI に公開（models/2536147）

## 9. 対応オンラインプラットフォーム

CivitAI / TensorArt / KusArt / IMGNAI / mage / AliveAI / DreamerLand が公式にホスト生成に対応。

## 10. ライセンス

- **CircleStone Labs Non-Commercial License**。モデルおよびその派生物は**非商用目的のみ**
- 加えて、Cosmos-Predict2-2B-Text2Image の「Derivative Model」に該当するため、派生モデルに適用される範囲で **NVIDIA Open Model License Agreement** にも従う
- 商用ライセンスの問い合わせは公式モデルカード（HuggingFace）記載の連絡先へ

**重要**：非商用の制限は**モデルにのみ**適用され、**Outputs（生成画像）には適用されない**。生成画像は商用利用してよい。

**許可される商用利用の例**
- 画像の販売
- 有償コミッションでの画像制作
- 有料プロダクト（ゲーム、ビジュアルノベル等）のコンセプトアート・アセット生成
- 個人として活動している場合の派生モデル重みの販売（第2条cに個別の除外規定あり）

**別途ライセンスが必要な例（無断では不可）**
- モデルをAPIの背後でホストしてアクセスを課金する
- 有料の画像生成プラットフォームにモデルを載せる
- 収益化されたゲーム等のプロダクトにモデル重みを同梱する
- 収益化された大きなプロダクトの一機能としてモデルを使う
