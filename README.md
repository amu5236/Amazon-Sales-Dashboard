<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Sales &amp; Profit Analytics Dashboard</title>
<meta name="description" content="Looker Studio dashboard connected to a Google Sheet that tracks revenue, profit, cost and expenditure by product and country.">
<style>
:root{
  --bg:#f6f7fb;--card:#ffffff;--text:#1b2030;--muted:#5b6478;--line:#e3e6ef;
  --accent:#4f46e5;--accent2:#06b6d4;--good:#16a34a;--bad:#dc2626;--shadow:0 6px 24px rgba(30,40,80,.08);
}
[data-theme="dark"]{
  --bg:#0e1220;--card:#161b2e;--text:#e8ebf5;--muted:#9aa3ba;--line:#262d45;
  --accent:#818cf8;--accent2:#22d3ee;--good:#4ade80;--bad:#f87171;--shadow:0 6px 24px rgba(0,0,0,.35);
}
*{box-sizing:border-box}
html{scroll-behavior:smooth;scroll-padding-top:72px}
body{margin:0;background:var(--bg);color:var(--text);font-family:system-ui,-apple-system,"Segoe UI",Roboto,Arial,sans-serif;line-height:1.6}
a{color:var(--accent)}
.wrap{max-width:1040px;margin:0 auto;padding:0 20px}

/* nav */
.nav{position:sticky;top:0;z-index:20;background:color-mix(in srgb,var(--bg) 88%,transparent);backdrop-filter:blur(10px);border-bottom:1px solid var(--line)}
.nav .wrap{display:flex;align-items:center;justify-content:space-between;height:60px;gap:12px}
.brand{font-weight:700;letter-spacing:.2px}
.links{display:flex;gap:4px;flex-wrap:wrap;align-items:center}
.links a{color:var(--muted);text-decoration:none;font-size:14px;padding:6px 10px;border-radius:8px}
.links a:hover,.links a.active{color:var(--text);background:var(--card)}
.theme{border:1px solid var(--line);background:var(--card);color:var(--text);border-radius:10px;padding:6px 10px;cursor:pointer;font-size:14px}

/* hero */
.hero{padding:64px 0 40px;text-align:center}
.pill{display:inline-block;font-size:12px;font-weight:600;color:var(--accent);background:color-mix(in srgb,var(--accent) 12%,transparent);padding:4px 12px;border-radius:999px;margin:0 3px 14px}
h1{font-size:clamp(30px,5vw,48px);line-height:1.15;margin:0 0 14px;
  background:linear-gradient(90deg,var(--accent),var(--accent2));-webkit-background-clip:text;background-clip:text;color:transparent}
