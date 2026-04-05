<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MAGUVA – Empowering Women</title>
<script>
/* Priya: when you open this site via `node server.mjs` (or start-maguva.bat), chat uses same-origin /chat automatically. Opening the file directly (file://) keeps offline replies unless you set MAGUVA_CHAT_URL yourself. */
(function () {
  if (location.protocol === 'http:' || location.protocol === 'https:') {
    window.MAGUVA_CHAT_URL = location.origin + '/chat';
  } else {
    window.MAGUVA_CHAT_URL = '';
  }
})();
</script>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Nunito:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<style>
*{margin:0;padding:0;box-sizing:border-box;}
:root{
  --rose:#F4A7B9;--blush:#FDDDE6;--rose-deep:#d4607e;
  --lav:#C9B8E8;--lav-light:#EDE8F9;--lav-deep:#7c5cbf;
  --peach:#FBBFA7;--peach-light:#FEE9E2;--peach-deep:#c0633a;
  --sage:#B5D5C5;--sage-light:#E3F2EB;--sage-deep:#3a8c65;
  --gold:#E8C97A;--gold-light:#FDF3DC;--gold-deep:#a07c20;
  --white:#FFFCFA;--cream:#FDF7F2;--card:#ffffffee;
  --dark:#2D1F28;--mid:#6D5A65;--light:#A8909A;
  --shadow:0 8px 32px rgba(180,120,140,0.13);
  --shadow-lg:0 20px 60px rgba(180,120,140,0.2);
  --r:22px;--rs:12px;
}
html{scroll-behavior:smooth;}
body{font-family:'Nunito',sans-serif;background:var(--cream);color:var(--dark);overflow-x:hidden;}

/* ═══ PAGE SYSTEM ═══ */
.page{display:none;min-height:100vh;animation:pageIn 0.5s ease;}
.page.active{display:block;}
@keyframes pageIn{from{opacity:0;transform:translateY(16px);}to{opacity:1;transform:translateY(0);}}

/* ═══ NAV ═══ */
nav{position:fixed;top:0;left:0;right:0;z-index:200;
  display:flex;align-items:center;justify-content:space-between;
  padding:14px 40px;background:rgba(253,247,242,0.92);
  backdrop-filter:blur(20px);border-bottom:1px solid rgba(244,167,185,0.2);}
.nav-logo{font-family:'Cormorant Garamond',serif;font-size:26px;font-weight:600;
  background:linear-gradient(135deg,var(--rose-deep),var(--lav-deep));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;
  letter-spacing:3px;cursor:pointer;}
.nav-menu{display:flex;gap:6px;list-style:none;}
.nav-menu li a{
  font-size:12px;font-weight:600;color:var(--mid);text-decoration:none;
  padding:7px 14px;border-radius:50px;transition:all 0.2s;letter-spacing:0.3px;
  cursor:pointer;display:block;}
