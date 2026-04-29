<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="theme-color" content="#1B3A6B">
<title>York SDA Planner</title>
<style>
/* ═══════════════════════════════════════════════════════
   RESET & BASE
═══════════════════════════════════════════════════════ */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
:root {
  --navy:  #1B3A6B;
  --navy2: #243f6e;
  --gold:  #C9A84C;
  --gold2: #d4b86a;
  --white: #ffffff;
  --bg:    #f0f3f9;
  --card:  #ffffff;
  --text:  #1a1a1a;
  --muted: #6b7a99;
  --border:#d5dff0;
  --green: #2e7d32;
  --red:   #b00020;
}
html, body { height: 100%; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif; background: var(--bg); color: var(--text); font-size: 15px; -webkit-tap-highlight-color: transparent; }

/* ═══════════════════════════════════════════════════════
   LAYOUT SHELL
═══════════════════════════════════════════════════════ */
#app { display: flex; flex-direction: column; height: 100vh; max-width: 520px; margin: 0 auto; background: var(--bg); position: relative; overflow: hidden; }

/* ── Top bar ── */
#topbar {
  background: var(--navy);
  color: white;
  padding: 14px 16px 10px;
  border-bottom: 3px solid var(--gold);
  flex-shrink: 0;
  position: relative;
}
#topbar h1 { font-size: 16px; font-weight: 700; letter-spacing: 0.04em; }
#topbar .sub { font-size: 11px; color: #aac4f0; margin-top: 1px; }
#week-badge {
  position: absolute; right: 16px; top: 50%; transform: translateY(-50%);
  background: var(--gold); color: #1a1000; font-size: 11px; font-weight: 700;
  padding: 3px 9px; border-radius: 20px; letter-spacing: 0.05em;
}

/* ── Tab bar ── */
#tabbar {
  display: flex;
  background: var(--navy);
  flex-shrink: 0;
}
.tab {
  flex: 1; text-align: center; padding: 10px 4px 8px;
  font-size: 11px; color: #7a9dd4; cursor: pointer;
  border-top: 3px solid transparent;
  transition: all 0.15s;
  user-select: none;
}
.tab.active { color: var(--gold); border-top-color: var(--gold); }
.tab-icon { font-size: 20px; display: block; margin-bottom: 2px; }

/* ── Screen container ── */
#screens { flex: 1; overflow: hidden; position: relative; }
.screen {
  position: absolute; inset: 0;
  overflow-y: auto; padding: 14px 12px 80px;
  display: none;
  -webkit-overflow-scrolling: touch;
}
.screen.active { display: block; }

/* ═══════════════════════════════════════════════════════
   CARDS & SECTIONS
═══════════════════════════════════════════════════════ */
.card {
  background: var(--card);
  border-radius: 12px;
  margin-bottom: 12px;
  box-shadow: 0 1px 4px rgba(0,0,0,0.07);
  overflow: hidden;
}
.card-head {
  background: var(--navy);
  color: white;
  font-size: 11px; font-weight: 700;
  letter-spacing: 0.1em; text-transform: uppercase;
  padding: 7px 14px;
}
.card-body { padding: 10px 14px 12px; }

/* ── Form fields ── */
.field { margin-bottom: 10px; }
.field label { display: block; font-size: 12px; font-weight: 700; color: var(--navy); margin-bottom: 3px; }
.field .hint { font-size: 11px; color: var(--muted); margin-bottom: 3px; }
.field input, .field textarea, .field select {
  width: 100%; border: 1.5px solid var(--border); border-radius: 8px;
  padding: 8px 11px; font-size: 14px; font-family: inherit;
  background: #fafbfd; color: var(--text);
  -webkit-appearance: none; appearance: none;
}
.field input:focus, .field textarea:focus, .field select:focus {
  outline: none; border-color: var(--navy); background: white;
}
.field textarea { resize: vertical; min-height: 70px; }
.field.inline { display: grid; grid-template-columns: 80px 1fr; gap: 8px; align-items: end; }
.field.inline label { grid-column: 1 / -1; margin-bottom: 2px; }

/* ── Fixed-role row ── */
.fixed-row {
  display: flex; justify-content: space-between; align-items: center;
  padding: 7px 0; border-bottom: 1px solid var(--border);
  font-size: 13px;
}
.fixed-row:last-child { border-bottom: none; }
.fixed-row .fr-label { font-weight: 700; color: var(--navy); flex: 0 0 130px; font-size: 12px; }
.fixed-row .fr-val { color: var(--muted); font-style: italic; font-size: 12px; }
.fixed-row .fr-override { margin-top: 4px; }

/* ── Hymn pair ── */
.hymn-pair { display: grid; grid-template-columns: 70px 1fr; gap: 6px; }

/* ── Gold rule ── */
.gold-rule { height: 2px; background: var(--gold); border-radius: 2px; margin: 8px 0; }