.lead{max-width:720px;margin:0 auto 26px;color:var(--muted);font-size:18px}
.btn{display:inline-block;text-decoration:none;font-weight:600;padding:11px 20px;border-radius:12px;margin:4px;border:1px solid var(--line);color:var(--text);background:var(--card);cursor:pointer;font-size:15px}
.btn.primary{background:var(--accent);color:#fff;border-color:var(--accent)}
.btn:hover{transform:translateY(-1px)}

/* sections */
section{padding:44px 0}
h2{font-size:28px;margin:0 0 6px}
.sub{color:var(--muted);margin:0 0 24px}
.grid{display:grid;gap:16px}
.g4{grid-template-columns:repeat(auto-fit,minmax(220px,1fr))}
.g2{grid-template-columns:repeat(auto-fit,minmax(300px,1fr))}
.card{background:var(--card);border:1px solid var(--line);border-radius:16px;padding:20px;box-shadow:var(--shadow);transition:transform .2s,border-color .2s}
.card:hover{transform:translateY(-3px);border-color:var(--accent)}
.card h3{margin:8px 0 6px;font-size:17px}
.card p{margin:0;color:var(--muted);font-size:15px}
.ico{font-size:26px}
.tag{display:inline-block;font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.6px;padding:3px 9px;border-radius:999px;color:var(--accent2);background:color-mix(in srgb,var(--accent2) 14%,transparent)}

/* reveal */
.reveal{opacity:0;transform:translateY(18px);transition:opacity .6s ease,transform .6s ease}
.reveal.in{opacity:1;transform:none}

/* chips + details */
.chips{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:18px}
.chip{border:1px solid var(--line);background:var(--card);color:var(--text);border-radius:999px;padding:7px 15px;cursor:pointer;font-size:14px}
.chip.on{background:var(--accent);border-color:var(--accent);color:#fff}
details{margin-top:12px;border-top:1px dashed var(--line);padding-top:10px}
summary{cursor:pointer;font-weight:600;font-size:14px;color:var(--accent)}
details ul{margin:10px 0 0;padding-left:20px;color:var(--muted);font-size:14px}
.hide{display:none}

/* simulator */
.sim{display:grid;gap:20px;grid-template-columns:repeat(auto-fit,minmax(300px,1fr))}
.field{margin-bottom:14px}
.field label{display:flex;justify-content:space-between;font-size:14px;font-weight:600;margin-bottom:5px}
.field label span{color:var(--accent)}
input[type=number]{width:100%;padding:9px 11px;border-radius:10px;border:1px solid var(--line);background:var(--bg);color:var(--text);font-size:15px}
input[type=range]{width:100%;accent-color:var(--accent)}
.res{display:grid;grid-template-columns:1fr 1fr;gap:12px}
.stat{background:var(--bg);border:1px solid var(--line);border-radius:12px;padding:12px}
.stat small{color:var(--muted);display:block;font-size:12px}
.stat b{font-size:20px}
.good{color:var(--good)}.bad{color:var(--bad)}
.note{font-size:13px;color:var(--muted);margin-top:12px}
.verdict{margin-top:14px;padding:12px 14px;border-radius:12px;font-weight:600;font-size:15px;background:var(--bg);border:1px solid var(--line)}

/* dashboard embed */
.frame{border:1px solid var(--line);border-radius:16px;overflow:hidden;background:var(--card);box-shadow:var(--shadow)}
.frame iframe{width:100%;height:600px;border:0;display:block}
.placeholder{padding:48px 24px;text-align:center;color:var(--muted)}
.placeholder code{background:var(--bg);padding:2px 7px;border-radius:6px;border:1px solid var(--line);font-size:13px}

/* checklist */
.bar{height:10px;background:var(--line);border-radius:999px;overflow:hidden;margin:6px 0 18px}
.bar i{display:block;height:100%;width:0;background:linear-gradient(90deg,var(--accent),var(--accent2));transition:width .4s}
.check{display:flex;gap:10px;align-items:flex-start;padding:10px 0;border-bottom:1px solid var(--line);cursor:pointer}
.check:last-child{border-bottom:0}
.check input{margin-top:5px;accent-color:var(--accent)}
.check span{font-size:15px}
.check input:checked + span{color:var(--muted);text-decoration:line-through}

footer{padding:36px 0 60px;text-align:center;color:var(--muted);font-size:14px;border-top:1px solid var(--line)}
@media(max-width:640px){.links a{display:none}.links a.keep{display:inline-block}.frame iframe{height:460px}}
@media(prefers-reduced-motion:reduce){.reveal{transition:none;opacity:1;transform:none}html{scroll-behavior:auto}}
</style>
</head>
<body>

<nav class="nav">
  <div class="wrap">
    <div class="brand">📊 Sales &amp; Profit Dashboard</div>
    <div class="links">
      <a href="#overview">Overview</a>
      <a href="#insights">Insights</a>
      <a href="#simulator">Simulator</a>
      <a href="#dashboard" class="keep">Dashboard</a>
      <a href="#setup">Build steps</a>
      <button class="theme" id="themeBtn" aria-label="Toggle theme">🌙</button>
    </div>
  </div>
</nav>

<header class="hero">
  <div class="wrap">
    <span class="pill">Looker Studio</span><span class="pill">Google Sheets</span><span class="pill">Data Analytics</span>
    <h1>Sales &amp; Profit Analytics Dashboard</h1>
    <p class="lead">A Looker Studio dashboard connected to a Google Sheet that tracks revenue, profit, cost and expenditure by product and by country, compares payment gateways, and maps sales revenue around the world.</p>
    <a class="btn primary" href="#dashboard">View the dashboard</a>
    <a class="btn" href="#insights">See the recommendations</a>
  </div>
</header>

<main>
<!-- OVERVIEW -->
<section id="overview">
  <div class="wrap">
    <h2 class="reveal">What the dashboard tracks</h2>
    <p class="sub reveal">All figures come live from a connected Google Sheet, so the dashboard updates when the sheet does.</p>

    <div class="grid g4">
      <div class="card reveal"><div class="ico">💰</div><h3>Actual revenue</h3><p>Total sales income, split by product and by country.</p></div>
      <div class="card reveal"><div class="ico">📈</div><h3>Profit</h3><p>What is left after actual cost and expenditure are taken out.</p></div>
      <div class="card reveal"><div class="ico">🧾</div><h3>Actual cost</h3><p>Direct cost of the goods sold, including shipping.</p></div>
      <div class="card reveal"><div class="ico">💸</div><h3>Expenditure</h3><p>Operating spend tracked next to revenue so margins stay visible.</p></div>
    </div>

    <div class="grid g2" style="margin-top:16px">
      <div class="card reveal"><div class="ico">💳</div><h3>Payment gateway comparison</h3><p>Compares gateways side by side to see which one suits customers best, with a focus on speed.</p></div>
      <div class="card reveal"><div class="ico">🌍</div><h3>World map of sales revenue</h3><p>A geo map that shades each country by revenue, so top markets and gaps are clear at a glance.</p></div>
    </div>
  </div>
</section>

<!-- INSIGHTS -->
<section id="insights">
  <div class="wrap">
    <h2 class="reveal">Insights &amp; recommendations</h2>
    <p class="sub reveal">Filter by area to see the actions the dashboard points to.</p>

    <div class="chips" id="chips">
      <button class="chip on" data-f="all">All</button>
      <button class="chip" data-f="profit">Profit</button>
      <button class="chip" data-f="payments">Payments</button>
      <button class="chip" data-f="inventory">Inventory</button>
      <button class="chip" data-f="sales">Sales</button>
    </div>

    <div class="grid g2" id="recs">
      <article class="card reveal" data-cat="profit">
        <span class="tag">Profit</span>
        <h3>🚚 Reduce shipping cost to protect profit</h3>
        <p>Shipping is part of actual cost, so every saving goes straight to profit.</p>
        <details><summary>How to act on it</summary>
          <ul>
            <li>Compare carrier rates and negotiate for volume discounts.</li>
            <li>Track shipping cost as a share of revenue for each country.</li>
            <li>Set a minimum order value for free shipping.</li>
          </ul>
        </details>
      </article>

      <article class="card reveal" data-cat="payments">
        <span class="tag">Payments</span>
        <h3>⚡ Focus on the fastest payment gateways</h3>
        <p>Customers prefer gateways that complete payment quickly, so speed should guide which gateway gets priority.</p>
        <details><summary>How to act on it</summary>
          <ul>
            <li>Rank gateways by processing time and success rate in the comparison chart.</li>
            <li>Make the fastest gateway the default option at checkout.</li>
            <li>Review gateways again each quarter.</li>
          </ul>
        </details>
      </article>

      <article class="card reveal" data-cat="inventory">
        <span class="tag">Inventory</span>
        <h3>📦 Track average monthly sales by product first</h3>
        <p>Knowing the average monthly sales for each product shows how much stock is needed, so best sellers do not run out.</p>
        <details><summary>How to act on it</summary>
          <ul>
            <li>Add a monthly average sales metric per product to the dashboard.</li>
            <li>Set a reorder level for each product based on that average.</li>
            <li>Review top sellers first when restocking.</li>
          </ul>
        </details>
      </article>

      <article class="card reveal" data-cat="sales">
        <span class="tag">Sales</span>
        <h3>🌿 Offer at least 10% off health products</h3>
        <p>A discount of at least 10% on health products is meant to increase their sales volume.</p>
        <details><summary>How to act on it</summary>
          <ul>
            <li>Try the discount simulator below with your own numbers.</li>
            <li>Compare health product sales before and after the offer.</li>
            <li>Keep the discount only if profit holds up.</li>
          </ul>
        </details>
      </article>
    </div>
  </div>
</section>

<!-- SIMULATOR -->
<section id="simulator">
  <div class="wrap">
    <h2 class="reveal">Health product discount simulator</h2>
    <p class="sub reveal">Enter your own numbers to see how a discount changes revenue and profit. The starting values are examples only.</p>

    <div class="sim reveal">
      <div class="card">
        <div class="field"><label for="price">Selling price per unit</label><input type="number" id="price" value="100" min="0" step="any"></div>
        <div class="field"><label for="cost">Cost per unit (including shipping)</label><input type="number" id="cost" value="60" min="0" step="any"></div>
        <div class="field"><label for="units">Average monthly units sold</label><input type="number" id="units" value="100" min="0" step="any"></div>
        <div class="field"><label for="disc">Discount <span id="discV">10%</span></label><input type="range" id="disc" min="10" max="40" value="10"></div>
        <div class="field"><label for="uplift">Expected rise in units sold <span id="upV">20%</span></label><input type="range" id="uplift" min="0" max="150" value="20"></div>
      </div>
      <div class="card">
        <div class="res">
          <div class="stat"><small>Revenue before</small><b id="revB">–</b></div>
          <div class="stat"><small>Revenue after</small><b id="revA">–</b></div>
          <div class="stat"><small>Profit before</small><b id="profB">–</b></div>
          <div class="stat"><small>Profit after</small><b id="profA">–</b></div>
        </div>
        <div class="verdict" id="verdict"></div>
        <p class="note" id="be"></p>
      </div>
    </div>
  </div>
</section>

<!-- DASHBOARD -->
<section id="dashboard">
  <div class="wrap">
    <h2 class="reveal">Live dashboard</h2>
    <p class="sub reveal">The Looker Studio report, embedded below.</p>
    <div class="frame reveal" id="frame"></div>
  </div>
</section>

<!-- SETUP -->
<section id="setup">
  <div class="wrap">
    <h2 class="reveal">How it was built</h2>
    <p class="sub reveal">Tick off the steps as you complete them. Your progress is saved in this browser.</p>
    <div class="card reveal">
      <strong id="progLabel">0 of 7 steps done</strong>
      <div class="bar"><i id="progBar"></i></div>
      <div id="steps"></div>
    </div>
  </div>
</section>
</main>

<footer>
  <div class="wrap">
    Built with Looker Studio and Google Sheets ·
    <a id="repoLink" href="#" target="_blank" rel="noopener">View on GitHub</a>
  </div>
</footer>

<script>
/* ====== EDIT THESE TWO LINES ====== */
const DASHBOARD_URL = "";   // paste your Looker Studio embed URL here
const REPO_URL = "https://github.com/amu5236/amu5236.github.io";
/* ================================== */

const $ = s => document.querySelector(s);
const $$ = s => [...document.querySelectorAll(s)];
const store = {
  get(k){ try{ return localStorage.getItem(k); }catch(e){ return null; } },
  set(k,v){ try{ localStorage.setItem(k,v); }catch(e){} }
};

/* theme */
function setTheme(t){
  document.documentElement.setAttribute("data-theme", t);
  $("#themeBtn").textContent = t === "dark" ? "☀️" : "🌙";
  store.set("theme", t);
}
setTheme(store.get("theme") || (matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light"));
$("#themeBtn").onclick = () => setTheme(document.documentElement.getAttribute("data-theme") === "dark" ? "light" : "dark");

/* scroll reveal */
const io = new IntersectionObserver(es => es.forEach(e => { if(e.isIntersecting){ e.target.classList.add("in"); io.unobserve(e.target); } }), {threshold:.12});
$$(".reveal").forEach(el => io.observe(el));

/* active nav link */
const secs = $$("main section");
const spy = new IntersectionObserver(es => es.forEach(e => {
  if(e.isIntersecting){
    $$(".links a").forEach(a => a.classList.toggle("active", a.getAttribute("href") === "#" + e.target.id));
  }
}), {rootMargin:"-40% 0px -55% 0px"});
secs.forEach(s => spy.observe(s));

/* filter recommendations */
$$("#chips .chip").forEach(c => c.onclick = () => {
  $$("#chips .chip").forEach(x => x.classList.remove("on"));
  c.classList.add("on");
  const f = c.dataset.f;
  $$("#recs article").forEach(a => a.classList.toggle("hide", f !== "all" && a.dataset.cat !== f));
});

/* discount simulator */
const fmt = n => n.toLocaleString(undefined, {maximumFractionDigits:0});
function sim(){
  const price = +$("#price").value || 0, cost = +$("#cost").value || 0, units = +$("#units").value || 0;
  const d = +$("#disc").value / 100, u = +$("#uplift").value / 100;
  $("#discV").textContent = Math.round(d*100) + "%";
  $("#upV").textContent = Math.round(u*100) + "%";

  const newPrice = price * (1 - d), newUnits = units * (1 + u);
  const revB = price * units, revA = newPrice * newUnits;
  const profB = (price - cost) * units, profA = (newPrice - cost) * newUnits;

  $("#revB").textContent = fmt(revB);
  $("#revA").textContent = fmt(revA);
  $("#profB").textContent = fmt(profB);
  const pa = $("#profA"); pa.textContent = fmt(profA);
  pa.className = profA >= profB ? "good" : "bad";

  const v = $("#verdict");
  const diff = profA - profB;
  v.textContent = diff >= 0
    ? "Profit would rise by " + fmt(diff) + " per month with these numbers."
    : "Profit would fall by " + fmt(-diff) + " per month with these numbers.";
  v.className = "verdict " + (diff >= 0 ? "good" : "bad");

  const margin = newPrice - cost;
  $("#be").textContent = margin > 0 && price > cost
    ? "To keep the same profit, units sold would need to rise by about " + Math.round(((price - cost) / margin - 1) * 100) + "%."
    : "At this discount the price no longer covers the cost, so extra sales would not restore profit.";
}
["price","cost","units","disc","uplift"].forEach(id => $("#" + id).addEventListener("input", sim));
sim();

/* dashboard embed */
const frame = $("#frame");
if(DASHBOARD_URL){
  const f = document.createElement("iframe");
  f.src = DASHBOARD_URL; f.title = "Looker Studio dashboard"; f.loading = "lazy"; f.allowFullscreen = true;
  frame.appendChild(f);
}else{
  frame.innerHTML = '<div class="placeholder"><div class="ico">🔗</div><p><strong>Dashboard link not added yet.</strong></p><p>In Looker Studio choose <b>Share → Embed report</b>, enable embedding, copy the URL, and paste it into <code>DASHBOARD_URL</code> at the top of the script in this file.</p></div>';
}

/* build-steps checklist */
const STEPS = [
  "Put sales data in a Google Sheet (date, product, country, payment gateway, revenue, cost, shipping, expenditure)",
  "Connect the sheet to Looker Studio as a data source",
  "Add scorecards for revenue, profit, actual cost and expenditure",
  "Add charts that break results down by product and by country",
  "Add a chart that compares payment gateways",
  "Add a geo map showing sales revenue by country",
  "Enable embedding and paste the URL into this page"
];
let done = [];
try{ done = JSON.parse(store.get("steps") || "[]"); }catch(e){ done = []; }
const box = $("#steps");
STEPS.forEach((t, i) => {
  const l = document.createElement("label"); l.className = "check";
  const c = document.createElement("input"); c.type = "checkbox"; c.checked = done.includes(i);
  const s = document.createElement("span"); s.textContent = t;
  c.onchange = () => {
    done = c.checked ? [...new Set([...done, i])] : done.filter(x => x !== i);
    store.set("steps", JSON.stringify(done)); prog();
  };
  l.append(c, s); box.appendChild(l);
});
function prog(){
  $("#progLabel").textContent = done.length + " of " + STEPS.length + " steps done";
  $("#progBar").style.width = (done.length / STEPS.length * 100) + "%";
}
prog();

$("#repoLink").href = REPO_URL;
</script>
</body>
</html>
