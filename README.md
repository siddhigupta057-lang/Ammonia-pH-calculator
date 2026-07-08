# Ammonia-pH-calculator
Make a working link
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>ACC Ammonia Dosing — pH Calculator</title>
<style>
  :root{
    --bg: #10161f;
    --panel: #171f2b;
    --panel-2: #1c2534;
    --line: #2a3547;
    --text: #dfe6ee;
    --text-dim: #8ea0b5;
    --cyan: #35c9c1;
    --amber: #f0a83c;
    --red: #e0605a;
    --mono: 'JetBrains Mono','Consolas',monospace;
    --sans: 'Inter','Segoe UI',sans-serif;
  }
  *{box-sizing:border-box;}
  body{
    margin:0; background:var(--bg); color:var(--text);
    font-family:var(--sans); padding:28px 20px 60px;
  }
  .wrap{max-width:1080px;margin:0 auto;}
  header{margin-bottom:22px; border-bottom:1px solid var(--line); padding-bottom:16px;}
  .eyebrow{
    font-family:var(--mono); font-size:11px; letter-spacing:.14em; color:var(--cyan);
    text-transform:uppercase;
  }
  h1{font-size:22px; margin:6px 0 4px; font-weight:600; letter-spacing:-.01em;}
  .sub{color:var(--text-dim); font-size:13px; max-width:640px; line-height:1.5;}

  .grid{display:grid; grid-template-columns: 360px 1fr; gap:18px;}
  @media (max-width:820px){ .grid{grid-template-columns:1fr;} }

  .panel{
    background:var(--panel); border:1px solid var(--line); border-radius:10px; padding:18px 20px;
  }
  .panel + .panel{margin-top:18px;}
  .panel h2{
    font-size:11px; text-transform:uppercase; letter-spacing:.1em; color:var(--text-dim);
    margin:0 0 16px; font-weight:600;
  }

  .field{margin-bottom:18px;}
  .field label{display:flex; justify-content:space-between; font-size:12.5px; color:var(--text-dim); margin-bottom:6px;}
  .field label .val{font-family:var(--mono); color:var(--cyan); font-size:13px;}
  input[type=range]{
    width:100%; -webkit-appearance:none; height:4px; border-radius:2px; background:var(--line); outline:none;
  }
  input[type=range]::-webkit-slider-thumb{
    -webkit-appearance:none; width:15px; height:15px; border-radius:50%;
    background:var(--cyan); cursor:pointer; border:2px solid #0d1319;
  }
  .numrow{display:flex; gap:8px; margin-top:8px;}
  .numrow input[type=number]{
    width:100%; background:var(--panel-2); border:1px solid var(--line); color:var(--text);
    font-family:var(--mono); font-size:13px; padding:7px 8px; border-radius:6px;
  }
  select{
    width:100%; background:var(--panel-2); border:1px solid var(--line); color:var(--text);
    font-family:var(--mono); font-size:13px; padding:7px 8px; border-radius:6px;
  }
  .toggle-row{display:flex; gap:8px;}
  .toggle-row button{
    flex:1; background:var(--panel-2); border:1px solid var(--line); color:var(--text-dim);
    font-family:var(--mono); font-size:12px; padding:7px 0; border-radius:6px; cursor:pointer;
  }
  .toggle-row button.active{ border-color:var(--cyan); color:var(--cyan); background:#122029;}

  .readout-hero{
    display:flex; align-items:flex-end; gap:26px; flex-wrap:wrap;
  }
  .ph-dial{position:relative; width:190px; flex:0 0 auto;}
  .ph-big{font-family:var(--mono); font-size:64px; font-weight:600; line-height:1; color:var(--cyan);}
  .ph-big.warn{color:var(--amber);}
  .ph-big.bad{color:var(--red);}
  .ph-label{font-size:11px; letter-spacing:.1em; text-transform:uppercase; color:var(--text-dim); margin-top:6px;}
  .status-pill{
    display:inline-block; margin-top:10px; padding:4px 10px; border-radius:999px;
    font-family:var(--mono); font-size:11px; border:1px solid var(--line);
  }
  .status-pill.ok{color:var(--cyan); border-color:var(--cyan);}
  .status-pill.warn{color:var(--amber); border-color:var(--amber);}
  .status-pill.bad{color:var(--red); border-color:var(--red);}

  .metrics{display:grid; grid-template-columns:repeat(auto-fit,minmax(120px,1fr)); gap:12px; margin-top:20px;}
  .metric{background:var(--panel-2); border:1px solid var(--line); border-radius:8px; padding:10px 12px;}
  .metric .k{font-size:10.5px; color:var(--text-dim); text-transform:uppercase; letter-spacing:.08em;}
  .metric .v{font-family:var(--mono); font-size:15px; color:var(--text); margin-top:4px;}

  .chart-wrap{margin-top:22px;}
  svg{width:100%; height:auto; display:block;}
  .grid-line{stroke:var(--line); stroke-width:1;}
  .axis-text{fill:var(--text-dim); font-family:var(--mono); font-size:10px;}
  .curve{fill:none; stroke:var(--cyan); stroke-width:2;}
  .band{fill:var(--cyan); opacity:.08;}
  .marker{fill:var(--amber); stroke:#10161f; stroke-width:2;}

  .note{font-size:11.5px; color:var(--text-dim); line-height:1.6; margin-top:16px; border-top:1px solid var(--line); padding-top:12px;}
  .note b{color:var(--text);}
  footer{margin-top:26px; font-size:11px; color:var(--text-dim); text-align:center;}
</style>
</head>
<body>
<div class="wrap">

  <header>
    <div class="eyebrow">Water Chemistry · ACC Condensate</div>
    <h1>Ammonia Dosing — pH Calculator</h1>
    <div class="sub">Estimates condensate pH in an Air-Cooled Condenser (ACC) system as a function of ammonia dose and condensate temperature, using the NH₃/NH₄⁺ dissociation equilibrium and the temperature-dependent ion product of water.</div>
  </header>

  <div class="grid">

    <div class="col-left">
      <div class="panel">
        <h2>Inputs</h2>

        <div class="field">
          <label>Ammonia dose <span class="val" id="doseVal">300 ppb</span></label>
          <input type="range" id="doseSlider" min="0" max="2000" step="1" value="300">
          <div class="numrow">
            <input type="number" id="doseNum" value="300" step="1">
            <select id="doseUnit" style="max-width:110px;">
              <option value="ppb" selected>ppb (µg/L)</option>
              <option value="ppm">ppm (mg/L)</option>
            </select>
          </div>
        </div>

        <div class="field">
          <label>Condensate temperature <span class="val" id="tempVal">40 °C</span></label>
          <input type="range" id="tempSlider" min="10" max="90" step="1" value="40">
        </div>

        <div class="field">
          <label>NH₃ purity (as supplied) <span class="val" id="purityVal">100 %</span></label>
          <input type="range" id="puritySlider" min="10" max="100" step="1" value="100">
        </div>

      </div>

      <div class="panel">
        <h2>Assumptions</h2>
        <div class="note" style="margin-top:0;border-top:none;padding-top:0;">
          <b>Model:</b> NH₃ + H₂O ⇌ NH₄⁺ + OH⁻. Solved from mass balance (total dosed ammonia) and charge balance ([NH₄⁺] ≈ [OH⁻]), assuming pure ammoniated condensate with no CO₂, amines, or other ionic contaminants. Real ACC condensate carrying dissolved CO₂ will read <b>lower</b> pH than this ideal estimate for a given dose — use cation conductivity / CO₂ data to correct if available.<br><br>
          <b>Ka (NH₄⁺):</b> Bates–Pinching correlation, pKa = 2729.92/T(K) + 0.09018.<br>
          <b>Kw(T):</b> fitted from standard water ionization data, valid 0–100 °C.
        </div>
      </div>
    </div>

    <div class="col-right">
      <div class="panel">
        <h2>Result</h2>
        <div class="readout-hero">
          <div class="ph-dial">
            <div class="ph-big" id="phBig">9.20</div>
            <div class="ph-label">Estimated pH</div>
            <div class="status-pill ok" id="statusPill">WITHIN TYPICAL BAND</div>
          </div>
        </div>

        <div class="metrics">
          <div class="metric"><div class="k">Dose (as NH₃)</div><div class="v" id="mDose">300 ppb</div></div>
          <div class="metric"><div class="k">Molar conc.</div><div class="v" id="mMolar">—</div></div>
          <div class="metric"><div class="k">pKa (NH₄⁺)</div><div class="v" id="mPka">—</div></div>
          <div class="metric"><div class="k">pKw</div><div class="v" id="mPkw">—</div></div>
          <div class="metric"><div class="k">[OH⁻]</div><div class="v" id="mOH">—</div></div>
          <div class="metric"><div class="k">% ionized (NH₄⁺)</div><div class="v" id="mIon">—</div></div>
        </div>

        <div class="chart-wrap">
          <svg id="chart" viewBox="0 0 640 260" preserveAspectRatio="xMidYMid meet"></svg>
        </div>

        <div class="note">
          Typical ACC / feedwater target band for all-volatile or oxygenated treatment is commonly <b>pH 9.0–9.6</b> at 25 °C reference — confirm the exact target against your plant's water chemistry program (EPRI / OEM guidelines), since acceptable range depends on materials (copper alloys vs. all-ferrous) and treatment regime.
        </div>
      </div>
    </div>

  </div>

  <footer>Engineering estimation tool — verify against plant chemistry programme and lab pH measurement before operational use.</footer>
</div>

<script>
const doseSlider = document.getElementById('doseSlider');
const doseNum = document.getElementById('doseNum');
const doseUnit = document.getElementById('doseUnit');
const doseVal = document.getElementById('doseVal');
const tempSlider = document.getElementById('tempSlider');
const tempVal = document.getElementById('tempVal');
const puritySlider = document.getElementById('puritySlider');
const purityVal = document.getElementById('purityVal');

const phBig = document.getElementById('phBig');
const statusPill = document.getElementById('statusPill');
const mDose = document.getElementById('mDose');
const mMolar = document.getElementById('mMolar');
const mPka = document.getElementById('mPka');
const mPkw = document.getElementById('mPkw');
const mOH = document.getElementById('mOH');
const mIon = document.getElementById('mIon');

const MW_NH3 = 17.031;

function pKw(tC){
  // fitted quadratic, valid ~0-100C
  return 14.943 - 0.04163*tC + 0.0001484*tC*tC;
}
function pKa_NH4(tC){
  const T = tC + 273.15;
  return 2729.92/T + 0.09018;
}

function computePH(doseMgL, tC){
  const pkw = pKw(tC);
  const pka = pKa_NH4(tC);
  const Kw = Math.pow(10, -pkw);
  const Ka = Math.pow(10, -pka);
  const Kb = Kw/Ka;

  const C = (doseMgL/1000) / MW_NH3; // mol/L (mg/L -> g/L /MW)
  if (C <= 0) {
    return {pH: pkw/2, pkw, pka, OH: Math.sqrt(Kw), C, ion:0};
  }
  // x^2 + Kb x - Kb C = 0
  const x = (-Kb + Math.sqrt(Kb*Kb + 4*Kb*C)) / 2; // [OH-] approx [NH4+]
  const pOH = -Math.log10(x);
  const pH = pkw - pOH;
  const ionFrac = x / C * 100;
  return {pH, pkw, pka, OH:x, C, ion: ionFrac};
}

function fmtSci(x){
  if (x <= 0) return '0';
  const exp = Math.floor(Math.log10(x));
  const mant = x / Math.pow(10, exp);
  return mant.toFixed(2)+'e'+exp;
}

function drawChart(tC, purityFrac, currentDoseMgL){
  const svg = document.getElementById('chart');
  const W=640,H=260, ml=46, mr=16, mt=14, mb=32;
  const plotW = W-ml-mr, plotH = H-mt-mb;
  const maxDoseMgL = 1.5; // up to 1500 ppb range for chart
  const nPts = 60;
  let pts = [];
  for(let i=0;i<=nPts;i++){
    const doseMgL = (i/nPts)*maxDoseMgL;
    const effectiveMgL = doseMgL * purityFrac;
    const r = computePH(effectiveMgL, tC);
    pts.push({x:doseMgL, y:r.pH});
  }
  const yMin = 7.5, yMax = 10.5;
  const xScale = v => ml + (v/maxDoseMgL)*plotW;
  const yScale = v => mt + (1-(v-yMin)/(yMax-yMin))*plotH;

  let grid = '';
  for(let g=8; g<=10.5; g+=0.5){
    const y = yScale(g);
    grid += `<line class="grid-line" x1="${ml}" y1="${y}" x2="${W-mr}" y2="${y}"/>`;
    grid += `<text class="axis-text" x="${ml-8}" y="${y+3}" text-anchor="end">${g.toFixed(1)}</text>`;
  }
  for(let x=0; x<=maxDoseMgL; x+=0.25){
    const xp = xScale(x);
    grid += `<line class="grid-line" x1="${xp}" y1="${mt}" x2="${xp}" y2="${H-mb}"/>`;
    grid += `<text class="axis-text" x="${xp}" y="${H-mb+16}" text-anchor="middle">${Math.round(x*1000)}</text>`;
  }

  const bandTop = yScale(9.6), bandBot = yScale(9.0);
  const band = `<rect class="band" x="${ml}" y="${bandTop}" width="${plotW}" height="${bandBot-bandTop}"/>`;

  let path = pts.map((p,i)=> (i===0?'M':'L') + xScale(p.x).toFixed(1)+','+yScale(Math.max(yMin,Math.min(yMax,p.y))).toFixed(1)).join(' ');
  const curve = `<path class="curve" d="${path}"/>`;

  const effNow = currentDoseMgL * purityFrac;
  const rNow = computePH(effNow, tC);
  const mx = xScale(currentDoseMgL), my = yScale(Math.max(yMin,Math.min(yMax,rNow.pH)));
  const marker = `<circle class="marker" cx="${mx}" cy="${my}" r="5"/>`;

  const axisLabels = `
    <text class="axis-text" x="${ml+plotW/2}" y="${H-4}" text-anchor="middle" style="fill:#8ea0b5">Ammonia dose (ppb)</text>
    <text class="axis-text" transform="rotate(-90 12 ${mt+plotH/2})" x="12" y="${mt+plotH/2}" text-anchor="middle" style="fill:#8ea0b5">pH</text>
  `;

  svg.innerHTML = grid + band + curve + marker + axisLabels;
}

function update(){
  const unit = doseUnit.value;
  let doseInput = parseFloat(doseNum.value) || 0;
  const tC = parseFloat(tempSlider.value);
  const purity = parseFloat(puritySlider.value)/100;

  tempVal.textContent = tC + ' °C';
  purityVal.textContent = Math.round(purity*100) + ' %';
  doseVal.textContent = doseInput + ' ' + unit;

  // convert dose to mg/L (ppm) as pure NH3
  let doseMgL_asShown = unit==='ppm' ? doseInput : doseInput/1000;
  const effectiveMgL = doseMgL_asShown * purity;

  const r = computePH(effectiveMgL, tC);

  phBig.textContent = r.pH.toFixed(2);
  phBig.className = 'ph-big';
  let status = 'ok', label='WITHIN TYPICAL BAND';
  if (r.pH < 9.0){ status='warn'; label='BELOW TYPICAL BAND'; phBig.classList.add('warn'); }
  if (r.pH > 9.6){ status='warn'; label='ABOVE TYPICAL BAND'; phBig.classList.add('warn'); }
  if (r.pH < 8.5 || r.pH > 10.2){ status='bad'; label='OUT OF RANGE — REVIEW DOSE'; phBig.classList.remove('warn'); phBig.classList.add('bad'); }
  statusPill.className = 'status-pill ' + status;
  statusPill.textContent = label;

  mDose.textContent = doseInput + ' ' + unit + (purity<1 ? ' (net '+(purity*100).toFixed(0)+'%)' : '');
  mMolar.textContent = fmtSci(r.C) + ' mol/L';
  mPka.textContent = r.pka.toFixed(3);
  mPkw.textContent = r.pkw.toFixed(3);
  mOH.textContent = fmtSci(r.OH) + ' mol/L';
  mIon.textContent = r.ion.toFixed(1) + ' %';

  drawChart(tC, purity, doseMgL_asShown);
}

doseSlider.addEventListener('input', ()=>{ doseNum.value = doseSlider.value; update(); });
doseNum.addEventListener('input', ()=>{
  let v = parseFloat(doseNum.value)||0;
  if(doseUnit.value==='ppb') doseSlider.value = Math.min(2000, v);
  update();
});
doseUnit.addEventListener('change', ()=>{
  // rescale slider range for unit
  if(doseUnit.value==='ppm'){ doseSlider.max=2; doseSlider.step=0.01; if(doseNum.value>2) doseNum.value=2; }
  else { doseSlider.max=2000; doseSlider.step=1; }
  doseSlider.value = doseNum.value;
  update();
});
tempSlider.addEventListener('input', update);
puritySlider.addEventListener('input', update);

update();
</script>
</body>
</html>
