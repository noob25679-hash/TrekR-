<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="theme-color" content="#080d08">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="TREKR">
<title>TREKR</title>
<link rel="manifest" href="#" />
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@400;500;600;700&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<style>
  :root {
    --bg: #080d08;
    --bg-card: #0f160f;
    --bg-elevated: #162016;
    --green: #22c55e;
    --green-dark: #16a34a;
    --green-dim: #166534;
    --green-muted: #4ade80;
    --text: #f0fdf4;
    --text-sec: #86efac;
    --text-muted: #4ade80;
    --text-dim: #1f2d1f;
    --border: #162016;
    --border-bright: #22c55e30;
    --danger: #ef4444;
    --warning: #f59e0b;
    --nav-h: 72px;
    --safe-bottom: env(safe-area-inset-bottom, 0px);
  }

  /* ── LIGHT MODE ── */
  body.light-mode {
    --bg: #f0f7f0;
    --bg-card: #ffffff;
    --bg-elevated: #e6f2e6;
    --green: #16a34a;
    --green-dark: #15803d;
    --green-dim: #bbf7d0;
    --green-muted: #16a34a;
    --text: #0a1a0a;
    --text-sec: #166534;
    --text-muted: #15803d;
    --text-dim: #d1fae5;
    --border: #d1fae5;
    --border-bright: #16a34a40;
    --danger: #dc2626;
    --warning: #d97706;
  }
  body.light-mode .profile-hero {
    background: linear-gradient(180deg, #bbf7d0 0%, #f0f7f0 100%);
  }
  body.light-mode .avatar-circle {
    background: linear-gradient(135deg, #16a34a, #15803d);
    border-color: #16a34a;
    box-shadow: 0 0 0 4px #bbf7d0;
  }
  body.light-mode nav {
    background: rgba(240,247,240,0.97);
    border-top: 1px solid #d1fae5;
  }
  body.light-mode .nav-center {
    box-shadow: 0 0 24px rgba(22,163,74,0.4);
  }
  body.light-mode .page-header {
    background: linear-gradient(180deg, #e6f2e6 0%, transparent 100%);
  }
  body.light-mode .modal-sheet {
    background: #ffffff;
    border-top: 1px solid #d1fae5;
  }
  body.light-mode .form-input {
    background: #f0f7f0;
    border-color: #d1fae5;
    color: #0a1a0a;
  }
  body.light-mode .form-input::placeholder { color: #6b7280; }
  body.light-mode .rank-beginner   { background: #dcfce7; color: #15803d; border-color: #86efac; }
  body.light-mode .rank-explorer   { background: #e0f2fe; color: #0369a1; border-color: #7dd3fc; }
  body.light-mode .rank-trailblazer{ background: #ede9fe; color: #7c3aed; border-color: #c4b5fd; }
  body.light-mode .rank-summit     { background: #fef3c7; color: #b45309; border-color: #fcd34d; }
  body.light-mode .rank-legend     { background: #fee2e2; color: #b91c1c; border-color: #fca5a5; }
  body.light-mode .trail-card { box-shadow: 0 4px 16px rgba(0,0,0,0.08); }
  body.light-mode .card-compact { box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
  body.light-mode .stat-card { box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
  body.light-mode .settings-item { border-bottom-color: #d1fae5; }
  body.light-mode .search-wrap { background: #ffffff; border-color: #d1fae5; }
  body.light-mode .search-wrap input { color: #0a1a0a; }
  body.light-mode .search-wrap input::placeholder { color: #9ca3af; }
  body.light-mode .hud-pill { background: rgba(240,247,240,0.95); }
  body.light-mode .trek-type-btn { background: #f0f7f0; }
  body.light-mode .companion-chip { background: #e6f2e6; }
  body.light-mode .cam-hud-top { background: linear-gradient(180deg, rgba(240,247,240,0.6) 0%, transparent 100%); }
  body.light-mode .friend-card { box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
  body.light-mode #rank-tabs { background: #e6f2e6; }
  body.light-mode .eyebrow { color: #16a34a; }
  body.light-mode .toggle { background: #d1fae5; }
  body.light-mode .toggle.on { background: var(--green); }

  /* Light mode toggle button in settings */
  .theme-toggle-wrap {
    display: flex; align-items: center; justify-content: space-between;
    width: 100%;
  }
  .theme-icon { transition: transform 0.4s ease; display: inline-block; }
  body.light-mode .theme-icon { transform: rotate(180deg); }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  html, body {
    width: 100%; height: 100%;
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    overflow: hidden;
    overscroll-behavior: none;
  }

  /* ── App Shell ── */
  #app { width: 100%; height: 100%; display: flex; flex-direction: column; position: relative; }

  .screen {
    position: absolute; inset: 0;
    bottom: calc(var(--nav-h) + var(--safe-bottom));
    display: none; flex-direction: column;
    overflow: hidden;
  }
  .screen.active { display: flex; }

  /* ── Bottom Nav ── */
  #nav {
    position: absolute; bottom: 0; left: 0; right: 0;
    height: calc(var(--nav-h) + var(--safe-bottom));
    padding-bottom: var(--safe-bottom);
    background: rgba(8,13,8,0.96);
    backdrop-filter: blur(24px);
    border-top: 1px solid var(--border-bright);
    display: flex; align-items: center; justify-content: space-around;
    z-index: 1000;
  }

  .nav-btn {
    display: flex; flex-direction: column; align-items: center; gap: 4px;
    padding: 8px 16px; border: none; background: none; cursor: pointer;
    color: var(--text-dim); transition: color 0.2s;
    font-family: 'DM Mono', monospace; font-size: 10px; letter-spacing: 1px; text-transform: uppercase;
    font-weight: 600;
  }
  .nav-btn.active { color: var(--green); }
  .nav-btn svg { width: 28px; height: 28px; stroke-width: 2; }

  .nav-center {
    width: 56px; height: 56px; border-radius: 50%;
    background: linear-gradient(135deg, var(--green), var(--green-dark));
    border: none; cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    box-shadow: 0 0 24px rgba(34,197,94,0.5);
    margin-bottom: 8px;
    transition: transform 0.15s, box-shadow 0.2s;
  }
  .nav-center:active { transform: scale(0.92); }
  .nav-center svg { width: 26px; height: 26px; stroke: white; stroke-width: 2; }

  /* ── Scrollable content ── */
  .scroll { flex: 1; overflow-y: auto; -webkit-overflow-scrolling: touch; padding-bottom: 16px; }
  .scroll::-webkit-scrollbar { display: none; }

  /* ── Page Header ── */
  .page-header {
    padding: 52px 20px 16px;
    background: linear-gradient(180deg, var(--bg-elevated) 0%, transparent 100%);
  }
  .eyebrow { font-family: 'DM Mono', monospace; font-size: 9px; letter-spacing: 3px; color: var(--green); text-transform: uppercase; }
  .page-title { font-family: 'Bebas Neue', sans-serif; font-size: 36px; letter-spacing: 1px; color: var(--text); line-height: 1; margin-top: 2px; }
  .page-sub { font-size: 12px; color: var(--text-muted); margin-top: 4px; }

  /* ── Cards ── */
  .card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 20px;
    overflow: hidden;
    margin-bottom: 12px;
    transition: transform 0.15s;
    active: transform 0.1s;
  }
  .card:active { transform: scale(0.98); }

  .card-img {
    width: 100%; height: 160px; object-fit: cover;
    background: linear-gradient(135deg, #0f2010, #162816);
    display: block; position: relative;
  }
  .card-img-tag {
    width: 100%; height: 160px; object-fit: cover; display: block;
  }
  .card-img-overlay {
    position: absolute; bottom: 0; left: 0; right: 0; height: 60px;
    background: linear-gradient(to top, rgba(8,13,8,0.85), transparent);
  }

  .card-body { padding: 14px; }
  .card-title { font-weight: 700; font-size: 15px; color: var(--text); margin-bottom: 4px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .card-loc { font-size: 11px; color: var(--text-muted); margin-bottom: 10px; }

  .card-meta { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
  .meta-pill {
    font-family: 'DM Mono', monospace; font-size: 9px; font-weight: 500;
    padding: 3px 8px; border-radius: 6px; border: 1px solid;
    text-transform: uppercase; letter-spacing: 0.5px;
  }
  .pill-green { color: var(--green); border-color: var(--green); }
  .pill-yellow { color: var(--warning); border-color: var(--warning); }
  .pill-red { color: var(--danger); border-color: var(--danger); }
  .pill-popular { color: var(--green); border-color: transparent; background: rgba(34,197,94,0.1); }

  .card-actions { display: flex; gap: 8px; margin-top: 12px; }

  /* ── Buttons ── */
  .btn {
    border: none; cursor: pointer; font-family: 'DM Mono', monospace;
    font-size: 10px; letter-spacing: 1.5px; text-transform: uppercase; font-weight: 500;
    padding: 12px 20px; border-radius: 999px; transition: all 0.15s;
    display: flex; align-items: center; gap: 6px;
  }
  .btn:active { transform: scale(0.95); }
  .btn-primary { background: linear-gradient(135deg, var(--green), var(--green-dark)); color: white; box-shadow: 0 4px 20px rgba(34,197,94,0.3); }
  .btn-ghost { background: transparent; color: var(--green); border: 1px solid var(--green); }
  .btn-danger { background: transparent; color: var(--danger); border: 1px solid var(--danger); }
  .btn-sm { padding: 8px 14px; font-size: 9px; }
  .btn-icon { padding: 10px; border-radius: 50%; }

  /* ── Map ── */
  #map-screen { position: relative; }
  #map-viewport { flex: 1; z-index: 0; }

  .map-hud {
    position: absolute; top: 52px; left: 16px; right: 16px;
    display: flex; justify-content: space-between; align-items: center;
    pointer-events: none; z-index: 500;
  }
  .hud-pill {
    background: rgba(8,13,8,0.9); backdrop-filter: blur(16px);
    border: 1px solid var(--border-bright); border-radius: 999px;
    padding: 8px 16px; pointer-events: auto;
    display: flex; align-items: center; gap: 8px;
  }
  .hud-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--text-dim); transition: background 0.3s; }
  .hud-dot.active { background: var(--green); animation: pulse 1.5s infinite; }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }
  .hud-text { font-family: 'DM Mono', monospace; font-size: 10px; color: var(--text-sec); letter-spacing: 1px; }

  .scan-fab {
    position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%);
    z-index: 500;
    background: linear-gradient(135deg, var(--green), var(--green-dark));
    border: none; cursor: pointer; border-radius: 999px;
    padding: 16px 28px; display: flex; align-items: center; gap: 10px;
    box-shadow: 0 0 32px rgba(34,197,94,0.5);
    font-family: 'DM Mono', monospace; font-size: 11px; color: white; letter-spacing: 2px; text-transform: uppercase;
    transition: all 0.2s;
  }
  .scan-fab:active { transform: translateX(-50%) scale(0.95); }
  .scan-fab svg { width: 22px; height: 22px; stroke: white; stroke-width: 2; }

  .map-controls {
    position: absolute; right: 16px; top: 110px; z-index: 500;
    display: flex; flex-direction: column; gap: 8px;
  }
  .map-ctrl-btn {
    width: 42px; height: 42px; border-radius: 21px;
    background: rgba(8,13,8,0.9); border: 1px solid var(--border-bright);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; color: var(--text-sec);
    font-size: 20px; font-weight: 300;
  }

  /* Trail mini sheet on map */
  .trail-sheet {
    position: absolute; bottom: 80px; left: 12px; right: 12px; z-index: 600;
    background: var(--bg-card); border: 1px solid var(--border-bright);
    border-radius: 20px; padding: 14px; display: none;
    flex-direction: row; gap: 12px; align-items: center;
    animation: slideUp 0.25s ease;
  }
  .trail-sheet.show { display: flex; }
  @keyframes slideUp { from{transform:translateY(20px);opacity:0} to{transform:translateY(0);opacity:1} }
  .trail-sheet-icon { font-size: 32px; flex-shrink: 0; }
  .trail-sheet-body { flex: 1; min-width: 0; }
  .trail-sheet-title { font-weight: 700; font-size: 14px; color: var(--text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .trail-sheet-meta { font-size: 11px; color: var(--text-muted); margin-top: 3px; }
  .trail-sheet-close { background: none; border: none; color: var(--text-dim); font-size: 20px; cursor: pointer; padding: 4px; }

  /* ── Feed ── */
  .search-wrap {
    margin: 0 16px 12px;
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 999px; padding: 10px 16px;
    display: flex; align-items: center; gap: 10px;
  }
  .search-wrap svg { width: 16px; height: 16px; stroke: var(--text-muted); flex-shrink: 0; }
  .search-wrap input { flex: 1; background: none; border: none; color: var(--text); font-size: 14px; font-family: 'DM Sans', sans-serif; outline: none; }
  .search-wrap input::placeholder { color: var(--text-dim); }

  .filter-row { display: flex; gap: 8px; padding: 0 16px; margin-bottom: 16px; overflow-x: auto; }
  .filter-row::-webkit-scrollbar { display: none; }
  .filter-chip {
    flex-shrink: 0; padding: 9px 16px; border-radius: 999px;
    border: 1px solid var(--border); background: var(--bg-card);
    font-family: 'DM Mono', monospace; font-size: 11px; letter-spacing: 0.5px;
    color: var(--text-muted); cursor: pointer; text-transform: uppercase;
    transition: all 0.15s; font-weight: 600;
  }
  .filter-chip.active { border-color: var(--green); background: var(--green-dim); color: var(--green); }

  .feed-list { padding: 0 16px; }

  /* ── Compact card ── */
  .card-compact {
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 16px; padding: 12px; margin-bottom: 10px;
    display: flex; gap: 12px; align-items: center;
    transition: transform 0.15s;
  }
  .card-compact:active { transform: scale(0.98); }
  .card-compact-icon { font-size: 28px; width: 52px; height: 52px; border-radius: 14px; background: var(--bg-elevated); display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .card-compact-body { flex: 1; min-width: 0; }
  .card-compact-title { font-weight: 700; font-size: 14px; color: var(--text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .card-compact-sub { font-size: 11px; color: var(--text-muted); margin-top: 3px; display: flex; gap: 10px; }
  .card-compact-action { flex-shrink: 0; }

  /* ── Stats ── */
  .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 0 16px; margin-bottom: 20px; }
  .stat-card {
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 16px; padding: 16px; text-align: center;
  }
  .stat-val { font-family: 'Bebas Neue', sans-serif; font-size: 32px; color: var(--green); line-height: 1; }
  .stat-lbl { font-family: 'DM Mono', monospace; font-size: 8px; letter-spacing: 2px; color: var(--text-muted); margin-top: 4px; text-transform: uppercase; }

  /* ── Profile hero ── */
  .profile-hero {
    background: linear-gradient(180deg, var(--green-dim) 0%, var(--bg) 100%);
    padding: 52px 20px 28px; text-align: center;
  }
  .avatar-circle {
    width: 80px; height: 80px; border-radius: 50%;
    background: var(--green-dim); border: 3px solid var(--green);
    display: flex; align-items: center; justify-content: center;
    font-family: 'Bebas Neue', sans-serif; font-size: 32px; color: white;
    margin: 0 auto 12px;
  }
  .profile-name { font-family: 'Bebas Neue', sans-serif; font-size: 28px; letter-spacing: 1px; }
  .profile-sub { font-size: 12px; color: var(--text-muted); margin-top: 4px; }

  /* ── Settings list ── */
  .settings-list { margin: 0 16px; background: var(--bg-card); border: 1px solid var(--border); border-radius: 16px; overflow: hidden; }
  .settings-item { display: flex; align-items: center; gap: 12px; padding: 16px; border-bottom: 1px solid var(--border); }
  .settings-item:last-child { border-bottom: none; }
  .settings-item-icon { font-size: 18px; width: 32px; text-align: center; }
  .settings-item-label { flex: 1; font-size: 14px; color: var(--text-sec); }
  .toggle { width: 44px; height: 24px; border-radius: 12px; background: var(--border); border: none; cursor: pointer; position: relative; transition: background 0.2s; flex-shrink: 0; }
  .toggle.on { background: var(--green-dim); }
  .toggle::after { content:''; position:absolute; width:18px;height:18px;border-radius:50%;background:var(--text-dim);top:3px;left:3px;transition:all 0.2s; }
  .toggle.on::after { left:23px; background:var(--green); }

  /* ── Log form modal ── */
  .modal-overlay {
    position: fixed; inset: 0; z-index: 2000;
    background: rgba(0,0,0,0.7); backdrop-filter: blur(8px);
    display: none; align-items: flex-end;
  }
  .modal-overlay.show { display: flex; }
  .modal-sheet {
    background: var(--bg-card); border-radius: 24px 24px 0 0;
    width: 100%; padding: 24px; border-top: 1px solid var(--border-bright);
    animation: sheetUp 0.3s ease;
    max-height: 90vh; overflow-y: auto;
  }
  @keyframes sheetUp { from{transform:translateY(100%)} to{transform:translateY(0)} }
  .modal-handle { width: 40px; height: 4px; border-radius: 2px; background: var(--text-dim); margin: 0 auto 20px; }
  .modal-title { font-family: 'Bebas Neue', sans-serif; font-size: 24px; margin-bottom: 20px; }

  .form-group { margin-bottom: 16px; }
  .form-label { font-family: 'DM Mono', monospace; font-size: 9px; letter-spacing: 2px; color: var(--text-muted); text-transform: uppercase; margin-bottom: 6px; display: block; }
  .form-input {
    width: 100%; background: var(--bg-elevated); border: 1px solid var(--border);
    border-radius: 12px; padding: 12px 14px; color: var(--text);
    font-family: 'DM Sans', sans-serif; font-size: 14px; outline: none;
    transition: border-color 0.2s;
  }
  .form-input:focus { border-color: var(--green); }
  .form-input::placeholder { color: var(--text-dim); }
  textarea.form-input { min-height: 80px; resize: none; }

  .star-row { display: flex; gap: 6px; margin-bottom: 16px; }
  .star { font-size: 28px; cursor: pointer; opacity: 0.3; transition: opacity 0.15s; }
  .star.lit { opacity: 1; }

  /* ── Empty state ── */
  .empty-state { text-align: center; padding: 60px 24px; }
  .empty-icon { font-size: 56px; margin-bottom: 16px; }
  .empty-title { font-family: 'Bebas Neue', sans-serif; font-size: 22px; color: var(--text-sec); }
  .empty-sub { font-size: 13px; color: var(--text-muted); margin-top: 8px; line-height: 1.5; }

  /* ── Toast ── */
  .toast {
    position: fixed; top: 52px; left: 50%; transform: translateX(-50%) translateY(-80px);
    z-index: 3000; background: var(--green-dim); border: 1px solid var(--green);
    border-radius: 999px; padding: 10px 20px;
    font-family: 'DM Mono', monospace; font-size: 11px; color: var(--green);
    letter-spacing: 1px; white-space: nowrap;
    transition: transform 0.3s cubic-bezier(0.34,1.56,0.64,1);
  }
  .toast.show { transform: translateX(-50%) translateY(0); }

  /* ── Loading spinner ── */
  .spinner { width: 20px; height: 20px; border: 2px solid rgba(34,197,94,0.2); border-top-color: var(--green); border-radius: 50%; animation: spin 0.7s linear infinite; }
  @keyframes spin { to { transform: rotate(360deg); } }

  /* Section label */
  .section-label { font-family: 'DM Mono', monospace; font-size: 11px; letter-spacing: 1.5px; color: var(--text-muted); text-transform: uppercase; padding: 0 16px; margin-bottom: 10px; font-weight: 600; }

  /* ── Profile Tabs ── */
  .profile-tab { flex:1; padding:13px 4px; border:none; background:none; color:var(--text-dim); font-family:'DM Mono',monospace; font-size:11px; letter-spacing:0.5px; text-transform:uppercase; cursor:pointer; border-bottom:2px solid transparent; transition:all 0.2s; font-weight:600; }
  .profile-tab.active { color:var(--green); border-bottom-color:var(--green); }

  /* ── Friends ── */
  .friend-card { display:flex; align-items:center; gap:12px; background:var(--bg-card); border:1px solid var(--border); border-radius:16px; padding:12px 14px; margin-bottom:10px; }
  .friend-avatar { width:44px; height:44px; border-radius:50%; background:var(--green-dim); border:2px solid var(--green); display:flex; align-items:center; justify-content:center; font-family:'Bebas Neue'; font-size:20px; color:white; flex-shrink:0; }
  .friend-body { flex:1; min-width:0; }
  .friend-name { font-weight:700; font-size:14px; color:var(--text); }
  .friend-meta { font-size:11px; color:var(--text-muted); margin-top:2px; }
  .friend-xp { font-family:'Bebas Neue'; font-size:18px; color:var(--green); }
  .friend-request { background:var(--bg-card); border:1px solid var(--warning); border-radius:16px; padding:12px 14px; margin-bottom:10px; display:flex; align-items:center; gap:10px; }

  /* ── Trek type selector ── */
  .trek-type-row { display:flex; gap:8px; margin-bottom:16px; }
  .trek-type-btn { flex:1; padding:10px 8px; border-radius:12px; border:1px solid var(--border); background:var(--bg-elevated); color:var(--text-sec); font-size:12px; text-align:center; cursor:pointer; transition:all 0.15s; }
  .trek-type-btn.selected { border-color:var(--green); background:rgba(34,197,94,0.1); color:var(--green); }

  /* ── Companions chips ── */
  .companions-wrap { display:flex; flex-wrap:wrap; gap:6px; margin-bottom:10px; }
  .companion-chip { display:flex; align-items:center; gap:6px; background:var(--bg-elevated); border:1px solid var(--border); border-radius:999px; padding:4px 10px; font-size:12px; color:var(--text-sec); }
  .companion-chip button { background:none; border:none; color:var(--text-dim); font-size:14px; cursor:pointer; padding:0; line-height:1; }

  /* ── Rank tabs ── */
  .rank-tab { padding:13px 16px; border:none; background:none; color:var(--text-dim); font-family:'DM Mono',monospace; font-size:11px; letter-spacing:0.5px; text-transform:uppercase; cursor:pointer; border-bottom:2px solid transparent; transition:all 0.2s; white-space:nowrap; flex-shrink:0; font-weight:600; }
  .rank-tab.active { color:var(--green); border-bottom-color:var(--green); }

  .xp-bar-wrap { background: var(--border); border-radius: 999px; height: 8px; overflow: hidden; margin: 8px 0; }
  .xp-bar-fill { height: 100%; border-radius: 999px; background: linear-gradient(90deg, var(--green), #84cc16); transition: width 0.6s cubic-bezier(0.34,1.56,0.64,1); }
  .rank-badge {
    display: inline-flex; align-items: center; gap: 6px;
    padding: 4px 12px; border-radius: 999px; font-family: 'DM Mono', monospace;
    font-size: 10px; letter-spacing: 1px; font-weight: 600; text-transform: uppercase;
  }
  .rank-beginner   { background: #1a2e1a; color: #4ade80; border: 1px solid #4ade80; }
  .rank-explorer   { background: #1a2a3a; color: #38bdf8; border: 1px solid #38bdf8; }
  .rank-trailblazer{ background: #2a1a3a; color: #a78bfa; border: 1px solid #a78bfa; }
  .rank-summit     { background: #3a2a10; color: #fbbf24; border: 1px solid #fbbf24; }
  .rank-legend     { background: #3a1010; color: #f87171; border: 1px solid #f87171; }

  .badge-grid { display: flex; flex-wrap: wrap; gap: 10px; padding: 0 16px; margin-bottom: 20px; }
  .badge-item {
    display: flex; flex-direction: column; align-items: center; gap: 4px;
    width: calc(33.33% - 7px); background: var(--bg-card);
    border: 1px solid var(--border); border-radius: 16px; padding: 12px 8px;
    text-align: center;
  }
  .badge-item.unlocked { border-color: var(--green); background: rgba(34,197,94,0.05); }
  .badge-item.locked { opacity: 0.35; filter: grayscale(1); }
  .badge-icon { font-size: 28px; }
  .badge-name { font-family: 'DM Mono', monospace; font-size: 8px; letter-spacing: 1px; color: var(--text-muted); text-transform: uppercase; }
  .badge-desc { font-size: 9px; color: var(--text-dim); }

  .leaderboard-item {
    display: flex; align-items: center; gap: 12px;
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 16px; padding: 12px 14px; margin-bottom: 8px;
  }
  .leaderboard-item.me { border-color: var(--green); background: rgba(34,197,94,0.05); }
  .leaderboard-rank { font-family: 'Bebas Neue', sans-serif; font-size: 22px; width: 32px; text-align: center; color: var(--text-dim); }
  .leaderboard-rank.gold { color: #fbbf24; }
  .leaderboard-rank.silver { color: #94a3b8; }
  .leaderboard-rank.bronze { color: #b45309; }
  .leaderboard-avatar { width: 40px; height: 40px; border-radius: 50%; background: var(--green-dim); display: flex; align-items: center; justify-content: center; font-family: 'Bebas Neue'; font-size: 18px; color: white; flex-shrink: 0; }
  .leaderboard-body { flex: 1; }
  .leaderboard-name { font-weight: 700; font-size: 14px; color: var(--text); }
  .leaderboard-meta { font-size: 11px; color: var(--text-muted); margin-top: 2px; }
  .leaderboard-xp { font-family: 'Bebas Neue', sans-serif; font-size: 20px; color: var(--green); }

  /* ── Shop ── */
  .shop-cats { display: flex; gap: 8px; padding: 0 16px; margin-bottom: 16px; overflow-x: auto; }
  .shop-cats::-webkit-scrollbar { display: none; }
  .shop-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 0 16px; }
  .shop-card {
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 20px; overflow: hidden;
    transition: transform 0.15s;
  }
  .shop-card:active { transform: scale(0.97); }
  .shop-img { width: 100%; height: 120px; object-fit: cover; background: var(--bg-elevated); display: flex; align-items: center; justify-content: center; font-size: 40px; }
  .shop-body { padding: 10px 12px 12px; }
  .shop-name { font-weight: 700; font-size: 13px; color: var(--text); line-height: 1.3; margin-bottom: 4px; }
  .shop-brand { font-size: 10px; color: var(--text-muted); margin-bottom: 6px; font-family: 'DM Mono', monospace; letter-spacing: 0.5px; }
  .shop-price { font-family: 'Bebas Neue', sans-serif; font-size: 20px; color: var(--green); }
  .shop-price-orig { font-size: 11px; color: var(--text-dim); text-decoration: line-through; margin-left: 4px; }
  .shop-add { width: 100%; margin-top: 8px; }
  .cart-badge {
    position: absolute; top: -6px; right: -6px;
    width: 18px; height: 18px; border-radius: 50%;
    background: var(--danger); color: white;
    font-size: 9px; font-weight: 800; display: none;
    align-items: center; justify-content: center;
  }
  .cart-badge.show { display: flex; }
  .cart-btn-wrap { position: relative; display: inline-flex; }

  /* Cart modal */
  .cart-item { display: flex; align-items: center; gap: 12px; padding: 12px 0; border-bottom: 1px solid var(--border); }
  .cart-item-icon { font-size: 28px; width: 48px; height: 48px; background: var(--bg-elevated); border-radius: 12px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .cart-item-body { flex: 1; }
  .cart-item-name { font-weight: 700; font-size: 13px; color: var(--text); }
  .cart-item-price { font-size: 12px; color: var(--green); margin-top: 2px; }
  .cart-qty { display: flex; align-items: center; gap: 8px; margin-top: 6px; }
  .qty-btn { width: 26px; height: 26px; border-radius: 50%; border: 1px solid var(--border); background: var(--bg-elevated); color: var(--text); font-size: 16px; cursor: pointer; display: flex; align-items: center; justify-content: center; }
  .qty-num { font-family: 'DM Mono', monospace; font-size: 13px; color: var(--text); min-width: 20px; text-align: center; }

  .leaflet-control-zoom { display: none; }
  .leaflet-control-attribution { display: none; }
  .custom-marker {
    display: flex; flex-direction: column; align-items: center; gap: 2px;
    cursor: pointer; transition: transform 0.15s;
  }
  .custom-marker:active { transform: scale(0.9); }
  .marker-icon {
    width: 38px; height: 38px; border-radius: 50%;
    background: var(--green-dark); border: 2.5px solid white;
    display: flex; align-items: center; justify-content: center;
    box-shadow: 0 3px 12px rgba(0,0,0,0.35); font-size: 16px;
  }
  .custom-marker.popular .marker-icon {
    width: 46px; height: 46px; background: var(--green);
    border: 3px solid white; font-size: 20px;
    box-shadow: 0 0 18px rgba(34,197,94,0.6);
  }
  .marker-name {
    background: rgba(8,13,8,0.88); backdrop-filter: blur(8px);
    color: white; font-family: 'DM Sans', sans-serif;
    font-size: 10px; font-weight: 700;
    padding: 3px 7px; border-radius: 6px;
    white-space: nowrap; max-width: 120px;
    overflow: hidden; text-overflow: ellipsis;
    border: 1px solid rgba(34,197,94,0.4);
    box-shadow: 0 2px 6px rgba(0,0,0,0.3);
  }
  .custom-marker.popular .marker-name {
    background: rgba(22,101,52,0.92);
    border-color: rgba(34,197,94,0.7);
    color: #86efac;
  }

  /* ── Sensor Dashboard ── */
  .sensor-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 0 16px; margin-bottom: 16px; }
  .sensor-card { background: var(--bg-card); border: 1px solid var(--border); border-radius: 20px; padding: 16px; text-align: center; position: relative; overflow: hidden; }
  .sensor-card.active-sensor { border-color: var(--green); }
  .sensor-card.warn { border-color: var(--warning); }
  .sensor-card.danger-card { border-color: var(--danger); }
  .sensor-icon { font-size: 28px; margin-bottom: 6px; }
  .sensor-val { font-family: 'Bebas Neue', sans-serif; font-size: 28px; color: var(--green); line-height: 1; }
  .sensor-val.warn { color: var(--warning); }
  .sensor-val.danger { color: var(--danger); }
  .sensor-unit { font-family: 'DM Mono', monospace; font-size: 9px; color: var(--text-muted); letter-spacing: 1px; margin-top: 2px; }
  .sensor-label { font-size: 10px; color: var(--text-dim); margin-top: 4px; text-transform: uppercase; letter-spacing: 0.5px; font-family: 'DM Mono', monospace; }
  .sensor-pulse { position: absolute; top: 10px; right: 10px; width: 8px; height: 8px; border-radius: 50%; background: var(--green); animation: pulse 2s infinite; }
  .sensor-pulse.off { background: var(--text-dim); animation: none; }
  .compass-wrap { display: flex; flex-direction: column; align-items: center; padding: 0 16px; margin-bottom: 16px; }
  .compass-ring { width: 180px; height: 180px; border-radius: 50%; border: 2px solid var(--border-bright); background: var(--bg-card); position: relative; display: flex; align-items: center; justify-content: center; box-shadow: 0 0 30px rgba(34,197,94,0.1); }
  .compass-needle { width: 4px; height: 70px; position: absolute; top: 20px; left: 50%; transform-origin: bottom center; transform: translateX(-50%) rotate(0deg); transition: transform 0.4s ease; border-radius: 2px; background: linear-gradient(180deg, var(--danger) 50%, #4ade80 50%); }
  .compass-dir { position: absolute; font-family: 'Bebas Neue'; font-size: 13px; color: var(--text-muted); }
  .compass-dir.n { top: 8px; left: 50%; transform: translateX(-50%); color: var(--danger); }
  .compass-dir.s { bottom: 8px; left: 50%; transform: translateX(-50%); }
  .compass-dir.e { right: 10px; top: 50%; transform: translateY(-50%); }
  .compass-dir.w { left: 10px; top: 50%; transform: translateY(-50%); }
  .compass-center { width: 12px; height: 12px; border-radius: 50%; background: var(--green); z-index: 1; }
  .tracker-card { margin: 0 16px 16px; background: var(--bg-card); border: 1px solid var(--border); border-radius: 20px; padding: 20px; }
  .tracker-row { display: flex; justify-content: space-around; margin: 16px 0; }
  .tracker-stat { text-align: center; }
  .tracker-stat-val { font-family: 'Bebas Neue'; font-size: 26px; color: var(--green); }
  .tracker-stat-lbl { font-family: 'DM Mono'; font-size: 8px; color: var(--text-muted); letter-spacing: 1px; text-transform: uppercase; }
  .track-btn { width: 64px; height: 64px; border-radius: 50%; border: none; cursor: pointer; font-size: 24px; margin: 0 auto; display: block; transition: all 0.2s; }
  .track-btn.start { background: linear-gradient(135deg, var(--green), var(--green-dark)); box-shadow: 0 0 24px rgba(34,197,94,0.4); }
  .track-btn.stop { background: linear-gradient(135deg, var(--danger), #b91c1c); box-shadow: 0 0 24px rgba(239,68,68,0.4); }
  .track-btn:active { transform: scale(0.92); }
  .batt-bar { height: 10px; border-radius: 5px; background: var(--border); overflow: hidden; margin-top: 6px; }
  .batt-fill { height: 100%; border-radius: 5px; transition: width 1s; }
  .accel-bars { display: flex; gap: 6px; align-items: flex-end; height: 48px; margin-top: 8px; }
  .accel-bar { flex: 1; border-radius: 4px 4px 0 0; transition: height 0.15s; min-height: 3px; }
  .accel-bar.x { background: var(--green); }
  .accel-bar.y { background: #38bdf8; }
  .accel-bar.z { background: #a78bfa; }
  .sensor-card-wide { background: var(--bg-card); border: 1px solid var(--border); border-radius: 20px; padding: 16px; margin: 0 16px 12px; }

  /* ── Camera / AR ── */
  #screen-camera { background: #000; position: absolute; inset: 0; bottom: calc(var(--nav-h) + var(--safe-bottom)); z-index: 10; }
  #cam-video { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: cover; }
  #cam-canvas { position: absolute; inset: 0; width: 100%; height: 100%; display: none; }

  /* AR overlays */
  .ar-compass-bar {
    position: absolute; bottom: 20px; left: 16px; right: 16px;
    background: rgba(8,13,8,0.75); backdrop-filter: blur(12px);
    border: 1px solid rgba(34,197,94,0.4); border-radius: 999px;
    padding: 10px 20px; display: flex; align-items: center; justify-content: space-between;
    z-index: 50;
  }
  .ar-compass-deg { font-family: 'Bebas Neue'; font-size: 24px; color: var(--green); }
  .ar-compass-dir { font-family: 'Bebas Neue'; font-size: 18px; color: white; }
  .ar-compass-arrow { font-size: 22px; transition: transform 0.4s ease; display: inline-block; }

  /* Top HUD */
  .cam-hud-top {
    position: absolute; top: 0; left: 0; right: 0;
    padding: 48px 16px 16px;
    background: linear-gradient(180deg, rgba(0,0,0,0.7) 0%, transparent 100%);
    display: flex; justify-content: space-between; align-items: flex-start;
    z-index: 50;
  }
  .cam-close-btn {
    width: 40px; height: 40px; border-radius: 50%;
    background: rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.2);
    color: white; font-size: 20px; cursor: pointer;
    display: flex; align-items: center; justify-content: center;
  }
  .cam-mode-pill {
    background: rgba(34,197,94,0.2); border: 1px solid var(--green);
    border-radius: 999px; padding: 6px 14px;
    font-family: 'DM Mono'; font-size: 10px; color: var(--green);
    letter-spacing: 1px;
  }

  /* Three dots menu */
  .cam-dots-btn {
    width: 40px; height: 40px; border-radius: 50%;
    background: rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.2);
    color: white; font-size: 20px; cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    position: relative;
  }
  .cam-menu {
    position: absolute; top: 48px; right: 0;
    background: rgba(15,22,15,0.97); backdrop-filter: blur(20px);
    border: 1px solid var(--border-bright); border-radius: 16px;
    overflow: hidden; min-width: 220px; display: none; z-index: 200;
  }
  .cam-menu.show { display: block; }
  .cam-menu-item {
    display: flex; align-items: center; gap: 12px;
    padding: 14px 16px; border-bottom: 1px solid var(--border);
    cursor: pointer; transition: background 0.15s;
  }
  .cam-menu-item:last-child { border-bottom: none; }
  .cam-menu-item:active { background: var(--bg-elevated); }
  .cam-menu-item.active-mode { background: rgba(34,197,94,0.08); }
  .cam-menu-icon { font-size: 20px; width: 28px; text-align: center; }
  .cam-menu-label { font-size: 13px; color: var(--text-sec); font-weight: 600; }
  .cam-menu-sub { font-size: 10px; color: var(--text-muted); margin-top: 1px; }
  .cam-menu-check { margin-left: auto; color: var(--green); font-size: 16px; }

  /* Shutter button */
  .cam-shutter-wrap {
    position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%);
    z-index: 50; display: flex; flex-direction: column; align-items: center; gap: 10px;
  }
  .cam-shutter {
    width: 72px; height: 72px; border-radius: 50%;
    background: rgba(255,255,255,0.15); border: 3px solid white;
    cursor: pointer; display: flex; align-items: center; justify-content: center;
    transition: all 0.15s;
  }
  .cam-shutter:active { transform: scale(0.9); background: rgba(255,255,255,0.3); }
  .cam-shutter-inner { width: 54px; height: 54px; border-radius: 50%; background: white; }
  .cam-shutter-inner.analyzing { background: var(--green); animation: pulse 0.8s infinite; }
  .cam-mode-label { font-family: 'DM Mono'; font-size: 10px; color: rgba(255,255,255,0.8); letter-spacing: 2px; text-transform: uppercase; }

  /* AI Result panel */
  .cam-result {
    position: absolute; left: 12px; right: 12px; bottom: 165px;
    background: rgba(8,13,8,0.92); backdrop-filter: blur(16px);
    border: 1px solid var(--border-bright); border-radius: 20px;
    padding: 16px; z-index: 50;
    animation: slideUp 0.3s ease;
    max-height: 240px; overflow-y: auto;
  }
  .cam-result-title { font-family: 'Bebas Neue'; font-size: 18px; color: var(--green); margin-bottom: 6px; }
  .cam-result-body { font-size: 13px; color: var(--text-sec); line-height: 1.6; }
  .cam-result-close { position: absolute; top: 10px; right: 12px; background: none; border: none; color: var(--text-dim); font-size: 20px; cursor: pointer; }

  /* Landmark AR tags */
  .ar-tag {
    position: absolute; z-index: 40; pointer-events: none;
    background: rgba(8,13,8,0.85); border: 1px solid var(--green);
    border-radius: 10px; padding: 6px 10px;
    font-family: 'DM Mono'; font-size: 9px; color: var(--green);
    letter-spacing: 0.5px; white-space: nowrap;
    animation: fadeIn 0.4s ease;
  }
  @keyframes fadeIn { from{opacity:0;transform:translateY(6px)} to{opacity:1;transform:translateY(0)} }
  .ar-tag::after { content:''; position:absolute; bottom:-6px; left:50%; transform:translateX(-50%); width:1px; height:6px; background:var(--green); }

  /* Crosshair */
  .cam-crosshair {
    position: absolute; top: 50%; left: 50%; transform: translate(-50%,-50%);
    width: 60px; height: 60px; z-index: 40; pointer-events: none;
  }
  .cam-crosshair::before, .cam-crosshair::after {
    content: ''; position: absolute; background: rgba(255,255,255,0.6);
  }
  .cam-crosshair::before { width: 1px; height: 100%; left: 50%; top: 0; }
  .cam-crosshair::after { width: 100%; height: 1px; top: 50%; left: 0; }
  .cam-corner { position: absolute; width: 16px; height: 16px; border-color: white; border-style: solid; opacity: 0.8; }
  .cam-corner.tl { top: 0; left: 0; border-width: 2px 0 0 2px; }
  .cam-corner.tr { top: 0; right: 0; border-width: 2px 2px 0 0; }
  .cam-corner.bl { bottom: 0; left: 0; border-width: 0 0 2px 2px; }
  .cam-corner.br { bottom: 0; right: 0; border-width: 0 2px 2px 0; }
</style>
</head>
<body>
<div id="app">

  <!-- ════════════════════════════════════════════
       SCREEN: FEED
  ════════════════════════════════════════════ -->
  <div class="screen active" id="screen-feed">
    <div class="page-header" style="display:flex;justify-content:space-between;align-items:center">
      <div style="display:flex;align-items:center;gap:10px">
        <svg width="40" height="40" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
          <circle cx="50" cy="50" r="48" fill="#10B981" fill-opacity="0.15"/>
          <circle cx="50" cy="50" r="40" fill="#10B981" stroke="white" stroke-width="4"/>
          <path d="M30 65L50 30L70 65" stroke="white" stroke-width="5" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M20 65L40 30L60 65" stroke="white" stroke-width="5" stroke-linecap="round" stroke-linejoin="round" opacity="0.6"/>
          <path d="M15 65H85" stroke="white" stroke-width="5" stroke-linecap="round"/>
        </svg>
        <div>
          <div style="font-family:'Bebas Neue';font-size:28px;letter-spacing:2px;color:var(--text);line-height:1">TREKR</div>
          <div class="eyebrow" style="font-size:8px">Nearby Trails</div>
        </div>
      </div>
      <div style="display:flex;gap:6px">
        <button class="btn btn-ghost btn-sm" onclick="showScreen('shop')" style="padding:7px 10px">🛒</button>
        <button class="btn btn-ghost btn-sm" onclick="showScreen('logs')" style="padding:7px 10px">📓</button>
        <button class="btn btn-primary btn-sm" onclick="openLogModal()" style="padding:7px 12px">+ Log</button>
      </div>
    </div>

    <div class="search-wrap">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/></svg>
      <input type="search" id="feed-search" placeholder="Search trails..." oninput="filterFeed()">
    </div>

    <div class="filter-row">
      <div class="filter-chip active" data-filter="all" onclick="setFilter(this,'all')">All</div>
      <div class="filter-chip" data-filter="popular" onclick="setFilter(this,'popular')">🔥 Popular</div>
      <div class="filter-chip" data-filter="easy" onclick="setFilter(this,'easy')">🟢 Easy</div>
      <div class="filter-chip" data-filter="moderate" onclick="setFilter(this,'moderate')">🟡 Moderate</div>
      <div class="filter-chip" data-filter="hard" onclick="setFilter(this,'hard')">🔴 Hard</div>
    </div>

    <div class="scroll">
      <div class="feed-list" id="feed-list">
        <div class="empty-state">
          <svg width="80" height="80" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg" style="margin-bottom:8px;animation:pulse 1.5s infinite">
            <circle cx="50" cy="50" r="48" fill="#10B981" fill-opacity="0.1"/>
            <circle cx="50" cy="50" r="40" fill="#10B981" stroke="white" stroke-width="4"/>
            <path d="M30 65L50 30L70 65" stroke="white" stroke-width="5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M20 65L40 30L60 65" stroke="white" stroke-width="5" stroke-linecap="round" stroke-linejoin="round" opacity="0.6"/>
            <path d="M15 65H85" stroke="white" stroke-width="5" stroke-linecap="round"/>
          </svg>
          <div class="empty-title">Finding Trails...</div>
          <div class="empty-sub">Getting your location to discover nearby trails</div>
        </div>
      </div>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: MAP
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-map">
    <div id="map-viewport"></div>

    <div class="map-hud">
      <div class="hud-pill">
        <div class="hud-dot" id="radar-dot"></div>
        <span class="hud-text" id="radar-status">0 TRAILS</span>
      </div>
      <div class="hud-pill" onclick="locateMe()" style="cursor:pointer">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="#22c55e" stroke-width="2"><circle cx="12" cy="12" r="3"/><path d="M12 1v4M12 19v4M1 12h4M19 12h4"/></svg>
        <span class="hud-text">LOCATE</span>
      </div>
    </div>

    <div class="map-controls">
      <div class="map-ctrl-btn" onclick="mapZoom(1)">+</div>
      <div class="map-ctrl-btn" onclick="mapZoom(-1)">−</div>
    </div>

    <div class="trail-sheet" id="trail-sheet">
      <img id="sheet-img" src="" alt="" style="width:64px;height:64px;border-radius:14px;object-fit:cover;flex-shrink:0" onerror="this.style.display='none'">
      <div class="trail-sheet-body">
        <div class="trail-sheet-title" id="sheet-title">Trail Name</div>
        <div class="trail-sheet-meta" id="sheet-meta">Loading...</div>
      </div>
      <div style="display:flex;flex-direction:column;gap:6px">
        <button class="btn btn-primary btn-sm" id="sheet-save-btn" onclick="saveFromSheet()">Save</button>
        <button class="btn btn-ghost btn-sm" onclick="logFromSheet()">Log</button>
      </div>
      <button class="trail-sheet-close" onclick="closeSheet()">×</button>
    </div>

    <button class="scan-fab" id="scan-btn" onclick="scanArea()">
      <svg viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2"><circle cx="12" cy="12" r="2"/><path d="M16.24 7.76a6 6 0 0 1 0 8.49m-8.48-.01a6 6 0 0 1 0-8.49m11.31-2.82a10 10 0 0 1 0 14.14m-14.14 0a10 10 0 0 1 0-14.14"/></svg>
      <span id="scan-label">SCAN AREA</span>
    </button>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: SAVED
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-saved">
    <div class="page-header">
      <div class="eyebrow">Collection</div>
      <div class="page-title">Saved Trails</div>
      <div class="page-sub" id="saved-count">0 trails bookmarked</div>
    </div>
    <div class="scroll">
      <div id="saved-list" style="padding:0 16px"></div>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: LOGS
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-logs">
    <div class="page-header" style="display:flex;justify-content:space-between;align-items:flex-end">
      <div>
        <div class="eyebrow">History</div>
        <div class="page-title">Trek Logs</div>
      </div>
      <button class="btn btn-ghost btn-sm" onclick="history.go(-1)||showScreen('feed')" style="padding:8px 12px">← Back</button>
    </div>
    <div class="scroll">
      <div class="stats-grid" id="stats-grid">
        <div class="stat-card"><div class="stat-val" id="stat-treks">0</div><div class="stat-lbl">Treks</div></div>
        <div class="stat-card"><div class="stat-val" id="stat-km">0</div><div class="stat-lbl">KM Hiked</div></div>
        <div class="stat-card"><div class="stat-val" id="stat-elev">0</div><div class="stat-lbl">Elev (m)</div></div>
        <div class="stat-card"><div class="stat-val" id="stat-rating">—</div><div class="stat-lbl">Avg Rating</div></div>
      </div>
      <div class="section-label">Trek History</div>
      <div id="logs-list" style="padding:0 16px"></div>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: PROFILE
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-profile">
    <!-- Profile Hero -->
    <div class="profile-hero">
      <div class="avatar-circle" id="profile-avatar">T</div>
      <div class="profile-name" id="profile-name">TREKKER</div>
      <div style="margin-top:6px"><span id="profile-rank-badge" class="rank-badge rank-beginner">🥾 Beginner</span></div>
      <div class="profile-sub" style="margin-top:6px;display:flex;align-items:center;gap:6px;justify-content:center">
        <svg width="16" height="16" viewBox="0 0 100 100" fill="none"><circle cx="50" cy="50" r="40" fill="#10B981"/><path d="M30 65L50 30L70 65" stroke="white" stroke-width="7" stroke-linecap="round" stroke-linejoin="round"/><path d="M20 65L40 30L60 65" stroke="white" stroke-width="7" stroke-linecap="round" stroke-linejoin="round" opacity="0.6"/><path d="M15 65H85" stroke="white" stroke-width="7" stroke-linecap="round"/></svg>
        TREKR v1.0 · Built for the mountains
      </div>
      <div style="margin-top:12px">
        <button class="btn btn-ghost btn-sm" onclick="editName()">✏️ Edit Name</button>
      </div>
    </div>

    <!-- Profile Tabs -->
    <div style="display:flex;border-bottom:1px solid var(--border);background:var(--bg-elevated);flex-shrink:0">
      <button class="profile-tab active" id="ptab-stats" onclick="switchProfileTab('stats')">📊 Stats</button>
      <button class="profile-tab" id="ptab-friends" onclick="switchProfileTab('friends')">👥 Friends</button>
      <button class="profile-tab" id="ptab-settings" onclick="switchProfileTab('settings')">⚙️ Settings</button>
    </div>

    <div class="scroll">

      <!-- Stats Panel -->
      <div id="ppanel-stats">
        <div class="stats-grid" style="margin-top:16px">
          <div class="stat-card"><div class="stat-val" id="p-treks">0</div><div class="stat-lbl">Treks</div></div>
          <div class="stat-card"><div class="stat-val" id="p-km">0.0</div><div class="stat-lbl">KM Hiked</div></div>
          <div class="stat-card"><div class="stat-val" id="p-saved">0</div><div class="stat-lbl">Saved</div></div>
          <div class="stat-card"><div class="stat-val" id="p-elev">0</div><div class="stat-lbl">Elev (m)</div></div>
        </div>
        <div class="section-label" style="margin-top:8px">Trek Breakdown</div>
        <div style="margin:0 16px 16px;background:var(--bg-card);border:1px solid var(--border);border-radius:16px;overflow:hidden">
          <div style="display:flex;justify-content:space-between;padding:12px 16px;border-bottom:1px solid var(--border)">
            <span style="color:var(--text-sec);font-size:13px">🧍 Solo Treks</span>
            <span style="color:var(--green);font-family:'DM Mono';font-size:12px" id="p-solo">0</span>
          </div>
          <div style="display:flex;justify-content:space-between;padding:12px 16px;border-bottom:1px solid var(--border)">
            <span style="color:var(--text-sec);font-size:13px">👫 Duo Treks</span>
            <span style="color:var(--green);font-family:'DM Mono';font-size:12px" id="p-duo">0</span>
          </div>
          <div style="display:flex;justify-content:space-between;padding:12px 16px">
            <span style="color:var(--text-sec);font-size:13px">👥 Group Treks</span>
            <span style="color:var(--green);font-family:'DM Mono';font-size:12px" id="p-group">0</span>
          </div>
        </div>
        <div style="padding:0 16px 16px">
          <button class="btn btn-ghost" style="width:100%;justify-content:center;margin-bottom:8px" onclick="showScreen('shop')">🛒 Trek Shop</button>
          <button class="btn btn-ghost" style="width:100%;justify-content:center" onclick="showScreen('logs')">📓 View All Trek Logs</button>
        </div>
      </div>

      <!-- Friends Panel -->
      <div id="ppanel-friends" style="display:none;padding:16px">
        <!-- Add friend -->
        <div style="background:var(--bg-card);border:1px solid var(--border);border-radius:16px;padding:16px;margin-bottom:16px">
          <div style="font-family:'DM Mono';font-size:9px;color:var(--text-muted);letter-spacing:2px;margin-bottom:10px">ADD A FRIEND</div>
          <div style="display:flex;gap:8px">
            <input class="form-input" id="friend-input" placeholder="Enter username..." style="flex:1" />
            <button class="btn btn-primary btn-sm" onclick="sendFriendRequest()" style="flex-shrink:0">Add</button>
          </div>
        </div>

        <!-- Pending requests -->
        <div id="friend-requests-section" style="display:none">
          <div class="section-label" style="padding:0;margin-bottom:8px">📬 Pending Requests</div>
          <div id="friend-requests-list"></div>
        </div>

        <!-- Friends list -->
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
          <div class="section-label" style="padding:0">Your Friends</div>
          <span style="font-family:'DM Mono';font-size:9px;color:var(--text-muted)" id="friend-count">0 friends</span>
        </div>
        <div id="friends-list">
          <div class="empty-state" style="padding:32px 0">
            <div class="empty-icon">👥</div>
            <div class="empty-title">No Friends Yet</div>
            <div class="empty-sub">Add friends to see them on the leaderboard and challenge them to battles!</div>
          </div>
        </div>
      </div>

      <!-- Settings Panel -->
      <div id="ppanel-settings" style="display:none">
        <div class="section-label" style="margin-top:16px">Settings</div>
        <div class="settings-list">
          <div class="settings-item">
            <div class="settings-item-icon">🔔</div>
            <div class="settings-item-label">Notifications</div>
            <button class="toggle on" onclick="this.classList.toggle('on')"></button>
          </div>
          <div class="settings-item">
            <div class="settings-item-icon"><span class="theme-icon">🌙</span></div>
            <div class="settings-item-label" id="theme-label">Dark Mode</div>
            <button class="toggle on" id="theme-toggle" onclick="toggleTheme()"></button>
          </div>
          <div class="settings-item">
            <div class="settings-item-icon">📍</div>
            <div class="settings-item-label">Location Access</div>
            <button class="toggle on" onclick="this.classList.toggle('on')"></button>
          </div>
          <div class="settings-item">
            <div class="settings-item-icon">💾</div>
            <div class="settings-item-label">Data stored locally on device</div>
            <span style="font-size:11px;color:var(--green)">ON</span>
          </div>
        </div>
        <div style="padding:20px 16px 40px">
          <button class="btn btn-danger" style="width:100%;justify-content:center" onclick="clearData()">🗑️ Clear All Data</button>
          <div style="text-align:center;margin-top:16px;font-size:11px;color:var(--text-dim);font-family:'DM Mono',monospace">TREKR v1.0 · Built for the mountains</div>
        </div>
      </div>

    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: SENSORS
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-sensors">
    <div class="page-header" style="display:flex;justify-content:space-between;align-items:flex-end">
      <div>
        <div class="eyebrow">Live Data</div>
        <div class="page-title">Sensors</div>
      </div>
      <div id="sensor-status-pill" style="background:var(--bg-card);border:1px solid var(--border);border-radius:999px;padding:6px 12px;display:flex;align-items:center;gap:6px">
        <div class="sensor-pulse off" id="sensor-main-dot"></div>
        <span style="font-family:'DM Mono';font-size:9px;color:var(--text-muted)" id="sensor-main-lbl">OFFLINE</span>
      </div>
    </div>

    <div class="scroll">

      <!-- Live Trek Tracker -->
      <div class="tracker-card">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:4px">
          <div style="font-family:'DM Mono';font-size:9px;letter-spacing:2px;color:var(--text-muted);text-transform:uppercase">Live Trek Tracker</div>
          <div id="tracker-time-display" style="font-family:'DM Mono';font-size:11px;color:var(--green)">00:00:00</div>
        </div>
        <div class="tracker-row">
          <div class="tracker-stat">
            <div class="tracker-stat-val" id="t-dist">0.00</div>
            <div class="tracker-stat-lbl">KM</div>
          </div>
          <div class="tracker-stat">
            <div class="tracker-stat-val" id="t-elev">—</div>
            <div class="tracker-stat-lbl">Elevation M</div>
          </div>
          <div class="tracker-stat">
            <div class="tracker-stat-val" id="t-speed">0.0</div>
            <div class="tracker-stat-lbl">KM/H</div>
          </div>
          <div class="tracker-stat">
            <div class="tracker-stat-val" id="t-pts">0</div>
            <div class="tracker-stat-lbl">GPS Pts</div>
          </div>
        </div>
        <button class="track-btn start" id="track-btn" onclick="toggleTracking()">▶</button>
        <div style="text-align:center;font-family:'DM Mono';font-size:9px;color:var(--text-dim);margin-top:8px" id="track-status">Tap to start tracking your trek</div>
      </div>

      <!-- Sensor grid -->
      <div class="sensor-grid">
        <!-- GPS -->
        <div class="sensor-card active-sensor">
          <div class="sensor-pulse" id="gps-pulse"></div>
          <div class="sensor-icon">📍</div>
          <div class="sensor-val" id="s-lat">—</div>
          <div class="sensor-unit">LATITUDE</div>
          <div class="sensor-val" id="s-lng" style="font-size:18px;margin-top:4px">—</div>
          <div class="sensor-unit">LONGITUDE</div>
          <div class="sensor-label">GPS</div>
        </div>

        <!-- Elevation -->
        <div class="sensor-card active-sensor">
          <div class="sensor-pulse" id="elev-pulse"></div>
          <div class="sensor-icon">🏔️</div>
          <div class="sensor-val" id="s-elev">—</div>
          <div class="sensor-unit">METRES</div>
          <div class="sensor-label">Elevation (API)</div>
        </div>

        <!-- GPS Accuracy -->
        <div class="sensor-card" id="acc-card">
          <div class="sensor-icon">🎯</div>
          <div class="sensor-val" id="s-acc">—</div>
          <div class="sensor-unit">METRES ACCURACY</div>
          <div class="sensor-label">GPS Accuracy</div>
        </div>

        <!-- Speed -->
        <div class="sensor-card active-sensor">
          <div class="sensor-pulse"></div>
          <div class="sensor-icon">💨</div>
          <div class="sensor-val" id="s-speed">0.0</div>
          <div class="sensor-unit">KM/H</div>
          <div class="sensor-label">Speed</div>
        </div>

        <!-- Battery -->
        <div class="sensor-card" id="batt-card">
          <div class="sensor-icon">🔋</div>
          <div class="sensor-val" id="s-batt">—</div>
          <div class="sensor-unit" id="s-batt-status">CHECKING</div>
          <div class="batt-bar"><div class="batt-fill" id="batt-fill" style="width:0%;background:var(--green)"></div></div>
          <div class="sensor-label">Battery</div>
        </div>

        <!-- Heading -->
        <div class="sensor-card active-sensor">
          <div class="sensor-pulse" id="compass-pulse"></div>
          <div class="sensor-icon">🧭</div>
          <div class="sensor-val" id="s-heading">—</div>
          <div class="sensor-unit">DEGREES</div>
          <div class="sensor-val" id="s-heading-dir" style="font-size:18px;margin-top:4px">—</div>
          <div class="sensor-label">Compass</div>
        </div>
      </div>

      <!-- Compass Rose -->
      <div class="section-label">Compass</div>
      <div class="compass-wrap">
        <div class="compass-ring">
          <span class="compass-dir n">N</span>
          <span class="compass-dir s">S</span>
          <span class="compass-dir e">E</span>
          <span class="compass-dir w">W</span>
          <div class="compass-needle" id="compass-needle"></div>
          <div class="compass-center"></div>
        </div>
        <div style="font-family:'Bebas Neue';font-size:22px;color:var(--text);margin-top:10px" id="compass-deg-display">— °</div>
        <div style="font-family:'DM Mono';font-size:10px;color:var(--text-muted);letter-spacing:1px" id="compass-dir-display">CALIBRATING</div>
      </div>

      <!-- Accelerometer -->
      <div class="section-label">Motion Sensor</div>
      <div class="sensor-card-wide">
        <div style="display:flex;justify-content:space-between;margin-bottom:8px">
          <span style="font-family:'DM Mono';font-size:9px;color:var(--text-muted);letter-spacing:1px">ACCELEROMETER</span>
          <span style="font-size:9px;color:var(--text-dim)" id="accel-status">Waiting...</span>
        </div>
        <div style="display:flex;gap:16px;margin-bottom:8px">
          <div style="text-align:center;flex:1">
            <div style="font-family:'Bebas Neue';font-size:22px;color:var(--green)" id="s-ax">0.0</div>
            <div style="font-family:'DM Mono';font-size:8px;color:var(--text-muted)">X AXIS</div>
          </div>
          <div style="text-align:center;flex:1">
            <div style="font-family:'Bebas Neue';font-size:22px;color:#38bdf8" id="s-ay">0.0</div>
            <div style="font-family:'DM Mono';font-size:8px;color:var(--text-muted)">Y AXIS</div>
          </div>
          <div style="text-align:center;flex:1">
            <div style="font-family:'Bebas Neue';font-size:22px;color:#a78bfa" id="s-az">0.0</div>
            <div style="font-family:'DM Mono';font-size:8px;color:var(--text-muted)">Z AXIS</div>
          </div>
        </div>
        <div class="accel-bars">
          <div class="accel-bar x" id="bar-x" style="height:24px"></div>
          <div class="accel-bar y" id="bar-y" style="height:24px"></div>
          <div class="accel-bar z" id="bar-z" style="height:24px"></div>
        </div>
        <div style="display:flex;gap:12px;margin-top:6px;justify-content:center">
          <span style="font-size:9px;color:var(--green)">■ X</span>
          <span style="font-size:9px;color:#38bdf8">■ Y</span>
          <span style="font-size:9px;color:#a78bfa">■ Z</span>
        </div>
      </div>

      <!-- Network -->
      <div class="section-label">Network & Device</div>
      <div class="sensor-grid" style="margin-bottom:80px">
        <div class="sensor-card" id="net-card">
          <div class="sensor-icon">📶</div>
          <div class="sensor-val" id="s-net" style="font-size:16px">—</div>
          <div class="sensor-unit" id="s-net-type">CHECKING</div>
          <div class="sensor-label">Network</div>
        </div>
        <div class="sensor-card">
          <div class="sensor-icon">🌅</div>
          <div class="sensor-val" id="s-time" style="font-size:16px">—</div>
          <div class="sensor-unit" id="s-date">—</div>
          <div class="sensor-label">Local Time</div>
        </div>
      </div>

    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: RANK
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-rank">
    <div class="scroll">
      <div class="page-header">
        <div class="eyebrow">Compete</div>
        <div class="page-title">Your Rank</div>
      </div>

      <!-- XP Card -->
      <div style="margin:0 16px 16px">
        <div style="background:var(--bg-card);border:1px solid var(--border);border-radius:20px;padding:20px">
          <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
            <div>
              <div id="rank-title-display" class="rank-badge rank-beginner">🥾 Beginner</div>
              <div style="font-family:'Bebas Neue';font-size:36px;color:var(--text);margin-top:8px" id="rank-xp-display">0 XP</div>
            </div>
            <div style="text-align:right">
              <div style="font-size:11px;color:var(--text-muted)">Next rank</div>
              <div style="font-size:13px;color:var(--text-sec);font-weight:700" id="rank-next-display">500 XP</div>
            </div>
          </div>
          <div class="xp-bar-wrap">
            <div class="xp-bar-fill" id="xp-bar" style="width:0%"></div>
          </div>
          <div style="display:flex;justify-content:space-between;margin-top:4px">
            <span style="font-size:10px;color:var(--text-dim);font-family:'DM Mono'">0</span>
            <span style="font-size:10px;color:var(--text-dim);font-family:'DM Mono'" id="xp-bar-max">500</span>
          </div>
        </div>
      </div>

      <!-- XP Breakdown -->
      <div class="section-label">How to earn XP</div>
      <div style="margin:0 16px 20px;background:var(--bg-card);border:1px solid var(--border);border-radius:16px;overflow:hidden">
        <div style="display:flex;justify-content:space-between;padding:12px 16px;border-bottom:1px solid var(--border)">
          <span style="color:var(--text-sec);font-size:13px">🥾 Log a trek</span>
          <span style="color:var(--green);font-family:'DM Mono';font-size:12px">+100 XP</span>
        </div>
        <div style="display:flex;justify-content:space-between;padding:12px 16px;border-bottom:1px solid var(--border)">
          <span style="color:var(--text-sec);font-size:13px">📏 Per km hiked</span>
          <span style="color:var(--green);font-family:'DM Mono';font-size:12px">+10 XP</span>
        </div>
        <div style="display:flex;justify-content:space-between;padding:12px 16px;border-bottom:1px solid var(--border)">
          <span style="color:var(--text-sec);font-size:13px">↑ Per 100m elevation</span>
          <span style="color:var(--green);font-family:'DM Mono';font-size:12px">+20 XP</span>
        </div>
        <div style="display:flex;justify-content:space-between;padding:12px 16px">
          <span style="color:var(--text-sec);font-size:13px">⭐ 5-star rating given</span>
          <span style="color:var(--green);font-family:'DM Mono';font-size:12px">+50 XP</span>
        </div>
      </div>

      <!-- Badges -->
      <div class="section-label">Badges</div>
      <div class="badge-grid" id="badge-grid"></div>

      <!-- Leaderboard -->
      <div class="section-label" style="margin-top:4px">Leaderboard</div>
      <div style="padding:0 16px 100px" id="leaderboard-list"></div>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: SHOP
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-shop">
    <div class="page-header" style="display:flex;justify-content:space-between;align-items:flex-end">
      <div>
        <div class="eyebrow">Gear Up</div>
        <div class="page-title">Trek Shop</div>
      </div>
      <div class="cart-btn-wrap">
        <button class="btn btn-ghost btn-sm" onclick="openCart()" style="padding:8px 14px">
          🛒 Cart
        </button>
        <div class="cart-badge" id="cart-badge">0</div>
      </div>
    </div>

    <div class="shop-cats" id="shop-cats">
      <div class="filter-chip active" onclick="filterShop(this,'all')">All</div>
      <div class="filter-chip" onclick="filterShop(this,'boots')">👟 Boots</div>
      <div class="filter-chip" onclick="filterShop(this,'bags')">🎒 Bags</div>
      <div class="filter-chip" onclick="filterShop(this,'poles')">🥢 Poles</div>
      <div class="filter-chip" onclick="filterShop(this,'clothing')">🧥 Clothing</div>
      <div class="filter-chip" onclick="filterShop(this,'safety')">⛑️ Safety</div>
      <div class="filter-chip" onclick="filterShop(this,'hydration')">💧 Hydration</div>
    </div>

    <div class="scroll">
      <div class="shop-grid" id="shop-grid"></div>
      <div style="height:100px"></div>
    </div>
  </div>

  <!-- Cart Modal -->
  <div class="modal-overlay" id="cart-modal" onclick="closeCart(event)">
    <div class="modal-sheet">
      <div class="modal-handle"></div>
      <div class="modal-title">🛒 Your Cart</div>
      <div id="cart-items"></div>
      <div id="cart-empty" style="text-align:center;padding:32px;color:var(--text-muted);display:none">
        <div style="font-size:40px;margin-bottom:12px">🛒</div>
        <div style="font-family:'Bebas Neue';font-size:20px">Cart is Empty</div>
        <div style="font-size:12px;margin-top:6px">Add some gear from the shop!</div>
      </div>
      <div id="cart-total-row" style="padding-top:16px;border-top:1px solid var(--border);margin-top:8px;display:none">
        <div style="display:flex;justify-content:space-between;margin-bottom:16px">
          <span style="font-weight:700;color:var(--text)">Total</span>
          <span style="font-family:'Bebas Neue';font-size:24px;color:var(--green)" id="cart-total">₹0</span>
        </div>
        <button class="btn btn-primary" style="width:100%;justify-content:center" onclick="checkout()">Proceed to Checkout</button>
      </div>
      <button class="btn btn-ghost" style="width:100%;justify-content:center;margin-top:10px" onclick="closeCartModal()">Continue Shopping</button>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       SCREEN: CAMERA (AR)
  ════════════════════════════════════════════ -->
  <div class="screen" id="screen-camera">
    <!-- Live video feed -->
    <video id="cam-video" autoplay playsinline muted></video>
    <canvas id="cam-canvas"></canvas>

    <!-- Crosshair -->
    <div class="cam-crosshair">
      <div class="cam-corner tl"></div>
      <div class="cam-corner tr"></div>
      <div class="cam-corner bl"></div>
      <div class="cam-corner br"></div>
    </div>

    <!-- Top HUD -->
    <div class="cam-hud-top">
      <button class="cam-close-btn" onclick="closeCamera()">✕</button>
      <div class="cam-mode-pill" id="cam-mode-label">🌿 IDENTIFY</div>
      <div style="position:relative">
        <button class="cam-dots-btn" onclick="toggleCamMenu()" id="cam-dots">⋮</button>
        <div class="cam-menu" id="cam-menu">
          <div class="cam-menu-item active-mode" onclick="setCamMode('identify')" id="mode-identify">
            <span class="cam-menu-icon">🌿</span>
            <div>
              <div class="cam-menu-label">Identify Nature</div>
              <div class="cam-menu-sub">Plants, animals, fruits, insects</div>
            </div>
            <span class="cam-menu-check" id="check-identify">✓</span>
          </div>
          <div class="cam-menu-item" onclick="setCamMode('landmark')" id="mode-landmark">
            <span class="cam-menu-icon">🏔️</span>
            <div>
              <div class="cam-menu-label">Peak & Landmark Finder</div>
              <div class="cam-menu-sub">Forts, peaks, waterfalls, rivers</div>
            </div>
            <span class="cam-menu-check" id="check-landmark" style="display:none">✓</span>
          </div>
          <div class="cam-menu-item" onclick="setCamMode('compass')" id="mode-compass">
            <span class="cam-menu-icon">🧭</span>
            <div>
              <div class="cam-menu-label">Compass Overlay</div>
              <div class="cam-menu-sub">Live direction on camera</div>
            </div>
            <span class="cam-menu-check" id="check-compass" style="display:none">✓</span>
          </div>
        </div>
      </div>
    </div>

    <!-- AR Landmark tags (injected by JS) -->
    <div id="ar-tags-container"></div>

    <!-- Compass overlay bar (shown in compass mode) -->
    <div class="ar-compass-bar" id="ar-compass-bar" style="display:none">
      <div style="display:flex;align-items:center;gap:10px">
        <span class="ar-compass-arrow" id="ar-arrow">↑</span>
        <div>
          <div class="ar-compass-dir" id="ar-dir-text">NORTH</div>
          <div style="font-family:'DM Mono';font-size:9px;color:var(--text-muted);letter-spacing:1px" id="ar-coords-text">Getting location...</div>
        </div>
      </div>
      <div style="text-align:right">
        <div class="ar-compass-deg" id="ar-deg-text">0°</div>
        <div style="font-family:'DM Mono';font-size:9px;color:var(--text-muted)">HEADING</div>
      </div>
    </div>

    <!-- Shutter + mode label -->
    <div class="cam-shutter-wrap">
      <div class="cam-shutter" onclick="captureAndAnalyze()" id="cam-shutter-btn">
        <div class="cam-shutter-inner" id="cam-shutter-inner"></div>
      </div>
      <div class="cam-mode-label" id="shutter-label">TAP TO IDENTIFY</div>
    </div>

    <!-- AI Result panel -->
    <div class="cam-result" id="cam-result" style="display:none">
      <button class="cam-result-close" onclick="document.getElementById('cam-result').style.display='none'">✕</button>
      <div class="cam-result-title" id="cam-result-title">Analyzing...</div>
      <div class="cam-result-body" id="cam-result-body"></div>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       BOTTOM NAV
  ════════════════════════════════════════════ -->
  <nav id="nav">
    <button class="nav-btn active" id="nav-feed" onclick="showScreen('feed')">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor"><circle cx="12" cy="12" r="10"/><polygon points="16.24 7.76 14.12 14.12 7.76 16.24 9.88 9.88 16.24 7.76" fill="currentColor" opacity="0.8"/></svg>
      Discover
    </button>
    <button class="nav-btn" id="nav-map" onclick="showScreen('map')">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor"><polygon points="1 6 1 22 8 18 16 22 23 18 23 2 16 6 8 2 1 6"/><line x1="8" y1="2" x2="8" y2="18"/><line x1="16" y1="6" x2="16" y2="22"/></svg>
      Map
    </button>
    <button class="nav-center" id="nav-center-btn" onclick="onCenterPress()">
      <svg id="nav-center-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor"><path d="M12 5v14M5 12h14"/></svg>
    </button>
    <button class="nav-btn" id="nav-rank" onclick="showScreen('rank')">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor"><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/><polyline points="17 6 23 6 23 12"/></svg>
      Ranks
    </button>
    <button class="nav-btn" id="nav-profile" onclick="showScreen('profile')">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor"><path d="M20 21v-2a4 4 0 00-4-4H8a4 4 0 00-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>
      Profile
    </button>
  </nav>

  <!-- ── Floating Camera Side Button ── -->
  <button id="cam-side-btn" onclick="openCamera()" style="
    position: fixed;
    right: 0;
    bottom: calc(var(--nav-h) + var(--safe-bottom) + 80px);
    z-index: 998;
    width: 52px;
    height: 52px;
    border-radius: 16px 0 0 16px;
    background: linear-gradient(135deg, var(--green), var(--green-dark));
    border: none;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: -4px 4px 20px rgba(34,197,94,0.4);
    transition: all 0.2s;
  ">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2">
      <path d="M23 19a2 2 0 01-2 2H3a2 2 0 01-2-2V8a2 2 0 012-2h4l2-3h6l2 3h4a2 2 0 012 2z"/>
      <circle cx="12" cy="13" r="4"/>
    </svg>
  </button>

  <!-- ════════════════════════════════════════════
       START TREK MODAL
  ════════════════════════════════════════════ -->
  <div class="modal-overlay" id="start-trek-modal" onclick="closeStartModal(event)">
    <div class="modal-sheet">
      <div class="modal-handle"></div>
      <div style="text-align:center;margin-bottom:20px">
        <div style="font-size:48px;margin-bottom:8px">🥾</div>
        <div class="modal-title" style="text-align:center;margin-bottom:4px">Start a Trek</div>
        <div style="font-size:13px;color:var(--text-muted)">Give your trek a name before heading out</div>
      </div>
      <div class="form-group">
        <label class="form-label">Trek Name *</label>
        <input class="form-input" id="start-trek-name" placeholder="e.g. Harishchandragad Summit" />
      </div>
      <div style="background:var(--bg-elevated);border-radius:14px;padding:14px;margin-bottom:20px">
        <div style="font-family:'DM Mono';font-size:9px;color:var(--text-muted);letter-spacing:1px;margin-bottom:8px">WHAT GETS RECORDED</div>
        <div style="display:flex;flex-wrap:wrap;gap:8px">
          <span style="font-size:12px;color:var(--text-sec)">📍 GPS Route</span>
          <span style="font-size:12px;color:var(--text-sec)">🏔️ Elevation</span>
          <span style="font-size:12px;color:var(--text-sec)">📏 Distance</span>
          <span style="font-size:12px;color:var(--text-sec)">⏱ Duration</span>
          <span style="font-size:12px;color:var(--text-sec)">💨 Speed</span>
          <span style="font-size:12px;color:var(--text-sec)">🧭 Compass</span>
        </div>
      </div>
      <button class="btn btn-primary" style="width:100%;justify-content:center;font-size:13px" onclick="confirmStartTrek()">
        🔴 Start Recording
      </button>
      <button class="btn btn-ghost" style="width:100%;justify-content:center;margin-top:8px" onclick="closeStartTrekModal()">Cancel</button>
    </div>
  </div>

  <!-- ════════════════════════════════════════════
       LOG TREK MODAL
  ════════════════════════════════════════════ -->
  <div class="modal-overlay" id="log-modal" onclick="closeModal(event)">
    <div class="modal-sheet">
      <div class="modal-handle"></div>
      <div class="modal-title">Log a Trek</div>

      <div class="form-group">
        <label class="form-label">Trail Name *</label>
        <input class="form-input" id="log-trail" placeholder="e.g. Harishchandragad" />
      </div>
      <div class="form-group">
        <label class="form-label">Date Completed</label>
        <input class="form-input" id="log-date" type="date" />
      </div>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:16px">
        <div class="form-group" style="margin:0">
          <label class="form-label">Distance (km)</label>
          <input class="form-input" id="log-dist" type="number" placeholder="12.5" />
        </div>
        <div class="form-group" style="margin:0">
          <label class="form-label">Duration (min)</label>
          <input class="form-input" id="log-dur" type="number" placeholder="240" />
        </div>
      </div>
      <div class="form-group">
        <label class="form-label">Elevation Gain (m)</label>
        <input class="form-input" id="log-elev" type="number" placeholder="1200" />
      </div>

      <!-- Trek Type -->
      <div class="form-group">
        <label class="form-label">Trek Type</label>
        <div class="trek-type-row">
          <div class="trek-type-btn selected" id="type-solo" onclick="selectTrekType('solo')">🧍 Solo</div>
          <div class="trek-type-btn" id="type-duo" onclick="selectTrekType('duo')">👫 Duo<br><span style="font-size:9px;color:var(--green)">+50 XP</span></div>
          <div class="trek-type-btn" id="type-group" onclick="selectTrekType('group')">👥 Group<br><span style="font-size:9px;color:var(--green)">+100 XP</span></div>
        </div>
      </div>

      <!-- Companions (shown for duo/group) -->
      <div class="form-group" id="companions-section" style="display:none">
        <label class="form-label">Trek Companions</label>
        <div class="companions-wrap" id="companions-chips"></div>
        <div style="display:flex;gap:8px">
          <input class="form-input" id="companion-input" placeholder="Add name or pick friend..." style="flex:1" oninput="filterFriendSuggestions()" />
          <button class="btn btn-ghost btn-sm" onclick="addCompanion()" style="flex-shrink:0">Add</button>
        </div>
        <!-- Friend suggestions dropdown -->
        <div id="friend-suggestions" style="background:var(--bg-elevated);border:1px solid var(--border);border-radius:12px;margin-top:4px;display:none;overflow:hidden"></div>
      </div>

      <div class="form-group">
        <label class="form-label">Your Rating</label>
        <div class="star-row" id="star-row">
          <span class="star" data-v="1" onclick="setRating(1)">⭐</span>
          <span class="star" data-v="2" onclick="setRating(2)">⭐</span>
          <span class="star" data-v="3" onclick="setRating(3)">⭐</span>
          <span class="star" data-v="4" onclick="setRating(4)">⭐</span>
          <span class="star" data-v="5" onclick="setRating(5)">⭐</span>
        </div>
      </div>
      <div class="form-group">
        <label class="form-label">Notes</label>
        <textarea class="form-input" id="log-notes" placeholder="How was the trek? Any tips for others..."></textarea>
      </div>
      <button class="btn btn-primary" style="width:100%;justify-content:center" onclick="submitLog()">Save Trek Log</button>
      <button class="btn btn-ghost" style="width:100%;justify-content:center;margin-top:8px" onclick="closeLogModal()">Cancel</button>
    </div>
  </div>

  <!-- Live Trek Banner (shown while tracking) -->
  <div id="live-banner" style="display:none;position:fixed;bottom:calc(var(--nav-h) + var(--safe-bottom));left:0;right:0;z-index:999;background:rgba(239,68,68,0.95);backdrop-filter:blur(8px);padding:8px 16px;display:none;align-items:center;justify-content:space-between;border-top:1px solid rgba(255,255,255,0.2)">
    <div style="display:flex;align-items:center;gap:8px">
      <div style="width:8px;height:8px;border-radius:50%;background:white;animation:pulse 1s infinite"></div>
      <span style="font-family:'DM Mono';font-size:10px;color:white;letter-spacing:1px">REC</span>
      <span style="font-family:'DM Mono';font-size:11px;color:white;font-weight:600" id="banner-name">Trek</span>
    </div>
    <div style="display:flex;align-items:center;gap:12px">
      <span style="font-family:'DM Mono';font-size:11px;color:white" id="banner-time">00:00:00</span>
      <span style="font-family:'DM Mono';font-size:11px;color:white" id="banner-dist">0.00km</span>
      <button onclick="showScreen('sensors')" style="background:rgba(255,255,255,0.2);border:none;border-radius:999px;padding:4px 10px;color:white;font-family:'DM Mono';font-size:9px;cursor:pointer;letter-spacing:1px">VIEW</button>
    </div>
  </div>

  <!-- Toast -->
  <div class="toast" id="toast"></div>
</div>

<script>
// ══════════════════════════════════════════════════════════════
// STATE
// ══════════════════════════════════════════════════════════════
let allTrails = [];
let savedTrails = JSON.parse(localStorage.getItem('trekr_saved') || '[]');
let trekLogs = JSON.parse(localStorage.getItem('trekr_logs') || '[]');
let userName = localStorage.getItem('trekr_name') || 'Trekker';
let currentFilter = 'all';
let activeRating = 0;
let sheetTrail = null;
let mapInstance = null;
let mapMarkers = [];
let userLatLng = null;

// ══════════════════════════════════════════════════════════════
// NAVIGATION
// ══════════════════════════════════════════════════════════════
function showScreen(name) {
  // Block sensors screen if not currently tracking
  if (name === 'sensors' && !isTracking) {
    showToast('⚠️ Start a trek first to see live sensors');
    return;
  }
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('screen-' + name).classList.add('active');
  const nb = document.getElementById('nav-' + name);
  if (nb) nb.classList.add('active');

  if (name === 'map') initMap();
  if (name === 'saved') renderSaved();
  if (name === 'logs') renderLogs();
  if (name === 'profile') { renderProfile(); switchProfileTab('stats'); }
  if (name === 'rank') renderRank();
  if (name === 'shop') renderShop();
  if (name === 'sensors') startSensors();
}

// ══════════════════════════════════════════════════════════════
// TOAST
// ══════════════════════════════════════════════════════════════
function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2500);
}

// ══════════════════════════════════════════════════════════════
// TRAIL DATA (OpenStreetMap / Overpass)
// ══════════════════════════════════════════════════════════════
async function fetchTrails(lat, lng, zoom = 11) {
  const radius = zoom > 12 ? 15000 : zoom > 9 ? 40000 : 80000;

  // Comprehensive query — forts, peaks, hiking routes, waterfalls, viewpoints, hilltops
  const query = `[out:json][timeout:40];(
    node["historic"="fort"]["name"](around:${radius},${lat},${lng});
    way["historic"="fort"]["name"](around:${radius},${lat},${lng});
    relation["historic"="fort"]["name"](around:${radius},${lat},${lng});
    node["historic"="castle"]["name"](around:${radius},${lat},${lng});
    way["historic"="castle"]["name"](around:${radius},${lat},${lng});
    node["natural"="peak"]["name"](around:${radius},${lat},${lng});
    way["natural"="peak"]["name"](around:${radius},${lat},${lng});
    node["natural"="waterfall"]["name"](around:${radius},${lat},${lng});
    node["natural"="hill"]["name"](around:${radius},${lat},${lng});
    node["tourism"="viewpoint"]["name"](around:${radius},${lat},${lng});
    way["tourism"="viewpoint"]["name"](around:${radius},${lat},${lng});
    node["route"="hiking"]["name"](around:${radius},${lat},${lng});
    way["route"="hiking"]["name"](around:${radius},${lat},${lng});
    way["highway"="path"]["name"](around:${radius},${lat},${lng});
    node["leisure"="nature_reserve"]["name"](around:${radius},${lat},${lng});
    way["leisure"="nature_reserve"]["name"](around:${radius},${lat},${lng});
    node["name"~"gad|fort|killa|garh|durg",i](around:${radius},${lat},${lng});
    way["name"~"gad|fort|killa|garh|durg",i](around:${radius},${lat},${lng});
  );out center;`;

  const res = await fetch(`https://overpass-api.de/api/interpreter?data=${encodeURIComponent(query)}`);
  const data = await res.json();

  // Deduplicate by name+type
  const seen = new Set();
  return data.elements.filter(e => {
    if (!e.tags?.name) return false;
    const key = e.tags.name + '_' + (e.tags.historic || e.tags.natural || e.tags.tourism || '');
    if (seen.has(key)) return false;
    seen.add(key);
    return true;
  }).map(el => {
    const tags = el.tags || {};

    // Determine type icon and label
    let typeIcon = '⛰️', typeLabel = 'Trail', difficulty = 'Moderate';
    if (tags.historic === 'fort' || tags.historic === 'castle') {
      typeIcon = '🏯'; typeLabel = 'Fort'; difficulty = 'Hard';
    } else if (tags.natural === 'peak') {
      typeIcon = '🏔️'; typeLabel = 'Peak';
      difficulty = (tags.ele && parseInt(tags.ele) > 1000) ? 'Hard' : 'Moderate';
    } else if (tags.natural === 'waterfall') {
      typeIcon = '💧'; typeLabel = 'Waterfall'; difficulty = 'Easy';
    } else if (tags.natural === 'hill') {
      typeIcon = '⛰️'; typeLabel = 'Hill'; difficulty = 'Easy';
    } else if (tags.tourism === 'viewpoint') {
      typeIcon = '🌄'; typeLabel = 'Viewpoint'; difficulty = 'Easy';
    } else if (tags.leisure === 'nature_reserve') {
      typeIcon = '🌿'; typeLabel = 'Reserve'; difficulty = 'Moderate';
    }

    // Override with OSM difficulty if available
    if (tags.sac_scale) {
      if (['hiking','mountain_hiking'].includes(tags.sac_scale)) difficulty = 'Easy';
      else if (['demanding_mountain_hiking','alpine_hiking'].includes(tags.sac_scale)) difficulty = 'Moderate';
      else difficulty = 'Hard';
    }

    const tagCount = Object.keys(tags).length;
    const rating = parseFloat((tagCount > 6 ? 4.2 + Math.random() * 0.8 : 3.2 + Math.random() * 1.2).toFixed(1));
    const ele = tags.ele ? parseInt(tags.ele) : null;
    const distance = ele ? parseFloat((ele / 300 + Math.random() * 4 + 2).toFixed(1)) : parseFloat((Math.random() * 10 + 2).toFixed(1));

    return {
      id: 'trail_' + el.id,
      title: tags.name,
      location: tags['addr:city'] || tags['addr:village'] || tags['addr:region'] || 'Nearby',
      lat: el.center ? el.center.lat : el.lat,
      lng: el.center ? el.center.lon : el.lon,
      difficulty,
      distance,
      rating,
      elevation: ele,
      typeIcon,
      typeLabel,
      isHighRated: rating >= 4.5,
    };
  }).filter(t => t.lat && t.lng); // only include items with valid coordinates
}

const ICONS = ['⛰️','🏔️','🌄','🗻','🏕️'];
function trailIcon(id) { return ICONS[Math.abs(hashCode(id)) % ICONS.length]; }
function hashCode(s) { let h=0; for(let i=0;i<s.length;i++){h=Math.imul(31,h)+s.charCodeAt(i)|0;} return h; }

const GOOGLE_KEY = 'AIzaSyAOVYRIgupAurZup5y1PRh8Ismb1A3lLao';

// Returns a real Google Street View photo of the exact trail location
function trailImage(id, lat, lng) {
  if (lat && lng) {
    return `https://maps.googleapis.com/maps/api/streetview?size=600x300&location=${lat},${lng}&fov=90&pitch=10&key=${GOOGLE_KEY}`;
  }
  // Fallback if no coords
  return `https://maps.googleapis.com/maps/api/streetview?size=600x300&location=19.2,73.7&fov=90&pitch=10&key=${GOOGLE_KEY}`;
}
function diffClass(d) {
  const dl = (d||'').toLowerCase();
  if(dl.includes('easy')||dl.includes('t1')||dl.includes('t2')) return 'pill-green';
  if(dl.includes('hard')||dl.includes('t5')||dl.includes('t6')) return 'pill-red';
  return 'pill-yellow';
}

// ══════════════════════════════════════════════════════════════
// FEED
// ══════════════════════════════════════════════════════════════
function renderFeed(trails) {
  const list = document.getElementById('feed-list');
  if (!trails.length) {
    list.innerHTML = `<div class="empty-state"><div class="empty-icon">🏔️</div><div class="empty-title">No Trails Found</div><div class="empty-sub">Try moving the map and scanning a different area</div></div>`;
    return;
  }
  list.innerHTML = trails.map(t => {
    const saved = savedTrails.find(s => s.id === t.id);
    return `
    <div class="card">
      <div class="card-img">
        <img class="card-img-tag" src="${trailImage(t.id, t.lat, t.lng)}" alt="${t.title}" loading="lazy" onerror="this.style.display='none'">
        <div class="card-img-overlay"></div>
      </div>
      <div class="card-body">
        <div class="card-title">${t.typeIcon || '⛰️'} ${t.title}</div>
        <div class="card-loc">📍 ${t.location} ${t.typeLabel ? `· <span style="color:var(--green);font-size:10px">${t.typeLabel}</span>` : ''}</div>
        <div class="card-meta">
          <span class="meta-pill ${diffClass(t.difficulty)}">${t.difficulty}</span>
          <span class="meta-pill pill-green">📏 ${t.distance}km</span>
          ${t.elevation ? `<span class="meta-pill pill-yellow">↑ ${t.elevation}m</span>` : `<span class="meta-pill pill-yellow">⭐ ${t.rating}</span>`}
          ${t.isHighRated ? '<span class="meta-pill pill-popular">🔥 Popular</span>' : ''}
        </div>
        <div class="card-actions">
          <button class="btn btn-primary btn-sm" onclick="toggleSave(${JSON.stringify(t).replace(/"/g,'&quot;')})">
            ${saved ? '🔖 Saved' : '+ Save'}
          </button>
          <button class="btn btn-ghost btn-sm" onclick="openLogModal('${t.title.replace(/'/g,"\\'")}')">Log Trek</button>
        </div>
      </div>
    </div>`;
  }).join('');
}

function filterFeed() {
  const q = document.getElementById('feed-search').value.toLowerCase();
  let filtered = allTrails.filter(t => {
    if(q && !t.title.toLowerCase().includes(q) && !t.location.toLowerCase().includes(q)) return false;
    if(currentFilter === 'popular') return t.isHighRated;
    if(currentFilter === 'easy') return (t.difficulty||'').toLowerCase().match(/easy|t1|t2/);
    if(currentFilter === 'moderate') return (t.difficulty||'').toLowerCase().match(/moderate|t3/);
    if(currentFilter === 'hard') return (t.difficulty||'').toLowerCase().match(/hard|t4|t5|t6/);
    return true;
  });
  renderFeed(filtered);
}

function setFilter(el, val) {
  document.querySelectorAll('.filter-chip').forEach(c => c.classList.remove('active'));
  el.classList.add('active');
  currentFilter = val;
  filterFeed();
}

// ══════════════════════════════════════════════════════════════
// MAP
// ══════════════════════════════════════════════════════════════
function initMap() {
  if (mapInstance) { renderMapMarkers(); return; }
  const L = window.L;
  mapInstance = L.map('map-viewport', { zoomControl: false }).setView([19.2, 73.7], 10);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { maxZoom: 19, attribution: '© OpenStreetMap' }).addTo(mapInstance);
  setTimeout(() => {
    mapInstance.invalidateSize();
    renderMapMarkers();
  }, 300);
  if (userLatLng) mapInstance.setView(userLatLng, 12);
  mapInstance.on('zoomend', renderMapMarkers);
}

function renderMapMarkers() {
  if (!mapInstance) return;
  const L = window.L;
  mapMarkers.forEach(m => mapInstance.removeLayer(m));
  mapMarkers = [];
  const zoom = mapInstance.getZoom();

  allTrails.forEach(trail => {
    if (!trail.lat || !trail.lng) return;

    const isPopular = trail.isHighRated;
    const bgColor = isPopular ? '#16a34a' : '#15803d';
    const glowColor = isPopular ? 'rgba(34,197,94,0.7)' : 'rgba(34,197,94,0.3)';
    const size = isPopular ? 44 : 36;

    const icon = L.divIcon({
      className: '',
      html: `<div style="display:flex;flex-direction:column;align-items:center;gap:2px;cursor:pointer">
        <div style="width:${size}px;height:${size}px;border-radius:50%;background:${bgColor};border:2.5px solid white;display:flex;align-items:center;justify-content:center;font-size:${isPopular?20:16}px;box-shadow:0 3px 12px ${glowColor}">${trail.typeIcon || '⛰️'}</div>
        <div style="background:rgba(8,13,8,0.88);color:white;font-family:sans-serif;font-size:10px;font-weight:700;padding:3px 7px;border-radius:6px;white-space:nowrap;max-width:110px;overflow:hidden;text-overflow:ellipsis;border:1px solid rgba(34,197,94,0.5);box-shadow:0 2px 6px rgba(0,0,0,0.4)">${trail.title}</div>
      </div>`,
      iconSize: [120, size + 26],
      iconAnchor: [60, size + 26],
    });
    const m = L.marker([trail.lat, trail.lng], { icon }).addTo(mapInstance);
    m.on('click', () => showTrailSheet(trail));
    mapMarkers.push(m);
  });

  const statusEl = document.getElementById('radar-status');
  if (statusEl) statusEl.textContent = `${mapMarkers.length} TRAILS`;
}

function mapZoom(dir) {
  if (!mapInstance) return;
  dir > 0 ? mapInstance.zoomIn() : mapInstance.zoomOut();
  setTimeout(renderMapMarkers, 400);
}

async function locateMe() {
  if (!navigator.geolocation) return showToast('Location not available');
  showToast('📍 Getting location...');
  navigator.geolocation.getCurrentPosition(pos => {
    userLatLng = [pos.coords.latitude, pos.coords.longitude];
    if (mapInstance) mapInstance.setView(userLatLng, 13);
  }, () => showToast('Could not get location'));
}

async function scanArea() {
  if (!mapInstance) return;
  const btn = document.getElementById('scan-btn');
  const lbl = document.getElementById('scan-label');
  const dot = document.getElementById('radar-dot');

  btn.disabled = true;
  lbl.textContent = 'SCANNING...';
  dot.classList.add('active');

  const center = mapInstance.getCenter();
  const zoom = mapInstance.getZoom();

  try {
    // Full fresh fetch for the current map center
    const found = await fetchTrails(center.lat, center.lng, zoom);
    const existingIds = new Set(allTrails.map(t => t.id));
    const newOnes = found.filter(t => !existingIds.has(t.id));
    allTrails = [...allTrails, ...newOnes];
    renderMapMarkers();
    filterFeed();
    showToast(newOnes.length > 0 ? `✅ Found ${newOnes.length} new places!` : `📍 ${allTrails.length} places loaded in this area`);
  } catch(e) {
    showToast('⚠️ Scan failed — check connection');
  }

  btn.disabled = false;
  lbl.textContent = 'SCAN AREA';
  dot.classList.remove('active');
}

function showTrailSheet(trail) {
  sheetTrail = trail;
  document.getElementById('sheet-img').src = trailImage(trail.id, trail.lat, trail.lng);
  document.getElementById('sheet-img').style.display = 'block';
  document.getElementById('sheet-title').textContent = trail.title;
  document.getElementById('sheet-meta').textContent = `${trail.difficulty} · ${trail.distance}km · ⭐${trail.rating}`;
  const saved = savedTrails.find(s => s.id === trail.id);
  document.getElementById('sheet-save-btn').textContent = saved ? '🔖 Saved' : '+ Save';
  document.getElementById('trail-sheet').classList.add('show');
}

function closeSheet() {
  document.getElementById('trail-sheet').classList.remove('show');
  sheetTrail = null;
}

function saveFromSheet() {
  if (!sheetTrail) return;
  toggleSave(sheetTrail);
  const saved = savedTrails.find(s => s.id === sheetTrail.id);
  document.getElementById('sheet-save-btn').textContent = saved ? '🔖 Saved' : '+ Save';
}

function logFromSheet() {
  if (!sheetTrail) return;
  closeSheet();
  openLogModal(sheetTrail.title);
}

// ══════════════════════════════════════════════════════════════
// SAVED TRAILS
// ══════════════════════════════════════════════════════════════
function toggleSave(trail) {
  const idx = savedTrails.findIndex(s => s.id === trail.id);
  if (idx >= 0) {
    savedTrails.splice(idx, 1);
    showToast('Removed from saved');
  } else {
    savedTrails.push(trail);
    showToast('🔖 Trail saved!');
  }
  localStorage.setItem('trekr_saved', JSON.stringify(savedTrails));
  filterFeed();
  renderProfile();
}

function renderSaved() {
  const list = document.getElementById('saved-list');
  document.getElementById('saved-count').textContent = `${savedTrails.length} trail${savedTrails.length !== 1 ? 's' : ''} bookmarked`;

  if (!savedTrails.length) {
    list.innerHTML = `<div class="empty-state"><div class="empty-icon">🔖</div><div class="empty-title">Nothing Saved Yet</div><div class="empty-sub">Tap the Save button on any trail to keep it here</div></div>`;
    return;
  }

  list.innerHTML = savedTrails.map(t => `
    <div class="card-compact">
      <div class="card-compact-icon" style="padding:0;overflow:hidden;border-radius:14px">
        <img src="${trailImage(t.id, t.lat, t.lng)}" alt="${t.title}" style="width:52px;height:52px;object-fit:cover;display:block" loading="lazy" onerror="this.parentElement.textContent='⛰️'">
      </div>
      <div class="card-compact-body">
        <div class="card-compact-title">${t.title}</div>
        <div class="card-compact-sub">
          <span>${t.difficulty}</span>
          <span>📏 ${t.distance}km</span>
          <span>⭐ ${t.rating}</span>
        </div>
      </div>
      <div class="card-compact-action">
        <button class="btn btn-danger btn-sm btn-icon" onclick="removeSaved('${t.id}')" title="Remove">🗑️</button>
      </div>
    </div>
  `).join('');
}

function removeSaved(id) {
  savedTrails = savedTrails.filter(t => t.id !== id);
  localStorage.setItem('trekr_saved', JSON.stringify(savedTrails));
  renderSaved();
  showToast('Removed');
}

// ══════════════════════════════════════════════════════════════
// TREK LOGS
// ══════════════════════════════════════════════════════════════
function openLogModal(preTitle = '') {
  document.getElementById('log-trail').value = preTitle;
  document.getElementById('log-date').value = new Date().toISOString().split('T')[0];
  document.getElementById('log-dist').value = '';
  document.getElementById('log-dur').value = '';
  document.getElementById('log-elev').value = '';
  document.getElementById('log-notes').value = '';
  setRating(0);
  selectTrekType('solo');
  activeCompanions = [];
  renderCompanionChips();
  document.getElementById('log-modal').classList.add('show');
}

function closeLogModal() {
  document.getElementById('log-modal').classList.remove('show');
}

function closeModal(e) {
  if (e.target === document.getElementById('log-modal')) closeLogModal();
}

function setRating(val) {
  activeRating = val;
  document.querySelectorAll('.star').forEach(s => {
    s.classList.toggle('lit', parseInt(s.dataset.v) <= val);
  });
}

function submitLog() {
  const title = document.getElementById('log-trail').value.trim();
  if (!title) { showToast('⚠️ Enter a trail name'); return; }

  const log = {
    id: 'log_' + Date.now(),
    trailTitle: title,
    completedAt: document.getElementById('log-date').value || new Date().toISOString().split('T')[0],
    distanceKm: parseFloat(document.getElementById('log-dist').value) || null,
    durationMinutes: parseInt(document.getElementById('log-dur').value) || null,
    elevationGain: parseInt(document.getElementById('log-elev').value) || null,
    rating: activeRating || null,
    notes: document.getElementById('log-notes').value.trim() || null,
    trekType: activeTrekType || 'solo',
    companions: [...activeCompanions],
  };

  trekLogs.unshift(log);
  localStorage.setItem('trekr_logs', JSON.stringify(trekLogs));
  closeLogModal();

  let xpEarned = 100
    + Math.floor((log.distanceKm||0)*10)
    + Math.floor((log.elevationGain||0)/100)*20
    + (log.rating===5 ? 50 : 0)
    + (log.trekType === 'duo' ? 50 : log.trekType === 'group' ? 100 : 0);

  showToast(`✅ Trek logged! +${xpEarned} XP 🎉`);
  renderLogs();
  renderProfile();
}

function deleteLog(id) {
  if (!confirm('Delete this trek log?')) return;
  trekLogs = trekLogs.filter(l => l.id !== id);
  localStorage.setItem('trekr_logs', JSON.stringify(trekLogs));
  renderLogs();
  renderProfile();
  showToast('Log deleted');
}

function renderLogs() {
  // Stats
  const total = trekLogs.length;
  const km = trekLogs.reduce((s,l) => s + (l.distanceKm||0), 0).toFixed(1);
  const elev = trekLogs.reduce((s,l) => s + (l.elevationGain||0), 0);
  const rated = trekLogs.filter(l => l.rating);
  const avgR = rated.length ? (rated.reduce((s,l) => s + l.rating, 0) / rated.length).toFixed(1) : '—';

  document.getElementById('stat-treks').textContent = total;
  document.getElementById('stat-km').textContent = km;
  document.getElementById('stat-elev').textContent = elev;
  document.getElementById('stat-rating').textContent = avgR;

  const list = document.getElementById('logs-list');
  if (!trekLogs.length) {
    list.innerHTML = `<div class="empty-state"><div class="empty-icon">📓</div><div class="empty-title">No Logs Yet</div><div class="empty-sub">Tap the + button to log your first trek</div></div>`;
    return;
  }

  list.innerHTML = trekLogs.map(l => {
    const date = new Date(l.completedAt).toLocaleDateString('en-US', { month:'short', day:'numeric', year:'numeric' });
    const meta = [
      l.distanceKm && `📏 ${l.distanceKm}km`,
      l.durationMinutes && `⏱ ${Math.floor(l.durationMinutes/60)}h${l.durationMinutes%60}m`,
      l.elevationGain && `↑${l.elevationGain}m`,
      l.rating && `${'⭐'.repeat(l.rating)}`,
    ].filter(Boolean).join(' · ');

    const typeIcon = l.trekType === 'duo' ? '👫' : l.trekType === 'group' ? '👥' : '🧍';
    const typeLabel = l.trekType === 'duo' ? 'Duo' : l.trekType === 'group' ? 'Group' : 'Solo';
    const companionStr = l.companions?.length
      ? `<div style="font-size:11px;color:var(--text-muted);margin-top:4px">🤝 With: <span style="color:var(--text-sec)">${l.companions.join(', ')}</span></div>`
      : '';

    return `
    <div class="card-compact">
      <div class="card-compact-icon">🥾</div>
      <div class="card-compact-body">
        <div style="display:flex;align-items:center;gap:6px">
          <div class="card-compact-title">${l.trailTitle}</div>
          <span style="font-size:10px;background:var(--bg-elevated);border-radius:6px;padding:2px 6px;color:var(--text-muted)">${typeIcon} ${typeLabel}</span>
        </div>
        <div class="card-compact-sub"><span>${date}</span></div>
        ${meta ? `<div class="card-compact-sub" style="margin-top:3px;font-size:10px">${meta}</div>` : ''}
        ${companionStr}
        ${l.notes ? `<div style="font-size:11px;color:var(--text-dim);margin-top:4px;font-style:italic">${l.notes}</div>` : ''}
      </div>
      <button class="btn btn-danger btn-sm btn-icon" onclick="deleteLog('${l.id}')">🗑️</button>
    </div>`;
  }).join('');
}

// ══════════════════════════════════════════════════════════════
// PROFILE
// ══════════════════════════════════════════════════════════════
function renderProfile() {
  document.getElementById('profile-name').textContent = userName.toUpperCase();
  document.getElementById('profile-avatar').textContent = (userName[0] || 'T').toUpperCase();

  const xp = calcXP(trekLogs);
  const rank = getRank(xp);
  const rankEl = document.getElementById('profile-rank-badge');
  if (rankEl) { rankEl.textContent = rank.title; rankEl.className = `rank-badge ${rank.cls}`; }

  const km = trekLogs.reduce((s,l) => s + (l.distanceKm||0), 0).toFixed(1);
  const elev = trekLogs.reduce((s,l) => s + (l.elevationGain||0), 0);
  document.getElementById('p-treks').textContent = trekLogs.length;
  document.getElementById('p-km').textContent = km;
  document.getElementById('p-saved').textContent = savedTrails.length;
  document.getElementById('p-elev').textContent = elev;

  // Trek type breakdown
  const soloEl = document.getElementById('p-solo');
  const duoEl = document.getElementById('p-duo');
  const groupEl = document.getElementById('p-group');
  if (soloEl) soloEl.textContent = trekLogs.filter(l => (l.trekType||'solo') === 'solo').length;
  if (duoEl) duoEl.textContent = trekLogs.filter(l => l.trekType === 'duo').length;
  if (groupEl) groupEl.textContent = trekLogs.filter(l => l.trekType === 'group').length;
}

function editName() {
  const n = prompt('Enter your name:', userName);
  if (n && n.trim()) {
    userName = n.trim();
    localStorage.setItem('trekr_name', userName);
    renderProfile();
    showToast('✅ Name updated!');
  }
}

function clearData() {
  if (!confirm('Clear ALL saved trails and trek logs? This cannot be undone.')) return;
  savedTrails = [];
  trekLogs = [];
  localStorage.removeItem('trekr_saved');
  localStorage.removeItem('trekr_logs');
  renderProfile();
  renderLogs();
  renderSaved();
  showToast('Data cleared');
}

// ══════════════════════════════════════════════════════════════
// XP & RANK SYSTEM
// ══════════════════════════════════════════════════════════════
const RANKS = [
  { title: '🥾 Beginner',     cls: 'rank-beginner',    min: 0,    max: 500  },
  { title: '🧭 Explorer',     cls: 'rank-explorer',    min: 500,  max: 1500 },
  { title: '⛰️ Trailblazer', cls: 'rank-trailblazer', min: 1500, max: 3000 },
  { title: '🏔️ Summit Seeker',cls: 'rank-summit',     min: 3000, max: 6000 },
  { title: '🌟 Legend',       cls: 'rank-legend',      min: 6000, max: 9999 },
];

const BADGES = [
  { id: 'first_trek',   icon: '🥾', name: 'First Step',    desc: 'Log your first trek',        check: (logs) => logs.length >= 1 },
  { id: 'trek5',        icon: '🎯', name: '5 Treks',        desc: 'Complete 5 treks',           check: (logs) => logs.length >= 5 },
  { id: 'trek10',       icon: '💪', name: '10 Treks',       desc: 'Complete 10 treks',          check: (logs) => logs.length >= 10 },
  { id: 'km50',         icon: '📏', name: '50km Club',      desc: 'Hike 50km total',            check: (logs) => logs.reduce((s,l)=>s+(l.distanceKm||0),0) >= 50 },
  { id: 'km100',        icon: '🛣️', name: '100km Legend',   desc: 'Hike 100km total',           check: (logs) => logs.reduce((s,l)=>s+(l.distanceKm||0),0) >= 100 },
  { id: 'elev1000',     icon: '🏔️', name: 'Sky High',       desc: '1000m elevation total',      check: (logs) => logs.reduce((s,l)=>s+(l.elevationGain||0),0) >= 1000 },
  { id: 'elev5000',     icon: '🌋', name: 'Summit King',    desc: '5000m elevation total',      check: (logs) => logs.reduce((s,l)=>s+(l.elevationGain||0),0) >= 5000 },
  { id: 'perfect',      icon: '⭐', name: 'Perfectionist',  desc: 'Give a 5-star rating',       check: (logs) => logs.some(l=>l.rating===5) },
  { id: 'streak3',      icon: '🔥', name: 'On Fire',        desc: '3 treks in a week',          check: (logs) => checkStreak(logs, 3) },
  { id: 'night',        icon: '🌙', name: 'Night Owl',      desc: 'Log a trek after 8PM',       check: (logs) => logs.some(l=> new Date(l.completedAt).getHours()>=20) },
  { id: 'explorer',     icon: '🧭', name: 'Explorer',       desc: 'Save 5 trails',              check: (_,saved) => saved.length >= 5 },
  { id: 'shopaholic',   icon: '🛒', name: 'Gear Head',      desc: 'Buy from the shop',          check: (_,__,cart) => cart.length > 0 },
];

function checkStreak(logs, n) {
  if (logs.length < n) return false;
  const recent = logs.slice(0, n).map(l => new Date(l.completedAt).getTime());
  const diff = (Math.max(...recent) - Math.min(...recent)) / (1000*60*60*24);
  return diff <= 7;
}

function calcXP(logs) {
  return logs.reduce((total, l) => {
    let xp = 100; // base per trek
    xp += Math.floor((l.distanceKm || 0) * 10);
    xp += Math.floor((l.elevationGain || 0) / 100) * 20;
    if (l.rating === 5) xp += 50;
    return total + xp;
  }, 0);
}

function getRank(xp) {
  for (let i = RANKS.length - 1; i >= 0; i--) {
    if (xp >= RANKS[i].min) return RANKS[i];
  }
  return RANKS[0];
}

function renderRank() {
  const xp = calcXP(trekLogs);
  const rank = getRank(xp);
  const nextRank = RANKS[RANKS.indexOf(rank) + 1];
  const progress = nextRank
    ? ((xp - rank.min) / (nextRank.min - rank.min)) * 100
    : 100;

  document.getElementById('rank-title-display').textContent = rank.title;
  document.getElementById('rank-title-display').className = `rank-badge ${rank.cls}`;
  document.getElementById('rank-xp-display').textContent = `${xp.toLocaleString()} XP`;
  document.getElementById('rank-next-display').textContent = nextRank ? `${nextRank.min.toLocaleString()} XP` : 'MAX RANK';
  document.getElementById('xp-bar').style.width = `${Math.min(progress, 100)}%`;
  document.getElementById('xp-bar-max').textContent = nextRank ? nextRank.min.toLocaleString() : '∞';

  // Badges
  const cart = JSON.parse(localStorage.getItem('trekr_cart') || '[]');
  document.getElementById('badge-grid').innerHTML = BADGES.map(b => {
    const unlocked = b.check(trekLogs, savedTrails, cart);
    return `<div class="badge-item ${unlocked ? 'unlocked' : 'locked'}">
      <div class="badge-icon">${b.icon}</div>
      <div class="badge-name">${b.name}</div>
      <div class="badge-desc">${b.desc}</div>
      ${unlocked ? '<div style="font-size:9px;color:var(--green);margin-top:2px">✓ EARNED</div>' : ''}
    </div>`;
  }).join('');

  // Leaderboard — simulated community + real user
  const userEntry = { name: userName, xp, isMe: true };
  const fakeBoard = [
    { name: 'ArjunTrekker', xp: xp + 4200 + Math.floor(Math.random()*100) },
    { name: 'PriyaHikes',   xp: xp + 2800 + Math.floor(Math.random()*100) },
    { name: 'RohitSummit',  xp: xp + 1500 + Math.floor(Math.random()*100) },
    { name: 'NehaAdventure',xp: xp + 900  + Math.floor(Math.random()*100) },
    userEntry,
    { name: 'VikasPeak',    xp: Math.max(0, xp - 300) },
    { name: 'SnehaTrails',  xp: Math.max(0, xp - 700) },
    { name: 'AmitWalker',   xp: Math.max(0, xp - 1200) },
  ].sort((a, b) => b.xp - a.xp);

  const medalClass = (i) => i === 0 ? 'gold' : i === 1 ? 'silver' : i === 2 ? 'bronze' : '';

  document.getElementById('leaderboard-list').innerHTML = fakeBoard.map((p, i) => `
    <div class="leaderboard-item ${p.isMe ? 'me' : ''}">
      <div class="leaderboard-rank ${medalClass(i)}">${i === 0 ? '🥇' : i === 1 ? '🥈' : i === 2 ? '🥉' : '#' + (i+1)}</div>
      <div class="leaderboard-avatar">${(p.name[0]||'T').toUpperCase()}</div>
      <div class="leaderboard-body">
        <div class="leaderboard-name">${p.name}${p.isMe ? ' 👈 You' : ''}</div>
        <div class="leaderboard-meta">${getRank(p.xp).title}</div>
      </div>
      <div class="leaderboard-xp">${p.xp.toLocaleString()}<span style="font-size:10px;color:var(--text-muted)"> XP</span></div>
    </div>
  `).join('');
}

// ══════════════════════════════════════════════════════════════
// SHOP
// ══════════════════════════════════════════════════════════════
let cart = JSON.parse(localStorage.getItem('trekr_cart') || '[]');
let shopFilter = 'all';

const PRODUCTS = [
  { id:'p1', name:'Trail Pro X Boots', brand:'Quechua', price:2499, orig:3499, cat:'boots', icon:'👟', rating:4.5 },
  { id:'p2', name:'Summit 45L Backpack', brand:'Decathlon', price:1899, orig:2499, cat:'bags', icon:'🎒', rating:4.7 },
  { id:'p3', name:'Carbon Trek Poles', brand:'Black Diamond', price:3299, orig:4500, cat:'poles', icon:'🥢', rating:4.8 },
  { id:'p4', name:'Windproof Jacket', brand:'Columbia', price:2999, orig:4999, cat:'clothing', icon:'🧥', rating:4.6 },
  { id:'p5', name:'First Aid Kit Pro', brand:'SafeTrek', price:799, orig:999, cat:'safety', icon:'⛑️', rating:4.9 },
  { id:'p6', name:'Hydration Bladder 2L', brand:'CamelBak', price:1299, orig:1799, cat:'hydration', icon:'💧', rating:4.7 },
  { id:'p7', name:'Merino Wool Socks', brand:'Darn Tough', price:599, orig:899, cat:'clothing', icon:'🧦', rating:4.8 },
  { id:'p8', name:'Headlamp 350 Lumens', brand:'Petzl', price:1499, orig:2199, cat:'safety', icon:'🔦', rating:4.9 },
  { id:'p9', name:'Trekking Gaiters', brand:'Outdoor Research', price:899, orig:1299, cat:'boots', icon:'🦵', rating:4.4 },
  { id:'p10', name:'Dry Bag Set 3pc', brand:'Sea to Summit', price:1099, orig:1499, cat:'bags', icon:'🗃️', rating:4.6 },
  { id:'p11', name:'Energy Gel Pack x10', brand:'GU Energy', price:699, orig:899, cat:'hydration', icon:'⚡', rating:4.5 },
  { id:'p12', name:'GPS Watch Lite', brand:'Garmin', price:8999, orig:12999, cat:'safety', icon:'⌚', rating:4.8 },
];

function filterShop(el, cat) {
  document.querySelectorAll('.shop-cats .filter-chip').forEach(c => c.classList.remove('active'));
  el.classList.add('active');
  shopFilter = cat;
  renderShop();
}

function renderShop() {
  const filtered = shopFilter === 'all' ? PRODUCTS : PRODUCTS.filter(p => p.cat === shopFilter);
  document.getElementById('shop-grid').innerHTML = filtered.map(p => {
    const inCart = cart.find(c => c.id === p.id);
    return `
    <div class="shop-card">
      <div class="shop-img">${p.icon}</div>
      <div class="shop-body">
        <div class="shop-brand">${p.brand}</div>
        <div class="shop-name">${p.name}</div>
        <div style="display:flex;align-items:baseline;gap:2px">
          <div class="shop-price">₹${p.price.toLocaleString()}</div>
          <div class="shop-price-orig">₹${p.orig.toLocaleString()}</div>
        </div>
        <div style="font-size:9px;color:var(--warning);margin-top:2px">${'★'.repeat(Math.floor(p.rating))} ${p.rating}</div>
        <button class="btn ${inCart ? 'btn-ghost' : 'btn-primary'} btn-sm shop-add" onclick="addToCart('${p.id}')">
          ${inCart ? '✓ In Cart' : '+ Add to Cart'}
        </button>
      </div>
    </div>`;
  }).join('');
  updateCartBadge();
}

function addToCart(id) {
  const product = PRODUCTS.find(p => p.id === id);
  if (!product) return;
  const existing = cart.find(c => c.id === id);
  if (existing) {
    existing.qty = (existing.qty || 1) + 1;
  } else {
    cart.push({ ...product, qty: 1 });
  }
  localStorage.setItem('trekr_cart', JSON.stringify(cart));
  updateCartBadge();
  renderShop();
  showToast(`🛒 ${product.name} added!`);
}

function removeFromCart(id) {
  cart = cart.filter(c => c.id !== id);
  localStorage.setItem('trekr_cart', JSON.stringify(cart));
  updateCartBadge();
  renderCartModal();
}

function changeQty(id, delta) {
  const item = cart.find(c => c.id === id);
  if (!item) return;
  item.qty = Math.max(1, (item.qty || 1) + delta);
  localStorage.setItem('trekr_cart', JSON.stringify(cart));
  renderCartModal();
}

function updateCartBadge() {
  const total = cart.reduce((s, c) => s + (c.qty || 1), 0);
  const badge = document.getElementById('cart-badge');
  badge.textContent = total;
  badge.classList.toggle('show', total > 0);
}

function openCart() {
  renderCartModal();
  document.getElementById('cart-modal').classList.add('show');
}

function closeCartModal() {
  document.getElementById('cart-modal').classList.remove('show');
}

function closeCart(e) {
  if (e.target === document.getElementById('cart-modal')) closeCartModal();
}

function renderCartModal() {
  const itemsEl = document.getElementById('cart-items');
  const emptyEl = document.getElementById('cart-empty');
  const totalRow = document.getElementById('cart-total-row');

  if (!cart.length) {
    itemsEl.innerHTML = '';
    emptyEl.style.display = 'block';
    totalRow.style.display = 'none';
    return;
  }
  emptyEl.style.display = 'none';
  totalRow.style.display = 'block';

  const total = cart.reduce((s, c) => s + c.price * (c.qty || 1), 0);
  document.getElementById('cart-total').textContent = `₹${total.toLocaleString()}`;

  itemsEl.innerHTML = cart.map(c => `
    <div class="cart-item">
      <div class="cart-item-icon">${c.icon}</div>
      <div class="cart-item-body">
        <div class="cart-item-name">${c.name}</div>
        <div class="cart-item-price">₹${(c.price * (c.qty||1)).toLocaleString()}</div>
        <div class="cart-qty">
          <button class="qty-btn" onclick="changeQty('${c.id}',-1)">−</button>
          <span class="qty-num">${c.qty || 1}</span>
          <button class="qty-btn" onclick="changeQty('${c.id}',1)">+</button>
          <button class="btn btn-danger btn-sm" onclick="removeFromCart('${c.id}')" style="margin-left:8px;padding:4px 10px;font-size:9px">Remove</button>
        </div>
      </div>
    </div>`).join('');
}

function checkout() {
  closeCartModal();
  showToast('🚀 Checkout coming soon!');
}

// ══════════════════════════════════════════════════════════════
// SENSOR ENGINE
// ══════════════════════════════════════════════════════════════
let sensorActive = false;
let gpsWatchId = null;
let lastPos = null;
let compassHeading = null;

// Trek Tracker state
let isTracking = false;
let trackName = '';
let trackStartTime = null;
let trackTimerInterval = null;
let trackPoints = [];
let trackDistanceKm = 0;
let trackElevation = null;

function startSensors() {
  if (sensorActive) return;
  sensorActive = true;
  document.getElementById('sensor-main-dot').classList.remove('off');
  document.getElementById('sensor-main-lbl').textContent = 'LIVE';

  // GPS — must be triggered from user gesture context
  if (navigator.geolocation) {
    gpsWatchId = navigator.geolocation.watchPosition(onGpsUpdate, onGpsError, {
      enableHighAccuracy: true, maximumAge: 2000, timeout: 15000
    });
  } else {
    document.getElementById('s-lat').textContent = 'N/A';
  }

  // Compass + Accelerometer — Android Chrome 13+ requires permission request
  requestMotionPermission();

  // Battery
  if (navigator.getBattery) {
    navigator.getBattery().then(batt => {
      updateBattery(batt);
      batt.addEventListener('levelchange', () => updateBattery(batt));
      batt.addEventListener('chargingchange', () => updateBattery(batt));
    }).catch(() => {
      document.getElementById('s-batt').textContent = 'N/A';
    });
  } else {
    document.getElementById('s-batt').textContent = 'N/A';
    document.getElementById('s-batt-status').textContent = 'NOT SUPPORTED';
  }

  // Network + Clock
  updateNetwork();
  window.addEventListener('online', updateNetwork);
  window.addEventListener('offline', updateNetwork);
  updateClock();
  setInterval(updateClock, 1000);
}

async function requestMotionPermission() {
  // iOS 13+ requires explicit permission for DeviceMotion/Orientation
  if (typeof DeviceMotionEvent !== 'undefined' && typeof DeviceMotionEvent.requestPermission === 'function') {
    try {
      const motionPerm = await DeviceMotionEvent.requestPermission();
      const orientPerm = await DeviceOrientationEvent.requestPermission();
      if (motionPerm === 'granted') {
        window.addEventListener('devicemotion', onMotion);
        document.getElementById('accel-status').textContent = 'Active ✓';
      }
      if (orientPerm === 'granted') {
        window.addEventListener('deviceorientation', onOrientation, true);
      }
    } catch(e) {
      document.getElementById('accel-status').textContent = 'Permission denied';
    }
  } else {
    // Android Chrome — just add listeners directly (no permission API needed)
    // But we need to check if events actually fire
    let motionFired = false;
    let orientFired = false;

    function onFirstMotion(e) {
      motionFired = true;
      document.getElementById('accel-status').textContent = 'Active ✓';
      onMotion(e);
    }
    function onFirstOrient(e) {
      if (!orientFired && (e.alpha !== null || e.webkitCompassHeading !== undefined)) {
        orientFired = true;
        document.getElementById('compass-dir-display').textContent = 'ACTIVE';
      }
      onOrientation(e);
    }

    window.addEventListener('devicemotion', onFirstMotion);
    window.addEventListener('deviceorientation', onFirstOrient, true);

    // After 3 seconds, if nothing fired, show helpful message
    setTimeout(() => {
      if (!motionFired) {
        document.getElementById('accel-status').textContent = 'Check browser permissions';
        showPermissionHint();
      }
    }, 3000);
  }
}

function showPermissionHint() {
  // Only show once
  if (document.getElementById('perm-hint')) return;
  const hint = document.createElement('div');
  hint.id = 'perm-hint';
  hint.style.cssText = 'position:fixed;top:60px;left:16px;right:16px;z-index:3000;background:#1a2e1a;border:1px solid var(--green);border-radius:16px;padding:14px 16px;font-size:12px;color:var(--text-sec);line-height:1.6';
  hint.innerHTML = `
    <div style="font-family:'DM Mono';font-size:9px;color:var(--green);letter-spacing:2px;margin-bottom:6px">SENSOR PERMISSIONS</div>
    To enable compass & motion on Android:<br>
    1. Tap the <b>🔒 lock icon</b> in Chrome's address bar<br>
    2. Tap <b>Site Settings</b><br>
    3. Enable <b>Motion sensors</b> → Allow<br>
    4. Reload the page
    <button onclick="this.parentElement.remove()" style="display:block;margin-top:10px;background:var(--green-dim);border:none;border-radius:8px;padding:6px 14px;color:var(--green);font-family:'DM Mono';font-size:9px;cursor:pointer;letter-spacing:1px">GOT IT</button>
  `;
  document.body.appendChild(hint);
  setTimeout(() => hint.remove(), 12000);
}

function stopSensors() {
  if (gpsWatchId !== null) navigator.geolocation.clearWatch(gpsWatchId);
  window.removeEventListener('deviceorientation', onOrientation, true);
  window.removeEventListener('devicemotion', onMotion);
  sensorActive = false;
}

// GPS update
async function onGpsUpdate(pos) {
  const { latitude: lat, longitude: lng, accuracy, speed, altitude } = pos.coords;
  lastPos = pos;

  document.getElementById('s-lat').textContent = lat.toFixed(5);
  document.getElementById('s-lng').textContent = lng.toFixed(5);
  document.getElementById('s-acc').textContent = Math.round(accuracy);
  const accCard = document.getElementById('acc-card');
  accCard.className = `sensor-card ${accuracy < 15 ? 'active-sensor' : accuracy < 40 ? 'warn' : 'danger-card'}`;

  // Speed
  const kmh = speed ? (speed * 3.6).toFixed(1) : '0.0';
  document.getElementById('s-speed').textContent = kmh;
  document.getElementById('t-speed').textContent = kmh;

  // GPS altitude (rough)
  if (altitude) {
    document.getElementById('s-elev').textContent = Math.round(altitude);
    document.getElementById('t-elev').textContent = Math.round(altitude);
    trackElevation = Math.round(altitude);
  }

  // Fetch accurate elevation from Open Elevation API
  try {
    const r = await fetch(`https://api.open-elevation.com/api/v1/lookup?locations=${lat.toFixed(5)},${lng.toFixed(5)}`);
    const d = await r.json();
    if (d.results?.[0]) {
      const elev = Math.round(d.results[0].elevation);
      document.getElementById('s-elev').textContent = elev;
      document.getElementById('t-elev').textContent = elev;
      document.getElementById('elev-pulse').classList.remove('off');
      trackElevation = elev;
    }
  } catch {}

  // Update map if open
  if (mapInstance) mapInstance.setView([lat, lng]);

  document.getElementById('gps-pulse').classList.remove('off');

  // Trek tracking distance
  if (isTracking && trackPoints.length > 0) {
    const prev = trackPoints[trackPoints.length - 1];
    trackDistanceKm += haversine(prev[0], prev[1], lat, lng);
    document.getElementById('t-dist').textContent = trackDistanceKm.toFixed(2);
  }
  if (isTracking) {
    trackPoints.push([lat, lng]);
    document.getElementById('t-pts').textContent = trackPoints.length;
  }
}

function onGpsError(err) {
  const msgs = {
    1: 'DENIED',    // Permission denied
    2: 'UNAVAIL',   // Position unavailable
    3: 'TIMEOUT'    // Timeout
  };
  document.getElementById('s-lat').textContent = msgs[err.code] || 'ERROR';
  document.getElementById('s-lng').textContent = '—';
  document.getElementById('gps-pulse').classList.add('off');

  if (err.code === 1) {
    showToast('⚠️ Location denied — enable in Chrome settings');
    const hint = document.createElement('div');
    hint.style.cssText = 'position:fixed;top:60px;left:16px;right:16px;z-index:3000;background:#2a1010;border:1px solid var(--danger);border-radius:16px;padding:14px 16px;font-size:12px;color:#fca5a5;line-height:1.6';
    hint.innerHTML = `
      <div style="font-family:'DM Mono';font-size:9px;color:var(--danger);letter-spacing:2px;margin-bottom:6px">📍 LOCATION BLOCKED</div>
      To fix GPS on Android Chrome:<br>
      1. Tap <b>🔒 lock icon</b> in address bar<br>
      2. Tap <b>Permissions → Location</b><br>
      3. Set to <b>Allow</b> then reload
      <button onclick="this.parentElement.remove()" style="display:block;margin-top:10px;background:rgba(239,68,68,0.2);border:1px solid var(--danger);border-radius:8px;padding:6px 14px;color:var(--danger);font-family:'DM Mono';font-size:9px;cursor:pointer">GOT IT</button>
    `;
    document.body.appendChild(hint);
    setTimeout(() => hint?.remove(), 15000);
  }
}

// Compass
function onOrientation(e) {
  let heading = e.webkitCompassHeading ?? (e.alpha ? (360 - e.alpha) : null);
  if (heading === null) return;
  heading = Math.round(heading);
  compassHeading = heading;

  document.getElementById('s-heading').textContent = heading;
  document.getElementById('s-heading-dir').textContent = degreesToDir(heading);
  document.getElementById('compass-needle').style.transform = `translateX(-50%) rotate(${heading}deg)`;
  document.getElementById('compass-deg-display').textContent = `${heading}°`;
  document.getElementById('compass-dir-display').textContent = degreesToDir(heading);
  document.getElementById('compass-pulse').classList.remove('off');
}

function degreesToDir(deg) {
  const dirs = ['N','NE','E','SE','S','SW','W','NW','N'];
  return dirs[Math.round(deg / 45) % 8];
}

// Accelerometer
function onMotion(e) {
  const acc = e.accelerationIncludingGravity;
  if (!acc) return;
  const x = (acc.x || 0).toFixed(1);
  const y = (acc.y || 0).toFixed(1);
  const z = (acc.z || 0).toFixed(1);

  document.getElementById('s-ax').textContent = x;
  document.getElementById('s-ay').textContent = y;
  document.getElementById('s-az').textContent = z;

  const maxG = 20;
  document.getElementById('bar-x').style.height = `${Math.min(48, Math.abs(acc.x||0)/maxG*48)}px`;
  document.getElementById('bar-y').style.height = `${Math.min(48, Math.abs(acc.y||0)/maxG*48)}px`;
  document.getElementById('bar-z').style.height = `${Math.min(48, Math.abs(acc.z||0)/maxG*48)}px`;
}

// Battery
function updateBattery(batt) {
  const pct = Math.round(batt.level * 100);
  document.getElementById('s-batt').textContent = `${pct}%`;
  document.getElementById('s-batt-status').textContent = batt.charging ? 'CHARGING ⚡' : 'ON BATTERY';
  const fill = document.getElementById('batt-fill');
  fill.style.width = `${pct}%`;
  fill.style.background = pct > 40 ? 'var(--green)' : pct > 20 ? 'var(--warning)' : 'var(--danger)';
  const card = document.getElementById('batt-card');
  card.className = `sensor-card ${pct > 40 ? 'active-sensor' : pct > 20 ? 'warn' : 'danger-card'}`;
  const sVal = document.getElementById('s-batt');
  sVal.className = `sensor-val ${pct > 40 ? '' : pct > 20 ? 'warn' : 'danger'}`;
  if (pct <= 15 && !batt.charging) showToast('⚠️ Low battery! Charge before heading out');
}

// Network
function updateNetwork() {
  const online = navigator.onLine;
  const conn = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
  document.getElementById('s-net').textContent = online ? '✅ ONLINE' : '❌ OFFLINE';
  document.getElementById('s-net-type').textContent = conn?.effectiveType?.toUpperCase() || (online ? 'CONNECTED' : 'NO SIGNAL');
  document.getElementById('net-card').className = `sensor-card ${online ? 'active-sensor' : 'danger-card'}`;
}

// Clock
function updateClock() {
  const now = new Date();
  document.getElementById('s-time').textContent = now.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
  document.getElementById('s-date').textContent = now.toLocaleDateString('en-IN', { weekday: 'short', month: 'short', day: 'numeric' }).toUpperCase();
}

// Haversine distance (km)
function haversine(lat1, lon1, lat2, lon2) {
  const R = 6371;
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;
  const a = Math.sin(dLat/2)**2 + Math.cos(lat1*Math.PI/180) * Math.cos(lat2*Math.PI/180) * Math.sin(dLon/2)**2;
  return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
}

// Trek Tracker
function toggleTracking() {
  if (!isTracking) {
    startTracking();
  } else {
    stopTracking();
  }
}

// Center button — if tracking show sensors, else show start modal
function onCenterPress() {
  if (isTracking) {
    showScreen('sensors');
  } else {
    document.getElementById('start-trek-name').value = '';
    document.getElementById('start-trek-modal').classList.add('show');
    setTimeout(() => document.getElementById('start-trek-name').focus(), 300);
  }
}

function closeStartTrekModal() {
  document.getElementById('start-trek-modal').classList.remove('show');
}

function closeStartModal(e) {
  if (e.target === document.getElementById('start-trek-modal')) closeStartTrekModal();
}

function confirmStartTrek() {
  const name = document.getElementById('start-trek-name').value.trim();
  if (!name) { showToast('⚠️ Enter a trek name first'); return; }
  closeStartTrekModal();
  startTracking(name);
  showScreen('sensors');
}

function startTracking(name) {
  isTracking = true;
  trackName = name;
  trackStartTime = Date.now();
  trackPoints = [];
  trackDistanceKm = 0;

  // Update center button to pulse red
  document.getElementById('nav-center-btn').style.background = 'linear-gradient(135deg, #ef4444, #b91c1c)';
  document.getElementById('nav-center-btn').style.boxShadow = '0 0 24px rgba(239,68,68,0.6)';
  document.getElementById('nav-center-icon').innerHTML = '<rect x="6" y="6" width="12" height="12" rx="2" fill="white"/>';

  // Show live banner
  document.getElementById('live-banner').style.display = 'flex';
  document.getElementById('banner-name').textContent = name;

  document.getElementById('track-btn').className = 'track-btn stop';
  document.getElementById('track-btn').textContent = '⏹';
  document.getElementById('track-status').textContent = `🔴 Recording "${name}" — GPS active`;

  if (!sensorActive) startSensors();

  trackTimerInterval = setInterval(() => {
    const elapsed = Date.now() - trackStartTime;
    const h = Math.floor(elapsed / 3600000).toString().padStart(2, '0');
    const m = Math.floor((elapsed % 3600000) / 60000).toString().padStart(2, '0');
    const s = Math.floor((elapsed % 60000) / 1000).toString().padStart(2, '0');
    const timeStr = `${h}:${m}:${s}`;
    document.getElementById('tracker-time-display').textContent = timeStr;
    document.getElementById('banner-time').textContent = timeStr;
    document.getElementById('banner-dist').textContent = `${trackDistanceKm.toFixed(2)}km`;
  }, 1000);

  showToast(`🔴 Recording "${name}"`);
}

function stopTracking() {
  isTracking = false;
  clearInterval(trackTimerInterval);

  // Reset center button
  document.getElementById('nav-center-btn').style.background = '';
  document.getElementById('nav-center-btn').style.boxShadow = '';
  document.getElementById('nav-center-icon').innerHTML = '<path d="M12 5v14M5 12h14"/>';

  // Hide live banner
  document.getElementById('live-banner').style.display = 'none';

  document.getElementById('track-btn').className = 'track-btn start';
  document.getElementById('track-btn').textContent = '▶';

  const durationMin = Math.round((Date.now() - trackStartTime) / 60000);
  document.getElementById('track-status').textContent = `✅ Stopped — ${trackDistanceKm.toFixed(2)}km in ${durationMin}min`;

  // Auto open log modal prefilled with trek name + all data
  setTimeout(() => {
    openLogModal(trackName || '');
    document.getElementById('log-dist').value = trackDistanceKm.toFixed(2);
    document.getElementById('log-dur').value = durationMin;
    if (trackElevation) document.getElementById('log-elev').value = trackElevation;
    showToast('✅ Trek data loaded — add your rating!');
  }, 400);
}

// ══════════════════════════════════════════════════════════════
// CAMERA / AR ENGINE
// ══════════════════════════════════════════════════════════════
let camStream = null;
let camMode = 'identify'; // 'identify' | 'landmark' | 'compass'
let camAnalyzing = false;
let camCompassInterval = null;

const CAM_MODE_CONFIG = {
  identify: { label: '🌿 IDENTIFY', shutter: 'TAP TO IDENTIFY' },
  landmark: { label: '🏔️ LANDMARK', shutter: 'TAP TO SCAN' },
  compass:  { label: '🧭 COMPASS',  shutter: 'COMPASS LIVE' },
};

async function openCamera() {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('screen-camera').classList.add('active');
  document.getElementById('cam-side-btn').style.display = 'none';

  try {
    camStream = await navigator.mediaDevices.getUserMedia({
      video: { facingMode: 'environment', width: { ideal: 1280 }, height: { ideal: 720 } },
      audio: false
    });
    document.getElementById('cam-video').srcObject = camStream;
    setCamMode(camMode);
    startCompassOverlay();
  } catch(e) {
    showToast('⚠️ Camera permission denied — enable in Chrome settings');
    document.getElementById('cam-side-btn').style.display = 'flex';
    showScreen('feed');
  }
}

function closeCamera() {
  if (camStream) {
    camStream.getTracks().forEach(t => t.stop());
    camStream = null;
  }
  clearInterval(camCompassInterval);
  document.getElementById('cam-result').style.display = 'none';
  document.getElementById('ar-compass-bar').style.display = 'none';
  document.getElementById('cam-side-btn').style.display = 'flex';
  showScreen('feed');
}

function toggleCamMenu() {
  document.getElementById('cam-menu').classList.toggle('show');
}

function setCamMode(mode) {
  camMode = mode;
  document.getElementById('cam-menu').classList.remove('show');

  // Update UI
  ['identify','landmark','compass'].forEach(m => {
    document.getElementById(`mode-${m}`).classList.toggle('active-mode', m === mode);
    document.getElementById(`check-${m}`).style.display = m === mode ? 'inline' : 'none';
  });
  const cfg = CAM_MODE_CONFIG[mode];
  document.getElementById('cam-mode-label').textContent = cfg.label;
  document.getElementById('shutter-label').textContent = cfg.shutter;
  document.getElementById('cam-result').style.display = 'none';

  // Compass overlay
  document.getElementById('ar-compass-bar').style.display = mode === 'compass' ? 'flex' : 'none';

  // Shutter visible in identify/landmark, hidden in compass (it's live)
  document.getElementById('cam-shutter-btn').style.opacity = mode === 'compass' ? '0.4' : '1';
  document.getElementById('cam-shutter-btn').style.pointerEvents = mode === 'compass' ? 'none' : 'auto';

  if (mode === 'compass') showCompassLiveAR();
}

// Live compass overlay on camera
function startCompassOverlay() {
  window.addEventListener('deviceorientation', updateCamCompass, true);
}

function updateCamCompass(e) {
  if (camMode !== 'compass') return;
  let heading = e.webkitCompassHeading ?? (e.alpha ? (360 - e.alpha) : null);
  if (heading === null) return;
  heading = Math.round(heading);

  document.getElementById('ar-deg-text').textContent = `${heading}°`;
  document.getElementById('ar-dir-text').textContent = degreesToDir(heading);

  // Rotate arrow emoji
  const arrows = ['↑','↗','→','↘','↓','↙','←','↖'];
  document.getElementById('ar-arrow').textContent = arrows[Math.round(heading/45)%8];

  // Update coords if available
  if (lastPos) {
    const lat = lastPos.coords.latitude.toFixed(4);
    const lng = lastPos.coords.longitude.toFixed(4);
    document.getElementById('ar-coords-text').textContent = `${lat}, ${lng}`;
  }
}

function showCompassLiveAR() {
  document.getElementById('ar-compass-bar').style.display = 'flex';
}

// Capture frame and send to Claude AI
async function captureAndAnalyze() {
  if (camAnalyzing) return;
  if (camMode === 'compass') return;
  camAnalyzing = true;

  const video = document.getElementById('cam-video');
  const canvas = document.getElementById('cam-canvas');
  canvas.width = video.videoWidth || 640;
  canvas.height = video.videoHeight || 480;
  const ctx = canvas.getContext('2d');
  ctx.drawImage(video, 0, 0);
  const base64 = canvas.toDataURL('image/jpeg', 0.8).split(',')[1];

  // Animate shutter
  const inner = document.getElementById('cam-shutter-inner');
  inner.classList.add('analyzing');
  document.getElementById('shutter-label').textContent = 'ANALYZING...';

  // Show result panel with loading
  const resultEl = document.getElementById('cam-result');
  const titleEl = document.getElementById('cam-result-title');
  const bodyEl = document.getElementById('cam-result-body');
  resultEl.style.display = 'block';
  titleEl.textContent = 'Analyzing...';
  bodyEl.innerHTML = '<div style="display:flex;align-items:center;gap:8px"><div class="spinner"></div> <span style="color:var(--text-muted);font-size:12px">AI is reading your camera...</span></div>';

  // Build prompt based on mode
  const prompts = {
    identify: `You are a nature expert. Look at this image and identify any plants, animals, fruits, insects, birds or other natural elements visible. For each one found:
- Name (common + scientific if possible)
- Brief description (1-2 sentences)
- Is it edible/safe? Any warnings?
- Fun fact
Format as a clean list. If nothing identifiable, say so clearly. Be concise.`,

    landmark: `You are a geography and trekking expert. Look at this image and identify any visible landmarks, peaks, mountains, forts, waterfalls, rivers, valleys, or notable geographical features.
For each one:
- Name of the landmark/peak/feature
- Type (mountain/fort/waterfall/river/etc)
- Approximate elevation if it's a peak
- Historical or geographical significance (1-2 sentences)
If you can't identify specific names, describe what type of terrain/feature you see and estimate the region. Be concise.`,
  };

  const userPrompt = prompts[camMode] || prompts.identify;
  const modeTitle = camMode === 'identify' ? '🌿 Nature Identification' : '🏔️ Landmark Detection';

  try {
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        messages: [{
          role: 'user',
          content: [
            { type: 'image', source: { type: 'base64', media_type: 'image/jpeg', data: base64 } },
            { type: 'text', text: userPrompt }
          ]
        }]
      })
    });

    const data = await response.json();
    const text = data.content?.map(c => c.text || '').join('') || 'Could not analyze image.';

    titleEl.textContent = modeTitle;
    bodyEl.innerHTML = text.replace(/\n/g, '<br>').replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>').replace(/\*(.*?)\*/g, '<em>$1</em>');

  } catch(e) {
    titleEl.textContent = '⚠️ Analysis Failed';
    bodyEl.textContent = 'Could not connect to AI. Check your internet connection and try again.';
  }

  inner.classList.remove('analyzing');
  document.getElementById('shutter-label').textContent = CAM_MODE_CONFIG[camMode].shutter;
  camAnalyzing = false;
}

// Close menu when tapping elsewhere on camera
document.addEventListener('click', (e) => {
  const menu = document.getElementById('cam-menu');
  const dotsBtn = document.getElementById('cam-dots');
  if (menu && dotsBtn && !menu.contains(e.target) && !dotsBtn.contains(e.target)) {
    menu.classList.remove('show');
  }
});

// ══════════════════════════════════════════════════════════════
// PROFILE TABS
// ══════════════════════════════════════════════════════════════
function switchProfileTab(tab) {
  ['stats','friends','settings'].forEach(t => {
    document.getElementById(`ptab-${t}`).classList.toggle('active', t === tab);
    document.getElementById(`ppanel-${t}`).style.display = t === tab ? 'block' : 'none';
  });
  if (tab === 'friends') renderFriends();
}

// ══════════════════════════════════════════════════════════════
// TREK TYPE + COMPANIONS
// ══════════════════════════════════════════════════════════════
let activeTrekType = 'solo';
let activeCompanions = [];

function selectTrekType(type) {
  activeTrekType = type;
  ['solo','duo','group'].forEach(t => {
    document.getElementById(`type-${t}`).classList.toggle('selected', t === type);
  });
  const compSection = document.getElementById('companions-section');
  compSection.style.display = type === 'solo' ? 'none' : 'block';
}

function renderCompanionChips() {
  const wrap = document.getElementById('companions-chips');
  if (!wrap) return;
  wrap.innerHTML = activeCompanions.map((name, i) => `
    <div class="companion-chip">
      <span>${name}</span>
      <button onclick="removeCompanion(${i})">×</button>
    </div>`).join('');
}

function removeCompanion(i) {
  activeCompanions.splice(i, 1);
  renderCompanionChips();
}

function addCompanion() {
  const input = document.getElementById('companion-input');
  const name = input.value.trim();
  if (!name) return;
  if (activeCompanions.includes(name)) { showToast('Already added!'); return; }
  if (activeTrekType === 'duo' && activeCompanions.length >= 1) { showToast('Duo = max 1 companion'); return; }
  activeCompanions.push(name);
  input.value = '';
  document.getElementById('friend-suggestions').style.display = 'none';
  renderCompanionChips();
}

function filterFriendSuggestions() {
  const val = document.getElementById('companion-input').value.toLowerCase();
  const sugg = document.getElementById('friend-suggestions');
  if (!val) { sugg.style.display = 'none'; return; }
  const matches = friends.filter(f => f.name.toLowerCase().includes(val) && !activeCompanions.includes(f.name));
  if (!matches.length) { sugg.style.display = 'none'; return; }
  sugg.style.display = 'block';
  sugg.innerHTML = matches.map(f => `
    <div onclick="pickFriendCompanion('${f.name}')" style="padding:10px 14px;cursor:pointer;display:flex;align-items:center;gap:10px;border-bottom:1px solid var(--border)">
      <div style="width:28px;height:28px;border-radius:50%;background:var(--green-dim);display:flex;align-items:center;justify-content:center;font-family:'Bebas Neue';font-size:14px;color:white">${f.name[0].toUpperCase()}</div>
      <div>
        <div style="font-size:13px;color:var(--text-sec);font-weight:600">${f.name}</div>
        <div style="font-size:10px;color:var(--text-muted)">${getRank(f.xp||0).title}</div>
      </div>
    </div>`).join('');
}

function pickFriendCompanion(name) {
  document.getElementById('companion-input').value = name;
  document.getElementById('friend-suggestions').style.display = 'none';
  addCompanion();
}

// ══════════════════════════════════════════════════════════════
// FRIENDS SYSTEM
// ══════════════════════════════════════════════════════════════
let friends = JSON.parse(localStorage.getItem('trekr_friends') || '[]');
let friendRequests = JSON.parse(localStorage.getItem('trekr_requests') || '[]');

// Simulated community pool for search
const COMMUNITY = [
  { name:'ArjunTrekker', xp:4800, treks:28, km:342 },
  { name:'PriyaHikes', xp:3200, treks:19, km:218 },
  { name:'RohitSummit', xp:2100, treks:14, km:156 },
  { name:'NehaAdventure', xp:1600, treks:11, km:98 },
  { name:'VikasPeak', xp:900, treks:7, km:64 },
  { name:'SnehaTrails', xp:600, treks:5, km:41 },
  { name:'AmitWalker', xp:350, treks:3, km:22 },
  { name:'KavyaPahadi', xp:5200, treks:33, km:410 },
  { name:'SureshRaahi', xp:2800, treks:17, km:198 },
  { name:'AnjaliSherpaji', xp:7100, treks:45, km:612 },
];

function sendFriendRequest() {
  const input = document.getElementById('friend-input');
  const name = input.value.trim();
  if (!name) { showToast('⚠️ Enter a username'); return; }
  if (name.toLowerCase() === userName.toLowerCase()) { showToast('⚠️ That\'s you!'); return; }
  if (friends.find(f => f.name.toLowerCase() === name.toLowerCase())) { showToast('Already your friend!'); return; }

  // Check community
  const found = COMMUNITY.find(u => u.name.toLowerCase() === name.toLowerCase());
  if (!found) { showToast('⚠️ User not found'); return; }

  // Simulate auto-accept (since no real backend)
  friends.push({ ...found, addedAt: Date.now() });
  localStorage.setItem('trekr_friends', JSON.stringify(friends));
  input.value = '';
  renderFriends();
  showToast(`✅ ${found.name} added as friend!`);
}

function removeFriend(name) {
  if (!confirm(`Remove ${name} from friends?`)) return;
  friends = friends.filter(f => f.name !== name);
  localStorage.setItem('trekr_friends', JSON.stringify(friends));
  renderFriends();
  showToast('Friend removed');
}

function renderFriends() {
  const list = document.getElementById('friends-list');
  const countEl = document.getElementById('friend-count');
  if (countEl) countEl.textContent = `${friends.length} friend${friends.length !== 1 ? 's' : ''}`;

  if (!friends.length) {
    list.innerHTML = `<div class="empty-state" style="padding:32px 0">
      <div class="empty-icon">👥</div>
      <div class="empty-title">No Friends Yet</div>
      <div class="empty-sub">Search for trekkers above and add them as friends!</div>
    </div>`;
    return;
  }

  list.innerHTML = friends.map(f => `
    <div class="friend-card">
      <div class="friend-avatar">${(f.name[0]||'T').toUpperCase()}</div>
      <div class="friend-body">
        <div class="friend-name">${f.name}</div>
        <div class="friend-meta">${getRank(f.xp||0).title} · ${f.treks||0} treks · ${f.km||0}km</div>
      </div>
      <div style="display:flex;flex-direction:column;align-items:flex-end;gap:4px">
        <div class="friend-xp">${(f.xp||0).toLocaleString()}<span style="font-size:9px;color:var(--text-muted)"> XP</span></div>
        <button class="btn btn-danger btn-sm" onclick="removeFriend('${f.name}')" style="padding:4px 8px;font-size:8px">Remove</button>
      </div>
    </div>`).join('');
}

// ══════════════════════════════════════════════════════════════
// THEME TOGGLE
// ══════════════════════════════════════════════════════════════
let isLightMode = localStorage.getItem('trekr_theme') === 'light';

function applyTheme() {
  document.body.classList.toggle('light-mode', isLightMode);
  const btn = document.getElementById('theme-toggle');
  const lbl = document.getElementById('theme-label');
  if (btn) btn.classList.toggle('on', !isLightMode);
  if (lbl) lbl.textContent = isLightMode ? 'Light Mode' : 'Dark Mode';
  // Update meta theme-color
  document.querySelector('meta[name="theme-color"]').content = isLightMode ? '#f0f7f0' : '#080d08';
}

function toggleTheme() {
  isLightMode = !isLightMode;
  localStorage.setItem('trekr_theme', isLightMode ? 'light' : 'dark');
  applyTheme();
  showToast(isLightMode ? '☀️ Light mode on!' : '🌙 Dark mode on!');
}

// ══════════════════════════════════════════════════════════════
// INIT
// ══════════════════════════════════════════════════════════════
(async function init() {
  applyTheme();
  renderProfile();
  renderLogs();

  // Try to get location and load nearby trails
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(async pos => {
      userLatLng = [pos.coords.latitude, pos.coords.longitude];
      document.getElementById('feed-list').innerHTML = `<div style="display:flex;justify-content:center;padding:40px"><div class="spinner"></div></div>`;
      try {
        allTrails = await fetchTrails(pos.coords.latitude, pos.coords.longitude, 11);
        renderFeed(allTrails);
        renderMapMarkers();
        if (mapInstance) mapInstance.setView(userLatLng, 12);
      } catch {
        document.getElementById('feed-list').innerHTML = `<div class="empty-state"><div class="empty-icon">⚡</div><div class="empty-title">Couldn't Load Trails</div><div class="empty-sub">Check your internet connection and try again</div></div>`;
      }
    }, () => {
      // No location — load default area
      fetchTrails(19.2, 73.7, 10).then(trails => {
        allTrails = trails;
        renderFeed(allTrails);
        renderMapMarkers();
      }).catch(() => {});
    });
  }
})();
</script>
</body>
</html>
