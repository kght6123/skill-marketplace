---
name: minimax-h3-prompt-craft
description: MiniMax H3（音声付き動画生成モデル）向けのプロンプトを新規作成・改良するスキル。ユーザーが「MiniMax H3のプロンプトを作って」「H3用のプロンプトに直して」「T2VAのプロンプトを書いて」「I2VA/FL2VA/L2VAで生成したい」「Ref2VAで参照画像を使ったプロンプトを作って」「音声込みの動画プロンプト」などと言った場合に使用する。integrated_multimodal_description・overall_soundscape・non_diegetic_musicの組み立て方、Ref2VAでの参照ラベル（<Picture 1>等）の付け方、モード（T2VA/I2VA/FL2VA/L2VA/Ref2VA）の判定に関する質問にも積極的に使う。
---

# MiniMax H3 Prompt Craft

MiniMax H3（テキスト・画像・動画・音声を入力に、音声付き動画を生成するモデル）向けに、コピペで使えるプロンプトを組み立てるスキル。

---

## 0. モードを判定する

ユーザーの入力（テキストだけか、画像があるか、動画があるか、音声があるか）から、まず生成モードを1つ決める。

| モード | 入力 | 用途 |
| --- | --- | --- |
| T2VA | テキストのみ | ゼロから音声付き動画の全体を組み立てる |
| I2VA | 最初のフレーム画像＋テキスト | 1枚絵を起点に、そこから先の展開を描写する |
| FL2VA | 最初と最後のフレーム画像＋テキスト | 2枚の画像をつなぐ連続した動きを描写する |
| L2VA | 最後のフレーム画像＋テキスト | 最後の絵に自然に収束するように、それ以前の展開を組み立てる |
| Ref2VA | 複数の参照素材（画像／動画／音声）＋テキスト | 既存の人物・物・声・音楽などを維持しつつ新しいシーンを作る |

迷ったら「参照して維持したい画像・動画・音声があるか」を確認する。あれば Ref2VA、なければ入力されているフレーム画像の有無で T2VA / I2VA / FL2VA / L2VA を選ぶ。

---

## 1. ベースモード（T2VA / I2VA / FL2VA / L2VA）の組み立て方

以下の3つの要素を、この順番で書く。

1. **`integrated_multimodal_description`**：シーン全体を時間軸に沿って描写する本体部分。被写体・構図・環境・動作・カメラワーク・音の発生源を、時間経過がわかる形で書く。
2. **`overall_soundscape`**：環境音・効果音など、画面内の出来事に紐づく音を描写する。せりふ・足音・風の音・物音など。
3. **`non_diegetic_music`**：画面内の出来事とは独立したBGM・劇伴を描写する。ジャンル、テンポ、雰囲気を書く。

モードごとの描写の起点は以下のように変える。

- **T2VA**：時間軸の最初から最後まで、シーン全体をゼロから設計する。
- **I2VA**：与えられた最初のフレームの状態を簡潔に確認したうえで、そこからの展開だけを描写する。
- **FL2VA**：最初のフレームと最後のフレームの間を、どう動き・移り変わるかに焦点を当てて描写する。
- **L2VA**：最後のフレームに何が起きて収束するのかが分かるように、そこへ至る妥当な展開を逆算して描写する。

---

## 2. Ref2VA（参照モード）の組み立て方

Ref2VAでは、渡された参照素材を保持しながら新しいシーンを作る。以下の順番でセクションを書く。

1. **`subject_definitions`**：各参照素材に一意なラベルを割り当て、何を参照しているか定義する。ラベルは `<Picture 1>` `<Video 1>` `<Audio 1>` のように、種類＋連番の形式で統一する。
2. **`summary`**：完成させたいシーンの要約を1〜2文で書く。
3. **`retention_analysis`**：各参照ラベルについて、何を維持し何を変えてよいかを明記する（例：人物の外見・声質は維持、服装や背景は変更可）。
4. **`detailed_description`**：`subject_definitions` のラベルを使いながら、シーンの時間展開を具体的に描写する。参照素材がどの時点でどう現れるかも明記する。
5. **`overall_soundscape`**：ベースモードと同様、環境音・効果音を描写する。
6. **`non_diegetic_music`**：ベースモードと同様、BGM・劇伴を描写する。

参照ラベルは `subject_definitions` で定義したものを、以降のすべてのセクションで一貫して使う。別の呼び方に変えたり、定義していないラベルを新たに使ったりしない。

---

## 3. 書き方のコツ

- 各セクションは英語で書く。ただし、せりふ・歌詞・画面内の文字など「そのまま残すべきテキスト」は元の言語を保持する。
- 各ショットは「構図」「被写体」「環境」「動作」「カメラ」「音」を一通り押さえて描写する。あらすじの要約や、抽象的な褒め言葉（"beautiful", "cinematic" など）だけで済ませない。
- 動画の長さ（目安4〜15秒）とプロンプト内の時間展開を一致させる。長さに対して展開が多すぎたり少なすぎたりしないか確認する。
- I2VA / FL2VA / L2VA では、与えられたフレームとプロンプトの展開がどうつながるかを明示する。
- 参照ラベルが宙に浮かない（定義したのに使われない、使われているのに定義されていない）ようにする。

