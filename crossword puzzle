<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>슬기로운 근로생활 가로세로 퍼즐 (칠판 버전)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />

  <!-- Poor Story 폰트 -->
  <link href="https://fonts.googleapis.com/css2?family=Poor+Story&display=swap" rel="stylesheet">

  <style>
    :root {
      --chalk-bg: #1b4d3e;
      --chalk-ink: #ffffff;
      --chalk-ink-soft: #ffeaa7;
    }

    body {
      font-family: 'Poor Story', sans-serif;
      background-color: var(--chalk-bg);
      background-image: url("https://upload.wikimedia.org/wikipedia/commons/2/2a/Green_chalkboard_texture.jpg");
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
      color: var(--chalk-ink);
      text-shadow: 0 1px 1px rgba(0,0,0,0.45);
      font-size: 18px;
      padding: 20px;
      overflow-x: hidden;
    }

    h1, .subtitle {
      text-align: center;
      margin: 6px 0;
    }

    /* 📏 전체 레이아웃 */
    .layout {
      display: grid;
      grid-template-columns: minmax(520px, 1fr) 420px;
      gap: 20px;
      align-items: start;
      max-width: 1280px;
      margin: 14px auto 0;
    }

    .side {
      position: sticky;
      top: 200px;
      align-self: start;
    }

    /* 🧩 퍼즐 */
    #boardHolder { display: flex; justify-content: center; }
    #boardHolder table {
      border-collapse: collapse;
      margin: 8px auto 18px;
      box-shadow: 0 0 12px rgba(255,255,255,.1);
    }

    /* ✅ 퍼즐 칸 (GitHub CSS 충돌 방지) */
    #boardHolder table td {
      width: 44px;
      height: 44px;
      position: relative;
      text-align: center;
      vertical-align: middle;
      border: 2px solid rgba(255,255,255,.8);
      border-radius: 6px;
      background: rgba(255,255,255,.06);
    }

    /* ✅ 빈칸: 완전 투명 처리 */
    #boardHolder table td.blank {
      border: none !important;
      background: transparent !important;
      box-shadow: none !important;
      background-color: transparent !important;
    }

    .num {
      position: absolute;
      top: 2px;
      left: 4px;
      font-size: 12px;
      color: var(--chalk-ink-soft);
    }

    /* ✅ 입력 글씨: 항상 흰색 */
    #boardHolder input {
      width: 100%;
      height: 100%;
      border: none;
      outline: none;
      background: transparent;
      text-align: center;
      font-family: 'Poor Story', sans-serif;
      font-size: 22px;
      color: #ffffff;
      letter-spacing: .02em;
    }

    /* 버튼 */
    .topbar {
      display: flex;
      gap: 10px;
      margin: 8px 0 16px;
      justify-content: center;
    }
    button {
      padding: 8px 16px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      color: #fff;
      background: #27ae60;
      box-shadow: 0 2px 6px rgba(0,0,0,.3);
    }
    .ghost { background: #95a5a6; }

    .correct { background: rgba(160,255,160,.42)!important; }
    .incorrect { background: rgba(255,120,120,.42)!important; }

    /* 📘 풀이 패널 */
    .panel {
      background: rgba(0,0,0,0.28);
      border: 2px dashed rgba(255,255,255,0.4);
      border-radius: 14px;
      padding: 14px 16px;
      backdrop-filter: blur(1px);
    }

    .panel h2 {
      margin: 0 0 10px;
      color: var(--chalk-ink-soft);
      font-size: 22px;
    }

    .panel ol {
      margin: 0 0 10px 22px;
      padding: 0;
    }

    .panel li {
      margin: 6px 0;
      line-height: 1.55;
    }

    .emoji { margin-right: 6px; }

    @media (max-width: 980px) {
      .layout { grid-template-columns: 1fr; }
      .side { position: static; }
    }
  </style>
</head>
<body>
  <h1>✏️ 슬기로운 근로생활 가로세로 퍼즐</h1>
  <div class="subtitle">왼쪽에서 퍼즐을 풀고, 오른쪽에서 힌트를 확인하세요!</div>

  <div class="layout">
    <!-- 퍼즐 -->
    <section>
      <div class="topbar">
        <button id="checkBtn">정답 확인</button>
        <button id="clearBtn" class="ghost">초기화</button>
      </div>
      <div id="boardHolder"></div>
    </section>

    <!-- 풀이 -->
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
    const entries = [
      { num:1, dir:'across', row:4, col:2, text:'직장내괴롭힘', clue:'💼 직장 내 폭언·모욕·따돌림 등 괴롭힘' },
      { num:2, dir:'across', row:6, col:4, text:'희망회로', clue:'🌈 현실을 낙관적으로 해석하는 심리' },
      { num:3, dir:'across', row:9, col:7, text:'스트레스', clue:'😵‍💫 신체·정신에 누적되는 긴장' },
      { num:4, dir:'across', row:7, col:9, text:'근로기준법', clue:'📚 근로조건의 최소 기준 법' },
      { num:5, dir:'across', row:11, col:9, text:'콘택트렌즈', clue:'👀 시력 보정용 얇은 렌즈' },
      { num:1, dir:'down', row:2, col:4, text:'직장내성희롱', clue:'🚫 직장 내 성적 언행으로 인한 굴욕감' },
      { num:2, dir:'down', row:6, col:7, text:'로보틱스', clue:'🤖 로봇 관련 공학 분야' },
      { num:3, dir:'down', row:9, col:9, text:'레미콘', clue:'🏗️ 공장에서 배합해 운반되는 콘크리트' },
      { num:4, dir:'down', row:6, col:10, text:'프로세스', clue:'🔁 단계적으로 진행되는 절차' },
      { num:5, dir:'down', row:9, col:13, text:'옴부즈퍼슨', clue:'🧑‍⚖️ 고충·신고 담당자' },
    ];

    const cells = new Map(); let maxR=0, maxC=0;
    for(const e of entries){
      const chars=[...e.text]; let r=e.row, c=e.col;
      for(let i=0;i<chars.length;i++){
        const key=`${r},${c}`; const exist=cells.get(key);
        if(exist && exist.ch!==chars[i]) exist.conflict=true;
        else cells.set(key,{ ch:chars[i], number:(i===0 ? e.num : (exist?.number??null)) });
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
        } else td.className='blank';
        tr.appendChild(td);
      }
      table.appendChild(tr);
    }
    document.getElementById('boardHolder').appendChild(table);

    const acrossList=document.getElementById('acrossList');
    const downList=document.getElementById('downList');
    entries.filter(e=>e.dir==='across').forEach(e=>{
      const li=document.createElement('li'); li.innerHTML=`<span class="emoji">➡️</span>${e.clue}`; acrossList.appendChild(li);
    });
    entries.filter(e=>e.dir==='down').forEach(e=>{
      const li=document.createElement('li'); li.innerHTML=`<span class="emoji">⬇️</span>${e.clue}`; downList.appendChild(li);
    });

    /* 한 글자 제한 */
    document.addEventListener('input',e=>{
      if(e.target.tagName==='INPUT'){
        const val=Array.from(e.target.value);
        e.target.value=val.length>0?val[0]:'';
      }
    });

    /* 정답 확인 */
    document.getElementById('checkBtn').addEventListener('click',()=>{
      let allCorrect=true;
      document.querySelectorAll('#boardHolder input').forEach(inp=>{
        const ok=inp.value===inp.dataset.answer;
        inp.classList.toggle('correct',ok);
        inp.classList.toggle('incorrect',!ok&&inp.value);
        if(!ok) allCorrect=false;
      });
      if(allCorrect) alert('🎉 정답입니다!');
      else alert('❌ 다시 한 번 확인해보세요!');
    });

    document.getElementById('clearBtn').addEventListener('click',()=>{
      document.querySelectorAll('#boardHolder input').forEach(i=>{
        i.value=''; i.classList.remove('correct','incorrect');
      });
    });
  </script>
</body>
</html>
