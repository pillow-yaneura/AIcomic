# 基本パラメーター (txt2img)

## キャラ絵生成時

Sampling stepsを20→30に変更し、縦か横かに応じてサイズを調整するのみ。  
あとはpromptを与えてbatchで複数枚の生成結果を待つ。  
この時点ではトレースする絵のベースを作成することをのみを目的に生成する

```
Steps: 30, Sampler: DPM++ 2M Karras, CFG scale: 7, Seed: 542581558, Size: 512x768, Model hash: 1449e5b0b9, Model: animagine-xl-3.0, Version: f0.0.17v1.8.0rc-latest-276-g29be1da7
```

## 背景生成時

Sampling stepsを20→30に変更し、縦か横かに応じてサイズを調整する。  
アニメ調の線を強調した絵を出したいので、ControlNetで`Lineart_anime_denoise`を使用する

```
Steps: 30, Sampler: DPM++ 2M Karras, CFG scale: 7, Size: 1024x512, Model hash: 1449e5b0b9, Model: animagine-xl-3.0, ControlNet 0: "Module: lineart_standard (from white bg & black line), Model: bdsqlsz_controlllite_xl_lineart_anime_denoise [74db2627], Weight: 1, Resize Mode: Crop and Resize, Processor Res: 512, Threshold A: 0.5, Threshold B: 0.5, Guidance Start: 0, Guidance End: 1, Pixel Perfect: False, Control Mode: Balanced, Hr Option: Both", Version: f0.0.17v1.8.0rc-latest-276-g29be1da7
```

# 基本パラメーター (img2img)

Sampling stepsを20→30に変更し、縦か横かに応じてサイズを調整するのみ。  
あとはpromptを与えてbatchで複数枚の生成結果を待つ。  
Denoising Strengthは後述

```
Steps: 30, Sampler: DPM++ 2M Karras, CFG scale: 7, Seed: 1019489612, Size: 768x512, Model hash: 1449e5b0b9, Model: animagine-xl-3.0, Denoising strength: 0.57, Version: f0.0.17v1.8.0rc-latest-276-g29be1da7
```

## 雑感メモ

- 1枚目は自作絵か、txt2imgの元絵のトレースをベースにする。発生しているキャラ特徴の齟齬(髪型、服装)はここで方向性をアジャストしておくほうがよい。
- 1枚目の絵が出来たら、img2imgでSketchを使う。出来栄えによりDenoising strengthを調整。
- 以降は生成絵で描きたいものが出来次第、読み込ませるソース絵として変更する。
- 明らかにこの時点で最終絵と齟齬がある場合は、手修正をしておくことでよりイメージに近いものが出来上がる。
- `sketch` と `img2img` の使い分けは曖昧、sketchは1枚目でしか使わなかったり、場合により使わなかったり。
- キャラ絵の場合2枚目か3枚目付近の時点で髪色、服色を着色する。
- トーン調にしたい箇所はペンで陰影ラインを軽めに入れておくことで次回生成で解釈される場合がある
- inpaintはUIの範囲選択が使いずらく、目線の最終調整とかそのあたりでどうしても修正しなければならない時のみ使用。

## Denoising strengthの使用範囲

AnimagineXL3.0では0.5～0.6付近が個人的に使いやすいと感じた。

0.6前後で構図から離れた解釈の結果が出てくる傾向。  
狙っている以上にキャラを可愛くしたい場合などは当たりを引くまでこの付近で回す。  
顔以外のパーツが破綻するリスクが伴うので、必要に応じて何枚か生成して顔以外のパーツとニコイチする  

0.5まで落とすと、元の構図を忠実にたもつ傾向になる  
一度起こしてある程度清書された画像を食わせるときはこっちのがよく、0.5～0.6の間の数値はバランスを見つつ微調整。  

## 画像について

効果音が入っている絵や手修正しているデータはPNG infoがないため反映していません。  
その場合、元となった画像を入れています。