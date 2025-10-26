<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>슬기로운 근로생활 가로세로 퍼즐 (빈칸 투명 보장)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link href="https://fonts.googleapis.com/css2?family=Poor+Story&display=swap" rel="stylesheet">
  <style>
    :root{ --chalk-bg:#1b4d3e; --ink:#fff; --ink-soft:#ffeaa7; }
    body{
      font-family:'Poor Story',sans-serif; background:#1b4d3e url("https://upload.wikimedia.org/wikipedia/commons/2/2a/Green_chalkboard_texture.jpg") center/cover no-repeat;
      color:var(--ink); text-shadow:0 1px 1px rgba(0,0,0,.45); font-size:18px; padding:20px; overflow-x:hidden;
    }
    h1,.subtitle{ text-align:center; margin:6px 0; }

    /* 레이아웃 */
    .layout{ display:grid; grid-template-columns:minmax(520px,1fr) 420px; gap:20px; align-items:start; max-width:1280px; margin:14px auto 0; }
    .side{ position:sticky; top:200px; align-self:start; }

    /* 제목 아래 버튼 */
    .topbar{ display:flex; justify-content:center; gap:16px; margin:12px 0 16px; }
    .topbar button{ padding:10px 18px; border:none; border-radius:10px; cursor:pointer; color:#fff; background:#27ae60; box-shadow:0 2px 6px rgba(0,0,0,.3); }
    .ghost{ background:#95a5a6; }

    /* 퍼즐 테이블: 기본을 ‘모두 투명’으로 리셋 */
    #boardHolder{ display:flex; justify-content:center; }
    #boardHolder table{ border-collapse:collapse; margin:8px auto 18px; box-shadow:0 0 12px rgba(255,255,255,.1); }
    #boardHolder table td{
      width:44px; height:44px; position:relative; text-align:center; vertical-align:middle; border-radius:6px;
      border:0 !important; background:transparent !important; background-color:transparent !important; box-shadow:none !important;
    }
    /* 입력칸(빈칸 아님)에만 분필 테두리/배경 */
    #boardHolder table td:not(.blank){
      border:2px solid rgba(255,255,255,.8) !important;
      background:rgba(255,255,255,.06) !important;
    }
    .num{ position:absolute; top:2px; left:4px; font-size:12px; color:var(--ink-soft); }

    /* 입력 글씨는 항상 흰색 */
    #boardHolder input{
      width:100%; height:100%; border:none; outline:none; background:transparent;
      text-align:center; font-family:'Poor Story',sans-serif; font-size:22px; color:#fff; letter-spacing:.02em;
    }
    .correct{ background:rgba(160,255,160,.42)!important; }
    .incorrect{ background:rgba(255,120,120,.42)!important; }

    /* 풀이 패널 */
    .panel{ background:rgba(0,0,0,.28); border:2px dashed rgba(255,255,255,.4); border-radius:14px; padding:14px 16px; backdrop-filter:blur(1px); }
    .panel h2{ margin:0 0 10px; color:var(--ink-soft); font-size:22px; }
    .panel ol{ margin:0 0 10px 22px; padding:0; }
    .panel li{ margin:6px 0; line-height:1.55; }
    .emoji{ margin-right:6px; }

    @media (max-width:980px){ .layout{ grid-template-columns:1fr; } .side{ position:static; } }
  </style>
