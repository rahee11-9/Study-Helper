# Study-Helper
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>프로 커스텀 스터디 플래너</title>
    <style>
        :root {
            --bg-color: #fff5f5;
            --panel-bg: #ffffff;
            --primary: #ffb7b2;
            --primary-hover: #ff9aa2;
            --text-main: #4a4a4a;
            --text-muted: #9e9e9e;
            --accent: #fff0f0;
        }

        body { 
            font-family: 'Malgun Gothic', sans-serif; 
            background-color: var(--bg-color); 
            margin: 0; padding: 15px; 
            color: var(--text-main);
            transition: background 0.2s ease, filter 0.3s ease;
            -webkit-tap-highlight-color: transparent;
        }

        body.eco-mode {
            background-color: #121212 !important;
            filter: brightness(0.6);
        }
        body.eco-mode .panel, body.eco-mode .app-theme-box, body.eco-mode .modal-content {
            background: #1e1e1e !important;
            color: #eee !important;
            border-color: #333 !important;
        }
        body.eco-mode input, body.eco-mode select, body.eco-mode textarea {
            background: #2d2d2d !important;
            color: #fff !important;
            border-color: #444 !important;
        }

        .wrapper { 
            max-width: 450px; 
            margin: 0 auto; 
            display: flex; 
            flex-direction: column; 
            gap: 15px; 
        }

        .panel { 
            background: var(--panel-bg); 
            padding: 20px; 
            border-radius: 20px; 
            box-shadow: 0 8px 24px rgba(0, 0, 0, 0.04); 
            box-sizing: border-box;
        }

        .app-theme-box {
            display: flex;
            flex-direction: column;
            gap: 6px;
            background: var(--panel-bg);
            padding: 12px 15px;
            border-radius: 16px;
            border: 1px solid #eee;
        }
        .theme-bar-label { font-size: 11px; font-weight: bold; color: var(--text-main); display: flex; justify-content: space-between; }

        .header-section { text-align: center; }
        .app-title { font-size: 18px; font-weight: bold; margin-bottom: 5px; letter-spacing: 1px; }
        .today-txt { font-size: 13px; color: var(--text-muted); margin-bottom: 12px; }
        
        /* 🔥 연속 표시 텍스트 스타일 수정 (제목 가림 방지) */
        .streak-text-inline {
            display: inline-block;
            font-size: 12px;
            color: #ff6b6b;
            background: #ffe3e3;
            padding: 2px 8px;
            border-radius: 8px;
            font-weight: bold;
            margin-left: 6px;
            vertical-align: middle;
        }

        .quote-box {
            background: #fdfaf6;
            border-left: 4px solid var(--primary-hover);
            padding: 10px;
            margin-bottom: 15px;
            border-radius: 0 12px 12px 0;
            font-size: 12.5px;
            line-height: 1.5;
            color: #555;
            font-style: italic;
            text-align: left;
        }

        .total-timer-box {
            background: linear-gradient(135deg, var(--primary), #ffe3e0);
            color: var(--text-main); padding: 18px; border-radius: 16px;
            border: 1px solid rgba(0,0,0,0.05);
        }
        .total-label { font-size: 11px; font-weight: bold; opacity: 0.8; margin-bottom: 4px; }
        .total-time { font-size: 34px; font-family: monospace; font-weight: bold; }

        .tab-menu { display: flex; background: #eee; padding: 4px; border-radius: 12px; gap: 4px; }
        .tab-btn { flex: 1; padding: 12px 3px; border: none; background: transparent; font-weight: bold; color: var(--text-muted); cursor: pointer; border-radius: 9px; font-size: 13px; white-space: nowrap; }
        .tab-btn.active { background: var(--panel-bg); color: var(--text-main); box-shadow: 0 2px 6px rgba(0,0,0,0.05); }

        .page { display: none; }
        .page.active { display: block; }

        /* 과목 타이머 */
        .subject-card { border: 1px solid rgba(0,0,0,0.05); background: #fffcfc; border-radius: 16px; padding: 15px; margin-bottom: 12px; }
        .subject-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
        .sub-title { font-size: 17px; font-weight: bold; }
        .sub-time { font-size: 19px; font-family: monospace; font-weight: bold; color: var(--text-main); }
        
        .timer-control-row { display: flex; gap: 5px; margin: 8px 0; }
        .sm-btn { flex: 1; padding: 6px; font-size: 11px; border: 1px solid #ddd; border-radius: 6px; background: white; cursor: pointer; font-weight: bold; color: var(--text-main); text-align: center; }
        
        .play-btn { background: var(--primary); color: var(--text-main); border: none; padding: 8px 16px; border-radius: 20px; font-weight: bold; cursor: pointer; font-size: 13px; }
        .play-btn.studying { background: #ff6b6b; color: white; }

        .sub-todo-box { background: white; border-radius: 10px; padding: 10px; margin-top: 5px; border: 1px dashed var(--primary); }
        .sub-todo-list { list-style: none; padding: 0; margin: 0; }
        .sub-todo-item { display: flex; justify-content: space-between; align-items: center; font-size: 13px; padding: 8px 0; border-bottom: 1px solid #f9f9f9; }
        .sub-todo-item.done span { text-decoration: line-through; color: var(--text-muted); }
        
        .inline-input-row { display: flex; gap: 5px; margin-top: 8px; }
        .inline-input-row input { flex: 1; padding: 8px; border: 1px solid #ddd; border-radius: 6px; font-size: 13px; }
        .inline-add-btn { background: var(--text-main); color: white; border: none; padding: 0 12px; border-radius: 6px; font-size: 13px; cursor: pointer; }

        .add-subject-form { display: flex; gap: 8px; margin-top: 15px; }
        .add-subject-form input { flex: 1; padding: 12px; border: 1px solid #ddd; border-radius: 8px; font-size: 14px; }

        /* 📅 캘린더 */
        .calendar-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
        .cal-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 6px; text-align: center; }
        .cal-day-label { font-size: 12px; font-weight: bold; color: var(--text-muted); padding: 5px 0; }
        .cal-cell { background: #fafafa; border-radius: 8px; min-height: 68px; padding: 4px; font-size: 12px; display: flex; flex-direction: column; justify-content: space-between; cursor: pointer; box-sizing: border-box; }
        .cal-cell.today { background: var(--accent); font-weight: bold; border: 1.5px solid var(--primary); }
        .cal-todo-container { display: flex; flex-direction: column; gap: 2px; width: 100%; margin-top: 3px; }
        .cal-todo-bar { font-size: 9px; color: #222; border-radius: 4px; padding: 2px 3px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; text-align: left; font-weight: bold; }

        .drag-color-bar { -webkit-appearance: none; width: 100%; height: 12px; border-radius: 6px; background: linear-gradient(to right, hsl(0,100%,85%), hsl(60,100%,85%), hsl(120,100%,85%), hsl(180,100%,85%), hsl(240,100%,85%), hsl(300,100%,85%), hsl(360,100%,85%)); outline: none; cursor: pointer; margin: 4px 0; }
        .drag-color-bar::-webkit-slider-thumb { -webkit-appearance: none; appearance: none; width: 20px; height: 20px; border-radius: 50%; background: #ffffff; border: 3px solid #4a4a4a; cursor: pointer; }

        /* 모달 팝업 */
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); justify-content: center; align-items: center; z-index: 1000; }
        .modal-content { background: white; padding: 20px; border-radius: 16px; width: 85%; max-width: 380px; box-sizing: border-box; }
        .modal-header { font-size: 16px; font-weight: bold; margin-bottom: 12px; }
        .modal-input-group { display: flex; gap: 5px; margin-bottom: 12px; }
        .modal-input-group input { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 8px; text-align: center; font-size: 14px; }

        /* 📝 영어 사전 탭 */
        .search-box { background: #fdf6f6; border: 1px solid #f2e4e4; border-radius: 12px; padding: 12px; margin-bottom: 15px; }
        .search-row { display: flex; gap: 6px; margin-bottom: 8px; }
        .search-row input { flex: 1; padding: 10px; border: 1px solid #ddd; border-radius: 8px; font-size: 14px; }
        .manual-input-box { display: flex; flex-direction: column; gap: 6px; margin-top: 10px; background: #fff; padding: 10px; border-radius: 8px; border: 1px solid #eee; }
        .manual-input-box input { padding: 8px; border: 1px solid #ddd; border-radius: 6px; font-size: 13px; }

        .word-card-list { display: flex; flex-direction: column; gap: 10px; max-height: 200px; overflow-y: auto; margin-top: 10px; }
        .word-card { display: flex; justify-content: space-between; align-items: center; background: #fffcfc; border: 1px solid #f0e4e4; padding: 12px 15px; border-radius: 12px; }
        .word-card.memorized { opacity: 0.4; background: #f5f5f5; }
        .word-card.memorized .word-eng { text-decoration: line-through; }
        .word-eng { font-size: 16px; font-weight: bold; color: var(--text-main); }
        .word-kor { font-size: 14px; color: #ff6b6b; font-weight: 500; margin-left: 10px; }

        /* 유틸리티 구조 */
        .util-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
        .util-card { background: #fafafa; border-radius: 14px; padding: 12px; border: 1px solid #eee; }
        .util-title { font-size: 13px; font-weight: bold; margin-bottom: 8px; color: var(--text-main); }
        .water-btn-grid { display: flex; gap: 4px; justify-content: space-between; }
        .water-dot { width: 22px; height: 22px; border-radius: 50%; border: 1.5px solid #a8dadc; cursor: pointer; display: inline-block; }
        .water-dot.filled { background: #457b9d; border-color: #457b9d; }

        .review-history-box { max-height: 120px; overflow-y: auto; margin-top: 8px; padding: 0; list-style: none; }
        .review-history-item { font-size: 12px; padding: 6px; border-bottom: 1px solid #eee; display: flex; justify-content: space-between; background: #fff; margin-bottom: 4px; border-radius: 6px; }
        .base-btn { background: var(--primary); color: var(--text-main); border: none; padding: 12px 20px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 14px; }
    </style>
</head>
<body>

<div class="wrapper">
    <div class="app-theme-box">
        <div class="theme-bar-label">
            <span>🎨 플래너 전체 테마 색상 커스텀</span>
            <span id="appThemePreview" style="font-weight:bold;">메인 톤</span>
        </div>
        <input type="range" min="0" max="360" value="0" class="drag-color-bar" id="appThemeSlider" oninput="changeAppTheme(this.value)">
    </div>

    <div class="panel header-section">
        <div class="app-title">🌸 SMART STUDY PLANNER <span class="streak-text-inline" id="streakBadgeBox">🔥 1일째</span></div>
        <div class="today-txt" id="todayDateStr">로드 중...</div>
        
        <div class="quote-box" id="quoteDisplayArea">"로딩 중..."</div>
        
        <div class="total-timer-box">
            <div class="total-label">TODAY TOTAL STUDY TIME</div>
            <div class="total-time" id="globalTotalTimer">00:00:00</div>
        </div>
    </div>

    <div class="tab-menu">
        <button class="tab-btn active" id="tab0" onclick="switchTab(0)">⏱️ 타이머/할일</button>
        <button class="tab-btn" id="tab1" onclick="switchTab(1)">📅 캘린더</button>
        <button class="tab-btn" id="tab2" onclick="switchTab(2)">📝 영어 사전</button>
    </div>

    <div id="page0" class="page active panel">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px;">
            <h3 style="margin:0;">📚 과목별 학습 코스</h3>
            <button class="sm-btn" id="ecoBtn" style="flex:none; padding:5px 10px; background:#444; color:#fff; border:none;" onclick="toggleEcoMode()">🔋 절전 모드 ON</button>
        </div>
        
        <div id="subjectContainer"></div>

        <div class="add-subject-form">
            <input type="text" id="newSubjectName" placeholder="과목 추가 (예: 영어독해)">
            <button class="base-btn" onclick="addNewSubject()">추가</button>
        </div>

        <hr style="border:0; border-top:1px dashed #eee; margin:20px 0;">

        <div class="util-grid">
            <div class="util-card">
                <div class="util-title">🎵 멀티 감성 백색소음 (6종)</div>
                <select id="noiseSelect" style="width:100%; padding:6px; border-radius:6px; font-size:12px;" onchange="playNoise(this.value)">
                    <option value="none">🔇 소리 끔</option>
                    <option value="rain">🌧️ 집중 빗소리</option>
                    <option value="cafe">☕ 잔잔한 카페 소음</option>
                    <option value="lib">📖 대학 도서관 야외</option>
                    <option value="fire">🔥 모닥불 장작 타는 소리</option>
                    <option value="cricket">🦗 여름밤 풀벌레 소리</option>
                    <option value="wave">🌊 시원한 바다 파도</option>
                </select>
            </div>
            <div class="util-card">
                <div class="util-title">💧 수분 충전 (목표 5잔)</div>
                <div class="water-btn-grid" id="waterContainer"></div>
            </div>
        </div>

        <div class="util-card" style="margin-top:12px;">
            <div class="util-title">📊 오늘 하루 나의 평가 및 누적 기록소</div>
            <div style="display:flex; gap:6px; margin-bottom:8px;">
                <select id="dayScore" style="padding:6px; border-radius:6px; font-size:12px;">
                    <option value="⭐⭐⭐⭐⭐">⭐⭐⭐⭐⭐</option>
                    <option value="⭐⭐⭐⭐">⭐⭐⭐⭐</option>
                    <option value="⭐⭐⭐">⭐⭐⭐</option>
                    <option value="⭐⭐">⭐⭐</option>
                </select>
                <input type="text" id="dayReviewText" style="flex:1; padding:6px; border:1px solid #ddd; border-radius:6px; font-size:12px;" placeholder="오늘의 성찰 일기 쓰기...">
                <button class="inline-add-btn" style="background:var(--primary-hover);" onclick="saveCumulativeReview()">저장</button>
            </div>
            
            <div style="font-size:11px; font-weight:bold; color:var(--text-muted); margin-top:5px; border-top:1px solid #eee; padding-top:5px;">📋 누적된 한줄평 History</div>
            <ul class="review-history-box" id="reviewHistoryArea"></ul>
        </div>
    </div>

    <div id="page1" class="page panel">
        <div class="calendar-header">
            <button onclick="moveMonth(-1)" style="border:none; background:none; cursor:pointer; font-weight:bold; padding:10px;">◀</button>
            <h3 id="calendarMonthTitle" style="margin:0;">2026년 05월</h3>
            <button onclick="moveMonth(1)" style="border:none; background:none; cursor:pointer; font-weight:bold; padding:10px;">▶</button>
        </div>
        <div class="cal-grid" id="calendarGrid"></div>
    </div>

    <div id="page2" class="page panel">
        <h3 style="margin-top:0;">📝 네이버 사전 연동 암기장</h3>
        
        <div class="search-box">
            <div class="search-row">
                <input type="text" id="dicSearchInput" placeholder="검색할 영어 단어 입력...">
                <button class="base-btn" style="padding:10px 14px;" onclick="openNaverDic()">네이버 사전 오픈</button>
            </div>
            <p style="font-size:11px; color:var(--text-muted); margin:4px 0 0 0;">💡 단어 검색 시 정확한 네이버 영어사전 창이 열립니다. 뜻을 확인한 뒤 아래에 적어 저장하세요!</p>
            
            <div class="manual-input-box">
                <input type="text" id="addEngField" placeholder="확인한 영어 단어 다시 쓰기">
                <input type="text" id="addKorField" placeholder="확인한 한글 뜻 받아 적기">
                <button class="base-btn" style="padding:8px; font-size:12px;" onclick="saveManualWord()">암기 보관소에 저장</button>
            </div>
        </div>

        <h4 style="margin: 10px 0 5px 0;">🗂️ 나의 단어 보관소</h4>
        <div class="word-card-list" id="wordCardList"></div>
    </div>
</div>

<div class="modal" id="timeEditModal">
    <div class="modal-content">
        <div class="modal-header" id="timeModalTitle">⏰ 공부 시간 직접 지정 수정</div>
        <p style="font-size:12px; color:var(--text-muted); margin-bottom:10px;">원하는 시간을 시/분/초 단위로 입력해 주세요.</p>
        <div class="modal-input-group">
            <input type="number" id="editHour" min="0" max="23" placeholder="시">
            <input type="number" id="editMin" min="0" max="59" placeholder="분">
            <input type="number" id="editSec" min="0" max="59" placeholder="초">
        </div>
        <div class="modal-btns">
            <button class="modal-btn" style="background:#eee;" onclick="closeTimeModal()">취소</button>
            <button class="modal-btn" style="background:var(--primary); color:var(--text-main);" onclick="saveCustomTime()">변경 적용</button>
        </div>
    </div>
</div>

<div class="modal" id="calModal">
    <div class="modal-content">
        <div class="modal-header" id="modalDateTitle">날짜 일정</div>
        <ul class="modal-todo-list" id="modalTodoList"></ul>
        <div class="color-picker-container">
            <div class="color-picker-label">
                <span>🎨 일정 색상 설정</span>
                <span id="colorPreviewText" style="font-weight:bold; color:hsl(0, 100%, 85%);">● 선택됨</span>
            </div>
            <input type="range" min="0" max="360" value="0" class="drag-color-bar" id="hueSlider" oninput="updateDragColor(this.value)">
        </div>
        <input type="text" id="modalTodoInput" style="width:100%; padding:10px; border:1px solid #ddd; border-radius:8px; box-sizing:border-box; margin-bottom:12px;" placeholder="태스크 내용 기입...">
        <div class="modal-btns">
            <button class="modal-btn" style="background:#eee;" onclick="closeModal()">닫기</button>
            <button class="modal-btn" style="background:var(--primary); color:var(--text-main);" onclick="saveCalendarTodo()">일정 추가</button>
        </div>
    </div>
</div>

<script>
    let subjects = JSON.parse(localStorage.getItem('study_v11_subjects')) || [
        { name: "국어", time: 0, todos: [] },
        { name: "수학", time: 0, todos: [] }
    ];
    let calendarEvents = JSON.parse(localStorage.getItem('study_v11_calendar')) || {};
    let words = JSON.parse(localStorage.getItem('study_v11_words')) || [];
    let waterCount = localStorage.getItem('study_v11_water') || 0;
    let reviewsHistory = JSON.parse(localStorage.getItem('study_v11_reviews_list')) || [];
    
    let savedHue = localStorage.getItem('study_v11_hue') || 0;
    let activeSubIdx = null;
    let mainLoopInterval = null;
    let calDate = new Date();
    let selectedDateStr = ""; 
    let currentDraggedColor = "hsl(0, 100%, 85%)"; 
    let targetTimeEditIdx = null;

    let audioCtx = null;
    let noiseNode = null;

    const motivationalQuotes = [
        "포기하지 마라. 지금의 고통은 너의 미래를 빛나게 할 밑거름이다.",
        "할 수 있다고 믿는 사람은 결국 해내고 만다.",
        "오늘 걷지 않으면, 내일은 뛰어야 한다.",
        "성공은 매일 반복된 작은 노력들의 합산이다.",
        "가장 늦었다고 생각할 때가 가장 빠른 때다.",
        "미래는 현재 우리가 무엇을 하고 있는가에 달려 있다."
    ];

    function changeAppTheme(hue) {
        document.documentElement.style.setProperty('--bg-color', `hsl(${hue}, 100%, 98%)`);
        document.documentElement.style.setProperty('--primary', `hsl(${hue}, 100%, 88%)`);
        document.documentElement.style.setProperty('--primary-hover', `hsl(${hue}, 100%, 80%)`);
        document.documentElement.style.setProperty('--accent', `hsl(${hue}, 100%, 93%)`);
        const titleColor = document.getElementById('appThemePreview');
        if(titleColor) titleColor.style.color = `hsl(${hue}, 100%, 75%)`;
        localStorage.setItem('study_v11_hue', hue);
    }

    function convertSecToTime(totalSec) {
        const h = String(Math.floor(totalSec / 3600)).padStart(2, '0');
        const m = String(Math.floor((totalSec % 3600) / 60)).padStart(2, '0');
        const s = String(totalSec % 60).padStart(2, '0');
        return `${h}:${m}:${s}`;
    }

    function renderDateTimeInfo() {
        const now = new Date();
        const week = ['일요일','월요일','화요일','수요일','목요일','금요일','토요일'];
        document.getElementById('todayDateStr').innerText = `${now.getFullYear()}년 ${String(now.getMonth()+1).padStart(2,'0')}월 ${String(now.getDate()).padStart(2,'0')}일 (${week[now.getDay()]})`;
    }

    function displayRandomQuote() {
        const randIdx = Math.floor(Math.random() * motivationalQuotes.length);
        document.getElementById('quoteDisplayArea').innerText = `"${motivationalQuotes[randIdx]}"`;
    }

    function switchTab(idx) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('page' + idx).classList.add('active');
        document.getElementById('tab' + idx).classList.add('active');
        
        if(idx === 1) renderGridCalendar();
        if(idx === 2) renderWordList();
    }

    // ==================== [⏱️ 타이머] ====================
    function renderSubjects() {
        const container = document.getElementById('subjectContainer');
        container.innerHTML = '';
        let totalSum = 0;

        subjects.forEach((sub, sIdx) => {
            totalSum += sub.time;
            const isRunning = activeSubIdx === sIdx;
            const card = document.createElement('div');
            card.className = `subject-card`;
            
            let todoHtml = '';
            sub.todos.forEach(td => {
                todoHtml += `
                    <li class="sub-todo-item ${td.done ? 'done' : ''}">
                        <div onclick="toggleTodoCheck(${sIdx}, ${td.id})" style="cursor:pointer; flex:1;">
                            <input type="checkbox" ${td.done ? 'checked' : ''} style="pointer-events:none;">
                            <span>${td.text}</span>
                        </div>
                        <span style="color:#ff4d4d; padding:0 5px; cursor:pointer;" onclick="removeTodoItem(${sIdx}, ${td.id})">삭제</span>
                    </li>
                `;
            });

            card.innerHTML = `
                <div class="subject-header">
                    <div class="sub-title">${sub.name}</div>
                    <div style="display:flex; align-items:center; gap:8px;">
                        <span class="sub-time" id="subTime-${sIdx}">${convertSecToTime(sub.time)}</span>
                        <button class="play-btn ${isRunning ? 'studying' : ''}" onclick="pressTimerTrigger(${sIdx})">
                            ${isRunning ? '정지' : '시작'}
                        </button>
                    </div>
                </div>

                <div class="timer-control-row">
                    <button class="sm-btn" onclick="openTimeModal(${sIdx})">✏️ 원하는 시간으로 수정</button>
                    <button class="sm-btn" style="color:#ff6b6b;" onclick="resetSubjectTime(${sIdx})">초기화</button>
                </div>

                <div class="sub-todo-box">
                    <ul class="sub-todo-list">${todoHtml || '<p style="margin:0; font-size:12px; color:#aaa; text-align:center;">할 일이 없습니다.</p>'}</ul>
                    <div class="inline-input-row">
                        <input type="text" id="todoInputFor-${sIdx}" placeholder="할 일 지정...">
                        <button class="inline-add-btn" onclick="addTodoToSubject(${sIdx})">등록</button>
                    </div>
                </div>
                <div style="text-align:right; margin-top:8px;">
                    <span style="font-size:11px; color:#bbb; cursor:pointer;" onclick="deleteSubjectCard(${sIdx})">과목 삭제</span>
                </div>
            `;
            container.appendChild(card);
        });
        document.getElementById('globalTotalTimer').innerText = convertSecToTime(totalSum);
    }

    function pressTimerTrigger(idx) {
        if(activeSubIdx === idx) {
            clearInterval(mainLoopInterval);
            activeSubIdx = null;
        } else {
            if(activeSubIdx !== null) clearInterval(mainLoopInterval);
            activeSubIdx = idx;
            mainLoopInterval = setInterval(() => {
                subjects[activeSubIdx].time++;
                document.getElementById(`subTime-${activeSubIdx}`).innerText = convertSecToTime(subjects[activeSubIdx].time);
                let sum = subjects.reduce((acc, cur) => acc + cur.time, 0);
                document.getElementById('globalTotalTimer').innerText = convertSecToTime(sum);
                localStorage.setItem('study_v11_subjects', JSON.stringify(subjects));
            }, 1000);
        }
        renderSubjects();
    }

    function openTimeModal(idx) {
        targetTimeEditIdx = idx;
        const currentTotalSec = subjects[idx].time;
        document.getElementById('editHour').value = Math.floor(currentTotalSec / 3600);
        document.getElementById('editMin').value = Math.floor((currentTotalSec % 3600) / 60);
        document.getElementById('editSec').value = currentTotalSec % 60;
        document.getElementById('timeEditModal').style.display = 'flex';
    }

    function closeTimeModal() { document.getElementById('timeEditModal').style.display = 'none'; }

    function saveCustomTime() {
        if(targetTimeEditIdx === null) return;
        const h = parseInt(document.getElementById('editHour').value) || 0;
        const m = parseInt(document.getElementById('editMin').value) || 0;
        const s = parseInt(document.getElementById('editSec').value) || 0;

        subjects[targetTimeEditIdx].time = (h * 3600) + (m * 60) + s;
        localStorage.setItem('study_v11_subjects', JSON.stringify(subjects));
        closeTimeModal();
        renderSubjects();
    }

    function resetSubjectTime(idx) {
        if(activeSubIdx === idx) { clearInterval(mainLoopInterval); activeSubIdx = null; }
        subjects[idx].time = 0;
        localStorage.setItem('study_v11_subjects', JSON.stringify(subjects));
        renderSubjects();
    }

    function addNewSubject() {
        const name = document.getElementById('newSubjectName').value.trim();
        if(!name) return;
        subjects.push({ name: name, time: 0, todos: [] });
        localStorage.setItem('study_v11_subjects', JSON.stringify(subjects));
        document.getElementById('newSubjectName').value = '';
        renderSubjects();
    }

    // ==================== [📅 캘린더] ====================
    function renderGridCalendar() {
        const grid = document.getElementById('calendarGrid'); grid.innerHTML = '';
        const viewYear = calDate.getFullYear(); const viewMonth = calDate.getMonth();
        document.getElementById('calendarMonthTitle').innerText = `${viewYear}년 ${String(viewMonth + 1).padStart(2,'0')}월`;

        const weeksLabels = ['일','월','화','수','목','금','토'];
        weeksLabels.forEach(w => {
            const label = document.createElement('div'); label.className = 'cal-day-label'; label.innerText = w; grid.appendChild(label);
        });

        const firstDayIndex = new Date(viewYear, viewMonth, 1).getDay();
        const lastDate = new Date(viewYear, viewMonth + 1, 0).getDate();

        for(let i=0; i<firstDayIndex; i++) grid.appendChild(document.createElement('div'));

        const nowReal = new Date();
        for(let day=1; day<=lastDate; day++) {
            const cell = document.createElement('div'); cell.className = 'cal-cell';
            if(nowReal.getFullYear() === viewYear && nowReal.getMonth() === viewMonth && nowReal.getDate() === day) cell.classList.add('today');

            cell.innerHTML = `<div>${day}</div>`;
            const dateKey = `${viewYear}-${String(viewMonth+1).padStart(2,'0')}-${String(day).padStart(2,'0')}`;
            
            if(calendarEvents[dateKey] && calendarEvents[dateKey].length > 0) {
                const todoContainer = document.createElement('div'); todoContainer.className = 'cal-todo-container';
                calendarEvents[dateKey].slice(0, 3).forEach(evt => {
                    const bar = document.createElement('div'); bar.className = 'cal-todo-bar'; bar.style.backgroundColor = evt.color; bar.innerText = evt.text; todoContainer.appendChild(bar);
                });
                cell.appendChild(todoContainer);
            }
            cell.onclick = () => openCalendarModal(dateKey);
            grid.appendChild(cell);
        }
    }

    function moveMonth(dir) { calDate.setMonth(calDate.getMonth() + dir); renderGridCalendar(); }
    function updateDragColor(hueValue) { currentDraggedColor = `hsl(${hueValue}, 100%, 85%)`; document.getElementById('colorPreviewText').style.color = currentDraggedColor; }
    
    function openCalendarModal(dateKey) {
        selectedDateStr = dateKey;
        document.getElementById('modalDateTitle').innerText = `📅 ${dateKey} 일정`;
        updateModalList();
        document.getElementById('calModal').style.display = 'flex';
    }

    function updateModalList() {
        const listContainer = document.getElementById('modalTodoList'); listContainer.innerHTML = '';
        const currentList = calendarEvents[selectedDateStr] || [];
        if(currentList.length === 0) { listContainer.innerHTML = '<p style="font-size:12px; color:#aaa; text-align:center;">일정이 없습니다.</p>'; return; }
        currentList.forEach((item, idx) => {
            const li = document.createElement('li'); li.className = 'modal-todo-item'; li.style.backgroundColor = item.color;
            li.innerHTML = `<span>${item.text}</span><button class="modal-todo-del" onclick="deleteModalTodo(${idx})">삭제</button>`;
            listContainer.appendChild(li);
        });
    }

    function closeModal() { document.getElementById('calModal').style.display = 'none'; }
    function saveCalendarTodo() {
        const val = document.getElementById('modalTodoInput').value.trim(); if(!val) return;
        if(!calendarEvents[selectedDateStr]) calendarEvents[selectedDateStr] = [];
        calendarEvents[selectedDateStr].push({ text: val, color: currentDraggedColor });
        localStorage.setItem('study_v11_calendar', JSON.stringify(calendarEvents));
        document.getElementById('modalTodoInput').value = ''; updateModalList(); renderGridCalendar();
    }

    // ==================== [📝 영어 사전] ====================
    function openNaverDic() {
        const word = document.getElementById('dicSearchInput').value.trim(); if(!word) return;
        window.open(`https://en.dict.naver.com/#/search?query=${encodeURIComponent(word)}`, '_blank');
    }

    function saveManualWord() {
        const eng = document.getElementById('addEngField').value.trim();
        const kor = document.getElementById('addKorField').value.trim();
        if(!eng || !kor) return;

        words.unshift({ eng: eng, kor: kor, done: false });
        localStorage.setItem('study_v11_words', JSON.stringify(words));
        document.getElementById('addEngField').value = '';
        document.getElementById('addKorField').value = '';
        renderWordList();
    }

    function renderWordList() {
        const container = document.getElementById('wordCardList'); container.innerHTML = '';
        if(words.length === 0) { container.innerHTML = '<p style="font-size:13px; color:#aaa; text-align:center; margin-top:20px;">보관된 단어가 없습니다.</p>'; return; }
        words.forEach((w, idx) => {
            const card = document.createElement('div'); card.className = `word-card ${w.done ? 'memorized' : ''}`;
            card.innerHTML = `
                <div onclick="toggleWordDone(${idx})" style="cursor:pointer; flex:1; text-align:left;">
                    <span class="word-eng">${w.eng}</span><span class="word-kor">${w.kor}</span>
                </div>
                <span style="font-size:12px; color:#ff6b6b; cursor:pointer;" onclick="deleteWordItem(${idx})">삭제</span>
            `;
            container.appendChild(card);
        });
    }

    // ==================== [💡 유틸리티 제어] ====================
    function playNoise(type) {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (noiseNode) { noiseNode.stop(); noiseNode.disconnect(); noiseNode = null; }
        if (type === 'none') return;

        const bufferSize = 2 * audioCtx.sampleRate, noiseBuffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate), output = noiseBuffer.getChannelData(0);
        for (let i = 0; i < bufferSize; i++) { output[i] = Math.random() * 2 - 1; }

        noiseNode = audioCtx.createBufferSource();
        noiseNode.buffer = noiseBuffer; noiseNode.loop = true;

        const filter = audioCtx.createBiquadFilter();
        if(type === 'rain') { filter.type = 'lowpass'; filter.frequency.setValueAtTime(450, audioCtx.currentTime); }
        else if(type === 'cafe') { filter.type = 'bandpass'; filter.frequency.setValueAtTime(900, audioCtx.currentTime); }
        else if(type === 'lib') { filter.type = 'peaking'; filter.frequency.setValueAtTime(1200, audioCtx.currentTime); }
        else if(type === 'fire') { filter.type = 'highpass'; filter.frequency.setValueAtTime(300, audioCtx.currentTime); }
        else if(type === 'cricket') { filter.type = 'bandpass'; filter.frequency.setValueAtTime(2200, audioCtx.currentTime); }
        else if(type === 'wave') { filter.type = 'lowpass'; filter.frequency.setValueAtTime(250, audioCtx.currentTime); }

        noiseNode.connect(filter); filter.connect(audioCtx.destination);
        noiseNode.start();
    }

    function renderWater() {
        const container = document.getElementById('waterContainer'); container.innerHTML = '';
        for(let i=1; i<=5; i++) {
            const dot = document.createElement('span'); dot.className = `water-dot ${i <= waterCount ? 'filled' : ''}`;
            dot.onclick = () => { waterCount = (waterCount == i) ? i - 1 : i; localStorage.setItem('study_v11_water', waterCount); renderWater(); };
            container.appendChild(dot);
        }
    }

    function saveCumulativeReview() {
        const score = document.getElementById('dayScore').value;
        const text = document.getElementById('dayReviewText').value.trim();
        if(!text) { alert("한줄평 내용을 기입해주세요!"); return; }

        const now = new Date();
        const dateStr = `${now.getMonth()+1}/${now.getDate()}`;

        reviewsHistory.unshift({ date: dateStr, score: score, text: text });
        localStorage.setItem('study_v11_reviews_list', JSON.stringify(reviewsHistory));
        
        document.getElementById('dayReviewText').value = '';
        renderReviewHistory();
    }

    function renderReviewHistory() {
        const area = document.getElementById('reviewHistoryArea'); area.innerHTML = '';
        if(reviewsHistory.length === 0) {
            area.innerHTML = '<li style="font-size:11px; color:#aaa; text-align:center; padding:10px 0;">기록된 평가가 없습니다.</li>';
            return;
        }
        reviewsHistory.forEach((item, idx) => {
            const li = document.createElement('li'); li.className = 'review-history-item';
            li.innerHTML = `<span><strong>[${item.date}]</strong> ${item.score} - ${item.text}</span><span style="color:#ff6b6b; cursor:pointer; font-weight:bold; margin-left:8px;" onclick="deleteReviewItem(${idx})">x</span>`;
            area.appendChild(li);
        });
    }

    function deleteReviewItem(idx) {
        reviewsHistory.splice(idx, 1);
        localStorage.setItem('study_v11_reviews_list', JSON.stringify(reviewsHistory));
        renderReviewHistory();
    }

    function toggleEcoMode() {
        const isEco = document.body.classList.toggle('eco-mode');
        document.getElementById('ecoBtn').innerText = isEco ? "🔋 일반 모드 복귀" : "🔋 절전 모드 ON";
        document.getElementById('ecoBtn').style.backgroundColor = isEco ? "#ffb7b2" : "#444";
    }

    function checkStreakSystem() {
        const lastOpen = localStorage.getItem('study_v11_last_open_date');
        let streak = parseInt(localStorage.getItem('study_v11_streak_count')) || 0;
        const todayStr = new Date().toDateString();

        if (lastOpen) {
            const yesterday = new Date(); yesterday.setDate(yesterday.getDate() - 1);
            if (lastOpen === todayStr) {} 
            else if (lastOpen === yesterday.toDateString()) { streak += 1; } 
            else { streak = 1; }
        } else { streak = 1; }

        localStorage.setItem('study_v11_last_open_date', todayStr);
        localStorage.setItem('study_v11_streak_count', streak);
        document.getElementById('streakBadgeBox').innerText = `🔥 ${streak}일째`;
    }

    // 초기 실행
    document.getElementById('appThemeSlider').value = savedHue;
    changeAppTheme(savedHue);
    renderDateTimeInfo();
    displayRandomQuote();
    renderSubjects();
    renderWater();
    renderReviewHistory();
    checkStreakSystem();
</script>
</body>
</html>
