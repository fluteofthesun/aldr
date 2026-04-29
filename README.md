<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>aldr V23.11</title>
    <style>
        :root { 
            --bg: #1a1a1a; --panel: #111; --accent: #ffffff; --dim: #666; --border: #333; --save: #44aa44; --danger: #ff4444; 
            --today-text: #ffff00; --note-marker: #ff8c00; --percent-color: #00ffff;
        }
        body { font-family: monospace; background: var(--bg); color: #ddd; padding: 0; margin: 0; line-height: 1.2; overflow-x: hidden; display: flex; flex-direction: column; height: 100vh; }
        
        .btn-h { height: 32px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; padding: 0 6px; font-size: 10px; cursor: pointer; flex-shrink: 0; }
        .top-utility-bar { position: fixed; top: 0; width: 100%; z-index: 1000; background: #111; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 4px; padding: 4px 5px; box-sizing: border-box; height: 45px; overflow-x: auto; }
        
        .btn-plus-only { padding: 0 10px !important; font-weight: 900; background: #333; color: #eee; border: 1px solid #555; font-size: 18px; }
        .btn-wide { min-width: 30px; font-size: 14px; padding: 0 !important; }
        .spacer-sm { width: 10px; flex-shrink: 0; }

        .full-modal { position: fixed; top: 45px; left: 0; width: 100%; bottom: 45px; background: rgba(10,10,10,0.98); z-index: 8000; display: none; padding: 20px; box-sizing: border-box; color: #fff; overflow-y: auto; }
        .full-modal.open { display: block; }
        .lock-bg { pointer-events: none; opacity: 0.3; filter: grayscale(1); }

        .header-container { z-index: 1000; background: var(--bg); border-top: 1px solid #444; flex-shrink: 0; }
        .header-row { display: flex; align-items: center; gap: 4px; padding: 5px 8px; background: #111; height: 45px; }
        
        .coord-window { background: #000; border: 1px solid #444; color: #fff; padding: 0 6px; font-size: 11px; text-align: center; cursor: pointer; white-space: nowrap; }
        .coord-window.active-view { border-bottom: 2px solid var(--accent); }
        .date-viewer.is-today { color: var(--today-text); font-weight: bold; }
        
        button { background: #333; color: #eee; border: 1px solid #555; padding: 0 6px; cursor: pointer; font-family: monospace; font-size: 9px; text-transform: uppercase; }
        .btn-square-b { width: 28px; height: 28px; flex-shrink: 0; }
        .btn-status { min-width: 45px; font-size: 10px; border: 1px solid #555; font-weight: bold; }
        .btn-status.saved { color: var(--save); }
        .btn-status.has-record { border-color: var(--save) !important; }
        .btn-note-active { background: #fff !important; color: #000 !important; border-color: #fff !important; }
        .btn-modal-active { background: #fff !important; color: #000 !important; border-color: #fff !important; }

        .t-panel-inner { display: flex; width: 100%; }
        .t-grid { display: flex; gap: 4px; overflow-x: auto; padding: 5px; flex-grow: 1; }
        .t-item { flex: 0 0 35px; height: 32px; border: 1px solid #333; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 11px; }
        .t-has-data { background: #333; color: #fff; }
        .t-active { border-color: #fff !important; color: #fff !important; }
        .t-folder-icon { flex: 0 0 40px; border-left: 1px solid #333; display: flex; align-items: center; justify-content: center; color: #555; font-size: 16px; }

        .cal-container { display: flex; justify-content: space-between; align-items: flex-start; gap: 10px; max-width: 400px; margin: 0 auto; }
        .cal-grid { display: grid; grid-template-columns: repeat(7, 1fr); grid-template-rows: repeat(7, 1fr); gap: 0px; flex-grow: 1; height: 260px; border: 1px solid #333; }
        .cal-controls-right { display: flex; flex-direction: column; gap: 4px; align-items: center; min-width: 50px; }
        
        .cal-day { padding: 4px 0; text-align: center; font-size: 10px; cursor: pointer; border: 1px solid #222; color: #555; display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 35px; }
        .cal-has-data { color: #fff !important; }
        .cal-viewing { border: 1px solid #fff !important; }
        .cal-today { color: var(--today-text) !important; font-weight: bold; }
        .cal-perc-val { font-size: 8px; color: var(--percent-color); margin-top: 2px; }

        #app { padding: 10px; padding-top: 55px; padding-bottom: 60px; overflow-y: auto; flex-grow: 1; }
        .node { margin-left: 12px; padding: 2px 0; border-left: 1px solid #333; position: relative; }
        .node.is-inactive .node-text { text-decoration: line-through; }
        .active-node > .node-content { background: rgba(255, 255, 255, 0.08); border-left: 2px solid #fff !important; }
        .primed-delete > .node-content { background: rgba(255, 68, 68, 0.3) !important; border-left: 2px solid var(--danger) !important; }
        .address-input { width: auto; min-width: 25px; max-width: 55px; background: transparent; border: none; color: #fff; font-size: 11px; margin-right: 2px; }
        .note-textarea { background: rgba(255,140,0,0.05); border: none; border-left: 1px solid var(--note-marker); color: #bbb; font-family: monospace; font-size: 12px; margin: 5px 0 5px 20px; padding: 8px; width: calc(100% - 40px); display: block; resize: none; overflow: hidden; }
        input[type="checkbox"] { transform: scale(1.1); cursor: pointer; }
        .hidden { display: none; }
        .cal-toggle-container { display: flex; flex-direction: column; gap: 2px; margin-top: 5px; }
        .cal-toggle-btn { width: 35px; height: 30px; padding: 4px; font-size: 14px; }
        .cal-toggle-btn.active { border-color: #fff; color: #fff; background: #555; }
    </style>
</head>
<body onload="init()">

    <div class="top-utility-bar" id="top-bar">
        <button onclick="smartAddNode()" class="btn-h btn-plus-only">+</button>
        <button onclick="toggleRecursiveExpansion()" class="btn-h btn-wide" style="font-weight:bold;">×</button>
        <div class="spacer-sm"></div>
        <button onclick="handleDelete()" class="btn-h" style="color:var(--danger)">DEL</button>
        <div class="spacer-sm"></div>
        <button onclick="undo()" id="undo-btn" class="btn-h">UNDO</button>
        <button onclick="redo()" id="redo-btn" class="btn-h">REDO</button>
        <div class="spacer-sm"></div>
        <button onclick="toggleActiveNote()" class="btn-h" id="note-toggle-btn"><b>*</b></button>
        <button onclick="toggleInactive()" class="btn-h" id="btn-inactive">∅</button>
    </div>

    <div id="help-modal" class="full-modal">
        <div style="font-size:14px; font-weight:bold; border-bottom:1px solid #444; padding-bottom:10px; margin-bottom:15px;">Instructions</div>
        <p style="font-size:11px; line-height:1.6; color:#aaa;">
            • [+]: Add sub-node.<br>
            • [×]: Deep collapse/expand toggle.<br>
            • [∅]: Toggle inactive state.
        </p>
        <button onclick="toggleGlobalModal('help-modal')" style="width:100%; padding:10px;">CLOSE</button>
    </div>

    <div id="settings-modal" class="full-modal">
        <div style="font-size:14px; font-weight:bold; border-bottom:1px solid #444; padding-bottom:10px; margin-bottom:15px;">Settings</div>
        <button onclick="exportData()" style="padding:12px; width:100%; border:1px solid var(--save); background:transparent; color:var(--save); margin-bottom:10px; cursor:pointer;">EXPORT LEDGER (.JSON)</button>
        <button onclick="toggleGlobalModal('help-modal')" style="padding:12px; width:100%; border:1px solid #555; background:transparent; color:#fff; margin-bottom:20px; cursor:pointer;">HELP</button>
        <div style="font-size:11px; color:#888;" id="ver-settings">aldr v23.11</div>
    </div>

    <div id="modal-overlay" style="position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.85); z-index:9000; display:none; align-items:center; justify-content:center;">
        <div class="compact-modal">
            <div id="modal-msg" style="font-size:10px; color:#fff;">SAVE CHANGES?</div>
            <div class="compact-btn-row" style="display:flex; flex-direction:column; gap:4px; margin-top:12px;">
                <button onclick="handleModalResponse(true)">YES</button>
                <button onclick="handleModalResponse(false)">NO</button>
                <button onclick="document.getElementById('modal-overlay').style.display='none'">CANCEL</button>
            </div>
        </div>
    </div>

    <div id="app"></div>

    <div class="header-container" id="bottom-bar">
        <div id="t-panel" style="background:var(--panel); display:none; padding: 5px 0;">
            <div class="t-panel-inner">
                <div id="t-grid" class="t-grid"></div>
                <div class="t-folder-icon">📁</div>
            </div>
        </div>
        <div id="cal-panel" style="background:var(--panel); border-top:1px solid var(--border); display:none; padding:15px;">
            <div class="cal-container">
                <div class="cal-grid" id="cal-grid"></div>
                <div class="cal-controls-right">
                    <button onclick="adjustMonth(1)" style="width:35px; height:20px;">▲</button>
                    <input type="number" id="cal-m" min="1" max="12" style="background:#000; border:1px solid #444; color:#fff; font-family:monospace; font-size:10px; padding:2px; text-align:center; width:35px;" onchange="updateDateFromInput()">
                    <input type="number" id="cal-y" min="2000" max="2099" style="background:#000; border:1px solid #444; color:#fff; font-family:monospace; font-size:10px; padding:2px; text-align:center; width:35px;" onchange="updateDateFromInput()">
                    <button onclick="adjustMonth(-1)" style="width:35px; height:20px;">▼</button>
                    <div class="cal-toggle-container">
                        <button id="toggle-perc" class="cal-toggle-btn" onclick="setCalMode('percent')">%</button>
                        <button id="toggle-count" class="cal-toggle-btn active" onclick="setCalMode('count')">#</button>
                    </div>
                </div>
            </div>
        </div>
        <div class="header-row" id="footer-controls">
            <span style="font-size:9px; color:var(--dim); font-weight:bold; width:65px; line-height:1;" id="ver-footer">aldr<br>v23.11</span>
            <div style="border:1px solid #333; width:32px; height:32px; display:flex; align-items:center; justify-content:center; opacity:0.25;">↻</div>
            <div class="coord-window t-viewer btn-h" id="t-display" onclick="togglePanel('t-panel')" style="min-width:40px;">T: 1</div>
            <div class="coord-window date-viewer btn-h" id="current-date-display" onclick="togglePanel('cal-panel')" style="flex-grow:1;">MON 00-00-00</div>
            <button id="save-status-btn" class="btn-status btn-h" onclick="manualSaveDay()">SAVE</button>
            <button onclick="toggleGlobalModal('settings-modal')" class="btn-square-b btn-h" id="btn-settings">⚙</button>
        </div>
    </div>

<script>
    const VERSION = "v23.11";
    let ledger = JSON.parse(localStorage.getItem('habitLedger')) || {};
    let templates = JSON.parse(localStorage.getItem('habitTemplates')) || {}; 
    let currentT = 1, currentDate = new Date().toISOString().split('T')[0];
    const systemToday = new Date().toISOString().split('T')[0];
    let workingData = [], savedSnapshot = "", activeParentId = null, editingId = null, primedDeleteId = null, openModalId = null;
    let undoStack = [];
    let redoStack = [];
    let pendingAction = null;
    let calMode = 'count';

    function init() { setCalMode('count'); loadWorkingState(); renderCalendar(); }

    function setCalMode(m) { calMode = m; document.getElementById('toggle-perc').classList.toggle('active', m==='percent'); document.getElementById('toggle-count').classList.toggle('active', m==='count'); renderCalendar(); }
    
    function recordState() { undoStack.push(JSON.stringify(workingData)); redoStack = []; updateHistoryBtns(); }
    function undo() { if (undoStack.length === 0) return; redoStack.push(JSON.stringify(workingData)); workingData = JSON.parse(undoStack.pop()); calculateProgress(); updateHistoryBtns(); }
    function redo() { if (redoStack.length === 0) return; undoStack.push(JSON.stringify(workingData)); workingData = JSON.parse(redoStack.pop()); calculateProgress(); updateHistoryBtns(); }
    function updateHistoryBtns() { 
        document.getElementById('undo-btn').style.opacity = undoStack.length ? "1" : "0.5"; 
        document.getElementById('redo-btn').style.opacity = redoStack.length ? "1" : "0.5"; 
    }

    function getRecursiveCount(targetNode, allNodes) {
        let count = 1;
        const children = allNodes.filter(n => n.parent === targetNode.id);
        children.forEach(child => {
            count += getRecursiveCount(child, allNodes);
        });
        return count;
    }

    function renderCalendar() {
        const parts = currentDate.split('-');
        const currentM = parseInt(parts[1]);
        const currentY = parseInt(parts[0]);
        document.getElementById('cal-m').value = currentM;
        document.getElementById('cal-y').value = currentY;
        
        const grid = document.getElementById('cal-grid'); grid.innerHTML = '';
        const firstDay = new Date(currentY, currentM - 1, 1).getDay();
        const daysInMonth = new Date(currentY, currentM, 0).getDate();
        ['S','M','T','W','T','F','S'].forEach(l => grid.innerHTML += `<div style="font-size:8px; color:#333; text-align:center; padding-bottom:4px;">${l}</div>`);
        for (let i = 0; i < firstDay; i++) grid.innerHTML += `<div class="cal-day"></div>`;
        
        const activeNode = activeParentId ? workingData.find(h => h.id === activeParentId) : null;

        for (let d = 1; d <= daysInMonth; d++) {
            const dateStr = `${currentY}-${String(currentM).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
            const dayData = ledger[dateStr + '_T' + currentT] || [];
            const hasData = dayData.some(n => n.value >= 1 && !n.inactive);
            
            let displayVal = d;
            let sub = "";

            if (activeNode) {
                const nodeInLedger = dayData.find(n => n.id === activeParentId);
                
                if (nodeInLedger) {
                    if (calMode === 'percent') {
                        sub = `<div class="cal-perc-val">${(nodeInLedger.value * 100).toFixed(0)}%</div>`;
                    } else if (calMode === 'count') {
                        const totalCount = getRecursiveCount(nodeInLedger, dayData);
                        sub = `<div class="cal-perc-val" style="color: #fff;">#${totalCount}</div>`;
                    }
                }
            }

            grid.innerHTML += `<div class="cal-day ${dateStr===systemToday?'cal-today':''} ${dateStr===currentDate?'cal-viewing':''} ${hasData?'cal-has-data':''}" onclick="switchDate('${dateStr}')"><span>${displayVal}</span>${sub}</div>`;
        }
    }

    function updateDateFromInput() {
        const m = document.getElementById('cal-m').value;
        const y = document.getElementById('cal-y').value;
        currentDate = `${y}-${String(m).padStart(2, '0')}-01`;
        loadWorkingState();
        renderCalendar();
    }

    function adjustMonth(delta) {
        const parts = currentDate.split('-');
        let m = parseInt(parts[1]) - 1 + delta;
        let y = parseInt(parts[0]);
        if (m < 0) { m = 11; y--; }
        else if (m > 11) { m = 0; y++; }
        currentDate = `${y}-${String(m + 1).padStart(2, '0')}-01`;
        renderCalendar();
    }

    function getUniqueName(name, parentId) {
        const siblings = workingData.filter(n => n.parent === parentId);
        let candidate = name;
        let counter = 1;
        while (siblings.some(n => n.name.toLowerCase() === candidate.toLowerCase() && n.id !== editingId)) {
            candidate = `${name} (${counter})`;
            counter++;
        }
        return candidate;
    }

    function toggleRecursiveExpansion() {
        if (!activeParentId) return;
        recordState();
        const descendants = [];
        const findDescendants = (id) => { workingData.filter(h => h.parent === id).forEach(child => { descendants.push(child); findDescendants(child.id); }); };
        findDescendants(activeParentId);
        const folders = descendants.filter(d => workingData.some(h => h.parent === d.id));
        const anyExpanded = folders.some(f => f.expanded);
        folders.forEach(f => f.expanded = !anyExpanded);
        calculateProgress();
    }

    function toggleInactive() {
        if (openModalId || !activeParentId) return;
        recordState();
        const node = workingData.find(h => h.id === activeParentId);
        node.inactive = !node.inactive;
        const setChildrenInactive = (pId, status) => {
            workingData.filter(h => h.parent === pId).forEach(c => {
                c.inactive = status;
                setChildrenInactive(c.id, status);
            });
        };
        setChildrenInactive(activeParentId, node.inactive);
        calculateProgress();
    }

    function calculateProgress() {
        const getScore = (nId) => {
            const n = workingData.find(h => h.id === nId);
            const children = workingData.filter(h => h.parent === nId);
            const activeChildren = children.filter(c => !c.inactive);
            if (children.length === 0) return n.value;
            if (activeChildren.length === 0) { n.value = 0; return 0; }
            const sum = activeChildren.reduce((acc, c) => acc + getScore(c.id), 0);
            n.value = sum / activeChildren.length;
            return n.value;
        };
        workingData.filter(h => h.parent === null).forEach(r => getScore(r.id));
        const isSynced = JSON.stringify(workingData) === savedSnapshot;
        const btn = document.getElementById('save-status-btn');
        btn.innerText = isSynced ? "✓✓✓" : "SAVE";
        btn.classList.toggle('saved', isSynced);
        btn.classList.toggle('has-record', !!ledger[`${currentDate}_T${currentT}`]);
        render();
    }

    function buildHTML(pId) {
        const children = workingData.filter(h => h.parent === pId); let html = '';
        children.forEach(node => {
            const hasC = workingData.some(h => h.parent === node.id); 
            const isA = activeParentId === node.id ? 'active-node' : ''; 
            const isI = node.inactive ? 'is-inactive' : '';
            const isP = primedDeleteId === node.id ? 'primed-delete' : ''; 
            const expI = hasC ? (node.expanded ? '(-)' : '(+)') : '';
            const hasN = node.note && node.note.trim().length > 0;
            html += `<div class="node ${isA} ${isP} ${isI}">
                <div class="node-content" style="display:flex; align-items:center; justify-content:space-between;">
                    <span>
                        <input type="text" class="address-input" value="${getAddress(node.id)}" onchange="relocate('${node.id}', this.value)">
                        <span class="node-text" style="cursor:pointer; color:${hasC?'#fff':'#888'}; font-weight:${hasC?'bold':'normal'};" onclick="selectNode('${node.id}')" ondblclick="editingId='${node.id}'; render();">
                            • ${editingId === node.id ? `<input type="text" id="edit-${node.id}" value="${node.name}" onblur="workingData.find(h=>h.id==='${node.id}').name=getUniqueName(this.value, '${node.parent}'); editingId=null; calculateProgress();" onkeydown="if(event.key==='Enter') this.blur()">` : node.name}
                            ${hasN?'<span style="color:var(--note-marker);margin-left:5px;">*</span>':''}
                            <span onclick="event.stopPropagation(); node = workingData.find(h=>h.id==='${node.id}'); node.expanded=!node.expanded; render();" style="cursor:pointer; margin-left:4px; font-size:11px; opacity:0.5;">${expI}</span>
                        </span>
                    </span>
                    <span class="controls"><span style="color:#ffcc00; font-size:11px; margin-right:5px;">${(node.value * 100).toFixed(0)}%</span><input type="checkbox" ${node.inactive ? 'disabled' : ''} ${node.value >= 1 ? 'checked' : ''} onchange="recordState(); const val = this.checked ? 1 : 0; if(${hasC}) { const setR = (p) => { workingData.forEach(n => { if(n.parent === p) { n.value = val; setR(n.id); } }); }; setR('${node.id}'); } workingData.find(h=>h.id==='${node.id}').value = val; calculateProgress();"></span></div>${node.noteOpen ? `<textarea class="note-textarea" onchange="workingData.find(h=>h.id==='${node.id}').note=this.value; calculateProgress();">${node.note || ''}</textarea>` : ''}<div class="${(hasC && !node.expanded) ? 'hidden' : ''}">${buildHTML(node.id)}</div></div>`;
        }); return html;
    }

    function selectNode(id) { if (openModalId) return; if (primedDeleteId !== id) { primedDeleteId = null; } activeParentId = id; calculateProgress(); if (document.getElementById('cal-panel').style.display === 'block') renderCalendar(); }
    
    function render() { 
        document.getElementById('app').innerHTML = buildHTML(null); 
        const nBtn = document.getElementById('note-toggle-btn'); 
        if (activeParentId) { 
            const aN = workingData.find(h => h.id === activeParentId); 
            if (aN && aN.noteOpen) nBtn.classList.add('btn-note-active'); else nBtn.classList.remove('btn-note-active'); 
        } else nBtn.classList.remove('btn-note-active'); 
        document.querySelectorAll('.note-textarea').forEach(el => { el.style.height='auto'; el.style.height=el.scrollHeight+'px'; }); 
        
        if (editingId) {
            setTimeout(() => {
                const el = document.getElementById('edit-' + editingId);
                if (el) { el.focus(); el.select(); }
            }, 100);
        }
    }

    function toggleGlobalModal(mId) {
        if (openModalId && openModalId !== mId) { closeModal(openModalId); }
        const m = document.getElementById(mId);
        const app = document.getElementById('app');
        const bottomBar = document.getElementById('bottom-bar');
        const topBar = document.getElementById('top-bar');
        const settBtn = document.getElementById('btn-settings');

        if (m.classList.contains('open')) {
            m.classList.remove('open');
            openModalId = null;
            app.classList.remove('lock-bg');
            bottomBar.classList.remove('lock-bg');
            topBar.classList.remove('lock-bg');
            document.getElementById('footer-controls').style.pointerEvents = 'auto';
            topBar.style.pointerEvents = 'auto';
            settBtn.classList.remove('btn-modal-active');
        } else {
            m.classList.add('open');
            openModalId = mId;
            app.classList.add('lock-bg');
            bottomBar.classList.add('lock-bg');
            topBar.classList.add('lock-bg');
            
            document.getElementById('footer-controls').style.pointerEvents = 'none';
            topBar.style.pointerEvents = 'none';
            settBtn.style.pointerEvents = 'auto';
            settBtn.classList.add('btn-modal-active');
        }
    }
    
    function closeModal(mId) { const m = document.getElementById(mId); m.classList.remove('open'); }

    function togglePanel(pId) {
        if (openModalId) return;
        const p = document.getElementById(pId);
        const w = pId === 't-panel' ? document.getElementById('t-display') : document.getElementById('current-date-display');
        const isOpen = p.style.display === 'block';
        p.style.display = isOpen ? 'none' : 'block';
        w.classList.toggle('active-view', !isOpen);
        if(p.style.display === 'block') { if(pId === 't-panel') renderTGrid(); if(pId === 'cal-panel') renderCalendar(); }
    }
    
    function renderTGrid() { 
        const grid = document.getElementById('t-grid'); 
        grid.innerHTML = ''; 
        for(let i=1; i<=15; i++) {
            const key = `${currentDate}_T${i}`;
            const hasActiveData = ledger[key] && ledger[key].some(n => n.value >= 1 && !n.inactive);
            const classes = `t-item ${i == currentT ? 't-active' : ''} ${hasActiveData ? 't-has-data' : ''}`;
            grid.innerHTML += `<div class="${classes}" onclick="switchTrack(${i})">${i}</div>`;
        }
    }

    function loadWorkingState() { 
        const parts = currentDate.split('-'); 
        const d = new Date(parts[0], parts[1]-1, parts[2]);
        const dayName = d.toLocaleDateString('en-US', {weekday: 'short'}).toUpperCase();
        document.getElementById('t-display').innerText = `T: ${currentT}`; 
        document.getElementById('current-date-display').innerText = `${dayName} ${parts[1]}-${parts[2]}-${parts[0].slice(2)}`; 
        document.getElementById('current-date-display').classList.toggle('is-today', currentDate === systemToday); 
        
        const key = `${currentDate}_T${currentT}`; 
        if (ledger[key]) workingData = JSON.parse(JSON.stringify(ledger[key])); 
        else { 
            const tT = templates[currentT]; 
            workingData = tT ? JSON.parse(JSON.stringify(tT)) : [{ id: "root-1", parent: null, name: "[...]", value: 0, expanded: true, note: "", noteOpen: false, inactive: false }]; 
        } 
        
        const root = workingData.find(h => h.parent === null);
        activeParentId = root ? root.id : null;

        savedSnapshot = JSON.stringify(workingData); calculateProgress(); 
        if(document.getElementById('t-panel').style.display === 'block') renderTGrid();
    }

    function manualSaveDay() { 
        const key = `${currentDate}_T${currentT}`; 
        ledger[key] = JSON.parse(JSON.stringify(workingData)); 
        localStorage.setItem('habitLedger', JSON.stringify(ledger)); 
        templates[currentT] = workingData.map(h => ({ ...h, value: 0, note: "", noteOpen: false, inactive: false })); 
        localStorage.setItem('habitTemplates', JSON.stringify(templates)); 
        savedSnapshot = JSON.stringify(workingData); 
        calculateProgress(); 
        if(document.getElementById('cal-panel').style.display === 'block') renderCalendar(); 
        if(document.getElementById('t-panel').style.display === 'block') renderTGrid();
    }

    function switchTrack(v) { if (JSON.stringify(workingData) !== savedSnapshot) { showModal(`SAVE CHANGES TO T:${currentT}?`, () => { manualSaveDay(); proceedToTrack(v); }, () => proceedToTrack(v), () => {} ); } else proceedToTrack(v); }
    function proceedToTrack(v) { currentT = v; loadWorkingState(); renderCalendar(); }
    function switchDate(v) { currentDate = v; loadWorkingState(); renderCalendar(); }
    
    function showModal(msg, onY, onN, onC) { 
        const o = document.getElementById('modal-overlay'); 
        document.getElementById('modal-msg').innerText = msg; 
        pendingAction = {onY, onN, onC};
        o.style.display = 'flex'; 
    }
    
    function handleModalResponse(res) {
        document.getElementById('modal-overlay').style.display='none';
        if(res) pendingAction.onY(); else pendingAction.onN();
    }

    function relocate(nodeId, newAddr) { 
        recordState(); 
        const node = workingData.find(h => h.id === nodeId); 
        if (newAddr === "0") { node.parent = null; calculateProgress(); return; } 
        const parts = newAddr.split('.'); 
        const lastPart = parseInt(parts.pop()); 
        let targetParent = null; 
        if (parts.length > 0) { 
            const pathStr = parts.join('.'); 
            targetParent = workingData.find(h => getAddress(h.id) === pathStr); 
            if (!targetParent) return; 
        } 
        node.parent = targetParent ? targetParent.id : null; 
        let siblings = workingData.filter(h => h.parent === node.parent && h.id !== nodeId); 
        siblings.splice(lastPart - 1, 0, node); 
        const others = workingData.filter(h => h.parent !== node.parent); 
        workingData = [...others, ...siblings]; calculateProgress(); 
    }

    function getAddress(nId) { 
        const n = workingData.find(h => h.id === nId); 
        if (!n) return ""; 
        if (!n.parent) { const roots = workingData.filter(h => !h.parent); return (roots.indexOf(n) + 1).toString(); } 
        const sibs = workingData.filter(h => h.parent === n.parent); 
        const p = workingData.find(h => h.id === n.parent); 
        return `${getAddress(p.id)}.${sibs.indexOf(n) + 1}`; 
    }

    function handleDelete() { 
        if (openModalId || !activeParentId) return; 
        if (primedDeleteId === activeParentId) { 
            recordState(); 
            const toDel = new Set([activeParentId]); 
            let c = true; 
            while(c) { 
                c = false; 
                workingData.forEach(h => { if (toDel.has(h.parent) && !toDel.has(h.id)) { toDel.add(h.id); c = true; } }); 
            } 
            workingData = workingData.filter(h => !toDel.has(h.id)); 
            activeParentId = null; primedDeleteId = null; calculateProgress(); 
        } else { primedDeleteId = activeParentId; render(); } 
    }

    window.smartAddNode = function() { 
        if (openModalId) return; 
        recordState(); 
        const nId = "n-" + Date.now(); 
        const name = getUniqueName("[...]", activeParentId);
        if (activeParentId) { const p = workingData.find(h => h.id === activeParentId); p.expanded = true; } 
        workingData.push({ id: nId, parent: activeParentId, name: name, value: 0, expanded: true, note: "", noteOpen: false, inactive: false }); 
        editingId = nId; 
        calculateProgress();
    };

    window.toggleActiveNote = function() { if (openModalId || !activeParentId) return; const n = workingData.find(h => h.id === activeParentId); n.noteOpen = !n.noteOpen; render(); }
    
    function exportData() { 
        const d = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify({ledger, templates})); 
        const a = document.createElement('a'); 
        a.href = d; 
        a.download = `aldr_${VERSION}.json`; 
        document.body.appendChild(a);
        a.click(); 
        document.body.removeChild(a);
    }
</script>
</body>
</html>
