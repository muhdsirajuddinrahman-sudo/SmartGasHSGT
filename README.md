<!DOCTYPE html>
<html lang="en">
<head>
<base target="_top">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>SmartGasHSGT</title>
<script>
/* apply the saved (or device) theme before the page paints, so there is no flash */
(function(){var t='dark';try{t=localStorage.getItem('sg-theme')||(matchMedia('(prefers-color-scheme: light)').matches?'light':'dark')}catch(e){}document.documentElement.dataset.theme=t==='light'?'light':'dark'})();
</script>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<script src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>

<style>
:root {
 color-scheme: dark;
 --bg-base: #0f172a;
 --tx: #f8fafc;
 --mu: #94a3b8;
 --nmu: #64748b;
 --line: rgba(255, 255, 255, 0.1);
 --card: rgba(30, 41, 59, 0.45);
 --soft: rgba(30, 41, 59, 0.25);
 --glass-edge: rgba(255, 255, 255, 0.15);
 --glass-sh: rgba(0, 0, 0, 0.3);
 --glass-ring: rgba(15, 23, 42, 0.8);
 --acc: #3b82f6;
 --on-acc: #ffffff;
 --ok: #10b981;
 --warn: #eab308;
 --bad: #ef4444;
 --red: #ef4444;
 --blob1: #3b82f6;
 --blob2: #8b5cf6;
 --blob3: #06b6d4;
 --c-unit: #93c5fd;
 --c-user: #c4b5fd;
}
html[data-theme="light"] {
 color-scheme: light;
 --bg-base: #f1f5f9;
 --tx: #0f172a;
 --mu: #64748b;
 --nmu: #94a3b8;
 --line: rgba(15, 23, 42, 0.1);
 --card: rgba(255, 255, 255, 0.65);
 --soft: rgba(255, 255, 255, 0.4);
 --glass-edge: rgba(255, 255, 255, 0.8);
 --glass-sh: rgba(30, 41, 59, 0.1);
 --glass-ring: rgba(241, 245, 249, 0.9);
 --acc: #4f46e5;
 --blob1: #93c5fd;
 --blob2: #c4b5fd;
 --blob3: #67e8f9;
 --c-unit: #1e40af;
 --c-user: #4338ca;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
html { min-height: 100%; }
body { font-family: 'Plus Jakarta Sans', system-ui, sans-serif; color: var(--tx); background: transparent; transition: color 0.4s ease; font-size: 14px; line-height: 1.45; }

.bg-blobs { position: fixed; inset: 0; z-index: -1; overflow: hidden; background: var(--bg-base); transition: background-color 0.4s ease; }
.blob { position: absolute; border-radius: 50%; filter: blur(80px); opacity: 0.6; animation: float 20s infinite alternate ease-in-out; }
.blob:nth-child(1) { top: -10%; left: -10%; width: 50vw; height: 50vw; background: var(--blob1); }
.blob:nth-child(2) { bottom: -20%; right: -10%; width: 60vw; height: 60vw; background: var(--blob2); animation-delay: -5s; }
.blob:nth-child(3) { top: 40%; left: 50%; width: 40vw; height: 40vw; background: var(--blob3); animation-delay: -10s; }
@keyframes float { 0% { transform: translate(0,0) scale(1); } 50% { transform: translate(5%,10%) scale(1.1); } 100% { transform: translate(-5%,-5%) scale(0.95); } }
@media (prefers-reduced-motion: reduce) { .blob { animation: none; } }

button, input, select, textarea { font: inherit; color: inherit; border: none; background: transparent; }
option { background: var(--bg-base); color: var(--tx); }
:focus-visible { outline: 3px solid var(--acc); outline-offset: 2px; }
[hidden] { display: none !important; }
.wrap { max-width: 620px; margin: 0 auto; padding: 18px 16px 130px; }

header { display: flex; justify-content: space-between; align-items: center; gap: 10px; margin-bottom: 16px; }
.brand { font-weight: 800; font-size: 19px; letter-spacing: -0.3px; }
.sync { font-size: 11px; color: var(--mu); margin-top: 2px; }
.hdr-l { min-width: 0; flex: 1; padding-right: 8px; }
.hdr-r { display: flex; flex-direction: row; align-items: center; gap: 12px; flex: none; }
.icon-btn { display: flex; align-items: center; justify-content: center; width: 32px; height: 32px; padding: 0; }
.ghost { background: var(--soft); border: 1px solid var(--line); color: var(--tx); border-radius: 99px; padding: 7px 14px; font-weight: 600; cursor: pointer; }

.dropdown-wrap { position: relative; }
.dropdown-menu { position: absolute; top: 100%; right: 0; margin-top: 8px; background: var(--card); border: 1px solid var(--glass-edge); border-radius: 12px; box-shadow: 0 8px 32px var(--glass-sh); backdrop-filter: blur(16px); -webkit-backdrop-filter: blur(16px); padding: 6px; display: flex; flex-direction: column; gap: 4px; z-index: 50; min-width: 140px; }
.dropdown-menu[hidden] { display: none !important; }
.dropdown-item { display: flex; align-items: center; padding: 12px 16px; border-radius: 8px; font-weight: 600; font-size: 14px; text-align: left; cursor: pointer; background: transparent; border: none; color: var(--tx); transition: background 0.2s; }
.dropdown-item:hover { background: var(--soft); }

.theme-sw { position: relative; width: 60px; height: 32px; border: 1px solid var(--line); border-radius: 99px; background: var(--soft); cursor: pointer; padding: 0; flex: none; }
.theme-sw span { position: absolute; top: 0; bottom: 0; z-index: 1; display: flex; align-items: center; font-size: 14px; line-height: 1; pointer-events: none; color: var(--tx); }
.theme-sw .ts-sun { left: 9px; } .theme-sw .ts-moon { right: 9px; }
.theme-sw::after { content: ''; position: absolute; left: 2px; top: 2px; width: 26px; height: 26px; border-radius: 50%; background: var(--acc); transform: translate3d(0,0,0); transition: transform .25s cubic-bezier(.4,0,.2,1); }
.theme-sw[aria-checked=true]::after { transform: translate3d(28px,0,0); }
.theme-sw[aria-checked=true] .ts-moon, .theme-sw[aria-checked=false] .ts-sun { color: var(--on-acc); }

/* ----- glass navigation ----- */
.tabs { position: fixed; left: 14px; right: 14px; bottom: calc(12px + env(safe-area-inset-bottom)); max-width: 440px; margin: 0 auto; height: 64px; z-index: 35; display: flex; align-items: stretch; border-radius: 28px; background: var(--card); backdrop-filter: blur(24px) saturate(1.5); -webkit-backdrop-filter: blur(24px) saturate(1.5); border: 1px solid var(--glass-edge); box-shadow: 0 12px 32px var(--glass-sh); transition: opacity .2s ease, transform .2s ease; }
.tabs button { position: relative; z-index: 1; flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; gap: 4px; padding: 12px 2px 0; color: var(--nmu); font-weight: 700; font-size: 10.5px; cursor: pointer; border-radius: 22px; min-height: 44px; }
.tabs button[aria-current=page] { color: var(--tx); }
.ni { display: flex; transition: transform .35s cubic-bezier(.3,1.3,.5,1); }
.ni svg { width: 24px; height: 24px; } .nl { line-height: 1.1; }
.tabs button[aria-current=page] .ni { transform: translate3d(0,-18px,0); color: var(--on-acc); }
.nav-ind { position: absolute; z-index: 0; top: -22px; left: 0; width: 56px; height: 56px; border-radius: 50%; background: var(--acc); box-shadow: 0 0 0 6px var(--glass-ring), 0 8px 18px var(--glass-sh); transform: translate3d(var(--nx,0px),0,0); transition: transform .35s cubic-bezier(.3,1.3,.5,1); opacity: 0; pointer-events: none; }
.tabs[data-on] .nav-ind { opacity: 1; }
.tabs.still .nav-ind, .tabs.still .ni { transition: none; }
body.cam .tabs { -webkit-backdrop-filter: none; backdrop-filter: none; }

.card, .stat, .sheet, .who { background: var(--card); backdrop-filter: blur(16px); -webkit-backdrop-filter: blur(16px); border: 1px solid var(--glass-edge); box-shadow: 0 8px 32px var(--glass-sh); }
.stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 14px; }
.stat { border-radius: 18px; padding: 14px; }
.stat b { display: block; font-size: 28px; font-weight: 800; letter-spacing: -1px; }
.stat span { color: var(--mu); font-size: 12px; }
.card { border-radius: 24px; padding: 18px; margin-bottom: 14px; }
h2 { font-size: 15px; font-weight: 800; margin-bottom: 12px; }

.f { display: block; font-weight: 700; font-size: 12px; color: var(--mu); margin-bottom: 12px; }
.f input, .f select, .search input { display: block; width: 100%; height: 46px; margin-top: 5px; padding: 0 14px; border: 1px solid var(--line); border-radius: 14px; background: rgba(0,0,0,0.1); font-weight: 600; color: var(--tx); }
html[data-theme="light"] .f input, html[data-theme="light"] .f select, html[data-theme="light"] .search input { background: rgba(255,255,255,0.4); }
.f input::placeholder, .search input::placeholder, .note::placeholder { color: var(--mu); }
.f input:focus, .f select:focus, .search input:focus { border-color: var(--acc); }
.row2 { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }

.modes { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
.modes button { --mc: #94a3b8; position: relative; overflow: hidden; isolation: isolate; border: 1px solid var(--glass-edge); background: var(--soft); color: var(--tx); border-radius: 18px; padding: 16px 10px; font-weight: 800; cursor: pointer; line-height: 1.3; }
.modes button::after { content: ''; position: absolute; inset: 0; border-radius: inherit; background: linear-gradient(135deg, rgba(255,255,255,0.15), transparent 60%); pointer-events: none; }
.modes button:hover { background: color-mix(in srgb, var(--mc) 20%, var(--soft)); }
.modes small { display: block; font-weight: 500; color: var(--nmu); margin-top: 2px; }
.modes button[data-mode=ASSIGN] { --mc: #3b82f6; }
.modes button[data-mode=RETURN] { --mc: #10b981; }
.modes button[data-mode=SEND_REFILL] { --mc: #8b5cf6; }
.modes button[data-mode=RECEIVE_REFILL] { --mc: #06b6d4; }
.modes button[aria-pressed=true] { background: var(--mc); border-color: rgba(255,255,255,0.45); color: #fff; box-shadow: 0 4px 15px color-mix(in srgb, var(--mc) 40%, transparent); }
.modes button[aria-pressed=true] small { color: rgba(255,255,255,0.85); }

.search { position: relative; } .search input { margin: 0; }
.res { margin-top: 8px; border-radius: 14px; overflow: hidden; border: 1px solid var(--line); background: rgba(0,0,0,0.1); }
html[data-theme="light"] .res { background: rgba(255,255,255,0.3); }
.res button { display: flex; justify-content: space-between; align-items: center; width: 100%; padding: 15px 14px; border-top: 1px solid var(--line); cursor: pointer; text-align: left; }
.res button:first-child { border-top: 0; } .res button:hover { background: var(--soft); }
.mu { color: var(--mu); font-size: 12px; }
.cam { display: flex; gap: 8px; margin-top: 10px; flex-wrap: wrap; }
.btn { border: 1px solid var(--line); background: var(--soft); color: var(--tx); border-radius: 14px; padding: 11px 16px; font-weight: 700; cursor: pointer; }
.btn:hover { background: rgba(255,255,255,0.1); }
.btn:disabled { opacity: .5; cursor: not-allowed; }
.big { width: 100%; height: 54px; border-radius: 18px; background: var(--acc); color: var(--on-acc); font-weight: 800; font-size: 15px; cursor: pointer; box-shadow: 0 4px 15px rgba(59,130,246,0.4); }
.big:disabled { opacity: 0.5; cursor: not-allowed; box-shadow: none; }

#reader { margin-top: 12px; border-radius: 16px; overflow: hidden; }
.msg { color: var(--bad); font-size: 13px; font-weight: 600; margin-top: 8px; }
.item { display: flex; justify-content: space-between; align-items: center; gap: 8px; padding: 11px 0; border-top: 1px solid var(--line); }
.item:first-of-type { border-top: 0; } .item small { display: block; color: var(--mu); } .item small.w { color: var(--warn); font-weight: 600; }
.item>div:first-child { flex: 1; min-width: 0; }
.x { background: rgba(239,68,68,0.1); font-size: 20px; line-height: 1; color: var(--bad); cursor: pointer; padding: 4px 10px; border-radius: 50%; }
.tag { display: inline-block; font-size: 11px; font-weight: 700; padding: 2px 10px; border-radius: 99px; background: var(--soft); color: var(--tx); }
.tag.d { background: var(--tx); color: var(--bg-base); }
.tag.dm { background: var(--bad); color: #fff; }
.empty { text-align: center; color: var(--mu); padding: 26px 10px; }
.empty b { display: block; color: var(--tx); font-size: 15px; margin-bottom: 4px; } .empty .btn { margin-top: 12px; }
.sk { height: 46px; border-radius: 12px; margin-bottom: 8px; background: var(--soft); }
.live { position: relative; display: inline-block; width: 9px; height: 9px; border-radius: 50%; background: var(--ok); margin-right: 6px; }

#toasts { position: fixed; top: 14px; left: 50%; transform: translateX(-50%); z-index: 100; display: flex; flex-direction: column; gap: 8px; width: calc(100% - 28px); max-width: 420px; }
.t { background: var(--tx); color: var(--bg-base); padding: 12px 16px; border-radius: 14px; font-weight: 600; border-left: 4px solid var(--ok); box-shadow: 0 8px 25px rgba(0,0,0,0.2); }
.t.err { border-color: var(--bad); } .t.warn { border-color: var(--warn); }

.who { display: flex; justify-content: space-between; align-items: center; gap: 12px; border-radius: 18px; padding: 12px 16px; margin-bottom: 12px; }
.who-l { display: flex; align-items: center; gap: 12px; min-width: 0; }
.av { flex: none; display: flex; align-items: center; justify-content: center; width: 40px; height: 40px; border-radius: 50%; background: linear-gradient(135deg, var(--acc), var(--blob2)); color: var(--on-acc); font-weight: 800; font-size: 17px; }
.who b { display: block; font-size: 17px; color: var(--c-unit); }
.who #whoN { display: block; font-size: 13px; font-weight: 700; color: var(--c-user); }

.res { max-height: 300px; overflow: auto; margin: 12px 0 0; }
.res button.on { background: rgba(59,130,246,0.15); border-left: 3px solid var(--acc); padding-left: 11px; }
.res .r { display: flex; justify-content: space-between; align-items: center; gap: 8px; padding: 11px 14px; border-top: 1px solid var(--line); }
.res .r:first-child { border-top: 0; } .res.tall { max-height: 460px; margin-top: 0; }
.slim { color: var(--mu); text-align: center; font-size: 13px; margin-bottom: 12px; } .slim b { font-weight: 800; color: var(--tx); }
.rh { display: flex; justify-content: space-between; align-items: center; margin-top: 12px; }
.rh:empty { display: none; } .rh+.res { margin-top: 8px; }
.ck { width: 22px; height: 22px; border-radius: 7px; border: 2px solid var(--mu); flex: none; margin-right: 12px; position: relative; }
.res button.on .ck { background: var(--acc); border-color: var(--acc); }
.res button.on .ck::after { content: ""; position: absolute; left: 6px; top: 2px; width: 5px; height: 11px; border: solid var(--on-acc); border-width: 0 2.5px 2.5px 0; transform: rotate(45deg); }

#bar { position: fixed; left: 0; right: 0; bottom: 0; z-index: 30; padding: 14px 16px calc(14px + env(safe-area-inset-bottom)); background: linear-gradient(transparent, var(--bg-base) 60%); }
#bar>* { max-width: 588px; margin-left: auto; margin-right: auto; display: block; }
#bar .msg { color: #fff; background: var(--bad); border-radius: 10px; padding: 8px 12px; margin: 0 auto 8px; } #bar .msg:empty { display: none; }

.ov { position: fixed; inset: 0; background: rgba(0,0,0,0.6); backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px); z-index: 50; display: flex; align-items: center; justify-content: center; padding: 16px; }
.sheet { border-radius: 22px; padding: 22px; max-width: 380px; width: 100%; }
.sheet h2 { font-size: 18px; } .sheet .mu { font-size: 13px; }
.onlyD { display: none; }

.auth { background: var(--card); backdrop-filter: blur(20px); border: 1px solid var(--glass-edge); border-radius: 28px; overflow: hidden; padding: 32px 24px; max-width: 460px; margin: 40px auto 0; box-shadow: 0 12px 40px var(--glass-sh); }
.auth-brand { display: flex; align-items: center; gap: 10px; margin-bottom: 24px; justify-content: center; }
.auth-brand b { font-size: 22px; font-weight: 800; letter-spacing: -0.2px; }
.logo { display: flex; align-items: center; justify-content: center; width: 44px; height: 44px; border-radius: 14px; background: linear-gradient(135deg, var(--acc), var(--blob2)); color: var(--on-acc); }
.auth-h { font-size: 20px; font-weight: 800; letter-spacing: -0.5px; margin-bottom: 4px; text-align: center; }
.auth-sub { margin-bottom: 24px; text-align: center; }

.gas { display: block; font-size: 11px; font-weight: 500; color: var(--mu); line-height: 1.3; }
.seg { display: flex; gap: 6px; margin-top: 8px; }
.seg button { flex: 1; border: 1px solid var(--line); background: rgba(0,0,0,0.1); border-radius: 10px; padding: 8px; font-weight: 700; font-size: 13px; cursor: pointer; }
html[data-theme="light"] .seg button { background: rgba(255,255,255,0.4); }
.seg button[aria-pressed=true] { background: var(--ok); color: #fff; border-color: transparent; }
.seg button[data-cond=D][aria-pressed=true] { background: var(--bad); }
/* light mode: these must beat the white light-mode button background above */
html[data-theme="light"] .seg button[aria-pressed=true] { background: var(--ok); color: #fff; border-color: transparent; }
html[data-theme="light"] .seg button[data-cond=D][aria-pressed=true] { background: var(--bad); color: #fff; }
.note { display: block; width: 100%; height: 42px; margin-top: 8px; padding: 0 12px; border: 1px solid var(--line); border-radius: 12px; background: rgba(0,0,0,0.1); font-weight: 600; color: var(--tx); }
.note.bad { border-color: var(--bad); box-shadow: 0 0 0 3px rgba(239,68,68,0.2); }

/* on/off switch */
.tg { position: relative; width: 46px; height: 26px; flex: none; display: inline-block; }
.tg input { position: absolute; inset: 0; width: 100%; height: 100%; opacity: 0; margin: 0; cursor: pointer; z-index: 1; }
.tg i { position: absolute; inset: 0; background: var(--nmu); border-radius: 99px; transition: .3s; }
.tg i::after { content: ''; position: absolute; left: 3px; top: 3px; width: 20px; height: 20px; border-radius: 50%; background: #fff; transition: .3s; box-shadow: 0 2px 4px rgba(0,0,0,.2); }
.tg input:checked + i { background: var(--ok); }
.tg input:checked + i::after { transform: translateX(20px); }

/* ----- Handover memo (folder) ----- */
.fld-wrap { position: relative; background: #101218; border-radius: 20px; margin-bottom: 24px; padding: 60px 16px 0; background-image: radial-gradient(rgba(255,255,255,0.06) 2px, transparent 2px); background-size: 16px 16px; background-position: center top; box-shadow: inset 0 0 0 1px rgba(255,255,255,0.05), 0 12px 32px rgba(0,0,0,0.3); }
.fld-papers { position: relative; z-index: 1; height: 140px; overflow: hidden; transition: height .4s ease; }
.fld-wrap.open .fld-papers { height: 300px; overflow-y: auto; padding-bottom: 40px; }
.fld-paper { background: #fff; border-radius: 12px; padding: 16px; color: #1e293b; box-shadow: 0 -4px 16px rgba(0,0,0,0.25); position: absolute; left: 10px; right: 10px; transition: all .5s cubic-bezier(.34,1.56,.64,1); border: 1px solid rgba(0,0,0,0.05); }
.fld-wrap:not(.open) .fld-paper:nth-last-child(1) { top: 60px; transform: scale(1) rotate(-1.5deg); z-index: 3; }
.fld-wrap:not(.open) .fld-paper:nth-last-child(2) { top: 35px; transform: scale(.94) rotate(1.5deg); z-index: 2; opacity: .9; }
.fld-wrap:not(.open) .fld-paper:nth-last-child(3) { top: 10px; transform: scale(.88) rotate(-1deg); z-index: 1; opacity: .7; }
.fld-wrap:not(.open) .fld-paper:nth-last-child(n+4) { top: 0; opacity: 0; pointer-events: none; }
.fld-wrap.open .fld-paper { position: relative; top: 0 !important; left: 0; right: 0; transform: none !important; margin-bottom: 12px; z-index: auto; opacity: 1 !important; box-shadow: 0 4px 12px rgba(0,0,0,0.08); }
.fld-front { position: relative; z-index: 10; background: #1a1c23; border-radius: 20px; margin: -20px -16px 0 -16px; padding: 24px; box-shadow: 0 -10px 24px rgba(0,0,0,.4), inset 0 1px 0 rgba(255,255,255,.05); display: flex; justify-content: space-between; align-items: flex-end; border-top-left-radius: 0; }
.fld-front::before { content: ''; position: absolute; top: -24px; left: 0; width: 150px; height: 24px; background: #1a1c23; border-radius: 12px 12px 0 0; }
.fld-info h2 { margin: 0 0 6px 0; font-size: 15px; color: #fff; }
.fld-info p { margin: 0; font-size: 12px; color: #64748b; font-family: monospace; }
.fld-count { font-size: 32px; font-weight: 800; color: #fff; display: flex; align-items: baseline; gap: 8px; margin-top: 20px; }
.fld-count span { font-size: 12px; font-weight: 600; color: #64748b; }
.fld-orb { width: 52px; height: 52px; border-radius: 50%; background: radial-gradient(circle at 35% 35%, #93c5fd, #2563eb); box-shadow: 0 0 24px rgba(59,130,246,.5), inset 0 2px 6px rgba(255,255,255,.5); display: flex; justify-content: center; align-items: center; cursor: pointer; color: #fff; transition: all .4s cubic-bezier(.34,1.56,.64,1); }
.fld-wrap.open .fld-orb { background: radial-gradient(circle at 35% 35%, #fca5a5, #dc2626); box-shadow: 0 0 24px rgba(220,38,38,.5), inset 0 2px 6px rgba(255,255,255,.5); transform: rotate(135deg); }
.fld-input { overflow: hidden; max-height: 0; opacity: 0; transition: all .4s ease; background: #1a1c23; padding: 0 24px; margin: 0 -16px; border-radius: 0 0 20px 20px; }
.fld-wrap.open .fld-input { max-height: 200px; opacity: 1; padding: 16px 24px 24px; }
.fld-paper-h { font-size: 11px; font-weight: 800; color: #64748b; margin-bottom: 8px; text-transform: uppercase; letter-spacing: .5px; }
.fld-paper-t { font-size: 14px; font-weight: 600; color: #0f172a; line-height: 1.5; white-space: pre-wrap; word-break: break-word; }
.fld-paper.dn { opacity: .6; }
.fld-paper.dn .fld-paper-t { text-decoration: line-through; }
.fld-foot { display: flex; justify-content: space-between; align-items: center; margin-top: 12px; font-size: 11px; color: #64748b; }
.fld-foot button { padding: 0; font-weight: 800; cursor: pointer; }

/* ----- dashboard ----- */
.d-top { display: flex; justify-content: space-between; align-items: flex-start; gap: 12px; flex-wrap: wrap; margin-bottom: 20px; }
.d-eye { font-size: 11px; font-weight: 800; letter-spacing: .08em; text-transform: uppercase; color: var(--mu); }
.d-ttl { font-size: 26px; font-weight: 800; letter-spacing: -0.5px; line-height: 1.15; margin: 2px 0; }
#dSheet { display: inline-block; text-decoration: none; padding: 9px 14px; background: var(--soft); color: var(--tx); border-radius: 12px; border: 1px solid var(--line); font-weight: 600; }
.d-seg { display: inline-flex; gap: 2px; flex-wrap: wrap; background: var(--soft); border-radius: 14px; padding: 3px; margin-bottom: 14px; border: 1px solid var(--line); }
.d-chip { background: none; color: var(--mu); border-radius: 11px; padding: 7px 12px; font-weight: 700; font-size: 12px; cursor: pointer; }
.d-chip[aria-pressed=true] { background: var(--acc); color: var(--on-acc); }
.d-kpis { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 14px; }
.d-kpi { --c: #94a3b8; border-radius: 18px; padding: 16px; background: var(--card); border: 1px solid var(--line); border-top: 3px solid var(--c); box-shadow: 0 4px 15px var(--glass-sh); }
.d-kpi b { display: block; font-size: 26px; font-weight: 800; letter-spacing: -1px; line-height: 1.1; }
.d-kpi span { display: block; font-size: 11px; font-weight: 600; color: var(--mu); margin-top: 2px; }
.d-grid { display: grid; gap: 14px; }
.d-cv { position: relative; height: 230px; }
.d-nd { position: absolute; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; }
.d-mv { display: grid; grid-template-columns: repeat(5, 1fr); gap: 6px; text-align: center; }
.d-mv b { display: block; font-size: 22px; font-weight: 800; line-height: 1.2; }
.d-mv span { font-size: 11px; color: var(--mu); }
.d-row { display: flex; justify-content: space-between; align-items: center; gap: 8px; padding: 12px 0; border-top: 1px solid var(--line); }
.d-row[data-trk] { cursor: pointer; }
.d-row:first-child { border-top: 0; }
.d-l { display: block; font-size: 11px; }
.d-b { display: inline-block; color: #fff; font-size: 11px; font-weight: 700; padding: 2px 9px; border-radius: 99px; margin-bottom: 2px; }
.d-h { font-size: 11px; font-weight: 800; color: var(--mu); margin: 14px 0 6px; text-transform: uppercase; letter-spacing: .5px; }
.d-h:first-child { margin-top: 0; }
.d-pw { display: grid; gap: 12px; }
.d-pcv { height: 280px; }
.d-det { box-sizing: border-box; min-height: 260px; max-height: 470px; overflow: auto; background: rgba(0,0,0,0.1); border-radius: 16px; padding: 16px; border-left: 5px solid var(--dc, var(--line)); }
html[data-theme="light"] .d-det { background: rgba(255,255,255,0.4); }
.d-dh { font-size: 14px; margin-bottom: 6px; }
.d-big { display: flex; align-items: baseline; gap: 8px; margin: 6px 0 8px; }
.d-big b { font-size: 34px; font-weight: 800; letter-spacing: -1px; line-height: 1; }
.d-big span { font-size: 12px; color: var(--mu); }
.d-tw { overflow-x: auto; }
.d-tbl { width: 100%; border-collapse: collapse; font-size: 12px; }
.d-tbl th { text-align: left; font-size: 10px; text-transform: uppercase; color: var(--mu); padding: 8px 6px; border-bottom: 1px solid var(--line); }
.d-tbl td { padding: 10px 6px; border-bottom: 1px solid var(--line); }
.d-st { display: inline-block; font-size: 10px; font-weight: 800; padding: 2px 8px; border-radius: 99px; color: #fff; }
.d-st.out { background: var(--bad); } .d-st.low { background: var(--warn); color: #000; } .d-st.ok { background: var(--ok); }
.d-gd { display: inline-block; width: 9px; height: 9px; border-radius: 50%; margin-right: 6px; }
.d-chipg { display: flex; flex-wrap: wrap; gap: 6px; margin: 6px 0; }
.d-chipg span { background: var(--soft); border: 1px solid var(--line); border-radius: 99px; padding: 3px 10px; font-size: 11px; font-weight: 700; }
.d-batch { background: var(--soft); border: 1px solid var(--line); border-left: 5px solid #06b6d4; border-radius: 14px; padding: 10px 14px; margin-bottom: 10px; }
.d-batch.late { border-left-color: var(--bad); }
.d-bhd { display: flex; justify-content: space-between; gap: 10px; font-size: 13px; }
.d-tl { display: flex; gap: 10px; padding: 8px 0; border-top: 1px solid var(--line); }
.d-tl>i { flex: none; width: 10px; height: 10px; border-radius: 50%; margin-top: 5px; }

/* infographic pie */
.pie-box { min-width: 0; }
#pie { display: block; width: 100%; height: auto; max-height: 340px; overflow: visible; -webkit-tap-highlight-color: transparent; }
#pie text { font-family: 'Plus Jakarta Sans', system-ui, sans-serif; }
#pie .seg { cursor: pointer; transition: transform .28s cubic-bezier(.34,1.56,.64,1); }
#pie .seg.on { transform: translate(var(--dx), var(--dy)); }
#pie .lbl { cursor: pointer; }
#pie .ld { stroke: var(--nmu); stroke-width: 1; opacity: .8; fill: none; }
#pie .ul { stroke: var(--mu); stroke-width: 1.6; }
#pie .pl-t { font-size: 15px; font-weight: 600; letter-spacing: 3px; fill: var(--mu); }
#pie .lbl.on .pl-t { fill: var(--tx); font-weight: 800; }
#pie .lbl.on .ul { stroke: var(--c); stroke-width: 3; }
#pie .lbl.on .ld { stroke: var(--c); opacity: 1; stroke-width: 1.4; }
#pie .pdot { fill: var(--tx); }
#pie .pc-n { font-size: 9.5px; font-weight: 700; fill: var(--mu); }
#pie .pc-b { font-size: 34px; font-weight: 800; letter-spacing: -1px; fill: var(--tx); }
#pie .pc-s { font-size: 10.5px; font-weight: 600; fill: var(--mu); }
.pie-lg { display: flex; flex-wrap: wrap; gap: 6px; justify-content: center; margin-top: 8px; }
.pie-lg button { display: inline-flex; align-items: center; gap: 6px; padding: 5px 10px; border-radius: 99px; border: 1px solid var(--line); background: var(--soft); font-size: 11px; font-weight: 700; cursor: pointer; }
.pie-lg button[aria-pressed=true] { border-color: var(--tx); }
.pie-lg i { width: 9px; height: 9px; border-radius: 50%; flex: none; }

/* damage reason, photo, popups, supplier habit */
select.note { appearance: auto; }
.ph-row { display: flex; align-items: center; gap: 8px; margin-top: 8px; flex-wrap: wrap; }
.ph-btn { padding: 8px 12px; font-size: 13px; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; }
.ph-th { width: 46px; height: 46px; border-radius: 10px; object-fit: cover; border: 1px solid var(--line); }
.ph-link { margin-left: 6px; padding: 2px 9px; border-radius: 99px; border: 1px solid var(--line); background: var(--soft); font-size: 11px; font-weight: 700; cursor: pointer; }
.d-tbl tr.lv-head td { background: var(--soft); font-weight: 700; }
.d-tbl td.ind { padding-left: 22px; }
.hab { background: var(--soft); border: 1px solid var(--line); border-radius: 14px; padding: 12px 14px; margin: 10px 0; }
#pvB img { max-width: 100%; max-height: 70vh; border-radius: 12px; }

/* expiry date */
.exp { display: block; font-size: 11px; font-weight: 700; color: var(--mu); line-height: 1.3; }
.exp.none { font-weight: 500; opacity: .85; }
.exp.soon { color: var(--warn); }
.exp.bad { color: var(--bad); }
.exp-in { display: block; margin-top: 8px; font-size: 12px; font-weight: 700; color: var(--mu); }
.exp-in input { margin-top: 4px; }

@media(max-width:899px) {
 .cam .btn { flex: 1; padding: 14px; }
 .sync { white-space: nowrap; overflow: hidden; text-overflow: ellipsis; width: 100%; display: block; font-size: 10px; }
 #stats { display: none; }
 #bar { bottom: calc(84px + env(safe-area-inset-bottom)); padding: 8px 16px; }
 body[data-view=scan] .wrap { padding-bottom: 200px; }
 body[data-view=inv] .wrap, body[data-view=dash] .wrap, body[data-view=alerts] .wrap, body[data-view=settings] .wrap { padding-bottom: 120px; }
 body.kb .tabs { opacity: 0; pointer-events: none; transform: translate3d(0,24px,0); }
}
@media(min-width:900px) {
 .wrap { max-width: 1080px; padding: 28px 28px 60px; }
 .tabs { position: sticky; top: 12px; left: auto; right: auto; bottom: auto; width: max-content; max-width: none; height: auto; margin: 0 auto 16px; padding: 4px; border-radius: 99px; overflow: hidden; }
 .tabs button { flex: none; flex-direction: row; justify-content: center; align-items: center; gap: 8px; padding: 10px 20px; font-size: 13px; border-radius: 99px; min-height: 40px; white-space: nowrap; max-width: 180px; transition: max-width .4s cubic-bezier(.34,1.56,.64,1), opacity .3s, padding .4s; }
 .nav-ind { display: none !important; }
 .tabs button[aria-current=page] { color: var(--on-acc); }
 .tabs button[aria-current=page]::before { content: ''; position: absolute; inset: 0; background: var(--acc); border-radius: 99px; z-index: -1; box-shadow: 0 4px 12px rgba(0,0,0,.15); }
 .tabs button[aria-current=page] .ni { transform: none; }
 .ni svg { width: 18px; height: 18px; }
 #v-gate { max-width: 860px; margin: 36px auto 0; }
 #v-scan { display: grid; grid-template-columns: minmax(0,1fr) 400px; grid-template-areas: "stats stats" "who who" "modes sel" "add sel" "add bar"; grid-template-rows: auto auto auto auto 1fr; column-gap: 24px; align-items: start; }
 #stats { grid-area: stats; text-align: left; } #v-scan .who { grid-area: who; } #v-scan .modes { grid-area: modes; max-width: 520px; }
 #cAdd { grid-area: add; } #cSel { grid-area: sel; position: sticky; top: 80px; }
 #bar { grid-area: bar; position: static; background: none; padding: 0; } #bar>* { max-width: none; }
 .res { max-height: 430px; } .res.tall { max-height: 560px; }
 .cam { display: none; } .onlyP { display: none; } .onlyD { display: inline; }
 .d-grid { grid-template-columns: 1fr 1fr; column-gap: 16px; }
 .d-wide { grid-column: 1/-1; }
 .d-kpis { grid-template-columns: repeat(6, 1fr); }
 .d-pw { grid-template-columns: 1fr 1fr; align-items: start; }
}
/* The "island" only collapses where there is a real mouse hover. Touch screens always show every tab. */
@media(min-width:900px) and (hover:hover) {
 .tabs button:not([aria-current=page]) { max-width: 0; opacity: 0; padding-left: 0; padding-right: 0; pointer-events: none; }
 .tabs:hover button:not([aria-current=page]), .tabs:focus-within button:not([aria-current=page]) { max-width: 180px; opacity: 1; padding-left: 20px; padding-right: 20px; pointer-events: auto; }
}
@keyframes cardin { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: none; } }
section .card, .auth { animation: cardin .4s cubic-bezier(.2,.8,.2,1) both; }
.big:active, .btn:active, .modes button:active { transform: scale(0.98); }
</style>
</head>
<body>

<div class="bg-blobs" aria-hidden="true"><div class="blob"></div><div class="blob"></div><div class="blob"></div></div>

<!-- cylinder id from a QR link (?id=...), filled in by Code.gs -->
<div id="deep" data-id="<?= cylinderId ?>" hidden></div>

<div class="wrap">
<header>
 <div class="hdr-l">
  <div class="brand">SmartGasHSGT</div>
  <div id="sync" class="sync"></div>
 </div>
 <div class="hdr-r">
  <button class="theme-sw" id="themeSw" role="switch" aria-checked="false" aria-label="Dark mode" title="Light / dark"><span class="ts-sun">&#9728;&#xFE0E;</span><span class="ts-moon">&#9790;</span></button>
  <div class="dropdown-wrap">
   <button class="icon-btn" id="menuBtn" aria-label="Menu" style="background:transparent; border:none; color:var(--tx); cursor:pointer;">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor"><circle cx="12" cy="5" r="2.2"/><circle cx="12" cy="12" r="2.2"/><circle cx="12" cy="19" r="2.2"/></svg>
   </button>
   <div class="dropdown-menu" id="menuDropdown" hidden>
    <button class="dropdown-item" id="refresh">Refresh</button>
    <button class="dropdown-item" id="hdrLogout" hidden style="color:var(--bad)">Logout</button>
   </div>
  </div>
 </div>
</header>

<nav class="tabs" id="tabs" aria-label="Main" hidden>
 <i class="nav-ind" aria-hidden="true"></i>
 <button data-go="scan"><span class="ni"><svg aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 8V5a1 1 0 0 1 1-1h3M16 4h3a1 1 0 0 1 1 1v3M20 16v3a1 1 0 0 1-1 1h-3M8 20H5a1 1 0 0 1-1-1v-3M4 12h16"/></svg></span><span class="nl">Scan</span></button>
 <button data-go="inv"><span class="ni"><svg aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 8l-9-5-9 5 9 5 9-5zM3 8v8l9 5 9-5V8M12 13v8"/></svg></span><span class="nl">Inventory</span></button>
 <button id="tabDash" data-go="dash" hidden><span class="ni"><svg aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="12" width="4" height="8" rx="1"/><rect x="10" y="4" width="4" height="16" rx="1"/><rect x="17" y="9" width="4" height="11" rx="1"/></svg></span><span class="nl">Dashboard</span></button>
 <button id="tabAlerts" data-go="alerts" hidden><span class="ni"><svg aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9M13.73 21a2 2 0 0 1-3.46 0"/></svg></span><span class="nl">Alerts</span></button>
 <button id="tabSettings" data-go="settings" hidden><span class="ni"><svg aria-hidden="true" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/></svg></span><span class="nl">Settings</span></button>
</nav>

<!-- LOGIN -->
<section id="v-gate">
 <div class="auth">
  <div class="auth-brand"><span class="logo"><svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9.5 3h5M10.5 3v2M13.5 3v2"/><rect x="7" y="5" width="10" height="16" rx="4"/><path d="M7 11h10"/></svg></span><b>SmartGasHSGT</b></div>
  <h2 class="auth-h">Choose your unit</h2>
  <p class="mu auth-sub">Sign in to scan and track gas cylinders.</p>
  <label class="f">Unit<select id="unit"></select></label>
  <label class="f" id="pWrap" hidden>Staff PIN<input id="pin" type="password" autocomplete="off" placeholder="Ask your supervisor"></label>
  <label class="f">Your name<input id="staff" autocomplete="name" placeholder="e.g. Alex"></label>
  <div class="msg" id="gerr" role="alert"></div>
  <button class="big" id="enter" style="margin-top:16px">Continue</button>
 </div>
</section>

<!-- SCAN -->
<section id="v-scan" hidden>
 <div class="slim" id="stats"></div>
 <div class="who"><div class="who-l"><span class="av" id="whoA" aria-hidden="true"></span><div><b id="whoU"></b><span class="mu" id="whoN"></span></div></div><button class="btn" id="change">Change</button></div>
 <div class="modes" style="margin-bottom:14px">
  <button data-mode="ASSIGN" aria-pressed="true"><svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" style="display:block;margin:0 auto 6px"><path d="M5 12h14M13 6l6 6-6 6"/></svg>Take out<small>from logistics</small></button>
  <button data-mode="RETURN" aria-pressed="false"><svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" style="display:block;margin:0 auto 6px"><path d="M19 12H5M11 6l-6 6 6 6"/></svg>Return<small>to logistics</small></button>
  <button data-mode="SEND_REFILL" aria-pressed="false" hidden><svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" style="display:block;margin:0 auto 6px"><path d="M12 19V5M5 12l7-7 7 7"/></svg><span id="mSendT">Out for refill (GPT)</span><small id="mSendS">empty or damaged, to the supplier</small></button>
  <button data-mode="RECEIVE_REFILL" aria-pressed="false" hidden><svg aria-hidden="true" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round" style="display:block;margin:0 auto 6px"><path d="M12 5v14M5 12l7 7 7-7"/></svg><span id="mRecvT">Return from (GPT)</span><small id="mRecvS">refilled, back to logistics</small></button>
 </div>
 <div class="card" id="cAdd">
  <label class="f" id="tWrap" hidden>For which unit?<select id="tUnit"></select></label>
  <h2 id="addTitle">Add cylinders</h2>
  <div class="search"><input id="q" type="search" autocomplete="off" placeholder="Scan, or search cylinder ID / gas name" aria-label="Scan or search by cylinder ID or gas name"></div>
  <div class="rh" id="resHead"></div><div class="res" id="res"></div>
  <div class="mu" style="margin-top:12px;text-align:center"><span class="onlyP">Scan with the camera or a handheld scanner, or type to search.</span><span class="onlyD">Scan with a handheld scanner, or type to search.</span></div>
  <div class="cam">
   <button class="btn" id="camStart" style="flex:1">Live camera</button>
   <label class="btn" style="flex:1;text-align:center">Snap photo<input type="file" id="camFile" accept="image/*" capture="environment" hidden></label>
   <button class="btn" id="camStop" style="flex:1;color:var(--bad)" hidden>Stop camera</button>
  </div>
  <div id="reader" hidden></div><div class="msg" id="camMsg" role="alert"></div>
 </div>
 <div class="card" id="cSel"><h2 id="qTitle">Selected cylinders</h2>
  <div id="expAllBox" class="hab" hidden>
   <b style="font-size:13px">Same expiry date for all</b>
   <div style="display:flex;gap:8px;margin-top:8px;align-items:center"><input type="date" id="expAll" class="note" style="margin-top:0;flex:1" aria-label="Expiry date for all selected cylinders"><button class="btn" id="expAllBtn" style="flex:none">Apply to all</button></div>
   <div class="mu" style="margin-top:6px">Each cylinder can still be changed below.</div>
  </div>
  <div id="qList"></div></div>
 <div id="bar" hidden><div class="msg" id="err" role="alert"></div><button class="big" id="go">Confirm</button></div>
</section>

<!-- INVENTORY -->
<section id="v-inv" hidden>
 <div class="who"><div><b id="iH" style="font-size:18px">Inventory</b><span class="mu" id="iS" style="display:block;margin-top:2px">Cylinders taken out by unit</span></div></div>
 <div class="stats" id="istats"></div>
 <div class="card" id="iCard"><label class="f" style="margin:0">Filter by unit<select id="iu"></select></label></div>
 <div class="card"><h2 id="iTitle">Cylinders taken</h2><div id="iList"></div></div>
</section>

<!-- DASHBOARD (logistics only) -->
<section id="v-dash" hidden>
 <div class="d-top">
  <div><div class="d-eye">SmartGasHSGT</div><div class="d-ttl">Dashboard</div><span class="mu" id="dSub">Loading…</span></div>
  <a class="btn" id="dSheet" target="_blank" rel="noopener" href="#">Open Google Sheet</a>
 </div>
 <div class="d-seg" id="dChips">
  <button class="d-chip" data-dr="all" aria-pressed="false">All</button>
  <button class="d-chip" data-dr="week" aria-pressed="false">Last 7 days</button>
  <button class="d-chip" data-dr="month" aria-pressed="true">Last 30 days</button>
  <button class="d-chip" data-dr="3m" aria-pressed="false">Last 3 months</button>
 </div>
 <div class="d-kpis" id="dKpis"><div class="sk"></div><div class="sk"></div><div class="sk"></div></div>
 <div class="card" style="margin-bottom:12px"><h2 id="dMvT">Movements</h2><div class="d-mv" id="dMv"></div></div>
 <div class="d-grid">
  <div class="card d-wide"><h2>Cylinder status</h2>
   <div class="d-pw">
    <div class="pie-box" id="pieWrap">
     <svg id="pie" viewBox="0 0 460 340" role="img" aria-label="Cylinder status chart"></svg>
     <div class="empty" id="pieEmpty" hidden><b>No data yet</b>No cylinders found.</div>
     <div class="pie-lg" id="pieLegend"></div>
    </div>
    <div class="d-det" id="dDet"><div class="d-dh" id="dDetH">Details</div><div id="dDetB"></div></div>
   </div>
  </div>
  <div class="card d-wide"><h2>Daily movements</h2><div class="d-cv"><canvas id="cTrend"></canvas><div class="empty d-nd" hidden><b>No data yet</b>Nothing to show for this period.</div></div></div>
  <div class="card"><h2>Needs attention</h2><div id="dAttn"></div></div>
  <div class="card"><h2>Recent activity</h2><div id="dRecent"></div></div>
  <div class="card d-wide"><h2>Track a cylinder</h2><div class="search"><input id="dTrackQ" type="search" autocomplete="off" placeholder="Type or scan a cylinder ID" aria-label="Track a cylinder"></div><div id="dTrack" style="margin-top:6px"></div></div>
 </div>
</section>

<!-- ALERTS (logistics only) -->
<section id="v-alerts" hidden>
 <div class="who"><div><b style="font-size:18px">Alerts</b><span class="mu" style="display:block;margin-top:2px">Needs attention</span></div></div>

 <div class="fld-wrap" id="memoFolder">
  <div class="fld-papers" id="mList"></div>
  <div class="fld-front">
   <div class="fld-info"><h2>Handover memo</h2><p>Notes for the next shift</p><div class="fld-count"><span id="mCountNum" style="font-size:32px;color:#fff;font-weight:800">0</span><span>open</span></div></div>
   <button class="fld-orb" id="memoOrb" aria-label="Open or close memo folder"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg></button>
  </div>
  <div class="fld-input">
   <div style="display:flex;gap:8px;align-items:stretch">
    <textarea id="mTxt" rows="2" maxlength="500" placeholder="Write a message for the next shift…" aria-label="Handover note" style="flex:1;min-width:0;min-height:60px;resize:none;padding:12px;border-radius:12px;background:rgba(0,0,0,.3);font-weight:600;color:#fff;outline:none"></textarea>
    <button id="mPost" aria-label="Send" style="flex:none;width:52px;border-radius:12px;background:radial-gradient(circle at 30% 30%,#60a5fa,#2563eb);color:#fff;font-size:18px;cursor:pointer">&#10148;</button>
   </div>
  </div>
 </div>

 <div class="card">
  <h2 style="color:var(--bad)">Damaged cylinders</h2>
  <div class="mu" style="margin-bottom:12px">Waiting at logistics to be sent to the supplier.</div>
  <div style="display:flex;justify-content:space-between;align-items:center;gap:10px;background:var(--soft);padding:14px;border-radius:16px;border:1px solid var(--line);margin-bottom:12px">
   <div style="min-width:0"><b style="display:block;font-size:13px">Email notifications</b><span id="aMailStatus" class="mu">Checking…</span></div>
   <div style="display:flex;gap:10px;align-items:center">
    <button id="dMailNow" class="btn" style="padding:8px 12px;font-size:12px;border-radius:10px">Send now</button>
    <label class="tg" title="Email new damaged cylinders automatically (checked hourly)"><input type="checkbox" id="dMailTg" aria-label="Automatic email alerts"><i></i></label>
   </div>
  </div>
  <div id="aDmgList" class="res tall" style="margin-top:0"></div>
 </div>
 <div class="card">
  <h2 style="color:var(--warn)">Out for refill (GPT)</h2>
  <div class="mu" style="margin-bottom:12px">Currently at the supplier depot.</div>
  <div id="aRefList" class="res tall" style="margin-top:0"></div>
 </div>
</section>

<!-- SETTINGS (logistics only) -->
<section id="v-settings" hidden>
 <div class="who"><div><b style="font-size:18px">Settings</b><span class="mu" style="display:block;margin-top:2px">Add cylinders, dispose cylinders, start tracking</span></div></div>

 <div class="card">
  <h2>Add cylinder</h2>
  <label class="f">Cylinder ID<input id="newCylId" placeholder="e.g. GAS-999" autocomplete="off"></label>
  <label class="f">Gas name<select id="newGasSel"></select></label>
  <label class="f" id="newGasWrap" hidden>New gas name<input id="newGas" placeholder="Type the new gas name" autocomplete="off" maxlength="60"></label>
  <div class="row2">
   <label class="f">Size (m&sup3;)<input id="newSize" type="text" inputmode="decimal" placeholder="e.g. 6" autocomplete="off"></label>
   <label class="f">Valve type<input id="newValve" placeholder="e.g. Pin index" autocomplete="off" maxlength="40"></label>
  </div>
  <label class="f">Expiry date (optional)<input id="newExp" type="date"></label>
  <button class="btn" id="addCylBtn" style="width:100%;background:var(--ok);color:#fff;border:none;height:46px">Add cylinder</button>
  <div class="mu" style="margin-top:8px">The gas list comes from column C of the QRcodes sheet. No spaces or ? &amp; # in the ID. The details are saved in QRcodes (C, D, E). New cylinders start at logistics, full.</div>
 </div>

 <div class="card">
  <h2>Disposal</h2>
  <label class="f">Cylinder ID<input id="disId" list="disIds" placeholder="Type or pick a cylinder at logistics" autocomplete="off"></label>
  <datalist id="disIds"></datalist>
  <label class="f">Reason
   <select id="disReason">
    <option value="">Choose a reason…</option>
    <option>Damaged beyond repair</option>
    <option>Failed inspection / expired</option>
    <option>Condemned by supplier</option>
    <option>Lost / missing</option>
    <option>Other</option>
   </select>
  </label>
  <label class="f">Note (needed if the reason is Other)<input id="disNote" maxlength="200" placeholder="Optional details" autocomplete="off"></label>
  <button class="btn" id="disBtn" style="width:100%;background:var(--bad);color:#fff;border:none;height:46px">Dispose cylinder</button>
  <div class="mu" style="margin-top:8px">The cylinder leaves stock, but nothing is erased: it is saved in the &ldquo;Disposal&rdquo; sheet and its history stays. Only cylinders at logistics can be disposed.</div>
 </div>

 <div class="card">
  <h2>Cylinders already at units</h2>
  <div class="mu" id="utText" style="margin-bottom:12px">Checking…</div>
  <label class="f">Start counting from<input id="utDate" type="date"></label>
  <button class="btn" id="utBtn" style="width:100%;height:46px" disabled>Start tracking these cylinders</button>
  <div class="mu" style="margin-top:8px">Optional. This adds an &ldquo;opening balance&rdquo; record with the date you choose, so the overdue timer starts from that day. Nothing else changes.</div>
 </div>
</section>
</div>

<div class="ov" id="ask" hidden>
 <div class="sheet" role="dialog" aria-modal="true" aria-labelledby="askT">
  <h2 id="askT"></h2>
  <p class="mu" id="askP" style="margin:8px 0 24px;font-size:14px;line-height:1.5"></p>
  <div class="row2"><button class="btn" id="no">Cancel</button><button class="big" id="yes" style="height:46px">Yes, confirm</button></div>
 </div>
</div>
<div class="ov" id="blk" hidden>
 <div class="sheet" role="alertdialog" aria-modal="true" aria-labelledby="blkT">
  <h2 id="blkT"></h2>
  <p class="mu" id="blkP" style="margin:8px 0 22px;font-size:15px;line-height:1.5;color:var(--tx);font-weight:600"></p>
  <button class="big" id="blkOk" style="height:46px">OK</button>
 </div>
</div>
<div class="ov" id="pv" hidden>
 <div class="sheet" role="dialog" aria-modal="true" aria-labelledby="pvT" style="max-width:520px">
  <h2 id="pvT">Damage photo</h2>
  <div id="pvB" style="min-height:90px;text-align:center"></div>
  <button class="btn" id="pvClose" style="width:100%;margin-top:12px">Close</button>
 </div>
</div>
<div id="toasts" aria-live="polite"></div>

<script>
/* Preview-only fake backend. Inside Apps Script, google.script.run is real and this block is skipped. */
if (typeof google === 'undefined') {
 const mock = { withSuccessHandler: function(cb){ this._s = cb; return this; }, withFailureHandler: function(cb){ this._f = cb; return this; } };
 window.google = { script: { run: new Proxy(mock, { get: function(t, p) {
   if (p in t) return t[p];
   return function() { setTimeout(function(){ if (t._f) t._f(new Error('Preview mode: no backend.')); }, 250); return t; };
 } }) } };
}

/* ================= basics ================= */
const $=id=>document.getElementById(id);
const esc=s=>String(s==null?'':s).replace(/[&<>"']/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
const DESK=matchMedia('(min-width:900px)');
const S={cyl:[],units:[],queue:[],shown:[],ret:{},mode:'ASSIGN',unit:'',role:'unit',pick:'',pin:'',target:'',staff:'',busy:false,loaded:false,view:'gate',ver:'',seq:0,lastFull:0,barcodeMap:{}};
let lastCode='',lastAt=0,qr=null,camOn=false,audio=null;
const same=(a,b)=>String(a||'').trim().toLowerCase()===String(b||'').trim().toLowerCase();
const byId=id=>S.cyl.find(c=>c.id.toLowerCase()===String(id).toLowerCase());
const U=()=>S.role==='logi'?S.target:S.unit;
/* serial / barcode normaliser: same rule as normSerial_ in Code.gs (trim, no spaces, no Code 39 asterisks, lower case) */
const nk=s=>String(s==null?'':s).trim().replace(/^\*|\*$/g,'').replace(/\s+/g,'').toLowerCase();
/* supplier wording: change SUP in one place */
const SUP='GPT';
const L_OUT='Out for refill ('+SUP+')', L_BACK='Return from ('+SUP+')';
const LBL=n=>n==='Out for refill'?L_OUT:n;   // dashboard status names -> what is shown on screen
$('mSendT').textContent=L_OUT;$('mSendS').textContent='empty or damaged, to the supplier';
$('mRecvT').textContent=L_BACK;$('mRecvS').textContent='refilled, back to logistics';
const phrase=k=>{const n=k+' cylinder'+(k>1?'s':'');return S.mode==='ASSIGN'?'Take out '+n:S.mode==='RETURN'?'Return '+n:S.mode==='SEND_REFILL'?L_OUT+': '+n:L_BACK+': '+n};
const DEF_REASONS=['Valve leaking','Valve damaged / stuck','Thread or neck damaged','Body dented / bent','Rust / corrosion','Cap or collar missing / damaged','Colour code / label unreadable','Test date expired','Other'];
const phBtn=id=>id?`<button class="ph-link" data-photo="${esc(id)}" aria-label="View damage photo">&#128247; Photo</button>`:'';
const isEmpty=c=>/^empty/i.test(c.condition);
const isDmg=c=>/^(damaged|complaint)/i.test(c.condition);   // (older "Complaint" rows count as damaged)
const dmgNote=c=>String(c.condition).replace(/^(damaged|complaint)\s*[:\-]?\s*/i,'');
const gasTxt=c=>(c&&c.gas?c.gas:[]).filter(Boolean).join(' · ');
const gasEl=c=>{const g=gasTxt(c);return g?`<small class="gas">${esc(g)}</small>`:''};
const hit=(c,q)=>!q||c.id.toLowerCase().includes(q)||(c.gas||[]).some(g=>String(g).toLowerCase().includes(q));
const R=id=>S.ret[id]||(S.ret[id]={type:'E',reason:'',note:'',photo:'',expiry:''});   // type: E = empty, D = damaged
const condTxt=c=>isDmg(c)?'Damaged':c.condition;
const condTag=c=>isDmg(c)?'<span class="tag dm">Damaged</span>':`<span class="tag">${esc(c.condition)}</span>`;
const setHtml=(el,h)=>{if(el._h!==h){el._h=h;el.innerHTML=h}};
const noteInput=id=>[...document.querySelectorAll('#qList input')].find(i=>i.dataset.nid===id);
const empty=(h,p,b)=>`<div class="empty"><b>${h}</b>${p||''}${b||''}</div>`;
const skel=()=>'<div class="sk"></div>'.repeat(3);
const p2=n=>String(n).padStart(2,'0');
const nowStr=()=>{const d=new Date();return d.getFullYear()+'-'+p2(d.getMonth()+1)+'-'+p2(d.getDate())+' '+p2(d.getHours())+':'+p2(d.getMinutes())+':'+p2(d.getSeconds())};
const STAFF_VIEWS=['dash','alerts','settings'];

/* ----- expiry date (RealTime column F) ----- */
const MON=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
const isoOf=d=>d.getFullYear()+'-'+p2(d.getMonth()+1)+'-'+p2(d.getDate());
const isoToday=()=>isoOf(new Date());
const isoPlus=n=>{const d=new Date();d.setDate(d.getDate()+n);return isoOf(d)};
const fmtD=s=>{const m=String(s||'').match(/^(\d{4})-(\d{2})-(\d{2})/);return m?(+m[3])+' '+MON[+m[2]-1]+' '+m[1]:String(s||'')};
function expInfo(c){
 const v=String((c&&c.expiry)||'').trim();
 if(!v)return {t:'No expiry date',c:'none'};
 const m=v.match(/^(\d{4})-(\d{2})-(\d{2})$/);
 if(!m)return {t:'Expiry: '+v,c:''};
 const n=new Date(),days=Math.round((Date.UTC(+m[1],+m[2]-1,+m[3])-Date.UTC(n.getFullYear(),n.getMonth(),n.getDate()))/864e5);
 if(days<0)return {t:'EXPIRED '+fmtD(v),c:'bad'};
 if(days<=30)return {t:'Expires '+fmtD(v)+' ('+(days===0?'today':days+' day'+(days===1?'':'s'))+')',c:'soon'};
 return {t:'Exp '+fmtD(v),c:''};
}
const expEl=(c,pre)=>{const e=expInfo(c);return `<small class="exp ${e.c}">${esc((pre&&e.c!=='none'?pre:'')+e.t)}</small>`};

function toast(m,t){const d=document.createElement('div');d.className='t '+(t||'');d.textContent=m;$('toasts').appendChild(d);setTimeout(()=>d.remove(),4500)}
function beep(ok=true){try{audio=audio||new (window.AudioContext||window.webkitAudioContext)();const o=audio.createOscillator(),g=audio.createGain();o.frequency.value=ok?880:220;g.gain.value=.15;o.connect(g);g.connect(audio.destination);o.start();o.stop(audio.currentTime+(ok?.12:.3))}catch(e){}if(navigator.vibrate)navigator.vibrate(ok?60:[80,60,80])}
function sync(ok){$('sync').innerHTML=ok?'<span class="live"></span>Live · updated '+new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit',second:'2-digit'})+' · '+(S.build?'code '+S.build:'server code is OLD, redeploy'):'Offline · retrying…'}

/* ================= theme ================= */
function setTheme(t,save){
 document.documentElement.dataset.theme=t;
 $('themeSw').setAttribute('aria-checked',String(t==='dark'));
 if(save){try{localStorage.setItem('sg-theme',t)}catch(e){}}
 document.dispatchEvent(new CustomEvent('sg-theme',{detail:t}));
}
$('themeSw').onclick=()=>setTheme(document.documentElement.dataset.theme==='dark'?'light':'dark',true);
$('themeSw').setAttribute('aria-checked',String(document.documentElement.dataset.theme==='dark'));

/* hide the floating nav while the phone keyboard is up */
const kbSel='input:not([type=checkbox]):not([type=radio]):not([type=button]),textarea,select';
document.addEventListener('focusin',e=>{if(!DESK.matches&&e.target.matches&&e.target.matches(kbSel))document.body.classList.add('kb')});
document.addEventListener('focusout',()=>setTimeout(()=>{const a=document.activeElement;if(!(a&&a.matches&&a.matches(kbSel)))document.body.classList.remove('kb')},60));

/* ================= rules shown in the UI (same rules as Code.gs) ================= */
function warn(c){const m=S.mode;if(!S.loaded)return '';
 if(!c)return 'Not found in the system';
 if(m==='ASSIGN'){if(c.status==='In Use')return 'Currently with '+c.location;if(c.status==='Out for Refill')return L_OUT+', not available';if(isDmg(c))return 'Damaged. Not available';if(isEmpty(c))return 'Empty. Needs a refill first'}
 if(m==='RETURN'){if(c.status==='At Logistics')return 'Already at logistics';if(c.status==='Out for Refill')return L_OUT;if(U()&&!same(c.location,U()))return 'Held by '+c.location}
 if(m==='SEND_REFILL'){if(c.status==='Out for Refill')return 'Already '+L_OUT.toLowerCase();if(c.status!=='At Logistics')return 'Not at logistics yet';if(!isEmpty(c)&&!isDmg(c))return 'Not empty or damaged'}
 if(m==='RECEIVE_REFILL'&&c.status!=='Out for Refill')return 'Not marked as '+L_OUT.toLowerCase();
 return ''}
const bad=id=>{const c=byId(id);return c?!!warn(c):true};

/* ================= data + auto refresh ================= */
function applyData(r,quiet){
 S.cyl=r.cylinders||[];if(r.units)S.units=r.units;if(r.build)S.build=r.build;if(r.gasNames)S.gasNames=r.gasNames;if(r.damageReasons)S.damageReasons=r.damageReasons;
 if(r.barcodeMap)S.barcodeMap=r.barcodeMap;
 /* a serial that was queued before the data arrived (e.g. from a QR link) becomes its cylinder ID now */
 S.queue=S.queue.map(x=>byId(x)?x:(S.barcodeMap[nk(x)]||x));
 S.ver=r.version;S.lastFull=Date.now();S.loaded=true;
 $('refresh').disabled=false;$('refresh').textContent='Refresh';
 const gone=S.queue.filter(bad);
 if(gone.length){S.queue=S.queue.filter(x=>!bad(x));if(!quiet)toast(gone.length+' selected cylinder(s) no longer available.','warn')}
 fillUnits();fillGas();render();sync(true);
 if(S.view==='alerts'){renderAlerts();loadAlertInfo(true)}
 if(S.view==='settings')fillDisIds();
 if(S.view==='dash'&&Date.now()-D.at>20000)dashLoad(true);
}
function load(silent){
 const my=++S.seq;
 if(!silent){$('refresh').disabled=true;$('refresh').textContent='Loading…'}
 google.script.run
  .withSuccessHandler(r=>{if(my!==S.seq)return;applyData(r)})
  .withFailureHandler(()=>{if(my!==S.seq)return;
   $('refresh').disabled=false;$('refresh').textContent='Refresh';sync(false);
   if(!silent)toast('Could not load data. Tap Refresh to retry.','err');
   if(!S.loaded){S.loaded=true;fillUnits();render()}})
  .getAppData();
}
function tick(){
 if(document.hidden||S.view==='gate'||S.busy||!$('ask').hidden)return;
 if(Date.now()-S.lastFull>60000)return load(true);
 const my=S.seq;
 google.script.run
  .withSuccessHandler(v=>{if(my!==S.seq||S.busy)return;String(v)!==String(S.ver)?load(true):sync(true)})
  .withFailureHandler(()=>sync(false))
  .getDataVersion();
}
setInterval(tick,10000);
document.addEventListener('visibilitychange',()=>{if(!document.hidden&&S.view!=='gate'&&!S.busy)load(true)});

function fillUnits(){
 const ci=$('iu').value,used=S.cyl.filter(c=>c.status==='In Use').map(c=>c.location);
 const all=[...new Set(S.units.concat(used))].sort();
 setHtml($('iu'),'<option value="">All units</option><option value="__log">At logistics (all)</option><option value="__ref">'+L_OUT+' (all)</option>'+all.map(u=>`<option ${u===ci?'selected':''}>${esc(u)}</option>`).join(''));
 setHtml($('tUnit'),'<option value="">Select unit</option>'+S.units.map(u=>`<option ${u===S.target?'selected':''}>${esc(u)}</option>`).join(''));
 $('unit').disabled=!S.loaded;
 setHtml($('unit'),!S.loaded?'<option value="">Loading units…</option>':`<option value="">${S.units.length?'Select your unit':'No units found. Add some to the Units sheet.'}</option>`+S.units.map(u=>`<option ${u===S.pick?'selected':''}>${esc(u)}</option>`).join('')+`<option value="__logi" ${S.pick==='__logi'?'selected':''}>Logistics staff</option>`);
 $('pWrap').hidden=S.pick!=='__logi';
}

/* ================= navigation (one function, one place) ================= */
function placeNav(){
 const n=$('tabs');if(!n||n.hidden)return;
 const a=n.querySelector('button[aria-current=page]');
 if(!a){n.removeAttribute('data-on');return}
 const first=!n.hasAttribute('data-on'),mob=!DESK.matches;
 if(first)n.classList.add('still');
 n.style.setProperty('--nx',(mob?a.offsetLeft+a.offsetWidth/2-28:a.offsetLeft)+'px');
 n.setAttribute('data-on','');
 if(first)requestAnimationFrame(()=>requestAnimationFrame(()=>n.classList.remove('still')));
}
window.addEventListener('resize',placeNav);

const VIEWS=['gate','scan','inv','dash','alerts','settings'];
function go(v){
 if(STAFF_VIEWS.indexOf(v)>-1&&S.role!=='logi')v='scan';
 S.view=v;
 VIEWS.forEach(x=>{const el=$('v-'+x);if(el)el.hidden=(v!==x)});
 $('tabs').hidden=v==='gate';
 $('hdrLogout').hidden=v==='gate';
 ['tabDash','tabAlerts','tabSettings'].forEach(id=>{$(id).hidden=S.role!=='logi'});
 document.querySelectorAll('.tabs button').forEach(b=>b.dataset.go===v?b.setAttribute('aria-current','page'):b.removeAttribute('aria-current'));
 document.body.dataset.view=v;
 if(v==='gate')$('tabs').removeAttribute('data-on');
 requestAnimationFrame(placeNav);
 if(v!=='scan')stopCam();
 if(v==='inv'&&S.unit)$('iu').value=S.unit;
 render();
 if(v==='dash')dashLoad(false);
 if(v==='alerts'){renderAlerts();mLoad(false);loadAlertInfo()}
 if(v==='settings'){fillGas();fillDisIds();loadUntracked()}
 window.scrollTo(0,0);
 if(v==='scan'&&matchMedia('(pointer:fine)').matches)$('q').focus();
 if(v!=='gate'&&S.loaded)load(true);
}
function render(){
 $('bar').hidden=S.view!=='scan'||(!S.queue.length&&!DESK.matches);
 if(S.view==='gate')fillUnits();else if(S.view==='scan')rScan();else if(S.view==='inv')rInv();
}

/* ================= scan view ================= */
function retUI(id){const r=R(id),rs=S.damageReasons||DEF_REASONS;
 const b=(k,t)=>`<button data-cond="${k}" data-cid="${esc(id)}" aria-pressed="${r.type===k}">${t}</button>`;
 let h=`<div class="seg" role="group" aria-label="Condition of ${esc(id)}">${b('E','Empty')}${b('D','Damaged')}</div>`;
 if(r.type==='D'){
  h+=`<select class="note" data-rid="${esc(id)}" aria-label="What is damaged on ${esc(id)}"><option value="">What is damaged? (required)</option>${rs.map(x=>`<option value="${esc(x)}"${x===r.reason?' selected':''}>${esc(x)}</option>`).join('')}</select>`;
  h+=`<input class="note" data-nid="${esc(id)}" maxlength="200" placeholder="${r.reason==='Other'?'Write what is damaged (required)':'More details (optional)'}" value="${esc(r.note)}">`;
  h+=`<div class="ph-row"><label class="btn ph-btn">&#128247; ${r.photo?'Change photo':'Add photo (optional)'}<input type="file" accept="image/*" data-pid="${esc(id)}" hidden></label>${r.photo?`<img class="ph-th" src="${r.photo}" alt="Photo of ${esc(id)}"><button class="x" data-rmphoto="${esc(id)}" aria-label="Remove photo">&times;</button>`:''}</div>`;
 }
 return h}

/* Return from (GPT): the NEW expiry date must be keyed in for every cylinder */
function recvUI(id){const r=R(id);
 return `<label class="exp-in">New expiry date (required)<input type="date" class="note" data-eid="${esc(id)}" value="${esc(r.expiry)}" min="${isoPlus(1)}"></label>`}

function rScan(){
 const n=s=>S.cyl.filter(c=>c.status===s).length,dm=S.cyl.filter(c=>c.status==='At Logistics'&&isDmg(c)).length;
 $('stats').innerHTML=!S.loaded?'Loading…':S.role==='unit'?`${esc(S.unit)}: <b>${S.cyl.filter(c=>c.status==='In Use'&&same(c.location,S.unit)).length}</b> cylinders taken out`:`Out with units <b>${n('In Use')}</b> &middot; At logistics <b>${n('At Logistics')}</b> &middot; ${L_OUT} <b>${n('Out for Refill')}</b>${dm?` &middot; Damaged <b>${dm}</b>`:''} &middot; Total <b>${S.cyl.length}</b>`;
 $('whoU').textContent=S.role==='logi'?'Logistics':S.unit;
 document.querySelectorAll('.modes [data-mode=SEND_REFILL],.modes [data-mode=RECEIVE_REFILL]').forEach(b=>b.hidden=S.role!=='logi');
 $('tWrap').hidden=!(S.role==='logi'&&(S.mode==='ASSIGN'||S.mode==='RETURN'));
 $('tUnit').value=S.target;$('whoN').textContent=S.staff;
 $('whoA').textContent=(S.staff||'?').trim().charAt(0).toUpperCase()||'?';
 rRes();
 document.querySelectorAll('.modes [data-mode]').forEach(b=>b.setAttribute('aria-pressed',b.dataset.mode===S.mode));
 const k=S.queue.length;
 $('qTitle').textContent='Selected cylinders ('+k+')';
 $('expAllBox').hidden=!(S.mode==='RECEIVE_REFILL'&&k>1);
 $('go').disabled=!k||S.busy;$('go').dataset.mode=S.mode;
 $('go').textContent=S.busy?'Saving…':!k?'Add a cylinder to continue':phrase(k);
 const ae=document.activeElement;
 if(!(ae&&ae.dataset&&(ae.dataset.nid||ae.dataset.eid))){
  $('qList').innerHTML=k?S.queue.map(id=>{const c=byId(id);const w=warn(c);
   return `<div class="item"><div><b>${esc(id)}</b>${gasEl(c)}${expEl(c,S.mode==='RECEIVE_REFILL'?'Previous ':'')}<small class="${w?'w':''}">${esc(w||(c?c.status+' · '+c.location:''))}</small>${S.mode==='RETURN'?retUI(id):''}${S.mode==='RECEIVE_REFILL'?recvUI(id):''}</div><button class="x" data-rm="${esc(id)}" aria-label="Remove ${esc(id)}">&times;</button></div>`}).join(''):empty('Nothing selected yet','Scan a cylinder or search by name above.');
 }
}

function avail(){const m=S.mode;
 if(m==='ASSIGN')return S.cyl.filter(c=>c.status==='At Logistics'&&!isEmpty(c)&&!isDmg(c));   // full cylinders at logistics only
 if(m==='RETURN')return U()?S.cyl.filter(c=>c.status==='In Use'&&same(c.location,U())):[];
 if(m==='SEND_REFILL')return S.cyl.filter(c=>c.status==='At Logistics'&&(isEmpty(c)||isDmg(c)));
 return S.cyl.filter(c=>c.status==='Out for Refill')}

function rRes(){
 const r=$('res'),h=$('resHead'),q=$('q').value.trim().toLowerCase(),m=S.mode;
 if(!S.loaded){r.innerHTML=skel();h.innerHTML='';return}
 const all=avail(),a=all.filter(c=>hit(c,q)).sort((x,y)=>x.id.localeCompare(y.id,undefined,{numeric:true}));
 S.shown=a.map(c=>c.id);
 const T={ASSIGN:'Full cylinders at logistics',RETURN:U()?'Taken by '+U():'Choose a unit above',SEND_REFILL:'Empty or damaged at logistics',RECEIVE_REFILL:L_OUT};
 $('addTitle').textContent=T[m]+(m==='RETURN'&&!U()?'':': '+all.length);
 const allSel=a.length>0&&a.every(c=>S.queue.indexOf(c.id)>-1);
 h.innerHTML=(m!=='ASSIGN'&&a.length>1)?`<span class="mu">${a.length} shown</span><button class="btn" data-all="1">${allSel?'Clear selection':'Select all ('+a.length+')'}</button>`:'';
 const E={ASSIGN:'No full cylinders are available at logistics.',RETURN:U()?esc(U())+' has no cylinders to return.':'Choose a unit above to see its cylinders.',SEND_REFILL:'No empty or damaged cylinders are waiting at logistics.',RECEIVE_REFILL:'No cylinders are out for refill.'};
 const st=r.scrollTop;
 r.innerHTML=a.length?a.map(c=>{const on=S.queue.indexOf(c.id)>-1;return `<button data-add="${esc(c.id)}" class="${on?'on':''}" aria-pressed="${on}"><i class="ck"></i><span style="min-width:0"><b>${esc(c.id)}</b>${gasEl(c)}${expEl(c)}</span><span class="mu" style="margin-left:auto;padding-left:8px">${esc(condTxt(c))}</span></button>`}).join(''):empty(q?'No match':'Nothing here',q?'Press Enter to check it, or check the name.':E[m]);
 r.scrollTop=st;
}

function addId(raw,src){
 let id=String(raw||'').trim();const m=id.match(/[?&]id=([^&#\s]+)/);if(m){try{id=decodeURIComponent(m[1])}catch(e){id=m[1]}}
 if(!id)return;
 if(S.barcodeMap&&S.barcodeMap[nk(id)])id=S.barcodeMap[nk(id)];   // a scanned serial/barcode (QRcodes column B) becomes the cylinder ID
 const c=byId(id);if(c)id=c.id;
 if(!$('blk').hidden&&(src==='cam'||src==='scan'))return;   // a popup is open: ignore more scans until it is closed
 const now=Date.now();
 if(src==='cam'||src==='scan'){const dup=id===lastCode&&now-lastAt<(src==='cam'?3000:1500);lastCode=id;lastAt=now;if(dup)return}
 if(S.queue.indexOf(id)>-1){toast(id+' is already selected.','warn');beep(false);return}
 if(S.loaded&&bad(id)){
  const why=c?warn(c):'Not found in the system';
  beep(false);
  if(S.mode==='ASSIGN')blockPop(id,c,why);else toast(id+': '+why,'err');
  return}
 S.queue.push(id);beep(true);$('q').value='';$('err').textContent='';
 if(src==='cam'||src==='scan')toast('\u2713 '+id+' added \u00b7 '+S.queue.length+' selected');
 render();
}
/* Take out: a cylinder that is not full / not at logistics cannot be selected. Show why, in a popup. */
function blockPop(id,c,why){
 let m;
 if(!c)m=id+' was not found in the system. Logistics staff can add it first in Settings \u2192 Add cylinder.';
 else if(c.status==='In Use')m=id+' is already with '+c.location+'.';
 else if(c.status==='Out for Refill')m=id+' is '+L_OUT.toLowerCase()+'. It is not at logistics.';
 else if(isDmg(c))m=id+' is DAMAGED'+(dmgNote(c)?' ('+dmgNote(c)+')':'')+'. It cannot be taken out.';
 else if(isEmpty(c))m=id+' is EMPTY. It needs a refill first, so it cannot be taken out.';
 else m=id+': '+why;
 $('blkT').textContent='Cannot take out';$('blkP').textContent=m;$('blk').hidden=false;$('blkOk').focus();
}
$('blkOk').onclick=()=>{$('blk').hidden=true;if(matchMedia('(pointer:fine)').matches)$('q').focus()};
$('q').addEventListener('input',rRes);
$('q').addEventListener('keydown',e=>{if(e.key!=='Enter')return;e.preventDefault();
 const raw=$('q').value.trim(),q=raw.toLowerCase();if(!q)return;
 const ex=S.cyl.find(c=>c.id.toLowerCase()===q),m=avail().filter(c=>c.id.toLowerCase().includes(q));
 if(ex)addId(ex.id);else if(m.length===1)addId(m[0].id);else if(!S.cyl.some(c=>hit(c,q)))addId(raw);});

/* handheld scanner: very fast typing that ends with Enter */
let sbuf='',sfast=0,slast=0,stimer=null;
function sFlush(){
 clearTimeout(stimer);
 const code=sbuf,ok=code.length>=3&&sfast>=code.length-1;
 sbuf='';sfast=0;
 if(!ok)return false;
 $('q').value='';
 const ae=document.activeElement;if(ae&&ae.tagName==='BUTTON')ae.blur();
 addId(code,'scan');rRes();return true;
}
document.addEventListener('keydown',e=>{
 if(S.view!=='scan'||!$('ask').hidden||!$('blk').hidden||!$('pv').hidden||e.ctrlKey||e.metaKey||e.altKey)return;
 const t=e.target,tag=t.tagName;
 if((tag==='INPUT'&&t.id!=='q')||tag==='SELECT'||tag==='TEXTAREA')return;
 const now=Date.now();
 if(e.key==='Enter'||e.key==='Tab'){
  if(sbuf.length>=3&&sfast>=sbuf.length-1){e.preventDefault();e.stopImmediatePropagation();sFlush()}
  else{sbuf='';sfast=0;clearTimeout(stimer)}
  return}
 if(e.key.length!==1)return;
 if(now-slast<=80&&sbuf)sfast++;else{sbuf='';sfast=0}
 sbuf+=e.key;slast=now;
 clearTimeout(stimer);stimer=setTimeout(sFlush,150);
},true);

/* camera: reads QR codes and common barcodes (Code 128, Code 39, EAN, UPC, ITF ...) */
function makeQr(){
 const F=window.Html5QrcodeSupportedFormats;
 return new Html5Qrcode('reader',{
  verbose:false,
  useBarCodeDetectorIfSupported:true,   // uses the phone's native detector when available (fast, accurate)
  formatsToSupport:F?[F.QR_CODE,F.CODE_128,F.CODE_39,F.CODE_93,F.EAN_13,F.EAN_8,F.UPC_A,F.UPC_E,F.ITF,F.CODABAR,F.DATA_MATRIX]:undefined
 });
}
async function startCam(){
 const m=t=>{$('camMsg').textContent=t};m('');
 try{audio=audio||new (window.AudioContext||window.webkitAudioContext)();if(audio.resume)audio.resume()}catch(e){}
 if(!window.Html5Qrcode)return m('The camera scanner could not load. Check the internet, or use a handheld scanner / type the ID.');
 if(!window.isSecureContext||!navigator.mediaDevices||!navigator.mediaDevices.getUserMedia)return m('Camera needs a secure (https) page. Open the app directly in Chrome or Safari. If it keeps failing, use Snap photo.');
 /* wide scan box so long, thin barcodes fit as well as square QR codes */
 const cfg={fps:15,qrbox:(w,h)=>({width:Math.floor(w*0.9),height:Math.floor(Math.min(h*0.55,240))}),disableFlip:true};
 const vc={facingMode:'environment',width:{ideal:1920},height:{ideal:1080}};
 try{
  qr=qr||makeQr();$('reader').hidden=false;
  try{await qr.start(vc,cfg,t=>addId(t,'cam'),()=>{})}
  catch(e1){
   if(/permission|notallowed|denied/i.test(String(e1)))throw e1;
   const cams=await Html5Qrcode.getCameras();if(!cams.length)throw new Error('No camera found on this device.');
   await qr.start(cams[0].id,cfg,t=>addId(t,'cam'),()=>{})}
  camOn=true;camUi();$('reader').scrollIntoView({block:'center',behavior:'smooth'});
 }catch(e){$('reader').hidden=true;camOn=false;camUi();
  m(/permission|notallowed|denied/i.test(String(e))?'Camera access is blocked here. Use Snap photo instead, or allow the camera in the browser settings.':'The camera could not start. '+(e&&e.message?e.message:'Close other apps using it and try again.')+' You can use Snap photo instead.')}
}
async function stopCam(){if(qr&&camOn){try{await qr.stop()}catch(e){}}camOn=false;$('reader').hidden=true;camUi()}
function camUi(){$('camStart').hidden=camOn;$('camFile').parentNode.hidden=camOn;$('camStop').hidden=!camOn;document.body.classList.toggle('cam',camOn)}

$('camFile').addEventListener('change', async e => {
 if(!e.target.files||!e.target.files.length)return;
 $('camMsg').textContent='Scanning photo...';
 $('camMsg').style.color='var(--tx)';
 const file = e.target.files[0];
 // Shrink the huge phone photo, but keep enough detail for thin barcode lines
 const img = new Image();
 const url = URL.createObjectURL(file);
 img.onload = async () => {
   URL.revokeObjectURL(url);
   const cvs = document.createElement('canvas');
   const MAX_W = 1800;
   const scale = Math.min(1, MAX_W / img.width);
   cvs.width = img.width * scale;
   cvs.height = img.height * scale;
   cvs.getContext('2d').drawImage(img, 0, 0, cvs.width, cvs.height);
   cvs.toBlob(async (blob) => {
     const smallFile = new File([blob], file.name, { type: 'image/jpeg' });
     try {
      qr = qr || makeQr();
      const result = await qr.scanFile(smallFile, false);
      addId(result, 'cam');
      $('camMsg').textContent='';
     } catch(err) {
      $('camMsg').style.color='var(--bad)';
      $('camMsg').textContent='No code found. Get closer, hold it horizontal and keep it in focus.';
     }
   }, 'image/jpeg', 0.92);
 };
 img.src = url;
 e.target.value='';
});

/* submit */
function doSubmit(){
 if(S.busy)return;const staff=S.staff,unit=S.unit,forUnit=(S.mode==='ASSIGN'||S.mode==='RETURN')?U():'',roleTag=S.role==='unit'?S.unit:(forUnit?'Logistics for '+forUnit:'Logistics');
 if(!S.queue.length)return;
 const det={};if(S.mode==='RETURN')S.queue.forEach(id=>{const r=R(id);det[id]=r.type==='D'?{condition:'Damaged',reason:r.reason,note:r.note.trim(),photo:r.photo||''}:{condition:'Empty'}});
 if(S.mode==='RECEIVE_REFILL')S.queue.forEach(id=>{det[id]={expiry:R(id).expiry}});
 try{localStorage.setItem('lpgStaff',staff);localStorage.setItem('lpgUnit',unit)}catch(e){}
 S.busy=true;$('err').textContent='';render();
 const fin=()=>{S.busy=false;render()};
 google.script.run.withSuccessHandler(r=>{fin();
  if(r&&r.success){S.seq++;S.queue=[];S.ret={};if(r.cylinders)applyData(r,true);else load(true);stopCam();toast(r.message)}
  else{if(r&&r.cylinders){S.seq++;applyData(r,true)}
   $('err').textContent=(r&&r.message)||'The update failed. Your selection is kept. Try again.'}
 }).withFailureHandler(e=>{fin();$('err').textContent='Could not reach the server ('+(e&&e.message||'unknown error')+'). Your selection is kept. Try again.'})
 .processBatchAction(S.queue.slice(),S.mode,forUnit,staff,roleTag,'',S.pin,det);
}
function askConfirm(){
 if(S.busy||!S.queue.length)return;const k=S.queue.length,m=S.mode,needU=m==='ASSIGN'||m==='RETURN';
 if(S.role==='logi'&&needU&&!U()){toast('Choose which unit this is for.','warn');$('tUnit').focus();return}
 let dn=0;
 if(m==='RETURN'){
  const miss=S.queue.find(id=>R(id).type==='D'&&(!R(id).reason||(R(id).reason==='Other'&&!R(id).note.trim())));
  if(miss){const r=R(miss);toast(!r.reason?'Choose what is damaged on '+miss+'.':'Write what is damaged on '+miss+'.','warn');
   const i=r.reason?noteInput(miss):[...document.querySelectorAll('#qList select')].find(x=>x.dataset.rid===miss);
   if(i){i.classList.add('bad');i.focus();i.scrollIntoView({block:'center'})}return}
  dn=S.queue.filter(id=>R(id).type==='D').length;
 }
 if(m==='RECEIVE_REFILL'){
  const b1=S.queue.find(id=>!R(id).expiry),b2=S.queue.find(id=>R(id).expiry&&R(id).expiry<=isoToday()),b3=S.queue.find(id=>R(id).expiry&&R(id).expiry>isoPlus(7300)),miss=b1||b2||b3;
  if(miss){toast(b1?'Enter the new expiry date for '+miss+'.':b2?'The expiry date for '+miss+' must be after today.':'Check the year of the expiry date for '+miss+'.','warn');
   const i=[...document.querySelectorAll('#qList input[type=date]')].find(x=>x.dataset.eid===miss);
   if(i){i.classList.add('bad');i.focus();i.scrollIntoView({block:'center'})}return}
 }
 if(m==='SEND_REFILL')dn=S.queue.filter(id=>{const c=byId(id);return c&&isDmg(c)}).length;
 const who=S.role==='unit'?S.unit:(needU?'Logistics for '+U():'Logistics');
 $('askT').textContent=phrase(k)+'?';
 $('askP').textContent=who+' \u00b7 '+S.staff+'. '+S.queue.slice(0,6).map(id=>m==='RECEIVE_REFILL'?id+' (expiry '+fmtD(R(id).expiry)+')':id).join(', ')+(k>6?' and '+(k-6)+' more':'')+(dn?' \u00b7 '+dn+' damaged':'');
 $('ask').hidden=false;$('yes').focus();
}
$('yes').onclick=()=>{$('ask').hidden=true;doSubmit()};
$('no').onclick=()=>{$('ask').hidden=true};
document.addEventListener('keydown',e=>{if(e.key==='Escape'){$('ask').hidden=true;$('blk').hidden=true;$('pv').hidden=true}});

/* ================= inventory view ================= */
function rInv(){
 const uo=S.role==='unit',u=uo?S.unit:$('iu').value,el=$('iList'),sp=u==='__log'?'At Logistics':u==='__ref'?'Out for Refill':'';
 if(!S.loaded){el.innerHTML=skel();$('istats').innerHTML='<div class="sk" style="grid-column:1/-1"></div>';return}
 const a=S.cyl.filter(c=>sp?c.status===sp:(c.status==='In Use'&&(!u||same(c.location,u)))).sort((x,y)=>x.location.localeCompare(y.location)||x.id.localeCompare(y.id,undefined,{numeric:true}));
 const nm=sp?(u==='__log'?'At logistics':L_OUT):(u||'All units');
 $('istats').innerHTML=(uo?[['Taken by '+esc(u),a.length]]:[[sp?nm:'Taken'+(u?' by '+esc(u):''),a.length],['At logistics',S.cyl.filter(c=>c.status==='At Logistics').length],[L_OUT,S.cyl.filter(c=>c.status==='Out for Refill').length]]).map(x=>`<div class="stat"><b>${x[1]}</b><span>${x[0]}</span></div>`).join('');
 $('iCard').hidden=uo;$('iH').textContent=uo?u+' stock':'Inventory';$('iS').textContent=uo?'Cylinders your unit has taken out':'Cylinders taken out by unit';
 $('iTitle').textContent=nm+' ('+a.length+')';
 const old=el.querySelector('.res'),st=old?old.scrollTop:0;
 el.innerHTML=a.length?'<div class="res tall">'+a.map(c=>`<div class="r"><div style="min-width:0"><b>${esc(c.id)}</b>${gasEl(c)}${expEl(c)}<span class="mu" style="display:block">${sp?'Updated ':'Taken '}${esc(c.lastUpdated)}</span>${isDmg(c)&&dmgNote(c)?`<span class="mu" style="display:block;color:var(--bad)">Note: ${esc(dmgNote(c))}</span>`:''}</div><div style="text-align:right">${u?'':`<span class="tag d">${esc(c.location)}</span> `}${condTag(c)}</div></div>`).join('')+'</div>':empty('Nothing here',sp?'No cylinders in this group right now.':(u?esc(u)+' has no':'No units have')+' cylinders taken out.','<br><button class="btn" data-go="scan">Go to scan</button>');
 const nw=el.querySelector('.res');if(nw)nw.scrollTop=st;
}

/* ================= alerts view ================= */
function renderAlerts(){
 if(!S.loaded)return;
 const ref=S.cyl.filter(c=>c.status==='Out for Refill');
 /* detailed list from the server (unit, date, note, photo); falls back to the plain list while it loads */
 const dmg=S.alertDmg||S.cyl.filter(c=>c.status==='At Logistics'&&isDmg(c)).map(c=>({id:c.id,gas:gasTxt(c),note:dmgNote(c),from:'',since:'',photo:''}));
 $('aDmgList').innerHTML=dmg.length?dmg.map(x=>`<div class="r"><div style="min-width:0"><b>${esc(x.id)}</b>${phBtn(x.photo)}${x.gas?`<small class="gas">${esc(x.gas)}</small>`:''}<span class="mu" style="display:block">${x.from?'From unit: '+esc(x.from)+' · ':''}${x.since?esc(String(x.since).slice(0,16))+' · ':''}${esc(x.note||'no note')}</span></div></div>`).join(''):empty('All clear','No damaged cylinders waiting.');
 $('aRefList').innerHTML=ref.length?ref.map(c=>`<div class="r"><div style="min-width:0"><b>${esc(c.id)}</b>${gasEl(c)}<span class="mu" style="display:block">Sent ${esc(c.lastUpdated)}</span></div></div>`).join(''):empty('All clear','No cylinders are '+L_OUT.toLowerCase()+'.');
}
function setTg(on){$('dMailTg').checked=!!on}
function loadAlertInfo(silent){
 if(!silent)$('aMailStatus').textContent='Checking…';
 google.script.run
  .withSuccessHandler(r=>{
   if(r&&r.ok){S.alertDmg=r.damaged;setTg(r.on);$('aMailStatus').textContent=(r.on?'Auto-email is ON':'Auto-email is OFF')+(r.lastSent?' · last sent '+String(r.lastSent).slice(5,16):'')+' · '+r.email;renderAlerts()}
   else{$('aMailStatus').textContent=(r&&r.error)||'Could not load.'}})
  .withFailureHandler(()=>{$('aMailStatus').textContent='Could not reach the server.'})
  .getAlertInfo(S.pin);
}
document.addEventListener('change',e=>{
 if(e.target.id!=='dMailTg')return;
 const el=e.target,on=el.checked;
 $('aMailStatus').textContent='Saving…';
 google.script.run
  .withSuccessHandler(r=>{if(r&&r.ok){toast(on?'Auto-email alerts are on.':'Auto-email alerts are off.');loadAlertInfo()}else{setTg(!on);loadAlertInfo();toast((r&&r.error)||'Could not save the setting.','err')}})
  .withFailureHandler(()=>{setTg(!on);loadAlertInfo();toast('Could not reach the server.','err')})
  .setDamagedAlert(S.pin,on);
});
$('dMailNow').onclick=function(){
 const b=this;
 if(!b.dataset.arm){b.dataset.arm='1';b.textContent='Tap again to send';setTimeout(()=>{if(b.dataset.arm&&!b.disabled){delete b.dataset.arm;b.textContent='Send now'}},4000);return}
 b.disabled=true;b.textContent='Sending…';
 const reset=()=>{b.disabled=false;delete b.dataset.arm;b.textContent='Send now'};
 google.script.run
  .withSuccessHandler(r=>{reset();if(r&&r.ok){toast('Notice sent ('+r.count+' cylinder'+(r.count===1?'':'s')+').');loadAlertInfo()}else toast((r&&r.error)||'Could not send the email.','err')})
  .withFailureHandler(()=>{reset();toast('Could not reach the server.','err')})
  .sendDamagedNotice(S.pin);
};

/* ----- handover memo ----- */
const M={list:[],ver:'',seq:0};
const MEMO_DAYS=7;
const alive=m=>{const t=Date.parse(String(m.time).replace(' ','T'));return isNaN(t)||Date.now()-t<MEMO_DAYS*864e5};
const mFmt=t=>String(t||'').slice(5,16);
function mCard(m){
 const foot=m.pending?'<span>Posting…</span><span></span>'
  :`<span>${esc(mFmt(m.time))}</span><button data-mdone="${esc(m.id)}" style="color:#3b82f6">&#10003; Done</button>`;
 return `<div class="fld-paper"><div class="fld-paper-h">${esc(m.author)}</div><div class="fld-paper-t">${esc(m.text)}</div><div class="fld-foot">${foot}</div></div>`;
}
function mRender(){
 const open=M.list.filter(alive).sort((a,b)=>String(a.time).localeCompare(String(b.time)));
 const L=$('mList');
 L.innerHTML=open.length?open.map(mCard).join(''):'<div class="fld-paper"><div class="fld-paper-t" style="text-align:center;color:#94a3b8;padding:20px 0">No notes.</div></div>';
 $('mCountNum').textContent=open.length;
 if($('memoFolder').classList.contains('open'))setTimeout(()=>{L.scrollTop=L.scrollHeight},50);
}
function mLoad(silent){
 const my=++M.seq;
 google.script.run
  .withSuccessHandler(r=>{if(my!==M.seq)return;
   if(!r||!r.ok){if(!silent)toast((r&&r.error)||'Could not load the memo.','err');return}
   M.ver=r.version;M.list=M.list.filter(m=>m.pending).concat(r.memos);mRender()})
  .withFailureHandler(e=>{if(!silent&&my===M.seq)toast('Memo error: '+((e&&e.message)||'unknown'),'err')})
  .getMemos(S.pin);
}
$('memoOrb').onclick=()=>{$('memoFolder').classList.toggle('open');setTimeout(()=>{$('mList').scrollTop=$('mList').scrollHeight},300)};
$('mPost').onclick=()=>{
 const t=$('mTxt').value.trim();if(!t)return;
 const name=S.staff||'Staff',tmp={id:'tmp'+Date.now(),time:nowStr(),author:name,text:t,done:false,pending:true};
 M.list.push(tmp);$('mTxt').value='';mRender();
 const fail=msg=>{M.list=M.list.filter(m=>m.id!==tmp.id);if(!$('mTxt').value)$('mTxt').value=t;mRender();toast(msg||'Could not post the note. Try again.','err')};
 google.script.run
  .withSuccessHandler(r=>{if(r&&r.ok){M.list=M.list.filter(m=>m.id!==tmp.id);mLoad(true)}else fail(r&&r.error)})
  .withFailureHandler(()=>fail())
  .addMemo(S.pin,name,t);
};
$('mTxt').addEventListener('keydown',e=>{if(e.key==='Enter'&&!e.shiftKey){e.preventDefault();$('mPost').click()}});
$('mList').addEventListener('click',e=>{
 const b=e.target.closest('[data-mdone]');if(!b)return;
 const id=b.dataset.mdone,m=M.list.find(x=>x.id===id);if(!m)return;
 M.list=M.list.filter(x=>x.id!==id);mRender();   // hidden from the app straight away; the sheet keeps the record
 const restore=msg=>{M.list.push(m);mRender();toast(msg,'err')};
 google.script.run
  .withSuccessHandler(r=>{if(!(r&&r.ok))restore((r&&r.error)||'Could not update the note.')})
  .withFailureHandler(()=>restore('Could not update the note.'))
  .setMemoDone(S.pin,id,true,S.staff||'Staff');
});
setInterval(()=>{
 if(S.view!=='alerts'||document.hidden)return;
 google.script.run.withSuccessHandler(v=>{if(String(v)!==String(M.ver))mLoad(true)}).getMemoVersion();
},15000);

/* ================= settings view ================= */
function cylResult(r){
 if(r&&r.ok){applyData({cylinders:r.cylinders,version:r.version,gasNames:r.gasNames},true);toast(r.message);return true}
 toast((r&&r.error)||'That did not work. Try again.','err');return false;
}
const todayStr=()=>{const d=new Date();return d.getFullYear()+'-'+p2(d.getMonth()+1)+'-'+p2(d.getDate())};

/* add cylinder: gas name is picked from QRcodes column C */
function fillGas(){
 const sel=$('newGasSel');if(!sel)return;
 const cur=sel.value,names=S.gasNames||[];
 setHtml(sel,'<option value="">Select gas…</option>'+names.map(g=>`<option value="${esc(g)}"${g===cur?' selected':''}>${esc(g)}</option>`).join('')+`<option value="__new"${cur==='__new'?' selected':''}>Other (not in the list)…</option>`);
}
$('newGasSel').addEventListener('change',()=>{$('newGasWrap').hidden=$('newGasSel').value!=='__new';if(!$('newGasWrap').hidden)$('newGas').focus()});
$('addCylBtn').onclick=()=>{
 const id=$('newCylId').value.trim(),sv=$('newGasSel').value,gas=sv==='__new'?$('newGas').value.trim():sv,size=$('newSize').value.trim(),valve=$('newValve').value.trim(),exp=$('newExp').value,b=$('addCylBtn');
 if(!id)return toast('Enter a cylinder ID.','warn');
 if(!gas)return toast(sv==='__new'?'Type the new gas name.':'Choose the gas name.','warn');
 b.disabled=true;
 google.script.run
  .withSuccessHandler(r=>{b.disabled=false;if(cylResult(r)){['newCylId','newGas','newSize','newValve','newExp'].forEach(x=>{$(x).value=''});$('newGasSel').value='';$('newGasWrap').hidden=true;$('newCylId').focus()}})
  .withFailureHandler(()=>{b.disabled=false;toast('Could not reach the server.','err')})
  .addCylinder(S.pin,id,gas,size,valve,exp);
};
$('newCylId').addEventListener('keydown',e=>{if(e.key==='Enter'){e.preventDefault();$('newGasSel').focus()}});
$('newGas').addEventListener('keydown',e=>{if(e.key==='Enter'){e.preventDefault();$('newSize').focus()}});
$('newSize').addEventListener('keydown',e=>{if(e.key==='Enter'){e.preventDefault();$('newValve').focus()}});
$('newValve').addEventListener('keydown',e=>{if(e.key==='Enter'){e.preventDefault();$('newExp').focus()}});
$('newExp').addEventListener('keydown',e=>{if(e.key==='Enter'){e.preventDefault();$('addCylBtn').click()}});

/* disposal */
function fillDisIds(){
 const el=$('disIds');if(!el)return;
 setHtml(el,S.cyl.filter(c=>c.status==='At Logistics').sort((a,b)=>a.id.localeCompare(b.id,undefined,{numeric:true})).map(c=>`<option value="${esc(c.id)}"></option>`).join(''));
}
$('disBtn').onclick=()=>{
 const id=$('disId').value.trim(),reason=$('disReason').value,note=$('disNote').value.trim(),b=$('disBtn');
 if(!id)return toast('Enter the cylinder ID.','warn');
 if(!reason)return toast('Choose a reason for the disposal.','warn');
 if(reason==='Other'&&!note)return toast('Add a note when the reason is Other.','warn');
 if(!b.dataset.arm){b.dataset.arm='1';b.textContent='Tap again to dispose '+id;setTimeout(()=>{if(b.dataset.arm&&!b.disabled){delete b.dataset.arm;b.textContent='Dispose cylinder'}},4000);return}
 b.disabled=true;b.textContent='Saving…';
 const reset=()=>{b.disabled=false;delete b.dataset.arm;b.textContent='Dispose cylinder'};
 google.script.run
  .withSuccessHandler(r=>{reset();if(cylResult(r)){$('disId').value='';$('disReason').value='';$('disNote').value='';D.at=0}})
  .withFailureHandler(()=>{reset();toast('Could not reach the server.','err')})
  .disposeCylinder(S.pin,id,reason,note,S.staff);
};
$('disId').addEventListener('input',()=>{const b=$('disBtn');if(b.dataset.arm){delete b.dataset.arm;b.textContent='Dispose cylinder'}});

/* cylinders that were already with units before tracking started */
function loadUntracked(){
 const t=$('utText'),b=$('utBtn');
 t.textContent='Checking…';b.disabled=true;
 if(!$('utDate').value)$('utDate').value=todayStr();
 $('utDate').max=todayStr();
 google.script.run
  .withSuccessHandler(r=>{
   if(!(r&&r.ok)){t.textContent=(r&&r.error)||'Could not check.';return}
   b.disabled=!r.count;
   t.innerHTML=r.count
    ?`<b>${r.count}</b> cylinder${r.count===1?' is':'s are'} with units but there is no record of when ${r.count===1?'it':'they'} went out (taken out before tracking started). ${r.count===1?'It counts':'They count'} normally in stock and ${r.count===1?'is':'are'} <b>not</b> flagged as overdue. When a unit returns ${r.count===1?'it':'one'}, normal tracking begins.`
    :'Every cylinder at a unit already has a record. Nothing to do here.';
  })
  .withFailureHandler(()=>{t.textContent='Could not reach the server.'})
  .getUntracked(S.pin);
}
$('utBtn').onclick=()=>{
 const b=$('utBtn'),d=$('utDate').value;
 if(!d)return toast('Choose a start date.','warn');
 if(!b.dataset.arm){b.dataset.arm='1';b.textContent='Tap again to start from '+d;setTimeout(()=>{if(b.dataset.arm&&!b.disabled){delete b.dataset.arm;b.textContent='Start tracking these cylinders'}},4000);return}
 b.disabled=true;b.textContent='Saving…';
 const reset=()=>{delete b.dataset.arm;b.textContent='Start tracking these cylinders'};
 google.script.run
  .withSuccessHandler(r=>{reset();if(r&&r.ok){toast(r.message);D.at=0;load(true)}else toast((r&&r.error)||'Could not save.','err');loadUntracked()})
  .withFailureHandler(()=>{reset();toast('Could not reach the server.','err');loadUntracked()})
  .startTracking(S.pin,d,S.staff);
};

/* ================= dashboard view ================= */
const D={range:'month',data:null,at:0,seq:0,tseq:0,busy:false,charts:{},sel:null,lock:null};
const AL={ASSIGN:['Taken out','#3b82f6'],RETURN:['Returned','#10b981'],SEND_REFILL:[L_OUT,'#8b5cf6'],RECEIVE_REFILL:[L_BACK,'#06b6d4'],DISPOSE:['Disposed','#64748b']};
const PC={'With units':'#3b82f6','Ready at logistics':'#10b981','Empty at logistics':'#eab308','Out for refill':'#06b6d4','Damaged':'#ef4444'};
const GC=['#8b5cf6','#0ea5e9','#10b981','#ec4899','#eab308','#84cc16','#6366f1','#f43f5e','#06b6d4','#a855f7'];
const RL={all:'All time',week:'Last 7 days',month:'Last 30 days','3m':'Last 3 months'};
const pl=(n,one,many)=>n+' '+(n===1?one:(many||one+'s'));
const gc=n=>{const i=((D.data&&D.data.gasList)||[]).indexOf(n);return i<0?'#94a3b8':GC[i%GC.length]};
const gdot=n=>`<i class="d-gd" style="background:${gc(n)}"></i>`;
const badge=(t,c)=>`<span class="d-b" style="background:${c}">${esc(t)}</span>`;
const fD=s=>{if(!s)return'';const d=new Date(s+'T00:00:00');return isNaN(d.getTime())?String(s):d.toLocaleDateString([],{day:'numeric',month:'short'})};
const inDays=n=>n<0?pl(-n,'day')+' late':n===0?'today':'in '+pl(n,'day');
const drow=(id,sub,b,right)=>`<div class="d-row" data-trk="${esc(id)}"><div style="min-width:0"><b>${esc(id)}</b><span class="mu d-l">${esc(sub)}</span></div><div style="text-align:right">${b}${right?`<span class="mu d-l">${esc(right)}</span>`:''}</div></div>`;
const cssv=v=>getComputedStyle(document.documentElement).getPropertyValue(v).trim();
function chartDefaults(){if(!window.Chart)return;Chart.defaults.color=cssv('--mu')||'#94a3b8';Chart.defaults.borderColor=cssv('--line')||'rgba(255,255,255,.1)'}
function killCharts(){Object.keys(D.charts).forEach(k=>{try{D.charts[k].destroy()}catch(e){}delete D.charts[k]})}
document.addEventListener('sg-theme',()=>{chartDefaults();if(D.data){killCharts();dashRender()}});
chartDefaults();

function chart(id,type,data,opts,isEmptyData){
 const cv=$(id),nd=cv.parentNode.querySelector('.d-nd');
 if(!window.Chart){cv.hidden=true;nd.hidden=false;nd.innerHTML='<b>Charts unavailable</b>Check the internet connection and refresh.';return}
 if(isEmptyData){if(D.charts[id]){D.charts[id].destroy();delete D.charts[id]}cv.hidden=true;nd.hidden=false;return}
 cv.hidden=false;nd.hidden=true;
 const ch=D.charts[id];
 if(ch){ch.data.labels=data.labels;ch.data.datasets=data.datasets;ch.update('none');return}
 D.charts[id]=new Chart(cv,{type:type,data:data,options:Object.assign({responsive:true,maintainAspectRatio:false},opts)});
}
function gasTable(rows,withLead){
 return `<div class="d-tw"><table class="d-tbl"><thead><tr><th>Gas</th><th>Qty</th>${withLead?'<th>Lead time</th>':''}</tr></thead><tbody>`+
  rows.map(x=>`<tr><td>${gdot(x.gas)}<b>${esc(x.gas)}</b></td><td><b>${x.qty}</b></td>${withLead?`<td class="mu">${x.lead!=null?x.lead+' days':'-'}</td>`:''}</tr>`).join('')+'</tbody></table></div>';
}
function leadText(l){return l.isDefault?`default ${l.days} days (no finished deliveries yet)`:`about ${l.days} days (recent average ${l.avg}${l.last!=null?', last delivery '+l.last:''}; based on your latest ${pl(l.samples,'delivery','deliveries')}, newer ones count more)`}

function dtUnits(d,B){
 if(!d.byUnit.length){B.innerHTML='<div class="mu">No cylinders are with units right now.</div>';return}
 const gases=d.gasList.filter(g=>d.byUnit.some(u=>u.gases&&u.gases[g]));
 const h=Math.min(380,Math.max(170,d.byUnit.length*36+70));
 B.innerHTML=`<div class="mu" style="margin-bottom:6px">Cylinders per unit, coloured by gas${d.untrackedCount?` &middot; ${d.untrackedCount} with start date unknown`:''}</div><div class="d-cv" style="height:${h}px"><canvas id="cDetail"></canvas></div>`;
 if(!window.Chart)return;
 D.charts.cDetail=new Chart($('cDetail'),{type:'bar',
  data:{labels:d.byUnit.map(u=>u.name),datasets:gases.map(g=>({label:g,data:d.byUnit.map(u=>(u.gases&&u.gases[g])||0),backgroundColor:gc(g),borderWidth:0}))},
  options:{indexAxis:'y',responsive:true,maintainAspectRatio:false,plugins:{legend:{position:'top',labels:{boxWidth:10,font:{size:11}}}},scales:{x:{stacked:true,beginAtZero:true,ticks:{precision:0}},y:{stacked:true,grid:{display:false}}}}});
}
function dtReady(d,B){
 const r=d.ready;
 if(!r.groups.length){B.innerHTML='<div class="mu">No gas types found yet.</div>';return}
 const lab={out:'Out of stock',low:'Low',ok:'OK'},sz=z=>z===''?'-':(/^[\d.,]+$/.test(String(z))?z+' m\u00b3':z),pill=l=>`<span class="d-st ${l}">${lab[l]}</span>`;
 let rows='';
 r.groups.forEach(g=>{
  if(g.detailed)rows+=`<tr class="lv-head"><td colspan="3">${gdot(g.gas)}<b>${esc(g.gas)}</b> <span class="mu">all sizes</span></td><td><b>${g.qty}</b> <span class="mu">of ${g.total}</span></td><td>${g.min||'-'}</td><td>${pill(g.level)}</td></tr>`;
  g.rows.forEach(x=>{rows+=`<tr><td>${g.detailed?'':gdot(g.gas)+'<b>'+esc(g.gas)+'</b>'}</td><td${g.detailed?' class="ind"':''}>${esc(sz(x.size))}</td><td>${esc(x.valve||'-')}</td><td><b>${x.qty}</b> <span class="mu">of ${x.total}</span></td><td>${x.min||'-'}</td><td>${pill(x.level)}</td></tr>`});
 });
 B.innerHTML=`<div style="display:flex;gap:8px;margin-bottom:8px"><span class="d-st out">${r.out} out</span><span class="d-st low">${r.low} low</span></div><div class="d-tw"><table class="d-tbl"><thead><tr><th>Gas</th><th>Size</th><th>Valve</th><th>Ready</th><th>Min</th><th>Status</th></tr></thead><tbody>${rows}</tbody></table></div><div class="mu" style="margin-top:8px">Set minimums in the &ldquo;StockSettings&rdquo; sheet: one row per gas, size and valve type. A row with size and valve left empty is the minimum for the whole gas.</div>`;
}
const addDays=(s,n)=>{const d=new Date(s+'T00:00:00');d.setDate(d.getDate()+n);return d.getFullYear()+'-'+p2(d.getMonth()+1)+'-'+p2(d.getDate())};
function dtEmpty(d,B){
 const e=d.empty,nx=e.onTheWay.next,p=e.pickup||{recent:[]},nd=p.nextDays;
 let habit;
 if(!p.last)habit=`<div class="hab"><b>Supplier pickup habit</b><div class="mu" style="margin-top:4px">No pickup recorded yet. After cylinders are marked ${esc(L_OUT)}, the supplier's usual pickup rhythm shows here.</div></div>`;
 else{
  const base=p.next&&p.next<todayStr()?todayStr():p.next;
  habit=`<div class="hab"><b>Supplier pickup habit</b>
   <div class="d-chipg"><span>Last pickup ${esc(fD(p.last))} &middot; ${pl(p.lastCount,'cylinder')}</span>${p.gap!=null?`<span>Usually every ~${p.gap} days</span>`:''}${p.weekday?`<span>Usually on ${esc(p.weekday)}s</span>`:''}</div>
   ${p.next?`<div class="mu" style="margin:4px 0 6px">Next pickup expected <b>${esc(fD(p.next))}</b> (${nd<0?pl(-nd,'day')+' overdue':nd===0?'today':'in '+pl(nd,'day')}).${e.count?` If these ${pl(e.count,'empty','empties')} go then, they should be back around <b>${esc(fD(addDays(base,e.lead.days)))}</b>.`:''}</div>`:''}
   <div class="mu">Latest pickups: ${p.recent.map(x=>esc(fD(x.day))+' '+esc(x.wd.slice(0,3))+' &times;'+x.count).join(' &middot; ')}</div></div>`;
 }
 B.innerHTML=`<div class="d-big"><b>${e.count}</b><span>${e.count===1?'empty':'empties'} waiting to be sent for refill</span></div>
  <div class="mu" style="margin-bottom:8px">Lead time (supplier &rarr; back): ${esc(leadText(e.lead))}</div>
  <div class="mu" style="margin-bottom:6px">${e.onTheWay.count&&nx?`On the way now: <b>${e.onTheWay.count}</b> &middot; next arrival ${esc(fD(nx.eta))} (${inDays(nx.days)})`:'Nothing is on the way right now.'}</div>`+habit+(e.byGas.length?gasTable(e.byGas,true):'');
}
function dtRefill(d,B){
 const f=d.refill;
 if(!f.count){B.innerHTML='<div class="mu">Nothing is at the supplier right now.</div>';return}
 B.innerHTML=`<div class="d-big"><b>${f.count}</b><span>at supplier &middot; ${pl(f.batches.length,'batch','batches')}</span></div>`+
  f.batches.map(b=>`<div class="d-batch ${b.late?'late':''}"><div class="d-bhd"><b>${pl(b.count,'cylinder')}</b><span class="mu">Sent ${esc(fD(b.sent))}</span></div><div class="d-chipg">${Object.keys(b.byGas).map(g=>`<span>${gdot(g)}${esc(g)} &times;${b.byGas[g]}</span>`).join('')}</div><div class="mu">Expected <b>${esc(fD(b.eta))}</b> &middot; ${inDays(b.days)}</div></div>`).join('')+
  `<div class="mu">Expected date = sent date + recent lead time: ${esc(leadText({isDefault:f.isDefault,days:f.leadDays,avg:d.empty.lead.avg,samples:d.empty.lead.samples,last:d.empty.lead.last}))}. It updates by itself with every new delivery.</div>`;
}
function dtDamaged(d,B){
 const list=d.damagedList;
 B.innerHTML=`<div class="d-big"><b>${list.length}</b><span>damaged, waiting to go to supplier</span></div>`+
  (d.damagedInfo.byGas.length?gasTable(d.damagedInfo.byGas,false):'')+
  (list.length?'<div class="d-h">Cylinders</div>'+list.slice(0,40).map(x=>drow(x.id,(x.gas?x.gas+' · ':'')+(x.from?'from '+x.from+' · ':'')+(x.note||'No note')+(x.by?' · by '+x.by:''),phBtn(x.photo),x.since?String(x.since).slice(5,16):'')).join(''):'')+
  '<div class="mu" style="margin-top:8px">The email notice (with photos) is in the Alerts tab.</div>';
}
function showDetail(name){
 const d=D.data,B=$('dDetB'),H=$('dDetH'),box=$('dDet');
 if(!d)return;
 if(D.charts.cDetail){D.charts.cDetail.destroy();delete D.charts.cDetail}
 if(!name||!PC[name]){box.style.setProperty('--dc','var(--line)');H.textContent='Details';B.innerHTML='<div class="mu">Hover or tap a slice to see details. Tap again to lock it.</div>';return}
 const col=PC[name],n=d.parts[name]||0,pc=d.total?Math.round(n/d.total*100):0;
 box.style.setProperty('--dc',col);
 H.innerHTML=`<b style="color:${col}">${esc(LBL(name))}</b> &middot; ${pl(n,'cylinder')} &middot; ${pc}%`+(D.lock===name?' <span class="mu">&#128274; locked</span>':'');
 ({'With units':dtUnits,'Ready at logistics':dtReady,'Empty at logistics':dtEmpty,'Out for refill':dtRefill,'Damaged':dtDamaged})[name](d,B);
}

/* ----- infographic pie (SVG): rounded slices, leader lines with %, percent in the centre ----- */
const PIE={cx:230,cy:170,Ro:118,Ri:64,ST:6,GAP:3,MIN:10,UL:56,H:340,LG:26};
const RAD=Math.PI/180;
const pieFmt=pc=>pc>0&&pc<1?'<1 %':Math.round(pc)+' %';
function piePt(r,a){return [PIE.cx+r*Math.cos(a*RAD),PIE.cy+r*Math.sin(a*RAD)]}
function pieSector(a0,a1){
 const ins=PIE.ST/2,g=ins+PIE.GAP/2,ro=PIE.Ro-ins,ri=PIE.Ri+ins,mid=(a0+a1)/2;
 let o0=a0+Math.asin(g/ro)/RAD,o1=a1-Math.asin(g/ro)/RAD,i0=a0+Math.asin(g/ri)/RAD,i1=a1-Math.asin(g/ri)/RAD;
 if(o1-o0<.6){o0=mid-.3;o1=mid+.3}
 if(i1-i0<.6){i0=mid-.3;i1=mid+.3}
 const P=(r,a)=>piePt(r,a).map(v=>v.toFixed(2)).join(' ');
 return `M${P(ro,o0)}A${ro} ${ro} 0 ${o1-o0>180?1:0} 1 ${P(ro,o1)}L${P(ri,i1)}A${ri} ${ri} 0 ${i1-i0>180?1:0} 0 ${P(ri,i0)}Z`;
}
function drawPie(d){
 const svg=$('pie'),total=d.total,names=Object.keys(PC),{cx,cy,Ro,Ri,H}=PIE;
 $('pieLegend').innerHTML=names.map(n=>`<button data-seg="${esc(n)}" aria-pressed="false"><i style="background:${PC[n]}"></i>${esc(LBL(n))} &middot; ${d.parts[n]||0}</button>`).join('');
 svg.hidden=total===0;$('pieEmpty').hidden=total>0;
 if(!total){svg.innerHTML='';return}
 const items=names.map(n=>({n,v:d.parts[n]||0})).filter(x=>x.v>0);
 /* slices that are too thin get a minimum angle, then everything is scaled back to 360 */
 let disp=items.map(x=>Math.max(x.v/total*360,PIE.MIN));
 const sum=disp.reduce((a,b)=>a+b,0);disp=disp.map(a=>a*360/sum);
 let start=-90;
 items.forEach((it,i)=>{it.a0=start;it.a1=start+disp[i];it.am=(it.a0+it.a1)/2;start=it.a1;it.pc=it.v/total*100});

 let segs='';
 items.forEach(it=>{
  const c=Math.cos(it.am*RAD),s=Math.sin(it.am*RAD);
  const path=items.length===1
   ?`M${cx-Ro+3} ${cy}a${Ro-3} ${Ro-3} 0 1 0 ${2*(Ro-3)} 0a${Ro-3} ${Ro-3} 0 1 0 ${-2*(Ro-3)} 0ZM${cx-Ri-3} ${cy}a${Ri+3} ${Ri+3} 0 1 1 ${2*(Ri+3)} 0a${Ri+3} ${Ri+3} 0 1 1 ${-2*(Ri+3)} 0Z`
   :pieSector(it.a0,it.a1);
  segs+=`<g class="seg" data-seg="${esc(it.n)}" style="--dx:${(c*8).toFixed(1)}px;--dy:${(s*8).toFixed(1)}px"><path d="${path}" fill="${PC[it.n]}" stroke="${PC[it.n]}" stroke-width="${PIE.ST}" stroke-linejoin="round" fill-rule="evenodd"><title>${esc(LBL(it.n))}: ${it.v}</title></path></g>`;
 });

 /* leader lines: keep the labels on each side from touching */
 const L=items.map(it=>{const c=Math.cos(it.am*RAD),s=Math.sin(it.am*RAD),p=piePt((Ro+Ri)/2,it.am);
  return {it,side:c>=0?1:-1,x0:p[0],y0:p[1],y:cy+(Ro+34)*s,xu:cx+(c>=0?1:-1)*Math.max(Math.abs(c)*(Ro+30),40)}});
 [-1,1].forEach(sd=>{
  const g=L.filter(l=>l.side===sd).sort((a,b)=>a.y-b.y);
  g.forEach(l=>{l.y=Math.max(l.y,20)});
  for(let i=1;i<g.length;i++)if(g[i].y-g[i-1].y<PIE.LG)g[i].y=g[i-1].y+PIE.LG;
  if(g.length){const over=g[g.length-1].y-(H-12);if(over>0)g.forEach(l=>{l.y-=over})}
 });
 let lbls='';
 L.forEach(l=>{
  const col=PC[l.it.n],x2=l.xu+l.side*PIE.UL;
  lbls+=`<g class="lbl" data-seg="${esc(l.it.n)}" style="--c:${col}"><polyline class="ld" points="${l.x0.toFixed(1)},${l.y0.toFixed(1)} ${l.xu.toFixed(1)},${l.y.toFixed(1)}"/><circle class="pdot" cx="${l.x0.toFixed(1)}" cy="${l.y0.toFixed(1)}" r="2.6"/><line class="ul" x1="${l.xu.toFixed(1)}" y1="${l.y.toFixed(1)}" x2="${x2.toFixed(1)}" y2="${l.y.toFixed(1)}"/><text class="pl-t" x="${((l.xu+x2)/2).toFixed(1)}" y="${(l.y-7).toFixed(1)}" text-anchor="middle">${pieFmt(l.it.pc)}</text></g>`;
 });

 svg.innerHTML=`<defs><filter id="pshadow" x="-25%" y="-25%" width="150%" height="150%"><feDropShadow dx="0" dy="7" stdDeviation="8" flood-color="#000" flood-opacity=".28"/></filter></defs>
  <circle cx="${cx}" cy="${cy}" r="${Ri-7}" fill="var(--card)"/>
  <g filter="url(#pshadow)">${segs}</g>${lbls}
  <g pointer-events="none" text-anchor="middle"><text id="pcName" class="pc-n" x="${cx}" y="${cy-20}"></text><text id="pcBig" class="pc-b" x="${cx}" y="${cy+12}"></text><text id="pcSub" class="pc-s" x="${cx}" y="${cy+32}"></text></g>`;
}

/* highlight the chosen slice and show its percent in the centre */
function pieSync(){
 const d=D.data;if(!d||!d.total||!$('pcBig'))return;
 const act=D.lock||D.sel;
 document.querySelectorAll('#pie [data-seg]').forEach(g=>g.classList.toggle('on',g.dataset.seg===act));
 document.querySelectorAll('#pieLegend [data-seg]').forEach(b=>b.setAttribute('aria-pressed',String(b.dataset.seg===act)));
 const big=$('pcBig'),nm=$('pcName'),sub=$('pcSub');
 if(act&&PC[act]){
  const n=d.parts[act]||0;
  big.textContent=pieFmt(d.total?n/d.total*100:0).replace(' ','');big.style.fill=PC[act];
  nm.textContent=LBL(act);sub.textContent=pl(n,'cylinder')+(D.lock===act?' · locked':'');
 }else{
  big.textContent=d.total;big.style.fill='var(--tx)';nm.textContent='CYLINDERS';sub.textContent='tap a slice';
 }
}
$('pie').addEventListener('mouseover',e=>{
 if(D.lock)return;
 const g=e.target.closest('[data-seg]');if(!g)return;
 const nm=g.dataset.seg;if(nm!==D.sel){D.sel=nm;showDetail(nm);pieSync()}
});

function dashRender(){
 const d=D.data;if(!d)return;
 $('dSub').textContent='Updated '+new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'})+' · '+(RL[d.range]||'');
 $('dSheet').href=d.sheetUrl||'#';
 const p=d.parts;
 $('dKpis').innerHTML=[['Total cylinders',d.total,'#94a3b8'],['With units',p['With units'],PC['With units']],['Ready at logistics',p['Ready at logistics'],PC['Ready at logistics']],['Empty at logistics',p['Empty at logistics'],PC['Empty at logistics']],[L_OUT,p['Out for refill'],PC['Out for refill']],['Damaged',p['Damaged'],PC['Damaged']]]
  .map(x=>`<div class="d-kpi" style="--c:${x[2]}"><b>${x[1]}</b><span>${esc(x[0])}</span></div>`).join('');
 $('dMvT').textContent='Movements · '+(RL[d.range]||'');
 $('dMv').innerHTML=[['Taken out',d.totals.ASSIGN],['Returned',d.totals.RETURN],[L_OUT,d.totals.SEND_REFILL],[L_BACK,d.totals.RECEIVE_REFILL],['Damaged returns',d.dmgRet]].map(x=>`<div><b>${x[1]}</b><span>${x[0]}</span></div>`).join('');

 drawPie(d);
 showDetail(D.lock||D.sel);
 pieSync();

 const keys=['ASSIGN','RETURN','SEND_REFILL','RECEIVE_REFILL'],any=keys.some(k=>d.trend.series[k].some(v=>v>0));
 chart('cTrend','line',{labels:d.trend.labels.map(x=>x.slice(5)),datasets:keys.map(k=>({label:AL[k][0],data:d.trend.series[k],borderColor:AL[k][1],backgroundColor:AL[k][1]+'33',fill:true,tension:.35,pointRadius:2,borderWidth:2}))},
  {interaction:{mode:'index',intersect:false},plugins:{legend:{position:'bottom',labels:{boxWidth:12,font:{size:11}}}},scales:{y:{beginAtZero:true,ticks:{precision:0}},x:{ticks:{maxTicksLimit:8,font:{size:10}}}}},!any);

 const od=d.overdue.filter(x=>x.kind==='unit'),orf=d.overdue.filter(x=>x.kind==='refill');
 let h='';
 if(od.length)h+=`<div class="d-h">With units over ${d.limits.unit} days</div>`+od.map(x=>drow(x.id,x.where+(x.gas?' · '+x.gas:''),badge('overdue','#ef4444'),x.days+' days')).join('');
 if(orf.length)h+=`<div class="d-h">At supplier over ${d.limits.refill} days</div>`+orf.map(x=>drow(x.id,x.gas||'Cylinder',badge('late','#d97706'),x.days+' days')).join('');
 if(d.damagedList.length)h+='<div class="d-h">Damaged, waiting to go to supplier</div>'+d.damagedList.map(x=>drow(x.id,(x.gas?x.gas+' · ':'')+(x.from?'from '+x.from+' · ':'')+(x.note||'No note'),badge('damaged','#ef4444')+phBtn(x.photo),'')).join('');
 if(d.untrackedCount)h+=`<div class="d-h">With units, start date unknown (${d.untrackedCount})</div><div class="mu" style="margin-bottom:6px">No record of when these went out, so they are not called overdue. Settings &rarr; Cylinders already at units lets you start tracking.</div>`+d.untracked.slice(0,30).map(x=>drow(x.id,x.where+(x.gas?' · '+x.gas:''),badge('unknown','#64748b'),'')).join('')+(d.untrackedCount>30?`<div class="mu">and ${d.untrackedCount-30} more</div>`:'');
 $('dAttn').innerHTML=h||empty('All clear','Nothing needs attention right now.');
 $('dRecent').innerHTML=d.recent.length?d.recent.map(e=>{const a=e.flag==='Damaged'?['Damaged return','#ef4444']:(AL[e.action]||[e.action,'#555']);return drow(e.id,e.unit+' · '+e.user,badge(a[0],a[1]),e.time.slice(5,16))}).join(''):empty('No activity','Nothing was recorded in this period.');
}

function dashLoad(silent){
 if(silent&&D.busy)return;
 D.busy=true;const my=++D.seq;
 if(!D.data)$('dKpis').innerHTML='<div class="sk"></div><div class="sk"></div><div class="sk"></div>';
 google.script.run
  .withSuccessHandler(r=>{if(my!==D.seq)return;D.busy=false;
   if(!r||!r.ok){if(!silent)toast((r&&r.error)||'Could not load the dashboard.','err');$('dSub').textContent='Could not load';return}
   D.data=r;D.at=Date.now();dashRender()})
  .withFailureHandler(e=>{if(my!==D.seq)return;D.busy=false;$('dSub').textContent='Offline · retrying…';if(!silent)toast('Dashboard error: '+((e&&e.message)||'unknown'),'err')})
  .getDashboardData(S.pin,D.range);
}

function trackHtml(r,q){
 if(!r||!r.ok)return `<div class="msg">${esc((r&&r.error)||'Could not load.')}</div>`;
 if(!r.found){
  const m=r.matches||[];
  return m.length?'<div class="d-h">Did you mean</div>'+m.map(x=>drow(x.id,x.gas||'','','')).join(''):empty('Not found','No cylinder or history for &ldquo;'+esc(q)+'&rdquo;.');
 }
 const c=r.cyl,col=PC[c.status]||'#94a3b8',dmg=/^(damaged|complaint)/i.test(c.condition);
 let h=`<div style="display:flex;justify-content:space-between;gap:10px;margin:10px 0"><div style="min-width:0"><b style="font-size:16px">${esc(r.id)}</b><div class="d-chipg">${(c.gas||[]).map(g=>`<span>${esc(g)}</span>`).join('')}</div><span class="mu d-l">${esc(c.location)} &middot; since ${esc(String(c.since).slice(5,16))} (${c.days<1?'today':pl(c.days,'day')})</span></div><div style="text-align:right">${badge(c.status,col)}<br><span class="tag${dmg?' dm':''}">${esc(c.condition)}</span></div></div><div class="d-h">History</div>`;
 h+=r.trail.map(e=>{const a=AL[e.action]||[e.action,'#555'];
  return `<div class="d-tl"><i style="background:${a[1]}"></i><div style="min-width:0"><b>${esc(a[0])}</b> <span class="mu">${esc(String(e.time).slice(0,16))}</span><span class="mu d-l">${esc(e.note||'')}${e.note?' · ':''}${esc(e.user)}</span><span class="mu d-l">${esc(e.condition)}${e.extra?' · '+esc(e.extra):''}</span>${e.photo?phBtn(e.photo):''}</div></div>`}).join('');
 return h;
}
function track(id){
 id=String(id||'').trim();const out=$('dTrack');
 if(!id){out.innerHTML='';return}
 if(id.length<2){out.innerHTML='<div class="mu">Type at least 2 characters, or tap a cylinder in the lists above.</div>';return}
 const my=++D.tseq;out.innerHTML=skel();
 google.script.run
  .withSuccessHandler(r=>{if(my!==D.tseq)return;try{out.innerHTML=trackHtml(r,id)}catch(e){out.innerHTML='<div class="msg">Could not show this cylinder.</div>'}})
  .withFailureHandler(()=>{if(my!==D.tseq)return;out.innerHTML='<div class="msg">Could not reach the server. Try again.</div>'})
  .getCylinderTrail(S.pin,id);
}
let tt=null;
$('dTrackQ').addEventListener('keydown',e=>{if(e.key==='Enter'){e.preventDefault();clearTimeout(tt);track($('dTrackQ').value)}});
$('dTrackQ').addEventListener('input',()=>{clearTimeout(tt);tt=setTimeout(()=>track($('dTrackQ').value),500)});
$('v-dash').addEventListener('click',e=>{
 const ph=e.target.closest('[data-photo]');
 if(ph){openPhoto(ph.dataset.photo);return}
 const sg=e.target.closest('[data-seg]');
 if(sg){const nm=sg.dataset.seg;D.lock=D.lock===nm?null:nm;D.sel=nm;showDetail(nm);pieSync();return}
 if(e.target.closest('#pie')){D.lock=null;showDetail(D.sel);pieSync();return}
 const c=e.target.closest('[data-dr]');
 if(c){D.range=c.dataset.dr;document.querySelectorAll('#dChips [data-dr]').forEach(b=>b.setAttribute('aria-pressed',b.dataset.dr===D.range));dashLoad(false);return}
 const t=e.target.closest('[data-trk]');
 if(t){$('dTrackQ').value=t.dataset.trk;track(t.dataset.trk);$('dTrackQ').scrollIntoView({block:'center',behavior:'smooth'})}
});

/* ================= shared click / input handlers ================= */
document.addEventListener('click',e=>{
 const md = $('menuDropdown');
 if(e.target.closest('#menuBtn')) { if(md) md.hidden = !md.hidden; return; }
 else if(md && !md.hidden) { md.hidden = true; }

 const t=e.target.closest('[data-go],.modes [data-mode],[data-rm],[data-add],[data-unit],[data-all],[data-cond],[data-rmphoto]');if(!t)return;const d=t.dataset;
 const ae=document.activeElement;if(ae&&ae.dataset&&ae.dataset.nid)ae.blur();
 if(d.go)go(d.go);
 else if(d.unit){S.unit=d.unit;$('gerr').textContent='';fillUnits()}
 else if(d.cond){const r=R(d.cid);r.type=d.cond;if(r.type==='E'){r.reason='';r.note='';r.photo=''}render()}
 else if(d.rmphoto){R(d.rmphoto).photo='';render()}
 else if(d.all){const sel=S.shown.every(x=>S.queue.indexOf(x)>-1);S.queue=sel?S.queue.filter(x=>S.shown.indexOf(x)<0):S.queue.concat(S.shown.filter(x=>S.queue.indexOf(x)<0));render()}
 else if(d.mode){S.mode=d.mode;S.queue=[];S.ret={};$('q').value='';render()}
 else if(d.rm){S.queue=S.queue.filter(x=>x!==d.rm);delete S.ret[d.rm];render()}
 else if(d.add!=null){S.queue.indexOf(d.add)>-1?(S.queue=S.queue.filter(x=>x!==d.add),render()):addId(d.add)}
});
document.addEventListener('input',e=>{const t=e.target;if(t.dataset&&t.dataset.nid){R(t.dataset.nid).note=t.value;t.classList.remove('bad')}
 if(t.dataset&&t.dataset.eid){R(t.dataset.eid).expiry=t.value;t.classList.remove('bad')}});
$('iu').addEventListener('change',rInv);
$('unit').addEventListener('change',e=>{S.pick=e.target.value;S.unit=S.pick==='__logi'?'':S.pick;$('pWrap').hidden=S.pick!=='__logi';$('gerr').textContent=''});
$('refresh').onclick=()=>load(false);
$('go').onclick=askConfirm;$('camStart').onclick=startCam;$('camStop').onclick=stopCam;
$('tUnit').addEventListener('change',e=>{S.target=e.target.value;if(S.mode==='RETURN'){S.queue=[];S.ret={}}render()});
$('change').onclick=()=>{S.pin='';S.role='unit';stopCam();S.queue=[];S.ret={};$('q').value='';go('gate')};
$('hdrLogout').onclick=()=>{S.pin='';S.staff='';S.unit='';S.pick='';S.role='unit';try{localStorage.removeItem('lpgStaff')}catch(e){}stopCam();S.queue=[];S.ret={};$('q').value='';$('staff').value='';D.data=null;M.list=[];S.alertDmg=null;go('gate')};
DESK.addEventListener('change',()=>{render();placeNav()});

/* ================= damage reason + photo (Return screen) ================= */
/* redraw even if a note box still has the cursor (what was typed is already saved) */
function forceRender(){const ae=document.activeElement;if(ae&&ae.dataset&&(ae.dataset.nid||ae.dataset.eid))ae.blur();render()}
function compressImage(file){
 return new Promise((res,rej)=>{
  const fr=new FileReader();
  fr.onerror=()=>rej(new Error('read'));
  fr.onload=()=>{
   const img=new Image();
   img.onerror=()=>rej(new Error('image'));
   img.onload=()=>{
    const k=Math.min(1,1280/Math.max(img.width,img.height)),w=Math.round(img.width*k),h=Math.round(img.height*k),c=document.createElement('canvas');
    c.width=w;c.height=h;c.getContext('2d').drawImage(img,0,0,w,h);
    res(c.toDataURL('image/jpeg',0.72));
   };
   img.src=fr.result;
  };
  fr.readAsDataURL(file);
 });
}
document.addEventListener('change',e=>{
 const t=e.target;if(!t.dataset)return;
 if(t.dataset.rid){const r=R(t.dataset.rid);r.reason=t.value;t.classList.remove('bad');forceRender();return}
 if(t.dataset.pid&&t.files&&t.files[0]){
  const id=t.dataset.pid,f=t.files[0];
  compressImage(f).then(d=>{R(id).photo=d;forceRender()}).catch(()=>toast('Could not read that photo. Try another one.','err'));
  t.value='';
 }
});

/* one expiry date for every selected cylinder (multiple scan) */
$('expAllBtn').onclick=()=>{
 const v=$('expAll').value;
 if(!v)return toast('Choose the expiry date first.','warn');
 if(v<=isoToday())return toast('The expiry date must be after today.','warn');
 S.queue.forEach(id=>{R(id).expiry=v});
 forceRender();
};

/* view a damage photo (dashboard, alerts, track a cylinder) */
function openPhoto(id){
 $('pvB').innerHTML='<div class="mu">Loading photo…</div>';$('pv').hidden=false;
 google.script.run
  .withSuccessHandler(r=>{$('pvB').innerHTML=r&&r.ok?`<img src="${r.data}" alt="Damage photo">`:`<div class="msg">${esc((r&&r.error)||'Could not load the photo.')}</div>`})
  .withFailureHandler(()=>{$('pvB').innerHTML='<div class="msg">Could not reach the server.</div>'})
  .getPhoto(S.pin,id);
}
$('pvClose').onclick=()=>{$('pv').hidden=true};
$('pv').addEventListener('click',e=>{if(e.target===$('pv'))$('pv').hidden=true});
$('aDmgList').addEventListener('click',e=>{const p=e.target.closest('[data-photo]');if(p)openPhoto(p.dataset.photo)});

/* ================= login ================= */
try{$('staff').value=localStorage.getItem('lpgStaff')||''}catch(e){}
$('enter').onclick=()=>{
 const n=$('staff').value.trim(),role=S.pick==='__logi'?'logi':'unit';
 if(role==='unit'&&!S.unit){$('gerr').textContent='Choose your unit.';return}
 if(!n){$('gerr').textContent='Enter your name.';$('staff').focus();return}
 const finish=()=>{S.role=role;if(role==='logi')S.unit='';S.staff=n;S.mode='ASSIGN';try{localStorage.setItem('lpgStaff',n)}catch(e){}$('gerr').textContent='';go('scan')};
 if(role==='unit'){S.pin='';return finish()}
 const pin=$('pin').value;if(!pin){$('gerr').textContent='Enter the staff PIN.';$('pin').focus();return}
 $('enter').disabled=true;$('enter').textContent='Checking…';
 const back=()=>{$('enter').disabled=false;$('enter').textContent='Continue'};
 google.script.run
  .withSuccessHandler(r=>{back();if(r&&r.ok){S.pin=pin;$('pin').value='';finish()}else $('gerr').textContent=(r&&r.message)||'Wrong PIN.'})
  .withFailureHandler(()=>{back();$('gerr').textContent='Could not check the PIN. Try again.'})
  .verifyStaffPin(pin);
};

/* ================= start ================= */
go('gate'); load(false);
(function(){
 const el=$('deep'),id=el?String(el.dataset.id||'').trim():'';
 if(id&&id.indexOf('<'+'?')<0)addId(id);
})();
</script>
</body>
</html>
