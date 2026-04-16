# BTC水平線ドロワー プロジェクト指示書

## このプロジェクトの使い方

### チャート分析を依頼する場合

チャート画像を貼り付けると、以下のフォーマットで水平線データを出力します。

**出力フォーマット:**

```
上端: [価格]
下端: [価格]

[価格 or 価格1,価格2] | [強度2.5〜5.0] | [名前] | [色]
```

**出力例:**

```
上端: 74707
下端: 69800

73238,73490 | 5.0 | 最重要レジスタンス | #FF4444
73767 | 2.5 | 上レジスタンス | #FF8C42
71416 | 3.5 | 中間ピボット | #FFD700
70968 | 3.0 | 現在値サポート | #4CAF50
70075 | 4.5 | 強サポート | #00BCD4
69500 | 3.5 | 深めのサポート | #9C27B0
```

### ドロワーを使う場合

「ドロワーを出して」と言うと、HTMLアーティファクトを表示します。

1. 画像をライブラリから選択
1. 上記フォーマットのテキストを貼り付け
1. 「水平線を描画する」を押す

-----

## チャート分析のルール

### 強度の基準

- 5.0: D1/H4/H1全てで機能、複数回反転、支持抵抗転換あり
- 4.5: H4/H1で強く機能、ラウンドナンバー近く
- 4.0: H4で機能、複数回タッチ
- 3.5: H1で機能、上位足でも意識される
- 3.0: H4で反応候補、単独では弱い
- 2.5: H1のみ、ヒゲ中心、回数少ない

### 色の割当

- レジスタンス系: #FF4444（赤）、#FF8C42（オレンジ）
- ピボット・中間: #FFD700（黄）
- サポート系: #4CAF50（緑）、#00BCD4（シアン）、#9C27B0（紫）

### 出力ルール

- 余計な説明は省いてデータだけ出す
- ラインは最大6本まで
- 必ず上端・下端の価格を含める

-----

## ドロワーHTML

