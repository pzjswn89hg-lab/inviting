<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Don't Leave Me On Read — reminder card maker</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Karla:wght@400;500;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
  :root{
    --ink:#211A2B;
    --paper:#FFFDF8;
    --pink:#FBD3E9;
    --pink-deep:#F6A6CE;
    --yellow:#FFE68A;
    --mint:#BFEEDD;
    --lav:#E1D3F7;
    --accent:#FF6FA5;
    --card-bg:#FFFDF8;
    --box-bg:var(--yellow);
    --radius-card:28px;
    --radius-ctrl:12px;
    --shadow-off:6px;
  }

  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    font-family:'Karla', sans-serif;
    color:var(--ink);
    background:
      radial-gradient(circle at 12% 8%, #fff3fa 0%, transparent 45%),
      linear-gradient(160deg, var(--pink) 0%, var(--lav) 100%);
    min-height:100vh;
  }

  h1,h2,h3,.display{
    font-family:'Fredoka', sans-serif;
  }

  a{color:inherit;}

  .wrap{
    max-width:1180px;
    margin:0 auto;
    padding:40px 24px 80px;
  }

  header.page-head{
    max-width:640px;
    margin:0 auto 44px;
    text-align:center;
  }
  header.page-head h1{
    font-size:clamp(28px, 4vw, 42px);
    font-weight:700;
    line-height:1.15;
    margin:0 0 12px;
  }
  header.page-head p{
    margin:0;
    font-size:16px;
    line-height:1.6;
    color:#4a3f57;
  }

  .builder{
    display:grid;
    grid-template-columns:minmax(280px, 380px) 1fr;
    gap:32px;
    align-items:start;
  }
  @media (max-width: 860px){
    .builder{grid-template-columns:1fr;}
  }

  .panel{
    background:var(--paper);
    border:3px solid var(--ink);
    border-radius:20px;
    padding:26px 24px 28px;
    box-shadow:var(--shadow-off) var(--shadow-off) 0 var(--ink);
  }
  @media (max-width: 860px){ .panel{order:2;} }

  .field{margin-bottom:20px;}
  .field:last-of-type{margin-bottom:0;}
  .field label{
    display:block;
    font-weight:700;
    font-size:13px;
    letter-spacing:.2px;
    margin-bottom:7px;
  }
  .field .hint{
    display:block;
    font-weight:400;
    font-size:12px;
    color:#7a6d86;
    margin-top:4px;
  }

  input[type=text],
  input[type=date],
  input[type=time],
  textarea,
  select{
    width:100%;
    font-family:'Karla', sans-serif;
    font-size:15px;
    padding:10px 12px;
    border:2.5px solid var(--ink);
    border-radius:var(--radius-ctrl);
    background:#fff;
    color:var(--ink);
    outline-offset:2px;
  }
  input:focus-visible,
  textarea:focus-visible,
  select:focus-visible{
    outline:3px solid var(--accent);
  }
  textarea{resize:vertical; min-height:64px; line-height:1.4;}

  .row2{display:grid; grid-template-columns:1fr 1fr; gap:12px;}

  .swatches{display:flex; gap:10px; flex-wrap:wrap;}
  .swatch{
    width:38px; height:38px;
    border-radius:50%;
    border:2.5px solid var(--ink);
    cursor:pointer;
    padding:0;
    position:relative;
  }
  .swatch[data-theme=bubblegum]{background:linear-gradient(135deg,#FFE68A,#F6A6CE);}
  .swatch[data-theme=mint]{background:linear-gradient(135deg,#BFEEDD,#8FD3C7);}
  .swatch[data-theme=lavender]{background:linear-gradient(135deg,#E1D3F7,#C9B6F2);}
  .swatch[data-theme=peach]{background:linear-gradient(135deg,#FFD9B3,#FFB6A3);}
  .swatch.active::after{
    content:"";
    position:absolute; inset:-6px;
    border:2.5px solid var(--ink);
    border-radius:50%;
  }

  .cta{
    margin-top:26px;
    width:100%;
    font-family:'Fredoka', sans-serif;
    font-weight:600;
    font-size:16px;
    padding:14px 18px;
    background:var(--accent);
    color:#fff;
    border:3px solid var(--ink);
    border-radius:14px;
    box-shadow:5px 5px 0 var(--ink);
    cursor:pointer;
    transition:transform .12s ease, box-shadow .12s ease;
  }
  .cta:hover{transform:translate(-2px,-2px); box-shadow:7px 7px 0 var(--ink);}
  .cta:active{transform:translate(1px,1px); box-shadow:3px 3px 0 var(--ink);}
  @media (prefers-reduced-motion: reduce){
    .cta{transition:none;}
    .cta:hover, .cta:active{transform:none;}
  }

  .stage{
    min-height:520px;
    display:flex;
    align-items:center;
    justify-content:center;
    padding:24px;
  }
  @media (max-width: 860px){ .stage{order:1; min-height:auto; padding:8px;} }

  .card{
    width:100%;
    max-width:380px;
    background:var(--card-bg);
    border:3px solid var(--ink);
    border-radius:var(--radius-card);
    padding:34px 28px 26px;
    box-shadow:10px 10px 0 var(--ink);
    transform:rotate(-1.2deg);
    transition:transform .2s ease;
  }
  .stage:hover .card{transform:rotate(0deg);}
  @media (prefers-reduced-motion: reduce){
    .card{transition:none;}
    .stage:hover .card{transform:rotate(-1.2deg);}
  }

  .card .spark{
    display:block;
    margin:0 auto 14px;
    width:34px; height:34px;
  }

  .card h2{
    text-align:center;
    font-size:26px;
    font-weight:700;
    line-height:1.2;
    margin:0 0 20px;
    word-break:break-word;
  }

  .card .info-box{
    background:var(--box-bg);
    border:2.5px solid var(--ink);
    border-radius:16px;
    padding:16px 18px;
    margin-bottom:20px;
  }
  .card .info-box .line{
    display:flex;
    align-items:center;
    gap:10px;
    font-weight:700;
    font-size:16px;
    padding:4px 0;
  }
  .card .info-box .line + .line{border-top:1.5px dashed rgba(33,26,43,.25);}

  .card .footer-msg{
    text-align:center;
    font-size:15px;
    font-weight:500;
    margin:0 0 20px;
  }

  .card .badge{
    display:inline-flex;
    align-items:center;
    gap:6px;
    margin:0 auto;
    padding:8px 16px;
    background:var(--ink);
    color:#fff;
    border-radius:999px;
    font-size:13px;
    font-weight:700;
  }
  .card .badge-wrap{display:flex; justify-content:center;}

  footer.page-foot{
    text-align:center;
    margin-top:56px;
    font-size:13px;
    color:#6b5f78;
  }
</style>
</head>
<body>

<div class="wrap">
  <header class="page-head">
    <h1>Leave them a note they can't ignore</h1>
    <p>Pick the when, the what, and the vibe. Download it as a card and send it their way — way harder to leave on read than a text.</p>
  </header>

  <div class="builder">
    <form class="panel" id="form">
      <div class="field">
        <label for="title">Headline</label>
        <input type="text" id="title" maxlength="40" value="SLAY! SEE U THEN!">
      </div>

      <div class="field row2">
        <div>
          <label for="date">Date</label>
          <input type="date" id="date">
        </div>
        <div>
          <label for="time">Time</label>
          <input type="time" id="time" value="15:45">
        </div>
      </div>

      <div class="field">
        <label for="activity">What's happening</label>
        <input type="text" id="activity" maxlength="40" value="Park Walk">
      </div>

      <div class="field">
        <label for="emoji">Vibe icon</label>
        <select id="emoji">
          <option value="🌳">🌳 outdoors</option>
          <option value="🍜">🍜 food</option>
          <option value="🎬">🎬 movie</option>
          <option value="☕">☕ coffee</option>
          <option value="🎉">🎉 party</option>
          <option value="💐">💐 date</option>
          <option value="🏖️">🏖️ beach</option>
          <option value="🎮">🎮 games</option>
        </select>
      </div>

      <div class="field">
        <label for="message">Bottom message
          <span class="hint">The line under the details box.</span>
        </label>
        <textarea id="message" maxlength="60">Don't leave me on read! 😎</textarea>
      </div>

      <div class="field">
        <label for="badge">Sign-off badge</label>
        <input type="text" id="badge" maxlength="24" value="made with love">
      </div>

      <div class="field">
        <label>Color mood</label>
        <div class="swatches" id="swatches">
          <button type="button" class="swatch active" data-theme="bubblegum" aria-label="Bubblegum theme"></button>
          <button type="button" class="swatch" data-theme="mint" aria-label="Mint theme"></button>
          <button type="button" class="swatch" data-theme="lavender" aria-label="Lavender theme"></button>
          <button type="button" class="swatch" data-theme="peach" aria-label="Peach theme"></button>
        </div>
      </div>

      <button type="button" class="cta" id="download">Download card</button>
    </form>

    <div class="stage">
      <div class="card" id="card">
        <svg class="spark" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
          <path d="M12 2 L14 10 L22 12 L14 14 L12 22 L10 14 L2 12 L10 10 Z" fill="#211A2B"/>
        </svg>
        <h2 id="c-title">SLAY! SEE U THEN!</h2>
        <div class="info-box">
          <div class="line"><span>📅</span><span id="c-date">Fri, Jul 10</span></div>
          <div class="line"><span>⏰</span><span id="c-time">3:45 PM</span></div>
          <div class="line"><span id="c-emoji">🌳</span><span id="c-activity">Park Walk</span></div>
        </div>
        <p class="footer-msg" id="c-message">Don't leave me on read! 😎</p>
        <div class="badge-wrap">
          <span class="badge">💌 <span id="c-badge">made with love</span></span>
        </div>
      </div>
    </div>
  </div>

  <footer class="page-foot">Built for the person who never leaves you on read.</footer>
</div>

<script>
(function(){
  const $ = (id) => document.getElementById(id);

  function formatDate(value){
    if(!value) return 'Pick a date';
    const [y,m,d] = value.split('-').map(Number);
    const dt = new Date(y, m-1, d);
    return dt.toLocaleDateString('en-US', { weekday:'short', month:'short', day:'numeric' });
  }

  function formatTime(value){
    if(!value) return 'Pick a time';
    let [h,m] = value.split(':').map(Number);
    const suffix = h >= 12 ? 'PM' : 'AM';
    h = h % 12; if(h === 0) h = 12;
    return `${h}:${String(m).padStart(2,'0')} ${suffix}`;
  }

  function sync(){
    $('c-title').textContent = $('title').value || 'SLAY! SEE U THEN!';
    $('c-date').textContent = formatDate($('date').value);
    $('c-time').textContent = formatTime($('time').value);
    $('c-activity').textContent = $('activity').value || 'Something fun';
    $('c-emoji').textContent = $('emoji').value;
    $('c-message').textContent = $('message').value || "Don't leave me on read!";
    $('c-badge').textContent = $('badge').value || 'made with love';
  }

  ['title','date','time','activity','emoji','message','badge'].forEach(id => {
    $(id).addEventListener('input', sync);
    $(id).addEventListener('change', sync);
  });

  const in7 = new Date();
  in7.setDate(in7.getDate() + 7);
  $('date').value = in7.toISOString().slice(0,10);

  const themes = {
    bubblegum: { box:'#FFE68A', card:'#FFFDF8', accent:'#FF6FA5' },
    mint:      { box:'#BFEEDD', card:'#FBFFFD', accent:'#2FAE8F' },
    lavender:  { box:'#E1D3F7', card:'#FDFBFF', accent:'#8A63D2' },
    peach:     { box:'#FFD9B3', card:'#FFFCF8', accent:'#FF8A5C' },
  };

  document.getElementById('swatches').addEventListener('click', (e) => {
    const btn = e.target.closest('.swatch');
    if(!btn) return;
    document.querySelectorAll('.swatch').forEach(s => s.classList.remove('active'));
    btn.classList.add('active');
    const t = themes[btn.dataset.theme];
    const root = document.documentElement.style;
    root.setProperty('--box-bg', t.box);
    root.setProperty('--card-bg', t.card);
    root.setProperty('--accent', t.accent);
  });

  $('download').addEventListener('click', () => {
    const card = $('card');
    const btn = $('download');
    const original = btn.textContent;
    btn.textContent = 'Preparing…';
    html2canvas(card, { backgroundColor:null, scale:3 }).then(canvas => {
      const link = document.createElement('a');
      link.download = 'reminder-card.png';
      link.href = canvas.toDataURL('image/png');
      link.click();
      btn.textContent = original;
    }).catch(() => { btn.textContent = original; });
  });

  sync();
})();
</script>

</body>
</html>