---

## 4. 出力フォーマット

必ずこの形で出す。説明は最小限、コピペのしやすさを優先する。

見出し（`integrated_multimodal_description` 等）はプロンプトの構成要素名であり、実際にどの形式（フォームの各欄、JSON、見出し付きテキストなど）で投入するかは利用するツール・APIの仕様に従う。見出しごとに内容を分けて書いておけば、どの投入形式にも組み替えやすい。

### ベースモード（T2VA / I2VA / FL2VA / L2VA）

````
## integrated_multimodal_description

```
（シーン全体の時間展開の描写）
```

## overall_soundscape

```
（環境音・効果音の描写）
```

## non_diegetic_music

```
（BGM・劇伴の描写）
```
````

### Ref2VA

````
## subject_definitions

```
（参照ラベルの定義）
```

## summary

```
（シーンの要約）
```

## retention_analysis

```
（維持する要素／変えてよい要素）
```

## detailed_description

```
（時間展開の詳細描写）
```

## overall_soundscape

```
（環境音・効果音の描写）
```

## non_diegetic_music

```
（BGM・劇伴の描写）
```
````

---

## 5. 作成ワークフロー

「○○のプロンプトを作って」と言われたら：

1. **モード判定**（§0）
2. **テーマ分解**：被写体／シーン（場所・時間）／動作の流れ／音の要素／（Ref2VAなら）参照素材と維持したい要素
3. **モードに応じたセクション構成で執筆**（§1 または §2）
4. **書き方のコツをチェック**（§3）：長さの整合性・参照ラベルの一貫性・具体性
5. **§4のフォーマットで出力**

---

## 6. 完成例

### T2VAの例（想定尺：約12秒）

````
## integrated_multimodal_description

```
A small coastal café at sunrise. A young barista wipes down the counter, then
looks up as the door chime rings. An elderly customer steps in, shaking rain
off an umbrella, and takes a seat by the window. The barista walks over,
notepad in hand, and the two exchange a brief nod of familiarity. The camera
starts on the door, then pans to follow the barista across the room, settling
into a medium shot of the table as the customer begins to speak.
```

## overall_soundscape

```
Door chime on entry. Rain tapping against the window. Footsteps on wooden
floor. Umbrella being folded and set against a chair. Low murmur of the
customer speaking, cup placed on saucer.
```

## non_diegetic_music

```
Soft acoustic guitar, slow tempo, warm and unhurried, fading in gently after
the door chime and staying low as the conversation begins.
```
````

### Ref2VAの例（想定尺：約10秒。`<Picture 1>` に写る人物を維持しつつ新しいシーンを作る）

````
## subject_definitions

```
<Picture 1>: the woman shown in the reference image — her face, hairstyle,
and body proportions are the subject to retain.
```

## summary

```
The woman from <Picture 1> walks through a night market, pausing at a
lantern stall.
```

## retention_analysis

```
<Picture 1>: retain her face, hairstyle, and body proportions exactly, as
defined in subject_definitions. Her outfit is not part of the retained
subject and may change to match the night market setting; the reference
image's background is not retained either.
```

## detailed_description

```
The scene opens on a crowded night market lit by hanging paper lanterns.
The woman from <Picture 1> walks slowly between stalls, her retained face
and hairstyle clearly visible under the warm light. She stops at a lantern
stall around the midpoint, reaching out to touch one of the lanterns as it
sways. The camera follows her from a three-quarter angle, then settles into
a close-up on her face as she smiles.
```

## overall_soundscape

```
Murmur of market crowds, distant vendor calls, wind chimes, paper lanterns
rustling in the breeze.
```

## non_diegetic_music

```
Light, playful erhu melody with a steady percussive undertone, matching the
festive atmosphere.
```
````

---

## 7. 自己改善

このSKILL.mdはマーケットプレイスの配布物であり、直接編集しない。プラグインが更新されるたびに上書きされ、更新を受け取れなくなるため。

ユーザーから出力への指摘があったら、このスキルを親とする**子スキル**に追記する。

- 子スキルの場所：`~/.claude/skills/minimax-h3-prompt-craft-updates/SKILL.md`（無ければ新規作成する）
- 子スキルのフロントマター例：
  ```
  ---
  name: minimax-h3-prompt-craft-updates
  description: minimax-h3-prompt-craft スキルの追加ルール・更新知見を保持する子スキル。minimax-h3-prompt-craft を使う際は必ず一緒に参照する。
  ---
  ```
- 追記後、子スキルのどこを更新したかを一言で伝える