「ドロワーを出して」と言われたら以下のHTMLをアーティファクトとして表示する。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0"/>
<title>BTC水平線ドロワー</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{background:#0d0d0d;color:#f0f0f0;font-family:system-ui,-apple-system,sans-serif;padding:0.75rem;min-height:100vh;}
h1{font-size:17px;font-weight:500;margin-bottom:2px;}
.sub{font-size:12px;color:#666;margin-bottom:14px;}
.step{background:#1a1a1a;border:0.5px solid rgba(255,255,255,0.1);border-radius:12px;padding:12px;margin-bottom:10px;}
.step-t{font-size:11px;color:#555;text-transform:uppercase;letter-spacing:.05em;margin-bottom:8px;display:flex;align-items:center;gap:6px;}
.step-num{background:#333;color:#aaa;border-radius:50%;width:18px;height:18px;display:flex;align-items:center;justify-content:center;font-size:10px;font-weight:600;flex-shrink:0;}
.step-num.done{background:#4CAF50;color:#fff;}
.img-drop{border:1.5px dashed rgba(255,255,255,0.18);border-radius:10px;background:#111;position:relative;text-align:center;padding:1.5rem 1rem;cursor:pointer;}
.img-drop.has{border-style:solid;border-color:rgba(255,255,255,0.2);padding:0;}
.img-drop input{position:absolute;inset:0;opacity:0;width:100%;height:100%;cursor:pointer;}
.img-drop canvas{display:block;width:100%;border-radius:9px;}
.pick-row{display:flex;gap:8px;margin-top:8px;}
.pick-btn{flex:1;background:#1a1a1a;border:0.5px solid rgba(255,255,255,0.18);border-radius:8px;padding:10px 6px;font-size:12px;font-weight:500;color:#aaa;cursor:pointer;text-align:center;position:relative;display:flex;align-items:center;justify-content:center;gap:5px;}
.pick-btn input{position:absolute;inset:0;opacity:0;width:100%;height:100%;cursor:pointer;}
.paste-area{width:100%;height:160px;background:#111;border:0.5px solid rgba(255,255,255,0.15);border-radius:8px;padding:8px 10px;font-size:12px;color:#f0f0f0;font-family:monospace;resize:vertical;line-height:1.7;}
.paste-hint{font-size:11px;color:#444;margin-bottom:6px;line-height:1.6;}
.btn-main{width:100%;background:#f0f0f0;color:#111;border:none;border-radius:10px;padding:13px;font-size:15px;font-weight:500;cursor:pointer;margin-top:10px;margin-bottom:8px;}
.btn-sub{width:100%;background:transparent;border:0.5px solid rgba(255,255,255,0.15);border-radius:10px;padding:10px;font-size:13px;color:#555;cursor:pointer;margin-bottom:10px;}
.result-img{border-radius:12px;overflow:hidden;border:0.5px solid rgba(255,255,255,0.1);margin-bottom:10px;display:none;}
.result-img.on{display:block;}
.result-img canvas{display:block;width:100%;}
.btn-dl{width:100%;background:transparent;border:0.5px solid rgba(255,255,255,0.2);border-radius:10px;padding:11px;font-size:13px;color:#aaa;cursor:pointer;margin-bottom:6px;display:none;}
.btn-dl.on{display:block;}
.legend{display:none;background:#1a1a1a;border-radius:10px;padding:10px 12px;margin-bottom:12px;}
.legend.on{display:block;}
.leg-t{font-size:11px;color:#444;text-transform:uppercase;letter-spacing:.05em;margin-bottom:8px;}
.leg-row{display:flex;align-items:flex-start;gap:10px;margin-bottom:6px;font-size:12px;}
.leg-row:last-child{margin-bottom:0;}
.err{font-size:12px;color:#FF4444;background:rgba(255,68,68,0.1);border-radius:6px;padding:6px 10px;margin-top:6px;display:none;}
.err.on{display:block;}
.ok{font-size:12px;color:#4CAF50;margin-top:4px;display:none;}
.ok.on{display:block;}
</style>
</head>
<body>
<h1>BTC水平線ドロワー</h1>
<p class="sub">画像を選択 → 水平線データを貼り付け → 描画</p>
<div class="step">
  <div class="step-t"><span class="step-num" id="sn1">1</span>チャート画像を選択</div>
  <div class="img-drop" id="imgDrop">
    <input type="file" id="fi" accept="image/*"/>
    <div id="dropHint">
      <div style="font-size:28px;margin-bottom:6px;">📊</div>
      <div style="font-size:14px;font-weight:500;margin-bottom:2px;">タップして画像を選択</div>
      <div style="font-size:12px;color:#666;">スクリーンショット・写真ライブラリから</div>
    </div>
    <canvas id="thumbCv" style="display:none;"></canvas>
  </div>
  <div class="pick-row">
    <div class="pick-btn"><input type="file" id="fl" accept="image/*"/>🖼 ライブラリ</div>
    <div class="pick-btn"><input type="file" id="fc" accept="image/*" capture="environment"/>📷 カメラ</div>
  </div>
  <div class="ok" id="imgOk">✓ 画像を読み込みました</div>
</div>
<div class="step">
  <div class="step-t"><span class="step-num" id="sn2">2</span>水平線データを貼り付け</div>
  <div class="paste-hint">Claudeが出力したテキストをそのまま貼り付け。上端・下端が含まれると自動設定。</div>
  <textarea class="paste-area" id="pasteArea" placeholder="上端: 74707&#10;下端: 69800&#10;&#10;73238,73490 | 5.0 | 最重要レジスタンス | #FF4444&#10;70075 | 4.5 | 強サポート | #00BCD4"></textarea>
  <div class="err" id="parseErr"></div>
  <div class="ok" id="parseOk"></div>
</div>
<div class="step">
  <div class="step-t"><span class="step-num" id="sn3">3</span>描画エリア調整（ズレる場合のみ）</div>
  <div style="display:flex;gap:8px;align-items:center;">
    <div style="font-size:12px;color:#555;min-width:50px;">上端px</div>
    <input id="chartTop" type="number" value="185" style="flex:1;background:#111;border:0.5px solid rgba(255,255,255,0.15);border-radius:6px;padding:7px 10px;font-size:14px;color:#f0f0f0;text-align:right;"/>
    <div style="font-size:12px;color:#555;min-width:50px;">下端px</div>
    <input id="chartBottom" type="number" value="975" style="flex:1;background:#111;border:0.5px solid rgba(255,255,255,0.15);border-radius:6px;padding:7px 10px;font-size:14px;color:#f0f0f0;text-align:right;"/>
  </div>
</div>
<button class="btn-main" id="btnDraw">水平線を描画する</button>
<div class="result-img" id="resultBox"><canvas id="cv"></canvas></div>
<button class="btn-dl" id="btnDl">📥 画像をダウンロード</button>
<div class="legend" id="legend"><div class="leg-t">凡例</div><div id="legRows"></div></div>
<button class="btn-sub" id="btnRst">リセット</button>
<script>
var thumbCv=document.getElementById('thumbCv'),tCtx=thumbCv.getContext('2d');
var cv=document.getElementById('cv'),ctx=cv.getContext('2d');
var imgEl=null;
var AC=['#FF4444','#FF8C42','#FFD700','#4CAF50','#00BCD4','#9C27B0','#2196F3','#FF69B4','#00E676','#FF6B6B'];
['fi','fl','fc'].forEach(function(id){var el=document.getElementById(id);if(el)el.addEventListener('change',function(e){if(e.target.files[0])loadFile(e.target.files[0]);});});
document.addEventListener('paste',function(e){var items=e.clipboardData&&e.clipboardData.items;if(!items)return;for(var i=0;i<items.length;i++){if(items[i].type.startsWith('image/')){loadFile(items[i].getAsFile());e.preventDefault();return;}}});
function loadFile(file){if(!file.type.startsWith('image/'))return;var r=new FileReader();r.onload=function(e){var img=new Image();img.onload=function(){imgEl=img;var drop=document.getElementById('imgDrop');var mw=drop.clientWidth||320;var s=Math.min(mw/img.width,220/img.height,1);thumbCv.width=Math.round(img.width*s);thumbCv.height=Math.round(img.height*s);tCtx.drawImage(img,0,0,thumbCv.width,thumbCv.height);thumbCv.style.display='block';document.getElementById('dropHint').style.display='none';drop.classList.add('has');document.getElementById('imgOk').classList.add('on');document.getElementById('sn1').classList.add('done');};img.src=e.target.result;};r.readAsDataURL(file);}
function parseInput(raw){var lines=raw.split('\n').map(function(l){return l.trim();}).filter(Boolean);var priceTop=null,priceBottom=null,parsed=[];var errEl=document.getElementById('parseErr');var okEl=document.getElementById('parseOk');errEl.classList.remove('on');okEl.classList.remove('on');lines.forEach(function(line,li){var topM=line.match(/上端[：:]\s*([\d.]+)/);var botM=line.match(/下端[：:]\s*([\d.]+)/);if(topM){priceTop=parseFloat(topM[1]);return;}if(botM){priceBottom=parseFloat(botM[1]);return;}if(line.startsWith('#')||line.startsWith('//'))return;if(line.indexOf('|')===-1)return;var parts=line.split('|').map(function(p){return p.trim();});if(parts.length<2)return;var price=parts[0],score=parts[1]||'3.0',name=parts[2]||('ライン'+(li+1)),color=parts[3]||AC[parsed.length%AC.length];if(!price)return;parsed.push({price:price,score:score,name:name,color:color,on:true});});if(parsed.length===0){errEl.textContent='水平線データが見つかりません。';errEl.classList.add('on');return null;}okEl.textContent='✓ '+parsed.length+'本検出'+(priceTop?' 上端:'+priceTop:'')+(priceBottom?' 下端:'+priceBottom:'');okEl.classList.add('on');document.getElementById('sn2').classList.add('done');return{lines:parsed,priceTop:priceTop,priceBottom:priceBottom};}
document.getElementById('pasteArea').addEventListener('input',function(){if(this.value.trim())parseInput(this.value);});
function p2y(p,pt,pb,ct,cb){return ct+(pt-p)/(pt-pb)*(cb-ct);}
function s2w(s){s=parseFloat(s);if(s>=5)return 4;if(s>=4.5)return 3;if(s>=4)return 2.5;if(s>=3.5)return 2;if(s>=3)return 1.5;return 1;}
function h2r(h){return{r:parseInt(h.slice(1,3),16),g:parseInt(h.slice(3,5),16),b:parseInt(h.slice(5,7),16)};}
document.getElementById('btnDraw').addEventListener('click',function(){
  if(!imgEl){alert('先に画像を選択してください');return;}
  var raw=document.getElementById('pasteArea').value.trim();
  if(!raw){alert('水平線データを貼り付けてください');return;}
  var result=parseInput(raw);if(!result)return;
  var pt=result.priceTop,pb=result.priceBottom;
  if(!pt||!pb){var allP=[];result.lines.forEach(function(l){l.price.split(',').forEach(function(p){var v=parseFloat(p.trim());if(!isNaN(v))allP.push(v);});});if(allP.length){if(!pt)pt=Math.max.apply(null,allP)*1.015;if(!pb)pb=Math.min.apply(null,allP)*0.985;}}
  var ct=parseFloat(document.getElementById('chartTop').value)||185;
  var cb=parseFloat(document.getElementById('chartBottom').value)||975;
  var mw=document.body.clientWidth-24,sc=Math.min(mw/imgEl.width,1);
  cv.width=Math.round(imgEl.width*sc);cv.height=Math.round(imgEl.height*sc);
  ctx.drawImage(imgEl,0,0,cv.width,cv.height);
  var isc=cv.width/imgEl.width,ctS=ct*isc,cbS=cb*isc;
  var lr=document.getElementById('legRows');lr.innerHTML='';
  result.lines.forEach(function(l){
    var ps=l.price.split(',').map(function(p){return parseFloat(p.trim());}).filter(function(p){return !isNaN(p);});
    if(!ps.length)return;
    var col=l.color,rgb=h2r(col),lw=s2w(l.score),zone=ps.length>=2;
    if(zone){var y1=p2y(Math.max(ps[0],ps[1]),pt,pb,ctS,cbS),y2=p2y(Math.min(ps[0],ps[1]),pt,pb,ctS,cbS);ctx.save();ctx.fillStyle='rgba('+rgb.r+','+rgb.g+','+rgb.b+',0.13)';ctx.fillRect(0,y1,cv.width-130*isc,y2-y1);ctx.strokeStyle=col;ctx.lineWidth=lw;ctx.globalAlpha=0.9;ctx.setLineDash([]);ctx.beginPath();ctx.moveTo(0,y1);ctx.lineTo(cv.width-130*isc,y1);ctx.stroke();ctx.beginPath();ctx.moveTo(0,y2);ctx.lineTo(cv.width-130*isc,y2);ctx.stroke();ctx.restore();drawLbl(l.name+' ('+l.price+')',(y1+y2)/2,col,isc);addLeg(lr,l,col,true);}
    else{var y=p2y(ps[0],pt,pb,ctS,cbS);ctx.save();ctx.strokeStyle=col;ctx.lineWidth=lw;ctx.globalAlpha=0.9;if(parseFloat(l.score)<=3)ctx.setLineDash([10,6]);ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(cv.width-130*isc,y);ctx.stroke();ctx.setLineDash([]);ctx.restore();drawLbl(l.name+' '+l.price,y,col,isc);addLeg(lr,l,col,false);}
  });
  document.getElementById('resultBox').classList.add('on');
  document.getElementById('btnDl').classList.add('on');
  document.getElementById('legend').classList.add('on');
  document.getElementById('resultBox').scrollIntoView({behavior:'smooth'});
});
function drawLbl(txt,y,col,sc){var fs=Math.max(10,Math.round(13*sc));ctx.save();ctx.font='bold '+fs+'px system-ui';var tw=ctx.measureText(txt).width,pd=4*sc,th=fs+2,lx=(cv.width-130*sc)+6*sc,ly=y-th/2;ctx.fillStyle='rgba(0,0,0,0.82)';ctx.fillRect(lx-pd,ly-pd,tw+pd*2,th+pd*2);ctx.fillStyle=col;ctx.fillText(txt,lx,ly+th-2);ctx.restore();}
function addLeg(c,l,col,zone){var d=document.createElement('div');d.className='leg-row';d.innerHTML='<div style="padding-top:4px;flex-shrink:0;"><div style="width:26px;height:'+(zone?12:2)+'px;background:'+col+';border-radius:2px;'+(zone?'':'margin-top:4px;')+'"></div></div><div style="flex:1;"><div style="font-size:12px;font-weight:500;color:#f0f0f0;">'+l.price+' <span style="color:#555;font-size:11px;">'+l.score+'/5</span></div><div style="font-size:11px;color:#444;">'+l.name+'</div></div>';c.appendChild(d);}
document.getElementById('btnDl').addEventListener('click',function(){var a=document.createElement('a');a.download='btc_chart.png';a.href=cv.toDataURL('image/png');a.click();});
document.getElementById('btnRst').addEventListener('click',function(){imgEl=null;thumbCv.style.display='none';document.getElementById('dropHint').style.display='';document.getElementById('imgDrop').classList.remove('has');['imgOk','parseOk','parseErr'].forEach(function(id){document.getElementById(id).classList.remove('on');});['sn1','sn2','sn3'].forEach(function(id){document.getElementById(id).classList.remove('done');});document.getElementById('pasteArea').value='';document.getElementById('resultBox').classList.remove('on');document.getElementById('btnDl').classList.remove('on');document.getElementById('legend').classList.remove('on');['fi','fl','fc'].forEach(function(id){var el=document.getElementById(id);if(el)el.value='';});});
</script>
</body>
</html>
```