/* ═══════════════════════════════════════════════════════
   BUTTONS
═══════════════════════════════════════════════════════ */
.btn {
  display: block; width: 100%; padding: 13px;
  border: none; border-radius: 10px; font-size: 15px;
  font-weight: 700; cursor: pointer; text-align: center;
  transition: background 0.15s; font-family: inherit;
}
.btn-primary { background: var(--navy); color: white; }
.btn-primary:active { background: #0f2448; }
.btn-gold { background: var(--gold); color: #1a1000; }
.btn-gold:active { background: var(--gold2); }
.btn-sm {
  display: inline-block; padding: 6px 14px; border-radius: 20px;
  font-size: 12px; font-weight: 700; cursor: pointer; border: none;
  font-family: inherit;
}
.btn-copy-sm { background: var(--navy); color: white; }
.btn-copy-sm.copied { background: var(--green); }

/* ═══════════════════════════════════════════════════════
   SCREEN 2 — WHATSAPP MESSAGES
═══════════════════════════════════════════════════════ */
.wa-list { margin-bottom: 12px; }
.wa-item {
  background: white; border-radius: 12px; margin-bottom: 8px;
  box-shadow: 0 1px 4px rgba(0,0,0,0.07); overflow: hidden;
}
.wa-header {
  display: flex; justify-content: space-between; align-items: center;
  padding: 11px 14px; cursor: pointer; user-select: none;
}
.wa-role { font-size: 13px; font-weight: 700; color: var(--navy); }
.wa-name { font-size: 11px; color: var(--muted); margin-top: 1px; }
.wa-chevron { color: var(--muted); font-size: 18px; transition: transform 0.2s; }
.wa-item.open .wa-chevron { transform: rotate(90deg); }
.wa-body {
  display: none; padding: 0 14px 14px;
  border-top: 1px solid var(--border);
}
.wa-item.open .wa-body { display: block; }
.wa-text {
  background: #f0f3f9; border-radius: 8px; padding: 12px;
  font-size: 12.5px; line-height: 1.65; white-space: pre-wrap;
  word-break: break-word; color: var(--text);
  margin: 10px 0 8px; font-family: inherit;
}
.wa-actions { display: flex; gap: 8px; }

/* ═══════════════════════════════════════════════════════
   SCREEN 3 — ROTA
═══════════════════════════════════════════════════════ */
.rota-table { width: 100%; border-collapse: collapse; font-size: 12px; }
.rota-table th { background: var(--navy); color: white; padding: 7px 10px; text-align: left; font-size: 11px; }
.rota-table td { padding: 7px 10px; border-bottom: 1px solid var(--border); }
.rota-table tr:nth-child(even) td { background: #f5f7fc; }
.rota-table tr.this-week td { background: #fff8e1; font-weight: 700; }
.rota-table tr.this-week td:first-child::after { content: " ◀"; color: var(--gold); }

/* ═══════════════════════════════════════════════════════
   TOAST
═══════════════════════════════════════════════════════ */
#toast {
  position: fixed; bottom: 80px; left: 50%; transform: translateX(-50%) translateY(20px);
  background: var(--green); color: white; padding: 9px 22px; border-radius: 30px;
  font-size: 13px; font-weight: 700; opacity: 0; transition: all 0.25s;
  pointer-events: none; z-index: 999; white-space: nowrap;
}
#toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }

/* ═══════════════════════════════════════════════════════
   GENERATE BANNER
═══════════════════════════════════════════════════════ */
#gen-bar {
  position: fixed; bottom: 0; left: 50%; transform: translateX(-50%);
  width: 100%; max-width: 520px;
  background: white; border-top: 1px solid var(--border);
  padding: 10px 16px 14px;
  box-shadow: 0 -2px 12px rgba(0,0,0,0.08);
  z-index: 100;
  display: none;
}
#gen-bar.visible { display: block; }

/* ═══════════════════════════════════════════════════════
   EMPTY STATE
═══════════════════════════════════════════════════════ */
.empty-state {
  text-align: center; padding: 50px 20px; color: var(--muted);
}
.empty-state .icon { font-size: 48px; margin-bottom: 12px; }
.empty-state p { font-size: 14px; line-height: 1.5; }

/* ═══════════════════════════════════════════════════════
   UTILITY
═══════════════════════════════════════════════════════ */
.mb8 { margin-bottom: 8px; }
.mt8 { margin-top: 8px; }
.text-center { text-align: center; }
.muted { color: var(--muted); font-size: 12px; }
</style>
</head>
<body>
<div id="app">

  <!-- TOP BAR -->
  <div id="topbar">
    <h1>✦ YORK SDA PLANNER</h1>
    <div class="sub" id="topbar-sub">York Seventh-day Adventist Church</div>
    <div id="week-badge">Q2 2026</div>
  </div>

  <!-- TAB BAR -->
  <div id="tabbar">
    <div class="tab active" onclick="switchTab(0)" id="tab0">
      <span class="tab-icon">📋</span>Planner
    </div>
    <div class="tab" onclick="switchTab(1)" id="tab1">
      <span class="tab-icon">💬</span>WhatsApp
    </div>
    <div class="tab" onclick="switchTab(2)" id="tab2">
      <span class="tab-icon">📅</span>Rota
    </div>
    <div class="tab" onclick="switchTab(3)" id="tab3">
      <span class="tab-icon">🎵</span>Hymns
    </div>
  </div>

  <!-- SCREENS -->
  <div id="screens">

    <!-- ═══ SCREEN 0: PLANNER ════════════════════════════════════════════════ -->
    <div class="screen active" id="screen0">

      <!-- Service basics -->
      <div class="card">
        <div class="card-head">Service Details</div>
        <div class="card-body">
          <div class="field">
            <label>Date</label>
            <input type="date" id="f_date" onchange="onDateChange()">
          </div>
          <div class="field">
            <label>Occasion / Theme <span class="muted">(optional)</span></label>
            <input type="text" id="f_occasion" placeholder="e.g. Personal Ministries Day">
          </div>
          <div class="field">
            <label>Sermon Title</label>
            <input type="text" id="f_sermon" placeholder="Full title of the sermon">
          </div>
          <div class="field">
            <label>Sermon Subtitle <span class="muted">(optional)</span></label>
            <input type="text" id="f_subtitle" placeholder="e.g. A call to Watch, Pray and Prepare">
          </div>
        </div>
      </div>

      <!-- Auto-filled roles -->
      <div class="card">
        <div class="card-head">Roles — Auto-filled from Rota</div>
        <div class="card-body">
          <div class="fixed-row">
            <span class="fr-label">Elder on Duty</span>
            <span class="fr-val" id="af_elder">Select a date</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Preacher</span>
            <span class="fr-val" id="af_preacher">Select a date</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Deacons</span>
            <span class="fr-val" id="af_deacons">Select a date</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Pianist</span>
            <span class="fr-val">Sharon (always)</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Invocation</span>
            <span class="fr-val">Preacher (always)</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Scripture Reading</span>
            <span class="fr-val">Preacher (always)</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Benediction</span>
            <span class="fr-val">Preacher (always)</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Tithes &amp; Offerings</span>
            <span class="fr-val">Elder (always)</span>
          </div>
          <div class="fixed-row">
            <span class="fr-label">Mission Story</span>
            <span class="fr-val">Personal Min. Dept</span>
          </div>
        </div>
      </div>

      <!-- Variable roles -->
      <div class="card">
        <div class="card-head">Variable Roles</div>
        <div class="card-body">
          <div class="field">
            <label>Pastoral Prayer</label>
            <input type="text" id="f_pastoral" placeholder="Name">
          </div>
          <div class="field">
            <label>Children's Message</label>
            <input type="text" id="f_children" placeholder="Name">
          </div>
          <div class="field">
            <label>Chorister / Song Leader <span class="muted">(optional)</span></label>
            <input type="text" id="f_chorister" placeholder="Leave blank — defaults to 'Chorister'">
          </div>
          <div class="field">
            <label>SS Song Leader</label>
            <input type="text" id="f_ss_leader" placeholder="Name">
          </div>
          <div class="field">
            <label>Offering Designation</label>
            <input type="text" id="f_offering_desig" placeholder="e.g. General Fund / Mission">
          </div>
        </div>
      </div>

      <!-- Scripture -->
      <div class="card">
        <div class="card-head">Scripture Reading</div>
        <div class="card-body">
          <div class="field">
            <label>Passage Reference</label>
            <input type="text" id="f_scripture_ref" placeholder="e.g. Matthew 26:36-46 (confirm with preacher)">
          </div>
        </div>
      </div>

      <!-- Hymns -->
      <div class="card">
        <div class="card-head">Hymns</div>
        <div class="card-body">
          <p class="muted mb8">Introit: SDAH 672 · Recessional: SDAH 220 (fixed — no need to enter)</p>

          <div class="field">
            <label>Opening Song</label>
            <div class="hymn-pair">
              <input type="text" id="h_open_no" placeholder="No." oninput="lookupHymn('h_open_no','h_open_title')">
              <input type="text" id="h_open_title" placeholder="Title (auto-fills from number)">
            </div>
          </div>
          <div class="field">
            <label>Meditational Song</label>
            <div class="hymn-pair">
              <input type="text" id="h_med_no" placeholder="No." oninput="lookupHymn('h_med_no','h_med_title')">
              <input type="text" id="h_med_title" placeholder="Title">
            </div>
          </div>
          <div class="field">
            <label>Song Service — Hymn 1 <span class="muted">(or Chorister's choice)</span></label>
            <div class="hymn-pair">
              <input type="text" id="h_ss1_no" placeholder="No." oninput="lookupHymn('h_ss1_no','h_ss1_title')">
              <input type="text" id="h_ss1_title" placeholder="Title">
            </div>
          </div>
          <div class="field">
            <label>Song Service — Hymn 2</label>
            <div class="hymn-pair">
              <input type="text" id="h_ss2_no" placeholder="No." oninput="lookupHymn('h_ss2_no','h_ss2_title')">
              <input type="text" id="h_ss2_title" placeholder="Title">
            </div>
          </div>
          <div class="field">
            <label>Song Service — Hymn 3</label>
            <div class="hymn-pair">
              <input type="text" id="h_ss3_no" placeholder="No." oninput="lookupHymn('h_ss3_no','h_ss3_title')">
              <input type="text" id="h_ss3_title" placeholder="Title">
            </div>
          </div>
          <div class="field">
            <label>Closing Song <span class="muted">(or Chorister's choice)</span></label>
            <div class="hymn-pair">
              <input type="text" id="h_close_no" placeholder="No." oninput="lookupHymn('h_close_no','h_close_title')">
              <input type="text" id="h_close_title" placeholder="Title">
            </div>
          </div>
          <div class="field">
            <label>Song after Announcements <span class="muted">(or Chorister's choice)</span></label>
            <div class="hymn-pair">
              <input type="text" id="h_ann_no" placeholder="No." oninput="lookupHymn('h_ann_no','h_ann_title')">
              <input type="text" id="h_ann_title" placeholder="Title">
            </div>
          </div>
        </div>
      </div>

      <!-- Announcements -->
      <div class="card">
        <div class="card-head">Announcements</div>
        <div class="card-body">
          <div class="field">
            <div class="hint">One announcement per line</div>
            <textarea id="f_announcements" placeholder="Youth fellowship this Friday at 7pm&#10;Bible study resumes Tuesday..."></textarea>
          </div>
        </div>
      </div>

      <!-- Generate button -->
      <button class="btn btn-gold" onclick="generateAll()" style="margin-bottom:16px;">
        ✦ Generate All Program Outputs
      </button>

    </div>

    <!-- ═══ SCREEN 1: WHATSAPP ═══════════════════════════════════════════════ -->
    <div class="screen" id="screen1">
      <div id="wa-empty" class="empty-state">
        <div class="icon">💬</div>
        <p>Fill in the <strong>Planner</strong> tab and tap<br><strong>"Generate All Program Outputs"</strong><br>to produce your WhatsApp messages.</p>
      </div>
      <div id="wa-list" class="wa-list" style="display:none"></div>
    </div>

    <!-- ═══ SCREEN 2: ROTA ═══════════════════════════════════════════════════ -->
    <div class="screen" id="screen2">
      <div class="card">
        <div class="card-head">Preaching Plan — Q2 2026</div>
        <div class="card-body" style="padding:0; overflow:auto;">
          <table class="rota-table" id="preaching-table">
            <thead><tr><th>Date</th><th>Elder</th><th>Preacher</th><th>Occasion</th></tr></thead>
            <tbody id="preaching-body"></tbody>
          </table>
        </div>
      </div>
      <div class="card">
        <div class="card-head">Deacon Duty Rota — Q2 2026</div>
        <div class="card-body" style="padding:0; overflow:auto;">
          <table class="rota-table" id="deacons-table">
            <thead><tr><th>Date</th><th>Deacons on Duty</th></tr></thead>
            <tbody id="deacons-body"></tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- ═══ SCREEN 3: HYMN LOOKUP ════════════════════════════════════════════ -->
    <div class="screen" id="screen3">
      <div class="card">
        <div class="card-head">Hymn Lookup — SDAH (all 695)</div>
        <div class="card-body">
          <div class="field">
            <input type="text" id="hymn-search" placeholder="Type number or title to search..." oninput="hymnSearch()" autocomplete="off">
          </div>
        </div>
      </div>
      <div id="hymn-results"></div>
    </div>

  </div><!-- /screens -->

</div><!-- /app -->

<div id="toast">✓ Copied!</div>

<script>
// ═══════════════════════════════════════════════════════════════════════════════
// DATA
// ═══════════════════════════════════════════════════════════════════════════════

const PREACHING = [
  { date:"2026-04-04", elder:"Paulo",   preacher:"Prospect",                    occasion:"Day of Prayer & Fasting" },
  { date:"2026-04-11", elder:"Prospect",preacher:"Mike",                         occasion:"Communion" },
  { date:"2026-04-18", elder:"Mike",    preacher:"Rachel",                       occasion:"Teens Day" },
  { date:"2026-04-25", elder:"Bheki",   preacher:"Pr Piniel",                    occasion:"" },
  { date:"2026-05-02", elder:"Prospect",preacher:"Admire P",                     occasion:"" },
  { date:"2026-05-09", elder:"Paulo",   preacher:"Pr Piniel",                    occasion:"Personal Ministries Day" },
  { date:"2026-05-16", elder:"Mike",    preacher:"Njabulo Ndlovu (BUC PF Dir)",  occasion:"Adventurers Day" },
  { date:"2026-05-23", elder:"Bheki",   preacher:"Shepherdess Ashriah",          occasion:"" },
  { date:"2026-05-30", elder:"Prospect",preacher:"Children",                     occasion:"Children's Day" },
  { date:"2026-06-06", elder:"Bheki",   preacher:"Claire",                       occasion:"Sabbath School Day" },
  { date:"2026-06-13", elder:"Mike",    preacher:"Women's Ministries TBC",       occasion:"Women's Day" },
  { date:"2026-06-20", elder:"Paulo",   preacher:"Family Ministries TBC",        occasion:"Family Ministries" },
  { date:"2026-06-27", elder:"Bheki",   preacher:"Paulo",                        occasion:"Personal Ministries Day" },
];

const DEACONS = [
  { date:"2026-04-04", names:"Phango, Mataruse, Jubilee, Rachel" },
  { date:"2026-04-11", names:"Eden, Shasha, Dawn, Deborah" },
  { date:"2026-04-18", names:"Finley, Emmanuel, Mavis, Bianca" },
  { date:"2026-04-25", names:"Divine, Phango, Deborah, Purity" },
  { date:"2026-05-02", names:"Mataruse, Eden, Rachel, Dawn" },
  { date:"2026-05-09", names:"Finley, Emmanuel, Mavis, Jubilee" },
  { date:"2026-05-16", names:"Eden, Divine, Bianca, Purity" },
  { date:"2026-05-23", names:"Phango, Shasha, Rachel, Deborah" },
  { date:"2026-05-30", names:"Mataruse, Emmanuel, Jubilee, Dawn" },
  { date:"2026-06-06", names:"Finley, Divine, Mavis, Purity" },
  { date:"2026-06-13", names:"Eden, Phango, Rachel, Deborah" },
  { date:"2026-06-20", names:"Shasha, Mataruse, Dawn, Bianca" },
  { date:"2026-06-27", names:"Finley, Emmanuel, Mavis, Jubilee" },
];

const HYMNS = {
1:"Praise to the Lord",2:"All Creatures of Our God and King",3:"God Himself Is With Us",4:"Praise, My Soul, the King of Heaven",5:"All My Hope on God Is Founded",6:"O Worship the Lord",7:"The Lord in Zion Reigneth",8:"We Gather Together",9:"Let All the World in Every Corner Sing",10:"Come, Christians, Join to Sing",11:"The God of Abraham Praise",12:"Joyful, Joyful, We Adore Thee",13:"New Songs of Celebration Render",14:"Let Us Praise the Name of the Lord",15:"My Maker and My King",16:"All People That on Earth Do Dwell",17:"Lord of All Being, Throned Afar",18:"O Morning Star, How Fair and Bright",19:"O Sing a New Song to the Lord",20:"O Praise Ye the Lord",21:"Immortal, Invisible, God Only Wise",22:"God Is Our Song",23:"Now the Joyful Bells A-Ringing",24:"Every Star Shall Sing a Carol",25:"Praise the Lord, His Glories Show",26:"Praise the Lord! You Heavens Adore Him",27:"Rejoice Ye Pure in Heart",28:"Praise We the Lord",29:"Sing Praise to God",30:"Holy God, We Praise Your Name",31:"Tell Out, My Soul",32:"When in Our Music God Is Glorified",33:"Sing a New Song to the Lord",34:"Wake the Song",35:"With Songs and Honors",36:"O Thou in Whose Presence",37:"O Sing, My Soul, Your Maker's Praise",38:"Arise, My Soul, Arise",39:"Lord, in the Morning",40:"The Dawn of God's Dear Sabbath",41:"O Splendor of God's Glory Bright",42:"Now That the Daylight Fills the Sky",43:"When Morning Gilds the Skies",44:"Morning Has Broken",45:"Open Now Thy Gates of Beauty",46:"Abide With Me, 'Tis Eventide",47:"God, Who Made the Earth and Heaven",48:"Softly Now the Light of Day",49:"Savior, Breathe an Evening Blessing",50:"Abide With Me",51:"Day Is Dying in the West",52:"Now the Day Is Over",53:"All Praise to Thee",54:"O Gladsome Light",55:"Jesus, Tender Shepherd, Hear Me",56:"The Day Thou Gavest",57:"Now All the Woods Are Sleeping",58:"Hark, the Vesper Hymn Is Stealing",59:"Great Our Joy As Now We Gather",60:"Blessed Jesus, at Thy Word",61:"God Is Here",62:"How Lovely Is Thy Dwelling Place",63:"O Come, Let Us Sing to the Lord",64:"Lord, Dismiss Us With Thy Blessing",65:"God Be With You",66:"God Be With You",67:"O Lord, Now Let Your Servant",68:"On Our Way Rejoicing",69:"Lord, Make Us More Holy",70:"Praise Ye the Father",71:"Come, Thou Almighty King",72:"Creator of the Stars of Night",73:"Holy, Holy, Holy",74:"Like a River Glorious",75:"The Wonder of It All",76:"O Love That Wilt Not Let Me Go",77:"O Love of God Most Full",78:"For God So Loved Us",79:"O Love of God, How Strong and True",80:"O World of God",81:"Though I Speak With Tongues",82:"Before Jehovah's Awful Throne",83:"O Worship the King",84:"God the Omnipotent",85:"Eternal Father, Strong to Save",86:"How Great Thou Art",87:"God Who Spoke in the Beginning",88:"I Sing the Mighty Power of God",89:"Let All on Earth Their Voices Raise",90:"Eternal God, Whose Power Upholds",91:"Ye Watchers and Ye Holy Ones",92:"This Is My Father's World",93:"All Things Bright and Beautiful",94:"Nature With Open Volume Stands",95:"Spring Has Now Unwrapped the Flowers",96:"The Spacious Firmament",97:"Lord of the Boundless Curves of Space",98:"Can You Count the Stars?",99:"God Will Take Care of You",100:"Great Is Thy Faithfulness",101:"Children of the Heavenly Father",102:"Unto the Hills",103:"O God, Our Help",104:"My Shepherd Will Supply My Need",105:"Sing to the Great Jehovah's Praise",106:"Give to Our God Immortal Praise",107:"God Moves in a Mysterious Way",108:"Amazing Grace",109:"Marvelous Grace",110:"God's Free Mercy Streameth",111:"It Took a Miracle",112:"Let Us With a Gladsome Mind",113:"As Pants the Hart",114:"There's a Wideness",115:"O Come, O Come, Immanuel",116:"Of the Father's Love Begotten",117:"The Advent of Our God",118:"The First Noel",119:"Angels From the Realms of Glory",120:"There's a Song in the Air",121:"Go, Tell It on the Mountain",122:"Hark! The Herald Angels Sing",123:"As With Gladness Men of Old",124:"Away in a Manger",125:"Joy to the World",126:"In the Bleak Midwinter",127:"Infant Holy, Infant Lowly",128:"Break Forth, O Beauteous Heavenly Light",129:"As It Fell Upon a Night",130:"It Came Upon the Midnight Clear",131:"Lo, How a Rose E'er Blooming",132:"O Come, All Ye Faithful",133:"Now Is Born the Divine Christ Child",134:"O Jesus Sweet",135:"O Little Town of Bethlehem",136:"Good Christians, Now Rejoice",137:"We Three Kings",138:"Rise Up, Shepherd, and Follow",139:"While Shepherds Watched Their Flocks",140:"Thou Didst Leave Thy Throne",141:"What Child Is This",142:"Angels We Have Heard on High",143:"Silent Night, Holy Night",144:"O Sing a Song of Bethlehem",145:"Songs of Thankfulness and Praise",146:"I Think When I Read That Sweet Story",147:"Christ Upon the Mountain Peak",148:"O Love, How Deep, How Broad",149:"Once in Royal David's City",150:"Who Is He in Yonder Stall",151:"Jesus Walked This Lonesome Valley",152:"Tell Me the Story of Jesus",153:"Prince of Peace, Control My Will",154:"When I Survey the Wondrous Cross",155:"When I Survey the Wondrous Cross",156:"O Sacred Head Now Wounded",157:"Go to Dark Gethsemane",158:"Were You There",159:"The Old Rugged Cross",160:"Ride On in Majesty",161:"Throned Upon the Awful Tree",162:"Wondrous Love",163:"At the Cross",164:"There Is a Green Hill Far Away",165:"Look, You Saints! The Sight Is Glorious",166:"Christ the Lord Is Risen Today",167:"Alleluia! Sing to Jesus",168:"And Have the Bright Immensities",169:"Come, You Faithful",170:"Come, You Faithful",171:"Thine Is the Glory",172:"The Strife Is O'er",173:"Good Christian Friends, Rejoice",174:"Star of Our Hope",175:"Now the Green Blade Rises",176:"Hail the Day That Sees Him Rise",177:"Jesus, Your Blood and Righteousness",178:"The Unveiled Christ",179:"The Wonders of Redeeming Love",180:"O Listen to Our Wondrous Story",181:"Does Jesus Care",182:"Christ Is Alive",183:"I Will Sing of Jesus' Love",184:"Jesus Paid It All",185:"Jesus Is All the World to Me",186:"I've Found a Friend",187:"Jesus, What a Friend for Sinners",188:"My Song Is Love Unknown",189:"All That Thrills My Soul",190:"Jesus Loves Me",191:"Love Divine",192:"O Shepherd Divine",193:"Savior, Teach Me",194:"Sing We of the Modern City",195:"Showers of Blessing",196:"Tell Me the Old, Old Story",197:"The King of Love My Shepherd Is",198:"And Can It Be",199:"The Head That Once Was Crowned",200:"The Lord Is Coming",201:"Christ Is Coming",202:"Hail Him the King of Glory",203:"This Is the Threefold Truth",204:"Come, Thou Long Expected Jesus",205:"Gleams of the Golden Morning",206:"Face to Face",207:"It May Be at Morn",208:"There'll Be No Dark Valley",209:"That Glorious Day Is Coming",210:"Wake, Awake, for Night Is Flying",211:"Lo! He Comes",212:"Tis Almost Time for the Lord to Come",213:"Jesus Is Coming Again",214:"We Have This Hope",215:"The King Shall Come",216:"When the Roll Is Called Up Yonder",217:"The Church Has Waited Long",218:"When He Cometh",219:"When Jesus Comes in Glory",220:"When He Comes",221:"Rejoice, the Lord Is King",222:"Hark! Ten Thousand Harps and Voices",223:"Crown Him With Many Crowns",224:"Seek Ye First the Kingdom",225:"God Is Working His Purpose Out",226:"Lift Up Your Heads",227:"Jesus Shall Reign",228:"A Hymn of Glory Let Us Sing",229:"All Hail the Power of Jesus' Name",230:"All Glory, Laud, and Honor",231:"Blest Be the King",232:"At the Name of Jesus",233:"Christ, Whose Glory Fills the Skies",234:"Christ Is the World's Light",235:"Christ Is Made the Sure Foundation",236:"I Love Thee",237:"In the Cross of Christ I Glory",238:"How Sweet the Name!",239:"Jesus, Priceless Treasure",240:"Fairest Lord Jesus",241:"Jesus, the Very Thought of Thee",242:"Jesus, Thou Joy of Loving Hearts",243:"King of Glory, King of Peace",244:"My Song Shall Be of Jesus",245:"More About Jesus",246:"Worthy, Worthy Is the Lamb",247:"Come, My Way",248:"O, How I Love Jesus",249:"Praise Him! Praise Him!",250:"O for a Thousand Tongues to Sing",251:"He Lives",252:"Come, Let Us Sing",253:"There's No Other Name Like Jesus",254:"The Great Physician Now is Near",255:"I Cannot Tell Why",256:"Ye Servants of God",257:"Come Down, O Love Divine",258:"Baptize Us Anew",259:"Draw Us in the Spirit's Tether",260:"Hover O'er Me, Holy Spirit",261:"The Spirit of the Lord Revealed",262:"Sweet, Sweet Spirit",263:"Fire of God, Thou Sacred Flame",264:"O for That Flame of Living Fire",265:"Breathe on Me, Breath of God",266:"Spirit of God",267:"Spirit Divine",268:"Holy Spirit, Light Divine",269:"Come, Holy Spirit",270:"O Holy Dove of God Descending",271:"Break Thou the Bread of Life",272:"Give Me the Bible",273:"Lord, I Have Made Thy Word My Choice",274:"O Word of God Incarnate",275:"O God of Light",276:"Thanks to God",277:"For Your Holy Book We Thank You",278:"Lord Jesus, Once You Spoke to Men",279:"Only Trust Him",280:"Come, Ye Sinners",281:"I Gave My Life for Thee",282:"I Hear Thy Welcome Voice",283:"O Jesus, Thou Art Standing",284:"For You I Am Praying",285:"Jesus Calls Us",286:"Wonderful Words of Life",287:"Softly and Tenderly",288:"I Am Going to Calvary",289:"The Savior Is Waiting",290:"Turn Your Eyes Upon Jesus",291:"We Have Not Known Thee",292:"Jesus, I Come",293:"Heavenly Father, Bless Us Now",294:"Power in the Blood",295:"Chief of Sinners",296:"Lord, I'm Coming Home",297:"God Be Merciful to Me",298:"I Lay My Sins on Jesus",299:"Forgive Our Sins as We Forgive",300:"Rock of Ages",301:"Nearer, Still Nearer",302:"Deeper Yet",303:"Beneath the Cross of Jesus",304:"Faith of Our Fathers",305:"Give Me Jesus",306:"Draw Me Nearer",307:"I Am Coming to the Cross",308:"Wholly Thine",309:"I Surrender All",310:"I Would Draw Nearer to Jesus",311:"I Would Be Like Jesus",312:"Near the Cross",313:"Just as I Am",314:"Just as I Am",315:"O for a Closer Walk!",316:"Live Out Thy Life Within Me",317:"Lead Me to Calvary",318:"Whiter Than Snow",319:"Lord, I Want to Be a Christian",320:"Lord of Creation",321:"My Jesus, I Love Thee",322:"Nothing Between",323:"O for a Heart to Praise My God!",324:"Just as I Am, Thine Own to Be",325:"Jesus, I My Cross Have Taken",326:"Open My Eyes That I May See",327:"I'd Rather Have Jesus",328:"Must Jesus Bear the Cross Alone",329:"Take the World, but Give Me Jesus",330:"Take My Life and Let It Be",331:"O Jesus, I Have Promised",332:"The Cleansing Wave",333:"On Jordan's Banks the Baptist's Cry",334:"Come, Thou Fount of Every Blessing",335:"What a Wonderful Savior",336:"There Is a Fountain",337:"Redeemed!",338:"Redeemed!",339:"God Is My Strong Salvation",340:"Jesus Saves",341:"To God Be the Glory",342:"Is This a Day of New Beginnings?",343:"I Will Sing of My Redeemer",344:"I Love Your Kingdom, Lord",345:"Christ Is the World's True Light",346:"Lord, Who Dost Give to Thy Church",347:"Built on the Rock",348:"The Church Has One Foundation",349:"God is Love",350:"Blest Be the Tie That Binds",351:"Thy Hand, O God Has Guided",352:"This Is My Will",353:"Father, Help Your People",354:"Thy Love, O God",355:"Where Cross the Crowded Ways of Life",356:"All Who Love and Serve Your City",357:"Come, Labor On",358:"Far and Near the Fields Are Teeming",359:"Hark! the Voice of Jesus Calling",360:"From the Eastern Mountains",361:"Hark! 'Tis the Shepherd's Voice I Hear",362:"Lift High the Cross",363:"Lord, Whose Love in Humble Service",364:"O Jesus Christ, to You",365:"O Zion, Haste",366:"O Where Are the Reapers?",367:"Rescue the Perishing",368:"Watchman, Blow the Gospel Trumpet",369:"Bringing in the Sheaves",370:"Christ for the World",371:"Lift Him Up",372:"How Beauteous Are Their Feet",373:"Seeking the Lost",374:"Jesus, With Thy Church Abide",375:"Work, for the Night Is Coming",376:"All Things Are Thine",377:"Go Forth, Go Forth With Christ",378:"Go, Preach My Gospel",379:"We Give This Child to You",380:"Welcome, Day of Sweet Repose",381:"Holy Sabbath Day of Rest",382:"O Day of Rest and Gladness",383:"O Day of Rest and Gladness",384:"Safely Through Another Week",385:"Crowning Jewel of Creation",386:"The Sacred Anthem",387:"Come, O Sabbath Day",388:"Don't Forget the Sabbath",389:"Light of Light, Enlighten Me",390:"We Love Thy Sabbath, Lord",391:"Welcome, Welcome, Day of Rest",392:"Dear Lord, We Come at Set of Sun",393:"Lord of the Sabbath",394:"Far From All Care",395:"As Birds Unto the Genial Homeland",396:"Lord God, Your Love Has Called Us Here",397:"An Upper Room",398:"Bread of the World",399:"Beneath the Forms of Outward Rite",400:"I Come With Joy",401:"In Imitation, Lord of Thee",402:"By Christ Redeemed",403:"Let Us Break Bread Together",404:"Now Let Us From This Table Rise",405:"O God, Unseen, Yet Ever Near",406:"Love Consecrates the Humblest Act",407:"Sent Forth by God's Blessing",408:"Lord, Enthroned in Heavenly Splendor",409:"Jesus Invites His Saints",410:"Thy Broken Body, Gracious Lord",411:"The Son of God Proclaim",412:"Cover With His Life",413:"God Has Spoken by His Prophets",414:"Fruitful Trees, the Spirit's Sowing",415:"Christ the Lord, All Power Possessing",416:"The Judgment Has Set",417:"O Solemn Thought",418:"Day of Judgment, Day of Wonders!",419:"Soon Shall the Trump of God",420:"Jerusalem, My Happy Home",421:"For All the Saints",422:"Marching to Zion",423:"Glorious Things of Thee Are Spoken",424:"For Thee, O Dear, Dear Country",425:"Holy, Holy, Is What the Angels Sing",426:"I Shall See the King",427:"No Night There",428:"Sweet By and By",429:"Jerusalem the Golden",430:"Joy By and By",431:"Over Yonder",432:"Shall We Gather at the River",433:"Ten Thousand Times Ten Thousand",434:"We Speak of the Realms",435:"The Glory Song",436:"The Homeland",437:"I'm Going Home",438:"You Will See Your Lord A-Coming",439:"How Far From Home?",440:"How Cheering Is the Christian's Hope",441:"I Saw One Weary",442:"How Sweet Are the Tidings",443:"There'll Be No Sorrow There",444:"I'm a Pilgrim",445:"I'm But a Stranger Here",446:"Lo, What a Glorious Sight Appears",447:"Long Upon the Mountains",448:"O, When Shall I See Jesus",449:"Never Part Again",450:"Beautiful Zion",451:"Together Let Us Sweetly Live",452:"What Heavenly Music",453:"We Have Heard",454:"Don't You See My Jesus Coming?",455:"Immortal Love, Forever Full",456:"My Lord and I",457:"I Love to Tell the Story",458:"More Love to Thee",459:"As the Bridegroom to His Chosen",460:"As Water to the Thirsty",461:"Be Still, My Soul",462:"Blessed Assurance, Jesus is Mine!",463:"Peace, Perfect Peace",464:"When I Can Read My Title Clear",465:"I Heard the Voice of Jesus",466:"Wonderful Peace",467:"Life Is Great! So Sing About It",468:"A Child of the King",469:"Leaning on the Everlasting Arms",470:"There's Sunshine in My Soul Today",471:"Grant Us Your Peace",472:"A Song of Heaven and Homeland",473:"Nearer My God to Thee",474:"Take the Name of Jesus With You",475:"Balm in Gilead",476:"Burdens Are Lifted at Calvary",477:"Come, Ye Disconsolate",478:"Sweet Hour of Prayer",479:"Tread Softly",480:"Dear Lord and Father",481:"Dear Lord and Father",482:"Father, Lead Me Day by Day",483:"I Need Thee Every Hour",484:"I Need Thee, Precious Jesus",485:"I Must Tell Jesus",486:"I Do Believe",487:"In the Garden",488:"At First I Prayed for Light",489:"Jesus, Lover of My Soul",490:"Jesus, Lover of My Soul",491:"In the Hour of Trial",492:"Like Jesus",493:"Fill My Cup, Lord",494:"We Would See Jesus",495:"Near to the Heart of God",496:"Eternal Love, We Have No Good",497:"O Gracious Father of Mankind",498:"Still, Still With Thee",499:"What a Friend We Have in Jesus",500:"Take Time to Be Holy",501:"Tis the Blessed Hour of Prayer",502:"Sun of My Soul",503:"A Quiet Place",504:"Lord Jesus, Think on Me",505:"I Need the Prayers",506:"A Mighty Fortress",507:"Moment by Moment",508:"Anywhere With Jesus",509:"How Firm a Foundation",510:"If You But Trust in God to Guide You",511:"I Know Whom I Have Believed",512:"Just When I Need Him Most",513:"In Heavenly Love Abiding",514:"Lord of Our Life",515:"The Lord Is My Light",516:"All the Way My Savior Leads Me",517:"My Faith Looks Up to Thee",518:"Standing on the Promises",519:"Give to the Winds Your Fears",520:"He Hideth My Soul",521:"Depth of Mercy",522:"My Hope Is Built on Nothing Less",523:"My Faith Has Found a Resting Place",524:"Tis So Sweet to Trust in Jesus",525:"Hiding in Thee",526:"Because He Lives",527:"From Every Stormy Wind",528:"A Shelter in the Time of Storm",529:"Under His Wings",530:"It Is Well With My Soul",531:"We'll Build on the Rock",532:"Day by Day",533:"O for a Faith",534:"Will Your Anchor Hold?",535:"I Am Trusting Thee, Lord Jesus",536:"God, Who Stretched the Spangled Heavens",537:"He Leadeth Me",538:"Guide Me, O Thou Great Jehovah",539:"I Will Early Seek the Savior",540:"Gentle Jesus, Meek and Mild",541:"Lord, Speak to Me",542:"Jesus, Friend So Kind",543:"Jesus, Friend of Little Children",544:"Jesus, Son of Blessed Mary",545:"Savior, Like a Shepherd",546:"The Lord's My Shepherd",547:"Be Thou My Vision",548:"Now Praise the Hidden God of Love",549:"Loving Shepherd of Thy Sheep",550:"Every Flower That Grows",551:"Jesus, Savior, Pilot Me",552:"The Lord's My Shepherd",553:"Jesus, Guide Our Way",554:"O Let Me Walk With Thee",555:"Shepherd of Tender Youth",556:"As Saints of Old",557:"Come, Ye Thankful People",558:"For the Fruits of His Creation",559:"Now Thank We All Our God",560:"Let All Things Now Living",561:"We Plow the Fields",562:"Come, Sing a Song of Harvest",563:"Praise and Thanksgiving",564:"For Sunrise Hope and Sunset Calm",565:"For the Beauty of the Earth",566:"Father, We Thank You",567:"Have Thine Own Way, Lord",568:"Make Me a Captive, Lord",569:"Pass Me Not, O Gentle Savior",570:"Not I, But Christ",571:"What Does the Lord Require?",572:"Give of Your Best to the Master",573:"I'll Go Where You Want Me to Go",574:"O Master, Let Me Walk With Thee",575:"Let Your Heart Be Broken",576:"Awake, Awake to Love and Work",577:"In the Heart of Jesus",578:"So Send I You",579:"Tis Love That Makes Us Happy",580:"This Little Light of Mine",581:"When the Church of Jesus",582:"Working, O Christ, With Thee",583:"You That Know the Lord",584:"There's a Spirit in the Air",585:"When Christ Was Lifted From the Earth",586:"What Joy It Is to Worship Here",587:"In Christ There Is No East nor West",588:"Lord of All Nations",589:"Holy Spirit, Gracious Guest",590:"Trust and Obey",591:"In Our Work and in Our Play",592:"Watchman, Tell Us of the Night",593:"In Times Like These",594:"Heir of the Kingdom",595:"Let Every Lamp Be Burning",596:"Look for the Waymarks",597:"Ye Servants of the Lord",598:"Watch, Ye Saints",599:"Rejoice, Rejoice, Believers",600:"Hold Fast Till I Come",601:"Watchmen, on the Walls of Zion",602:"O Brother, Be Faithful",603:"Christian, Seek Not Repose",604:"We Know Not the Hour",605:"My Soul, Be on Thy Guard",606:"Once to Every Man and Nation",607:"God of Grace and God of Glory",608:"Faith Is the Victory",609:"Am I a Soldier of the Cross?",610:"Stand Like the Brave",611:"Awake, My Soul!",612:"Onward, Christian Soldiers!",613:"Fight the Good Fight",614:"Sound the Battle Cry",615:"Rise Up, O Church of God",616:"Soldiers of Christ, Arise",617:"We Are Living, We Are Dwelling",618:"Stand Up! Stand Up for Jesus!",619:"Lead On, O King Eternal",620:"On Jordan's Stormy Banks",621:"Gracious Father, Guard Thy Children",622:"Come, Come, Ye Saints",623:"I Will Follow Thee",624:"I Want Jesus to Walk With Me",625:"Higher Ground",626:"In a Little While We're Going Home",627:"Jacob's Ladder",628:"As Jacob With Travel Was Weary",629:"O Happy Band of Pilgrims",630:"Rise, My Soul, and Stretch Thy Wings",631:"When on Life a Darkness Falls",632:"Until Then",633:"When We All Get to Heaven",634:"Come, All Christians, Be Committed",635:"Lord of All Good",636:"God, Whose Giving Knows No Ending",637:"Son of God, Eternal Savior",638:"The Wise May Bring Their Learning",639:"A Diligent and Grateful Heart",640:"For Beauty of Meadows",641:"God in His Love for Us",642:"We Praise Thee With Our Minds",643:"Father, Who on Us Do Shower",644:"O God, Whose Will Is Life and Good",645:"God of Our Fathers",646:"To the Name That Brings Salvation",647:"Mine Eyes Have Seen the Glory",648:"I Vow to Thee, My Country",649:"Lord, While for All Mankind",650:"Our Father, by Whose Name",651:"Happy the Home That Welcomes You",652:"Love at Home",653:"Lead Them, My God, to Thee",654:"Lord, Bless Our Homes",655:"Happy the Home",656:"O Perfect Love",657:"O God, From Whom Mankind",658:"Heavenly Father, Hear Our Prayer",659:"May the Grace of Christ Our Savior",660:"Glory Be to the Father",661:"Holy, Holy, Holy",662:"Let All Mortal Flesh Keep Silence",663:"Amens",664:"Sevenfold Amen",665:"All Things Come of Thee",666:"Cast Thy Burden Upon the Lord",667:"Lord, Bless Thy Word to Every Heart",668:"O Thou Who Hearest",669:"The Lord Bless You and Keep You",670:"We Give Thee But Thine Own",671:"Now, Dear Lord, As We Pray",672:"Spirit of the Living God",673:"May God Be With You",674:"Shalom",675:"May the Lord Bless and Keep You",676:"Thy Word Is a Lantern",677:"Heavenly Father, to Thee We Pray",678:"God Be in My Head",679:"God Be in My Head",680:"Holy Spirit, Hear Us",681:"This Is the Day the Lord Hath Made",682:"As You Have Promised, Lord",683:"Jesus Stand Among Us",684:"Hear Our Prayer, O Lord",685:"Cause Me to Hear",686:"Bless Thou the Gifts",687:"The Lord Is in His Holy Temple",688:"Surely, Surely",689:"Day by Day, Dear Lord",690:"Dismiss Us, Lord, With Blessing",691:"Lead Me, Lord",692:"The Lord Is in His Holy Temple",693:"Almighty Father",694:"Praise God, From Whom All Blessings",695:"Praise God, From Whom All Blessings"
};

// ═══════════════════════════════════════════════════════════════════════════════
// STATE
// ═══════════════════════════════════════════════════════════════════════════════
let generatedData = null;

// ═══════════════════════════════════════════════════════════════════════════════
// TABS
// ═══════════════════════════════════════════════════════════════════════════════
function switchTab(idx) {
  document.querySelectorAll('.tab').forEach((t,i) => t.classList.toggle('active', i===idx));
  document.querySelectorAll('.screen').forEach((s,i) => s.classList.toggle('active', i===idx));
}

// ═══════════════════════════════════════════════════════════════════════════════
// DATE CHANGE — auto-fill from rota
// ═══════════════════════════════════════════════════════════════════════════════
function onDateChange() {
  const d = document.getElementById('f_date').value;
  const p = PREACHING.find(r => r.date === d);
  const dc = DEACONS.find(r => r.date === d);
  document.getElementById('af_elder').textContent   = p  ? p.elder   : '—';
  document.getElementById('af_preacher').textContent= p  ? p.preacher: '—';
  document.getElementById('af_deacons').textContent = dc ? dc.names  : '—';
  if (p && p.occasion) document.getElementById('f_occasion').value = p.occasion;
  // update topbar
  if (d) {
    const dt = fmtDate(d);
    document.getElementById('topbar-sub').textContent = dt;
  }
  // highlight rota rows
  buildRotaTables(d);
}

// ═══════════════════════════════════════════════════════════════════════════════
// HYMN AUTO-LOOKUP
// ═══════════════════════════════════════════════════════════════════════════════
function lookupHymn(noId, titleId) {
  const no = parseInt(document.getElementById(noId).value);
  if (no && HYMNS[no]) {
    document.getElementById(titleId).value = HYMNS[no];
  }
}

// ═══════════════════════════════════════════════════════════════════════════════
// HELPERS
// ═══════════════════════════════════════════════════════════════════════════════
function v(id) { return (document.getElementById(id)?.value||'').trim(); }

function fmtDate(iso) {
  if (!iso) return '';
  const d = new Date(iso + 'T00:00:00');
  const days = ['Sunday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'];
  const months = ['January','February','March','April','May','June','July','August','September','October','November','December'];
  const ord = n => n+(n===1?'st':n===2?'nd':n===3?'rd':'th');
  return `${days[d.getDay()]} ${ord(d.getDate())} ${months[d.getMonth()]} ${d.getFullYear()}`;
}

function fmtDateShort(iso) {
  if (!iso) return '';
  const d = new Date(iso + 'T00:00:00');
  const months = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  return `${d.getDate()} ${months[d.getMonth()]}`;
}

function hymnStr(no, title) {
  if (!no && !title) return "Chorister's choice";
  if (no && title)   return `SDAH ${no} \u2013 ${title}`;
  if (no)            return `SDAH ${no}`;
  return title;
}

function getNextSabbath(iso) {
  if (!iso) return null;
  const idx = PREACHING.findIndex(r => r.date === iso);
  return idx >= 0 && idx < PREACHING.length - 1 ? PREACHING[idx+1] : null;
}
function getNextDeacons(iso) {
  if (!iso) return null;
  const idx = DEACONS.findIndex(r => r.date === iso);
  return idx >= 0 && idx < DEACONS.length - 1 ? DEACONS[idx+1] : null;
}

function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg || '✓ Copied!';
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2000);
}

function copyText(text, btn) {
  navigator.clipboard.writeText(text).then(() => {
    if (btn) { const orig = btn.textContent; btn.textContent = '✓ Copied!'; btn.classList.add('copied'); setTimeout(()=>{btn.textContent=orig; btn.classList.remove('copied');},2000); }
    showToast('✓ Copied to clipboard!');
  });
}

// ═══════════════════════════════════════════════════════════════════════════════
// GENERATE ALL
// ═══════════════════════════════════════════════════════════════════════════════
function generateAll() {
  const date      = v('f_date');
  const dateStr   = fmtDate(date) || '[Date TBC]';
  const p         = PREACHING.find(r => r.date===date) || {};
  const dc        = DEACONS.find(r => r.date===date) || {};
  const next      = getNextSabbath(date);
  const nextDc    = getNextDeacons(date);

  const elder     = p.elder    || '[Elder TBC]';
  const preacher  = p.preacher || v('f_preacher') || '[Preacher TBC]';
  const deacons   = dc.names   || '[Deacons TBC]';
  const occasion  = v('f_occasion') || p.occasion || '';
  const sermon    = v('f_sermon') || '[Sermon TBC]';
  const subtitle  = v('f_subtitle');

  const chorister = v('f_chorister') || 'Chorister';
  const pastoral  = v('f_pastoral')  || 'TBC';
  const children  = v('f_children')  || 'TBC';
  const ssLeader  = v('f_ss_leader') || 'TBC';
  const offerDesig= v('f_offering_desig') || 'General Fund';
  const scrRef    = v('f_scripture_ref') || 'To be confirmed by Preacher';

  const hOpenNo   = v('h_open_no');   const hOpenT  = v('h_open_title');
  const hMedNo    = v('h_med_no');    const hMedT   = v('h_med_title');
  const hSS1No    = v('h_ss1_no');    const hSS1T   = v('h_ss1_title');
  const hSS2No    = v('h_ss2_no');    const hSS2T   = v('h_ss2_title');
  const hSS3No    = v('h_ss3_no');    const hSS3T   = v('h_ss3_title');
  const hCloseNo  = v('h_close_no');  const hCloseT = v('h_close_title');
  const hAnnNo    = v('h_ann_no');    const hAnnT   = v('h_ann_title');

  const openSong   = hymnStr(hOpenNo, hOpenT);
  const medSong    = hymnStr(hMedNo,  hMedT);
  const ss1        = hymnStr(hSS1No,  hSS1T);
  const ss2        = hymnStr(hSS2No,  hSS2T);
  const ss3        = hymnStr(hSS3No,  hSS3T);
  const closeSong  = hymnStr(hCloseNo,hCloseT);
  const annSong    = hymnStr(hAnnNo,  hAnnT);

  const announcements = v('f_announcements') || 'None provided';
  const dateOcc = occasion ? `${dateStr} — ${occasion}` : dateStr;

  const nextStr  = next ? `${fmtDateShort(next.date)}${next.occasion?' ('+next.occasion+')':''}` : 'TBC';
  const nextElder = next ? next.elder : 'TBC';
  const nextPreacher = next ? next.preacher : 'TBC';
  const nextDeacons = nextDc ? nextDc.names : 'TBC';

  // ── Sign-off ──
  const sig = `Please confirm. God bless you.\n— Elder ${elder}`;

  // ── Build all messages ──
  const messages = [
    {
      role: 'Platform Party',
      names: `${elder}, ${preacher}`,
      text: `Good morning Elder ${elder} and ${preacher},

You are part of the *Platform Party* for our Sabbath service on ${dateOcc}.

*Your roles:*
• Elder ${elder} — Announcement (11:00AM, 5 min) & Welcome (11:17AM, 1 min) & Tithes & Offerings (11:22AM, 5 min)
• ${preacher} — Invocation (11:10AM, 2 min), Sermon: "${sermon}"${subtitle ? '\n  ('+subtitle+')':''} (12:02PM, 30 min) & Benediction (12:36PM, 2 min)

*Processional:*
• Arrive by 10:50AM and gather at the BACK of the church
• At 11:08AM walk in together during SDAH 672 — Spirit of the Living God
• Walk reverently to the platform and be seated

*Recessional:*
• After the Benediction, rise and exit together during SDAH 220 — When He Comes

Please confirm receipt. God bless you.
— Elder ${elder}`
    },
    {
      role: 'Pastoral Prayer',
      names: pastoral,
      text: `Good morning ${pastoral},

You are leading the *Pastoral Prayer* at our Sabbath service on ${dateOcc}.

Time: 11:18AM
Duration: 4 minutes

Please include: praise, intercession for the church and community, and a blessing for the upcoming sermon: "${sermon}."

${sig}`
    },
    {
      role: 'Children\'s Message',
      names: children,
      text: `Good morning ${children},

You are giving the *Children's Message* at our Sabbath service on ${dateOcc}.

Time: 11:27AM
Duration: 15 minutes

Invite children ages 2–10 to come to the front. Please align your message with the sermon theme if possible: *"${sermon}."*

${sig}`
    },
    {
      role: 'Scripture Reading',
      names: preacher,
      text: `Good morning ${preacher},

You are doing the *Scripture Reading* at our Sabbath service on ${dateOcc}.

Time: 11:42AM
Duration: 2 minutes
Passage: ${scrRef}

Read clearly and at a steady pace. State the reference before and after reading. You will be called up by Elder ${elder}.

${sig}`
    },
    {
      role: 'Song Service Leader',
      names: chorister,
      text: `Good morning ${chorister},

You are leading the *Song Service* at our Sabbath service on ${dateOcc}.

Your responsibilities:
• 11:05AM — Song after Announcements: ${annSong}
• 11:12AM — Opening Song: ${openSong}
• 11:44AM — Song Service (15 min):
    1. ${ss1}
    2. ${ss2}
    3. ${ss3}
• 11:59AM — Meditational Song: ${medSong} (led by Sharon/soloist/choir)
• 12:32PM — Closing Song: ${closeSong}

Please confirm all hymn numbers with Sharon (pianist) before the service. Announce each hymn number clearly.

${sig}`
    },
    {
      role: 'Pianist',
      names: 'Sharon',
      text: `Good morning Sharon,

Here are the hymn numbers for Sabbath service on ${dateOcc}.

All hymns in order:
• 09:45AM — SS Song Service (SS Leader: ${ssLeader})
• 11:05AM — Song after Announcements: ${annSong}
• 11:08AM — Introit: SDAH 672 — Spirit of the Living God (fixed)
• 11:12AM — Opening Song: ${openSong}
• 11:22AM — Background music during Offerings
• 11:44AM — Song Service:
    ${ss1}
    ${ss2}
    ${ss3}
• 11:59AM — Meditational: ${medSong}
• 12:32PM — Closing Song: ${closeSong}
• 12:38PM — Recessional: SDAH 220 — When He Comes (fixed)

Please arrive by 9:15AM to confirm all numbers with Elder ${elder}.

${sig}`
    },
    {
      role: 'Deacons on Duty',
      names: deacons,
      text: `Good morning,

You are the *Deacons on Duty* for Sabbath ${dateOcc}.

Team: ${deacons}

Your schedule:
• 09:20AM — Arrive, open church, set up
• 09:25AM — Take positions at doors, welcome congregation
• 09:30AM — Morning Tea begins
• 10:50AM — Collect SS Offering during Mission Story time
• 11:22AM — Collect Tithes & Offerings when signalled by Elder ${elder}
• 12:38PM — Assist congregation as they depart
• 12:50PM — Secure the building

${sig}`
    },
    {
      role: 'All-Participants Summary',
      names: 'Leadership WhatsApp Group',
      text: `*York SDA Church*
*Sabbath ${dateStr}*${occasion ? '\n'+occasion:''}

*SABBATH SCHOOL (9:45–11:00)*
09:45 — Song Service: ${ssLeader}
10:00 — Lesson Study: Elder ${elder}
10:45 — Mission Story: Personal Ministries Dept
10:50 — SS Offering

*DIVINE SERVICE (11:00–12:38)*
11:00 — Announcement & Welcome: Elder ${elder}
11:08 — Introit: SDAH 672 — Spirit of the Living God
          Platform Party: Elder ${elder}, ${preacher}
11:10 — Invocation: ${preacher}
11:12 — Opening Song: ${openSong}
11:17 — Welcome: Elder ${elder}
11:18 — Pastoral Prayer: ${pastoral}
11:22 — Tithes & Offerings: Elder ${elder} — ${offerDesig}
11:27 — Children's Message: ${children}
11:42 — Scripture Reading: ${scrRef} — ${preacher}
11:44 — Song Service: ${chorister}
          ${ss1}
          ${ss2}
          ${ss3}
11:59 — Meditational: ${medSong}
12:02 — Sermon: *${sermon}*${subtitle ? '\n          ('+subtitle+')':''} — ${preacher}
12:32 — Closing Song: ${closeSong}
12:36 — Benediction: ${preacher}
12:38 — Recessional: SDAH 220 — When He Comes

*Pianist:* Sharon
*Deacons:* ${deacons}

*Next Sabbath — ${nextStr}:*
Elder: ${nextElder} | Preacher: ${nextPreacher}
Deacons: ${nextDeacons}`
    },
  ];

  generatedData = messages;

  // ── Render WhatsApp screen ──
  const list = document.getElementById('wa-list');
  list.innerHTML = '';
  messages.forEach((m, i) => {
    const el = document.createElement('div');
    el.className = 'wa-item';
    el.innerHTML = `
      <div class="wa-header" onclick="toggleWA(${i})">
        <div>
          <div class="wa-role">${m.role}</div>
          <div class="wa-name">${m.names}</div>
        </div>
        <span class="wa-chevron">›</span>
      </div>
      <div class="wa-body">
        <div class="wa-text" id="wa-text-${i}">${m.text.replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/\*([^*]+)\*/g,'<strong>$1</strong>')}</div>
        <div class="wa-actions">
          <button class="btn-sm btn-copy-sm" onclick="copyWA(${i}, this)">Copy Message</button>
        </div>
      </div>`;
    list.appendChild(el);
  });

  document.getElementById('wa-empty').style.display = 'none';
  document.getElementById('wa-list').style.display = 'block';

  switchTab(1);
  showToast('✓ All messages generated!');
}

function toggleWA(idx) {
  const item = document.querySelectorAll('.wa-item')[idx];
  item.classList.toggle('open');
}

function copyWA(idx, btn) {
  copyText(generatedData[idx].text, btn);
}

// ═══════════════════════════════════════════════════════════════════════════════
// ROTA TABLES
// ═══════════════════════════════════════════════════════════════════════════════
function buildRotaTables(selectedDate) {
  const pb = document.getElementById('preaching-body');
  pb.innerHTML = '';
  PREACHING.forEach(r => {
    const tr = document.createElement('tr');
    if (r.date === selectedDate) tr.className = 'this-week';
    tr.innerHTML = `<td>${fmtDateShort(r.date)}</td><td>${r.elder}</td><td>${r.preacher}</td><td>${r.occasion||'—'}</td>`;
    pb.appendChild(tr);
  });

  const db = document.getElementById('deacons-body');
  db.innerHTML = '';
  DEACONS.forEach(r => {
    const tr = document.createElement('tr');
    if (r.date === selectedDate) tr.className = 'this-week';
    tr.innerHTML = `<td>${fmtDateShort(r.date)}</td><td>${r.names}</td>`;
    db.appendChild(tr);
  });
}

// ═══════════════════════════════════════════════════════════════════════════════
// HYMN SEARCH
// ═══════════════════════════════════════════════════════════════════════════════
function hymnSearch() {
  const q = document.getElementById('hymn-search').value.trim().toLowerCase();
  const res = document.getElementById('hymn-results');
  if (!q) { res.innerHTML = ''; return; }

  const num = parseInt(q);
  const entries = Object.entries(HYMNS);
  let matches;

  if (!isNaN(num) && HYMNS[num]) {
    matches = [[num, HYMNS[num]]];
  } else {
    matches = entries.filter(([n,t]) => t.toLowerCase().includes(q)).slice(0,20);
  }

  if (!matches.length) {
    res.innerHTML = '<div class="card"><div class="card-body"><p class="muted">No hymns found.</p></div></div>';
    return;
  }

  res.innerHTML = matches.map(([n,t]) => `
    <div class="card" style="margin-bottom:6px;">
      <div class="card-body" style="display:flex;justify-content:space-between;align-items:center;padding:10px 14px;">
        <div>
          <span style="font-weight:700;color:var(--navy);font-size:14px;">SDAH ${n}</span><br>
          <span style="font-size:13px;">${t}</span>
        </div>
        <button class="btn-sm btn-copy-sm" onclick="copyText('SDAH ${n} \u2013 ${t.replace(/'/g,"\\'")}', this)">Copy</button>
      </div>
    </div>`).join('');
}

// ═══════════════════════════════════════════════════════════════════════════════
// INIT
// ═══════════════════════════════════════════════════════════════════════════════
buildRotaTables(null);
</script>
</body>
</html># YorkChurchPlanner
