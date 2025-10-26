<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>슬기로운 근로생활 가로세로 퍼즐 (GitHub Pages 호환 · 흰색 글자)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />

  <!-- Poor Story 폰트 -->
  <link href="https://fonts.googleapis.com/css2?family=Poor+Story&display=swap" rel="stylesheet">

  <style>
    :root {
      --chalk-bg: #1b4d3e;
      --chalk-ink: #ffffff;
      --chalk-ink-soft: #ffeaa7;
    }

    /* ===== 칠판 테마 ===== */
    body{
      font-family:'Poor Story',sans-serif;
      background-color:var(--chalk-bg);
      background-image:url("https://upload.wikimedia.org/wikipedia/commons/2/2a/Green_chalkboard_texture.jpg");
      background-size:cover;background-position:center;background-repeat:no-repeat;
      color:var(--chalk-ink);
      text-shadow:0 1px 1px rgba(0,0,0,.45);
      font-size:18px; padding:20px; overflow-x:hidden;
    }
    h1,.subtitle{ text-align:center; margin:6px 0; }

    /* ===== 버튼(제목 아래 중앙) ===== */
    .topbar{ display:flex; justify-content:center; gap:20px; margin:16px 0 24px; }
    .topbar button{
      padding:10px 18px; border:none; border-radius:10px; cursor:pointer; color:#fff;
      background:#27ae60; box-shadow:0 2px 6px rgba(0,0,0,.3);
    }
    .topbar .ghost{ background:#95a5a6; }

    /* ===== 레이아웃: 퍼즐 왼쪽 / 풀이 오른쪽 ===== */
    .layout{
      display:grid; grid-template-columns:minmax(520px,1fr) 420px; gap:20px; align-items:start;
      max-width:1280px; margin:0 auto;
    }
    .side{ position:sticky; top:100px; align-self:start; }

    /* ===== 퍼즐 보드 ===== */
    #boardHolder{ display:flex; justify-content:center; }
    #boardHolder table{ border-collapse:collapse; margin:8px auto 18px; box-shadow:0 0 12px rgba(255,255,255,.1); }

    /* GitHub Pages 충돌 방지: 보드에만 테두리 적용 */
    #boardHolder table td{
      width:44px; height:44px; position:relative; text-align:center; vertical-align:middle;
      border:2px solid rgba(255,255,255,.8);
      border-radius:6px; background:rgba(255,255,255,.06);
    }
    /* 빈칸은 어떤 경우에도 표시하지 않음 */
    #boardHolder table td.blank{
      border:none !important; background:transparent !important; box-shadow:none !important;
    }
    .num{ position:absolute; top:2px; left:4px; font-size:12px; color:var(--chalk-ink-soft); }

    /* 입력 글씨 = 항상 흰색 */
    #boardHolder input{
      width:100%; height:100%; border:none; outline:none; background:transparent;
      text-align:center; font-family:'Poor Story',sans-serif; font-size:22px; color:#fff; letter-spacing:.02em;
    }

    .correct{ background:rgba(160,255,160,.42)!important; }
    .incorrect{ background:rgba(255,120,120,.42)!important; }

    /* ===== 설명 패널 ===== */
    .panel{
      background:rgba(0,0,0,.28); border:2px dashed rgba(255,255,255,.4);
      border-radius:14px; padding:14px 16px; backdrop-filter:blur(1px);
    }
    .panel h2{ margin:0 0 10px; color:var(--chalk-ink-soft); font-size:22px; }
    .panel ol{ margin:0 0 10px 22px; padding:0; }
    .panel li{ margin:6px 0; line-height:1.55; }
    .emoji{ margin-right:6px; }

    /* ===== 정답/오답 분필 배너 ===== */
    @keyframes chalkText{
      0%{opacity:0;transform:translate(-50%,-50%) scale(.92) rotate(-3deg);filter:blur(1px)}
      18%{opacity:1;transform:translate(-50%,-50%) scale(1) rotate(0);filter:blur(0)}
      80%{opacity:1}
      100%{opacity:0;transform:translate(-50%,-50%) scale(1.06) rotate(3deg);filter:blur(1px)}
    }
    .chalkBanner{
      position:fixed; left:50%; top:50%; transform:translate(-50%,-50%); pointer-events:none;
      white-space:nowrap; opacity:0; z-index:10000; font-size:72px;
      text-shadow:0 0 12px rgba(255,255,255,.9);
    }
    .chalkBanner.show{ animation:chalkText 2.6s ease-in-out forwards; }
    .ok{ color:#fff; }
    .retry{ color:#ffeaa7; text-shadow:0 0 10px rgba(255,230,120,.9); }

    /* ===== 반응형 ===== */
    @media (max-width:980px){
      .layout{ grid-template-columns:1fr; }
      .side{ position:static; }
    }
  </style>
</head>
<body>
  <h1>✏️ 슬기로운 근로생활 가로세로 퍼즐</h1>
  <div class="subtitle">왼쪽에서 퍼즐을 풀고, 오른쪽에서 힌트를 확인하세요!</div>

  <!-- 제목 아래 중앙 버튼 -->
  <div class="topbar">
    <button id="checkBtn">정답 확인</button>
    <button id="clearBtn" class="ghost">초기화</button>
  </div>

  <div class="layout">
    <!-- 퍼즐 -->
    <section>
      <div id="boardHolder"></div>
    </section>

    <!-- 풀이(오른쪽, sticky) -->
    <aside class="side">
      <div class="panel">
        <h2>🧭 가로 풀이 (Across)</h2>
        <ol id="acrossList"></ol>
      </div>
      <div style="height:12px"></div>
      <div class="panel">
        <h2>🧷 세로 풀이 (Down)</h2>
        <ol id="downList"></ol>
      </div>
    </aside>
  </div>

  <!-- 분필 배너 -->
  <div id="chalkOk" class="chalkBanner ok">정답입니다! 🎉</div>
  <div id="chalkRetry" class="chalkBanner retry">다시 도전! ✏️</div>

  <!-- JS는 DOMContentLoaded 이후 실행 (GitHub Pages 안전) -->
  <script>
    window.addEventListener('DOMContentLoaded', () => {
      /* 1) 퍼즐 데이터 */
      const entries = [
        // 가로
        { num:1, dir:'across', row:4,  col:2,  text:'직장내괴롭힘', clue:'💼 직장 내 폭언·모욕·따돌림 등 괴롭힘' },
        { num:2, dir:'across', row:6,  col:4,  text:'희망회로',     clue:'🌈 현실을 낙관적으로 해석하는 심리' },
        { num:3, dir:'across', row:9,  col:7,  text:'스트레스',     clue:'😵‍💫 신체·정신에 누적되는 긴장' },
        { num:4, dir:'across', row:7,  col:9,  text:'근로기준법',   clue:'📚 근로조건의 최소 기준 법' },
        { num:5, dir:'across', row:11, col:9,  text:'콘택트렌즈',   clue:'👀 시력 보정용 얇은 렌즈' },
        // 세로
        { num:1, dir:'down',   row:2,  col:4,  text:'직장내성희롱', clue:'🚫 직장 내 성적 언행으로 인한 굴욕감' },
        { num:2, dir:'down',   row:6,  col:7,  text:'로보틱스',     clue:'🤖 로봇 관련 공학 분야' },
        { num:3, dir:'down',   row:9,  col:9,  text:'레미콘',       clue:'🏗️ 공장에서 배합해 운반되는 콘크리트' },
        { num:4, dir:'down',   row:6,  col:10, text:'프로세스',     clue:'🔁 단계적으로 진행되는 절차' },
        { num:5, dir:'down',   row:9,  col:13, text:'옴부즈퍼슨',   clue:'🧑‍⚖️ 고충·신고 담당자' },
      ];

      /* 2) 보드 생성 */
      const cells = new Map(); let maxR=0, maxC=0;
      for(const e of entries){
        const chars=[...e.text]; let r=e.row, c=e.col;
        for(let i=0;i<chars.length;i++){
          const key=`${r},${c}`; const exist=cells.get(key);
          if(exist && exist.ch!==chars[i]) exist.conflict=true;
          else cells.set(key,{ ch:chars[i], number:(i===0 ? e.num : (exist?.number ?? null)) });
          if(e.dir==='across') c++; else r++;
          if(r>maxR) maxR=r; if(c>maxC) maxC=c;
        }
      }

      const table=document.createElement('table');
      for(let r=1;r<=maxR;r++){
        const tr=document.createElement('tr');
        for(let c=1;c<=maxC;c++){
          const td=document.createElement('td');
          const key=`${r},${c}`;
          if(cells.has(key)){
            const info=cells.get(key);
            if(info.number!=null){
              const n=document.createElement('div'); n.className='num'; n.textContent=info.number; td.appendChild(n);
            }
            const inp=document.createElement('input'); inp.maxLength=1; inp.dataset.answer=info.ch;
            td.appendChild(inp);
          }else{
            td.className='blank';
          }
          tr.appendChild(td);
        }
        table.appendChild(tr);
      }
      document.getElementById('boardHolder').appendChild(table);

      /* 3) 설명(가로 → 세로) */
      const acrossList=document.getElementById('acrossList');
      const downList=document.getElementById('downList');
      entries.filter(e=>e.dir==='across').forEach(e=>{
        const li=document.createElement('li'); li.innerHTML=`<span class="emoji">➡️</span>${e.clue}`; acrossList.appendChild(li);
      });
      entries.filter(e=>e.dir==='down').forEach(e=>{
        const li=document.createElement('li'); li.innerHTML=`<span class="emoji">⬇️</span>${e.clue}`; downList.appendChild(li);
      });

      /* 4) 한 글자 제한 (IME 포함) */
      document.addEventListener('compositionstart',e=>{ if(e.target.tagName==='INPUT') e.target.dataset.ime='on'; });
      document.addEventListener('compositionend',e=>{
        if(e.target.tagName==='INPUT'){ e.target.dataset.ime='off'; const ch=[...e.target.value||'']; e.target.value=ch[0]||''; }
      });
      document.addEventListener('input',e=>{
        if(e.target.tagName!=='INPUT') return;
        if(e.target.dataset.ime!=='on'){ const ch=[...e.target.value||'']; e.target.value=ch[0]||''; }
      });

      /* 5) 방향키 이동 */
      function ref(el){ const td=el.closest('td'); const tr=td.parentElement; return {r:tr.rowIndex+1,c:td.cellIndex+1}; }
      function findNext(r,c,dr,dc){
        while(true){
          r+=dr; c+=dc;
          if(r<1||c<1||r>table.rows.length||c>table.rows[0].cells.length) return null;
          const i=table.rows[r-1].cells[c-1].querySelector('input'); if(i) return i;
        }
      }
      document.addEventListener('keydown',e=>{
        const a=document.activeElement; if(!a||a.tagName!=='INPUT') return;
        let nxt=null; const {r,c}=ref(a);
        if(e.key==='ArrowRight') nxt=findNext(r,c,0,1);
        if(e.key==='ArrowLeft')  nxt=findNext(r,c,0,-1);
        if(e.key==='ArrowDown')  nxt=findNext(r,c,1,0);
        if(e.key==='ArrowUp')    nxt=findNext(r,c,-1,0);
        if(nxt){ e.preventDefault(); nxt.focus(); }
      });

      /* 6) 정답/오답 배너 */
      const chalkOk=document.getElementById('chalkOk');
      const chalkRetry=document.getElementById('chalkRetry');
      function showBanner(el){ el.classList.remove('show'); void el.offsetWidth; el.classList.add('show'); setTimeout(()=>el.classList.remove('show'),2600); }

      document.getElementById('checkBtn').addEventListener('click',()=>{
        let allCorrect=true;
        document.querySelectorAll('#boardHolder input').forEach(inp=>{
          const ok = inp.value === inp.dataset.answer;
          inp.classList.toggle('correct', ok);
          inp.classList.toggle('incorrect', !ok && inp.value);
          if(!ok) allCorrect=false;
        });
        if(allCorrect) showBanner(chalkOk); else showBanner(chalkRetry);
      });

      document.getElementById('clearBtn').addEventListener('click',()=>{
        document.querySelectorAll('#boardHolder input').forEach(i=>{ i.value=''; i.classList.remove('correct','incorrect'); });
        chalkOk.classList.remove('show'); chalkRetry.classList.remove('show');
      });
    });
  </script>
</body>
</html>
