<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Stock Validation Tool</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:'Segoe UI',system-ui,sans-serif;background:#f4f5f7;color:#1a1a2e;min-height:100vh;}
.header{background:#1a1a2e;color:white;padding:1rem 2rem;display:flex;align-items:center;justify-content:space-between;}
.header-left h1{font-size:1.2rem;font-weight:700;letter-spacing:-0.3px;}
.header-left p{font-size:0.75rem;color:#8b9bb4;margin-top:2px;}
.header-badge{font-size:0.7rem;background:#2d3a5a;color:#8b9bb4;padding:4px 10px;border-radius:20px;}
.steps-bar{background:white;border-bottom:1px solid #e5e7eb;display:flex;padding:0 2rem;}
.step-tab{display:flex;align-items:center;gap:8px;padding:0.875rem 1.25rem;font-size:0.82rem;color:#9ca3af;border-bottom:2px solid transparent;cursor:default;white-space:nowrap;}
.step-tab.active{color:#1a1a2e;border-bottom-color:#1a1a2e;font-weight:600;}
.step-tab.done{color:#16a34a;}
.step-num{width:20px;height:20px;border-radius:50%;background:#e5e7eb;display:flex;align-items:center;justify-content:center;font-size:0.7rem;font-weight:700;flex-shrink:0;}
.step-tab.active .step-num{background:#1a1a2e;color:white;}
.step-tab.done .step-num{background:#16a34a;color:white;}
.content{padding:1.75rem 2rem;max-width:1100px;margin:0 auto;}
.section-label{font-size:0.78rem;color:#6b7280;margin-bottom:1.25rem;line-height:1.5;}

/* Upload */
.upload-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:1rem;}
.upload-card{background:white;border:1.5px dashed #d1d5db;border-radius:12px;padding:1.5rem 1.25rem;text-align:center;cursor:pointer;transition:all .2s;position:relative;}
.upload-card:hover{border-color:#4f46e5;background:#fafafe;}
.upload-card.loaded{border-style:solid;border-color:#16a34a;background:#f0fdf4;}
.upload-card.err{border-color:#dc2626;background:#fef2f2;}
.upload-icon{font-size:1.75rem;margin-bottom:.5rem;}
.upload-title{font-size:.875rem;font-weight:600;color:#374151;margin-bottom:.2rem;}
.upload-sub{font-size:.72rem;color:#9ca3af;line-height:1.5;}
.upload-ok{font-size:.72rem;color:#16a34a;margin-top:.5rem;font-weight:600;}
.upload-err{font-size:.72rem;color:#dc2626;margin-top:.5rem;}
input[type=file]{display:none;}

/* Mapping */
.map-block{background:white;border:1px solid #e5e7eb;border-radius:12px;margin-bottom:.875rem;overflow:hidden;}
.map-head{padding:.875rem 1.25rem;background:#f9fafb;border-bottom:1px solid #e5e7eb;font-size:.85rem;font-weight:600;color:#374151;display:flex;align-items:center;gap:.5rem;}
.map-body{padding:1.125rem 1.25rem;display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:.875rem;}
.field-wrap{display:flex;flex-direction:column;gap:.3rem;}
.field-label{font-size:.75rem;color:#6b7280;font-weight:500;}
.field-select{padding:.45rem .7rem;border:1px solid #d1d5db;border-radius:8px;font-size:.82rem;color:#374151;background:white;outline:none;width:100%;}
.field-select:focus{border-color:#4f46e5;}
.field-select.auto-detected{border-color:#16a34a;background:#f0fdf4;}

/* Summary */
.summary-row{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:.75rem;margin-bottom:1.25rem;}
.scard{background:white;border-radius:10px;padding:.875rem 1rem;border:1px solid #e5e7eb;}
.scard-label{font-size:.7rem;color:#6b7280;margin-bottom:.3rem;}
.scard-value{font-size:1.6rem;font-weight:700;}
.c-blue{color:#2563eb;}.c-green{color:#16a34a;}.c-amber{color:#d97706;}.c-red{color:#dc2626;}.c-indigo{color:#4f46e5;}.c-purple{color:#7c3aed;}

/* Filters */
.topbar{display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:.75rem;margin-bottom:1rem;}
.filters{display:flex;gap:.4rem;flex-wrap:wrap;}
.fbtn{padding:.3rem .8rem;border-radius:20px;border:1px solid #d1d5db;background:white;font-size:.75rem;cursor:pointer;transition:all .15s;white-space:nowrap;}
.fbtn:hover{border-color:#4f46e5;}
.fbtn.on{background:#1a1a2e;color:white;border-color:#1a1a2e;}
.right-tools{display:flex;gap:.6rem;align-items:center;}
.search-box{padding:.4rem .75rem;border:1px solid #d1d5db;border-radius:8px;font-size:.82rem;outline:none;width:200px;}
.search-box:focus{border-color:#4f46e5;}

/* Table */
.table-wrap{background:white;border-radius:12px;border:1px solid #e5e7eb;overflow:auto;}
table{width:100%;border-collapse:collapse;font-size:.78rem;min-width:960px;}
thead{background:#f9fafb;position:sticky;top:0;z-index:1;}
th{padding:.6rem .875rem;text-align:left;font-weight:600;color:#4b5563;border-bottom:1px solid #e5e7eb;white-space:nowrap;font-size:.72rem;}
td{padding:.45rem .875rem;border-bottom:.5px solid #f3f4f6;vertical-align:middle;}
tbody tr:hover td{background:#f9fafb;}
tr.ok td{background:#f0fdf4 !important;}
tr.status td{background:#fffbeb !important;}
tr.stock td{background:#fff7ed !important;}
tr.impact td{background:#fef2f2 !important;}
tr.import td{background:#eff6ff !important;}
tr.missing td{background:#faf5ff !important;}
.sku-cell{font-weight:600;font-family:monospace;font-size:.8rem;}
.badge{display:inline-block;padding:2px 7px;border-radius:4px;font-size:.68rem;font-weight:700;white-space:nowrap;}
.b-ok{background:#dcfce7;color:#166534;}
.b-status{background:#fef3c7;color:#92400e;}
.b-stock{background:#ffedd5;color:#9a3412;}
.b-impact{background:#fee2e2;color:#991b1b;}
.b-import{background:#dbeafe;color:#1e40af;}
.b-missing{background:#f3e8ff;color:#6b21a8;}
.check-ok{color:#16a34a;font-size:1rem;}
.check-no{color:#dc2626;font-size:1rem;}
.diff-pos{color:#d97706;font-weight:600;}
.diff-neg{color:#dc2626;font-weight:600;}
.diff-zero{color:#9ca3af;}
.empty-state{text-align:center;padding:3rem;color:#9ca3af;}

/* Buttons */
.btn{padding:.55rem 1.1rem;border-radius:8px;font-size:.82rem;font-weight:600;cursor:pointer;border:none;transition:all .15s;display:inline-flex;align-items:center;gap:.4rem;}
.btn-primary{background:#1a1a2e;color:white;}
.btn-primary:hover{background:#2d2d44;}
.btn-primary:disabled{background:#9ca3af;cursor:not-allowed;}
.btn-outline{background:white;color:#374151;border:1px solid #d1d5db;}
.btn-outline:hover{background:#f9fafb;}
.btn-green{background:#16a34a;color:white;}
.btn-green:hover{background:#15803d;}
.nav-row{display:flex;justify-content:space-between;align-items:center;margin-top:1.5rem;}
.row-count{font-size:.75rem;color:#9ca3af;}
</style>
</head>
<body>

<div class="header">
  <div class="header-left">
    <h1>📦 Stock Validation Tool</h1>
    <p>WMS &nbsp;·&nbsp; TurboCharger &nbsp;·&nbsp; Marketplace</p>
  </div>
  <div class="header-badge">All processing is local — no data leaves your browser</div>
</div>

<div class="steps-bar">
  <div class="step-tab active" id="tab1"><span class="step-num">1</span> Upload Files</div>
  <div class="step-tab" id="tab2"><span class="step-num">2</span> Map Columns</div>
  <div class="step-tab" id="tab3"><span class="step-num">3</span> Results</div>
</div>

<div class="content">

  <!-- PAGE 1: Upload -->
  <div id="p1">
    <p class="section-label">Upload the 4 daily reports below. ZIP files for MP will be extracted automatically.</p>
    <div class="upload-grid">

      <div class="upload-card" id="z-wms" onclick="document.getElementById('f-wms').click()">
        <div class="upload-icon">📊</div>
        <div class="upload-title">WMS Report</div>
        <div class="upload-sub">Sales / Availability Stock<br>CSV or Excel (.xlsx / .xls)</div>
        <div class="upload-ok" id="ok-wms"></div>
        <div class="upload-err" id="err-wms"></div>
        <input type="file" id="f-wms" accept=".csv,.xlsx,.xls" onchange="loadFile('wms',this)">
      </div>

      <div class="upload-card" id="z-tcm" onclick="document.getElementById('f-tcm').click()">
        <div class="upload-icon">🗃️</div>
        <div class="upload-title">TC Product Master</div>
        <div class="upload-sub">Master Stock + Reserved Stock<br>CSV or Excel (.xlsx / .xls)</div>
        <div class="upload-ok" id="ok-tcm"></div>
        <div class="upload-err" id="err-tcm"></div>
        <input type="file" id="f-tcm" accept=".csv,.xlsx,.xls" onchange="loadFile('tcm',this)">
      </div>

      <div class="upload-card" id="z-tci" onclick="document.getElementById('f-tci').click()">
        <div class="upload-icon">📋</div>
        <div class="upload-title">TC Inventory</div>
        <div class="upload-sub">Status Report<br>CSV or Excel (.xlsx / .xls)</div>
        <div class="upload-ok" id="ok-tci"></div>
        <div class="upload-err" id="err-tci"></div>
        <input type="file" id="f-tci" accept=".csv,.xlsx,.xls" onchange="loadFile('tci',this)">
      </div>

      <div class="upload-card" id="z-mp" onclick="document.getElementById('f-mp').click()">
        <div class="upload-icon">🛒</div>
        <div class="upload-title">MP Price &amp; Stock</div>
        <div class="upload-sub">ZIP (auto-extracted), CSV or Excel<br>.zip / .csv / .xlsx / .xls</div>
        <div class="upload-ok" id="ok-mp"></div>
        <div class="upload-err" id="err-mp"></div>
        <input type="file" id="f-mp" accept=".csv,.xlsx,.xls,.zip" onchange="loadFile('mp',this)">
      </div>

    </div>
    <div class="nav-row">
      <span class="row-count" id="load-status">Upload all 4 files to continue.</span>
      <button class="btn btn-primary" id="btn1" onclick="goMap()" disabled>Next: Map Columns →</button>
    </div>
  </div>

  <!-- PAGE 2: Mapping -->
  <div id="p2" style="display:none">
    <p class="section-label">Match the columns from each report to the right field. Green borders = auto-detected. Adjust if needed.</p>

    <div class="map-block">
      <div class="map-head">🛒 Marketplace (MP) Report</div>
      <div class="map-body" id="mb-mp"></div>
    </div>
    <div class="map-block">
      <div class="map-head">🗃️ TC Product Master Report</div>
      <div class="map-body" id="mb-tcm"></div>
    </div>
    <div class="map-block">
      <div class="map-head">📋 TC Inventory Report</div>
      <div class="map-body" id="mb-tci"></div>
    </div>
    <div class="map-block">
      <div class="map-head">📊 WMS Report</div>
      <div class="map-body" id="mb-wms"></div>
    </div>

    <div class="nav-row">
      <button class="btn btn-outline" onclick="goPage(1)">← Back</button>
      <button class="btn btn-primary" onclick="runValidation()">▶ Run Validation</button>
    </div>
  </div>

  <!-- PAGE 3: Results -->
  <div id="p3" style="display:none">
    <div class="summary-row" id="summary"></div>

    <div class="topbar">
      <div class="filters">
        <button class="fbtn on" onclick="filt('all',this)">All</button>
        <button class="fbtn" onclick="filt('ok',this)">✅ All Match</button>
        <button class="fbtn" onclick="filt('status',this)">⚠️ Status Mismatch</button>
        <button class="fbtn" onclick="filt('stock',this)">📦 Stock Mismatch</button>
        <button class="fbtn" onclick="filt('import',this)">🔵 Need Import</button>
        <button class="fbtn" onclick="filt('missing',this)">🔴 SKU Missing</button>
      </div>
      <div class="right-tools">
        <input type="text" class="search-box" placeholder="🔍 Search SKU..." oninput="doSearch(this.value)">
        <button class="btn btn-green" onclick="dlExcel()">⬇ Download Excel</button>
        <button class="btn btn-outline" onclick="goPage(1)">↩ New Run</button>
      </div>
    </div>

    <div class="table-wrap">
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>MP SKU / EAN</th>
            <th>MP Qty</th>
            <th>MP Status</th>
            <th>TC All Qty</th>
            <th>TC Reserved</th>
            <th>TC Status</th>
            <th>WMS Qty</th>
            <th>Qty Check</th>
            <th>Status Check</th>
            <th>Qty Diff</th>
            <th>Remarks</th>
          </tr>
        </thead>
        <tbody id="tbody"></tbody>
      </table>
      <div class="empty-state" id="empty" style="display:none">No records match the current filter.</div>
    </div>
    <p class="row-count" id="row-count" style="margin-top:.5rem;text-align:right;"></p>
  </div>

</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<script>
const S = {
  data:{wms:[],tcm:[],tci:[],mp:[]},
  hdrs:{wms:[],tcm:[],tci:[],mp:[]},
  loaded:{wms:false,tcm:false,tci:false,mp:false},
  results:[],
  filter:'all',
  query:''
};

// ── File loading ──────────────────────────────────────────────
async function loadFile(key, inp) {
  const file = inp.files[0];
  if (!file) return;
  try {
    let buf, fname;
    if (file.name.toLowerCase().endsWith('.zip')) {
      const zip = await JSZip.loadAsync(file);
      let found = null;
      zip.forEach((path, entry) => {
        if (!found && !entry.dir) {
          const p = path.toLowerCase();
          if (p.endsWith('.csv')||p.endsWith('.xlsx')||p.endsWith('.xls')) found = {path, entry};
        }
      });
      if (!found) throw new Error('No CSV/Excel found inside ZIP.');
      buf = await found.entry.async('arraybuffer');
      fname = found.path;
    } else {
      buf = await file.arrayBuffer();
      fname = file.name;
    }
    const {rows, hdrs} = parseBuf(buf, fname);
    S.data[key] = rows;
    S.hdrs[key] = hdrs;
    S.loaded[key] = true;
    document.getElementById('z-'+key).classList.add('loaded');
    document.getElementById('ok-'+key).textContent = '✓ ' + file.name + ' (' + rows.length + ' rows)';
    document.getElementById('err-'+key).textContent = '';
  } catch(e) {
    S.loaded[key] = false;
    document.getElementById('z-'+key).classList.add('err');
    document.getElementById('err-'+key).textContent = '✗ ' + e.message;
  }
  refreshStatus();
}

function parseBuf(buf, fname) {
  const wb = XLSX.read(buf, {type:'array', raw:false});
  const ws = wb.Sheets[wb.SheetNames[0]];
  const rows = XLSX.utils.sheet_to_json(ws, {defval:''});
  const hdrs = rows.length ? Object.keys(rows[0]) : [];
  return {rows, hdrs};
}

function refreshStatus() {
  const n = Object.values(S.loaded).filter(Boolean).length;
  const ready = n === 4;
  document.getElementById('btn1').disabled = !ready;
  document.getElementById('load-status').textContent = ready
    ? '✅ All files loaded — ready to continue!'
    : n + '/4 files loaded.';
}

// ── Navigation ────────────────────────────────────────────────
function goPage(n) {
  [1,2,3].forEach(i => {
    document.getElementById('p'+i).style.display = i===n ? 'block':'none';
    const t = document.getElementById('tab'+i);
    t.classList.remove('active','done');
    if (i===n) t.classList.add('active');
    else if (i<n) t.classList.add('done');
  });
  window.scrollTo(0,0);
}

function goMap() {
  buildMapUI();
  goPage(2);
}

// ── Column mapping UI ─────────────────────────────────────────
const FIELDS = {
  mp:[
    {k:'sku',  label:'Seller SKU / EAN',   hints:['sku','ean','seller_sku','sellersku','asin','product_id','item_id','barcode']},
    {k:'qty',  label:'Stock Quantity',      hints:['qty','stock','quantity','available','inventory','count']},
    {k:'status',label:'Listing Status',    hints:['status','listing','active','state','enabled']}
  ],
  tcm:[
    {k:'sku',       label:'Seller SKU / EAN',  hints:['sku','ean','seller_sku','sellersku','product_id','item_id','barcode']},
    {k:'allQty',    label:'All / Master Qty',   hints:['all','total','master','qty','stock','quantity','available']},
    {k:'reservedQty',label:'Reserved Qty',      hints:['reserved','reserve','campaign','hold','promo']}
  ],
  tci:[
    {k:'sku',   label:'Seller SKU / EAN', hints:['sku','ean','seller_sku','sellersku','product_id','item_id','barcode']},
    {k:'status',label:'Inventory Status', hints:['status','state','active','listing','enabled']}
  ],
  wms:[
    {k:'sku',label:'Seller SKU / EAN',      hints:['sku','ean','seller_sku','sellersku','product_id','item_id','barcode']},
    {k:'qty',label:'Available Stock Qty',   hints:['available','qty','stock','quantity','inventory','count']}
  ]
};

const CONTAINERS = {mp:'mb-mp', tcm:'mb-tcm', tci:'mb-tci', wms:'mb-wms'};

function detect(hdrs, hints) {
  const lc = hdrs.map(h => h.toLowerCase());
  for (const hint of hints) {
    const i = lc.findIndex(h => h.includes(hint));
    if (i !== -1) return hdrs[i];
  }
  return '';
}

function buildMapUI() {
  for (const [src, cid] of Object.entries(CONTAINERS)) {
    const hdrs = S.hdrs[src];
    const fields = FIELDS[src];
    document.getElementById(cid).innerHTML = fields.map(f => {
      const auto = detect(hdrs, f.hints);
      const opts = ['', ...hdrs].map(h =>
        `<option value="${esc(h)}" ${h===auto?'selected':''}>${h || '— select —'}</option>`
      ).join('');
      return `<div class="field-wrap">
        <label class="field-label">${f.label}</label>
        <select class="field-select ${auto?'auto-detected':''}" id="m-${src}-${f.k}" onchange="this.classList.remove('auto-detected')">${opts}</select>
      </div>`;
    }).join('');
  }
}

function esc(s){ return String(s).replace(/"/g,'&quot;'); }

function getMap() {
  const m = {};
  for (const [src, fields] of Object.entries(FIELDS)) {
    m[src] = {};
    for (const f of fields) {
      const el = document.getElementById(`m-${src}-${f.k}`);
      m[src][f.k] = el ? el.value : '';
    }
  }
  return m;
}

// ── Validation ────────────────────────────────────────────────
function gv(row, col) { return col && row ? String(row[col]??'').trim() : ''; }
function gn(row, col) { const v = gv(row, col); return parseFloat(v)||0; }

function runValidation() {
  const m = getMap();
  const tcmMap = {}, tciMap = {}, wmsMap = {};

  for (const r of S.data.tcm) { const s = gv(r, m.tcm.sku); if(s) tcmMap[s]=r; }
  for (const r of S.data.tci) { const s = gv(r, m.tci.sku); if(s) tciMap[s]=r; }
  for (const r of S.data.wms) { const s = gv(r, m.wms.sku); if(s) wmsMap[s]=r; }

  const results = [];
  const seen = new Set();

  // Primary: iterate MP
  for (const mpR of S.data.mp) {
    const sku = gv(mpR, m.mp.sku);
    if (!sku) continue;
    seen.add(sku);
    const mpQty   = gn(mpR, m.mp.qty);
    const mpStat  = gv(mpR, m.mp.status);
    const tcmR    = tcmMap[sku] || null;
    const tciR    = tciMap[sku] || null;
    const wmsR    = wmsMap[sku] || null;
    results.push(validate({sku, mpQty, mpStat, tcmR, tciR, wmsR, m, hasMp:true}));
  }

  // Secondary: WMS-only SKUs
  for (const wmsR of S.data.wms) {
    const sku = gv(wmsR, m.wms.sku);
    if (!sku || seen.has(sku)) continue;
    seen.add(sku);
    const tcmR = tcmMap[sku] || null;
    const tciR = tciMap[sku] || null;
    results.push(validate({sku, mpQty:null, mpStat:null, tcmR, tciR, wmsR, m, hasMp:false}));
  }

  S.results = results;
  S.filter  = 'all';
  S.query   = '';
  renderSummary();
  renderTable();
  goPage(3);
}

function validate({sku, mpQty, mpStat, tcmR, tciR, wmsR, m, hasMp}) {
  const tcAllQty  = tcmR ? gn(tcmR, m.tcm.allQty)      : null;
  const tcReserv  = tcmR ? gn(tcmR, m.tcm.reservedQty) : 0;
  const tcStat    = tciR ? gv(tciR, m.tci.status)       : null;
  const wmsQty    = wmsR ? gn(wmsR, m.wms.qty)          : null;

  const hasTc  = tcmR !== null || tciR !== null;
  const hasWms = wmsR !== null;

  const base = {sku, mpQty, mpStat, tcAllQty, tcReserv, tcStat, wmsQty};

  // Step 7 — WMS only, not in MP or TC
  if (hasWms && !hasMp && !hasTc)
    return {...base, qtyOk:false, statOk:false, diff:0,
      remarks:'SKU Missing — Create in TC & MP', cls:'missing', badge:'missing'};

  // Step 6 — in WMS & MP but not in TC, MP Active
  if (!hasTc && hasMp && (mpStat||'').toLowerCase()==='active')
    return {...base, qtyOk:false, statOk:false, diff:0,
      remarks:'Import — SKU needs to be added to TurboCharger', cls:'import', badge:'import'};

  const wq = wmsQty  !== null ? wmsQty  : 0;
  const tq = tcAllQty!== null ? tcAllQty: 0;
  const mq = mpQty   !== null ? mpQty   : 0;
  const ts = (tcStat ||'').toLowerCase();
  const ms = (mpStat ||'').toLowerCase();

  const stockOk  = (wq === tq && tq === mq);
  const statusOk = ts !== '' && ms !== '' && ts === ms;
  const diff = wq - mq;

  // Step 1 — All match
  if (stockOk && statusOk)
    return {...base, qtyOk:true, statOk:true, diff:0,
      remarks:'No action needed', cls:'ok', badge:'ok'};

  // Step 2/4 — Stock OK, Status mismatch (TC is source of truth)
  if (stockOk && !statusOk) {
    let rem = '';
    if (ts==='inactive' && ms==='inactive')
      rem = 'Both Inactive — Activate to sync stock';
    else if (ts==='inactive')
      rem = 'TC Inactive — Activate in TC to sync';
    else
      rem = 'Status mismatch — Change to Active';
    return {...base, qtyOk:true, statOk:false, diff:0,
      remarks:rem, cls:'status', badge:'status'};
  }

  // Step 3/5 — Stock mismatch (WMS is source of truth)
  if (!stockOk) {
    const tcEff = tq - tcReserv;
    let rem = '';
    if (tcEff === mq)
      rem = 'Reserved qty causing gap — Align TC & MP to WMS';
    else
      rem = 'Impact — Investigate: API sync, reserved qty, or WMS delay';
    return {...base, qtyOk:false, statOk:statusOk, diff,
      remarks:rem, cls:'stock', badge:'stock'};
  }

  return {...base, qtyOk:false, statOk:false, diff,
    remarks:'Review manually', cls:'impact', badge:'stock'};
}

// ── Render ────────────────────────────────────────────────────
function renderSummary() {
  const all = S.results;
  const cnt = b => all.filter(r=>r.badge===b).length;
  document.getElementById('summary').innerHTML = `
    <div class="scard"><div class="scard-label">Total SKUs</div><div class="scard-value c-blue">${all.length}</div></div>
    <div class="scard"><div class="scard-label">✅ All Match</div><div class="scard-value c-green">${cnt('ok')}</div></div>
    <div class="scard"><div class="scard-label">⚠️ Status Mismatch</div><div class="scard-value c-amber">${cnt('status')}</div></div>
    <div class="scard"><div class="scard-label">📦 Stock Mismatch</div><div class="scard-value c-red">${cnt('stock')}</div></div>
    <div class="scard"><div class="scard-label">🔵 Need Import</div><div class="scard-value c-indigo">${cnt('import')}</div></div>
    <div class="scard"><div class="scard-label">🔴 SKU Missing</div><div class="scard-value c-purple">${cnt('missing')}</div></div>
  `;
}

const BADGE_HTML = {
  ok:     '<span class="badge b-ok">✓ Match</span>',
  status: '<span class="badge b-status">⚠ Status</span>',
  stock:  '<span class="badge b-stock">📦 Stock</span>',
  import: '<span class="badge b-import">↑ Import</span>',
  missing:'<span class="badge b-missing">✗ Missing</span>'
};

function renderTable() {
  let rows = S.results;
  if (S.filter !== 'all') rows = rows.filter(r => r.badge === S.filter);
  if (S.query)            rows = rows.filter(r => (r.sku||'').toLowerCase().includes(S.query));

  const tbody = document.getElementById('tbody');
  const empty = document.getElementById('empty');

  if (!rows.length) {
    tbody.innerHTML = '';
    empty.style.display = 'block';
    document.getElementById('row-count').textContent = '';
    return;
  }
  empty.style.display = 'none';
  document.getElementById('row-count').textContent = `Showing ${rows.length} of ${S.results.length} SKUs`;

  tbody.innerHTML = rows.map((r,i) => {
    const diff = r.diff;
    const dCls = diff > 0 ? 'diff-pos' : diff < 0 ? 'diff-neg' : 'diff-zero';
    const dStr = diff === 0 ? '0' : (diff > 0 ? '+'+diff : diff);
    return `<tr class="${r.cls}">
      <td style="color:#9ca3af;font-size:.72rem">${i+1}</td>
      <td class="sku-cell">${r.sku||'—'}</td>
      <td>${r.mpQty!==null?r.mpQty:'—'}</td>
      <td>${r.mpStat||'—'}</td>
      <td>${r.tcAllQty!==null?r.tcAllQty:'—'}</td>
      <td>${r.tcReserv!==null?r.tcReserv:'—'}</td>
      <td>${r.tcStat||'—'}</td>
      <td>${r.wmsQty!==null?r.wmsQty:'—'}</td>
      <td>${r.qtyOk?'<span class="check-ok">✅</span>':'<span class="check-no">❌</span>'}</td>
      <td>${r.statOk?'<span class="check-ok">✅</span>':'<span class="check-no">❌</span>'}</td>
      <td class="${dCls}">${dStr}</td>
      <td>${BADGE_HTML[r.badge]||''} <span style="color:#6b7280">${r.remarks}</span></td>
    </tr>`;
  }).join('');
}

function filt(f, btn) {
  S.filter = f;
  document.querySelectorAll('.fbtn').forEach(b => b.classList.remove('on'));
  btn.classList.add('on');
  renderTable();
}

function doSearch(q) {
  S.query = q.toLowerCase();
  renderTable();
}

// ── Excel download ────────────────────────────────────────────
function dlExcel() {
  const rows = S.results.map(r => ({
    'MP SKU':       r.sku||'',
    'MP Qty':       r.mpQty??'',
    'MP Status':    r.mpStat||'',
    'TC All Qty':   r.tcAllQty??'',
    'TC Reserved':  r.tcReserv??'',
    'TC Status':    r.tcStat||'',
    'WMS Qty':      r.wmsQty??'',
    'Qty Check':    r.qtyOk?'TRUE':'FALSE',
    'Status Check': r.statOk?'TRUE':'FALSE',
    'Qty Difference': r.diff,
    'Remarks':      r.remarks||''
  }));
  const ws = XLSX.utils.json_to_sheet(rows);
  // Column widths
  ws['!cols'] = [14,16,10,12,10,12,10,10,10,13,40].map(w=>({wch:w}));
  const wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, 'Stock Validation');
  const d = new Date().toISOString().slice(0,10);
  XLSX.writeFile(wb, `Stock_Validation_${d}.xlsx`);
}
</script>
</body>
</html>