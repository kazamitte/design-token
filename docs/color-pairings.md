# 配色の組み合わせ

各primaryに対して推奨する組み合わせの一覧である。

これらはあくまで出発点であり、厳格なルールではない。根拠を持って色を選ぶための参考資料として用意した。

表は単一の計算式の出力ではない。secondaryは数値の順位をそのまま採り、harmonizedは1番目が数値どおりで2番目以降にわずかに判断が入る。achromaticは3色固定で順序だけが変わり、linkは判断で選んでいる。以下ではその内訳を示す。

## 推奨の組み合わせ

| Primary   | Secondary                    | Base (harmonized)   | Base (achromatic)   | Link             |
| --------- | ---------------------------- | ------------------- | ------------------- | ---------------- |
| `red`     | indigo, purple, violet, blue | taupe, stone        | neutral, zinc, gray | blue             |
| `orange`  | indigo, purple, violet, blue | taupe, stone        | neutral, zinc, gray | blue             |
| `amber`   | indigo, purple, violet, blue | stone, taupe, olive | neutral, zinc, gray | blue             |
| `yellow`  | indigo, purple, violet, blue | olive, stone        | neutral, zinc, gray | blue             |
| `lime`    | indigo, violet, purple, blue | olive, stone        | neutral, zinc, gray | indigo, blue     |
| `green`   | indigo, violet, blue, purple | olive, mist         | neutral, zinc, gray | indigo           |
| `emerald` | indigo, violet, blue, purple | mist, olive         | neutral, zinc, gray | blue, indigo     |
| `teal`    | lime, yellow, amber, orange  | mist, slate         | neutral, zinc, gray | blue             |
| `cyan`    | lime, yellow, amber, orange  | mist, slate         | neutral, zinc, gray | indigo           |
| `sky`     | lime, yellow, amber, orange  | slate, mist         | neutral, zinc, gray | indigo           |
| `blue`    | lime, yellow, amber, orange  | slate, mist         | neutral, zinc, gray | cyan             |
| `indigo`  | lime, yellow, amber, orange  | slate, mauve        | zinc, neutral, gray | cyan             |
| `violet`  | lime, yellow, amber, orange  | mauve               | zinc, neutral, gray | blue             |
| `purple`  | yellow, lime, amber, orange  | mauve               | zinc, neutral, gray | blue             |
| `fuchsia` | yellow, amber, lime, orange  | mauve, taupe        | neutral, zinc, gray | teal, cyan, blue |
| `pink`    | yellow, amber, lime, orange  | mauve, taupe        | neutral, zinc, gray | teal, cyan, blue |
| `rose`    | indigo, purple, violet, blue | taupe, mauve        | neutral, zinc, gray | blue, teal       |

各列の候補は推奨順に並べている。件数は列ごとに一定ではなく、これも選定の結果である。baseはharmonizedとachromaticのどちらかを選ぶ。

## 選定の根拠

### Secondary — 知覚的な分離

primaryから十分に離れた色を選定した。secondaryをprimaryの濃淡ではなく、独立した信号として認識しやすくするためである。

「十分に離れている」ことの確認には、色差の指標 ΔE2000（CIEDE2000）を用いた。2つの色が人の目にどれだけ違って見えるかを数値化した指標で、0が同一色、値が大きいほど知覚上の差も大きい。

さらに、色覚の型による見え方の違いも考慮した。人の色覚は、目にある3種類の錐体（L・M・S）の働き方によっていくつかの型に分かれる。P型（1型）はL錐体、D型（2型）はM錐体の欠損または変異によるもので、いずれも赤と緑の系統を判別しにくい傾向がある。