.nav-menu li a:hover,.nav-menu li a.active{background:var(--blush);color:var(--rose-deep);}
.nav-sos{background:linear-gradient(135deg,#e05555,#c03030);color:white;border:none;
  padding:8px 18px;border-radius:50px;font-size:12px;font-weight:700;cursor:pointer;
  animation:pulse-sos 2s infinite;letter-spacing:1px;}
@keyframes pulse-sos{0%,100%{box-shadow:0 0 0 0 rgba(224,85,85,0.4);}50%{box-shadow:0 0 0 8px rgba(224,85,85,0);}}

/* ═══ HERO ═══ */
.hero{min-height:100vh;display:flex;align-items:center;justify-content:center;
  padding:100px 40px 60px;position:relative;overflow:hidden;
  background:radial-gradient(ellipse 90% 70% at 60% 10%,#FDDDE6 0%,var(--cream) 55%);}
.hero-orb{position:absolute;border-radius:50%;filter:blur(70px);opacity:0.35;pointer-events:none;}
.o1{width:600px;height:600px;background:var(--lav);top:-150px;right:-150px;animation:orb 9s ease-in-out infinite;}
.o2{width:400px;height:400px;background:var(--peach);bottom:-80px;left:-100px;animation:orb 11s ease-in-out infinite 2s;}
.o3{width:300px;height:300px;background:var(--rose);top:45%;left:25%;animation:orb 7s ease-in-out infinite 1s;}
@keyframes orb{0%,100%{transform:translateY(0)scale(1);}50%{transform:translateY(-24px)scale(1.05);}}

.hero-inner{text-align:center;position:relative;z-index:1;max-width:820px;}
.hero-pill{display:inline-flex;align-items:center;gap:8px;
  background:rgba(244,167,185,0.18);border:1px solid rgba(244,167,185,0.4);
  padding:7px 20px;border-radius:50px;font-size:12px;font-weight:600;
  color:var(--mid);letter-spacing:2px;text-transform:uppercase;margin-bottom:28px;
  animation:fadeUp 0.7s ease both;}
.hero-h1{font-family:'Cormorant Garamond',serif;
  font-size:clamp(80px,12vw,130px);font-weight:300;line-height:.95;
  letter-spacing:10px;color:var(--dark);animation:fadeUp 0.7s ease 0.1s both;}
.hero-h1 em{font-style:italic;
  background:linear-gradient(135deg,var(--rose-deep),var(--lav-deep),var(--peach-deep));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;}
.hero-sub{font-family:'Cormorant Garamond',serif;font-size:21px;font-style:italic;
  color:var(--mid);margin:22px 0 14px;animation:fadeUp 0.7s ease 0.2s both;}
.hero-p{font-size:15px;color:var(--light);line-height:1.75;max-width:480px;
  margin:0 auto 44px;animation:fadeUp 0.7s ease 0.3s both;}
.hero-btns{display:flex;gap:14px;justify-content:center;flex-wrap:wrap;animation:fadeUp 0.7s ease 0.4s both;}
.btn-glow{background:linear-gradient(135deg,var(--rose-deep),#b84060);color:white;
  border:none;padding:16px 38px;border-radius:50px;font-size:14px;font-weight:700;
  cursor:pointer;transition:all 0.3s;box-shadow:0 8px 28px rgba(212,96,126,0.4);}
.btn-glow:hover{transform:translateY(-3px);box-shadow:0 14px 38px rgba(212,96,126,0.5);}
.btn-outline{background:transparent;color:var(--mid);
  border:2px solid rgba(180,120,140,0.28);padding:16px 38px;
  border-radius:50px;font-size:14px;font-weight:700;cursor:pointer;transition:all 0.3s;}
.btn-outline:hover{border-color:var(--rose);color:var(--rose-deep);}

.hero-stats{display:flex;gap:56px;justify-content:center;margin-top:72px;animation:fadeUp 0.7s ease 0.5s both;}
.stat-n{font-family:'Cormorant Garamond',serif;font-size:40px;font-weight:600;
  background:linear-gradient(135deg,var(--rose-deep),var(--lav-deep));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;display:block;}
.stat-l{font-size:11px;color:var(--light);letter-spacing:1.5px;text-transform:uppercase;}

@keyframes fadeUp{from{opacity:0;transform:translateY(28px);}to{opacity:1;transform:translateY(0);}}

/* ═══ CATEGORY CARDS ═══ */
.cats-section{background:var(--white);padding:80px 40px;}
.section-label{font-size:11px;text-transform:uppercase;letter-spacing:3px;color:var(--light);margin-bottom:10px;}
.section-title{font-family:'Cormorant Garamond',serif;font-size:clamp(32px,4vw,52px);
  font-weight:300;line-height:1.15;margin-bottom:14px;}
.section-title em{font-style:italic;}
.section-p{font-size:14px;color:var(--light);line-height:1.7;max-width:480px;}
.sec-head{margin-bottom:50px;}
.container{max-width:1160px;margin:0 auto;}

.cats-grid{display:grid;grid-template-columns:repeat(5,1fr);gap:18px;}
@media(max-width:900px){.cats-grid{grid-template-columns:repeat(2,1fr);}}

.cat-tile{border-radius:var(--r);padding:36px 22px;text-align:center;cursor:pointer;
  border:1.5px solid transparent;transition:all 0.35s cubic-bezier(.34,1.56,.64,1);
  text-decoration:none;display:block;position:relative;overflow:hidden;}
.cat-tile::before{content:'';position:absolute;inset:0;opacity:0;transition:opacity 0.3s;
  background:linear-gradient(135deg,rgba(255,255,255,0.4),rgba(255,255,255,0));}
.cat-tile:hover{transform:translateY(-10px) scale(1.03);box-shadow:var(--shadow-lg);}
.cat-tile:hover::before{opacity:1;}
.cat-emoji{font-size:44px;margin-bottom:18px;display:block;
  filter:drop-shadow(0 4px 10px rgba(0,0,0,0.1));transition:transform 0.3s;}
.cat-tile:hover .cat-emoji{transform:scale(1.1) rotate(-5deg);}
.cat-tname{font-family:'Cormorant Garamond',serif;font-size:20px;font-weight:600;margin-bottom:6px;}
.cat-tdesc{font-size:12px;line-height:1.5;}

.t-health{background:linear-gradient(145deg,#fff0f4,#ffd6e4);border-color:rgba(244,167,185,0.4);}
.t-health .cat-tname{color:#c85c7a;}
.t-health .cat-tdesc{color:#d4788e;}
.t-safety{background:linear-gradient(145deg,#fff6f0,#ffe0cc);border-color:rgba(251,191,167,0.4);}
.t-safety .cat-tname{color:#c0633a;}
.t-safety .cat-tdesc{color:#d4784e;}
.t-mental{background:linear-gradient(145deg,#f5f0ff,#e8dcfc);border-color:rgba(201,184,232,0.4);}
.t-mental .cat-tname{color:#7c5cbf;}
.t-mental .cat-tdesc{color:#9070cc;}
.t-career{background:linear-gradient(145deg,#efffed,#d0f0d8);border-color:rgba(181,213,197,0.4);}
.t-career .cat-tname{color:#3a8c65;}
.t-career .cat-tdesc{color:#52a87e;}
.t-legal{background:linear-gradient(145deg,#fffbef,#ffefc4);border-color:rgba(232,201,122,0.4);}
.t-legal .cat-tname{color:#a07c20;}
.t-legal .cat-tdesc{color:#b89030;}

/* ═══ SHARED PAGE HEADER ═══ */
.page-hero{padding:100px 40px 60px;position:relative;overflow:hidden;}
.page-hero-inner{max-width:1160px;margin:0 auto;display:flex;align-items:center;justify-content:space-between;gap:40px;}
.page-hero-text{flex:1;}
.back-btn{display:inline-flex;align-items:center;gap:8px;
  background:rgba(255,255,255,0.8);border:1px solid rgba(180,120,140,0.2);
  padding:8px 18px;border-radius:50px;font-size:13px;font-weight:600;
  color:var(--mid);cursor:pointer;margin-bottom:24px;transition:all 0.2s;}
.back-btn:hover{background:white;color:var(--rose-deep);}
.page-title{font-family:'Cormorant Garamond',serif;font-size:clamp(42px,6vw,72px);
  font-weight:300;line-height:1.1;margin-bottom:14px;}
.page-desc{font-size:15px;color:var(--mid);line-height:1.7;max-width:500px;}

/* ═══ SUBPAGE NAV TABS ═══ */
.sub-tabs{display:flex;gap:10px;flex-wrap:wrap;padding:0 40px 40px;max-width:1160px;margin:0 auto;}
.sub-tab{padding:10px 22px;border-radius:50px;font-size:13px;font-weight:600;
  border:2px solid transparent;cursor:pointer;transition:all 0.25s;background:white;}
.sub-tab:hover{transform:translateY(-2px);box-shadow:var(--shadow);}
.sub-tab.on{color:white;border-color:transparent;}

/* ═══ CONTENT PANELS ═══ */
.panel{display:none;padding:0 40px 80px;max-width:1160px;margin:0 auto;}
.panel.on{display:block;animation:fadeUp 0.45s ease;}

/* ═══ CARDS ═══ */
.card{background:var(--card);border-radius:var(--r);padding:28px;box-shadow:var(--shadow);}
.card-title{font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:8px;}
.grid-2{display:grid;grid-template-columns:1fr 1fr;gap:20px;}
.grid-3{display:grid;grid-template-columns:repeat(3,1fr);gap:18px;}
@media(max-width:768px){.grid-2,.grid-3{grid-template-columns:1fr;}}

/* ═══ HEALTH PAGE ═══ */
.h-survey{background:linear-gradient(135deg,#fff0f4,#fdf0ff);border-radius:var(--r);padding:40px;box-shadow:var(--shadow-lg);}
.survey-step{display:none;animation:fadeUp 0.4s ease;}
.survey-step.on{display:block;}
.survey-q{font-family:'Cormorant Garamond',serif;font-size:26px;margin-bottom:8px;color:var(--dark);}
.survey-sub{font-size:14px;color:var(--light);margin-bottom:28px;}
.survey-options{display:flex;flex-wrap:wrap;gap:10px;margin-bottom:28px;}
.s-opt{padding:11px 22px;border-radius:50px;border:2px solid rgba(244,167,185,0.3);
  font-size:13px;font-weight:600;cursor:pointer;transition:all 0.2s;background:white;color:var(--mid);}
.s-opt:hover{border-color:var(--rose);color:var(--rose-deep);}
.s-opt.picked{background:linear-gradient(135deg,var(--rose),#e8849c);color:white;border-color:transparent;
  box-shadow:0 4px 14px rgba(244,167,185,0.4);}
.survey-nav{display:flex;gap:12px;align-items:center;}
.btn-next{background:linear-gradient(135deg,var(--rose-deep),#b84060);color:white;
  border:none;padding:13px 32px;border-radius:50px;font-size:14px;font-weight:700;cursor:pointer;
  transition:all 0.3s;box-shadow:0 6px 20px rgba(212,96,126,0.35);}
.btn-next:hover{transform:translateY(-2px);}
.btn-back-s{background:none;border:none;color:var(--light);font-size:13px;cursor:pointer;font-weight:600;}
.progress-bar{height:4px;background:rgba(244,167,185,0.2);border-radius:10px;margin-bottom:32px;}
.progress-fill{height:100%;background:linear-gradient(90deg,var(--rose),var(--lav));border-radius:10px;transition:width 0.4s ease;}
.survey-result{background:white;border-radius:var(--rs);padding:28px;border:1.5px solid rgba(244,167,185,0.3);}
.res-title{font-family:'Cormorant Garamond',serif;font-size:28px;color:var(--rose-deep);margin-bottom:10px;}
.res-list{list-style:none;margin-top:14px;}
.res-list li{padding:8px 0;border-bottom:1px solid rgba(0,0,0,0.05);font-size:13px;color:var(--mid);display:flex;gap:10px;align-items:flex-start;}
.res-list li::before{content:'✦';color:var(--rose);font-size:10px;margin-top:3px;flex-shrink:0;}

/* Risk Meter */
.risk-wrap{background:linear-gradient(135deg,#fff0f4,#f5eeff);border-radius:var(--r);padding:36px;box-shadow:var(--shadow);}
.risk-header{margin-bottom:28px;}
.risk-header h3{font-family:'Cormorant Garamond',serif;font-size:28px;margin-bottom:6px;}
.sliders-area{display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-bottom:28px;}
@media(max-width:600px){.sliders-area{grid-template-columns:1fr;}}
.sl-group{}
.sl-top{display:flex;justify-content:space-between;font-size:13px;color:var(--mid);margin-bottom:8px;font-weight:600;}
.sl-val{font-weight:700;color:var(--rose-deep);}
input[type=range]{width:100%;-webkit-appearance:none;height:5px;border-radius:10px;outline:none;cursor:pointer;background:#f0dfe4;}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:18px;height:18px;border-radius:50%;background:white;box-shadow:0 2px 8px rgba(212,96,126,0.4);border:2px solid var(--rose);}
.risk-gauge{background:white;border-radius:var(--rs);padding:24px;text-align:center;border:1.5px solid rgba(244,167,185,0.25);}
.gauge-ring{width:120px;height:120px;border-radius:50%;margin:0 auto 16px;display:flex;align-items:center;justify-content:center;flex-direction:column;position:relative;font-family:'Cormorant Garamond',serif;font-size:22px;font-weight:600;transition:all 0.5s;}
.gauge-lbl{font-size:11px;font-weight:600;letter-spacing:1px;text-transform:uppercase;}
.gauge-msg{font-size:13px;color:var(--mid);line-height:1.6;}

/* Meal Plan */
.meal-tabs{display:flex;gap:8px;margin-bottom:24px;flex-wrap:wrap;}
.meal-tab{padding:8px 18px;border-radius:50px;font-size:13px;font-weight:600;cursor:pointer;
  border:2px solid rgba(181,213,197,0.4);transition:all 0.2s;background:white;color:var(--mid);}
.meal-tab.on{background:var(--sage-deep);color:white;border-color:transparent;}
.meal-panel{display:none;animation:fadeUp 0.3s ease;}
.meal-panel.on{display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:14px;}
.meal-card{background:white;border-radius:var(--rs);padding:20px;text-align:center;box-shadow:0 2px 12px rgba(0,0,0,0.05);border:1px solid rgba(181,213,197,0.3);transition:all 0.25s;}
.meal-card:hover{transform:translateY(-4px);box-shadow:var(--shadow);}
.meal-time{font-size:10px;text-transform:uppercase;letter-spacing:2px;color:var(--sage-deep);font-weight:700;margin-bottom:8px;}
.meal-icon{font-size:30px;margin-bottom:8px;display:block;}
.meal-name{font-size:13px;font-weight:600;color:var(--dark);margin-bottom:4px;}
.meal-desc{font-size:11px;color:var(--light);line-height:1.4;}
.meal-tag{display:inline-block;font-size:10px;padding:2px 8px;border-radius:20px;margin-top:6px;font-weight:700;}

/* Condition Cards */
.cond-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:18px;}
.cond-card{background:white;border-radius:var(--r);padding:26px;box-shadow:var(--shadow);border-top:4px solid var(--rose);cursor:pointer;transition:all 0.3s;}
.cond-card:hover{transform:translateY(-5px);box-shadow:var(--shadow-lg);}
.cond-card h3{font-family:'Cormorant Garamond',serif;font-size:22px;color:var(--rose-deep);margin-bottom:10px;}
.cond-card p{font-size:13px;color:var(--mid);line-height:1.6;margin-bottom:14px;}
.cond-tags{display:flex;flex-wrap:wrap;gap:6px;}
.tag{display:inline-block;padding:3px 10px;border-radius:20px;font-size:11px;font-weight:700;background:var(--blush);color:var(--rose-deep);}
.expand-btn{font-size:12px;color:var(--rose-deep);font-weight:700;background:none;border:none;cursor:pointer;padding:0;margin-top:10px;}
.cond-expand{max-height:0;overflow:hidden;transition:max-height 0.4s ease;}
.cond-expand.open{max-height:500px;}
.expand-inner{padding-top:14px;border-top:1px solid rgba(0,0,0,0.05);margin-top:14px;}
.expand-inner h4{font-size:12px;font-weight:700;color:var(--dark);margin-bottom:6px;text-transform:uppercase;letter-spacing:1px;}
.expand-inner ul{list-style:none;margin-bottom:12px;}
.expand-inner ul li{font-size:12px;color:var(--mid);padding:3px 0;display:flex;gap:8px;}
.expand-inner ul li::before{content:'→';color:var(--rose);}

/* Checklist */
.checklist-wrap{background:white;border-radius:var(--r);padding:32px;box-shadow:var(--shadow);}
.cl-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:24px;}
.cl-header h3{font-family:'Cormorant Garamond',serif;font-size:26px;}
.cl-score{font-size:13px;color:var(--mid);font-weight:600;}
.cl-item{display:flex;align-items:center;gap:14px;padding:14px 0;border-bottom:1px solid rgba(0,0,0,0.04);cursor:pointer;transition:opacity 0.2s;}
.cl-item:last-child{border-bottom:none;}
.cl-box{width:24px;height:24px;border-radius:8px;border:2px solid var(--lav);
  flex-shrink:0;transition:all 0.2s;display:flex;align-items:center;justify-content:center;background:white;}
.cl-item.done .cl-box{background:var(--lav-deep);border-color:var(--lav-deep);}
.cl-item.done .cl-box::after{content:'✓';color:white;font-size:13px;font-weight:800;}
.cl-item.done .cl-txt{text-decoration:line-through;color:var(--light);}
.cl-txt{font-size:14px;color:var(--mid);}
.cl-progress{height:6px;background:rgba(201,184,232,0.25);border-radius:10px;margin-top:20px;overflow:hidden;}
.cl-pfill{height:100%;background:linear-gradient(90deg,var(--lav),var(--lav-deep));border-radius:10px;transition:width 0.4s ease;width:0%;}

</style>
</head>
<body>

<!-- NAV -->
<nav id="mainNav">
  <div class="nav-logo" onclick="goPage('home')">MAGUVA</div>
  <ul class="nav-menu">
    <li><a onclick="goPage('health')" id="nav-health">🏥 Health</a></li>
    <li><a onclick="goPage('safety')" id="nav-safety">🚨 Safety</a></li>
    <li><a onclick="goPage('mental')" id="nav-mental">🧠 Mind</a></li>
    <li><a onclick="goPage('career')" id="nav-career">💼 Career</a></li>
    <li><a onclick="goPage('legal')" id="nav-legal">⚖️ Legal</a></li>
  </ul>
  <button class="nav-sos" onclick="goPage('safety');setTimeout(()=>openSOSModal(),300)">🆘 SOS</button>
</nav>

<!-- ══════════════════════════════════════ HOME PAGE ══════════════════════════════════════ -->
<div id="page-home" class="page active">
  <section class="hero">
    <div class="hero-orb o1"></div>
    <div class="hero-orb o2"></div>
    <div class="hero-orb o3"></div>
    <div class="hero-inner">
      <div class="hero-pill">🌸 Your Safe Space, Always</div>
      <h1 class="hero-h1">MAG<em>UVA</em></h1>
      <p class="hero-sub">Empowering Women with Knowledge & Solutions</p>
      <p class="hero-p">One platform for health, safety, mental wellness, career growth and legal awareness — built with love for every woman.</p>
      <div class="hero-btns">
        <button class="btn-glow" onclick="goPage('health')">Start Your Journey ✨</button>
        <button class="btn-outline" onclick="document.getElementById('home-cats').scrollIntoView({behavior:'smooth'})">Explore Sections</button>
      </div>
      <div class="hero-stats">
        <div><span class="stat-n" id="countTopics">0</span><span class="stat-l">Topics</span></div>
        <div><span class="stat-n" id="countFeatures">0</span><span class="stat-l">Features</span></div>
        <div><span class="stat-n">24/7</span><span class="stat-l">Available</span></div>
      </div>
    </div>
  </section>

  <section class="cats-section" id="home-cats">
    <div class="container">
      <div class="sec-head">
        <div class="section-label">Explore</div>
        <h2 class="section-title">What do you need <em>today?</em></h2>
        <p class="section-p">Each section is a world of resources built specifically for you. Tap to explore.</p>
      </div>
      <div class="cats-grid">
        <div class="cat-tile t-health" onclick="goPage('health')">
          <span class="cat-emoji">🏥</span>
          <div class="cat-tname">Health</div>
          <div class="cat-tdesc">PCOS · Thyroid · Anemia · Pregnancy</div>
        </div>
        <div class="cat-tile t-safety" onclick="goPage('safety')">
          <span class="cat-emoji">🚨</span>
          <div class="cat-tname">Safety</div>
          <div class="cat-tdesc">Emergency · Travel · Online · Workplace</div>
        </div>
        <div class="cat-tile t-mental" onclick="goPage('mental')">
          <span class="cat-emoji">🧠</span>
          <div class="cat-tname">Mental Health</div>
          <div class="cat-tdesc">Mood · Stress · AI Chat · Self-care</div>
        </div>
        <div class="cat-tile t-career" onclick="goPage('career')">
          <span class="cat-emoji">💼</span>
          <div class="cat-tname">Career</div>
          <div class="cat-tdesc">Growth · Finance · Confidence · Skills</div>
        </div>
        <div class="cat-tile t-legal" onclick="goPage('legal')">
          <span class="cat-emoji">⚖️</span>
          <div class="cat-tname">Legal Rights</div>
          <div class="cat-tdesc">POSH · DV Act · Property · Maternity</div>
        </div>
      </div>
    </div>
  </section>
</div>

<!-- ══════════════════════════════════════ HEALTH PAGE ══════════════════════════════════════ -->
<div id="page-health" class="page">
  <div class="page-hero" style="background:linear-gradient(135deg,#fff0f4 0%,#fdf0ff 100%);">
    <div class="page-hero-inner">
      <div class="page-hero-text">
        <button class="back-btn" onclick="goPage('home')">← Back to Home</button>
        <h1 class="page-title" style="color:#c85c7a;">Women's<br><em style="color:var(--lav-deep);">Health Hub</em></h1>
        <p class="page-desc">Understand your body, assess your risks, get personalized meal plans, and build daily wellness habits.</p>
      </div>
      <div style="font-size:80px;opacity:0.3;">🏥</div>
    </div>
  </div>

  <div class="sub-tabs">
    <div class="sub-tab on" style="background:var(--rose-deep);color:white;" onclick="openSubTab('health','survey',this)">🔍 Health Survey</div>
    <div class="sub-tab" onclick="openSubTab('health','risk',this)" style="--c:var(--rose-deep)">📊 Risk Meter</div>
    <div class="sub-tab" onclick="openSubTab('health','conditions',this)">💊 Conditions</div>
    <div class="sub-tab" onclick="openSubTab('health','meal',this)">🍽️ Meal Plans</div>
    <div class="sub-tab" onclick="openSubTab('health','checklist',this)">✔️ Daily Checklist</div>
  </div>

  <!-- SURVEY PANEL -->
  <div id="hp-survey" class="panel on">
    <div class="h-survey">
      <div class="progress-bar"><div class="progress-fill" id="surveyProgress" style="width:14%"></div></div>
      <!-- Step 1 -->
      <div class="survey-step on" id="ss1">
        <div class="survey-q">How old are you?</div>
        <div class="survey-sub">This helps us tailor health advice to your life stage.</div>
        <div class="survey-options">
          <div class="s-opt" onclick="pickSurvey(this,'age','Under 18')">Under 18</div>
          <div class="s-opt" onclick="pickSurvey(this,'age','18–25')">18–25</div>
          <div class="s-opt" onclick="pickSurvey(this,'age','26–35')">26–35</div>
          <div class="s-opt" onclick="pickSurvey(this,'age','36–45')">36–45</div>
          <div class="s-opt" onclick="pickSurvey(this,'age','45+')">45+</div>
        </div>
        <div class="survey-nav"><button class="btn-next" onclick="surveyNext(1)">Next →</button></div>
      </div>
      <!-- Step 2 -->
      <div class="survey-step" id="ss2">
        <div class="survey-q">What symptoms are you experiencing?</div>
        <div class="survey-sub">Select all that apply.</div>
        <div class="survey-options">
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Fatigue')">😴 Fatigue</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Irregular periods')">🌀 Irregular periods</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Weight gain')">⚖️ Weight gain</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Hair loss')">💇 Hair loss</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Acne')">✨ Acne</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Mood swings')">🎭 Mood swings</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Pelvic pain')">💫 Pelvic pain</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','Cold sensitivity')">🥶 Cold sensitivity</div>
          <div class="s-opt" onclick="toggleSurvey(this,'sym','None')">✅ None right now</div>
        </div>
        <div class="survey-nav">
          <button class="btn-next" onclick="surveyNext(2)">Next →</button>
          <button class="btn-back-s" onclick="surveyBack(2)">← Back</button>
        </div>
      </div>
      <!-- Step 3 -->
      <div class="survey-step" id="ss3">
        <div class="survey-q">How would you describe your diet?</div>
        <div class="survey-sub">Be honest — no judgment here!</div>
        <div class="survey-options">
          <div class="s-opt" onclick="pickSurvey(this,'diet','Balanced & nutritious')">🥗 Balanced & nutritious</div>
          <div class="s-opt" onclick="pickSurvey(this,'diet','Mostly vegetarian')">🌿 Mostly vegetarian</div>
          <div class="s-opt" onclick="pickSurvey(this,'diet','Skip meals often')">⏭️ Skip meals often</div>
          <div class="s-opt" onclick="pickSurvey(this,'diet','Lots of processed food')">🍔 Lots of processed food</div>
          <div class="s-opt" onclick="pickSurvey(this,'diet','Iron / protein deficient')">🩸 Iron/protein deficient</div>
        </div>
        <div class="survey-nav">
          <button class="btn-next" onclick="surveyNext(3)">Next →</button>
          <button class="btn-back-s" onclick="surveyBack(3)">← Back</button>
        </div>
      </div>
      <!-- Step 4 -->
      <div class="survey-step" id="ss4">
        <div class="survey-q">How is your sleep & stress level?</div>
        <div class="survey-sub">These affect your hormonal health significantly.</div>
        <div class="survey-options">
          <div class="s-opt" onclick="pickSurvey(this,'stress','Great sleep, low stress')">😌 Great sleep, low stress</div>
          <div class="s-opt" onclick="pickSurvey(this,'stress','Okay sleep, moderate stress')">😐 Okay sleep, moderate stress</div>
          <div class="s-opt" onclick="pickSurvey(this,'stress','Poor sleep, high stress')">😓 Poor sleep, high stress</div>
          <div class="s-opt" onclick="pickSurvey(this,'stress','Insomnia / burnout')">🤯 Insomnia / burnout</div>
        </div>
        <div class="survey-nav">
          <button class="btn-next" onclick="surveyNext(4)">Next →</button>
          <button class="btn-back-s" onclick="surveyBack(4)">← Back</button>
        </div>
      </div>
      <!-- Step 5 -->
      <div class="survey-step" id="ss5">
        <div class="survey-q">When did you last visit a doctor?</div>
        <div class="survey-sub">Preventive care is always better than reactive care.</div>
        <div class="survey-options">
          <div class="s-opt" onclick="pickSurvey(this,'doctor','Last 3 months')">✅ Last 3 months</div>
          <div class="s-opt" onclick="pickSurvey(this,'doctor','6 months ago')">📅 6 months ago</div>
          <div class="s-opt" onclick="pickSurvey(this,'doctor','Over a year ago')">⏰ Over a year ago</div>
          <div class="s-opt" onclick="pickSurvey(this,'doctor','Never/rarely')">😬 Never / rarely</div>
        </div>
        <div class="survey-nav">
          <button class="btn-next" onclick="surveyNext(5)">See My Results 🌸</button>
          <button class="btn-back-s" onclick="surveyBack(5)">← Back</button>
        </div>
      </div>
      <!-- Result -->
      <div class="survey-step" id="ss6">
        <div class="survey-result" id="surveyResultBox"></div>
        <div style="margin-top:20px;display:flex;gap:12px;flex-wrap:wrap;">
          <button class="btn-next" onclick="surveyReset()">Take Again 🔄</button>
          <button class="btn-next" style="background:linear-gradient(135deg,var(--lav-deep),#9070cc);" onclick="openSubTab('health','conditions',document.querySelector('.sub-tab:nth-child(3))'))">View Conditions →</button>
        </div>
      </div>
    </div>
  </div>

  <!-- RISK METER PANEL -->
  <div id="hp-risk" class="panel">
    <div class="risk-wrap">
      <div class="risk-header">
        <h3>📊 Interactive Health Risk Meter</h3>
        <p style="font-size:14px;color:var(--light)">Adjust the sliders to reflect your current state. Your risk level updates in real time.</p>
      </div>
      <div class="sliders-area">
        <div class="sl-group">
          <div class="sl-top"><span>😴 Fatigue Level</span><span class="sl-val" id="rv1">5</span></div>
          <input type="range" min="1" max="10" value="5" id="rs1" oninput="upRisk()">
        </div>
        <div class="sl-group">
          <div class="sl-top"><span>🌀 Hormonal Irregularity</span><span class="sl-val" id="rv2">4</span></div>
          <input type="range" min="1" max="10" value="4" id="rs2" oninput="upRisk()">
        </div>
        <div class="sl-group">
          <div class="sl-top"><span>😰 Stress Level</span><span class="sl-val" id="rv3">5</span></div>
          <input type="range" min="1" max="10" value="5" id="rs3" oninput="upRisk()">
        </div>
        <div class="sl-group">
          <div class="sl-top"><span>😴 Sleep Quality</span><span class="sl-val" id="rv4">6</span></div>
          <input type="range" min="1" max="10" value="6" id="rs4" oninput="upRisk()">
        </div>
        <div class="sl-group">
          <div class="sl-top"><span>🍽️ Nutrition Score</span><span class="sl-val" id="rv5">6</span></div>
          <input type="range" min="1" max="10" value="6" id="rs5" oninput="upRisk()">
        </div>
        <div class="sl-group">
          <div class="sl-top"><span>🏃 Exercise Frequency</span><span class="sl-val" id="rv6">5</span></div>
          <input type="range" min="1" max="10" value="5" id="rs6" oninput="upRisk()">
        </div>
      </div>
      <div class="risk-gauge">
        <div class="gauge-ring" id="gaugeRing">
          <div id="gaugeScore" style="font-size:28px;font-weight:700;"></div>
          <div class="gauge-lbl" id="gaugeLabel"></div>
        </div>
        <div class="gauge-msg" id="gaugeMsg"></div>
      </div>
    </div>
  </div>

  <!-- CONDITIONS PANEL -->
  <div id="hp-conditions" class="panel">
    <div class="cond-grid">
      <div class="cond-card" onclick="toggleCond(this)">
        <h3>🦋 Thyroid Disorder</h3>
        <p>A butterfly-shaped gland governing metabolism, energy & hormones. Both hypo & hyperthyroidism are common in women.</p>
        <div class="cond-tags"><span class="tag">Fatigue</span><span class="tag">Weight change</span><span class="tag">Hair loss</span></div>
        <button class="expand-btn">Learn more ↓</button>
        <div class="cond-expand">
          <div class="expand-inner">
            <h4>Symptoms</h4><ul><li>Unexplained weight gain or loss</li><li>Persistent fatigue & brain fog</li><li>Hair thinning, cold sensitivity</li><li>Irregular periods, dry skin</li></ul>
            <h4>Prevention & Management</h4><ul><li>Eat selenium-rich foods (Brazil nuts, eggs)</li><li>Avoid excess soy & cruciferous raw veg</li><li>Regular TSH blood tests</li><li>Manage stress — cortisol affects thyroid</li></ul>
            <h4>When to see a doctor</h4><ul><li>TSH outside 0.4–4.0 mIU/L range</li><li>Neck swelling or difficulty swallowing</li></ul>
          </div>
        </div>
      </div>
      <div class="cond-card" onclick="toggleCond(this)">
        <h3>🌀 PCOS</h3>
        <p>Polycystic Ovary Syndrome — a hormonal imbalance affecting up to 1 in 5 women of reproductive age.</p>
        <div class="cond-tags"><span class="tag">Irregular periods</span><span class="tag">Acne</span><span class="tag">Fertility</span></div>
        <button class="expand-btn">Learn more ↓</button>
        <div class="cond-expand">
          <div class="expand-inner">
            <h4>Symptoms</h4><ul><li>Missed or infrequent periods</li><li>Excess facial/body hair (hirsutism)</li><li>Weight gain, especially around abdomen</li><li>Difficulty conceiving</li></ul>
            <h4>Management</h4><ul><li>Low-GI diet — reduce sugar & refined carbs</li><li>Regular exercise (even 30 min/day)</li><li>Inositol supplement (consult doctor)</li><li>Track your cycle with an app</li></ul>
            <h4>When to see a doctor</h4><ul><li>Missing 3+ periods in a row</li><li>Trying to conceive without success after 12 months</li></ul>
          </div>
        </div>
      </div>
      <div class="cond-card" onclick="toggleCond(this)">
        <h3>🩸 Anemia</h3>
        <p>Iron-deficiency anemia is the most common nutritional deficiency in women, especially during menstruation & pregnancy.</p>
        <div class="cond-tags"><span class="tag">Fatigue</span><span class="tag">Pale skin</span><span class="tag">Breathlessness</span></div>
        <button class="expand-btn">Learn more ↓</button>
        <div class="cond-expand">
          <div class="expand-inner">
            <h4>Symptoms</h4><ul><li>Extreme tiredness & weakness</li><li>Pale lips, skin and inner eyelids</li><li>Shortness of breath on exertion</li><li>Craving ice or unusual substances (pica)</li></ul>
            <h4>Iron-Rich Foods</h4><ul><li>Spinach, methi, beetroot</li><li>Lentils, kidney beans, chickpeas</li><li>Jaggery, ragi, sesame seeds</li><li>Pair with Vitamin C for absorption</li></ul>
            <h4>When to see a doctor</h4><ul><li>Haemoglobin below 12 g/dL</li><li>Fainting spells or severe breathlessness</li></ul>
          </div>
        </div>
      </div>
      <div class="cond-card" onclick="toggleCond(this)">
        <h3>🌸 Menstrual Health</h3>
        <p>Your monthly cycle is a vital sign — understanding it unlocks insights into your overall health.</p>
        <div class="cond-tags"><span class="tag">Cramps</span><span class="tag">Heavy flow</span><span class="tag">PMS</span></div>
        <button class="expand-btn">Learn more ↓</button>
        <div class="cond-expand">
          <div class="expand-inner">
            <h4>Normal vs Concern</h4><ul><li>Normal: 21–35 day cycle, 3–7 days flow</li><li>Concern: Soaking a pad per hour</li><li>Concern: Clots larger than a 50-paise coin</li><li>Concern: Severe cramping missing work/school</li></ul>
            <h4>Natural Relief</h4><ul><li>Heat therapy on lower abdomen</li><li>Ginger tea reduces prostaglandins</li><li>Magnesium-rich foods (dark chocolate, nuts)</li><li>Gentle yoga: child's pose, cat-cow</li></ul>
          </div>
        </div>
      </div>
      <div class="cond-card" onclick="toggleCond(this)">
        <h3>🤰 Pregnancy Care</h3>
        <p>Knowledge is the best prenatal vitamin. Understand what to expect and what to watch for.</p>
        <div class="cond-tags"><span class="tag">Nutrition</span><span class="tag">Antenatal</span><span class="tag">Wellbeing</span></div>
        <button class="expand-btn">Learn more ↓</button>
        <div class="cond-expand">
          <div class="expand-inner">
            <h4>First Trimester Essentials</h4><ul><li>Folic acid 400mcg daily — start before conception</li><li>Avoid alcohol, smoking, raw meat</li><li>First OB visit by week 8</li></ul>
            <h4>Warning Signs — See Doctor Immediately</h4><ul><li>Heavy bleeding at any stage</li><li>Severe abdominal pain</li><li>Decreased fetal movement after 28 weeks</li><li>Blurred vision or sudden swelling</li></ul>
          </div>
        </div>
      </div>
      <div class="cond-card" onclick="toggleCond(this)">
        <h3>🌙 Menopause</h3>
        <p>A natural transition, not a disease. Perimenopause can begin in your early 40s — be prepared, not surprised.</p>
        <div class="cond-tags"><span class="tag">Hot flashes</span><span class="tag">Mood</span><span class="tag">Bone health</span></div>
        <button class="expand-btn">Learn more ↓</button>
        <div class="cond-expand">
          <div class="expand-inner">
            <h4>Symptoms</h4><ul><li>Hot flashes and night sweats</li><li>Vaginal dryness & discomfort</li><li>Mood changes, anxiety, brain fog</li><li>Bone density loss risk</li></ul>
            <h4>Management</h4><ul><li>Weight-bearing exercise protects bones</li><li>Calcium 1200mg + Vitamin D daily</li><li>Discuss HRT with your gynaecologist</li><li>Phytoestrogens: flaxseeds, soy</li></ul>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- MEAL PLAN PANEL -->
  <div id="hp-meal" class="panel">
    <div class="card">
      <div class="card-title" style="font-size:28px;margin-bottom:6px;">🍽️ Personalised Nutrition Plans</div>
      <p style="font-size:14px;color:var(--light);margin-bottom:24px;">Choose your health focus to see a tailored daily meal plan.</p>
      <div class="meal-tabs">
        <div class="meal-tab on" onclick="openMealTab('pcos',this)">🌀 PCOS</div>
        <div class="meal-tab" onclick="openMealTab('thyroid',this)">🦋 Thyroid</div>
        <div class="meal-tab" onclick="openMealTab('anemia',this)">🩸 Anemia</div>
        <div class="meal-tab" onclick="openMealTab('pregnancy',this)">🤰 Pregnancy</div>
        <div class="meal-tab" onclick="openMealTab('general',this)">🌿 General Wellness</div>
      </div>
      <div id="meal-pcos" class="meal-panel on">
        <div class="meal-card"><div class="meal-time">Early Morning</div><span class="meal-icon">🌱</span><div class="meal-name">Soaked Methi Seeds</div><div class="meal-desc">In warm water on empty stomach</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Insulin control</span></div>
        <div class="meal-card"><div class="meal-time">Breakfast</div><span class="meal-icon">🥣</span><div class="meal-name">Oats + Chia + Berries</div><div class="meal-desc">Low GI, high fiber breakfast</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Hormone balance</span></div>
        <div class="meal-card"><div class="meal-time">Mid-Morning</div><span class="meal-icon">🥑</span><div class="meal-name">Avocado on Ragi Cracker</div><div class="meal-desc">Healthy fats for hormones</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Good fats</span></div>
        <div class="meal-card"><div class="meal-time">Lunch</div><span class="meal-icon">🍚</span><div class="meal-name">Brown Rice + Dal + Sabzi</div><div class="meal-desc">Complex carbs, plant protein</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Anti-inflammatory</span></div>
        <div class="meal-card"><div class="meal-time">Evening</div><span class="meal-icon">🌰</span><div class="meal-name">Mixed Nuts + Green Tea</div><div class="meal-desc">Omega-3, antioxidants</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Antioxidant</span></div>
        <div class="meal-card"><div class="meal-time">Dinner</div><span class="meal-icon">🥗</span><div class="meal-name">Quinoa + Grilled Veg</div><div class="meal-desc">Light, complete protein</div><span class="meal-tag" style="background:#ede8f9;color:#7c5cbf;">Complete protein</span></div>
      </div>
      <div id="meal-thyroid" class="meal-panel">
        <div class="meal-card"><div class="meal-time">Early Morning</div><span class="meal-icon">🧄</span><div class="meal-name">Soaked Almonds + Selenium tea</div><div class="meal-desc">Brazil nut or pumpkin seed</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Selenium</span></div>
        <div class="meal-card"><div class="meal-time">Breakfast</div><span class="meal-icon">🥚</span><div class="meal-name">Eggs + Whole Grain Toast</div><div class="meal-desc">Iodine + selenium combo</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Thyroid support</span></div>
        <div class="meal-card"><div class="meal-time">Mid-Morning</div><span class="meal-icon">🍌</span><div class="meal-name">Banana + Coconut Water</div><div class="meal-desc">Potassium & electrolytes</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Electrolytes</span></div>
        <div class="meal-card"><div class="meal-time">Lunch</div><span class="meal-icon">🐟</span><div class="meal-name">Fish/Paneer + Rice + Curry</div><div class="meal-desc">Iodine-rich protein meal</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Iodine</span></div>
        <div class="meal-card"><div class="meal-time">Evening</div><span class="meal-icon">🫚</span><div class="meal-name">Flaxseed Smoothie</div><div class="meal-desc">Omega-3 for inflammation</div><span class="meal-tag" style="background:#ede8f9;color:#7c5cbf;">Anti-inflammatory</span></div>
        <div class="meal-card"><div class="meal-time">Dinner</div><span class="meal-icon">🍲</span><div class="meal-name">Moong Dal Khichdi</div><div class="meal-desc">Easy digestion, zinc-rich</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Zinc</span></div>
      </div>
      <div id="meal-anemia" class="meal-panel">
        <div class="meal-card"><div class="meal-time">Early Morning</div><span class="meal-icon">🫐</span><div class="meal-name">Amla Juice + Jaggery Water</div><div class="meal-desc">Vitamin C boosts iron absorption</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Vit C + Iron</span></div>
        <div class="meal-card"><div class="meal-time">Breakfast</div><span class="meal-icon">🥣</span><div class="meal-name">Ragi Porridge + Dates</div><div class="meal-desc">High iron breakfast</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Iron-rich</span></div>
        <div class="meal-card"><div class="meal-time">Mid-Morning</div><span class="meal-icon">🍊</span><div class="meal-name">Orange / Guava</div><div class="meal-desc">Enhances iron absorption from lunch</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Iron boost</span></div>
        <div class="meal-card"><div class="meal-time">Lunch</div><span class="meal-icon">🥬</span><div class="meal-name">Palak Dal + Beetroot Salad</div><div class="meal-desc">Iron + folate powerhouse</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Folate + Iron</span></div>
        <div class="meal-card"><div class="meal-time">Evening</div><span class="meal-icon">🌰</span><div class="meal-name">Sesame Laddoo + Herbal Tea</div><div class="meal-desc">Sesame seeds are iron-rich</div><span class="meal-tag" style="background:#ede8f9;color:#7c5cbf;">Iron snack</span></div>
        <div class="meal-card"><div class="meal-time">Dinner</div><span class="meal-icon">🫘</span><div class="meal-name">Rajma / Chana + Chapati</div><div class="meal-desc">Plant iron + B12 combination</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Complete meal</span></div>
      </div>
      <div id="meal-pregnancy" class="meal-panel">
        <div class="meal-card"><div class="meal-time">Early Morning</div><span class="meal-icon">🍋</span><div class="meal-name">Ginger Lemon Warm Water</div><div class="meal-desc">Eases morning nausea</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Nausea relief</span></div>
        <div class="meal-card"><div class="meal-time">Breakfast</div><span class="meal-icon">🥛</span><div class="meal-name">Upma + Milk + Folic Acid</div><div class="meal-desc">B vitamins & calcium</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Folic acid</span></div>
        <div class="meal-card"><div class="meal-time">Mid-Morning</div><span class="meal-icon">🍌</span><div class="meal-name">Banana + Coconut Water</div><div class="meal-desc">Potassium, prevents cramps</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Cramp relief</span></div>
        <div class="meal-card"><div class="meal-time">Lunch</div><span class="meal-icon">🍱</span><div class="meal-name">Rice + Dal + Curd + Veg</div><div class="meal-desc">Complete nutrition for mother & baby</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Full nutrition</span></div>
        <div class="meal-card"><div class="meal-time">Evening</div><span class="meal-icon">🥜</span><div class="meal-name">Handful Mixed Nuts</div><div class="meal-desc">DHA for baby brain development</div><span class="meal-tag" style="background:#ede8f9;color:#7c5cbf;">Brain development</span></div>
        <div class="meal-card"><div class="meal-time">Dinner</div><span class="meal-icon">🥣</span><div class="meal-name">Vegetable Khichdi + Ghee</div><div class="meal-desc">Light, calcium & protein</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Calcium + protein</span></div>
      </div>
      <div id="meal-general" class="meal-panel">
        <div class="meal-card"><div class="meal-time">Early Morning</div><span class="meal-icon">🫖</span><div class="meal-name">Warm Water + Honey + Lemon</div><div class="meal-desc">Detox & digestive kick-start</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Detox</span></div>
        <div class="meal-card"><div class="meal-time">Breakfast</div><span class="meal-icon">🥗</span><div class="meal-name">Idli/Dosa + Sambar</div><div class="meal-desc">Fermented = gut-friendly probiotics</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Gut health</span></div>
        <div class="meal-card"><div class="meal-time">Mid-Morning</div><span class="meal-icon">🍎</span><div class="meal-name">Seasonal Fruit</div><div class="meal-desc">Vitamins, fiber & antioxidants</div><span class="meal-tag" style="background:#fff0f4;color:#c85c7a;">Vitamins</span></div>
        <div class="meal-card"><div class="meal-time">Lunch</div><span class="meal-icon">🍚</span><div class="meal-name">Rice + 2 Sabzis + Curd</div><div class="meal-desc">Balanced macro & micronutrients</div><span class="meal-tag" style="background:#ede8f9;color:#7c5cbf;">Balanced</span></div>
        <div class="meal-card"><div class="meal-time">Evening</div><span class="meal-icon">☕</span><div class="meal-name">Masala Chai + Makhana</div><div class="meal-desc">Calcium-rich, light snack</div><span class="meal-tag" style="background:#e3f2eb;color:#3a8c65;">Calcium</span></div>
        <div class="meal-card"><div class="meal-time">Dinner</div><span class="meal-icon">🥘</span><div class="meal-name">Soup + Chapati + Dal</div><div class="meal-desc">Light, easy digestion for sleep</div><span class="meal-tag" style="background:#fdf3dc;color:#a07c20;">Sleep-friendly</span></div>
      </div>
    </div>
  </div>

  <!-- CHECKLIST PANEL -->
  <div id="hp-checklist" class="panel">
    <div class="checklist-wrap">
      <div class="cl-header">
        <h3>✔️ Daily Wellness Checklist</h3>
        <span class="cl-score" id="clScore">0 / 10 done</span>
      </div>
      <div id="clList">
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">💧 Drank at least 8 glasses of water</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">🥗 Ate iron-rich foods today (spinach, lentils, jaggery)</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">🚶 30+ minutes of walking, yoga, or exercise</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">😴 Got 7–8 hours of quality sleep last night</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">💊 Took my supplements or prescribed medication</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">🧘 5 minutes of deep breathing or meditation</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">📅 Logged my period / health symptoms in my app</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">🌿 Included seasonal fruits or vegetables in a meal</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">📵 Had at least 30 min of screen-free time</span></div>
        <div class="cl-item" onclick="toggleCl(this)"><div class="cl-box"></div><span class="cl-txt">💌 Did something kind for myself today</span></div>
      </div>
      <div class="cl-progress"><div class="cl-pfill" id="clFill"></div></div>
      <p style="font-size:13px;color:var(--light);margin-top:12px;" id="clMsg">Start checking off your day! 🌸</p>
    </div>
  </div>
</div>

<script>
// ══════════════════════════
// PAGE ROUTING
// ══════════════════════════
function goPage(p){
  document.querySelectorAll('.page').forEach(x=>x.classList.remove('active'));
  document.getElementById('page-'+p).classList.add('active');
  document.querySelectorAll('.nav-menu a').forEach(x=>x.classList.remove('active'));
  const navEl = document.getElementById('nav-'+p);
  if(navEl) navEl.classList.add('active');
  window.scrollTo({top:0,behavior:'smooth'});
}

// Sub-tabs
function openSubTab(page,tab,el){
  const prefix = page==='health'?'hp':page==='safety'?'sp':page==='mental'?'mp':page==='career'?'cp':'lp';
  document.querySelectorAll(`#page-${page} .panel`).forEach(p=>p.classList.remove('on'));
  document.getElementById(`${prefix}-${tab}`).classList.add('on');
  const tabsEl = document.querySelectorAll(`#page-${page} .sub-tab`);
  tabsEl.forEach(t=>{t.classList.remove('on');t.style.background='white';t.style.color='';});
  el.classList.add('on');
  const colors={health:'#c85c7a',safety:'#c0633a',mental:'#7c5cbf',career:'#3a8c65',legal:'#a07c20'};
  el.style.background=colors[page]||'#c85c7a';el.style.color='white';
  if(page==='safety'&&tab==='map'&&typeof ensureMaguvaSafetyMap==='function'){
    setTimeout(function(){ensureMaguvaSafetyMap();},80);
  }
}

// ══════════════════════════
// HERO COUNTER
// ══════════════════════════
function animCount(el,target){
  let cur=0;const step=Math.ceil(target/30);
  const t=setInterval(()=>{cur=Math.min(cur+step,target);el.textContent=cur;if(cur>=target)clearInterval(t);},50);
}
window.addEventListener('load',()=>{
  animCount(document.getElementById('countTopics'),30);
  animCount(document.getElementById('countFeatures'),20);
  upRisk();
});

// ══════════════════════════
// HEALTH SURVEY
// ══════════════════════════
const surveyData={};
let currentStep=1;
const totalSteps=5;

function pickSurvey(el,key,val){
  el.closest('.survey-options').querySelectorAll('.s-opt').forEach(o=>o.classList.remove('picked'));
  el.classList.add('picked');
  surveyData[key]=[val];
}
function toggleSurvey(el,key,val){
  if(!surveyData[key])surveyData[key]=[];
  if(el.classList.contains('picked')){el.classList.remove('picked');surveyData[key]=surveyData[key].filter(v=>v!==val);}
  else{el.classList.add('picked');surveyData[key].push(val);}
}
function surveyNext(step){
  currentStep=step+1;
  document.querySelectorAll('.survey-step').forEach(s=>s.classList.remove('on'));
  if(step>=totalSteps){showSurveyResult();document.getElementById('ss6').classList.add('on');}
  else{document.getElementById('ss'+(step+1)).classList.add('on');}
  document.getElementById('surveyProgress').style.width=((currentStep/(totalSteps+1))*100)+'%';
}
function surveyBack(step){
  currentStep=step-1;
  document.querySelectorAll('.survey-step').forEach(s=>s.classList.remove('on'));
  document.getElementById('ss'+(step-1)).classList.add('on');
  document.getElementById('surveyProgress').style.width=((currentStep/(totalSteps+1))*100)+'%';
}
function surveyReset(){
  currentStep=1;
  Object.keys(surveyData).forEach(k=>delete surveyData[k]);
  document.querySelectorAll('.s-opt').forEach(o=>o.classList.remove('picked'));
  document.querySelectorAll('.survey-step').forEach(s=>s.classList.remove('on'));
  document.getElementById('ss1').classList.add('on');
  document.getElementById('surveyProgress').style.width='14%';
}
function showSurveyResult(){
  const syms=surveyData.sym||[];
  let conditions=[];let tips=[];
  if(syms.includes('Irregular periods')||syms.includes('Acne')||syms.includes('Weight gain'))conditions.push('PCOS');
  if(syms.includes('Fatigue')||syms.includes('Hair loss')||syms.includes('Cold sensitivity'))conditions.push('Thyroid Disorder');
  if(syms.includes('Fatigue')&&!conditions.includes('Thyroid'))conditions.push('Possible Anemia');
  if(syms.includes('Pelvic pain'))conditions.push('Menstrual Health concern');
  if(conditions.length===0)conditions.push('General Wellness — you seem to be doing well!');

  const stress=surveyData.stress||[];
  if(stress.includes('Poor sleep, high stress')||stress.includes('Insomnia / burnout'))tips.push('🧠 Prioritize sleep hygiene — your hormones depend on it');
  const diet=surveyData.diet||[];
  if(diet.includes('Skip meals often'))tips.push('🍽️ Never skip meals — blood sugar dips worsen PCOS & thyroid');
  if(diet.includes('Lots of processed food'))tips.push('🥗 Reduce ultra-processed food — it drives inflammation');
  const doc=surveyData.doctor||[];
  if(doc.includes('Over a year ago')||doc.includes('Never/rarely'))tips.push('🩺 Book a preventive health check-up — TSH, Hemoglobin & Vitamin D tests');
  if(tips.length===0)tips.push('✨ Keep maintaining your healthy habits!');

  document.getElementById('surveyResultBox').innerHTML=`
    <div class="res-title">🌸 Your Health Snapshot</div>
    <p style="font-size:14px;color:var(--mid);margin-bottom:16px;">Based on your responses, here are potential areas to focus on:</p>
    <h4 style="font-size:12px;text-transform:uppercase;letter-spacing:1.5px;color:var(--light);margin-bottom:8px;">Possible Concerns</h4>
    <ul class="res-list">${conditions.map(c=>`<li>${c}</li>`).join('')}</ul>
    <h4 style="font-size:12px;text-transform:uppercase;letter-spacing:1.5px;color:var(--light);margin:16px 0 8px;">Personalised Tips</h4>
    <ul class="res-list">${tips.map(t=>`<li>${t}</li>`).join('')}</ul>
    <p style="font-size:12px;color:var(--light);margin-top:16px;">⚠️ This is not a medical diagnosis. Please consult a healthcare professional for proper evaluation.</p>
  `;
}

// ══════════════════════════
// RISK METER
// ══════════════════════════
function upRisk(){
  let total=0;
  [1,2,3,4,5,6].forEach(i=>{
    const v=parseInt(document.getElementById('rs'+i).value);
    document.getElementById('rv'+i).textContent=v;
    // inverse for sleep/nutrition/exercise (higher = better)
    total+=(i>=4)?v:(11-v);
  });
  const avg=total/6;
  const ring=document.getElementById('gaugeRing');
  const score=document.getElementById('gaugeScore');
  const label=document.getElementById('gaugeLabel');
  const msg=document.getElementById('gaugeMsg');
  if(avg<=4){ring.style.background='conic-gradient(#3a8c65 0%,#e5f5ec 0%)';ring.style.background='radial-gradient(circle,#efffed,white)';ring.style.boxShadow='0 0 0 6px #d0f0d8';score.textContent='✦';score.style.fontSize='36px';label.textContent='LOW RISK';label.style.color='#3a8c65';msg.innerHTML='<strong style="color:#3a8c65">You\'re doing wonderfully!</strong> Keep up your healthy habits and visit your doctor for regular preventive check-ups.';}
  else if(avg<=6.5){ring.style.boxShadow='0 0 0 6px #fff0cc';score.textContent='◈';score.style.fontSize='36px';label.textContent='MODERATE';label.style.color='#b07a20';msg.innerHTML='<strong style="color:#b07a20">Some areas need attention.</strong> Consider adjusting your diet, sleep, or stress management. A doctor\'s check-up would be beneficial.';}
  else{ring.style.boxShadow='0 0 0 6px #ffd6e4';score.textContent='⚠';score.style.fontSize='32px';label.textContent='HIGH RISK';label.style.color='#c85c7a';msg.innerHTML='<strong style="color:#c85c7a">Please prioritise your health.</strong> These indicators suggest you should consult a doctor soon. Your wellbeing is the priority.';}
}

// ══════════════════════════
// CONDITIONS EXPAND
// ══════════════════════════
function toggleCond(card){
  const exp=card.querySelector('.cond-expand');
  const btn=card.querySelector('.expand-btn');
  if(exp.classList.contains('open')){exp.classList.remove('open');btn.textContent='Learn more ↓';}
  else{exp.classList.add('open');btn.textContent='Show less ↑';}
}

// ══════════════════════════
// MEAL TABS
// ══════════════════════════
function openMealTab(tab,el){
  document.querySelectorAll('.meal-panel').forEach(p=>p.classList.remove('on'));
  document.querySelectorAll('.meal-tab').forEach(t=>{t.classList.remove('on');});
  document.getElementById('meal-'+tab).classList.add('on');
  el.classList.add('on');
}

// ══════════════════════════
// CHECKLIST
// ══════════════════════════
function toggleCl(el){
  el.classList.toggle('done');
  const total=document.querySelectorAll('.cl-item').length;
  const done=document.querySelectorAll('.cl-item.done').length;
  document.getElementById('clScore').textContent=done+' / '+total+' done';
  document.getElementById('clFill').style.width=(done/total*100)+'%';
  const msgs=['Start checking off your day! 🌸','Good start! Keep going 💪','You\'re halfway there! ✨','Almost done, amazing! 🌺','You crushed today\'s wellness goals! 🎉'];
  const idx=Math.floor(done/total*4);
  document.getElementById('clMsg').textContent=msgs[idx]||msgs[4];
}
</script>


<!-- SAFETY PAGE -->
<div id="page-safety" class="page">
  <div class="page-hero" style="background:linear-gradient(135deg,#fff6f0 0%,#ffe8dc 60%);">
    <div class="page-hero-inner">
      <div class="page-hero-text">
        <button class="back-btn" onclick="goPage('home')">← Back to Home</button>
        <h1 class="page-title" style="color:#c0633a;">Stay Safe,<br><em style="color:#e07030;">Stay Strong</em></h1>
        <p class="page-desc">Quick tips, emergency contacts, live safety map, and step-by-step action guides.</p>
      </div>
      <div style="font-size:80px;opacity:0.3;">🚨</div>
    </div>
  </div>

  <div class="sub-tabs">
    <div class="sub-tab on" style="background:#c0633a;color:white;" onclick="openSubTab('safety','sos',this)">🆘 SOS & Helplines</div>
    <div class="sub-tab" onclick="openSubTab('safety','map',this)">🗺️ Safety Map</div>
    <div class="sub-tab" onclick="openSubTab('safety','tips',this)">⚡ Safety Tips</div>
    <div class="sub-tab" onclick="openSubTab('safety','guide',this)">📋 Action Guides</div>
    <div class="sub-tab" onclick="openSubTab('safety','checklist2',this)">✔️ Safety Checklist</div>
  </div>

  <!-- SOS PANEL -->
  <div id="sp-sos" class="panel on">
    <div style="background:linear-gradient(135deg,#fff6f0,#ffe8dc);border-radius:var(--r);padding:40px;box-shadow:var(--shadow-lg);margin-bottom:24px;">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:32px;margin-bottom:8px;">🆘 Emergency SOS Panel</h3>
      <p style="font-size:14px;color:var(--light);margin-bottom:28px;">Tap any number to call immediately. Your safety comes first.</p>

      <!-- Fake SOS Button -->
      <div style="text-align:center;margin-bottom:36px;">
        <button id="sosMainBtn" onclick="triggerSOS()" style="width:140px;height:140px;border-radius:50%;background:linear-gradient(135deg,#e04040,#c01010);color:white;border:none;font-size:16px;font-weight:800;cursor:pointer;box-shadow:0 0 0 0 rgba(224,64,64,0.5);animation:pulse-sos 1.5s infinite;letter-spacing:1px;transition:transform 0.1s;">
          🆘<br>SOS<br><span style="font-size:11px;font-weight:400;">TAP TO ALERT</span>
        </button>
        <div id="sosStatus" style="margin-top:14px;font-size:13px;color:var(--mid);display:none;"></div>
      </div>

      <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:14px;">
        <div class="helpline-tile" onclick="callNumber('1091')">
          <span style="font-size:28px;">🆘</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Women Helpline</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">1091</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('100')">
          <span style="font-size:28px;">🚔</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Police</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">100</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('108')">
          <span style="font-size:28px;">🚑</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Ambulance</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">108</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('181')">
          <span style="font-size:28px;">🏠</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">DV Helpline</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">181</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('1930')">
          <span style="font-size:28px;">💻</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Cyber Crime</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">1930</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('112')">
          <span style="font-size:28px;">🚨</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Emergency</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">112</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('1096')">
          <span style="font-size:28px;">🧠</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Mental Health</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">1096</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
        <div class="helpline-tile" onclick="callNumber('15100')">
          <span style="font-size:28px;">⚖️</span>
          <div><div style="font-size:12px;font-weight:700;color:var(--mid);">Legal Aid</div><div style="font-family:'Cormorant Garamond',serif;font-size:28px;font-weight:700;color:#c0633a;">15100</div></div>
          <div class="call-badge">📞 Call</div>
        </div>
      </div>
    </div>

    <!-- Trusted Contacts -->
    <div style="background:white;border-radius:var(--r);padding:32px;box-shadow:var(--shadow);">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:26px;margin-bottom:6px;">👥 My Trusted Contacts</h3>
      <p style="font-size:13px;color:var(--light);margin-bottom:20px;">Add contacts you'd want alerted in an emergency.</p>
      <div id="contactsList" style="margin-bottom:16px;"></div>
      <div style="display:flex;gap:10px;flex-wrap:wrap;">
        <input id="cName" placeholder="Name" style="flex:1;min-width:120px;padding:10px 16px;border-radius:50px;border:1.5px solid rgba(180,120,140,0.2);font-family:'Nunito',sans-serif;font-size:13px;outline:none;">
        <input id="cPhone" placeholder="Phone Number" style="flex:1;min-width:140px;padding:10px 16px;border-radius:50px;border:1.5px solid rgba(180,120,140,0.2);font-family:'Nunito',sans-serif;font-size:13px;outline:none;">
        <button onclick="addContact()" style="background:linear-gradient(135deg,#c0633a,#e07030);color:white;border:none;padding:10px 22px;border-radius:50px;font-size:13px;font-weight:700;cursor:pointer;">Add +</button>
      </div>
    </div>
  </div>

  <!-- MAP PANEL (Leaflet + Overpass — same feature set as index2) -->
  <div id="sp-map" class="panel">
    <div style="background:white;border-radius:var(--r);padding:32px;box-shadow:var(--shadow);margin-bottom:20px;">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:28px;margin-bottom:8px;">🗺️ Live Safety Map</h3>
      <p style="font-size:14px;color:var(--light);margin-bottom:20px;">Search places, tap the map for details, or use live tracking. Police and hospitals load from OpenStreetMap as you explore.</p>
      <div class="sm-map-shell">
        <aside class="sm-sidebar">
          <div class="sm-sidebar-head">
            <h4 class="sm-sidebar-title"><i class="fa-solid fa-map-location-dot sm-title-ico"></i> Maguva Maps</h4>
            <p class="sm-sidebar-sub">Safety Atlas • Live data</p>
          </div>
          <div class="sm-sidebar-controls">
            <form id="sm-search-form" class="sm-search-form">
              <input type="text" id="sm-search-input" placeholder="Search city or place..." autocomplete="off">
              <span class="sm-search-ico" aria-hidden="true"><i class="fa-solid fa-magnifying-glass"></i></span>
              <button type="submit" class="sm-search-submit">Search</button>
            </form>
            <button type="button" id="sm-btn-sidebar-location" class="sm-loc-btn">
              <i class="fa-solid fa-location-arrow sm-loc-ico"></i>
              <span>Find My Location</span>
            </button>
            <p class="sm-zoom-hint"><i class="fa-solid fa-circle-info"></i> <span class="sm-zoom-hint-strong">Live infrastructure</span> appears when zoomed in.</p>
            <div id="sm-error-message" class="sm-error sm-hidden"></div>
          </div>
          <div id="sm-info-panel" class="sm-info-panel sm-hidden">
            <div class="sm-info-head">
              <h2 id="sm-location-title" class="sm-loc-title">Location</h2>
              <div id="sm-live-badge" class="sm-live-badge sm-hidden"><span class="sm-live-dot"></span> Live Tracking</div>
              <p id="sm-location-subtitle" class="sm-loc-sub">Target Point</p>
            </div>
            <div class="sm-nearest-block">
              <h3 class="sm-nearest-label"><i class="fa-solid fa-tower-broadcast"></i> Closest Safety Units</h3>
              <div id="sm-nearest-hospital" class="sm-card sm-card-hosp">
                <div class="sm-card-inner">
                  <div class="sm-card-icon sm-icon-hosp"><i class="fa-solid fa-hospital"></i></div>
                  <div class="sm-card-text">
                    <div class="sm-card-tag sm-tag-hosp">Emergency Care</div>
                    <div id="sm-hosp-name" class="sm-card-name">Scanning...</div>
                    <div id="sm-hosp-dist" class="sm-card-dist">-- km away</div>
                  </div>
                  <button type="button" id="sm-goto-hosp" class="sm-goto sm-hidden" title="Center on map"><i class="fa-solid fa-location-dot"></i></button>
                </div>
              </div>
              <div id="sm-nearest-police" class="sm-card sm-card-pol">
                <div class="sm-card-inner">
                  <div class="sm-card-icon sm-icon-pol"><i class="fa-solid fa-shield-halved"></i></div>
                  <div class="sm-card-text">
                    <div class="sm-card-tag sm-tag-pol">Public Safety</div>
                    <div id="sm-police-name" class="sm-card-name">Scanning...</div>
                    <div id="sm-police-dist" class="sm-card-dist">-- km away</div>
                  </div>
                  <button type="button" id="sm-goto-police" class="sm-goto sm-hidden" title="Center on map"><i class="fa-solid fa-location-dot"></i></button>
                </div>
              </div>
            </div>
            <div class="sm-ai-box">
              <h3 class="sm-ai-title"><i class="fa-solid fa-circle-info"></i> Intelligent Insights</h3>
              <div id="sm-ai-loading" class="sm-ai-loading">
                <div class="sm-typing"><span></span><span></span><span></span></div>
              </div>
              <div id="sm-ai-content" class="sm-ai-content sm-hidden"></div>
            </div>
          </div>
          <div id="sm-placeholder-panel" class="sm-placeholder">
            <i class="fa-solid fa-satellite-dish sm-ph-icon"></i>
            <h3 class="sm-ph-title">GPS Ready</h3>
            <p class="sm-ph-text">Use <strong>Find My Location</strong> or the crosshairs on the map to start. Zoom in to load nearby police and hospitals.</p>
          </div>
          <div class="sm-sidebar-foot">OpenStreetMap contributors • CARTO / Esri imagery</div>
        </aside>
        <div class="sm-map-wrap">
          <div id="sm-poi-status" class="sm-poi-status sm-hidden" aria-live="polite">
            <div class="sm-poi-status-inner"><i class="fa-solid fa-spinner fa-spin sm-spin"></i><span>Analyzing infrastructure...</span></div>
          </div>
          <div id="sm-map"></div>
          <button type="button" id="sm-btn-my-location" class="sm-crosshair-btn" title="Toggle live tracking"><i class="fa-solid fa-location-crosshairs"></i></button>
        </div>
      </div>
      <div style="display:flex;gap:20px;margin-top:16px;flex-wrap:wrap;">
        <button onclick="locateMe()" style="background:linear-gradient(135deg,#4a90d9,#2a70b9);color:white;border:none;padding:12px 24px;border-radius:50px;font-size:13px;font-weight:700;cursor:pointer;">📍 Locate Me</button>
        <button onclick="shareLocation()" style="background:linear-gradient(135deg,var(--peach-deep),#e07030);color:white;border:none;padding:12px 24px;border-radius:50px;font-size:13px;font-weight:700;cursor:pointer;">📤 Share My Location</button>
      </div>
      <div id="mapStatus" style="margin-top:12px;font-size:13px;color:var(--sage-deep);font-weight:600;"></div>
    </div>
  </div>

  <!-- TIPS PANEL -->
  <div id="sp-tips" class="panel">
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:20px;">
      <div class="safety-topic-card" style="--accent:#c0633a;--bg:#fff6f0;">
        <div class="stc-icon">🚌</div>
        <h3>Travel Safety</h3>
        <ul class="stc-list">
          <li>Share live location before boarding</li>
          <li>Note vehicle number & driver details</li>
          <li>Sit near women or well-lit areas</li>
          <li>Keep emergency numbers on speed dial</li>
          <li>Avoid isolated late-night routes</li>
          <li>Trust your gut — leave if uncomfortable</li>
        </ul>
      </div>
      <div class="safety-topic-card" style="--accent:#c0633a;--bg:#fff6f0;">
        <div class="stc-icon">🌙</div>
        <h3>Night Safety</h3>
        <ul class="stc-list">
          <li>Inform someone of your return time</li>
          <li>Stay on well-lit, populated streets</li>
          <li>Avoid headphones — stay aware</li>
          <li>Keep phone charged & accessible</li>
          <li>Know nearby safe havens (shops, petrol pumps)</li>
          <li>Walk confidently, away from walls</li>
        </ul>
      </div>
      <div class="safety-topic-card" style="--accent:#c0633a;--bg:#fff6f0;">
        <div class="stc-icon">🏢</div>
        <h3>Workplace Safety</h3>
        <ul class="stc-list">
          <li>Document incidents with dates & details</li>
          <li>Know your ICC (Internal Complaints Committee)</li>
          <li>POSH Act 2013 protects you — know it</li>
          <li>Don't be silenced — speak to HR</li>
          <li>Build a support network at work</li>
          <li>Screenshot any harassing messages</li>
        </ul>
      </div>
      <div class="safety-topic-card" style="--accent:#c0633a;--bg:#fff6f0;">
        <div class="stc-icon">🌐</div>
        <h3>Online Safety</h3>
        <ul class="stc-list">
          <li>Use strong passwords + 2FA everywhere</li>
          <li>Never share personal details publicly</li>
          <li>Block & report harassment immediately</li>
          <li>Screenshot evidence before deleting</li>
          <li>Check app privacy permissions regularly</li>
          <li>Report: cybercrime.gov.in or call 1930</li>
        </ul>
      </div>
    </div>
  </div>

  <!-- ACTION GUIDES PANEL -->
  <div id="sp-guide" class="panel">
    <div style="margin-bottom:20px;">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:28px;margin-bottom:8px;">📋 Step-by-Step Action Guides</h3>
      <p style="font-size:14px;color:var(--light);">Tap a situation to see exactly what to do, step by step.</p>
    </div>
    <div style="display:flex;flex-direction:column;gap:14px;">
      <div class="guide-accordion">
        <div class="guide-header" onclick="toggleGuide(this)"><span>🚨 I feel unsafe right now</span><span class="gacc-arrow">▼</span></div>
        <div class="guide-body">
          <div class="guide-step"><span class="gs-num">1</span><div><strong>Move to a safe, public place immediately</strong> — a shop, a petrol pump, any crowd.</div></div>
          <div class="guide-step"><span class="gs-num">2</span><div><strong>Call 112</strong> (emergency) or <strong>100</strong> (police). Keep the line open.</div></div>
          <div class="guide-step"><span class="gs-num">3</span><div><strong>Text your location</strong> to a trusted contact. Share Google Maps live location.</div></div>
          <div class="guide-step"><span class="gs-num">4</span><div><strong>Make noise</strong> if threatened — shout, sound your phone alarm, attract attention.</div></div>
          <div class="guide-step"><span class="gs-num">5</span><div><strong>Stay on the phone</strong> with emergency services until help arrives.</div></div>
        </div>
      </div>
      <div class="guide-accordion">
        <div class="guide-header" onclick="toggleGuide(this)"><span>😰 I was harassed at work</span><span class="gacc-arrow">▼</span></div>
        <div class="guide-body">
          <div class="guide-step"><span class="gs-num">1</span><div><strong>Write down everything</strong> — date, time, location, what was said/done, witnesses.</div></div>
          <div class="guide-step"><span class="gs-num">2</span><div><strong>Screenshot or save</strong> any messages, emails, or digital evidence.</div></div>
          <div class="guide-step"><span class="gs-num">3</span><div><strong>File a written complaint</strong> with your company's Internal Complaints Committee (ICC).</div></div>
          <div class="guide-step"><span class="gs-num">4</span><div><strong>If no ICC exists</strong>, your employer has violated the POSH Act. Report to the District Officer.</div></div>
          <div class="guide-step"><span class="gs-num">5</span><div><strong>Seek legal advice</strong> — call 15100 (Legal Aid) for free guidance.</div></div>
        </div>
      </div>
      <div class="guide-accordion">
        <div class="guide-header" onclick="toggleGuide(this)"><span>💻 I'm being harassed online</span><span class="gacc-arrow">▼</span></div>
        <div class="guide-body">
          <div class="guide-step"><span class="gs-num">1</span><div><strong>Screenshot all evidence</strong> — posts, messages, profile. Don't delete yet.</div></div>
          <div class="guide-step"><span class="gs-num">2</span><div><strong>Block the person</strong> on all platforms and enable strict privacy settings.</div></div>
          <div class="guide-step"><span class="gs-num">3</span><div><strong>Report to the platform</strong> — all platforms have abuse reporting tools.</div></div>
          <div class="guide-step"><span class="gs-num">4</span><div><strong>File a complaint</strong> at cybercrime.gov.in or call 1930 (Cyber Crime Helpline).</div></div>
          <div class="guide-step"><span class="gs-num">5</span><div><strong>Inform someone you trust</strong> — don't face this alone.</div></div>
        </div>
      </div>
      <div class="guide-accordion">
        <div class="guide-header" onclick="toggleGuide(this)"><span>🏠 I'm experiencing domestic violence</span><span class="gacc-arrow">▼</span></div>
        <div class="guide-body">
          <div class="guide-step"><span class="gs-num">1</span><div><strong>Your safety is first.</strong> If in immediate danger, call 112 or go to the nearest police station.</div></div>
          <div class="guide-step"><span class="gs-num">2</span><div><strong>Call 181</strong> (Women's Helpline) — they can connect you to a shelter and legal support.</div></div>
          <div class="guide-step"><span class="gs-num">3</span><div><strong>Seek medical attention</strong> for any injuries. Ask for a medical report — it's legal evidence.</div></div>
          <div class="guide-step"><span class="gs-num">4</span><div><strong>Under DV Act 2005</strong> you can get a Protection Order, Residence Order, and monetary relief.</div></div>
          <div class="guide-step"><span class="gs-num">5</span><div><strong>Contact a lawyer or legal aid</strong> — 15100 or the nearest women's commission.</div></div>
        </div>
      </div>
    </div>
  </div>

  <!-- SAFETY CHECKLIST -->
  <div id="sp-checklist2" class="panel">
    <div class="checklist-wrap">
      <div class="cl-header">
        <h3>✔️ Daily Safety Checklist</h3>
        <span class="cl-score" id="clScore2">0 / 8 done</span>
      </div>
      <div id="clList2">
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">📱 Phone is fully charged before leaving home</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">📍 Shared live location with trusted contact</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">🔢 Emergency numbers saved on speed dial (1091, 100, 112)</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">🔒 Social media privacy settings reviewed this week</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">👀 Aware of exits & safe spots in the places I visit</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">🛡️ Informed someone of my plans for today</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">🔐 Passwords strong & 2FA enabled on key apps</span></div>
        <div class="cl-item" onclick="toggleCl2(this)"><div class="cl-box" style="border-color:var(--peach-deep)"></div><span class="cl-txt">📋 Know my rights (POSH, DV Act) in case I need them</span></div>
      </div>
      <div class="cl-progress"><div class="cl-pfill" id="clFill2" style="background:linear-gradient(90deg,var(--peach),var(--peach-deep));"></div></div>
    </div>
  </div>
</div>

<!-- MENTAL HEALTH PAGE -->
<div id="page-mental" class="page">
  <div class="page-hero" style="background:linear-gradient(135deg,#f5f0ff 0%,#e8dcfc 60%);">
    <div class="page-hero-inner">
      <div class="page-hero-text">
        <button class="back-btn" onclick="goPage('home')">← Back to Home</button>
        <h1 class="page-title" style="color:#7c5cbf;">You Are<br><em style="color:#c85c7a;">Not Alone</em></h1>
        <p class="page-desc">Track your mood, talk to a supportive AI companion, explore calming techniques, and build a self-care toolkit.</p>
      </div>
      <div style="font-size:80px;opacity:0.3;">🧠</div>
    </div>
  </div>

  <div class="sub-tabs">
    <div class="sub-tab on" style="background:#7c5cbf;color:white;" onclick="openSubTab('mental','chat',this)">💬 Support Chat</div>
    <div class="sub-tab" onclick="openSubTab('mental','mood',this)">😊 Mood Tracker</div>
    <div class="sub-tab" onclick="openSubTab('mental','calm',this)">🌿 Calm Toolkit</div>
    <div class="sub-tab" onclick="openSubTab('mental','selfcare',this)">💕 Self-Care</div>
  </div>

  <!-- AI SUPPORT CHAT -->
  <div id="mp-chat" class="panel on">
    <div style="background:linear-gradient(135deg,#f5f0ff,#fdf0ff);border-radius:var(--r);padding:0;box-shadow:var(--shadow-lg);overflow:hidden;max-width:740px;margin:0 auto;">
      <div style="background:linear-gradient(135deg,#7c5cbf,#9070cc);padding:24px 28px;display:flex;align-items:center;gap:14px;">
        <div style="width:48px;height:48px;background:rgba(255,255,255,0.2);border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:24px;">🌸</div>
        <div>
          <div style="font-size:16px;font-weight:700;color:white;">Pooja — Your Support Companion</div>
          <div style="font-size:12px;color:rgba(255,255,255,0.7);display:flex;align-items:center;gap:6px;margin-top:2px;"><span style="width:8px;height:8px;background:#7dff96;border-radius:50%;display:inline-block;"></span>Always here for you</div>
        </div>
      </div>
      <div id="chatMessages" style="height:420px;overflow-y:auto;padding:24px;display:flex;flex-direction:column;gap:14px;background:linear-gradient(135deg,#faf8ff,#fff8fd);">
        <div class="chat-msg bot">
          <div class="chat-bubble bot">Hi, I'm Pooja 🌸 I'm here to listen and support you — without judgment. You can share how you're feeling, ask about coping strategies, or just talk. How are you doing today?</div>
        </div>
        <div style="display:flex;gap:8px;flex-wrap:wrap;margin-left:44px;">
          <button class="quick-reply" onclick="sendQuickReply(this)">I'm feeling anxious 😰</button>
          <button class="quick-reply" onclick="sendQuickReply(this)">I'm really stressed 😓</button>
          <button class="quick-reply" onclick="sendQuickReply(this)">I feel lonely 💔</button>
          <button class="quick-reply" onclick="sendQuickReply(this)">I need some motivation ✨</button>
        </div>
      </div>
      <div style="padding:16px 20px;background:white;border-top:1px solid rgba(201,184,232,0.2);display:flex;gap:10px;align-items:center;">
        <input id="chatInput" placeholder="Share how you're feeling..." 
          style="flex:1;padding:12px 18px;border-radius:50px;border:1.5px solid rgba(201,184,232,0.4);font-family:'Nunito',sans-serif;font-size:14px;outline:none;background:#faf8ff;"
          onkeypress="if(event.key==='Enter')sendChat()">
        <button onclick="sendChat()" style="background:linear-gradient(135deg,#7c5cbf,#9070cc);color:white;border:none;width:44px;height:44px;border-radius:50%;font-size:18px;cursor:pointer;flex-shrink:0;transition:transform 0.2s;" onmouseover="this.style.transform='scale(1.1)'" onmouseout="this.style.transform='scale(1)'">➤</button>
      </div>
    </div>
  </div>

  <!-- MOOD TRACKER -->
  <div id="mp-mood" class="panel">
    <div style="background:linear-gradient(135deg,#f5f0ff,#ede4fc);border-radius:var(--r);padding:40px;box-shadow:var(--shadow);">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:30px;margin-bottom:8px;">😊 How are you feeling today?</h3>
      <p style="font-size:14px;color:var(--light);margin-bottom:30px;">Tap your mood. Your pattern over time reveals a lot.</p>
      <div style="display:flex;gap:14px;justify-content:center;flex-wrap:wrap;margin-bottom:32px;">
        <button class="mood-big-btn" onclick="logMood(this,'😄','Great','You\'re glowing today! Ride this energy — connect with someone you love, do something creative, or simply bask in the joy. 🌟')">😄<br><span>Great</span></button>
        <button class="mood-big-btn" onclick="logMood(this,'😊','Good','Feeling good is worth celebrating! Take a walk, enjoy a meal, and stay present in these small joys. 🌸')">😊<br><span>Good</span></button>
        <button class="mood-big-btn" onclick="logMood(this,'😐','Okay','Neutral days are perfectly human. Rest, drink water, and be gentle with yourself. 🫖')">😐<br><span>Okay</span></button>
        <button class="mood-big-btn" onclick="logMood(this,'😔','Low','It\'s okay not to be okay. Rest is valid. Reaching out is brave. You don\'t have to carry this alone. 💜')">😔<br><span>Low</span></button>
        <button class="mood-big-btn" onclick="logMood(this,'😰','Anxious','Take a slow breath. You are safe, right here, right now. Let\'s try a grounding exercise together. 🤍')">😰<br><span>Anxious</span></button>
        <button class="mood-big-btn" onclick="logMood(this,'😡','Frustrated','Your feelings are valid. It\'s okay to be angry. Let\'s find a healthy way to release this energy. 🔥')">😡<br><span>Frustrated</span></button>
      </div>
      <div id="moodResponse" style="background:white;border-radius:var(--rs);padding:24px;text-align:center;font-size:15px;color:var(--mid);line-height:1.7;display:none;border:1.5px solid rgba(201,184,232,0.3);animation:fadeUp 0.4s ease;"></div>
    </div>
    <!-- Mood History -->
    <div style="background:white;border-radius:var(--r);padding:32px;box-shadow:var(--shadow);margin-top:20px;">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:20px;">📅 This Week's Mood Log</h3>
      <div id="moodHistory" style="display:flex;gap:8px;flex-wrap:wrap;"></div>
      <p style="font-size:12px;color:var(--light);margin-top:14px;">Your mood log is private and stays on your device.</p>
    </div>
  </div>

  <!-- CALM TOOLKIT -->
  <div id="mp-calm" class="panel">
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:20px;">
      <div class="calm-card-big" onclick="expandCalm(this)">
        <div class="cc-icon">🌬️</div>
        <h4>Box Breathing</h4>
        <p>Activates your parasympathetic nervous system. Try it for 2 minutes.</p>
        <div class="cc-expand">
          <div id="breathAnim" style="width:100px;height:100px;border-radius:50%;background:linear-gradient(135deg,var(--lav-light),var(--lav));margin:20px auto;display:flex;align-items:center;justify-content:center;font-size:13px;font-weight:700;color:var(--lav-deep);transition:all 4s ease;">Inhale</div>
          <button onclick="startBreathing(event)" style="background:var(--lav-deep);color:white;border:none;padding:10px 24px;border-radius:50px;font-size:13px;font-weight:700;cursor:pointer;margin-top:10px;">▶ Start</button>
        </div>
      </div>
      <div class="calm-card-big" onclick="expandCalm(this)">
        <div class="cc-icon">🖐️</div>
        <h4>5-4-3-2-1 Grounding</h4>
        <p>Pulls you out of anxiety and back into the present moment.</p>
        <div class="cc-expand">
          <div style="display:flex;flex-direction:column;gap:8px;margin-top:14px;">
            <div style="padding:10px;background:var(--lav-light);border-radius:10px;font-size:13px;"><strong>5 👀</strong> Things you can SEE</div>
            <div style="padding:10px;background:var(--blush);border-radius:10px;font-size:13px;"><strong>4 🖐️</strong> Things you can TOUCH</div>
            <div style="padding:10px;background:var(--sage-light);border-radius:10px;font-size:13px;"><strong>3 👂</strong> Things you can HEAR</div>
            <div style="padding:10px;background:var(--gold-light);border-radius:10px;font-size:13px;"><strong>2 👃</strong> Things you can SMELL</div>
            <div style="padding:10px;background:var(--peach-light);border-radius:10px;font-size:13px;"><strong>1 👅</strong> Thing you can TASTE</div>
          </div>
        </div>
      </div>
      <div class="calm-card-big" onclick="expandCalm(this)">
        <div class="cc-icon">📝</div>
        <h4>Brain Dump Journal</h4>
        <p>Write everything out. Clear your mind. No editing allowed.</p>
        <div class="cc-expand">
          <textarea id="journalText" placeholder="Write freely here... no one will see this 💕" style="width:100%;height:120px;border:1.5px solid rgba(201,184,232,0.3);border-radius:12px;padding:12px;font-family:'Nunito',sans-serif;font-size:13px;resize:none;outline:none;margin-top:12px;"></textarea>
          <button onclick="clearJournal(event)" style="background:none;border:none;color:var(--light);font-size:13px;cursor:pointer;margin-top:6px;">Clear & release 🍃</button>
        </div>
      </div>
      <div class="calm-card-big" onclick="expandCalm(this)">
        <div class="cc-icon">🎵</div>
        <h4>Mood Music</h4>
        <p>Music is medicine. Choose what resonates with how you want to feel.</p>
        <div class="cc-expand">
          <div style="display:flex;flex-direction:column;gap:8px;margin-top:14px;">
            <a href="https://www.youtube.com/results?search_query=calm+meditation+music" target="_blank" style="padding:10px 16px;background:var(--lav-light);border-radius:10px;font-size:13px;text-decoration:none;color:var(--lav-deep);font-weight:600;display:flex;justify-content:space-between;">🧘 Calm & Meditative <span>→</span></a>
            <a href="https://www.youtube.com/results?search_query=uplifting+happy+music" target="_blank" style="padding:10px 16px;background:var(--gold-light);border-radius:10px;font-size:13px;text-decoration:none;color:var(--gold-deep);font-weight:600;display:flex;justify-content:space-between;">☀️ Happy & Uplifting <span>→</span></a>
            <a href="https://www.youtube.com/results?search_query=focus+concentration+music" target="_blank" style="padding:10px 16px;background:var(--sage-light);border-radius:10px;font-size:13px;text-decoration:none;color:var(--sage-deep);font-weight:600;display:flex;justify-content:space-between;">🎯 Focus & Study <span>→</span></a>
            <a href="https://www.youtube.com/results?search_query=sleep+music+relaxation" target="_blank" style="padding:10px 16px;background:var(--blush);border-radius:10px;font-size:13px;text-decoration:none;color:var(--rose-deep);font-weight:600;display:flex;justify-content:space-between;">😴 Sleep & Rest <span>→</span></a>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- SELF CARE -->
  <div id="mp-selfcare" class="panel">
    <div style="background:linear-gradient(135deg,#7c5cbf,#a078d0);border-radius:var(--r);padding:40px;text-align:center;margin-bottom:24px;box-shadow:var(--shadow-lg);">
      <div style="font-family:'Cormorant Garamond',serif;font-size:32px;font-style:italic;color:white;line-height:1.4;margin-bottom:14px;" id="affirmText">"You are worthy of love, rest, and every good thing that comes your way."</div>
      <div style="font-size:13px;color:rgba(255,255,255,0.65);" id="affirmAuth">— A reminder just for you 🌸</div>
      <button onclick="nextAffirm()" style="background:rgba(255,255,255,0.2);border:1.5px solid rgba(255,255,255,0.4);color:white;padding:10px 24px;border-radius:50px;font-size:13px;font-weight:700;cursor:pointer;margin-top:20px;backdrop-filter:blur(8px);transition:all 0.2s;" onmouseover="this.style.background='rgba(255,255,255,0.3)'" onmouseout="this.style.background='rgba(255,255,255,0.2)'">New Affirmation ✨</button>
    </div>
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px;">
      <div class="selfcare-card" style="--c:#c85c7a;--bg:#fff0f4;">🛁<h4>Body Care</h4><ul><li>Warm bath with salts</li><li>Gentle self-massage</li><li>Skincare ritual</li><li>Rest without guilt</li></ul></div>
      <div class="selfcare-card" style="--c:#7c5cbf;--bg:#f5f0ff;">🧠<h4>Mind Care</h4><ul><li>5-min journaling</li><li>No doom-scrolling after 9pm</li><li>Read something joyful</li><li>Learn something small</li></ul></div>
      <div class="selfcare-card" style="--c:#3a8c65;--bg:#f0fff6;">🌿<h4>Nature & Movement</h4><ul><li>Morning sunlight, 10 mins</li><li>Barefoot on grass</li><li>Gentle yoga or stretch</li><li>Walk without phone</li></ul></div>
      <div class="selfcare-card" style="--c:#a07c20;--bg:#fffbef;">💌<h4>Connection</h4><ul><li>Text someone you love</li><li>Say no to one obligation</li><li>Ask for help today</li><li>Hug someone (or yourself)</li></ul></div>
    </div>
  </div>
</div>

<!-- CAREER PAGE -->
<div id="page-career" class="page">
  <div class="page-hero" style="background:linear-gradient(135deg,#efffed 0%,#d0f0d8 60%);">
    <div class="page-hero-inner">
      <div class="page-hero-text">
        <button class="back-btn" onclick="goPage('home')">← Back to Home</button>
        <h1 class="page-title" style="color:#3a8c65;">Rise,<br><em style="color:#52a87e;">Thrive, Lead</em></h1>
        <p class="page-desc">Career tools, financial independence guides, confidence boosters and daily empowerment — all in one place.</p>
      </div>
      <div style="font-size:80px;opacity:0.3;">💼</div>
    </div>
  </div>

  <div class="sub-tabs">
    <div class="sub-tab on" style="background:#3a8c65;color:white;" onclick="openSubTab('career','quiz',this)">🎯 Career Quiz</div>
    <div class="sub-tab" onclick="openSubTab('career','tips',this)">💡 Skill Tips</div>
    <div class="sub-tab" onclick="openSubTab('career','finance',this)">💰 Money Basics</div>
    <div class="sub-tab" onclick="openSubTab('career','inspire',this)">✨ Inspiration Board</div>
  </div>

  <!-- CAREER QUIZ -->
  <div id="cp-quiz" class="panel on">
    <div style="background:linear-gradient(135deg,#efffed,#d0f0d8);border-radius:var(--r);padding:40px;box-shadow:var(--shadow-lg);">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:30px;margin-bottom:8px;">🎯 What's Holding You Back?</h3>
      <p style="font-size:14px;color:var(--mid);margin-bottom:28px;">A quick 4-question quiz to identify your biggest career challenge and get personalised advice.</p>
      <div id="careerQuizArea">
        <div class="cquiz-step on" id="cq1">
          <div class="survey-q" style="color:var(--dark);">What's your biggest career challenge right now?</div>
          <div class="survey-options" style="margin-top:16px;">
            <div class="s-opt" onclick="pickCQ(this,'challenge','Imposter syndrome / self-doubt')" style="border-color:rgba(181,213,197,0.4);">🤔 Imposter syndrome / self-doubt</div>
            <div class="s-opt" onclick="pickCQ(this,'challenge','Work-life balance')" style="border-color:rgba(181,213,197,0.4);">⚖️ Work-life balance</div>
            <div class="s-opt" onclick="pickCQ(this,'challenge','Salary / negotiation')" style="border-color:rgba(181,213,197,0.4);">💰 Salary / negotiation</div>
            <div class="s-opt" onclick="pickCQ(this,'challenge','Gender bias at workplace')" style="border-color:rgba(181,213,197,0.4);">🛡️ Gender bias at workplace</div>
            <div class="s-opt" onclick="pickCQ(this,'challenge','Don\'t know what career path')" style="border-color:rgba(181,213,197,0.4);">🧭 Don't know my path</div>
          </div>
          <button class="btn-next" style="background:linear-gradient(135deg,var(--sage-deep),#52a87e);margin-top:16px;" onclick="nextCQ(1)">Next →</button>
        </div>
        <div class="cquiz-step" id="cq2">
          <div class="survey-q" style="color:var(--dark);">How long have you been in your current role?</div>
          <div class="survey-options" style="margin-top:16px;">
            <div class="s-opt" onclick="pickCQ(this,'experience','Just starting out')" style="border-color:rgba(181,213,197,0.4);">🌱 Just starting out</div>
            <div class="s-opt" onclick="pickCQ(this,'experience','1–3 years')" style="border-color:rgba(181,213,197,0.4);">📈 1–3 years</div>
            <div class="s-opt" onclick="pickCQ(this,'experience','4–8 years')" style="border-color:rgba(181,213,197,0.4);">🏆 4–8 years</div>
            <div class="s-opt" onclick="pickCQ(this,'experience','8+ years')" style="border-color:rgba(181,213,197,0.4);">⭐ 8+ years (senior)</div>
          </div>
          <div style="display:flex;gap:10px;margin-top:16px;">
            <button class="btn-next" style="background:linear-gradient(135deg,var(--sage-deep),#52a87e);" onclick="nextCQ(2)">Next →</button>
            <button class="btn-back-s" onclick="nextCQ(0)">← Back</button>
          </div>
        </div>
        <div class="cquiz-step" id="cq3">
          <div class="survey-q" style="color:var(--dark);">What skills do you want to develop?</div>
          <div class="survey-options" style="margin-top:16px;">
            <div class="s-opt" onclick="toggleCQ(this,'skills','Leadership')" style="border-color:rgba(181,213,197,0.4);">👑 Leadership</div>
            <div class="s-opt" onclick="toggleCQ(this,'skills','Communication')" style="border-color:rgba(181,213,197,0.4);">🗣️ Communication</div>
            <div class="s-opt" onclick="toggleCQ(this,'skills','Technical / Digital')" style="border-color:rgba(181,213,197,0.4);">💻 Technical / Digital</div>
            <div class="s-opt" onclick="toggleCQ(this,'skills','Financial literacy')" style="border-color:rgba(181,213,197,0.4);">📊 Financial literacy</div>
            <div class="s-opt" onclick="toggleCQ(this,'skills','Networking')" style="border-color:rgba(181,213,197,0.4);">🤝 Networking</div>
          </div>
          <div style="display:flex;gap:10px;margin-top:16px;">
            <button class="btn-next" style="background:linear-gradient(135deg,var(--sage-deep),#52a87e);" onclick="nextCQ(3)">See My Advice →</button>
            <button class="btn-back-s" onclick="nextCQ(1)">← Back</button>
          </div>
        </div>
        <div class="cquiz-step" id="cqResult">
          <div id="cqResultBox" style="background:white;border-radius:var(--rs);padding:28px;border-left:4px solid var(--sage-deep);"></div>
          <button class="btn-next" style="background:linear-gradient(135deg,var(--sage-deep),#52a87e);margin-top:16px;" onclick="resetCQ()">Retake Quiz 🔄</button>
        </div>
      </div>
    </div>
  </div>

  <!-- SKILLS TIPS -->
  <div id="cp-tips" class="panel">
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:18px;">
      <div class="career-tile" style="--c:#3a8c65;--bg:#efffed;">
        <span style="font-size:36px;">🎤</span>
        <h4>Own Your Voice</h4>
        <ul><li>Prepare 1 talking point before every meeting</li><li>Say "I recommend" — not "I think maybe"</li><li>When interrupted, say "Let me finish my thought"</li><li>Join a Toastmasters club near you</li></ul>
        <a href="https://www.toastmasters.org" target="_blank" class="cta-link">Find a club →</a>
      </div>
      <div class="career-tile" style="--c:#3a8c65;--bg:#efffed;">
        <span style="font-size:36px;">📜</span>
        <h4>Free Certifications</h4>
        <ul><li>Google Digital Garage — digital skills</li><li>NPTEL — IIT courses, free</li><li>Coursera — financial aid available</li><li>LinkedIn Learning — first month free</li></ul>
        <a href="https://learndigital.withgoogle.com" target="_blank" class="cta-link">Google Digital Garage →</a>
      </div>
      <div class="career-tile" style="--c:#3a8c65;--bg:#efffed;">
        <span style="font-size:36px;">📈</span>
        <h4>Build Your Brand</h4>
        <ul><li>Update LinkedIn with measurable wins</li><li>Write 1 post about your field monthly</li><li>Keep a "brag document" of achievements</li><li>Ask for LinkedIn recommendations</li></ul>
        <a href="https://linkedin.com" target="_blank" class="cta-link">Open LinkedIn →</a>
      </div>
      <div class="career-tile" style="--c:#3a8c65;--bg:#efffed;">
        <span style="font-size:36px;">🤝</span>
        <h4>Network Smartly</h4>
        <ul><li>Reconnect with 1 old contact per week</li><li>Attend 1 industry event per quarter</li><li>Offer value before asking for favours</li><li>Find a mentor — offer to be a mentee</li></ul>
      </div>
    </div>
  </div>

  <!-- MONEY BASICS -->
  <div id="cp-finance" class="panel">
    <div style="background:linear-gradient(135deg,#efffed,#d0f0d8);border-radius:var(--r);padding:40px;margin-bottom:20px;box-shadow:var(--shadow);">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:30px;margin-bottom:8px;">💰 Financial Independence Toolkit</h3>
      <p style="font-size:14px;color:var(--mid);margin-bottom:28px;">Beginner-friendly money lessons every woman should know.</p>
      <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:16px;">
        <div style="background:white;border-radius:var(--rs);padding:22px;box-shadow:0 2px 12px rgba(0,0,0,0.05);">
          <div style="font-size:28px;margin-bottom:10px;">🏦</div>
          <h4 style="font-family:'Cormorant Garamond',serif;font-size:20px;color:var(--sage-deep);margin-bottom:8px;">Save First, Spend Later</h4>
          <p style="font-size:13px;color:var(--mid);line-height:1.6;">Follow the 50-30-20 rule: 50% needs, 30% wants, 20% savings. Automate your savings on pay day.</p>
        </div>
        <div style="background:white;border-radius:var(--rs);padding:22px;box-shadow:0 2px 12px rgba(0,0,0,0.05);">
          <div style="font-size:28px;margin-bottom:10px;">📈</div>
          <h4 style="font-family:'Cormorant Garamond',serif;font-size:20px;color:var(--sage-deep);margin-bottom:8px;">SIP — Start Small</h4>
          <p style="font-size:13px;color:var(--mid);line-height:1.6;">A SIP of just ₹500/month in an index fund grows to ₹1 lakh+ in 10 years. Compound interest is magic.</p>
        </div>
        <div style="background:white;border-radius:var(--rs);padding:22px;box-shadow:0 2px 12px rgba(0,0,0,0.05);">
          <div style="font-size:28px;margin-bottom:10px;">🛡️</div>
          <h4 style="font-family:'Cormorant Garamond',serif;font-size:20px;color:var(--sage-deep);margin-bottom:8px;">Emergency Fund First</h4>
          <p style="font-size:13px;color:var(--mid);line-height:1.6;">Build 3–6 months of expenses in a liquid account before investing. This is your financial safety net.</p>
        </div>
        <div style="background:white;border-radius:var(--rs);padding:22px;box-shadow:0 2px 12px rgba(0,0,0,0.05);">
          <div style="font-size:28px;margin-bottom:10px;">📋</div>
          <h4 style="font-family:'Cormorant Garamond',serif;font-size:20px;color:var(--sage-deep);margin-bottom:8px;">File Your ITR</h4>
          <p style="font-size:13px;color:var(--mid);line-height:1.6;">Every earning woman should file ITR. It builds your financial history — essential for loans and visas.</p>
          <a href="https://www.incometax.gov.in" target="_blank" style="font-size:12px;color:var(--sage-deep);font-weight:700;text-decoration:none;">Income Tax Portal →</a>
        </div>
      </div>
    </div>
  </div>

  <!-- INSPIRATION BOARD -->
  <div id="cp-inspire" class="panel">
    <div style="background:linear-gradient(135deg,#3a8c65,#52a87e);border-radius:var(--r);padding:40px;text-align:center;margin-bottom:24px;box-shadow:var(--shadow-lg);">
      <div style="font-family:'Cormorant Garamond',serif;font-size:30px;font-style:italic;color:white;line-height:1.4;margin-bottom:10px;" id="insQuote">"There is no limit to what we, as women, can accomplish."</div>
      <div style="font-size:13px;color:rgba(255,255,255,0.65);" id="insAuthor">— Michelle Obama</div>
      <button onclick="nextInspo()" style="background:rgba(255,255,255,0.2);border:1.5px solid rgba(255,255,255,0.4);color:white;padding:10px 24px;border-radius:50px;font-size:13px;font-weight:700;cursor:pointer;margin-top:20px;">New Inspiration ✨</button>
    </div>
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:16px;">
      <div style="background:linear-gradient(135deg,#efffed,#d0f0d8);border-radius:var(--rs);padding:22px;text-align:center;cursor:pointer;transition:all 0.3s;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
        <div style="font-size:36px;margin-bottom:10px;">👩‍💻</div>
        <h4 style="font-size:14px;font-weight:700;color:var(--sage-deep);">Indra Nooyi</h4>
        <p style="font-size:12px;color:var(--mid);">CEO of PepsiCo for 12 years. From Chennai to Fortune 500.</p>
      </div>
      <div style="background:linear-gradient(135deg,#fff0f4,#ffd6e4);border-radius:var(--rs);padding:22px;text-align:center;cursor:pointer;transition:all 0.3s;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
        <div style="font-size:36px;margin-bottom:10px;">🚀</div>
        <h4 style="font-size:14px;font-weight:700;color:var(--rose-deep);">Kiran Mazumdar Shaw</h4>
        <p style="font-size:12px;color:var(--mid);">Built Biocon from a garage. India's biotech pioneer.</p>
      </div>
      <div style="background:linear-gradient(135deg,#f5f0ff,#ede4fc);border-radius:var(--rs);padding:22px;text-align:center;cursor:pointer;transition:all 0.3s;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
        <div style="font-size:36px;margin-bottom:10px;">⭐</div>
        <h4 style="font-size:14px;font-weight:700;color:var(--lav-deep);">Falguni Nayar</h4>
        <p style="font-size:12px;color:var(--mid);">Founded Nykaa at 49. Became India's richest self-made woman.</p>
      </div>
      <div style="background:linear-gradient(135deg,#fffbef,#ffefc4);border-radius:var(--rs);padding:22px;text-align:center;cursor:pointer;transition:all 0.3s;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
        <div style="font-size:36px;margin-bottom:10px;">🏆</div>
        <h4 style="font-size:14px;font-weight:700;color:var(--gold-deep);">Mary Kom</h4>
        <p style="font-size:12px;color:var(--mid);">6-time World Boxing Champion. Proved sports has no gender.</p>
      </div>
    </div>
  </div>
</div>

<!-- LEGAL PAGE -->
<div id="page-legal" class="page">
  <div class="page-hero" style="background:linear-gradient(135deg,#fffbef 0%,#ffefc4 60%);">
    <div class="page-hero-inner">
      <div class="page-hero-text">
        <button class="back-btn" onclick="goPage('home')">← Back to Home</button>
        <h1 class="page-title" style="color:#a07c20;">Know Your<br><em style="color:#c0633a;">Rights</em></h1>
        <p class="page-desc">Plain language explanations of laws that protect you — no jargon, no confusion.</p>
      </div>
      <div style="font-size:80px;opacity:0.3;">⚖️</div>
    </div>
  </div>

  <div class="sub-tabs">
    <div class="sub-tab on" style="background:#a07c20;color:white;" onclick="openSubTab('legal','quiz2',this)">🧩 Rights Quiz</div>
    <div class="sub-tab" onclick="openSubTab('legal','laws',this)">📜 Key Laws</div>
    <div class="sub-tab" onclick="openSubTab('legal','steps',this)">👣 Action Steps</div>
    <div class="sub-tab" onclick="openSubTab('legal','resources',this)">🔗 Resources</div>
  </div>

  <!-- RIGHTS QUIZ -->
  <div id="lp-quiz2" class="panel on">
    <div style="background:linear-gradient(135deg,#fffbef,#ffefc4);border-radius:var(--r);padding:40px;box-shadow:var(--shadow-lg);">
      <h3 style="font-family:'Cormorant Garamond',serif;font-size:30px;margin-bottom:8px;">🧩 Know Your Rights — Quick Quiz</h3>
      <p style="font-size:14px;color:var(--mid);margin-bottom:28px;">Test your legal knowledge. Learn as you go!</p>
      <div id="lqArea">
        <div class="lquiz-q on" id="lq0">
          <div style="font-size:12px;color:var(--light);letter-spacing:1.5px;text-transform:uppercase;margin-bottom:8px;">Question 1 of 5</div>
          <div style="font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:20px;color:var(--dark);">Under the POSH Act 2013, within how many days must you file a workplace harassment complaint?</div>
          <div style="display:flex;flex-direction:column;gap:10px;" id="lq0opts">
            <div class="lq-opt" onclick="answerLQ(0,0,'3 months (90 days)')">A. 30 days</div>
            <div class="lq-opt" onclick="answerLQ(0,1,'3 months (90 days)')">B. 3 months (90 days) ✓</div>
            <div class="lq-opt" onclick="answerLQ(0,2,'3 months (90 days)')">C. 6 months</div>
            <div class="lq-opt" onclick="answerLQ(0,3,'3 months (90 days)')">D. 1 year</div>
          </div>
          <div class="lq-explanation" id="lqe0"></div>
        </div>
        <div class="lquiz-q" id="lq1">
          <div style="font-size:12px;color:var(--light);letter-spacing:1.5px;text-transform:uppercase;margin-bottom:8px;">Question 2 of 5</div>
          <div style="font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:20px;">How many weeks of paid maternity leave are you entitled to for your first two children?</div>
          <div style="display:flex;flex-direction:column;gap:10px;" id="lq1opts">
            <div class="lq-opt" onclick="answerLQ(1,0,'26 weeks')">A. 12 weeks</div>
            <div class="lq-opt" onclick="answerLQ(1,1,'26 weeks')">B. 26 weeks ✓</div>
            <div class="lq-opt" onclick="answerLQ(1,2,'26 weeks')">C. 52 weeks</div>
            <div class="lq-opt" onclick="answerLQ(1,3,'26 weeks')">D. 8 weeks</div>
          </div>
          <div class="lq-explanation" id="lqe1"></div>
        </div>
        <div class="lquiz-q" id="lq2">
          <div style="font-size:12px;color:var(--light);letter-spacing:1.5px;text-transform:uppercase;margin-bottom:8px;">Question 3 of 5</div>
          <div style="font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:20px;">Under which act do daughters have equal inheritance rights in ancestral property?</div>
          <div style="display:flex;flex-direction:column;gap:10px;" id="lq2opts">
            <div class="lq-opt" onclick="answerLQ(2,0,'Hindu Succession Act (amended 2005)')">A. Indian Contract Act</div>
            <div class="lq-opt" onclick="answerLQ(2,1,'Hindu Succession Act (amended 2005)')">B. Hindu Succession Act (amended 2005) ✓</div>
            <div class="lq-opt" onclick="answerLQ(2,2,'Hindu Succession Act (amended 2005)')">C. Property Act 1882</div>
            <div class="lq-opt" onclick="answerLQ(2,3,'Hindu Succession Act (amended 2005)')">D. Family Courts Act</div>
          </div>
          <div class="lq-explanation" id="lqe2"></div>
        </div>
        <div class="lquiz-q" id="lq3">
          <div style="font-size:12px;color:var(--light);letter-spacing:1.5px;text-transform:uppercase;margin-bottom:8px;">Question 4 of 5</div>
          <div style="font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:20px;">Which helpline number is for reporting cyber crimes in India?</div>
          <div style="display:flex;flex-direction:column;gap:10px;" id="lq3opts">
            <div class="lq-opt" onclick="answerLQ(3,0,'1930')">A. 1091</div>
            <div class="lq-opt" onclick="answerLQ(3,1,'1930')">B. 1930 ✓</div>
            <div class="lq-opt" onclick="answerLQ(3,2,'1930')">C. 100</div>
            <div class="lq-opt" onclick="answerLQ(3,3,'1930')">D. 181</div>
          </div>
          <div class="lq-explanation" id="lqe3"></div>
        </div>
        <div class="lquiz-q" id="lq4">
          <div style="font-size:12px;color:var(--light);letter-spacing:1.5px;text-transform:uppercase;margin-bottom:8px;">Question 5 of 5</div>
          <div style="font-family:'Cormorant Garamond',serif;font-size:24px;margin-bottom:20px;">Under CrPC Section 46, a woman generally cannot be arrested after what time?</div>
          <div style="display:flex;flex-direction:column;gap:10px;" id="lq4opts">
            <div class="lq-opt" onclick="answerLQ(4,0,'Sunset')">A. 8 PM</div>
            <div class="lq-opt" onclick="answerLQ(4,1,'Sunset')">B. Sunset (and before sunrise) ✓</div>
            <div class="lq-opt" onclick="answerLQ(4,2,'Sunset')">C. Midnight</div>
            <div class="lq-opt" onclick="answerLQ(4,3,'Sunset')">D. 10 PM</div>
          </div>
          <div class="lq-explanation" id="lqe4"></div>
        </div>
        <div class="lquiz-q" id="lqFinal" style="text-align:center;">
          <div style="font-size:48px;margin-bottom:16px;" id="lqEmoji">🏆</div>
          <div style="font-family:'Cormorant Garamond',serif;font-size:32px;margin-bottom:8px;" id="lqFinalTitle">Quiz Complete!</div>
          <div style="font-size:15px;color:var(--mid);" id="lqFinalMsg"></div>
          <button class="btn-next" style="background:linear-gradient(135deg,var(--gold-deep),#c0963a);margin-top:20px;" onclick="resetLQ()">Retake Quiz 🔄</button>
        </div>
      </div>
      <div style="display:flex;justify-content:space-between;align-items:center;margin-top:20px;">
        <div style="font-size:13px;color:var(--mid);">Score: <strong id="lqScore">0</strong>/5</div>
        <button id="lqNext" class="btn-next" style="background:linear-gradient(135deg,var(--gold-deep),#c0963a);display:none;" onclick="nextLQ()">Next Question →</button>
      </div>
    </div>
  </div>

  <!-- KEY LAWS -->
  <div id="lp-laws" class="panel">
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:18px;">
      <div class="law-card"><span class="law-badge" style="background:var(--gold-light);color:var(--gold-deep);">POSH Act 2013</span><h3>Sexual Harassment at Workplace</h3><p>Every organisation with 10+ employees must have an Internal Complaints Committee. You can file a complaint within 3 months. The employer cannot retaliate against you.</p><div class="law-actions"><button class="law-btn" onclick="openGuideModal('posh')">Read Your Rights</button></div></div>
      <div class="law-card"><span class="law-badge" style="background:var(--blush);color:var(--rose-deep);">DV Act 2005</span><h3>Protection from Domestic Violence</h3><p>Covers physical, emotional, sexual and economic abuse. You can get a Protection Order, Residence Order, and monetary relief. The woman can remain in the shared household.</p><div class="law-actions"><button class="law-btn" onclick="callNumber('181')">Call 181 for Help</button></div></div>
      <div class="law-card"><span class="law-badge" style="background:var(--sage-light);color:var(--sage-deep);">Hindu Succession Act</span><h3>Equal Inheritance Rights</h3><p>Amended in 2005 — daughters have equal share in ancestral property from birth. This right cannot be taken away by a will alone. Applies regardless of marital status.</p><div class="law-actions"><button class="law-btn" onclick="callNumber('15100')">Free Legal Aid: 15100</button></div></div>
      <div class="law-card"><span class="law-badge" style="background:var(--lav-light);color:var(--lav-deep);">Maternity Benefit Act</span><h3>Maternity Leave & Benefits</h3><p>26 weeks of fully paid maternity leave for first two children. 12 weeks for third+ child. No termination during pregnancy. Work from home option post leave (in many companies).</p><div class="law-actions"><button class="law-btn">Know Your Benefits</button></div></div>
      <div class="law-card"><span class="law-badge" style="background:var(--peach-light);color:var(--peach-deep);">IT Act Sections 66C, 67</span><h3>Cyber Crime Protection</h3><p>Sections 66E (privacy violation), 66C (identity theft) and 67 (obscene content) protect you online. Report at cybercrime.gov.in or call 1930.</p><div class="law-actions"><a href="https://cybercrime.gov.in" target="_blank" class="law-btn" style="text-decoration:none;">Report Online</a></div></div>
      <div class="law-card"><span class="law-badge" style="background:var(--gold-light);color:var(--gold-deep);">CrPC Section 46</span><h3>Right Against Wrongful Arrest</h3><p>A woman cannot generally be arrested after sunset and before sunrise. You can demand a female officer for search. You have the right to be informed of charges and to free legal aid.</p><div class="law-actions"><button class="law-btn" onclick="callNumber('15100')">Legal Aid: 15100</button></div></div>
    </div>
  </div>

  <!-- ACTION STEPS (legal) -->
  <div id="lp-steps" class="panel">
    <div style="display:flex;flex-direction:column;gap:14px;">
      <div class="guide-accordion" style="border-left-color:var(--gold);">
        <div class="guide-header" onclick="toggleGuide(this)" style="background:var(--gold-light);">
          <span>📝 How to file a POSH complaint</span><span class="gacc-arrow">▼</span>
        </div>
        <div class="guide-body">
          <div class="guide-step"><span class="gs-num" style="background:var(--gold-light);color:var(--gold-deep);">1</span><div>Write a formal complaint addressed to the ICC (Internal Complaints Committee) of your organisation.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--gold-light);color:var(--gold-deep);">2</span><div>Include: date, time, location, what happened, names of witnesses if any, and how it affected you.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--gold-light);color:var(--gold-deep);">3</span><div>Attach any evidence — screenshots, emails, messages.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--gold-light);color:var(--gold-deep);">4</span><div>File within 3 months of the last incident. The ICC must respond within 90 days.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--gold-light);color:var(--gold-deep);">5</span><div>If the company has no ICC, file with the Local Complaints Committee (LCC) in your district.</div></div>
        </div>
      </div>
      <div class="guide-accordion" style="border-left-color:var(--rose);">
        <div class="guide-header" onclick="toggleGuide(this)" style="background:var(--blush);">
          <span>🏠 How to file a domestic violence case</span><span class="gacc-arrow">▼</span>
        </div>
        <div class="guide-body">
          <div class="guide-step"><span class="gs-num" style="background:var(--blush);color:var(--rose-deep);">1</span><div>Call 181 (Women's Helpline) or visit the nearest police station / Mahila Thana.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--blush);color:var(--rose-deep);">2</span><div>Get medical examination for any injuries. Keep the medical report — it is legal evidence.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--blush);color:var(--rose-deep);">3</span><div>File a Domestic Incident Report (DIR) with a Protection Officer (available at every district office).</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--blush);color:var(--rose-deep);">4</span><div>Apply to the Magistrate for a Protection Order. You can do this without a lawyer using free legal aid.</div></div>
          <div class="guide-step"><span class="gs-num" style="background:var(--blush);color:var(--rose-deep);">5</span><div>You are entitled to emergency shelter. Call 181 to be connected to a Shelter Home.</div></div>
        </div>
      </div>
    </div>
  </div>

  <!-- RESOURCES -->
  <div id="lp-resources" class="panel">
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:16px;">
      <a href="https://ncw.nic.in" target="_blank" class="resource-link-card">🏛️<h4>National Commission for Women</h4><p>File online complaints, access legal resources</p></a>
      <a href="https://cybercrime.gov.in" target="_blank" class="resource-link-card">💻<h4>National Cyber Crime Reporting Portal</h4><p>Report online harassment, financial fraud</p></a>
      <a href="https://nalsa.gov.in" target="_blank" class="resource-link-card">⚖️<h4>NALSA — Free Legal Aid</h4><p>Free legal services for women in India</p></a>
      <a href="https://wcd.nic.in" target="_blank" class="resource-link-card">👩<h4>Ministry of Women & Child Dev</h4><p>Schemes, policies, and rights for women</p></a>
    </div>
  </div>
</div>

<!-- ═══════════════ EXTRA STYLES ═══════════════ -->
<style>
/* Safety */
.helpline-tile{background:white;border-radius:var(--rs);padding:18px 20px;display:flex;align-items:center;gap:14px;box-shadow:0 2px 12px rgba(0,0,0,0.05);cursor:pointer;transition:all 0.2s;position:relative;}
.helpline-tile:hover{transform:translateY(-3px);box-shadow:var(--shadow);}
.call-badge{position:absolute;top:10px;right:14px;background:linear-gradient(135deg,#c0633a,#e07030);color:white;font-size:10px;font-weight:700;padding:3px 8px;border-radius:20px;}
/* Safety — embedded Leaflet map (index2 feature) */
#sp-map .sm-hidden{display:none!important;}
#sp-map .sm-map-shell{display:flex;flex-direction:column;border-radius:var(--rs);overflow:hidden;border:1px solid rgba(0,0,0,0.1);background:#0b0f14;min-height:460px;}
@media(min-width:960px){#sp-map .sm-map-shell{flex-direction:row;min-height:480px;}}
#sp-map .sm-sidebar{display:flex;flex-direction:column;background:#111827;color:#e5e7eb;max-height:340px;min-height:0;}
@media(min-width:960px){#sp-map .sm-sidebar{width:360px;max-height:none;flex-shrink:0;}}
#sp-map .sm-sidebar-head{padding:18px 20px;border-bottom:1px solid #1f2937;background:#0c1117;}
#sp-map .sm-sidebar-title{font-family:'Cormorant Garamond',serif;font-size:22px;font-weight:600;color:#fff;margin:0;display:flex;align-items:center;gap:10px;}
#sp-map .sm-title-ico{color:#60a5fa;}
#sp-map .sm-sidebar-sub{font-size:11px;color:#9ca3af;margin:6px 0 0;}
#sp-map .sm-sidebar-controls{padding:16px 18px;border-bottom:1px solid #1f2937;}
#sp-map .sm-search-form{position:relative;margin-bottom:12px;}
#sp-map #sm-search-input{width:100%;background:#1f2937;border:1px solid #374151;color:#e5e7eb;font-size:13px;border-radius:10px;padding:12px 88px 12px 38px;outline:none;font-family:'Nunito',sans-serif;}
#sp-map #sm-search-input::placeholder{color:#6b7280;}
#sp-map .sm-search-ico{position:absolute;left:12px;top:50%;transform:translateY(-50%);color:#6b7280;font-size:13px;pointer-events:none;}
#sp-map .sm-search-submit{position:absolute;right:4px;top:50%;transform:translateY(-50%);background:#2563eb;color:#fff;border:none;border-radius:8px;padding:8px 12px;font-size:12px;font-weight:600;cursor:pointer;font-family:'Nunito',sans-serif;}
#sp-map .sm-search-submit:hover{background:#1d4ed8;}
#sp-map .sm-loc-btn{width:100%;display:flex;align-items:center;justify-content:center;gap:10px;padding:11px;background:#1f2937;border:1px solid #374151;border-radius:10px;color:#e5e7eb;font-size:13px;font-weight:600;cursor:pointer;font-family:'Nunito',sans-serif;transition:background .2s;}
#sp-map .sm-loc-btn:hover{background:#374151;}
#sp-map .sm-loc-ico{transition:transform .2s;}
#sp-map .sm-loc-btn:hover .sm-loc-ico{transform:scale(1.1);}
#sp-map .sm-loc-btn.sm-tracking-on{background:#2563eb;border-color:#3b82f6;color:#fff;}
#sp-map .sm-loc-btn.sm-tracking-on .sm-loc-ico{color:#fff!important;}
#sp-map .sm-zoom-hint{font-size:10px;color:#6b7280;margin-top:10px;display:flex;align-items:center;gap:6px;}
#sp-map .sm-zoom-hint-strong{color:#60a5fa;font-weight:600;}
#sp-map .sm-error{color:#f87171;font-size:12px;margin-top:8px;}
#sp-map .sm-info-panel{flex:1;min-height:0;overflow-y:auto;padding:18px;display:flex;flex-direction:column;gap:16px;}
#sp-map .sm-info-head{text-align:center;margin-bottom:4px;}
#sp-map .sm-loc-title{font-family:'Cormorant Garamond',serif;font-size:26px;font-weight:600;color:#fff;margin:0 0 6px;}
#sp-map .sm-live-badge{display:inline-flex;align-items:center;gap:6px;padding:4px 10px;border-radius:6px;background:rgba(59,130,246,.15);color:#60a5fa;font-size:10px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;border:1px solid rgba(59,130,246,.3);margin-bottom:8px;}
#sp-map .sm-live-dot{width:6px;height:6px;border-radius:50%;background:#3b82f6;animation:sm-pulse-dot 1.5s ease infinite;}
@keyframes sm-pulse-dot{0%,100%{opacity:1}50%{opacity:.4}}
#sp-map .sm-loc-sub{font-size:11px;color:#9ca3af;letter-spacing:.12em;text-transform:uppercase;margin:0;}
#sp-map .sm-nearest-label{font-size:10px;font-weight:700;color:#6b7280;text-transform:uppercase;letter-spacing:.1em;margin:0 0 8px;display:flex;align-items:center;gap:8px;}
#sp-map .sm-card{border-radius:12px;padding:14px;}
#sp-map .sm-card-hosp{background:rgba(30,58,138,.25);border:1px solid rgba(30,64,175,.35);}
#sp-map .sm-card-pol{background:rgba(127,29,29,.25);border:1px solid rgba(153,27,27,.35);}
#sp-map .sm-card-inner{display:flex;align-items:center;gap:12px;}
#sp-map .sm-card-icon{width:40px;height:40px;border-radius:50%;display:flex;align-items:center;justify-content:center;color:#fff;flex-shrink:0;}
#sp-map .sm-icon-hosp{background:#2563eb;}
#sp-map .sm-icon-pol{background:#dc2626;}
#sp-map .sm-card-text{flex:1;min-width:0;}
#sp-map .sm-card-tag{font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:.04em;}
#sp-map .sm-tag-hosp{color:#60a5fa;}
#sp-map .sm-tag-pol{color:#f87171;}
#sp-map .sm-card-name{font-size:14px;font-weight:600;color:#fff;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
#sp-map .sm-card-dist{font-size:12px;color:#9ca3af;}
#sp-map .sm-goto{background:none;border:none;color:inherit;cursor:pointer;padding:8px;font-size:16px;opacity:.9;}
#sp-map .sm-goto:hover{opacity:1;}
#sp-map .sm-ai-box{background:rgba(31,41,55,.6);border:1px solid #374151;border-radius:12px;padding:16px;}
#sp-map .sm-ai-title{font-size:12px;font-weight:600;color:#d1d5db;margin:0 0 10px;display:flex;align-items:center;gap:8px;}
#sp-map .sm-ai-content{font-size:12px;color:#9ca3af;line-height:1.6;}
#sp-map .sm-typing{display:flex;align-items:center;gap:4px;}
#sp-map .sm-typing span{width:6px;height:6px;background:#6b7280;border-radius:50%;animation:sm-typing 1.4s infinite both;}
#sp-map .sm-typing span:nth-child(2){animation-delay:.2s;}
#sp-map .sm-typing span:nth-child(3){animation-delay:.4s;}
@keyframes sm-typing{0%,80%,100%{transform:scale(0);opacity:.5}40%{transform:scale(1);opacity:1}}
#sp-map .sm-placeholder{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:28px;text-align:center;color:#6b7280;min-height:120px;}
#sp-map .sm-ph-icon{font-size:48px;color:#3b82f6;margin-bottom:12px;animation:sm-pulse-dot 2s ease infinite;}
#sp-map .sm-ph-title{font-size:17px;font-weight:600;color:#d1d5db;margin:0 0 8px;font-family:'Nunito',sans-serif;}
#sp-map .sm-ph-text{font-size:13px;line-height:1.55;max-width:280px;margin:0;}
#sp-map .sm-sidebar-foot{padding:12px;font-size:10px;color:#4b5563;text-align:center;border-top:1px solid #1f2937;}
#sp-map .sm-map-wrap{flex:1;position:relative;min-height:360px;}
@media(min-width:960px){#sp-map .sm-map-wrap{min-height:100%;}}
#sp-map #sm-map{position:absolute;inset:0;width:100%;height:100%;z-index:1;}
#sp-map .sm-poi-status{position:absolute;top:12px;left:50%;transform:translateX(-50%);z-index:500;pointer-events:none;}
#sp-map .sm-poi-status-inner{display:flex;align-items:center;gap:10px;background:rgba(17,24,39,.92);border:1px solid #374151;border-radius:999px;padding:8px 16px;font-size:12px;color:#e5e7eb;box-shadow:0 8px 24px rgba(0,0,0,.35);}
#sp-map .sm-spin{color:#60a5fa;}
#sp-map .sm-crosshair-btn{position:absolute;bottom:18px;right:18px;z-index:500;width:48px;height:48px;border-radius:50%;background:#111827;border:1px solid #374151;color:#fff;cursor:pointer;display:flex;align-items:center;justify-content:center;box-shadow:0 8px 24px rgba(0,0,0,.35);font-size:18px;transition:background .2s;}
#sp-map .sm-crosshair-btn:hover{background:#1f2937;}
#sp-map .leaflet-popup-content-wrapper{background:#1f2937;color:#f3f4f6;border-radius:8px;border:1px solid #374151;}
#sp-map .leaflet-popup-tip{background:#1f2937;}
#sp-map .poi-container{display:flex;flex-direction:column;align-items:center;text-align:center;}
#sp-map .poi-label{font-size:10px;font-weight:700;color:#fff;text-shadow:2px 2px 3px #000,-1px -1px 3px #000,0 0 5px rgba(0,0,0,1);white-space:normal;width:100px;margin-top:2px;pointer-events:none;line-height:1;display:none;}
#sp-map .leaflet-zoom-10 .poi-label,#sp-map .leaflet-zoom-11 .poi-label,#sp-map .leaflet-zoom-12 .poi-label,#sp-map .leaflet-zoom-13 .poi-label,#sp-map .leaflet-zoom-14 .poi-label,#sp-map .leaflet-zoom-15 .poi-label,#sp-map .leaflet-zoom-16 .poi-label,#sp-map .leaflet-zoom-17 .poi-label,#sp-map .leaflet-zoom-18 .poi-label{display:block;}
#sp-map .poi-wrapper i{transition:transform .2s;}
#sp-map .poi-wrapper:hover i{transform:scale(1.25);}
#sp-map .user-location-pulse{width:20px;height:20px;background:#3b82f6;border:3px solid #fff;border-radius:50%;box-shadow:0 0 10px rgba(0,0,0,.5);position:relative;}
#sp-map .user-location-pulse::after{content:"";position:absolute;top:-10px;left:-10px;width:34px;height:34px;background:rgba(59,130,246,.4);border-radius:50%;animation:sm-pulse-blue 2s infinite;}
@keyframes sm-pulse-blue{0%{transform:scale(.5);opacity:1}100%{transform:scale(2.5);opacity:0}}
#sp-map .pulse-red{animation:sm-pulse-red 2s infinite;}
@keyframes sm-pulse-red{0%{box-shadow:0 0 0 0 rgba(239,68,68,.7)}70%{box-shadow:0 0 0 10px rgba(239,68,68,0)}100%{box-shadow:0 0 0 0 rgba(239,68,68,0)}}
#sp-map .active-tracking{background-color:#2563eb!important;color:#fff!important;border-color:#3b82f6!important;}
.safety-topic-card{background:white;border-radius:var(--r);padding:28px;box-shadow:var(--shadow);border-top:4px solid var(--accent);}
.safety-topic-card .stc-icon{font-size:36px;margin-bottom:14px;display:block;}
.safety-topic-card h3{font-family:'Cormorant Garamond',serif;font-size:22px;color:var(--accent);margin-bottom:12px;}
.stc-list{list-style:none;margin-top:10px;}
.stc-list li{font-size:13px;color:var(--mid);padding:6px 0;border-bottom:1px solid rgba(0,0,0,0.04);display:flex;gap:10px;}
.stc-list li::before{content:'→';color:var(--peach-deep);font-weight:700;flex-shrink:0;}
.guide-accordion{background:white;border-radius:var(--rs);overflow:hidden;box-shadow:var(--shadow);border-left:4px solid var(--peach);}
.guide-header{padding:18px 22px;display:flex;justify-content:space-between;align-items:center;cursor:pointer;font-size:15px;font-weight:700;color:var(--dark);transition:background 0.2s;}
.guide-header:hover{background:rgba(0,0,0,0.02);}
.gacc-arrow{transition:transform 0.3s;font-size:12px;color:var(--light);}
.guide-header.open .gacc-arrow{transform:rotate(180deg);}
.guide-body{max-height:0;overflow:hidden;transition:max-height 0.4s ease;padding:0 22px;}
.guide-body.open{max-height:600px;padding:0 22px 20px;}
.guide-step{display:flex;gap:14px;align-items:flex-start;padding:10px 0;font-size:13px;color:var(--mid);line-height:1.6;}
.gs-num{width:26px;height:26px;border-radius:50%;background:var(--peach-light);color:var(--peach-deep);display:flex;align-items:center;justify-content:center;font-size:12px;font-weight:800;flex-shrink:0;}

/* Mental */
.chat-msg{display:flex;gap:10px;align-items:flex-end;}
.chat-msg.bot{flex-direction:row;}
.chat-msg.user{flex-direction:row-reverse;}
.chat-bubble{padding:12px 16px;border-radius:18px;font-size:14px;line-height:1.6;max-width:80%;white-space:pre-wrap;word-break:break-word;}
.chat-bubble.bot{background:linear-gradient(135deg,#f0eaff,#e8dcfc);color:var(--dark);border-bottom-left-radius:4px;}
.chat-bubble.user{background:linear-gradient(135deg,#7c5cbf,#9070cc);color:white;border-bottom-right-radius:4px;}
.bot-avatar{width:32px;height:32px;border-radius:50%;background:linear-gradient(135deg,#7c5cbf,#9070cc);display:flex;align-items:center;justify-content:center;font-size:16px;flex-shrink:0;}
.quick-reply{padding:8px 16px;border-radius:50px;border:1.5px solid rgba(201,184,232,0.4);font-size:12px;font-weight:600;cursor:pointer;transition:all 0.2s;background:white;color:var(--mid);font-family:'Nunito',sans-serif;}
.quick-reply:hover{background:var(--lav-light);border-color:var(--lav);}
.mood-big-btn{background:white;border:2px solid transparent;border-radius:16px;padding:18px 20px;font-size:28px;cursor:pointer;transition:all 0.3s;box-shadow:0 2px 12px rgba(0,0,0,0.06);display:flex;flex-direction:column;align-items:center;gap:8px;}
.mood-big-btn span{font-size:12px;font-weight:700;color:var(--mid);}
.mood-big-btn:hover,.mood-big-btn.active{border-color:var(--lav);transform:scale(1.08) translateY(-4px);box-shadow:var(--shadow);}
.calm-card-big{background:white;border-radius:var(--r);padding:28px;box-shadow:var(--shadow);cursor:pointer;border:1.5px solid rgba(201,184,232,0.2);transition:all 0.3s;}
.calm-card-big:hover{transform:translateY(-4px);}
.cc-icon{font-size:36px;margin-bottom:12px;display:block;}
.calm-card-big h4{font-family:'Cormorant Garamond',serif;font-size:22px;color:var(--lav-deep);margin-bottom:8px;}
.calm-card-big p{font-size:13px;color:var(--mid);line-height:1.6;}
.cc-expand{max-height:0;overflow:hidden;transition:max-height 0.4s ease;text-align:center;}
.cc-expand.open{max-height:400px;}
.selfcare-card{background:var(--bg);border-radius:var(--rs);padding:24px;border-top:3px solid var(--c);box-shadow:var(--shadow);}
.selfcare-card>span{font-size:32px;display:block;margin-bottom:10px;}
.selfcare-card h4{font-family:'Cormorant Garamond',serif;font-size:20px;color:var(--c);margin-bottom:10px;}
.selfcare-card ul{list-style:none;margin-top:8px;}
.selfcare-card ul li{font-size:12px;color:var(--mid);padding:4px 0;display:flex;gap:8px;}
.selfcare-card ul li::before{content:'✦';color:var(--c);font-size:10px;}

/* Career */
.cquiz-step{display:none;}
.cquiz-step.on{display:block;}
.career-tile{background:var(--bg);border-radius:var(--r);padding:28px;box-shadow:var(--shadow);border-top:4px solid var(--c);}
.career-tile h4{font-family:'Cormorant Garamond',serif;font-size:22px;color:var(--c);margin:12px 0 10px;}
.career-tile ul{list-style:none;}
.career-tile ul li{font-size:13px;color:var(--mid);padding:5px 0;display:flex;gap:8px;}
.career-tile ul li::before{content:'→';color:var(--c);font-weight:700;}
.cta-link{display:inline-block;margin-top:14px;font-size:12px;font-weight:700;color:var(--c);text-decoration:none;border:1.5px solid currentColor;padding:7px 16px;border-radius:50px;transition:all 0.2s;}
.cta-link:hover{background:rgba(58,140,101,0.1);}

/* Legal */
.law-card{background:white;border-radius:var(--r);padding:28px;box-shadow:var(--shadow);border-left:4px solid var(--gold);}
.law-badge{display:inline-block;font-size:10px;font-weight:800;padding:4px 10px;border-radius:20px;letter-spacing:1px;text-transform:uppercase;margin-bottom:10px;}
.law-card h3{font-family:'Cormorant Garamond',serif;font-size:20px;color:var(--dark);margin-bottom:10px;}
.law-card p{font-size:13px;color:var(--mid);line-height:1.6;margin-bottom:14px;}
.law-actions{margin-top:12px;}
.law-btn{background:none;border:1.5px solid rgba(232,201,122,0.6);color:var(--gold-deep);font-size:12px;font-weight:700;padding:8px 18px;border-radius:50px;cursor:pointer;transition:all 0.2s;display:inline-block;}
.law-btn:hover{background:var(--gold-light);}
.lquiz-q{display:none;}
.lquiz-q.on{display:block;}
.lq-opt{padding:14px 18px;border-radius:var(--rs);border:1.5px solid rgba(232,201,122,0.3);cursor:pointer;font-size:14px;font-weight:600;color:var(--mid);transition:all 0.2s;background:white;}
.lq-opt:hover:not(.disabled){background:var(--gold-light);border-color:var(--gold);}
.lq-opt.correct{background:var(--sage-light);border-color:var(--sage);color:var(--sage-deep);}
.lq-opt.wrong{background:#fff0f0;border-color:#ffaaaa;color:#c03030;}
.lq-opt.disabled{cursor:not-allowed;}
.lq-explanation{margin-top:14px;padding:14px 18px;background:var(--gold-light);border-radius:var(--rs);font-size:13px;color:var(--gold-deep);line-height:1.6;display:none;}
.resource-link-card{background:white;border-radius:var(--rs);padding:24px;box-shadow:var(--shadow);text-decoration:none;display:block;border-top:3px solid var(--gold);transition:all 0.3s;}
.resource-link-card:hover{transform:translateY(-4px);}
.resource-link-card h4{font-family:'Cormorant Garamond',serif;font-size:18px;color:var(--gold-deep);margin:10px 0 6px;}
.resource-link-card p{font-size:12px;color:var(--mid);}
.resource-link-card{font-size:28px;}
</style>

<script>
// ═══════════════════════════
// SAFETY
// ═══════════════════════════
function callNumber(n){
  if(confirm('Call '+n+'?\n\nThis will open your phone dialer.')){window.location.href='tel:'+n;}
}
function openSOSModal(){document.querySelector('#page-safety .sub-tab').click();}

function triggerSOS(){
  const btn=document.getElementById('sosMainBtn');
  const status=document.getElementById('sosStatus');
  btn.style.transform='scale(0.95)';
  setTimeout(()=>{btn.style.transform='scale(1)';},150);
  status.style.display='block';
  status.innerHTML='🔴 <strong>SOS Activated!</strong> Alerting trusted contacts and sharing location...';
  status.style.color='#c03030';
  setTimeout(()=>{
    const loc=navigator.geolocation;
    if(loc){loc.getCurrentPosition(p=>{status.innerHTML='✅ Location shared: '+p.coords.latitude.toFixed(4)+', '+p.coords.longitude.toFixed(4)+' — Emergency contacts notified.';status.style.color='#3a8c65';},()=>{status.innerHTML='📍 Vijayawada, AP (approximate) — Emergency contacts notified.';status.style.color='#3a8c65';});}
    else{status.innerHTML='📍 Location service unavailable. Contacts notified.';status.style.color='#a07c20';}
  },2000);
}

const contacts=[];
function addContact(){
  const n=document.getElementById('cName').value.trim();
  const p=document.getElementById('cPhone').value.trim();
  if(!n||!p)return;
  contacts.push({n,p});
  document.getElementById('cName').value='';
  document.getElementById('cPhone').value='';
  renderContacts();
}
function removeContact(i){contacts.splice(i,1);renderContacts();}
function renderContacts(){
  const el=document.getElementById('contactsList');
  if(!contacts.length){el.innerHTML='<p style="font-size:13px;color:var(--light);">No contacts added yet.</p>';return;}
  el.innerHTML=contacts.map((c,i)=>`<div style="display:flex;align-items:center;justify-content:space-between;padding:10px 14px;background:var(--peach-light);border-radius:var(--rs);margin-bottom:8px;">
    <div><strong style="font-size:14px;">${c.n}</strong> <span style="font-size:13px;color:var(--mid);">&nbsp;${c.p}</span></div>
    <div style="display:flex;gap:8px;">
      <button onclick="callNumber('${c.p}')" style="background:linear-gradient(135deg,#c0633a,#e07030);color:white;border:none;padding:6px 12px;border-radius:20px;font-size:11px;font-weight:700;cursor:pointer;">📞 Call</button>
      <button onclick="removeContact(${i})" style="background:none;border:none;color:var(--light);cursor:pointer;font-size:16px;">✕</button>
    </div>
  </div>`).join('');
}

function locateMe(){
  if(typeof maguvaLocateOnce==='function'){maguvaLocateOnce();return;}
  navigator.geolocation?navigator.geolocation.getCurrentPosition(
    p=>{document.getElementById('mapStatus').textContent='📍 Located: '+p.coords.latitude.toFixed(5)+', '+p.coords.longitude.toFixed(5);},
    ()=>{document.getElementById('mapStatus').textContent='📍 Vijayawada, AP — 16.5062° N, 80.6480° E';}
  ):document.getElementById('mapStatus').textContent='📍 Vijayawada, AP (location services not available)';
}
function shareLocation(){
  let q='Vijayawada,AP';
  if(window._maguvaLastCoords&&typeof window._maguvaLastCoords.lat==='number'){
    q=window._maguvaLastCoords.lat+','+window._maguvaLastCoords.lng;
  }
  const msg='🆘 I need help! My location: https://maps.google.com/?q='+encodeURIComponent(q);
  if(navigator.share){navigator.share({text:msg}).catch(()=>{prompt('Copy this message:',msg);});}
  else{prompt('Copy and send this message:',msg);}
}
function toggleGuide(header){
  const body=header.nextElementSibling;
  body.classList.toggle('open');
  header.classList.toggle('open');
}
function toggleCl2(el){
  el.classList.toggle('done');
  const total=document.querySelectorAll('#clList2 .cl-item').length;
  const done=document.querySelectorAll('#clList2 .cl-item.done').length;
  document.getElementById('clScore2').textContent=done+' / '+total+' done';
  document.getElementById('clFill2').style.width=(done/total*100)+'%';
}

// ═══════════════════════════
// MENTAL — CHATBOT (Priya + OpenAI via same-origin /chat when using node server.mjs)
// ═══════════════════════════
const PRIYA_SYSTEM = "You are Priya, a warm, compassionate support companion on MAGUVA, a women's wellness platform. You listen without judgment, reflect feelings, and offer gentle coping ideas. You are not a doctor or therapist — never diagnose or prescribe medication. If the user expresses imminent self-harm, suicide, or immediate danger, tell them to contact local emergency services right away and mention India crisis options when relevant (e.g. 112, Mental Health Helpline 1096, iCall 9152987821). Keep replies concise: usually 2–4 short paragraphs. Use a caring tone; light emoji sparingly is fine. Do not use Markdown headings or bullet markdown; plain sentences are best.";

let priyaConvo = [];

const chatResponses = {
  anxious: ["I hear you 💜 Anxiety can feel so overwhelming. Let's try something together: take one slow breath right now — in for 4 counts, hold for 4, out for 4. How does that feel? I'm right here with you.", "Anxiety is your nervous system trying to protect you — it's not a weakness. Try the 5-4-3-2-1 grounding technique: name 5 things you can see around you right now. It helps bring you back to the present moment 🤍"],
  stressed: ["Stress is real and valid. Your feelings matter 💪 One thing that helps: write down the 3 biggest things stressing you right now. Sometimes seeing them on paper makes them feel more manageable. What's going on?", "When we're stressed, our body needs basic care first — water, food, a short walk. Have you eaten and had water today? Small acts of self-care can shift the nervous system."],
  lonely: ["Loneliness hurts, and I want you to know your feelings are valid 💔 You reaching out right now — even to me — is brave. Would you like to talk about what's behind the loneliness? I'm listening.", "Connection is a fundamental human need, not a luxury. Even small moments of connection matter — a text to an old friend, a smile to a stranger. You are more connected than you might feel right now 🌸"],
  motivation: ["You deserve to feel motivated — let me offer a reminder: You have already survived every hard day you've faced. That's a 100% track record of resilience ✨", "Sometimes motivation follows action, not the other way around. Start with the smallest possible step — even 5 minutes — and momentum builds from there. What's one tiny thing you could do today? 🌱"],
  default: ["Thank you for sharing that with me 💜 I want to understand better — can you tell me more about what's going on?", "I'm here with you and I'm listening 🌸 Your feelings are valid. What would help you most right now — someone to listen, some calming techniques, or a gentle reminder of your strength?", "That sounds really hard, and I'm glad you're talking about it. You don't have to carry this alone. Would you like me to share a coping strategy that might help? 🤍"]
};

function getChatReply(msg) {
  const m = msg.toLowerCase();
  if (m.includes('anxi') || m.includes('panic') || m.includes('worry') || m.includes('fear')) return pick(chatResponses.anxious);
  if (m.includes('stress') || m.includes('overwhelm') || m.includes('too much') || m.includes('burnout')) return pick(chatResponses.stressed);
  if (m.includes('lone') || m.includes('alone') || m.includes('isol')) return pick(chatResponses.lonely);
  if (m.includes('motiv') || m.includes('inspire') || m.includes('stuck') || m.includes('lost')) return pick(chatResponses.motivation);
  if (m.includes('sad') || m.includes('depress') || m.includes('low') || m.includes('cry')) return "It's okay to feel sad sometimes 💔 Your emotions are valid and don't need to be fixed right away. Let yourself feel them. Is there anything that's happened recently that's weighing on you?";
  if (m.includes('angry') || m.includes('frustrat') || m.includes('upset')) return "Anger is valid — it's often a signal that something important to you was crossed 🔥 You're allowed to feel it. Do you want to talk about what happened?";
  if (m.includes('happy') || m.includes('good') || m.includes('great') || m.includes('well')) return "That genuinely makes me happy to hear 🌟 Hold onto this feeling. What's making today good for you?";
  if (m.includes('help') || m.includes('advice')) return "I'm here 💜 Tell me more about what you're going through and I'll do my best to support you.";
  return pick(chatResponses.default);
}
function pick(arr) { return arr[Math.floor(Math.random() * arr.length)]; }

function priyaChatUrl() {
  const u = (typeof window.MAGUVA_CHAT_URL === 'string' && window.MAGUVA_CHAT_URL.trim()) || (localStorage.getItem('maguva_chat_url') || '').trim();
  return u;
}

async function fetchPriyaOpenAI(userText) {
  const url = priyaChatUrl();
  if (!url) throw new Error('no relay url');
  priyaConvo.push({ role: 'user', content: userText });
  const messages = [{ role: 'system', content: PRIYA_SYSTEM }].concat(priyaConvo.slice(-20));
  const res = await fetch(url, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: 'gpt-4o-mini',
      messages,
      max_tokens: 650,
      temperature: 0.75
    })
  });
  const raw = await res.text();
  if (!res.ok) throw new Error(raw || String(res.status));
  let data;
  try { data = JSON.parse(raw); } catch (e) { throw new Error(raw); }
  const text = (data.choices && data.choices[0] && data.choices[0].message && data.choices[0].message.content || '').trim();
  if (!text) throw new Error('empty reply');
  priyaConvo.push({ role: 'assistant', content: text });
  return text;
}

function addChatMsg(text, from) {
  const msgs = document.getElementById('chatMessages');
  const div = document.createElement('div');
  div.className = 'chat-msg ' + from;
  if (from === 'bot') {
    const av = document.createElement('div');
    av.className = 'bot-avatar';
    av.textContent = '🌸';
    const bubble = document.createElement('div');
    bubble.className = 'chat-bubble bot';
    bubble.textContent = text;
    div.appendChild(av);
    div.appendChild(bubble);
  } else {
    const bubble = document.createElement('div');
    bubble.className = 'chat-bubble user';
    bubble.textContent = text;
    div.appendChild(bubble);
  }
  msgs.appendChild(div);
  msgs.scrollTop = msgs.scrollHeight;
}

function scrollChatToEnd() {
  const el = document.getElementById('chatMessages');
  el.scrollTop = el.scrollHeight;
}

async function sendChat() {
  const inp = document.getElementById('chatInput');
  const msg = inp.value.trim();
  if (!msg) return;
  addChatMsg(msg, 'user');
  inp.value = '';
  const qrRow = document.querySelector('#chatMessages .quick-reply') && document.querySelector('#chatMessages .quick-reply').closest('div');
  if (qrRow) qrRow.remove();

  const typing = document.createElement('div');
  typing.className = 'chat-msg bot';
  typing.id = 'typingInd';
  typing.innerHTML = '<div class="bot-avatar">🌸</div><div class="chat-bubble bot" style="font-style:italic;opacity:0.6;">Priya is typing...</div>';
  document.getElementById('chatMessages').appendChild(typing);
  scrollChatToEnd();

  const useRelay = !!priyaChatUrl();
  if (useRelay) {
    try {
      const reply = await fetchPriyaOpenAI(msg);
      typing.remove();
      addChatMsg(reply, 'bot');
    } catch (err) {
      typing.remove();
      addChatMsg("I couldn't reach the chat service just now — I'll stay with you using my built-in words instead:\n\n" + getChatReply(msg), 'bot');
    }
  } else {
    await new Promise(function (r) { setTimeout(r, 450 + Math.random() * 550); });
    typing.remove();
    addChatMsg(getChatReply(msg), 'bot');
  }
}
function sendQuickReply(btn) {
  document.getElementById('chatInput').value = btn.textContent;
  sendChat();
}

// ═══════════════════════════
// MENTAL — MOOD
// ═══════════════════════════
const moodLog=[];
function logMood(btn,emoji,label,msg){
  document.querySelectorAll('.mood-big-btn').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  const res=document.getElementById('moodResponse');
  res.innerHTML=`<div style="font-size:32px;margin-bottom:10px;">${emoji}</div><strong>${label}</strong><br><br>${msg}`;
  res.style.display='block';
  const d=new Date();
  moodLog.push({emoji,label,date:d.toLocaleDateString('en-IN',{weekday:'short',day:'numeric',month:'short'})});
  renderMoodHistory();
}
function renderMoodHistory(){
  const el=document.getElementById('moodHistory');
  el.innerHTML=moodLog.slice(-14).map(m=>`<div style="background:white;border-radius:10px;padding:10px 14px;text-align:center;box-shadow:0 2px 8px rgba(0,0,0,0.05);border:1px solid rgba(201,184,232,0.3);">
    <div style="font-size:24px;">${m.emoji}</div>
    <div style="font-size:10px;color:var(--light);margin-top:4px;">${m.date}</div>
  </div>`).join('')||'<p style="font-size:13px;color:var(--light);">Log your first mood above! 🌸</p>';
}

// CALM
function expandCalm(card){
  const exp=card.querySelector('.cc-expand');
  if(exp.classList.contains('open')){exp.classList.remove('open');}
  else{document.querySelectorAll('.cc-expand').forEach(e=>e.classList.remove('open'));exp.classList.add('open');}
}

let breathPhase=0,breathTimer=null;
function startBreathing(e){
  e.stopPropagation();
  const anim=document.getElementById('breathAnim');
  const phases=[{text:'Inhale',dur:4000,scale:1.5,color:'linear-gradient(135deg,#c9b8e8,#a088d0)'},
    {text:'Hold',dur:4000,scale:1.5,color:'linear-gradient(135deg,#e8dcfc,#c9b8e8)'},
    {text:'Exhale',dur:4000,scale:1,color:'linear-gradient(135deg,var(--lav-light),var(--lav))'},
    {text:'Hold',dur:4000,scale:1,color:'linear-gradient(135deg,#fdf0ff,var(--lav-light))'}];
  function step(){
    const p=phases[breathPhase%4];
    anim.style.background=p.color;
    anim.style.transform='scale('+p.scale+')';
    anim.textContent=p.text;
    breathPhase++;
    breathTimer=setTimeout(step,p.dur);
  }
  if(breathTimer){clearTimeout(breathTimer);breathTimer=null;anim.style.transform='scale(1)';anim.textContent='Start';anim.style.background='linear-gradient(135deg,var(--lav-light),var(--lav))';e.target.textContent='▶ Start';return;}
  e.target.textContent='⏸ Pause';
  step();
}
function clearJournal(e){e.stopPropagation();document.getElementById('journalText').value='';}

// AFFIRMATIONS
const affirmList=[
  {q:'"You are worthy of love, rest, and every good thing that comes your way."',a:'— A reminder just for you 🌸'},
  {q:'"Your strength is not measured by how much you carry alone, but by how bravely you ask for help."',a:'— MAGUVA 💜'},
  {q:'"Healing is not linear. Some days you bloom. Some days you simply survive. Both are enough."',a:'— Gentle truth 🌿'},
  {q:'"You do not have to earn the right to take up space. You already belong."',a:'— For every woman 🤍'},
  {q:'"Your voice matters. Your story matters. You matter."',a:'— Always 🌺'},
  {q:'"You are not behind. You are exactly where you are supposed to be."',a:'— Trust yourself ✨'},
];
let aIdx=0;
function nextAffirm(){aIdx=(aIdx+1)%affirmList.length;document.getElementById('affirmText').textContent=affirmList[aIdx].q;document.getElementById('affirmAuth').textContent=affirmList[aIdx].a;}

// ═══════════════════════════
// CAREER QUIZ
// ═══════════════════════════
const cqData={};
function pickCQ(el,k,v){el.closest('.survey-options').querySelectorAll('.s-opt').forEach(o=>o.classList.remove('picked'));el.classList.add('picked');cqData[k]=[v];}
function toggleCQ(el,k,v){if(!cqData[k])cqData[k]=[];if(el.classList.contains('picked')){el.classList.remove('picked');cqData[k]=cqData[k].filter(x=>x!==v);}else{el.classList.add('picked');cqData[k].push(v);}}
function nextCQ(from){
  document.querySelectorAll('.cquiz-step').forEach(s=>{s.classList.remove('on');});
  if(from===3){
    const c=(cqData.challenge||[])[0]||'';
    const exp=(cqData.experience||[])[0]||'';
    const skills=(cqData.skills||[]).join(', ')||'multiple skills';
    let advice='';
    if(c.includes('Imposter'))advice=`<p>Imposter syndrome is incredibly common among high-achieving women. Here's what helps:</p><ul class="res-list"><li>Keep a "wins journal" — write down 1 achievement every day</li><li>Remember: being asked to do something means someone believed you could</li><li>Find a mentor who has faced the same challenges</li><li>Read "The Confidence Code" by Kay and Shipman</li></ul>`;
    else if(c.includes('balance'))advice=`<p>Work-life balance isn't a destination — it's a daily practice. Try:</p><ul class="res-list"><li>Define your non-negotiables (e.g., dinner with family) and protect them</li><li>Set "offline" hours and actually stick to them</li><li>Block "focus time" in your calendar — treat it like a meeting</li><li>Audit your tasks: what can you delegate or drop?</li></ul>`;
    else if(c.includes('Salary'))advice=`<p>Negotiating is a skill, and women who negotiate earn significantly more over a career. Tips:</p><ul class="res-list"><li>Research market salary (glassdoor.co.in, LinkedIn Salary)</li><li>Anchor high — it's easier to come down than go up</li><li>Practice saying the number out loud until it feels natural</li><li>Negotiate benefits too: WFH days, learning budget, leave</li></ul>`;
    else if(c.includes('bias'))advice=`<p>Gender bias is real and not your fault. Here's how to navigate it:</p><ul class="res-list"><li>Document everything — bias is easier to address with evidence</li><li>Build alliances with supportive colleagues across levels</li><li>Know your company's grievance policy and HR process</li><li>If systemic, consider external reporting to labour authorities</li></ul>`;
    else advice=`<p>Finding your path takes time and that's completely okay. Start here:</p><ul class="res-list"><li>Take a free career assessment (16personalities.com, StrengthsFinder)</li><li>Explore 2–3 fields through LinkedIn, YouTube, or informational interviews</li><li>Look for skills you enjoy using, not just jobs you know</li><li>Small experiments beat big commitments — try a course or project first</li></ul>`;
    document.getElementById('cqResultBox').innerHTML=`<h3 style="font-family:'Cormorant Garamond',serif;font-size:26px;color:var(--sage-deep);margin-bottom:12px;">🎯 Personalised Advice For You</h3><p style="font-size:13px;color:var(--light);margin-bottom:16px;">Based on your challenge: <strong>${c}</strong></p>${advice}<p style="font-size:13px;color:var(--light);margin-top:14px;">Skills to focus on: <strong>${skills}</strong> — check the Skill Tips section for resources!</p>`;
    document.getElementById('cqResult').classList.add('on');return;
  }
  document.getElementById('cq'+(from<1?1:from+1)).classList.add('on');
}
function resetCQ(){document.querySelectorAll('.cquiz-step').forEach(s=>s.classList.remove('on'));document.getElementById('cq1').classList.add('on');document.querySelectorAll('.s-opt').forEach(o=>o.classList.remove('picked'));}

// INSPO QUOTES
const inspoList=[
  {q:'"There is no limit to what we, as women, can accomplish."',a:'— Michelle Obama'},
  {q:'"A woman is like a tea bag — you never know how strong she is until she gets in hot water."',a:'— Eleanor Roosevelt'},
  {q:'"The question isn\'t who\'s going to let me; it\'s who\'s going to stop me."',a:'— Ayn Rand'},
  {q:'"Whatever you do, be different. If you\'re different, you will stand out."',a:'— Anita Roddick'},
  {q:'"Success is not final. Failure is not fatal. It is the courage to continue that counts."',a:'— Kiran Bedi'},
];
let iIdx=0;
function nextInspo(){iIdx=(iIdx+1)%inspoList.length;document.getElementById('insQuote').textContent=inspoList[iIdx].q;document.getElementById('insAuthor').textContent=inspoList[iIdx].a;}

// ═══════════════════════════
// LEGAL QUIZ
// ═══════════════════════════
let lqCurrent=0,lqScore=0,lqAnswered=false;
const lqAnswers=['B','B','B','B','B'];
const lqExps=[
  'Under POSH Act 2013, you must file your complaint within 3 months (90 days) of the last incident. The ICC must complete the inquiry within 90 days.',
  'The Maternity Benefit (Amendment) Act 2017 provides 26 weeks of paid maternity leave for the first two children, and 12 weeks for the third child onwards.',
  'The Hindu Succession (Amendment) Act 2005 gave daughters equal rights in ancestral property from birth — regardless of whether their father was alive when the amendment was passed.',
  '1930 is the National Cyber Crime Helpline. You can also report online at cybercrime.gov.in. Preserve all evidence before reporting.',
  'Under Section 46(4) of CrPC, a woman cannot be arrested after sunset and before sunrise, except in exceptional circumstances with prior approval from a judicial magistrate.'
];
function answerLQ(qIdx,optIdx,correct){
  if(lqAnswered)return;lqAnswered=true;
  const opts=document.querySelectorAll('#lq'+qIdx+'opts .lq-opt');
  opts.forEach(o=>o.classList.add('disabled'));
  const letters=['A','B','C','D'];
  opts[optIdx].classList.add(letters[optIdx]===lqAnswers[qIdx]?'correct':'wrong');
  if(letters[optIdx]!==lqAnswers[qIdx]){opts.forEach((o,i)=>{if(letters[i]===lqAnswers[qIdx])o.classList.add('correct');});}
  const exp=document.getElementById('lqe'+qIdx);
  exp.textContent='💡 '+lqExps[qIdx];exp.style.display='block';
  if(letters[optIdx]===lqAnswers[qIdx]){lqScore++;document.getElementById('lqScore').textContent=lqScore;}
  document.getElementById('lqNext').style.display='block';
}
function nextLQ(){
  lqCurrent++;lqAnswered=false;
  document.querySelectorAll('.lquiz-q').forEach(q=>q.classList.remove('on'));
  document.getElementById('lqNext').style.display='none';
  if(lqCurrent>=5){
    document.getElementById('lqFinal').classList.add('on');
    const pct=lqScore/5;
    document.getElementById('lqEmoji').textContent=pct===1?'🏆':pct>=0.6?'⭐':'📚';
    document.getElementById('lqFinalTitle').textContent=pct===1?'Perfect Score!':pct>=0.6?'Well done!':'Keep Learning!';
    document.getElementById('lqFinalMsg').innerHTML=`You scored <strong>${lqScore}/5</strong>. ${pct===1?'You know your rights brilliantly! Share MAGUVA with a friend 🌸':pct>=0.6?'Good knowledge! Explore the Key Laws section to fill any gaps.':'No worries — explore the Key Laws and Action Steps sections to learn more about your rights.'}`;
  } else {document.getElementById('lq'+lqCurrent).classList.add('on');}
}
function resetLQ(){
  lqCurrent=0;lqScore=0;lqAnswered=false;
  document.querySelectorAll('.lquiz-q').forEach(q=>q.classList.remove('on'));
  document.getElementById('lqScore').textContent='0';
  document.getElementById('lqNext').style.display='none';
  document.getElementById('lq0').classList.add('on');
  document.querySelectorAll('.lq-opt').forEach(o=>{o.classList.remove('correct','wrong','disabled');});
  document.querySelectorAll('.lq-explanation').forEach(e=>e.style.display='none');
}
</script>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
<script>
(function(){
  var apiKey='';
  var map,userMarker,currentMarker,poiLayer=L.layerGroup(),lastPoiBounds=null,currentPOIElements=[],isTracking=false,watchId=null,mapReady=false;

  function initMap(){
    var worldBounds=L.latLngBounds(L.latLng(-90,-180),L.latLng(90,180));
    map=L.map('sm-map',{zoomControl:false,maxBounds:worldBounds,maxBoundsViscosity:1,minZoom:2}).setView([20,0],3);
    L.control.zoom({position:'bottomright'}).addTo(map);
    var cartoDark=L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png',{attribution:'&copy; OpenStreetMap & CARTO',noWrap:true}).addTo(map);
    var satellite=L.layerGroup([
      L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',{noWrap:true}),
      L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_only_labels/{z}/{x}/{y}{r}.png',{noWrap:true})
    ]);
    L.control.layers({'Standard':cartoDark,'Satellite':satellite},{'Safety Infrastructure':poiLayer},{position:'topright'}).addTo(map);
    poiLayer.addTo(map);
    map.on('click',function(e){disableTracking();handleMapClick(e);});
    map.on('moveend',handleMoveEnd);
  }

  function toggleLiveTracking(){
    var mapBtn=document.getElementById('sm-btn-my-location');
    var sidebarBtn=document.getElementById('sm-btn-sidebar-location');
    var badge=document.getElementById('sm-live-badge');
    if(isTracking){disableTracking();return;}
    if(!navigator.geolocation){showError('Geolocation not supported by device.');return;}
    function startTrack(){
      isTracking=true;
      mapBtn.classList.add('active-tracking');
      sidebarBtn.classList.add('sm-tracking-on');
      sidebarBtn.innerHTML='<i class="fa-solid fa-circle-stop"></i><span> Stop Tracking</span>';
      badge.classList.remove('sm-hidden');
      showLoadingState();
      document.getElementById('sm-location-title').textContent='My Device Location';
      document.getElementById('sm-location-subtitle').textContent='Real-time Tracking Active';
      if(currentMarker)map.removeLayer(currentMarker);
      watchId=navigator.geolocation.watchPosition(handleLivePosition,handleLocationError,{enableHighAccuracy:true,timeout:10000,maximumAge:0});
    }
    if(navigator.permissions&&navigator.permissions.query){
      navigator.permissions.query({name:'geolocation'}).then(function(result){
        if(result.state==='denied'){showError('Location access denied in browser settings.');return;}
        startTrack();
      }).catch(function(){startTrack();});
    }else{startTrack();}
  }

  function disableTracking(){
    if(watchId!==null){navigator.geolocation.clearWatch(watchId);watchId=null;}
    isTracking=false;
    var mapBtn=document.getElementById('sm-btn-my-location');
    var sidebarBtn=document.getElementById('sm-btn-sidebar-location');
    mapBtn.classList.remove('active-tracking');
    sidebarBtn.classList.remove('sm-tracking-on');
    sidebarBtn.innerHTML='<i class="fa-solid fa-location-arrow sm-loc-ico"></i><span>Find My Location</span>';
    document.getElementById('sm-live-badge').classList.add('sm-hidden');
  }

  function handleLivePosition(position){
    if(!position||!position.coords)return;
    var latitude=position.coords.latitude,longitude=position.coords.longitude;
    var latlng=[latitude,longitude];
    window._maguvaLastCoords={lat:latitude,lng:longitude};
    if(!userMarker){
      userMarker=L.marker(latlng,{icon:L.divIcon({className:'user-location-icon',html:'<div class="user-location-pulse"></div>',iconSize:[20,20],iconAnchor:[10,10]}),zIndexOffset:1000}).addTo(map);
    }else{userMarker.setLatLng(latlng);}
    if(isTracking){
      map.setView(latlng,15);
      updateNearestInfrastructure(latlng[0],latlng[1]);
    }
  }

  function handleLocationError(err){
    console.error('Geolocation Error:',err);
    disableTracking();
    var message='Unknown location error.';
    if(err&&typeof err==='object'){
      switch(err.code){
        case 1:message='Location access denied. Please enable GPS.';break;
        case 2:message='Position unavailable. Check signal.';break;
        case 3:message='Location request timed out.';break;
        default:message=err.message||message;
      }
    }
    showError(message);
  }

  async function handleMoveEnd(){
    var zoom=map.getZoom();
    if(zoom<5){poiLayer.clearLayers();lastPoiBounds=null;return;}
    var bounds=map.getBounds();
    if(lastPoiBounds&&lastPoiBounds.contains(bounds)){
      var c=map.getCenter();
      updateNearestInfrastructure(c.lat,c.lng);
      return;
    }
    fetchPOIs(bounds);
  }

  async function fetchPOIs(bounds){
    var status=document.getElementById('sm-poi-status');
    status.classList.remove('sm-hidden');
    var s=bounds.getSouth(),w=bounds.getWest(),n=bounds.getNorth(),e=bounds.getEast();
    var query='[out:json][timeout:60];(node["amenity"="police"]('+s+','+w+','+n+','+e+');way["amenity"="police"]('+s+','+w+','+n+','+e+');node["amenity"~"hospital|clinic"]('+s+','+w+','+n+','+e+');way["amenity"~"hospital|clinic"]('+s+','+w+','+n+','+e+'););out center 400;';
    var endpoints=['https://overpass-api.de/api/interpreter','https://lz4.overpass-api.de/api/interpreter'];
    var success=false;
    for(var i=0;i<endpoints.length;i++){
      if(success)break;
      try{
        var response=await fetch(endpoints[i]+'?data='+encodeURIComponent(query));
        if(!response.ok)continue;
        var data=await response.json();
        if(data&&data.elements){
          currentPOIElements=data.elements;
          renderPOIs(data.elements);
          var center=map.getCenter();
          updateNearestInfrastructure(center.lat,center.lng);
          lastPoiBounds=bounds.pad(0.2);
          success=true;
        }
      }catch(err){console.warn('Overpass Error:',err);}
    }
    status.classList.add('sm-hidden');
  }

  function renderPOIs(elements){
    poiLayer.clearLayers();
    var zoom=map.getZoom();
    elements.forEach(function(el){
      var lat=el.lat||(el.center&&el.center.lat);
      var lon=el.lon||(el.center&&el.center.lon);
      if(!lat||!lon)return;
      var isPolice=el.tags.amenity==='police';
      var color=isPolice?'#ef4444':'#3b82f6';
      var icon=isPolice?'fa-shield-halved':'fa-hospital-user';
      var name=el.tags.name||(isPolice?'Police Station':'Hospital');
      var baseSize=zoom<8?12:(zoom<12?16:20);
      L.marker([lat,lon],{icon:L.divIcon({className:'poi-wrapper',html:'<div class="poi-container"><i class="fa-solid '+icon+'" style="color:'+color+';font-size:'+baseSize+'px;filter:drop-shadow(0 0 2px black)"></i><span class="poi-label">'+name.replace(/</g,'')+'</span></div>',iconSize:[120,50],iconAnchor:[60,10]})}).addTo(poiLayer).bindPopup('<div style="padding:8px"><div style="font-weight:700;color:'+color+'">'+name.replace(/</g,'')+'</div><div style="font-size:10px;color:#9ca3af;text-transform:uppercase">'+(el.tags.amenity||'')+'</div></div>');
    });
  }

  function calculateDistance(lat1,lon1,lat2,lon2){
    var R=6371,dLat=(lat2-lat1)*Math.PI/180,dLon=(lon2-lon1)*Math.PI/180;
    var a=Math.sin(dLat/2)*Math.sin(dLat/2)+Math.cos(lat1*Math.PI/180)*Math.cos(lat2*Math.PI/180)*Math.sin(dLon/2)*Math.sin(dLon/2);
    return R*(2*Math.atan2(Math.sqrt(a),Math.sqrt(1-a)));
  }

  function updateNearestInfrastructure(lat,lng){
    if(currentPOIElements.length===0)return;
    var nearestHosp=null,nearestPolice=null,minDistHosp=Infinity,minDistPolice=Infinity;
    currentPOIElements.forEach(function(el){
      var elLat=el.lat||(el.center&&el.center.lat),elLon=el.lon||(el.center&&el.center.lon);
      if(!elLat||!elLon)return;
      var dist=calculateDistance(lat,lng,elLat,elLon);
      var isPolice=el.tags.amenity==='police';
      if(isPolice){if(dist<minDistPolice){minDistPolice=dist;nearestPolice=Object.assign({},el,{lat:elLat,lon:elLon});}}
      else{if(dist<minDistHosp){minDistHosp=dist;nearestHosp=Object.assign({},el,{lat:elLat,lon:elLon});}}
    });
    function updateCard(id,el,dist){
      var nameEl=document.getElementById('sm-'+id+'-name');
      var distEl=document.getElementById('sm-'+id+'-dist');
      var btn=document.getElementById('sm-goto-'+id);
      if(!nameEl)return;
      if(el){
        nameEl.textContent=el.tags.name||(id==='hosp'?'Unknown Hospital':'Unknown Station');
        distEl.textContent=dist.toFixed(2)+' km away';
        btn.classList.remove('sm-hidden');
        btn.onclick=function(e){e.stopPropagation();disableTracking();map.setView([el.lat,el.lon],16);};
      }else{
        nameEl.textContent='No facility detected nearby';
        distEl.textContent='Scan higher density areas';
        btn.classList.add('sm-hidden');
      }
    }
    updateCard('hosp',nearestHosp,minDistHosp);
    updateCard('police',nearestPolice,minDistPolice);
  }

  async function searchLocation(query){
    if(!query.trim())return;
    hideError();
    disableTracking();
    showLoadingState();
    try{
      var res=await fetch('https://nominatim.openstreetmap.org/search?format=json&q='+encodeURIComponent(query)+'&limit=1');
      var data=await res.json();
      if(data&&data[0])processLocationResult(data[0]);
      else showError('Location not found.');
    }catch(err){console.error(err);showError('Search failed.');}
  }

  function processLocationResult(data,flat,flon){
    var lat=parseFloat(data.lat!=null?data.lat:flat);
    var lon=parseFloat(data.lon!=null?data.lon:flon);
    var name=data.name||'Selected Area';
    if(data.address)name=data.address.city||data.address.town||data.address.country||name;
    window._maguvaLastCoords={lat:lat,lng:lon};
    if(currentMarker)map.removeLayer(currentMarker);
    currentMarker=L.circleMarker([lat,lon],{radius:10,color:'#ef4444',fillOpacity:0.8,className:'pulse-red'}).addTo(map);
    map.setView([lat,lon],14);
    document.getElementById('sm-location-title').textContent=name;
    document.getElementById('sm-location-subtitle').textContent='Search Target';
    fetchAIInsights(name,data.display_name||'');
    updateNearestInfrastructure(lat,lon);
  }

  async function handleMapClick(e){
    var lat=e.latlng.lat,lon=e.latlng.lng;
    disableTracking();
    showLoadingState();
    try{
      var res=await fetch('https://nominatim.openstreetmap.org/reverse?format=json&lat='+lat+'&lon='+lon+'&zoom=12');
      var data=await res.json();
      processLocationResult(data,lat,lon);
    }catch(err){processLocationResult({name:'Unmapped Point'},lat,lon);}
  }

  function setupEventListeners(){
    document.getElementById('sm-search-form').addEventListener('submit',function(e){
      e.preventDefault();
      searchLocation(document.getElementById('sm-search-input').value);
    });
    document.getElementById('sm-btn-my-location').addEventListener('click',toggleLiveTracking);
    document.getElementById('sm-btn-sidebar-location').addEventListener('click',toggleLiveTracking);
  }

  function showLoadingState(){
    document.getElementById('sm-placeholder-panel').classList.add('sm-hidden');
    document.getElementById('sm-info-panel').classList.remove('sm-hidden');
    document.getElementById('sm-ai-loading').classList.remove('sm-hidden');
    document.getElementById('sm-ai-content').classList.add('sm-hidden');
  }

  async function fetchAIInsights(loc,addr){
    if(!apiKey){
      document.getElementById('sm-ai-loading').classList.add('sm-hidden');
      var el=document.getElementById('sm-ai-content');
      el.innerHTML='<p style="margin:0">Add a Gemini API key in the map script for AI area summaries, or explore the map for live OSM data.</p>';
      el.classList.remove('sm-hidden');
      return;
    }
    var model='gemini-2.5-flash-preview-09-2025';
    var url='https://generativelanguage.googleapis.com/v1beta/models/'+model+':generateContent?key='+apiKey;
    var prompt='Summarize the safety and infrastructure landscape for '+loc+' ('+addr+') in 2026. Be concise. HTML format only.';
    async function tryFetch(retries,delay){
      try{
        var response=await fetch(url,{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({contents:[{parts:[{text:prompt}]}],systemInstruction:{parts:[{text:'Urban infrastructure analyst. Clean HTML, no markdown blocks.'}]}})});
        if(!response.ok){
          if(response.status===429&&retries>0){await new Promise(function(r){setTimeout(r,delay);});return tryFetch(retries-1,delay*2);}
          throw new Error('API '+response.status);
        }
        var result=await response.json();
        var text=(result.candidates&&result.candidates[0]&&result.candidates[0].content&&result.candidates[0].content.parts&&result.candidates[0].content.parts[0].text)||'';
        text=text.replace(/```html/gi,'').replace(/```/g,'').trim();
        document.getElementById('sm-ai-loading').classList.add('sm-hidden');
        var ac=document.getElementById('sm-ai-content');
        ac.innerHTML=text;
        ac.classList.remove('sm-hidden');
      }catch(e){
        document.getElementById('sm-ai-loading').classList.add('sm-hidden');
        var ac=document.getElementById('sm-ai-content');
        ac.innerText='Intelligence summary currently unavailable.';
        ac.classList.remove('sm-hidden');
      }
    }
    await tryFetch(5,1000);
  }

  function showError(m){
    var err=document.getElementById('sm-error-message');
    err.textContent=m;
    err.classList.remove('sm-hidden');
    setTimeout(function(){err.classList.add('sm-hidden');},5000);
  }
  function hideError(){document.getElementById('sm-error-message').classList.add('sm-hidden');}

  window.ensureMaguvaSafetyMap=function(){
    if(!mapReady){
      initMap();
      setupEventListeners();
      mapReady=true;
    }
    setTimeout(function(){if(map)map.invalidateSize();},120);
  };

  window.maguvaLocateOnce=function(){
    window.ensureMaguvaSafetyMap();
    if(!navigator.geolocation){
      document.getElementById('mapStatus').textContent='📍 Location services not available';
      return;
    }
    navigator.geolocation.getCurrentPosition(
      function(p){
        var la=p.coords.latitude,lo=p.coords.longitude;
        window._maguvaLastCoords={lat:la,lng:lo};
        document.getElementById('mapStatus').textContent='📍 Located: '+la.toFixed(5)+', '+lo.toFixed(5);
        if(!map)return;
        if(!userMarker){
          userMarker=L.marker([la,lo],{icon:L.divIcon({className:'user-location-icon',html:'<div class="user-location-pulse"></div>',iconSize:[20,20],iconAnchor:[10,10]}),zIndexOffset:1000}).addTo(map);
        }else{userMarker.setLatLng([la,lo]);}
        map.setView([la,lo],15);
        showLoadingState();
        document.getElementById('sm-location-title').textContent='My Location';
        document.getElementById('sm-location-subtitle').textContent='One-time fix';
        updateNearestInfrastructure(la,lo);
      },
      function(){
        document.getElementById('mapStatus').textContent='📍 Vijayawada, AP — 16.5062° N, 80.6480° E';
      }
    );
  };
})();
</script>

</body>
</html>
