# Wan2.2 プロンプト実践例リファレンス

## 目次

1. モデル選択ガイド
2. ジャンル別プロンプト例
3. カメラワーク組み合わせパターン
4. パラメータ設定ガイド
5. トラブルシューティング

---

## 1. モデル選択ガイド

| モデル | パラメータ数 | 構成 | 用途 | VRAM目安 |
|---|---|---|---|---|
| Wan2.2-TI2V-5B | 5B | Dense | Text/Image → Video | 8GB〜（オフロード設定使用時の目安。環境により変動） |
| Wan2.2-T2V-A14B | 14B（MoE） | Mixture-of-Experts | Text → Video | 20GB+ |
| Wan2.2-I2V-A14B | 14B（MoE） | Mixture-of-Experts | Image → Video | 20GB+ |

**選択基準:**
- ローカル環境で試したい / VRAMが少ない → 5Bモデル（オフロード設定が前提になりやすい）
- 最高品質が欲しい → 14B・MoEモデル（T2VまたはI2V）
- 画像生成もしたい → 5BモデルでFrames=1に設定
- Apple Silicon Mac → 5Bモデル推奨

---

## 2. ジャンル別プロンプト例

### アクションシーン（トラッキングショット）

```
Cinematic NYC alley chase: The camera starts shoulder-height behind a hooded man, 
steadily tracking forward as he weaves through crowds. Cold tones, high contrast, 
neon lights. Smooth glide with intense shake for immersive pursuer tension. 
Blurred steam and wet pavement. Lens flare, shallow depth of field.
```

構造分析:
- ① Opening: NYC alley chase, hooded man
- ② Camera: shoulder-height, steadily tracking forward
- ③ Motion: weaves through crowds, smooth glide with intense shake
- ④ Visual Style & Color Grade: cold tones, high contrast, neon lights, lens flare, shallow depth of field

### 風景シーン（クレーンショット）

```
Slow crane shot rising above misty mountain peaks at dawn. Clouds drift 
through valleys below. Soft golden hour light breaks through fog creating 
god rays. Desaturated color palette with subtle blue-green tones. 
Cinemagraphic, serene atmosphere.
```

### ポートレート（クローズアップ）

```
Close-up shot of a woman's face as she slowly turns toward camera. Soft 
smile forms, eyes catch the light. Bokeh background with warm cafe ambiance. 
Soft key light from window creates natural skin tones. Kodak portra aesthetic 
with gentle film grain.
```

### サイバーパンク（トラッキング + ドリー）

```
A rainy night in a dense cyberpunk market, neon kanji signs flicker overhead. 
The camera starts shoulder-height behind a hooded courier, steadily tracking 
forward as he weaves through crowds of holographic umbrellas. Volumetric 
pink-blue backlight cuts through steam vents, puddles mirror the glow. 
Lens flare, shallow depth of field. Teal-and-orange color grade, moody 
Blade-Runner vibe.
```

### ノスタルジック（オービタル）

```
Golden hour meadow scene, a young woman in a sundress walks through tall grass. 
Warm kodak portra color palette with soft backlight creating a halo effect. 
16mm film grain adds texture. Camera performs a slow orbital arc around her 
as she twirls. Dreamy, nostalgic atmosphere with lens flare kissing the edges.
```

### 水中 / スローモーション

```
A diver plunges into crystal-clear pool water. Slow-motion capture shows water 
droplets suspended mid-air, backlit by golden hour sunlight. Camera follows the 
descent with a gentle downward tilt. Volumetric lighting creates god rays through 
the splashing water.
```

### ドリーアウト（キャラクター紹介）

```
In the style of an American drama promotional poster, a hero sits in a sleek, 
futuristic metal chair inside a dimly lit industrial setting. Camera dollies 
out slowly. The background shows an abandoned factory with light filtering 
through the windows. A medium shot with a straight-on close-up of the character.
```

### 360度オービタル（自然）

```
An orca breaches in crystal-clear Arctic waters. Slow 360° orbital shot around 
the soaring whale as droplets hang suspended. Soft polar sunset lights the scene 
in pastel pinks and blues; cinemagraphic HDR.
```

### 高速チェイス（ウィップパン）

```
Fast-paced motorcycle chase through narrow city streets. Camera performs aggressive 
whip-pans following the bike's sharp turns. Motion blur on background buildings, 
neon signs streak past. Handheld camera shake adds kinetic energy. High contrast, 
teal-and-orange color grade.
```

### Image-to-Video (I2V) 用

```
She slowly turns toward the camera, a soft smile forming as she notices 
something off-frame. Her hair shifts gently with the motion, eyes brightening 
with quiet surprise. The camera begins in a tight front view, then slowly 
zooms in while gently panning right. The scene feels warm and intimate, with 
smooth motion and soft lighting enhancing her expressive reaction.
```

I2Vでは元画像の内容を先に記述し、そこから追加する動きとカメラワークを書く。

### 画像生成（frames=1）

```
A medieval knight in ornate silver armor, polished and gleaming under radiant 
sunlight, riding a gigantic shimmering koi fish flying through the sky. 
Photorealistic style with natural depth and layered composition. Cinematic 
still frame. Ultra HD, detailed textures, volumetric lighting.
```

---

### アニメ風：日常×感情（ドリーイン）

