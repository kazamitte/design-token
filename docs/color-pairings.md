# 配色の組み合わせ

primaryを決めたあとに、secondary・base・linkをどこから選ぶかの一覧である。出発点として使い、最終判断はデザインに委ねる。

## 推奨の組み合わせ表

SecondaryとBase (harmonized)は推奨順に並べている。Base (achromatic)とLinkはどの候補を選んでもよく、既定値は列の1番目である。

| Primary | Secondary | Base (harmonized) | Base (achromatic) | Link |
| --------- | ------------------------------- | ------------------- | ------------------- | ---------------------------- |
| `red` | violet, purple, indigo, fuchsia | taupe, stone | neutral, zinc, gray | indigo, violet, purple, blue |
| `orange` | violet, purple, indigo, blue | taupe, stone | neutral, zinc, gray | indigo, violet, purple, blue |
| `amber` | violet, purple, indigo, blue | stone, taupe, olive | neutral, zinc, gray | indigo, violet, purple, blue |
| `yellow` | violet, purple, indigo, blue | olive, stone | neutral, zinc, gray | indigo, violet, purple, blue |
| `lime` | indigo, violet, purple, blue | olive, stone | neutral, gray, zinc | indigo, violet, purple, blue |
| `green` | indigo, blue, violet, purple | olive, mist | neutral, gray, zinc | indigo, violet, purple, blue |
| `emerald` | indigo, blue, violet, purple | mist, olive | neutral, gray, zinc | indigo, violet, purple, blue |
| `teal` | lime, orange, amber, yellow | mist, slate | neutral, gray, zinc | indigo, violet, purple, blue |
| `cyan` | lime, yellow, amber, orange | mist, slate | neutral, gray, zinc | indigo, violet, purple, blue |
| `sky` | lime, yellow, amber, orange | slate, mist | neutral, gray, zinc | indigo, violet, purple, blue |
| `blue` | lime, yellow, amber, orange | slate, mist | neutral, gray, zinc | indigo, violet, purple, blue |
| `indigo` | lime, yellow, amber, orange | slate, mauve | neutral, zinc, gray | indigo, violet, purple, blue |
| `violet` | yellow, lime, amber, orange | mauve, slate | neutral, zinc, gray | indigo, violet, purple, blue |
| `purple` | yellow, amber, lime, orange | mauve, slate | neutral, zinc, gray | indigo, violet, purple, blue |
| `fuchsia` | amber, orange, yellow, red | mauve, slate | neutral, zinc, gray | indigo, violet, purple, blue |
| `pink` | blue, orange, amber, yellow | mauve, taupe | neutral, zinc, gray | indigo, violet, purple, blue |
| `rose` | violet, purple, indigo, blue | taupe, stone | neutral, zinc, gray | indigo, violet, purple, blue |

## Secondary の選び方

Secondaryは、P型・D型の色覚シミュレーション後でもprimaryとできるだけ離れて見える色を選ぶ。

- P型（1型色覚/protan）: 赤系の光に対する感度が低い、または欠けるタイプ
- D型（2型色覚/deutan）: 緑系の光に対する感度が低い、または欠けるタイプ

primary以外の16色すべてについて、P型・D型に変換した後の色差を、ΔE2000（CIEDE2000）指標を用いて、2色の知覚的な違いを数値化する。0に近いほど似ており、値が大きいほど違って見える。

なお、T型（3型色覚/tritan）は、青系の色に対応するが、まれな色覚特性であるため今回の評価からは除外している。

評価には次の3つの表示条件を使う。

- `bg-solid`（step 700）
- ライトモードの`fg-subtle`
- ダークモードの`fg-subtle`

P型・D型それぞれで測るため、合計6条件になる。そのうち最も小さいΔE2000値を、そのprimaryとsecondaryの組み合わせのスコアとする。

つまり、ある条件だけで大きく離れて見える色ではなく、P型・D型やライト・ダークをまたいでも安定して見分けやすい色を優先している。

そのスコアが大きい順に上位4色をSecondary候補とした。

### P型・D型での見え方

赤と緑を見分けにくい色覚（P型・D型）では、このパレットの多くの色が「黄っぽく見える色」と「青っぽく見える色」に大きく分かれる。

- 黄グループ: `rose`, `red`, `orange`, `amber`, `yellow`, `lime`, `green`, `emerald`
- 青グループ: `cyan`, `sky`, `blue`, `indigo`, `violet`, `purple`, `fuchsia`
- 境界にある色: `teal`, `pink`

`teal`と`pink`はP型とD型で見え方の方向が異なるため、どちらか一方のグループには固定していない。

同じ側にある色どうしはP型・D型への変換後に色相差が小さくなり、主に明るさや鮮やかさの違いとして知覚されやすい。たとえば`red`と`green`は、通常色覚では大きく異なるが、P型・D型ではどちらも黄寄りに見える。

### 数値での評価

P型・D型の見え方の変換にはMachado et al. (2009) の行列を使う。severityは1.0で、Chromium DevToolsの色覚エミュレーションで使われている行列と同じものである。

変換手順は次のとおり。

```text
OKLCH
→ sRGB 8bit
→ 線形RGB
→ Machado 2009 行列
→ CIELAB
→ ΔE2000
```

2色の違いはΔE2000で測る。0に近いほど似ており、値が大きいほど離れている。

この評価では、十分な余裕を持たせるため20以上をひとつの目安としている。これはP型・D型における一般的な知覚閾値を意味するものではなく、この配色評価のための基準である。

