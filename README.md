<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Khaders Al Badariyya — Pro Billing (Luxury)</title>

<!-- libs -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<!-- Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600&family=Playfair+Display:wght@600;700&display=swap" rel="stylesheet">

<!-- Tailwind (quick) -->
<link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">

<style>
:root{ --gold:#b88b1b; --dark:#0b1220; --muted:#8b8f98; }
body{ font-family:Inter,system-ui,Segoe UI,Roboto,Arial; background:#0f1724; color:#0b1220; padding:20px; }
.container{ max-width:1200px; margin:0 auto; }
.app{ display:grid; grid-template-columns: 280px 1fr; gap:20px; align-items:start; }

/* Sidebar */
.sidebar{ background:#071028; color:#d1d5db; border-radius:12px; padding:18px; height:calc(100vh - 40px); box-shadow:0 8px 30px rgba(0,0,0,0.6); }
.brand{ font-family:'Playfair Display', serif; color:var(--gold); font-size:20px; font-weight:700; }
.menu button{ width:100%; text-align:left; color:#cbd5e1; padding:10px; border-radius:8px; background:transparent; border:none; cursor:pointer; margin-top:6px; }
.menu button.active, .menu button:hover{ background:#0b1630; color:white; }

/* Main panels */
.panel{ background:linear-gradient(180deg,#fff 0%, #f8fafc 100%); border-radius:12px; padding:18px; box-shadow:0 10px 30px rgba(2,6,23,0.4); color:#0b1220; }
.header-title{ font-family:'Playfair Display', serif; color:var(--dark); font-size:22px; font-weight:700; }

/* Invoice luxury look */
.invoice-card{ position:relative; padding:28px; border-radius:10px; background:linear-gradient(180deg,#ffffff,#fafafa); box-shadow:0 8px 40px rgba(2,6,23,0.12); }
.logo-top-right{ position:absolute; right:28px; top:28px; width:120px; height:auto; object-fit:contain; }
.wm{ position:absolute; inset:0; display:flex; align-items:center; justify-content:center; pointer-events:none; z-index:0; }
.wm img{ width:60%; opacity:0.04; filter:grayscale(100%); }

.h1{ font-family:'Playfair Display', serif; font-size:28px; color:var(--dark); letter-spacing:1px; }

/* invoice table */
.table th, .table td{ border-bottom:1px solid #e6e9ef; padding:10px; }
.table thead th{ color:#6b7280; font-weight:600; }

/* footer totals */
.totals{ background:linear-gradient(90deg,#111827,#111827); color:white; padding:12px; border-radius:8px; text-align:right; }

/* small */
.small{ color:var(--muted); font-size:13px; }

/* responsive */
@media (max-width:980px){ .app{ grid-template-columns:1fr; } .sidebar{ height:auto; } .logo-top-right{ display:none; } }

</style>
</head>
<body>
<div class="container">
  <div class="app">
    <!-- Sidebar -->
    <aside class="sidebar">
      <div class="brand">Khaders Al Badariyya</div>
      <div class="small mt-1 mb-4">Luxury Catering — Billing</div>

      <div class="menu">
        <button class="active" data-view="dashboard">🏠 Dashboard</button>
        <button data-view="billing">🧾 Billing / New Invoice</button>
        <button data-view="products">📦 Products / Categories</button>
        <button data-view="customers">👥 Customers</button>
        <button data-view="bills">📜 Bills / Records</button>
        <button data-view="reports">📊 Reports</button>
        <button data-view="settings">⚙️ Settings</button>
        <button data-view="help">❓ Help</button>
      </div>

      <div class="mt-6 small">
        <div>Local storage mode</div>
        <div class="mt-2">Export DB to move to server later</div>
      </div>
    </aside>

    <!-- Main -->
    <main>
      <!-- Dashboard -->
      <section id="dashboard" class="panel view">
        <div class="flex justify-between items-start">
          <div>
            <div class="header-title">Dashboard</div>
            <div class="small">Overview & quick actions</div>
          </div>
          <div class="text-right">
            <div class="small">Local</div>
            <div id="statCount" class="font-bold text-lg">0 bills</div>
          </div>
        </div>

        <div class="mt-4 grid grid-cols-1 md:grid-cols-3 gap-4">
          <div class="panel">
            <div class="small">Total Sales</div>
            <div id="statSales" class="text-2xl font-bold">₹0.00</div>
          </div>
          <div class="panel">
            <div class="small">Products</div>
            <div id="statProducts" class="text-2xl font-bold">0</div>
          </div>
          <div class="panel">
            <div class="small">Customers</div>
            <div id="statCustomers" class="text-2xl font-bold">0</div>
          </div>
        </div>

        <div class="mt-4 panel">
          <div class="small mb-2">Recent invoices</div>
          <div id="recentList" class="small"></div>
        </div>
      </section>

      <!-- Billing -->
      <section id="billing" class="panel view" style="display:none;">
        <div class="invoice-card" id="invoiceCard">
          <!-- precise logo on top-right -->
          <img id="logoImg" class="logo-top-right" src="" alt="logo" style="display:none"/>

          <div class="wm" id="wm"></div>

          <div class="flex justify-between items-start mb-4">
            <div>
              <div class="h1" id="brandTitle">Khaders Al Badariyya Catering Service</div>
              <div class="small" id="brandTag">Luxury Catering · Malappuram</div>
              <div class="small" id="brandPhone">Ph: +91 8606221010</div>
            </div>

            <div class="text-right">
              <div class="small">Invoice #</div>
              <input id="inv_no" class="border p-2 rounded w-48 mb-2" placeholder="auto">
              <div class="small">Date</div>
              <input type="datetime-local" id="inv_date" class="border p-2 rounded w-48">
              <div class="mt-2">
                <label class="small">Upload Logo</label>
                <input type="file" id="logoInput" accept="image/*" class="block mt-1">
              </div>
            </div>
          </div>

          <div class="grid md:grid-cols-3 gap-3 mb-3">
            <div>
              <div class="small">Customer</div>
              <select id="selCustomer" class="border p-2 rounded w-full"></select>
              <div class="mt-2 small">Phone</div>
              <input id="custPhone" class="border p-2 rounded w-full" placeholder="Customer phone">
              <div class="mt-2 small">Address</div>
              <input id="custAddr" class="border p-2 rounded w-full" placeholder="Customer address">
            </div>

            <div>
              <div class="small">Category Filter</div>
              <select id="categoryFilter" class="border p-2 rounded w-full">
                <option value="">-- All Categories --</option>
              </select>

              <div class="small mt-2">Add product</div>
              <div class="flex gap-2 mt-1">
                <select id="selProduct" class="flex-1 border p-2 rounded"></select>
                <input id="add_qty" type="number" step="0.01" value="1" class="w-24 border p-2 rounded">
                <button id="btnAdd" class="btn">Add</button>
              </div>
              <div id="stockHint" class="small mt-2 text-red-600"></div>
            </div>

            <div>
              <div class="small">Actions</div>
              <div class="flex gap-2 mt-2">
                <button id="saveBill" class="btn">Save Invoice</button>
                <button id="downloadA3" class="px-3 py-2 rounded border">Download A3 PDF</button>
                <button id="printBill" class="px-3 py-2 rounded border">Print</button>
                <button id="clearBill" class="px-3 py-2 rounded border">Clear</button>
              </div>

              <div class="small mt-3">Free delivery up to <input id="freeKg" type="number" class="w-18 border p-1 rounded inline" value="5"> kg</div>
            </div>
          </div>

          <div class="overflow-x-auto mb-4">
            <table class="w-full table-auto">
              <thead class="bg-gray-50">
                <tr>
                  <th class="p-2 small">#</th><th class="p-2 small">Description</th><th class="p-2 small">Qty</th><th class="p-2 small">Rate</th><th class="p-2 small text-right">Amount</th><th class="p-2 small">Action</th>
                </tr>
              </thead>
              <tbody id="invItems"></tbody>
              <tfoot>
                <tr><td colspan="4" class="text-right p-2 small">Subtotal</td><td id="subtotal" class="text-right p-2 font-bold">₹0.00</td><td></td></tr>
                <tr><td colspan="4" class="text-right p-2 small">Tax</td><td class="text-right p-2"><select id="selTax" class="border p-1 rounded"></select></td><td></td></tr>
                <tr><td colspan="4" class="text-right p-2 small">Discount (₹)</td><td class="text-right p-2"><input id="billDiscount" type="number" step="0.01" value="0" class="w-32 border p-1 rounded"></td><td></td></tr>
                <tr><td colspan="4" class="text-right p-2 small">Delivery Fee</td><td id="deliveryFee" class="text-right p-2">₹0.00</td><td></td></tr>
                <tr><td colspan="4" class="text-right p-2 font-bold">Grand Total</td><td id="grandtotal" class="text-right p-2 font-bold text-xl">₹0.00</td><td></td></tr>
              </tfoot>
            </table>
          </div>

        </div>
      </section>

      <!-- Products -->
      <section id="products" class="panel view" style="display:none;">
        <div class="flex justify-between items-center mb-3">
          <div><div class="header-title">Products & Categories</div><div class="small">Add product with stock level & category</div></div>
          <div class="small">Stock updates when invoice saved</div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-4 gap-3 mb-3">
          <input id="p_name" placeholder="Product name" class="border p-2 rounded col-span-2" />
          <input id="p_cat" placeholder="Category" class="border p-2 rounded" />
          <input id="p_stock" placeholder="Stock" type="number" class="border p-2 rounded" />
          <input id="p_rate" placeholder="Rate ₹" type="number" class="border p-2 rounded" />
        </div>

        <div class="flex gap-2 mb-3">
          <button id="p_add" class="btn">Add / Update</button>
          <button id="p_clear" class="px-3 py-2 rounded border">Clear All</button>
          <input id="p_search" placeholder="Search products" class="border p-2 rounded flex-1" />
        </div>

        <div id="p_list" class="space-y-1 max-h-60 overflow-auto"></div>
      </section>

      <!-- Customers -->
      <section id="customers" class="panel view" style="display:none;">
        <div class="flex justify-between items-center mb-3">
          <div><div class="header-title">Customers</div><div class="small">Add & autofill while billing</div></div>
          <div class="small">Use dropdown in Billing to autofill</div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-3 mb-3">
          <input id="c_name" placeholder="Name" class="border p-2 rounded" />
          <input id="c_phone" placeholder="Phone" class="border p-2 rounded" />
          <input id="c_addr" placeholder="Address" class="border p-2 rounded" />
        </div>

        <div class="flex gap-2 mb-3">
          <button id="c_add" class="btn">Add / Update</button>
          <button id="c_clear" class="px-3 py-2 rounded border">Clear All</button>
          <input id="c_search" placeholder="Search customers" class="border p-2 rounded flex-1" />
        </div>

        <div id="c_list" class="space-y-1 max-h-60 overflow-auto"></div>
      </section>

      <!-- Bills -->
      <section id="bills" class="panel view" style="display:none;">
        <div class="flex justify-between items-center mb-3">
          <div><div class="header-title">Bills</div><div class="small">View / Print / Export</div></div>
          <div class="small">Search or export DB</div>
        </div>

        <div class="mb-3">
          <input id="b_search" placeholder="Search invoice or item" class="border p-2 rounded w-64" />
          <button id="exportAll" class="px-3 py-2 rounded border ml-3">Export DB</button>
        </div>

        <div id="b_history" class="space-y-2 max-h-64 overflow-auto"></div>
      </section>

      <!-- Reports -->
      <section id="reports" class="panel view" style="display:none;">
        <div class="header-title mb-2">Reports</div>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
          <div class="p-4 rounded panel"><div class="small">Daily Sales</div><div id="repDaily" class="text-xl font-bold mt-2">₹0.00</div></div>
          <div class="p-4 rounded panel"><div class="small">Monthly Sales</div><div id="repMonthly" class="text-xl font-bold mt-2">₹0.00</div></div>
          <div class="p-4 rounded panel"><div class="small">Invoice Count</div><div id="repCount" class="text-xl font-bold mt-2">0</div></div>
        </div>
      </section>

      <!-- Settings -->
      <section id="settings" class="panel view" style="display:none;">
        <div class="header-title">Settings</div>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-3 mt-3">
          <div>
            <label class="small">Business name</label>
            <input id="set_bname" class="border p-2 rounded w-full" value="Khaders Al Badariyya Catering Service">
          </div>
          <div>
            <label class="small">Phone</label>
            <input id="set_phone" class="border p-2 rounded w-full" value="+91 8606221010">
          </div>
        </div>
        <div class="mt-3">
          <button id="saveSettings" class="btn">Save Settings</button>
        </div>
      </section>

      <!-- Help -->
      <section id="help" class="panel view" style="display:none;">
        <div class="header-title">Help & Quick Guide</div>
        <ul class="mt-2 small list-disc ml-5">
          <li>Add categories & products under Products</li>
          <li>Add customers — then in Billing choose a customer to autofill phone/address</li>
          <li>When saving a bill, product stock will reduce automatically</li>
          <li>Use Download A3 to get a luxury-styled A3 invoice</li>
        </ul>
      </section>

    </main>
  </div>
</div>

<script>
/* ----------------------- Storage keys & helpers ----------------------- */
const PRODUCTS_KEY = 'kab_products_v4';
const CUSTOMERS_KEY = 'kab_customers_v4';
const BILLS_KEY = 'kab_bills_v6';
const TAX_KEY = 'kab_tax_v4';
const SETTINGS_KEY = 'kab_settings_v4';
const LOGO_KEY = 'kab_logo_v4';

const $ = s => document.querySelector(s);
const $$ = s => Array.from(document.querySelectorAll(s));
const fmt = n => (Number(n)||0).toFixed(2);
const uid = ()=> (crypto && crypto.randomUUID)? crypto.randomUUID() : 'id'+Math.random().toString(36).slice(2,9);

function load(key, def=[]){ try{ return JSON.parse(localStorage.getItem(key) || JSON.stringify(def)); }catch(e){ return def; } }
function save(key, val){ localStorage.setItem(key, JSON.stringify(val)); }
function esc(s){ return String(s||'').replace(/[&<>"']/g, c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c])); }

/* -------------------- App state -------------------- */
let invoiceItems = [];
let currentLogo = localStorage.getItem(LOGO_KEY) || '';
let settings = load(SETTINGS_KEY, { bname:'Khaders Al Badariyya Catering Service', phone:'+91 8606221010', freeKg:5, deliveryFee:50 });

/* -------------------- Menu views -------------------- */
$$('.menu button').forEach(btn=>{
  btn.addEventListener('click', ()=>{
    $$('.menu button').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
    const view = btn.dataset.view;
    $$('.view').forEach(v=> v.style.display = 'none');
    document.getElementById(view).style.display = 'block';
    if(view==='dashboard') renderDashboard();
    if(view==='products') renderProducts();
    if(view==='customers') renderCustomers();
    if(view==='bills') renderBills();
    if(view==='reports') renderReports();
  });
});

/* -------------------- Initial UI load -------------------- */
function init(){
  loadSettingsToUI();
  renderProducts();
  renderCustomers();
  renderTaxes();
  renderInvItems();
  renderBills();
  renderDashboard();
  applyLogoToWM(currentLogo);
}
init();

/* -------------------- Settings -------------------- */
function loadSettingsToUI(){
  $('#set_bname').value = settings.bname;
  $('#set_phone').value = settings.phone;
  $('#brandTitle').textContent = settings.bname;
  $('#brandPhone').textContent = 'Ph: ' + (settings.phone||'');
  $('#freeKg').value = settings.freeKg ?? 5;
}
$('#saveSettings').addEventListener('click', ()=>{
  settings.bname = $('#set_bname').value.trim();
  settings.phone = $('#set_phone').value.trim();
  settings.freeKg = Number($('#freeKg').value) || 5;
  save(SETTINGS_KEY, settings);
  loadSettingsToUI();
  alert('Settings saved');
});

/* -------------------- Logo upload / watermark -------------------- */
$('#logoInput').addEventListener('change', e=>{
  const f = e.target.files && e.target.files[0];
  if(!f) return;
  const r = new FileReader();
  r.onload = ev => {
    currentLogo = ev.target.result;
    localStorage.setItem(LOGO_KEY, currentLogo);
    applyLogoToWM(currentLogo);
  };
  r.readAsDataURL(f);
});
function applyLogoToWM(src){
  $('#wm').innerHTML = src ? `<img src="${src}" alt="wm">` : '';
  if(src){ $('#logoImg').src = src; $('#logoImg').style.display = ''; } else { $('#logoImg').style.display = 'none'; }
}

/* -------------------- Products (with category+stock) -------------------- */
function renderProducts(filter=''){
  const arr = load(PRODUCTS_KEY,[]);
  $('#p_list').innerHTML = '';
  // fill category filter
  const cats = [...new Set(arr.map(p => p.category || '').filter(Boolean))];
  $('#categoryFilter').innerHTML = '<option value="">-- All Categories --</option>';
  cats.forEach(c=> $('#categoryFilter').appendChild(new Option(c,c)));
  // fill product select
  $('#selProduct').innerHTML = '<option value="">-- select product --</option>';
  arr.filter(p => !filter || p.name.toLowerCase().includes(filter.toLowerCase())).forEach(p=>{
    const div = document.createElement('div'); div.className = 'flex items-center justify-between py-1 border-b';
    div.innerHTML = `<div><strong>${esc(p.name)}</strong><div class="small">${esc(p.category||'')} · Stock: ${Number(p.stock||0)} · ₹${fmt(p.rate)}</div></div>
      <div class="flex gap-1">
        <button data-id="${p.id}" class="p_edit px-2 py-1 bg-indigo-600 text-white rounded text-xs">Edit</button>
        <button data-id="${p.id}" class="p_del px-2 py-1 bg-red-600 text-white rounded text-xs">Del</button>
      </div>`;
    $('#p_list').appendChild(div);
  });
  // fill select options (all)
  load(PRODUCTS_KEY,[]).forEach(p => {
    const opt = document.createElement('option'); opt.value = p.id; opt.textContent = `${p.name} — ₹${fmt(p.rate)} (Stock ${p.stock||0})`;
    $('#selProduct').appendChild(opt);
  });
  $$('.p_edit').forEach(b=> b.onclick = e => editProduct(e.target.dataset.id));
  $$('.p_del').forEach(b=> b.onclick = e => { if(!confirm('Delete product?')) return; deleteProduct(e.target.dataset.id); });
  $('#statProducts').textContent = load(PRODUCTS_KEY,[]).length;
}
$('#p_add').addEventListener('click', ()=>{
  const name = $('#p_name').value.trim(); if(!name) return alert('Name required');
  const category = $('#p_cat').value.trim();
  const stock = Number($('#p_stock').value) || 0;
  const rate = Number($('#p_rate').value) || 0;
  const arr = load(PRODUCTS_KEY,[]);
  const existing = arr.find(x => x.name.toLowerCase() === name.toLowerCase());
  if(existing){ existing.category = category; existing.stock = stock; existing.rate = rate; } else { arr.push({ id: uid(), name, category, stock, rate }); }
  save(PRODUCTS_KEY, arr);
  $('#p_name').value=''; $('#p_cat').value=''; $('#p_stock').value=''; $('#p_rate').value='';
  renderProducts($('#p_search').value);
});
$('#p_clear').addEventListener('click', ()=> { if(!confirm('Clear all products?')) return save(PRODUCTS_KEY,[]), renderProducts(); });
$('#p_search').addEventListener('input', e=> renderProducts(e.target.value));
$('#categoryFilter').addEventListener('change', ()=> renderProducts($('#p_search').value));

function editProduct(id){ const arr = load(PRODUCTS_KEY,[]); const p = arr.find(x=> x.id===id); if(!p) return; $('#p_name').value=p.name; $('#p_cat').value=p.category; $('#p_stock').value=p.stock; $('#p_rate').value=p.rate; }
function deleteProduct(id){ let arr = load(PRODUCTS_KEY,[]); arr = arr.filter(x=> x.id!==id); save(PRODUCTS_KEY, arr); renderProducts(); }

/* -------------------- Customers (autofill) -------------------- */
function renderCustomers(filter=''){
  const list = load(CUSTOMERS_KEY,[]);
  $('#c_list').innerHTML = '';
  $('#selCustomer').innerHTML = '<option value="">Walk-in</option>';
  list.filter(c => !filter || c.name.toLowerCase().includes(filter.toLowerCase())).forEach(c=>{
    const div = document.createElement('div'); div.className='flex items-center justify-between py-1 border-b';
    div.innerHTML = `<div><strong>${esc(c.name)}</strong><div class="small">${esc(c.phone)} · ${esc(c.address||'')}</div></div>
      <div class="flex gap-1"><button data-id="${c.id}" class="c_edit px-2 py-1 bg-indigo-600 text-white rounded text-xs">Edit</button><button data-id="${c.id}" class="c_del px-2 py-1 bg-red-600 text-white rounded text-xs">Del</button></div>`;
    $('#c_list').appendChild(div);
  });
  list.forEach(c => { const opt = document.createElement('option'); opt.value = c.id; opt.textContent = `${c.name}${c.phone?(' • '+c.phone):''}`; $('#selCustomer').appendChild(opt); });
  $$('.c_edit').forEach(b=> b.onclick = e => editCustomer(e.target.dataset.id));
  $$('.c_del').forEach(b=> b.onclick = e => { if(!confirm('Delete customer?')) return; deleteCustomer(e.target.dataset.id); });
  $('#statCustomers').textContent = list.length;
}
$('#c_add').addEventListener('click', ()=>{
  const name = $('#c_name').value.trim(); if(!name) return alert('Name required');
  const phone = $('#c_phone').value.trim(); const addr = $('#c_addr').value.trim();
  const arr = load(CUSTOMERS_KEY,[]); const existing = arr.find(c => c.name.toLowerCase()===name.toLowerCase() && c.phone===phone);
  if(existing){ existing.address = addr; } else { arr.push({ id: uid(), name, phone, address: addr }); }
  save(CUSTOMERS_KEY, arr); $('#c_name').value=''; $('#c_phone').value=''; $('#c_addr').value=''; renderCustomers($('#c_search').value);
});
$('#c_clear').addEventListener('click', ()=> { if(!confirm('Clear all customers?')) return save(CUSTOMERS_KEY,[]), renderCustomers(); });
$('#c_search').addEventListener('input', e=> renderCustomers(e.target.value));
function editCustomer(id){ const arr = load(CUSTOMERS_KEY,[]); const c = arr.find(x=> x.id===id); if(!c) return; $('#c_name').value=c.name; $('#c_phone').value=c.phone; $('#c_addr').value=c.address; }
function deleteCustomer(id){ let arr = load(CUSTOMERS_KEY,[]); arr = arr.filter(x=> x.id!==id); save(CUSTOMERS_KEY,arr); renderCustomers(); }

/* Autofill phone/address when customer selected */
$('#selCustomer').addEventListener('change', ()=>{
  const id = $('#selCustomer').value;
  if(!id){ $('#custPhone').value=''; $('#custAddr').value=''; return; }
  const c = load(CUSTOMERS_KEY,[]).find(x=> x.id===id);
  if(c){ $('#custPhone').value = c.phone || ''; $('#custAddr').value = c.address || ''; }
});

/* -------------------- Tax presets -------------------- */
function renderTaxes(){ const arr = load(TAX_KEY,[]); $('#selTax').innerHTML = '<option value="none">-- None --</option>'; arr.forEach(t=> { const opt=document.createElement('option'); opt.value=t.id; opt.textContent=`${t.name} (${t.pct}%)`; $('#selTax').appendChild(opt); }); }
$('#addTax')?.addEventListener('click', ()=>{ const name=$('#taxLabel').value.trim(); const pct=Number($('#taxPct').value)||0; if(!name) return alert('Tax label required'); const arr=load(TAX_KEY,[]); arr.push({id:uid(), name, pct}); save(TAX_KEY,arr); renderTaxes(); });

/* -------------------- Invoice composition, stock checks -------------------- */
function addItemToInvoice(prodId, qty){
  const prod = load(PRODUCTS_KEY,[]).find(p=> p.id===prodId); if(!prod) return alert('Select product');
  qty = Number(qty)||0; if(qty<=0) return alert('Quantity must be > 0');
  // stock check
  if(prod.stock !== undefined && prod.stock !== null){
    if(qty > prod.stock) return $('#stockHint').textContent = `Not enough stock (available ${prod.stock})`;
    else $('#stockHint').textContent = '';
  }
  const existing = invoiceItems.find(i=> i.id===prodId);
  if(existing){ existing.qty += qty; existing.amount = existing.qty * existing.rate; } else { invoiceItems.push({ id:prodId, name:prod.name, qty, rate:prod.rate, amount: qty*prod.rate }); }
  renderInvItems();
}
$('#btnAdd').addEventListener('click', ()=> addItemToInvoice($('#selProduct').value, $('#add_qty').value));

function renderInvItems(){
  const tb = $('#invItems'); tb.innerHTML='';
  invoiceItems.forEach((it, idx)=>{
    const tr = document.createElement('tr');
    tr.innerHTML = `<td class="p-2">${idx+1}</td><td class="p-2">${esc(it.name)}</td><td class="p-2"><input data-idx="${idx}" class="qty border p-1 w-20" value="${it.qty}"></td><td class="p-2">₹${fmt(it.rate)}</td><td class="p-2 text-right">₹${fmt(it.amount)}</td><td class="p-2"><button data-idx="${idx}" class="rm p-1 bg-red-600 text-white rounded text-xs">Del</button></td>`;
    tb.appendChild(tr);
  });
  $$('.qty').forEach(inp => inp.oninput = e => { const i=Number(e.target.dataset.idx); const v=Number(e.target.value)||0; invoiceItems[i].qty=v; invoiceItems[i].amount = v * invoiceItems[i].rate; renderInvItems(); });
  $$('.rm').forEach(b => b.onclick = e => { const i = Number(e.target.dataset.idx); invoiceItems.splice(i,1); renderInvItems(); });
  recalcInvoice();
}

function recalcInvoice(){
  const subtotal = invoiceItems.reduce((s,i)=> s + (i.amount||0), 0);
  $('#subtotal').textContent = '₹' + fmt(subtotal);
  const taxId = $('#selTax').value;
  let taxAmt = 0;
  if(taxId !== 'none'){ const t = load(TAX_KEY,[]).find(x=> x.id===taxId); if(t) taxAmt = subtotal * (Number(t.pct)||0)/100; }
  const discount = Number($('#billDiscount').value)||0;
  const freeKg = Number($('#freeKg').value)||settings.freeKg;
  const totalKg = invoiceItems.reduce((s,i)=> s + (i.qty||0), 0);
  let deliveryFee = 0; if(totalKg > freeKg) deliveryFee = settings.deliveryFee || 0;
  $('#deliveryFee').textContent = '₹' + fmt(deliveryFee);
  const grand = subtotal + taxAmt - discount + deliveryFee;
  $('#grandtotal').textContent = '₹' + fmt(grand);
}
$('#selTax').addEventListener('change', recalcInvoice);
$('#billDiscount').addEventListener('input', recalcInvoice);
$('#freeKg').addEventListener('input', recalcInvoice);

/* -------------------- Save invoice (decrement stock) -------------------- */
function genInvNo(){ return 'KAB-' + new Date().toISOString().replace(/[-:T.Z]/g,'').slice(2,12); }

$('#saveBill').addEventListener('click', ()=>{
  if(invoiceItems.length === 0) return alert('Add items first');
  // stock verify again before save
  const products = load(PRODUCTS_KEY,[]);
  for(const it of invoiceItems){
    const p = products.find(x=> x.id===it.id);
    if(p && p.stock !== undefined && it.qty > p.stock) return alert(`Insufficient stock for ${p.name} (available ${p.stock})`);
  }
  // build bill
  const bills = load(BILLS_KEY,[]);
  const billNo = $('#inv_no').value.trim() || genInvNo();
  if(bills.some(b => b.billNo === billNo) && !confirm('Invoice exists. Overwrite?')) return;
  const custId = $('#selCustomer').value; const cust = load(CUSTOMERS_KEY,[]).find(c=> c.id===custId) || { name: $('#custPhone').value? 'Walk-in':'' };
  const subtotal = invoiceItems.reduce((s,i)=> s + (i.amount||0), 0);
  const taxId = $('#selTax').value; const taxPct = taxId==='none'?0: (load(TAX_KEY,[]).find(t=> t.id===taxId)?.pct || 0);
  const taxAmt = subtotal * taxPct / 100;
  const discount = Number($('#billDiscount').value)||0;
  const delivery = Number($('#deliveryFee').textContent.replace(/[^\d.]/g,''))||0;
  const total = subtotal + taxAmt - discount + delivery;
  const bill = { id:uid(), billNo, createdAt: new Date().toISOString(), customer: cust, items: invoiceItems, subtotal, taxPct, taxAmt, discount, delivery, total, logo: currentLogo };
  // save
  const newBills = bills.filter(b=> b.billNo !== billNo); newBills.push(bill); save(BILLS_KEY, newBills);
  // decrement stock
  const prods = load(PRODUCTS_KEY,[]);
  invoiceItems.forEach(it => {
    const p = prods.find(x=> x.id === it.id);
    if(p && p.stock !== undefined){ p.stock = Math.max(0, (Number(p.stock)||0) - Number(it.qty)||0); }
  });
  save(PRODUCTS_KEY, prods);
  alert('Saved invoice');
  invoiceItems = []; renderInvItems(); renderProducts(); renderBills(); renderDashboard();
});

/* -------------------- Bills / History -------------------- */
function renderBills(filter=''){
  const arr = load(BILLS_KEY,[]).sort((a,b)=> new Date(b.createdAt) - new Date(a.createdAt));
  $('#b_history').innerHTML = '';
  arr.filter(b => {
    if(!filter) return true;
    const f = filter.toLowerCase();
    return b.billNo.toLowerCase().includes(f) || (b.customer && b.customer.name.toLowerCase().includes(f)) || b.items.some(it => it.name.toLowerCase().includes(f));
  }).forEach(b=>{
    const el = document.createElement('div'); el.className='p-3 border rounded mb-1 bg-white';
    el.innerHTML = `<div class="flex justify-between"><div><strong>${esc(b.billNo)}</strong> <div class="small">${new Date(b.createdAt).toLocaleString()}</div></div><div>₹${fmt(b.total)} <button data-id="${b.id}" class="viewb ml-2 px-2 py-1 rounded border">View</button> <button data-id="${b.id}" class="delb ml-2 px-2 py-1 rounded border">Del</button></div></div>`;
    $('#b_history').appendChild(el);
  });
  $$('.viewb').forEach(b=> b.onclick = e => viewBill(e.target.dataset.id));
  $$('.delb').forEach(b=> b.onclick = e => { if(!confirm('Delete invoice?')) return; const id=e.target.dataset.id; const rem=load(BILLS_KEY,[]).filter(x=> x.id!==id); save(BILLS_KEY, rem); renderBills(); renderDashboard(); });
}
$('#b_search').addEventListener('input', e=> renderBills(e.target.value));

function viewBill(id){
  const b = load(BILLS_KEY,[]).find(x=> x.id===id);
  if(!b) return alert('Not found');
  $('#inv_no').value = b.billNo; $('#inv_date').value = new Date(b.createdAt).toISOString().slice(0,16);
  invoiceItems = JSON.parse(JSON.stringify(b.items)); renderInvItems();
  if(b.logo){ currentLogo = b.logo; localStorage.setItem(LOGO_KEY, currentLogo); applyLogoToWM(currentLogo); }
  showView('billing');
}

/* -------------------- Dashboard & Reports -------------------- */
function renderDashboard(){
  const bills = load(BILLS_KEY,[]);
  $('#statCount').textContent = bills.length + ' bills';
  const total = bills.reduce((s,b)=> s + (b.total||0), 0);
  $('#statSales').textContent = '₹' + fmt(total);
  $('#recentList').innerHTML = bills.slice(0,6).map(b => `<div>${esc(b.billNo)} — ₹${fmt(b.total)} <span class="small">(${new Date(b.createdAt).toLocaleDateString()})</span></div>`).join('');
  renderProducts(); renderCustomers();
}
function renderReports(){
  const bills = load(BILLS_KEY,[]);
  const today = new Date().toISOString().slice(0,10);
  const daily = bills.filter(b => b.createdAt.slice(0,10) === today).reduce((s,b)=> s + (b.total||0), 0);
  const monthly = bills.filter(b=> b.createdAt.slice(0,7) === new Date().toISOString().slice(0,7)).reduce((s,b)=> s + (b.total||0), 0);
  $('#repDaily').textContent = '₹' + fmt(daily);
  $('#repMonthly').textContent = '₹' + fmt(monthly);
  $('#repCount').textContent = bills.length;
}

/* -------------------- PDF A3 generation (luxury layout) -------------------- */
async function downloadA3(){
  applyLogoToWM(currentLogo);
  // ensure nice spacing before capture
  await new Promise(r=> setTimeout(r,200));
  const el = document.getElementById('invoiceCard');
  const canvas = await html2canvas(el, {scale:2, background:'#ffffff'});
  const imgData = canvas.toDataURL('image/png');
  const { jsPDF } = window.jspdf;
  const pdf = new jsPDF({orientation:'portrait', unit:'mm', format:'a3'});
  const pageW = pdf.internal.pageSize.getWidth();
  const margin = 10; const imgW = pageW - margin*2;
  const imgH = (canvas.height * imgW) / canvas.width;
  pdf.addImage(imgData, 'PNG', margin, margin, imgW, imgH);
  const filename = `Invoice-${($('#inv_no').value||'invoice')}.pdf`.replace(/\s+/g,'_');
  pdf.save(filename);
}
$('#downloadA3').addEventListener('click', downloadA3);
$('#printBill').addEventListener('click', ()=> window.print());

/* -------------------- Export DB -------------------- */
$('#exportAll').addEventListener('click', ()=>{
  const payload = { products: load(PRODUCTS_KEY,[]), customers: load(CUSTOMERS_KEY,[]), bills: load(BILLS_KEY,[]), taxes: load(TAX_KEY,[]), settings };
  const blob = new Blob([JSON.stringify(payload,null,2)], {type:'application/json'});
  const url = URL.createObjectURL(blob); const a = document.createElement('a'); a.href=url; a.download='billing_export.json'; a.click(); URL.revokeObjectURL(url);
});

/* -------------------- Helpers -------------------- */
function showView(id){ $$('.view').forEach(v=> v.style.display='none'); document.getElementById(id).style.display='block'; }
function refreshAll(){ renderProducts(); renderCustomers(); renderTaxes(); renderInvItems(); renderBills(); renderDashboard(); renderReports(); loadSettingsToUI(); applyLogoToWM(localStorage.getItem(LOGO_KEY)||''); }
function renderInvItems(){} // placeholder overwritten earlier
// ensure renderInvItems callable
renderInvItems = function(){ const tb = $('#invItems'); tb.innerHTML=''; invoiceItems.forEach((it, idx)=> { const tr = document.createElement('tr'); tr.innerHTML = `<td class="p-2">${idx+1}</td><td class="p-2">${esc(it.name)}</td><td class="p-2"><input data-idx="${idx}" class="qty border p-1 w-20" value="${it.qty}"></td><td class="p-2">₹${fmt(it.rate)}</td><td class="p-2 text-right">₹${fmt(it.amount)}</td><td class="p-2"><button data-idx="${idx}" class="rm p-1 bg-red-600 text-white rounded text-xs">Del</button></td>`; tb.appendChild(tr); }); $$('.qty').forEach(inp=>inp.oninput = e=>{ const i=Number(e.target.dataset.idx); const v=Number(e.target.value)||0; invoiceItems[i].qty=v; invoiceItems[i].amount = v * invoiceItems[i].rate; renderInvItems(); }); $$('.rm').forEach(b=> b.onclick = e=> { const i=Number(e.target.dataset.idx); invoiceItems.splice(i,1); renderInvItems(); }); recalcInvoice(); };

refreshAll();

/* keyboard shortcut */
document.addEventListener('keydown', e=> { if((e.ctrlKey||e.metaKey) && e.key.toLowerCase()==='n'){ invoiceItems=[]; renderInvItems(); $('#inv_no').value = genInvNo(); showView('billing'); } });

</script>
</body>
</html>
