<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>슬기로운 근로생활 가로세로 퍼즐 (Jua 폰트 · 칠판 완성본)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <!-- 분필 느낌의 선명한 한글 폰트 -->
  <link href="https://fonts.googleapis.com/css2?family=Gowun+Dodum&display=swap" rel="stylesheet">

  <style>
    /* ===== 🎨 칠판 테마 ===== */
    body{
      font-family:'Gowun+Dodum', sans-serif;
      background-color:#1b4d3e;
      background-image:url("https://upload.wikimedia.org/wikipedia/commons/2/2a/Green_chalkboard_texture.jpg");
      background-size:cover;
      background-position:center;
      background-repeat:no-repeat;
      color:#fff;
      text-shadow:0 1px 1px rgba(0,0,0,.4);
      font-size:20px;
      padding:20px;
      overflow-x:hidden;
    }
    h1,p{text-align:center;margin:8px 0;}

    /* ===== 분필가루 효과 ===== */
    @keyframes chalkDust{0%{opacity:1;transform:translateY(0) scale(1)}100%{opacity:0;transform:translateY(40px) scale(.6)}}
    .dust{position:absolute;width:4px;height:4px;background:rgba(255,255,255,.85);border-radius:50%;
          pointer-events:none;animation:chalkDust 1.5s linear forwards;}

    /* ===== 퍼즐 보드 ===== */
    #boardHolder{display:flex;justify-content:center;}
    table{border-collapse:collapse;margin:16px auto;box-shadow:0 0 12px rgba(255,255,255,.1);}
    td{
      width:44px;height:44px;position:relative;text-align:center;vertical-align:middle;
      border:2px solid rgba(255,255,255,.75);
      border-radius:4px;background:rgba(255,255,255,.05);
    }
    td.blank{border:none;background:transparent;}
    .num{position:absolute;top:2px;left:4px;font-size:12px;color:#ffeaa7;}

    input{
      width:100%;height:100%;border:none;outline:none;background:transparent;text-align:center;
      font-family:'Jua', sans-serif;font-size:24px;color:#fff;transition:color .2s;
    }
    input[data-color="1"]{color:#fefee0}
    input[data-color="2"]{color:#b0e0ff}
    input[data-color="3"]{color:#ffb6c1}
    input[data-color="4"]{color:#a7f0ba}

    .correct{background:rgba(160,255,160,.4)!important;}
    .incorrect{background:rgba(255,120,120,.4)!important;}

    .topbar{display:flex;justify-content:center;gap:10px;margin:12px 0 18px;}
    button{padding:8px 16px;border:none;border-radius:10px;cursor:pointer;color:#fff;
           box-shadow:0 2px 6px rgba(0,0,0,.3);font-family:'Jua',sans-serif;font-size:20px;}
    .primary{background:#27ae60;}
    .ghost{background:#95a5a6;}

    .cols{display:grid;grid-template-columns:1fr 1fr;gap:16px;max-width:980px;margin:0 auto;}
    @media (max-width:820px){.cols{grid-template-columns:1fr;}}
    .hint{background:rgba(0,0,0,.3);border:2px dashed rgba(255,255,255,.4);border-radius:12px;padding:14px;}
    .hint h3{margin:0 0 8px;color:#ffeaa7;}

    /* ===== 정답/오답 분필 배너 ===== */
    @keyframes chalkText{
      0%{opacity:0;transform:translate(-50%,-50%) scale(.92) rotate(-3deg);filter:blur(1px)}
      18%{opacity:1;transform:translate(-50%,-50%) scale(1) rotate(0);filter:blur(0)}
      80%{opacity:1}
      100%{opacity:0;transform:translate(-50%,-50%) scale(1.06) rotate(3deg);filter:blur(1px)}
    }
    .chalkBanner{
      position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);
      pointer-events:none;font-family:'Jua',sans-serif;white-space:nowrap;
      opacity:0;z-index:10000;
    }
    .chalkBanner.show{animation:chalkText 2.8s ease-in-out forwards;}
    .chalk--ok{font-size:80px;color:#fff;text-shadow:0 0 12px rgba(255,255,255,.9);}
    .chalk--retry{font-size:80px;color:#ffeaa7;text-shadow:0 0 10px rgba(255,230,120,.9);}
  </style>
</head>
<body>
  <h1>✏️ 슬기로운 근로생활 가로세로 퍼즐</h1>
  <p>칠판 위의 분필로 정답을 써보세요!</p>

  <div class="topbar">
    <button id="checkBtn" class="primary">정답 확인</button>
    <button id="clearBtn" class="ghost">초기화</button>
  </div>

  <div id="boardHolder"></div>

  <div class="cols">
    <div class="hint"><h3>가로 풀이</h3><ol id="acrossList"></ol></div>
    <div class="hint"><h3>세로 풀이</h3><ol id="downList"></ol></div>
  </div>

  <div id="chalkOk" class="chalkBanner chalk--ok">정답입니다! 🎉</div>
  <div id="chalkRetry" class="chalkBanner chalk--retry">다시 도전! ✏️</div>

  <script>
    const entries = [
      {num:1,dir:'across',row:4,col:2,text:'직장내괴롭힘',clue:'직장 내 폭언·모욕·따돌림 등 괴롭힘 행위'},
      {num:2,dir:'across',row:6,col:4,text:'희망회로',clue:'현실을 낙관적으로 해석해 위안 얻는 심리'},
      {num:3,dir:'across',row:9,col:7,text:'스트레스',clue:'외부 자극으로 생기는 신체·정신적 긴장'},
      {num:4,dir:'across',row:7,col:9,text:'근로기준법',clue:'근로조건의 최저 기준을 정한 법률'},
      {num:5,dir:'across',row:11,col:9,text:'콘택트렌즈',clue:'눈에 착용하는 얇은 시력교정 렌즈'},
      {num:1,dir:'down',row:2,col:4,text:'직장내성희롱',clue:'직장에서 성적 언행으로 굴욕감을 주는 행위'},
      {num:2,dir:'down',row:6,col:7,text:'로보틱스',clue:'로봇의 설계·제작·제어를 다루는 공학'},
      {num:3,dir:'down',row:9,col:9,text:'레미콘',clue:'공장에서 배합한 콘크리트를 운반하는 것'},
      {num:4,dir:'down',row:6,col:10,text:'프로세스',clue:'작업이 단계적으로 진행되는 과정'},
      {num:5,dir:'down',row:9,col:13,text:'옴부즈퍼슨',clue:'조직 내 고충을 처리하는 독립 담당자'}
    ];

    const cells=new Map(); let maxR=0,maxC=0;
    for(const e of entries){
      const chars=[...e.text]; let r=e.row,c=e.col;
      for(let i=0;i<chars.length;i++){
        const key=`${r},${c}`; const exist=cells.get(key);
        if(exist&&exist.ch!==chars[i]) exist.conflict=true;
        else cells.set(key,{ch:chars[i],number:(i===0?e.num:(exist?.number??null))});
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
          if(info.number!=null){const n=document.createElement('div');n.className='num';n.textContent=info.number;td.appendChild(n);}
          const inp=document.createElement('input');inp.maxLength=1;inp.setAttribute('data-answer',info.ch);
          inp.setAttribute('data-color',String(1+Math.floor(Math.random()*4)));
          td.appendChild(inp);
        } else td.className='blank';
        tr.appendChild(td);
      }
      table.appendChild(tr);
    }
    document.getElementById('boardHolder').appendChild(table);

    const acrossList=document.getElementById('acrossList');
    const downList=document.getElementById('downList');
    entries.filter(e=>e.dir==='across').forEach(e=>{const li=document.createElement('li');li.textContent=e.clue;acrossList.appendChild(li);});
    entries.filter(e=>e.dir==='down').forEach(e=>{const li=document.createElement('li');li.textContent=e.clue;downList.appendChild(li);});

    // 한 글자 제한
    document.addEventListener('compositionstart',e=>{if(e.target.tagName==='INPUT')e.target.dataset.ime='on';});
    document.addEventListener('compositionend',e=>{if(e.target.tagName==='INPUT'){e.target.dataset.ime='off';const chars=[...e.target.value||''];e.target.value=chars[0]||'';}});
    document.addEventListener('input',e=>{if(e.target.tagName!=='INPUT')return;if(e.target.dataset.ime!=='on'){const chars=[...e.target.value||''];e.target.value=chars[0]||'';}});

    // 방향키 이동
    function ref(el){const td=el.closest('td');const tr=td.parentElement;return{r:tr.rowIndex+1,c:td.cellIndex+1};}
    function findNext(r,c,dr,dc){const t=table;while(true){r+=dr;c+=dc;if(r<1||c<1||r>t.rows.length||c>t.rows[0].cells.length)return null;const i=t.rows[r-1].cells[c-1].querySelector('input');if(i)return i;}}
    document.addEventListener('keydown',e=>{const a=document.activeElement;if(!a||a.tagName!=='INPUT')return;let nxt=null;const {r,c}=ref(a);
      if(e.key==='ArrowRight')nxt=findNext(r,c,0,1);if(e.key==='ArrowLeft')nxt=findNext(r,c,0,-1);
      if(e.key==='ArrowDown')nxt=findNext(r,c,1,0);if(e.key==='ArrowUp')nxt=findNext(r,c,-1,0);
      if(nxt){e.preventDefault();nxt.focus();}});

    // 분필가루
    function spawnChalkDust(x,y){const dust=document.createElement('div');dust.className='dust';dust.style.left=x+'px';dust.style.top=y+'px';
      document.body.appendChild(dust);setTimeout(()=>dust.remove(),1500);}
    document.addEventListener('click',e=>{for(let i=0;i<5;i++){setTimeout(()=>{const dx=(Math.random()-.5)*40,dy=Math.random()*30;
      spawnChalkDust(e.pageX+dx,e.pageY+dy);},i*100);}});

    const chalkOk=document.getElementById('chalkOk');const chalkRetry=document.getElementById('chalkRetry');
    function showBanner(el){el.classList.remove('show');void el.offsetWidth;el.classList.add('show');setTimeout(()=>el.classList.remove('show'),2900);}
    document.getElementById('checkBtn').addEventListener('click',()=>{
      let allCorrect=true;
      document.querySelectorAll('#boardHolder input').forEach(inp=>{
        const ok=inp.value===inp.dataset.answer;
        inp.classList.toggle('correct',ok);
        inp.classList.toggle('incorrect',!ok&&inp.value);
        if(!ok)allCorrect=false;
      });
      if(allCorrect)showBanner(chalkOk);else showBanner(chalkRetry);
    });
    document.getElementById('clearBtn').addEventListener('click',()=>{
      document.querySelectorAll('#boardHolder input').forEach(i=>{i.value='';i.classList.remove('correct','incorrect');});
      chalkOk.classList.remove('show');chalkRetry.classList.remove('show');
    });
  </script>
</body>
</html>