評価には、実際にsemantic tokenが参照する次の条件を使う。

- `bg-solid`: step 700
- ライトモードの`fg-subtle`: 色ごとにstep 500または600
- ダークモードの`fg-subtle`: 色ごとにstep 500または600

P型・D型それぞれについて3つの表示条件を測り、6条件のうち最も小さいΔE2000をその組み合わせの値とする。

代表的な結果は次のとおり。

- `teal`と`pink`を除く15色では、表の上位候補が39以上
- `teal`では23〜24程度
- `pink`では最大でも16程度
- `red` × `green`: 6
- `orange` × `green`: 7
- `rose` × `green`: 3

`pink`は、この評価で採用した20の目安を満たすSecondary候補がない。PrimaryとSecondaryを色だけで明確に区別する必要がある用途では注意が必要である。

## Base の選び方

Baseは、primaryに色みを寄せるharmonizedと、色みを抑えるachromaticの2系統から選ぶ。

### harmonized — primaryに寄せた灰色

primaryと色相の近い灰色を選ぶ。画面全体に統一感を持たせながら、地の色としては灰色に近い見た目を保つ。

候補は次の6色である。

- `taupe`
- `stone`
- `olive`
- `mist`
- `slate`
- `mauve`

順位にはstep 500のOKLCH hueを使い、primaryとの色相角の差が小さい順に並べる。

候補には常に上位2色を載せる。3番目の色もprimaryとの色相差が40°以内であれば候補に加える。

### achromatic — 色みを抑えた灰色

primaryを灰色の上で目立たせたい場合や、画面内ですでに多くの色を使っている場合に使う。

候補は次の3色で固定する。

- `neutral`
- `zinc`
- `gray`

既定値は色みを持たない`neutral`である。

残りの`zinc`と`gray`は、step 500のOKLCH hueがprimaryから近い順に並べる。順位は選択時の参考であり、どちらを使ってもよい。

## Link の選び方

Linkはprimaryとは独立して選ぶ。

`link-fg`と`link-fg-strong`の2トークンを使い、それぞれ選んだ色の`fg-muted`と`fg`を参照する。

表に載せた4色で、参照するstepは次のとおり。

| Mode | `link-fg` | `link-fg-strong` |
| ----- | --------- | ---------------- |
| Light | 600 | 700 |
| Dark | 400 | 300 |

### Link の順位

各色について、P型・D型変換後のリンク色と本文色との差を測る。

評価対象は次の4条件である。

- Lightの`link-fg`
- Lightの`link-fg-strong`
- Darkの`link-fg`
- Darkの`link-fg-strong`

さらにP型・D型の両方を評価し、その中で最も小さいΔE2000をその色の値とする。

青グループの結果は次のとおり。

- `indigo`: 23
- `violet`: 22
- `purple`: 20
- `blue`: 20
- `sky`: 17
- `cyan`: 12
- `teal`: 5

このうち上位4色を候補として表に載せる。

- `indigo`, `violet`, `purple`, `blue`

既定値は`indigo`である。

どの候補でも、最小値はダークモードのhover時に使うstep 300で発生する。

黄グループでは`lime`、`yellow`、`amber`もダークモードで本文との色差を取りやすい。ただし、一般的なリンク色としての慣習を優先し、標準候補には含めていない。ダークモード主体のUIでは選択肢になり得る。

一方、`red`、`rose`、`teal`、`pink`はP型・D型変換後に本文との差が小さくなりやすいため、リンク色としては避ける。特に`teal`はダークモードで無彩色に近づき、本文との差が小さくなる。

### 色以外でもリンクを示す

リンクは下線など、色以外の視覚的な目印を付ける前提とする。

WCAG 2.2 Success Criterion 1.4.1 Use of Colorに従い、色だけでリンクであることを伝えない。

## ステータスカラーとの競合

ステータスカラーは慣例に沿って次の色を使う。

- Error: `red`
- Warning: `amber`
- Success: `green`
- Info: `blue`

PrimaryやSecondaryがこれらと同色または近い色になる場合、ブランド表現と状態表現を色だけでは区別しにくくなる。

特に`red`、`amber`、`green`、`blue`はステータスカラーと直接競合する。`rose`や`orange`などの近似色も、用途によっては同じ意味の色に見えることがある。

### 回避策

1. ステータス色とブランド色を近接して配置しない。
2. アイコン、形状、ラベルなど色以外の視覚要素を併用する。
3. 必要に応じてステータスカラーの変数を個別に上書きする。
4. Warningでは、黄と黒の高コントラストな意匠を使う方法もある。

色だけで状態を伝えず、意味の違いを他の視覚表現でも示すことを基本とする。

## 参考文献

- カラーユニバーサルデザイン機構（CUDO）『カラーユニバーサルデザイン推奨配色セット ガイドブック』ver.4、2018年。[配布ページ](https://jfly.uni-koeln.de/colorset/)
- Machado, G. M., Oliveira, M. M., Fernandes, L. A. F. (2009). A Physiologically-based Model for Simulation of Color Vision Deficiency. _IEEE TVCG_ 15(6)
- Chromium, [Simulating color vision deficiencies in the Blink Renderer](https://developer.chrome.com/docs/chromium/cvd)
- W3C, [WCAG 2.2 Success Criterion 1.4.1 Use of Color](https://www.w3.org/TR/WCAG22/#use-of-color)
