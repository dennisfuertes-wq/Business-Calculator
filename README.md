<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Finance Calculator — ROI · Revenue · Commission · Savings</title>
  <style>
    :root{
      --bg:#f5f7fb;
      --card:#ffffff;
      --accent:#2563eb;
      --muted:#6b7280;
      --success:#10b981;
      --danger:#ef4444;
      --radius:12px;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      background:linear-gradient(180deg,#eef2ff 0%,var(--bg) 100%);
      color:#111827;
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      padding:24px;
    }
    .card{
      width:100%;
      max-width:920px;
      background:var(--card);
      border-radius:var(--radius);
      box-shadow:0 8px 30px rgba(17,24,39,0.08);
      padding:20px;
      display:grid;
      grid-template-columns: 320px 1fr;
      gap:20px;
    }
    .sidebar{
      border-right:1px solid #eef2ff;
      padding-right:16px;
    }
    h1{font-size:18px;margin:0 0 8px 0}
    p.lead{margin:0 0 14px 0;color:var(--muted);font-size:13px}
    select, input, button{
      width:100%;
      padding:10px 12px;
      border-radius:10px;
      border:1px solid #e6edf9;
      font-size:14px;
      outline:none;
    }
    label{display:block;font-size:13px;margin:12px 0 6px;color:var(--muted)}
    .calc-list{display:flex;flex-direction:column;gap:8px}
    .calc-item{
      display:flex;align-items:center;gap:10px;padding:10px;border-radius:9px;cursor:pointer;
      border:1px solid transparent;
    }
    .calc-item:hover{background:#f8fafc}
    .calc-item.active{background:linear-gradient(90deg,rgba(37,99,235,0.06),rgba(59,130,246,0.03));border-color:rgba(37,99,235,0.12)}
    .content{padding-left:8px}
    .formula{font-weight:600;color:#0f172a}
    .muted{color:var(--muted);font-size:13px}
    .main{
      padding-left:12px;
      display:flex;
      flex-direction:column;
      gap:12px;
    }
    .inputs-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:12px}
    .actions{display:flex;gap:8px;margin-top:6px}
    .btn{background:var(--accent);color:white;border:none}
    .btn.ghost{background:transparent;color:var(--accent);border:1px solid rgba(37,99,235,0.12)}
    .result{
      margin-top:6px;padding:12px;border-radius:10px;background:#f8fafc;border:1px solid #e6f0ff;
      display:flex;align-items:center;justify-content:space-between;gap:12px;
    }
    .result .value{font-weight:700;font-size:18px}
    .history{margin-top:8px;max-height:180px;overflow:auto;padding:8px;border-radius:8px;background:#fff; border:1px solid #f1f5f9}
    .hist-row{font-size:13px;padding:6px;border-bottom:1px dashed #f1f5f9;display:flex;justify-content:space-between;gap:10px}
    .hint{font-size:13px;color:var(--muted)}
    .error{color:var(--danger);font-size:13px;margin-top:6px}
    @media (max-width:820px){.card{grid-template-columns:1fr;}.sidebar{border-right:0;padding-right:0;border-bottom:1px solid #f1f5f9;padding-bottom:12px}}
  </style>
</head>
<body>
  <div class="card" role="application" aria-label="Finance calculator">
    <aside class="sidebar" aria-hidden="false">
      <h1>Finance Calculator</h1>
      <p class="lead">Choose a calculator to compute ROI, Revenue, Commission or Savings quickly.</p>

      <div class="calc-list" role="list" id="calcList" aria-label="Calculators">
        <div class="calc-item active" data-calc="roi" role="listitem" tabindex="0">
          <div class="content">
            <div class="formula">ROI</div>
            <div class="muted">ROI (%) = ((Gain − Cost) ÷ Cost) × 100</div>
          </div>
        </div>

        <div class="calc-item" data-calc="revenue" role="listitem" tabindex="0">
          <div class="content">
            <div class="formula">Revenue</div>
            <div class="muted">Revenue = Price × Quantity Sold</div>
          </div>
        </div>

        <div class="calc-item" data-calc="commission" role="listitem" tabindex="0">
          <div class="content">
            <div class="formula">Commission</div>
            <div class="muted">Commission = Sales × Commission Rate</div>
          </div>
        </div>

        <div class="calc-item" data-calc="savings" role="listitem" tabindex="0">
          <div class="content">
            <div class="formula">Savings</div>
            <div class="muted">Savings = Original Cost − New Cost</div>
          </div>
        </div>
      </div>

      <div style="margin-top:12px;">
        <label for="precision">Display precision (decimal places)</label>
        <select id="precision" aria-label="Precision">
          <option value="0">0</option>
          <option value="1">1</option>
          <option value="2" selected>2</option>
          <option value="3">3</option>
        </select>
      </div>
    </aside>

    <main class="main" id="main">
      <div>
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <strong id="title">ROI</strong>
            <div class="hint" id="subtitle">ROI (%) = ((Gain − Cost) ÷ Cost) × 100</div>
          </div>
          <div style="display:flex;gap:8px;align-items:center">
            <button id="copyBtn" class="btn ghost" title="Copy result">Copy</button>
            <button id="resetBtn" class="btn">Reset</button>
          </div>
        </div>

        <div class="inputs-grid" id="inputs" style="margin-top:12px;">
          <!-- Dynamic inputs inserted here -->
        </div>

        <div class="actions">
          <button id="calcBtn" class="btn">Calculate</button>
          <button id="addHistoryBtn" class="btn ghost">Add to history</button>
        </div>

        <div id="error" class="error" role="alert" aria-live="polite" style="display:none"></div>

        <div class="result" id="result" aria-live="polite" style="display:none">
          <div>
            <div class="muted">Result</div>
            <div class="value" id="resultValue"></div>
            <div class="hint" id="resultSub"></div>
          </div>
          <div style="text-align:right">
            <div class="muted">Timestamp</div>
            <div id="resultTime" class="hint"></div>
          </div>
        </div>

        <div style="margin-top:12px">
          <div class="muted" style="margin-bottom:6px">History</div>
          <div class="history" id="history" aria-live="polite">
            <!-- history rows -->
            <div class="hint">No entries yet. Calculate something and add to history.</div>
          </div>
        </div>
      </div>

      <div style="font-size:13px;color:var(--muted);margin-top:6px">
        Tip: Use tab to switch between fields. Inputs accept decimal numbers. ROI handles Cost = 0 safely.
      </div>
    </main>
  </div>

  <script>
    // App state and helpers
    const calculators = {
      roi: {
        title: 'ROI',
        subtitle: 'ROI (%) = ((Gain − Cost) ÷ Cost) × 100',
        fields: [
          { id: 'gain', label: 'Gain (total return)', placeholder:'e.g. 1500' },
          { id: 'cost', label: 'Cost (initial investment)', placeholder:'e.g. 1000' }
        ],
        compute: ({gain, cost}) => {
          // handle division by zero
          if (cost === 0) {
            if (gain === 0) return { value: 0, unit: '%' };
            return { error: 'Cost is zero — ROI is infinite or undefined.' };
          }
          const roi = ((gain - cost) / cost) * 100;
          return { value: roi, unit: '%' };
        }
      },
      revenue: {
        title: 'Revenue',
        subtitle: 'Revenue = Price × Quantity Sold',
        fields: [
          { id: 'price', label: 'Price (per unit)', placeholder:'e.g. 25.50' },
          { id: 'quantity', label: 'Quantity sold', placeholder:'e.g. 120' }
        ],
        compute: ({price, quantity}) => {
          const revenue = price * quantity;
          return { value: revenue, unit: '' };
        }
      },
      commission: {
        title: 'Commission',
        subtitle: 'Commission = Sales × Commission Rate',
        fields: [
          { id: 'sales', label: 'Sales (total)', placeholder:'e.g. 2000' },
          { id: 'rate', label: 'Commission rate (as %)', placeholder:'e.g. 5' }
        ],
        compute: ({sales, rate}) => {
          // rate provided as percent (e.g., 5 => 0.05)
          const commission = sales * (rate / 100);
          return { value: commission, unit: '' };
        }
      },
      savings: {
        title: 'Savings',
        subtitle: 'Savings = Original Cost − New Cost',
        fields: [
          { id: 'original', label: 'Original cost', placeholder:'e.g. 1200' },
          { id: 'newcost', label: 'New cost', placeholder:'e.g. 900' }
        ],
        compute: ({original, newcost}) => {
          const savings = original - newcost;
          return { value: savings, unit: '' };
        }
      }
    };

    // DOM refs
    const calcList = document.getElementById('calcList');
    const inputsEl = document.getElementById('inputs');
    const titleEl = document.getElementById('title');
    const subtitleEl = document.getElementById('subtitle');
    const calcBtn = document.getElementById('calcBtn');
    const resultEl = document.getElementById('result');
    const resultValueEl = document.getElementById('resultValue');
    const resultSubEl = document.getElementById('resultSub');
    const resultTimeEl = document.getElementById('resultTime');
    const errorEl = document.getElementById('error');
    const historyEl = document.getElementById('history');
    const precisionEl = document.getElementById('precision');
    const copyBtn = document.getElementById('copyBtn');
    const resetBtn = document.getElementById('resetBtn');
    const addHistoryBtn = document.getElementById('addHistoryBtn');

    let activeCalc = 'roi';
    let lastResult = null;
    let history = [];

    // Utilities
    function fmt(v, precision){
      if (Number.isFinite(v)) {
        return Number(v).toLocaleString(undefined, {minimumFractionDigits:0, maximumFractionDigits:precision});
      }
      return String(v);
    }

    function clearChildren(el){ while(el.firstChild) el.removeChild(el.firstChild); }

    // Render inputs for selected calculator
    function renderInputs(calcKey){
      clearChildren(inputsEl);
      const calc = calculators[calcKey];
      calc.fields.forEach(f => {
        const wrapper = document.createElement('div');
        const label = document.createElement('label');
        label.htmlFor = f.id;
        label.textContent = f.label;
        const input = document.createElement('input');
        input.type = 'number';
        input.step = 'any';
        input.id = f.id;
        input.placeholder = f.placeholder || '';
        input.autocomplete = 'off';
        input.addEventListener('keydown', (e) => {
          if (e.key === 'Enter') calcBtn.click();
        });
        wrapper.appendChild(label);
        wrapper.appendChild(input);
        inputsEl.appendChild(wrapper);
      });
    }

    function setActiveCalc(key){
      activeCalc = key;
      // update sidebar active class
      [...calcList.querySelectorAll('.calc-item')].forEach(el => el.classList.toggle('active', el.dataset.calc === key));
      titleEl.textContent = calculators[key].title;
      subtitleEl.textContent = calculators[key].subtitle;
      errorEl.style.display = 'none';
      resultEl.style.display = 'none';
      renderInputs(key);
    }

    // Get numeric values from inputs, with validation
    function getInputValues(calcKey){
      const calc = calculators[calcKey];
      const values = {};
      for (const f of calc.fields){
        const el = document.getElementById(f.id);
        if (!el) return { error: 'Missing input element.' };
        const raw = el.value.trim();
        // treat empty as NaN -> error
        if (raw === '') return { error: `Please enter ${f.label}.` };
        const num = Number(raw);
        if (Number.isNaN(num)) return { error: `${f.label} must be a number.` };
        values[f.id] = num;
      }
      return { values };
    }

    // Calculate and display
    function calculate(){
      errorEl.style.display = 'none';
      resultEl.style.display = 'none';
      const pr = getInputValues(activeCalc);
      if (pr.error){ showError(pr.error); return; }
      const calc = calculators[activeCalc];
      const res = calc.compute(pr.values);
      if (res.error){ showError(res.error); return; }
      const precision = Number(precisionEl.value);
      const formatted = fmt(res.value, precision) + (res.unit ? ' ' + res.unit : '');
      resultValueEl.textContent = formatted;
      resultSubEl.textContent = (() => {
        // show a brief breakdown for clarity
        switch(activeCalc){
          case 'roi': return `Gain ${pr.values.gain} − Cost ${pr.values.cost} = ${(pr.values.gain - pr.values.cost).toLocaleString()}`;
          case 'revenue': return `Price × Quantity = ${pr.values.price} × ${pr.values.quantity}`;
          case 'commission': return `Rate ${pr.values.rate}% of ${pr.values.sales}`;
          case 'savings': return `Original − New = ${pr.values.original} − ${pr.values.newcost}`;
          default: return '';
        }
      })();
      const now = new Date();
      resultTimeEl.textContent = now.toLocaleString();
      lastResult = {
        calculator: activeCalc,
        inputs: pr.values,
        result: res.value,
        unit: res.unit,
        precision,
        time: now.toISOString()
      };
      resultEl.style.display = 'flex';
    }

    function showError(msg){
      errorEl.style.display = 'block';
      errorEl.textContent = msg;
    }

    function addToHistory(){
      if (!lastResult) { showError('No result to add. Calculate first.'); return; }
      const entry = Object.assign({}, lastResult, { id: Date.now() });
      history.unshift(entry);
      // keep history limited
      if (history.length > 50) history.pop();
      renderHistory();
    }

    function renderHistory(){
      clearChildren(historyEl);
      if (history.length === 0){
        const hint = document.createElement('div');
        hint.className = 'hint';
        hint.textContent = 'No entries yet. Calculate something and add to history.';
        historyEl.appendChild(hint);
        return;
      }
      history.forEach(item => {
        const row = document.createElement('div');
        row.className = 'hist-row';
        const left = document.createElement('div');
        left.style.maxWidth = '70%';
        left.innerHTML = `<strong style="font-size:13px">${calculators[item.calculator].title}</strong><div class="muted" style="font-size:12px">${Object.entries(item.inputs).map(([k,v])=>k+': '+v).join(' · ')}</div>`;
        const right = document.createElement('div');
        right.style.textAlign='right';
        const val = document.createElement('div');
        val.textContent = fmt(item.result, item.precision) + (item.unit ? ' '+item.unit : '');
        val.style.fontWeight='700';
        const t = document.createElement('div');
        t.className='muted';
        t.style.fontSize='12px';
        t.textContent = new Date(item.time).toLocaleString();
        right.appendChild(val);
        right.appendChild(t);
        row.appendChild(left);
        row.appendChild(right);

        // clicking a history row restores inputs and shows result
        row.style.cursor = 'pointer';
        row.title = 'Click to restore';
        row.addEventListener('click', () => {
          setActiveCalc(item.calculator);
          // fill inputs
          for (const [k,v] of Object.entries(item.inputs)){
            const el = document.getElementById(k);
            if (el) el.value = v;
          }
          // set precision
          precisionEl.value = item.precision;
          calculate();
        });

        historyEl.appendChild(row);
      });
    }

    // Copy result
    async function copyResult(){
      if (!lastResult) { showError('No result to copy.'); return; }
      const text = `${calculators[lastResult.calculator].title}: ${fmt(lastResult.result, lastResult.precision)}${lastResult.unit ? ' ' + lastResult.unit : ''}`;
      try {
        await navigator.clipboard.writeText(text);
        copyBtn.textContent = 'Copied!';
        setTimeout(()=>copyBtn.textContent = 'Copy',1200);
      } catch (e){
        showError('Unable to copy to clipboard.');
      }
    }

    // Reset form
    function resetAll(){
      // clear inputs
      const inputs = inputsEl.querySelectorAll('input');
      inputs.forEach(i=>i.value='');
      resultEl.style.display='none';
      errorEl.style.display='none';
      lastResult = null;
    }

    // Attach event listeners
    calcList.addEventListener('click', (e) => {
      const item = e.target.closest('.calc-item');
      if (!item) return;
      setActiveCalc(item.dataset.calc);
    });
    // keyboard support for list items
    calcList.addEventListener('keydown', (e) => {
      const item = e.target.closest('.calc-item');
      if (!item) return;
      if (e.key === 'Enter' || e.key === ' ') {
        setActiveCalc(item.dataset.calc);
        e.preventDefault();
      }
    });

    calcBtn.addEventListener('click', calculate);
    addHistoryBtn.addEventListener('click', addToHistory);
    copyBtn.addEventListener('click', copyResult);
    resetBtn.addEventListener('click', resetAll);

    // init
    setActiveCalc('roi');

    // Optional: remember history in localStorage
    try {
      const saved = localStorage.getItem('financeCalcHistory_v1');
      if (saved) {
        history = JSON.parse(saved);
        renderHistory();
      }
      // save when window unloads
      window.addEventListener('beforeunload', () => {
        try { localStorage.setItem('financeCalcHistory_v1', JSON.stringify(history)); } catch(e){}
      });
    } catch(e){ /* ignore storage errors */ }
  </script>
</body>
</html>
