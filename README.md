<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>UMSC ศบส.31 — ระบบบันทึกข้อมูลบริการ</title>
<link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:'Sarabun',Tahoma,sans-serif;background:#f0f4f8;color:#1c2833;min-height:100vh;font-size:14px}
:root{--pri:#1a5276;--pl:#2980b9;--pp:#d6eaf8;--acc:#1abc9c;--warn:#e67e22;--danger:#e74c3c;--brd:#d5e8f3;--muted:#6b7a8d;--bg:#f0f4f8}

/* SCREENS */
.screen{display:none}
#login-screen.active{display:flex;min-height:100vh;align-items:center;justify-content:center;background:var(--bg)}
#app-screen.active{display:flex;flex-direction:column;min-height:100vh}
#setup-screen.active{display:flex;min-height:100vh;align-items:center;justify-content:center;background:var(--bg)}

/* LOGIN */
.login-box{background:#fff;border-radius:16px;border:1px solid var(--brd);padding:36px 40px;width:360px;text-align:center}
.llogo{width:54px;height:54px;background:var(--pri);border-radius:13px;display:flex;align-items:center;justify-content:center;font-size:22px;font-weight:700;color:#fff;margin:0 auto 14px}
.ltitle{font-size:17px;font-weight:700;color:var(--pri);margin-bottom:3px}
.lsub{font-size:12px;color:var(--muted);margin-bottom:22px;line-height:1.6}
.login-box select,.login-box input{width:100%;padding:10px 12px;border:1px solid var(--brd);border-radius:8px;font-size:13px;margin-bottom:10px;font-family:inherit;color:#1c2833}
.login-box select:focus,.login-box input:focus{outline:none;border-color:var(--pl);box-shadow:0 0 0 3px rgba(41,128,185,.12)}
.btn-login{width:100%;padding:11px;background:var(--pri);color:#fff;border:none;border-radius:8px;font-size:14px;font-weight:600;cursor:pointer;font-family:inherit;transition:background .15s}
.btn-login:hover{background:var(--pl)}
.lerr{color:var(--danger);font-size:12px;margin-top:6px;min-height:18px}
.lhint{font-size:11px;color:var(--muted);margin-top:12px;background:var(--bg);border-radius:8px;padding:10px 12px;text-align:left;line-height:1.7}
.lhint b{color:var(--pri)}

/* SETUP SCREEN */
.setup-box{background:#fff;border-radius:16px;border:1px solid var(--brd);padding:32px 36px;width:520px;max-width:95vw}
.setup-box h2{font-size:16px;color:var(--pri);margin-bottom:6px}
.setup-box p{font-size:13px;color:var(--muted);margin-bottom:16px;line-height:1.6}
.step{background:var(--bg);border-radius:10px;padding:14px 16px;margin-bottom:12px;border-left:3px solid var(--pri)}
.step-num{font-size:11px;font-weight:700;color:var(--pri);margin-bottom:4px}
.step p{margin-bottom:8px;color:#1c2833}
.setup-box input{width:100%;padding:9px 12px;border:1px solid var(--brd);border-radius:7px;font-size:13px;font-family:inherit;color:#1c2833;margin-top:6px}
.setup-box input:focus{outline:none;border-color:var(--pl)}
code{background:#e8f4fb;padding:2px 7px;border-radius:4px;font-family:monospace;font-size:12px;color:var(--pri)}
.copy-btn{background:var(--pp);border:none;padding:4px 10px;border-radius:5px;font-size:11px;cursor:pointer;color:var(--pri);font-weight:600;margin-left:6px}

/* TOPBAR */
.topbar{background:var(--pri);color:#fff;display:flex;align-items:center;gap:10px;padding:0 18px;height:50px;position:sticky;top:0;z-index:100;flex-shrink:0}
.tlogo{width:30px;height:30px;background:var(--acc);border-radius:7px;display:flex;align-items:center;justify-content:center;font-size:14px;font-weight:700;color:#fff;flex-shrink:0}
.ttitle{font-size:13px;font-weight:600;flex:1;min-width:0}
.ttitle small{font-weight:300;font-size:11px;opacity:.8;display:block;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.tab-bar{display:flex;gap:2px;background:rgba(255,255,255,.12);border-radius:7px;padding:3px;flex-shrink:0}
.tab-btn{padding:5px 11px;border:none;background:transparent;color:rgba(255,255,255,.8);border-radius:5px;cursor:pointer;font-family:inherit;font-size:12px;font-weight:500;white-space:nowrap;transition:all .15s}
.tab-btn.active{background:#fff;color:var(--pri)}
.tab-btn:hover:not(.active){background:rgba(255,255,255,.18);color:#fff}
.sync-st{font-size:10px;white-space:nowrap;flex-shrink:0;padding:3px 8px;border-radius:5px;background:rgba(255,255,255,.1)}
.sync-st.ok{color:#7debb5}.sync-st.err{color:#f1948a}.sync-st.ing{color:rgba(255,255,255,.6)}
.btn-logout{background:none;border:1px solid rgba(255,255,255,.3);color:rgba(255,255,255,.7);padding:4px 10px;border-radius:5px;cursor:pointer;font-size:11px;font-family:inherit;flex-shrink:0}
.btn-logout:hover{background:rgba(255,255,255,.1);color:#fff}

/* MAIN */
.main{flex:1;padding:18px;max-width:960px;width:100%;margin:0 auto}
.page{display:none}.page.active{display:block}

/* CARD */
.card{background:#fff;border-radius:12px;border:1px solid var(--brd);padding:18px 20px;margin-bottom:16px}
.chd{display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:14px;padding-bottom:10px;border-bottom:1px solid var(--brd)}
.ctitle{font-size:14px;font-weight:600;color:var(--pri)}
.csub{font-size:11px;color:var(--muted);margin-top:2px}

/* FORM */
.fgrid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
.fg{display:flex;flex-direction:column;gap:4px}
.fg.full{grid-column:1/-1}
label{font-size:11px;font-weight:600;color:var(--muted)}
input,select,textarea{padding:8px 11px;border:1px solid var(--brd);border-radius:7px;font-family:inherit;font-size:13px;color:#1c2833;background:#fff;width:100%;transition:border-color .15s}
input:focus,select:focus,textarea:focus{outline:none;border-color:var(--pl);box-shadow:0 0 0 3px rgba(41,128,185,.1)}
textarea{resize:vertical;min-height:60px}
.chgrid{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin:10px 0}
.chc{border:1.5px solid var(--brd);border-radius:9px;padding:9px 7px;text-align:center}
.chc input{font-size:11px;padding:5px;text-align:center;margin-bottom:3px;border-radius:5px}
.chico{font-size:18px;margin-bottom:3px}
.chn{font-size:10px;font-weight:600;color:var(--pri);margin-bottom:5px}
.brow{display:flex;gap:8px;justify-content:flex-end;margin-top:10px}
.btn{padding:8px 20px;border:none;border-radius:7px;font-family:inherit;font-size:13px;font-weight:600;cursor:pointer;transition:all .15s}
.bp{background:var(--pri);color:#fff}.bp:hover{background:var(--pl)}.bp:disabled{background:#aaa;cursor:not-allowed}
.bo{background:transparent;border:1.5px solid var(--brd);color:var(--muted)}.bo:hover{border-color:var(--pri);color:var(--pri)}
.bsm{padding:5px 12px;font-size:12px}

/* TABLE */
.twrap{overflow-x:auto}
.dtbl{width:100%;border-collapse:collapse;font-size:12px;min-width:500px}
.dtbl th{background:var(--bg);padding:7px 9px;text-align:left;font-weight:600;color:var(--muted);border-bottom:1px solid var(--brd);font-size:11px;white-space:nowrap}
.dtbl td{padding:8px 9px;border-bottom:1px solid #eef3f8;vertical-align:middle}
.dtbl tr:hover td{background:#f7fbff}
.badge{padding:2px 8px;border-radius:20px;font-size:10px;font-weight:600}
.bbl{background:var(--pp);color:var(--pri)}.bgr{background:#d5f5e3;color:#1e8449}.bor{background:#fdebd0;color:#935116}.bpk{background:#fce4ec;color:#880e4f}

/* METRICS */
.mgrid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:16px}
.mc{background:#fff;border:1px solid var(--brd);border-radius:10px;padding:12px 16px;border-top:3px solid}
.mc.bl{border-top-color:var(--pri)}.mc.gr{border-top-color:var(--acc)}.mc.or{border-top-color:var(--warn)}.mc.rd{border-top-color:var(--danger)}
.ml{font-size:10px;color:var(--muted);font-weight:600;text-transform:uppercase;letter-spacing:.04em;margin-bottom:3px}
.mv{font-size:22px;font-weight:700;line-height:1}
.ms{font-size:10px;color:var(--muted);margin-top:2px}

/* CHARTS */
.cgrid{display:grid;grid-template-columns:1fr 1fr;gap:16px}
.ccard{background:#fff;border:1px solid var(--brd);border-radius:10px;padding:16px}
.ccard.full{grid-column:1/-1}
.cctitle{font-size:12px;font-weight:600;margin-bottom:10px;color:var(--pri)}
.pleg{display:flex;flex-wrap:wrap;gap:7px;margin-bottom:8px;font-size:10px;color:var(--muted)}
.pleg span{display:flex;align-items:center;gap:3px}
.pdot{width:8px;height:8px;border-radius:2px;flex-shrink:0}

/* FILTER */
.frow{display:flex;gap:8px;align-items:center;margin-bottom:14px;flex-wrap:wrap}
.frow select{font-size:12px;padding:6px 9px;width:auto;border:1px solid var(--brd);border-radius:7px}
.flabel{font-size:11px;color:var(--muted);font-weight:600;white-space:nowrap}

/* DEPT STATUS */
.dsgrid{display:grid;grid-template-columns:repeat(auto-fill,minmax(145px,1fr));gap:9px}
.dsi{border-radius:9px;padding:9px 11px;border:1px solid}
.dsi.done{background:#d5f5e3;border-color:#a9dfbf}.dsi.pend{background:#fce4ec;border-color:#f1948a}

/* MISC */
.empty{text-align:center;color:var(--muted);padding:24px;font-size:13px}
.live-dot{width:6px;height:6px;border-radius:50%;background:var(--acc);animation:pulse 2s infinite;display:inline-block;margin-right:4px;vertical-align:middle}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}
.mtag{background:var(--pri);color:#fff;padding:3px 11px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap}
.toast-el{position:fixed;bottom:18px;right:18px;color:#fff;padding:10px 18px;border-radius:9px;font-size:13px;font-weight:500;z-index:9999;pointer-events:none;opacity:0;transition:opacity .3s;min-width:180px;text-align:center}

/* SPINNER */
.spin-wrap{display:none;position:fixed;inset:0;background:rgba(255,255,255,.85);z-index:999;align-items:center;justify-content:center;flex-direction:column;gap:10px}
.spin-wrap.show{display:flex}
.spinner{width:34px;height:34px;border:3px solid var(--brd);border-top-color:var(--pri);border-radius:50%;animation:spin .7s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}

@media(max-width:680px){
  .fgrid{grid-template-columns:1fr}.chgrid{grid-template-columns:1fr 1fr}
  .mgrid{grid-template-columns:1fr 1fr}.cgrid{grid-template-columns:1fr}
  .tab-btn{padding:4px 7px;font-size:11px}.topbar{padding:0 10px;gap:6px}
  .main{padding:12px}.login-box{width:94vw;padding:24px 18px}
}
</style>
</head>
<body>

<!-- SPINNER -->
<div class="spin-wrap" id="spinner"><div class="spinner"></div><div style="font-size:13px;color:var(--muted)" id="spin-msg">กำลังโหลด...</div></div>

<!-- ══════ SETUP SCREEN ══════ -->
<div id="setup-screen" class="screen">
  <div class="setup-box">
    <div style="text-align:center;margin-bottom:20px">
      <div style="width:50px;height:50px;background:var(--pri);border-radius:12px;display:flex;align-items:center;justify-content:center;font-size:20px;font-weight:700;color:#fff;margin:0 auto 10px">31</div>
      <h2>ตั้งค่า Google Sheets Backend</h2>
      <p>ระบบใช้ Google Sheets เก็บข้อมูลกลาง ทุกหน่วยงานเห็นข้อมูลเดียวกัน</p>
    </div>

    <div class="step">
      <div class="step-num">ขั้นที่ 1 — สร้าง Google Apps Script</div>
      <p>เปิด <b>Google Drive</b> ใน Folder ของคุณ → คลิก <b>+ ใหม่ → More → Google Apps Script</b></p>
      <p>วางโค้ดด้านล่างทั้งหมด แทนที่โค้ดเดิม แล้วกด <b>Save (Ctrl+S)</b></p>
      <div style="position:relative">
        <button class="copy-btn" onclick="copyScript()" style="position:absolute;top:6px;right:6px;z-index:1">📋 คัดลอก</button>
        <textarea id="gs-code" readonly style="width:100%;height:140px;font-family:monospace;font-size:11px;background:#f8f9fa;border-radius:6px;padding:10px;border:1px solid var(--brd);resize:none;margin-top:6px"></textarea>
      </div>
    </div>

    <div class="step">
      <div class="step-num">ขั้นที่ 2 — Deploy เป็น Web App</div>
      <p>ใน Apps Script → <b>Deploy → New deployment → Web app</b><br>
      ตั้งค่า: Execute as = <b>Me</b> | Who has access = <b>Anyone</b> → กด <b>Deploy</b></p>
      <p>คัดลอก <b>Web app URL</b> แล้ววางด้านล่าง:</p>
      <input type="text" id="setup-url" placeholder="https://script.google.com/macros/s/AKfyc.../exec" oninput="validateSetupUrl()">
    </div>

    <div class="step">
      <div class="step-num">ขั้นที่ 3 — ระบุ Sheet ID (ไม่บังคับ)</div>
      <p>Google Sheets จะถูกสร้างอัตโนมัติ ถ้าต้องการใช้ Sheet ที่มีอยู่แล้ว วาง ID ที่นี่:</p>
      <input type="text" id="setup-sheet-id" placeholder="ปล่อยว่างให้สร้างใหม่อัตโนมัติ (แนะนำ)">
    </div>

    <button class="btn bp" id="setup-btn" onclick="finishSetup()" disabled style="width:100%;margin-top:4px;padding:12px">เชื่อมต่อ Google Sheets →</button>
    <p style="font-size:11px;color:var(--muted);text-align:center;margin-top:10px">
      หรือ <a href="#" onclick="useLocalMode()" style="color:var(--pri)">ใช้โหมดทดสอบ (localStorage เท่านั้น)</a>
    </p>
  </div>
</div>

<!-- ══════ LOGIN ══════ -->
<div id="login-screen" class="screen active">
  <div class="login-box">
    <div class="llogo">31</div>
    <div class="ltitle">ศูนย์บริการสาธารณสุข 31</div>
    <div class="lsub">เอิบ-จิตร ทังสุบุตร<br>ระบบบันทึกข้อมูลบริการ UMSC</div>
    <select id="login-dept">
      <option value="">— เลือกหน่วยงาน —</option>
      <option value="OPD">🏥 OPD</option>
      <option value="งานอนามัยชุมชน">🏘 งานอนามัยชุมชน</option>
      <option value="งานอนามัยโรงเรียน">🏫 งานอนามัยโรงเรียน</option>
      <option value="คลินิกวัณโรค">🫁 คลินิกวัณโรค</option>
      <option value="คลินิกทันตกรรม">🦷 คลินิกทันตกรรม</option>
      <option value="งานธุรการ">📂 งานธุรการ</option>
      <option value="งานการเงิน">💰 งานการเงิน</option>
      <option value="งานสังคมสงเคราะห์">🤝 งานสังคมสงเคราะห์</option>
      <option value="คลินิกก้าวใหม่พลัส">💊 คลินิกก้าวใหม่พลัส</option>
      <option value="งานเภสัชกรรม">⚗️ งานเภสัชกรรม</option>
      <option value="คลินิกกายภาพบำบัด">🩺 คลินิกกายภาพบำบัด</option>
      <option value="พนักงานขับรถยนต์">🚐 พนักงานขับรถยนต์</option>
      <option value="__admin__">👑 ผู้ดูแลระบบ (Admin)</option>
    </select>
    <input type="password" id="login-pass" placeholder="รหัสผ่าน" autocomplete="current-password">
    <button class="btn-login" onclick="doLogin()">เข้าสู่ระบบ</button>
    <div class="lerr" id="lerr"></div>
    <div class="lhint">
      <b>รหัสผ่าน:</b><br>
      • หน่วยงาน = ชื่อหน่วยงาน (เช่น <b>OPD</b>, <b>คลินิกทันตกรรม</b>)<br>
      • Admin = <b>Umsc31Admin</b>
    </div>
  </div>
</div>

<!-- ══════ APP ══════ -->
<div id="app-screen" class="screen">
  <div class="topbar">
    <div class="tlogo">31</div>
    <div class="ttitle">ศบส.31 UMSC<small id="tb-dept"></small></div>
    <div class="tab-bar" id="tab-bar"></div>
    <div class="sync-st" id="sync-st"></div>
    <button class="btn-logout" onclick="doLogout()">ออก</button>
  </div>
  <div class="main">

    <!-- บันทึก -->
    <div id="page-input" class="page">
      <div class="card">
        <div class="chd">
          <div><div class="ctitle" id="form-title">📋 บันทึกข้อมูลบริการ</div><div class="csub">กรอกข้อมูลการให้บริการประจำเดือน</div></div>
          <div class="mtag" id="mtag">—</div>
        </div>
        <div class="fgrid">
          <div class="fg"><label>วันที่บันทึก</label><input type="date" id="inp-date"></div>
          <div class="fg"><label>เดือนที่รายงาน</label><input type="month" id="inp-month" onchange="renderQuickSummary()"></div>
          <div class="fg full"><label>รายการบริการ</label>
            <select id="inp-svc">
              <option value="">— เลือกรายการบริการ —</option>
              <option>ให้คำปรึกษาปัญหาสุขภาพและการให้ข้อมูลด้านสุขภาพ</option>
              <option>การตรวจคัดกรองสุขภาพ</option><option>การฉีดวัคซีน</option>
              <option>การดูแลผู้ป่วยเรื้อรัง</option><option>การส่งต่อผู้ป่วย</option>
              <option>การเยี่ยมบ้าน</option><option>การให้ความรู้สุขศึกษา</option>
              <option>การรักษาทันตกรรม</option><option>การทำกายภาพบำบัด</option>
              <option>บริการจ่ายยา</option><option>อื่นๆ (ระบุในหมายเหตุ)</option>
            </select>
          </div>
        </div>
        <div style="margin:12px 0 5px"><label>ช่องทางการให้บริการ</label></div>
        <div class="chgrid">
          <div class="chc"><div class="chico">🏥</div><div class="chn">จุดบริการ</div>
            <input type="number" id="c0c" placeholder="ผู้รับบริการ" min="0">
            <input type="number" id="c0s" placeholder="ครั้งบริการ" min="0">
          </div>
          <div class="chc"><div class="chico">💬</div><div class="chn">Line OA</div>
            <input type="number" id="c1c" placeholder="ผู้รับบริการ" min="0">
            <input type="number" id="c1s" placeholder="ครั้งบริการ" min="0">
            <input type="number" id="c1o" placeholder="เกิน 30 นาที" min="0">
          </div>
          <div class="chc"><div class="chico">👍</div><div class="chn">Facebook</div>
            <input type="number" id="c2c" placeholder="ผู้รับบริการ" min="0">
            <input type="number" id="c2s" placeholder="ครั้งบริการ" min="0">
          </div>
          <div class="chc"><div class="chico">📞</div><div class="chn">โทรศัพท์</div>
            <input type="number" id="c3c" placeholder="ผู้รับบริการ" min="0">
            <input type="number" id="c3s" placeholder="ครั้งบริการ" min="0">
          </div>
        </div>
        <div class="fg full" style="margin-top:8px"><label>หมายเหตุ</label><textarea id="inp-notes" placeholder="ระบุข้อมูลเพิ่มเติม (ถ้ามี)"></textarea></div>
        <div class="brow">
          <button class="btn bo" onclick="clearForm()">ล้างข้อมูล</button>
          <button class="btn bp" id="save-btn" onclick="saveEntry()">💾 บันทึกข้อมูล</button>
        </div>
      </div>
      <div class="card">
        <div class="chd"><div class="ctitle">สรุปการบันทึก — เดือนที่เลือก</div></div>
        <div id="quick-summary"><div class="empty">ยังไม่มีข้อมูล</div></div>
      </div>
    </div>

    <!-- ประวัติ -->
    <div id="page-history" class="page">
      <div class="card">
        <div class="chd"><div class="ctitle">📁 ประวัติข้อมูลของหน่วยงาน</div></div>
        <div class="frow">
          <span class="flabel">เดือน:</span>
          <select id="hist-mon" onchange="renderHistory()"><option value="">ทุกเดือน</option></select>
          <button class="btn bo bsm" onclick="loadData()" style="margin-left:auto">🔄 รีเฟรช</button>
          <button class="btn bo bsm" onclick="exportCSV()">⬇️ Export CSV</button>
        </div>
        <div class="twrap"><table class="dtbl">
          <thead><tr><th>วันที่</th><th>เดือน</th><th>รายการบริการ</th><th>ช่องทาง</th><th>ผู้รับ</th><th>ครั้ง</th><th>30+</th><th></th></tr></thead>
          <tbody id="hist-body"></tbody>
        </table></div>
      </div>
    </div>

    <!-- Dashboard หน่วยงาน -->
    <div id="page-dashboard" class="page">
      <div class="frow">
        <span class="flabel">เดือน:</span>
        <select id="dash-mon" onchange="renderDash()"><option value="">ทุกเดือน</option></select>
        <button class="btn bo bsm" onclick="loadData()">🔄 รีเฟรช</button>
        <span style="margin-left:auto;font-size:11px;color:var(--muted)"><span class="live-dot"></span>อัปเดตทุก 15 วินาที</span>
      </div>
      <div class="mgrid">
        <div class="mc bl"><div class="ml">ผู้รับบริการ</div><div class="mv" id="dc">0</div><div class="ms">ราย</div></div>
        <div class="mc gr"><div class="ml">ครั้งบริการ</div><div class="mv" id="ds">0</div><div class="ms">ครั้ง</div></div>
        <div class="mc or"><div class="ml">เกิน 30 นาที</div><div class="mv" id="do">0</div><div class="ms">ครั้ง</div></div>
        <div class="mc rd"><div class="ml">รายการบันทึก</div><div class="mv" id="dr">0</div><div class="ms">รายการ</div></div>
      </div>
      <div class="cgrid">
        <div class="ccard"><div class="cctitle">ช่องทางบริการ</div><div class="pleg" id="pie-leg"></div><div style="position:relative;height:190px"><canvas id="ch-pie"></canvas></div></div>
        <div class="ccard"><div class="cctitle">รายการบริการ (ครั้ง)</div><div style="position:relative;height:220px"><canvas id="svc-bar"></canvas></div></div>
        <div class="ccard full"><div class="cctitle">แนวโน้มรายเดือน</div>
          <div class="pleg"><span><span class="pdot" style="background:#1a5276"></span>ผู้รับบริการ</span><span><span class="pdot" style="background:#1abc9c"></span>ครั้งบริการ</span></div>
          <div style="position:relative;height:190px"><canvas id="trend-line"></canvas></div>
        </div>
      </div>
    </div>

    <!-- Admin -->
    <div id="page-admin" class="page">
      <div class="frow">
        <span class="flabel">เดือน:</span>
        <select id="adm-mon" onchange="renderAdmin()"><option value="">ทุกเดือน</option></select>
        <button class="btn bo bsm" onclick="loadData()">🔄 รีเฟรช</button>
        <span style="margin-left:auto;font-size:11px;color:var(--muted)"><span class="live-dot"></span>ภาพรวมทุกหน่วยงาน</span>
      </div>
      <div class="mgrid">
        <div class="mc bl"><div class="ml">ผู้รับบริการรวม</div><div class="mv" id="ac">0</div><div class="ms">ราย</div></div>
        <div class="mc gr"><div class="ml">ครั้งบริการรวม</div><div class="mv" id="as2">0</div><div class="ms">ครั้ง</div></div>
        <div class="mc or"><div class="ml">หน่วยงานบันทึกแล้ว</div><div class="mv" id="ad">0</div><div class="ms">/ 12 หน่วย</div></div>
        <div class="mc rd"><div class="ml">รายการทั้งหมด</div><div class="mv" id="ar">0</div><div class="ms">รายการ</div></div>
      </div>
      <div class="card">
        <div class="chd"><div class="ctitle">สถานะการบันทึกรายหน่วยงาน</div></div>
        <div class="dsgrid" id="dept-status"></div>
      </div>
      <div class="cgrid" style="margin-top:16px">
        <div class="ccard full"><div class="cctitle">ผู้รับบริการรายหน่วยงาน</div><div style="position:relative;height:260px"><canvas id="adm-bar"></canvas></div></div>
        <div class="ccard"><div class="cctitle">ช่องทางบริการ (รวม)</div><div class="pleg" id="adm-pie-leg"></div><div style="position:relative;height:200px"><canvas id="adm-pie"></canvas></div></div>
        <div class="ccard"><div class="cctitle">แนวโน้มรายเดือน (ภาพรวม)</div><div style="position:relative;height:200px"><canvas id="adm-trend"></canvas></div></div>
      </div>
      <div class="card" style="margin-top:4px">
        <div class="chd">
          <div class="ctitle">รายการข้อมูลทั้งหมด</div>
          <div style="display:flex;gap:6px;align-items:center">
            <select id="adm-dept-f" onchange="renderAdminTable()" style="font-size:12px;padding:5px 8px;width:auto;border:1px solid var(--brd);border-radius:6px"><option value="">ทุกหน่วยงาน</option>
              <option>OPD</option><option>งานอนามัยชุมชน</option><option>งานอนามัยโรงเรียน</option>
              <option>คลินิกวัณโรค</option><option>คลินิกทันตกรรม</option><option>งานธุรการ</option>
              <option>งานการเงิน</option><option>งานสังคมสงเคราะห์</option><option>คลินิกก้าวใหม่พลัส</option>
              <option>งานเภสัชกรรม</option><option>คลินิกกายภาพบำบัด</option><option>พนักงานขับรถยนต์</option>
            </select>
            <button class="btn bo bsm" onclick="exportAdminCSV()">⬇️ Export</button>
          </div>
        </div>
        <div class="twrap"><table class="dtbl">
          <thead><tr><th>วันที่</th><th>หน่วยงาน</th><th>รายการบริการ</th><th>ช่องทาง</th><th>ผู้รับ</th><th>ครั้ง</th><th></th></tr></thead>
          <tbody id="adm-body"></tbody>
        </table></div>
      </div>
    </div>

  </div>
</div>

<div class="toast-el" id="toast"></div>

<script>
// ════════════════════════════════════════
// CONSTANTS
// ════════════════════════════════════════
const DEPTS = ['OPD','งานอนามัยชุมชน','งานอนามัยโรงเรียน','คลินิกวัณโรค','คลินิกทันตกรรม','งานธุรการ','งานการเงิน','งานสังคมสงเคราะห์','คลินิกก้าวใหม่พลัส','งานเภสัชกรรม','คลินิกกายภาพบำบัด','พนักงานขับรถยนต์'];
const ICO = {'OPD':'🏥','งานอนามัยชุมชน':'🏘','งานอนามัยโรงเรียน':'🏫','คลินิกวัณโรค':'🫁','คลินิกทันตกรรม':'🦷','งานธุรการ':'📂','งานการเงิน':'💰','งานสังคมสงเคราะห์':'🤝','คลินิกก้าวใหม่พลัส':'💊','งานเภสัชกรรม':'⚗️','คลินิกกายภาพบำบัด':'🩺','พนักงานขับรถยนต์':'🚐'};
const CH_COL = {'จุดบริการ':'#1a5276','Line OA':'#27ae60','Facebook':'#2980b9','โทรศัพท์':'#e67e22'};
const ADMIN_PW = 'Umsc31Admin';
const CFG_KEY = 'umsc31_cfg';
const LOCAL_KEY = 'umsc31_data';

// APPS SCRIPT CODE
const GAS_CODE = `function doGet(e) {
  return handleRequest(e);
}
function doPost(e) {
  return handleRequest(e);
}
function handleRequest(e) {
  var ss = getSheet();
  var action = (e.parameter && e.parameter.action) || (e.postData && JSON.parse(e.postData.contents).action);
  var result;
  try {
    if (action === 'read') {
      result = readAll(ss);
    } else if (action === 'write') {
      var data = JSON.parse(e.postData.contents).data;
      result = writeRow(ss, data);
    } else if (action === 'delete') {
      var id = JSON.parse(e.postData.contents).id;
      result = deleteRow(ss, id);
    } else {
      result = { ok: false, error: 'Unknown action' };
    }
  } catch(err) {
    result = { ok: false, error: err.toString() };
  }
  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}
function getSheet() {
  var id = PropertiesService.getScriptProperties().getProperty('SHEET_ID');
  var ss = id ? SpreadsheetApp.openById(id) : SpreadsheetApp.create('UMSC_Data_31');
  if (!id) {
    PropertiesService.getScriptProperties().setProperty('SHEET_ID', ss.getId());
  }
  var sheet = ss.getSheetByName('Data');
  if (!sheet) {
    sheet = ss.insertSheet('Data');
    sheet.appendRow(['id','date','month','dept','service','channel','clients','sessions','over30','notes','ts']);
    sheet.getRange(1,1,1,11).setFontWeight('bold').setBackground('#1a5276').setFontColor('#ffffff');
  }
  return sheet;
}
function readAll(sheet) {
  var rows = sheet.getDataRange().getValues();
  if (rows.length <= 1) return { ok: true, data: [] };
  var headers = rows[0];
  var data = rows.slice(1).map(function(r) {
    var obj = {};
    headers.forEach(function(h, i) { obj[h] = r[i]; });
    return obj;
  });
  return { ok: true, data: data };
}
function writeRow(sheet, d) {
  sheet.appendRow([d.id, d.date, d.month, d.dept, d.service, d.channel, d.clients, d.sessions, d.over30||0, d.notes||'', d.ts]);
  return { ok: true };
}
function deleteRow(sheet, id) {
  var data = sheet.getDataRange().getValues();
  for (var i = 1; i < data.length; i++) {
    if (String(data[i][0]) === String(id)) {
      sheet.deleteRow(i + 1);
      return { ok: true };
    }
  }
  return { ok: false, error: 'Not found' };
}`;

// ════════════════════════════════════════
// STATE
// ════════════════════════════════════════
let currentUser = null;
let allData = [];
let gCharts = {};
let syncTimer = null;
let apiUrl = null;
let useLocal = false;

// ════════════════════════════════════════
// INIT
// ════════════════════════════════════════
document.addEventListener('DOMContentLoaded', () => {
  // Fill GAS code textarea
  const ta = document.getElementById('gs-code');
  if (ta) ta.value = GAS_CODE;

  // Load saved config
  try {
    const cfg = JSON.parse(localStorage.getItem(CFG_KEY) || '{}');
    if (cfg.apiUrl) { apiUrl = cfg.apiUrl; useLocal = false; }
    else if (cfg.useLocal) { useLocal = true; }
  } catch(e) {}

  // Restore session
  const saved = sessionStorage.getItem('umsc31_user');
  if (saved) {
    currentUser = saved;
    showScreen('app-screen');
    setupUI();
    loadData();
    startSync();
  } else {
    showScreen('login-screen');
  }

  // Enter key on login
  document.getElementById('login-pass').addEventListener('keydown', e => {
    if (e.key === 'Enter') doLogin();
  });
});

// ════════════════════════════════════════
// SCREENS
// ════════════════════════════════════════
function showScreen(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}

// ════════════════════════════════════════
// SETUP
// ════════════════════════════════════════
function copyScript() {
  const ta = document.getElementById('gs-code');
  ta.select();
  document.execCommand('copy');
  toast('📋 คัดลอกโค้ดแล้ว!');
}

function validateSetupUrl() {
  const url = document.getElementById('setup-url').value.trim();
  document.getElementById('setup-btn').disabled = !url.startsWith('https://script.google.com');
}

async function finishSetup() {
  const url = document.getElementById('setup-url').value.trim();
  showSpinner('กำลังทดสอบการเชื่อมต่อ...');
  try {
    const res = await fetch(url + '?action=read');
    const json = await res.json();
    if (json.ok !== undefined) {
      apiUrl = url;
      localStorage.setItem(CFG_KEY, JSON.stringify({ apiUrl: url }));
      hideSpinner();
      toast('✅ เชื่อมต่อสำเร็จ!');
      setTimeout(() => showScreen('login-screen'), 1000);
    } else throw new Error('Response not ok');
  } catch(e) {
    hideSpinner();
    toast('❌ เชื่อมต่อไม่ได้ ตรวจสอบ URL อีกครั้ง', '#e74c3c');
  }
}

function useLocalMode() {
  useLocal = true;
  localStorage.setItem(CFG_KEY, JSON.stringify({ useLocal: true }));
  showScreen('login-screen');
  toast('ใช้โหมดทดสอบ (เฉพาะเครื่องนี้)');
}

// ════════════════════════════════════════
// AUTH
// ════════════════════════════════════════
function getPw(dept) {
  return dept === '__admin__' ? ADMIN_PW : dept;
}

function doLogin() {
  const dept = document.getElementById('login-dept').value;
  const pass = document.getElementById('login-pass').value;
  const errEl = document.getElementById('lerr');
  errEl.textContent = '';

  if (!dept) { toast('กรุณาเลือกหน่วยงาน', '#e74c3c'); return; }
  if (!pass) { toast('กรุณากรอกรหัสผ่าน', '#e74c3c'); return; }
  if (pass !== getPw(dept)) {
    errEl.textContent = '❌ รหัสผ่านไม่ถูกต้อง กรุณาลองใหม่';
    document.getElementById('login-pass').value = '';
    document.getElementById('login-pass').focus();
    return;
  }

  // Check config
  if (!apiUrl && !useLocal) {
    showScreen('setup-screen');
    return;
  }

  currentUser = dept;
  sessionStorage.setItem('umsc31_user', dept);
  showScreen('app-screen');
  setupUI();
  loadData();
  startSync();
}

function doLogout() {
  currentUser = null;
  allData = [];
  gCharts = {};
  clearInterval(syncTimer);
  sessionStorage.removeItem('umsc31_user');
  document.getElementById('login-dept').value = '';
  document.getElementById('login-pass').value = '';
  document.getElementById('lerr').textContent = '';
  document.getElementById('tab-bar').innerHTML = '';
  showScreen('login-screen');
}

// ════════════════════════════════════════
// UI SETUP — แก้ bug: ไม่เรียก switchPage ทันที
// ════════════════════════════════════════
function setupUI() {
  const isAdmin = currentUser === '__admin__';
  document.getElementById('tb-dept').textContent = isAdmin ? '👑 Admin' : ((ICO[currentUser]||'') + ' ' + currentUser);

  const today = new Date();
  if (!isAdmin) {
    document.getElementById('inp-date').value = today.toISOString().split('T')[0];
    document.getElementById('inp-month').value = today.toISOString().slice(0,7);
    document.getElementById('form-title').textContent = `📋 บันทึกข้อมูลบริการ — ${currentUser}`;
    const thM = ['ม.ค.','ก.พ.','มี.ค.','เม.ย.','พ.ค.','มิ.ย.','ก.ค.','ส.ค.','ก.ย.','ต.ค.','พ.ย.','ธ.ค.'];
    document.getElementById('mtag').textContent = thM[today.getMonth()] + ' ' + (today.getFullYear()+543);
  }

  const tb = document.getElementById('tab-bar');
  // ── FIX: สร้าง tab ก่อน แล้วค่อย activate หลัง DOM update ──
  if (isAdmin) {
    tb.innerHTML = `<button class="tab-btn" data-page="admin">👑 Admin</button>`;
  } else {
    tb.innerHTML = `
      <button class="tab-btn" data-page="input">📋 บันทึก</button>
      <button class="tab-btn" data-page="history">📁 ประวัติ</button>
      <button class="tab-btn" data-page="dashboard">📊 Dashboard</button>`;
  }

  // attach click handlers
  tb.querySelectorAll('.tab-btn').forEach(btn => {
    btn.addEventListener('click', () => switchPage(btn.dataset.page, btn));
  });

  // activate first tab after DOM settles
  requestAnimationFrame(() => {
    const firstBtn = tb.querySelector('.tab-btn');
    if (firstBtn) switchPage(firstBtn.dataset.page, firstBtn);
  });
}

function switchPage(name, el) {
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  if (el) el.classList.add('active');
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  const pg = document.getElementById('page-' + name);
  if (pg) pg.classList.add('active');
  if (name === 'dashboard') renderDash();
  if (name === 'history') renderHistory();
  if (name === 'admin') { renderAdmin(); renderAdminTable(); }
}

// ════════════════════════════════════════
// DATA LAYER
// ════════════════════════════════════════
async function apiCall(method, body) {
  if (useLocal || !apiUrl) {
    return localApiCall(method, body);
  }
  const opts = method === 'GET'
    ? { method: 'GET' }
    : { method: 'POST', body: JSON.stringify(body) };
  const url = method === 'GET' ? apiUrl + '?action=read' : apiUrl;
  const res = await fetch(url, opts);
  return await res.json();
}

function localApiCall(method, body) {
  try {
    let data = JSON.parse(localStorage.getItem(LOCAL_KEY) || '[]');
    if (method === 'GET') return { ok: true, data };
    if (body.action === 'write') {
      data.push(body.data);
      localStorage.setItem(LOCAL_KEY, JSON.stringify(data));
      return { ok: true };
    }
    if (body.action === 'delete') {
      data = data.filter(r => r.id !== body.id);
      localStorage.setItem(LOCAL_KEY, JSON.stringify(data));
      return { ok: true };
    }
  } catch(e) { return { ok: false, error: e.message }; }
}

async function loadData() {
  setSyncStatus('ing', '⟳ กำลังโหลด...');
  try {
    const res = await apiCall('GET');
    if (res.ok) {
      allData = (res.data || []).map(r => ({
        ...r,
        clients: Number(r.clients)||0,
        sessions: Number(r.sessions)||0,
        over30: Number(r.over30)||0
      }));
      setSyncStatus('ok', '✓ ซิงค์แล้ว ' + new Date().toLocaleTimeString('th-TH',{hour:'2-digit',minute:'2-digit'}));
      refreshMonthFilters();
      refreshCurrentPage();
    } else throw new Error(res.error);
  } catch(e) {
    setSyncStatus('err', '⚠️ โหลดไม่ได้');
    toast('โหลดข้อมูลไม่ได้: ' + e.message, '#e74c3c');
  }
}

function startSync() {
  clearInterval(syncTimer);
  syncTimer = setInterval(loadData, 15000);
}

function refreshCurrentPage() {
  renderQuickSummary();
  const active = document.querySelector('.page.active')?.id;
  if (active === 'page-dashboard') renderDash();
  if (active === 'page-admin') { renderAdmin(); renderAdminTable(); }
  if (active === 'page-history') renderHistory();
}

// ════════════════════════════════════════
// SAVE / DELETE
// ════════════════════════════════════════
async function saveEntry() {
  const date = document.getElementById('inp-date').value;
  const month = document.getElementById('inp-month').value;
  const svc = document.getElementById('inp-svc').value;
  const notes = document.getElementById('inp-notes').value.trim();

  if (!date) { toast('กรุณาเลือกวันที่', '#e74c3c'); return; }
  if (!month) { toast('กรุณาเลือกเดือน', '#e74c3c'); return; }
  if (!svc) { toast('กรุณาเลือกรายการบริการ', '#e74c3c'); return; }

  const chs = [
    {name:'จุดบริการ', c:nv('c0c'), s:nv('c0s'), o:0},
    {name:'Line OA',   c:nv('c1c'), s:nv('c1s'), o:nv('c1o')},
    {name:'Facebook',  c:nv('c2c'), s:nv('c2s'), o:0},
    {name:'โทรศัพท์', c:nv('c3c'), s:nv('c3s'), o:0},
  ].filter(ch => ch.c > 0 || ch.s > 0);

  if (!chs.length) { toast('กรุณากรอกจำนวนอย่างน้อย 1 ช่องทาง', '#e74c3c'); return; }

  const btn = document.getElementById('save-btn');
  btn.disabled = true; btn.textContent = '⏳ กำลังบันทึก...';
  setSyncStatus('ing', '⟳ กำลังบันทึก...');

  let saved = 0;
  for (const ch of chs) {
    const row = {
      id: Date.now() + '_' + Math.random().toString(36).slice(2,8),
      date, month, dept: currentUser, service: svc,
      channel: ch.name, clients: ch.c, sessions: ch.s, over30: ch.o,
      notes, ts: new Date().toISOString()
    };
    const res = await apiCall('POST', { action: 'write', data: row });
    if (res.ok) { allData.push(row); saved++; }
  }

  btn.disabled = false; btn.textContent = '💾 บันทึกข้อมูล';
  if (saved > 0) {
    clearForm();
    refreshMonthFilters();
    renderQuickSummary();
    setSyncStatus('ok', '✓ บันทึกแล้ว');
    toast(`✅ บันทึก ${saved} รายการเรียบร้อย`);
  } else {
    setSyncStatus('err', '⚠️ บันทึกไม่สำเร็จ');
    toast('❌ บันทึกไม่สำเร็จ กรุณาลองใหม่', '#e74c3c');
  }
}

async function delEntry(id, isAdmin) {
  if (!confirm('ยืนยันการลบรายการนี้?')) return;
  const res = await apiCall('POST', { action: 'delete', id });
  if (res.ok) {
    allData = allData.filter(r => String(r.id) !== String(id));
    if (isAdmin) { renderAdmin(); renderAdminTable(); }
    else { renderHistory(); renderQuickSummary(); }
    toast('ลบรายการแล้ว', '#e74c3c');
  } else toast('ลบไม่สำเร็จ', '#e74c3c');
}

function clearForm() {
  document.getElementById('inp-svc').value = '';
  document.getElementById('inp-notes').value = '';
  ['c0c','c0s','c1c','c1s','c1o','c2c','c2s','c3c','c3s'].forEach(id => {
    const el = document.getElementById(id);
    if (el) el.value = '';
  });
}

// ════════════════════════════════════════
// RENDERS
// ════════════════════════════════════════
function renderQuickSummary() {
  if (!currentUser || currentUser === '__admin__') return;
  const month = document.getElementById('inp-month')?.value || '';
  const data = allData.filter(r => r.dept === currentUser && (!month || r.month === month));
  const el = document.getElementById('quick-summary');
  if (!data.length) { el.innerHTML = '<div class="empty">ยังไม่มีข้อมูลในเดือนที่เลือก</div>'; return; }
  const tc = S(data,'clients'), ts = S(data,'sessions'), to = S(data,'over30');
  el.innerHTML = `<div style="display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-bottom:10px">
    <div style="background:var(--pp);border-radius:8px;padding:10px;text-align:center"><div style="font-size:10px;color:var(--muted);font-weight:600;margin-bottom:3px">ผู้รับบริการ</div><div style="font-size:20px;font-weight:700;color:var(--pri)">${tc.toLocaleString()}</div></div>
    <div style="background:#d5f5e3;border-radius:8px;padding:10px;text-align:center"><div style="font-size:10px;color:var(--muted);font-weight:600;margin-bottom:3px">ครั้งบริการ</div><div style="font-size:20px;font-weight:700;color:#1e8449">${ts.toLocaleString()}</div></div>
    <div style="background:#fdebd0;border-radius:8px;padding:10px;text-align:center"><div style="font-size:10px;color:var(--muted);font-weight:600;margin-bottom:3px">เกิน 30 นาที</div><div style="font-size:20px;font-weight:700;color:#935116">${to.toLocaleString()}</div></div>
  </div><div style="font-size:11px;color:var(--muted)">${data.length} รายการ | ${[...new Set(data.map(r=>r.channel))].join(', ')}</div>`;
}

function renderHistory() {
  const mf = document.getElementById('hist-mon').value;
  const data = allData.filter(r => r.dept === currentUser && (!mf || r.month === mf)).slice().reverse();
  const cc = {'จุดบริการ':'bbl','Line OA':'bgr','Facebook':'bpk','โทรศัพท์':'bor'};
  const tb = document.getElementById('hist-body');
  if (!data.length) { tb.innerHTML = `<tr><td colspan="8" class="empty">ไม่พบข้อมูล</td></tr>`; return; }
  tb.innerHTML = data.map(r => `<tr>
    <td>${r.date}</td><td>${fm(r.month)}</td>
    <td style="max-width:160px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap" title="${r.service}">${r.service}</td>
    <td><span class="badge ${cc[r.channel]||'bbl'}">${r.channel}</span></td>
    <td style="text-align:center;font-weight:600">${r.clients}</td>
    <td style="text-align:center;font-weight:600">${r.sessions}</td>
    <td style="text-align:center">${r.over30||'-'}</td>
    <td><button onclick="delEntry('${r.id}',false)" style="background:none;border:none;cursor:pointer;color:var(--danger);font-size:14px;padding:2px 5px">🗑</button></td>
  </tr>`).join('');
}

function renderDash() {
  const mf = document.getElementById('dash-mon').value;
  const data = allData.filter(r => r.dept === currentUser && (!mf || r.month === mf));
  V('dc', S(data,'clients')); V('ds', S(data,'sessions')); V('do', S(data,'over30')); V('dr', data.length);
  drawPie('ch-pie','pie-leg', grpBy(data,'channel','clients'));
  drawHBar('svc-bar', grpBy(data,'service','sessions'));
  drawTrend('trend-line', currentUser);
}

function renderAdmin() {
  const mf = document.getElementById('adm-mon').value;
  const data = allData.filter(r => !mf || r.month === mf);
  V('ac', S(data,'clients')); V('as2', S(data,'sessions')); V('ar', data.length);
  V('ad', new Set(data.map(r=>r.dept)).size);
  document.getElementById('dept-status').innerHTML = DEPTS.map(d => {
    const dd = data.filter(r=>r.dept===d), has = dd.length > 0;
    return `<div class="dsi ${has?'done':'pend'}">
      <div style="font-size:15px;margin-bottom:2px">${ICO[d]||'🏥'}</div>
      <div style="font-size:10px;font-weight:600;color:${has?'#1e8449':'#922b21'}">${d}</div>
      <div style="font-size:10px;color:${has?'#1e8449':'#922b21'};margin-top:1px">${has?`✅ ${S(dd,'clients').toLocaleString()} ราย`:'⏳ ยังไม่บันทึก'}</div>
    </div>`;
  }).join('');
  const byD = {}; DEPTS.forEach(d=>{ byD[d]=S(data.filter(r=>r.dept===d),'clients'); });
  drawVBar('adm-bar', byD);
  drawPie('adm-pie','adm-pie-leg', grpBy(data,'channel','clients'));
  drawTrend('adm-trend', null);
}

function renderAdminTable() {
  const df = document.getElementById('adm-dept-f').value;
  const mf = document.getElementById('adm-mon').value;
  const data = allData.filter(r => (!df||r.dept===df) && (!mf||r.month===mf)).slice().reverse().slice(0,300);
  const cc = {'จุดบริการ':'bbl','Line OA':'bgr','Facebook':'bpk','โทรศัพท์':'bor'};
  const tb = document.getElementById('adm-body');
  if (!data.length) { tb.innerHTML = `<tr><td colspan="7" class="empty">ไม่พบข้อมูล</td></tr>`; return; }
  tb.innerHTML = data.map(r=>`<tr>
    <td>${r.date}</td><td>${ICO[r.dept]||''} ${r.dept}</td>
    <td style="max-width:150px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap" title="${r.service}">${r.service}</td>
    <td><span class="badge ${cc[r.channel]||'bbl'}">${r.channel}</span></td>
    <td style="text-align:center;font-weight:600">${r.clients}</td>
    <td style="text-align:center;font-weight:600">${r.sessions}</td>
    <td><button onclick="delEntry('${r.id}',true)" style="background:none;border:none;cursor:pointer;color:var(--danger);font-size:14px;padding:2px 5px">🗑</button></td>
  </tr>`).join('');
}

// ════════════════════════════════════════
// CHARTS
// ════════════════════════════════════════
function drawPie(cid, lid, grouped) {
  const labels=Object.keys(grouped), vals=labels.map(l=>grouped[l]), cols=labels.map(l=>CH_COL[l]||'#888');
  const lel=document.getElementById(lid);
  if(lel) lel.innerHTML=labels.map((l,i)=>`<span><span class="pdot" style="background:${cols[i]}"></span>${l}(${vals[i].toLocaleString()})</span>`).join('');
  dc(cid); if(!labels.length) return;
  gCharts[cid]=new Chart(document.getElementById(cid),{type:'doughnut',data:{labels,datasets:[{data:vals,backgroundColor:cols,borderWidth:2,borderColor:'#fff'}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}}}});
}

function drawHBar(cid, grouped) {
  const entries=Object.entries(grouped).sort((a,b)=>b[1]-a[1]).slice(0,8);
  const labels=entries.map(([l])=>l.length>15?l.slice(0,15)+'…':l), vals=entries.map(([,v])=>v);
  dc(cid); if(!labels.length) return;
  gCharts[cid]=new Chart(document.getElementById(cid),{type:'bar',data:{labels,datasets:[{data:vals,backgroundColor:'#1abc9c',borderRadius:4}]},options:{indexAxis:'y',responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{beginAtZero:true,ticks:{font:{size:10}}},y:{ticks:{font:{size:10}}}}}});
}

function drawVBar(cid, grouped) {
  const labels=DEPTS, vals=labels.map(l=>grouped[l]||0);
  dc(cid);
  gCharts[cid]=new Chart(document.getElementById(cid),{type:'bar',data:{labels,datasets:[{data:vals,backgroundColor:vals.map(v=>v>0?'#1a5276':'#d5e8f3'),borderRadius:5}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{ticks:{font:{size:10},maxRotation:45}},y:{beginAtZero:true}}}});
}

function drawTrend(cid, dept) {
  const src = dept ? allData.filter(r=>r.dept===dept) : allData;
  const months=[...new Set(src.map(r=>r.month))].sort();
  const cs=months.map(m=>S(src.filter(r=>r.month===m),'clients'));
  const ss=months.map(m=>S(src.filter(r=>r.month===m),'sessions'));
  dc(cid); if(!months.length) return;
  gCharts[cid]=new Chart(document.getElementById(cid),{type:'line',data:{labels:months.map(fm),datasets:[{label:'ผู้รับบริการ',data:cs,borderColor:'#1a5276',backgroundColor:'rgba(26,82,118,.1)',tension:.4,fill:true,pointRadius:4},{label:'ครั้งบริการ',data:ss,borderColor:'#1abc9c',backgroundColor:'rgba(26,188,156,.08)',tension:.4,fill:true,pointRadius:4}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{y:{beginAtZero:true}}}});
}

function dc(id) { if(gCharts[id]){gCharts[id].destroy();delete gCharts[id];} }

// ════════════════════════════════════════
// UTILS
// ════════════════════════════════════════
function S(arr,k){return arr.reduce((s,r)=>s+(Number(r[k])||0),0);}
function V(id,v){const el=document.getElementById(id);if(el)el.textContent=typeof v==='number'?v.toLocaleString():v;}
function nv(id){return Math.max(0,parseInt(document.getElementById(id)?.value)||0);}
function grpBy(data,kf,vf){const o={};data.forEach(r=>{o[r[kf]]=(o[r[kf]]||0)+(Number(r[vf])||0);});return o;}
function fm(ym){if(!ym)return'';const[y,m]=ym.split('-');return['ม.ค.','ก.พ.','มี.ค.','เม.ย.','พ.ค.','มิ.ย.','ก.ค.','ส.ค.','ก.ย.','ต.ค.','พ.ย.','ธ.ค.'][parseInt(m)-1]+' '+(parseInt(y)+543);}

function refreshMonthFilters(){
  const months=[...new Set(allData.map(r=>r.month))].sort().reverse();
  ['hist-mon','dash-mon','adm-mon'].forEach(id=>{
    const el=document.getElementById(id);if(!el)return;
    const cur=el.value;
    el.innerHTML='<option value="">ทุกเดือน</option>'+months.map(m=>`<option value="${m}"${m===cur?' selected':''}>${fm(m)}</option>`).join('');
  });
}

function setSyncStatus(state, text) {
  const el = document.getElementById('sync-st');
  if (!el) return;
  el.textContent = text;
  el.className = 'sync-st ' + state;
}

function showSpinner(msg) { document.getElementById('spinner').classList.add('show'); document.getElementById('spin-msg').textContent = msg||''; }
function hideSpinner() { document.getElementById('spinner').classList.remove('show'); }

let _toastTimer;
function toast(msg, color='#1abc9c') {
  const t = document.getElementById('toast');
  t.textContent = msg; t.style.background = color; t.style.opacity = '1';
  clearTimeout(_toastTimer);
  _toastTimer = setTimeout(()=>t.style.opacity='0', 3000);
}

// ════════════════════════════════════════
// EXPORT CSV
// ════════════════════════════════════════
function exportCSV() {
  const mf=document.getElementById('hist-mon').value;
  const data=allData.filter(r=>r.dept===currentUser&&(!mf||r.month===mf));
  if(!data.length){toast('ไม่มีข้อมูล','#e74c3c');return;}
  dlCSV(data, `UMSC_${currentUser}_${new Date().toISOString().slice(0,10)}.csv`);
}
function exportAdminCSV() {
  const df=document.getElementById('adm-dept-f').value;
  const mf=document.getElementById('adm-mon').value;
  const data=allData.filter(r=>(!df||r.dept===df)&&(!mf||r.month===mf));
  if(!data.length){toast('ไม่มีข้อมูล','#e74c3c');return;}
  dlCSV(data, `UMSC_รวม_${new Date().toISOString().slice(0,10)}.csv`);
}
function dlCSV(data, filename) {
  const h=['วันที่','เดือน','หน่วยงาน','รายการบริการ','ช่องทาง','ผู้รับบริการ','ครั้งบริการ','เกิน 30 นาที','หมายเหตุ'];
  const rows=data.map(r=>[r.date,r.month,r.dept,r.service,r.channel,r.clients,r.sessions,r.over30||0,r.notes||''].map(v=>`"${String(v).replace(/"/g,'""')}"`).join(','));
  const a=document.createElement('a');
  a.href='data:text/csv;charset=utf-8,\uFEFF'+encodeURIComponent([h.join(','),...rows].join('\r\n'));
  a.download=filename; a.click();
  toast(`⬇️ Export ${data.length} รายการ`);
}
</script>
</body>
</html>