計算は`bg-solid`が参照するstep 700を対象とした。npmパッケージの[@bjornlu/colorblind](https://www.npmjs.com/package/@bjornlu/colorblind)でP型・D型をシミュレートし、変換後の色について、表に挙げた組み合わせの ΔE2000を求めている。その結果、最小値は26.5、17件すべてが25以上となった。この列は数値の順位をそのまま採っており、17行すべてで4色が計算上の上位4件と一致する。

26.5という下限自体に根拠があるわけではない。妥当性は既知の混同ペアとの比較で判断した。古くから見分けにくいとされる組み合わせを同じ方法で計算すると、`red`×`green`が11.3、`orange`×`green`が11.6である。今回の下限はこれらを大きく上回る。

数値だけでなく、カラーユニバーサルデザイン推奨配色セットの考え方も照合した。ガイドブックでは、P型・D型で見分けにくい色の組み合わせが媒体別に整理されており、例としてピンクと緑が挙げられている。

ただしCUDのトークンは調整済みの固定値で、名前もこのパレットの色名と対応しない。実測でもっとも近いのは次の色である。

| CUD トークン   | 近いパレット色       |
| -------------- | -------------------- |
| pink #ff8082   | `red-400`（ΔE 5.9）  |
| green #03af7a  | `emerald-500`（3.9） |
| red #ff4b00    | `orange-600`（2.0）  |
| orange #f6aa00 | `yellow-500`（3.7）  |
| purple #990099 | `fuchsia-800`（2.9） |

つまり「ピンクと緑」が指すのは`pink`×`green`ではなく、`red`・`rose`×`emerald`にあたる。該当する組み合わせは`rose`×`emerald`が15.3、`red`×`green`が11.3、`rose`×`green`が7.9と、いずれも下限を下回るため数値の段階で落ちる。名前だけを突き合わせた手動の除外は行っていない。参考までに`pink`×`green`は27.9で、分離は十分である。

T型（3型）は評価対象外とした。S錐体の欠損または変異によるもので、青と黄の系統を見分けにくい型だが、P型・D型と比べて割合が極めて小さいためである。また、評価に含めると`green`×`cyan`が ΔE 0.4となり、この型を基準に据えると選べる組み合わせが過度に狭まる。

### Base（harmonized）— 調和色

primaryと色相を共有するニュートラルを選定した。画面全体にまとまりを持たせつつ、色みは中立に見える範囲へ抑えている。

候補は、9つのニュートラルのうち調和色に割り当てた6色である。step 500のHはtaupeが43°、stoneが58°、oliveが107°、mistが213°、slateが257°、mauveが322°で、色相環上に散らしてある。どのprimaryにも近い候補が1つはある。

順位はstep 500でのHの距離だけで決めている。17のprimaryすべてで、1番目は距離が最小の候補と一致する。

2番目まで見ると、外れるのはfuchsiaとroseの2件である。fuchsiaは距離2位のslate（65°）を、roseは2位のstone（42°）を飛ばして、それぞれtaupeとmauveを採っている。どちらもマゼンタ寄りと暖色寄りを1つずつ持たせた形になる。

Cは重みに使っていない。Cで重み付けすると、彩度の高いslate（0.046）やmauve（0.034）が色相の近さを押しのけ、たとえばcyanの1番目がmist（距離2°）ではなくslate（42°）になる。アンダートーンを共有させたいのだから、見るべきはHの距離だけである。

### Base（achromatic）— 無彩色

primaryを中立な面の上で単独で際立たせたい場合、あるいはUI内にすでに多くの色が存在する場合に用いる。

9つのニュートラルは、あらかじめ役割で二分してある。どのprimaryに対しても中立な地として使う3色（neutral、zinc、gray）と、primaryのアンダートーンを共有させる6色（taupe、stone、olive、mist、slate、mauve）である。この列は前者を全primaryで共通に使う。3色の顔ぶれはどの行でも同じで、順序だけがindigo・violet・purpleで変わる。この3つはzincの色相（285.9°）から20°以内にあり、zincがもっとも馴染むためである。それ以外はneutralが先頭で、grayは常に最後に置く。

この分け方はCの閾値ではない。step 500のCは小さい順に、neutralが0、stoneが0.013、zincが0.016、taupeとmistが0.021、grayが0.027、oliveが0.031、mauveが0.034、slateが0.046で、両者は数直線上で入り混じっている。stoneはzincやgrayより無彩色に近いが調和色側にあり、grayはtaupeやmistより色みが強いが無彩色側にある。

分けているのは色みの量ではなく、色みの向きに役割を与えるかどうかである。stoneの暖色みやslateの青みは、対応するprimaryと組ませたときに地の色として働かせたいので調和色に回している。neutral・zinc・grayは、どのprimaryとも組ませずに灰色として使う。

### Link — 本文への馴染み

primaryとbaseの双方に馴染みやすい色を選定した。この列は数値で順位付けしておらず、primaryとの見分けやすさとbaseとの馴染みの両方を見て判断で決めている。表ではいずれも別の色を挙げているが、primaryと同色でも構わない。リンクを本文から浮かせたくない場合は、baseに近い色を採ることもできる。

リンクの識別は、下線など色以外の手がかりによって担保する前提である。そのためLink列では、色覚多様性のもとでのprimaryとの判別可能性を保証していない。

WCAG 1.4.1も、色だけで情報を伝えてはならないと定めている。リンク色そのものに意味を背負わせるのではなく、色以外の視覚的手がかりと併用する設計を前提とする。

## 補足事項

`red`、`rose`、`orange`をprimaryにすると、ステータスカラーの`--r-error-*`（red）や`--r-warning-*`（amber）と競合しやすい。

たとえば「エラーを示す赤」と「ブランドカラーとしての赤」を同一画面で使用した場合、トークン上では別の役割を割り当てていても、ユーザーには同じ意味を持つ色として認識される可能性がある。

これを避けるには、同系色のステータスカラーとブランドカラーを隣接させない設計が必要となる。ステータスカラーは`tailwind/base/color/status.css`で固定しており、色を差し替える手段は用意していない。どうしても分けたい場合は、変数を上書きするか、色以外の手がかりで差をつけることになる。警告表現については、危険標識で広く使われる黄色と黒の組み合わせを採用するのも有効な選択肢である。

## 参考文献

- カラーユニバーサルデザイン機構（CUDO）『カラーユニバーサルデザイン推奨配色セット ガイドブック』ver.4、2018年。PDFは[配布ページ](https://jfly.uni-koeln.de/colorset/)から入手できる。
- [@bjornlu/colorblind](https://www.npmjs.com/package/@bjornlu/colorblind) — P型・D型のシミュレーションに使用した。