```
Anime-style cinematic scene, a high school girl stands alone on a rooftop 
at golden hour, gripping a folded letter against her chest. Wind catches her 
hair and school uniform ribbon, flowing dramatically to the right. The camera 
slowly dollies in from a medium shot to a close-up of her face as tears form 
in her eyes. Cherry blossom petals drift lazily across the frame. Foreground 
petals blur softly while the distant cityscape glows in warm sunset orange 
and purple gradient sky. Sparkling light particles float in the golden air. 
Vibrant warm colors, clean lines, smooth shading, soft glow surrounding 
the character.
```

構造分析:
- ① Opening: アニメ風 + 屋上の女子高生 + 手紙
- ② Camera: ドリーインでミディアム→クローズアップ（感情強調）
- ③ Motion: 髪のなびき + 桜の花びら + 光の粒子
- ④ Visual Style & Color Grade: vibrant warm colors, clean lines（アニメ的語彙）、sunset gradient, soft glow（フィルム系ではなくアニメ的光）

### アニメ風：バトルアクション（トラッキング+ウィップパン）

```
Anime-style cinematic scene, a swordsman in dark flowing robes charges 
forward through a moonlit bamboo forest, katana drawn and glinting. The camera 
performs an aggressive tracking shot at ground level, keeping pace with his 
sprint. Dynamic speed lines streak across the background as bamboo stalks 
blur past. He leaps high into the air and the camera whip-pans upward to 
follow the arc. Foreground bamboo leaves scatter from the force. Cool 
blue moonlight contrasts with bright sparks from the blade. Cel-shaded 
animation style, high contrast, vibrant highlights against deep shadows, 
dramatic anime-style lens flare on the sword edge.
```

### アニメ風：ファンタジー風景（クレーンショット）

```
In the style of Japanese animation, a vast floating island city hovers above 
an ocean of clouds at sunrise. Waterfalls pour off the island edges into the 
mist below. The camera performs a slow crane shot rising from cloud level 
upward to reveal the full scale of the city with its towers and bridges. 
Sparkling light particles drift upward like inverse snowfall. Foreground 
clouds part gently while distant mountain peaks emerge on the horizon. 
Dramatic sunset gradient sky in warm orange and purple. Vibrant fantasy 
colors, clean architectural lines, smooth shading with soft ambient glow.
```

### アニメ風：キービジュアル静止画（frames=1）

```
Anime key visual style, a young mage stands at the edge of a crystal cliff 
overlooking an endless starlit ocean. Her long silver hair and ornate cape 
billow in a frozen dramatic pose. A massive glowing magic circle radiates 
beneath her feet. Layered composition with natural depth: foreground crystal 
shards in soft focus, midground character in sharp detail, background star 
ocean stretching to the horizon. Cinematic still frame. Vibrant jewel-tone 
colors, clean lines, smooth cel shading, bright highlights and deep rich 
shadows.
```

注意: アニメ風のframes=1では `photorealistic` の代わりに `anime key visual style` を使い、`16mm grain` や `kodak portra` は避ける。

---

## 3. カメラワーク組み合わせパターン

### 単一カメラワーク
最もシンプル。1つの動きを丁寧に記述する。
- `The camera slowly pans left to reveal the cityscape`

### 時間経過での切り替え
前半と後半で異なるカメラワークを使う。
- `Camera begins with a close-up, then dollies out to reveal the full scene`

### 同時併用
2つの動きを同時に行う。
- `Camera tracks forward while tilting up to frame the building`
- `Steadily tracking forward as the camera pans slightly right`

### 強調表現（逆方向防止）
カメラワークが意図通りに動かない場合の強化表現の例は SKILL.md の「カメラワーク語彙表」を参照。

---

## 4. パラメータ設定ガイド

| パラメータ | 推奨値 | 備考 |
|---|---|---|
| length | 81 または 121（`4n+1` を満たす値ならこれ以外も可） | 24fpsなら121で約5秒、16fpsなら81で約5秒。この長さで最高品質 |
| size | 960×540（下書き）/ 1280×720（本番） | VRAMと相談 |
| fps | 24（既定）/ 16（高速プロト） | lengthはfpsに合わせて選ぶ |
| sampling steps | 20〜50 | 多いほど高品質 |

**Lightx2v LoRAで高速化:**
Lightx2v LoRA使用で4ステップ生成が可能。

**8GB VRAMでの動作:**
`--offload_model True --convert_model_dtype --t5_cpu`

---

## 5. トラブルシューティング

### カメラワークが逆方向に動く
→ 表現を強化する（セクション3参照）。複数回生成して最良のものを選ぶ。

### 動画が茶色い霧だけになる
→ VAE（wan2.2_vae.safetensors）の配置を確認。ComfyUIを最新版に更新。

### 生成時間が長すぎる
14B・MoEモデルは5Bモデルよりかなり時間がかかる（具体的な所要時間はGPU・設定により大きく変わるため、まず自分の環境で計測するのが確実）。高速化には:
1. 5Bモデルに切り替える
2. Lightx2v LoRA使用（4ステップ）
3. 解像度を下げる（960×540）
4. フレーム数を減らす（`4n+1` を維持したまま、例えば61フレーム ≈ 2.5秒〔24fps〕）

### プロンプト拡張機能
Wan2.2にはAIによるプロンプト自動拡張機能がある。DashScope API または ローカルQwen モデルで利用可能。短いプロンプトを入力すると詳細なプロンプトに自動変換される。