</head>
<body>
  <h1>✏️ 슬기로운 근로생활 가로세로 퍼즐</h1>
  <div class="subtitle">왼쪽에서 퍼즐을 풀고, 오른쪽에서 힌트를 확인하세요!</div>

  <div class="topbar">
    <button id="checkBtn">정답 확인</button>
    <button id="clearBtn" class="ghost">초기화</button>
  </div>

  <div class="layout">
    <section><div id="boardHolder"></div></section>
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

  <script>
    window.addEventListener('DOMContentLoaded', () => {
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

      // 보드 데이터 구성
      const cells = new Map(); let maxR=0, maxC=0;
      for (const e of entries) {
        const chars = [...e.text]; let r = e.row, c = e.col;
        for (let i=0;i<chars.length;i++) {
          const key = `${r},${c}`; const exist = cells.get(key);
          if (exist && exist.ch !== chars[i]) exist.conflict = true;
          else cells.set(key, { ch: chars[i], number: (i===0 ? e.num : (exist?.number ?? null)) });
          if (e.dir==='across') c++; else r++;
          if (r>maxR) maxR=r; if (c>maxC) maxC=c;
        }
      }

      // 테이블 생성
      const table = document.createElement('table');
      for (let r=1;r<=maxR;r++){
        const tr = document.createElement('tr');
        for (let c=1;c<=maxC;c++){
          const td = document.createElement('td');
          const key = `${r},${c}`;
          if (cells.has(key)) {
            const info = cells.get(key);
            if (info.number != null) {
              const n = document.createElement('div'); n.className='num'; n.textContent=info.number; td.appendChild(n);
            }
            const inp = document.createElement('input'); inp.maxLength=1; inp.dataset.answer = info.ch;
            td.appendChild(inp);
          } else {
            td.classList.add('blank');
            // ✅ 인라인으로도 완전 투명 보장 (외부 CSS 무력화)
            td.style.border = '0';
            td.style.background = 'transparent';
            td.style.backgroundColor = 'transparent';
            td.style.boxShadow = 'none';
          }
          tr.appendChild(td);
        }
        table.appendChild(tr);
      }
      document.getElementById('boardHolder').appendChild(table);

      // 풀이 텍스트
      const acrossList = document.getElementById('acrossList');
      const downList   = document.getElementById('downList');
      entries.filter(e=>e.dir==='across').forEach(e=>{
        const li=document.createElement('li'); li.innerHTML=`<span class="emoji">➡️</span>${e.clue}`; acrossList.appendChild(li);
      });
      entries.filter(e=>e.dir==='down').forEach(e=>{
        const li=document.createElement('li'); li.innerHTML=`<span class="emoji">⬇️</span>${e.clue}`; downList.appendChild(li);
      });

      // 한 글자 제한 (IME 포함)
      document.addEventListener('compositionstart', e=>{ if(e.target.tagName==='INPUT') e.target.dataset.ime='on'; });
      document.addEventListener('compositionend', e=>{
        if(e.target.tagName==='INPUT'){ e.target.dataset.ime='off'; const ch=[...e.target.value||'']; e.target.value=ch[0]||''; }
      });
      document.addEventListener('input', e=>{
        if(e.target.tagName!=='INPUT') return;
        if(e.target.dataset.ime!=='on'){ const ch=[...e.target.value||'']; e.target.value=ch[0]||''; }
      });

      // 방향키 이동
      function ref(el){ const td=el.closest('td'); const tr=td.parentElement; return {r:tr.rowIndex+1,c:td.cellIndex+1}; }
      function findNext(r,c,dr,dc){
        while(true){
          r+=dr; c+=dc;
          if(r<1||c<1||r>table.rows.length||c>table.rows[0].cells.length) return null;
          const i=table.rows[r-1].cells[c-1].querySelector('input'); if(i) return i;
        }
      }
      document.addEventListener('keydown', e=>{
        const a=document.activeElement; if(!a||a.tagName!=='INPUT') return;
        let nxt=null; const {r,c}=ref(a);
        if(e.key==='ArrowRight') nxt=findNext(r,c,0,1);
        if(e.key==='ArrowLeft')  nxt=findNext(r,c,0,-1);
        if(e.key==='ArrowDown')  nxt=findNext(r,c,1,0);
        if(e.key==='ArrowUp')    nxt=findNext(r,c,-1,0);
        if(nxt){ e.preventDefault(); nxt.focus(); }
      });

      // 정답/초기화
      document.getElementById('checkBtn').addEventListener('click', ()=>{
        let allCorrect=true;
        document.querySelectorAll('#boardHolder input').forEach(inp=>{
          const ok = (inp.value === inp.dataset.answer);
          inp.classList.toggle('correct', ok);
          inp.classList.toggle('incorrect', !ok && inp.value);
          if(!ok) allCorrect=false;
        });
        alert(allCorrect ? '🎉 정답입니다!' : '❌ 다시 한 번 확인해보세요!');
      });
      document.getElementById('clearBtn').addEventListener('click', ()=>{
        document.querySelectorAll('#boardHolder input').forEach(i=>{ i.value=''; i.classList.remove('correct','incorrect'); });
      });
    });
  </script>
</body>
</html>
