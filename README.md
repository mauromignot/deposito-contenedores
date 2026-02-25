<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Depósito de Contenedores</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Share+Tech+Mono&family=Barlow:wght@400;600;700&display=swap');

  :root {
    --bg: #1a1d1f;
    --surface: #22262a;
    --surface2: #2c3136;
    --border: #3a4048;
    --accent: #f5a623;
    --accent2: #4ecdc4;
    --danger: #e74c3c;
    --text: #d4dbe3;
    --text-dim: #6b7a8d;
    --c20: #2ecc71;
    --c40: #3498db;
    --cell-size: 48px;
    --grid-cols: 20;
    --grid-rows: 12;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Barlow', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* HEADER */
  header {
    background: var(--surface);
    border-bottom: 2px solid var(--accent);
    padding: 12px 24px;
    display: flex;
    align-items: center;
    gap: 20px;
    flex-wrap: wrap;
  }

  .logo {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 28px;
    letter-spacing: 3px;
    color: var(--accent);
    text-shadow: 0 0 20px rgba(245,166,35,0.3);
  }

  .logo span { color: var(--text-dim); font-size: 14px; display: block; font-family: 'Share Tech Mono', monospace; letter-spacing: 2px; margin-top: -4px; }

  .stats {
    display: flex;
    gap: 16px;
    margin-left: auto;
    flex-wrap: wrap;
  }

  .stat {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 6px 14px;
    font-family: 'Share Tech Mono', monospace;
    font-size: 13px;
  }

  .stat .val { color: var(--accent); font-size: 18px; display: block; text-align: center; }
  .stat .lbl { color: var(--text-dim); font-size: 10px; text-transform: uppercase; letter-spacing: 1px; }

  /* LAYOUT */
  .app {
    display: flex;
    height: calc(100vh - 70px);
  }

  /* SIDEBAR */
  .sidebar {
    width: 220px;
    min-width: 220px;
    background: var(--surface);
    border-right: 1px solid var(--border);
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 16px;
    overflow-y: auto;
  }

  .sidebar h3 {
    font-family: 'Share Tech Mono', monospace;
    font-size: 11px;
    color: var(--text-dim);
    letter-spacing: 2px;
    text-transform: uppercase;
    border-bottom: 1px solid var(--border);
    padding-bottom: 8px;
  }

  .container-card {
    border-radius: 8px;
    padding: 12px;
    cursor: grab;
    user-select: none;
    border: 2px solid transparent;
    transition: all 0.15s;
    position: relative;
    overflow: hidden;
  }

  .container-card:active { cursor: grabbing; transform: scale(0.97); }
  .container-card:hover { transform: translateY(-2px); box-shadow: 0 8px 20px rgba(0,0,0,0.4); }

  .container-card.c20 {
    background: linear-gradient(135deg, #1a4a2e, #0d2b1a);
    border-color: var(--c20);
  }

  .container-card.c40 {
    background: linear-gradient(135deg, #0d2b4a, #071a2e);
    border-color: var(--c40);
  }

  .container-card .size-badge {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 32px;
    letter-spacing: 2px;
    line-height: 1;
  }

  .c20 .size-badge { color: var(--c20); }
  .c40 .size-badge { color: var(--c40); }

  .container-card .desc {
    font-size: 11px;
    color: var(--text-dim);
    margin-top: 4px;
    font-family: 'Share Tech Mono', monospace;
  }

  .container-card .dims {
    font-size: 10px;
    color: var(--text-dim);
    margin-top: 6px;
    background: rgba(0,0,0,0.2);
    padding: 3px 6px;
    border-radius: 3px;
    font-family: 'Share Tech Mono', monospace;
  }

  .drag-hint {
    font-size: 10px;
    color: var(--text-dim);
    text-align: center;
    margin-top: 4px;
    opacity: 0.6;
  }

  /* Orientación */
  .orient-btns {
    display: flex;
    gap: 6px;
  }

  .orient-btn {
    flex: 1;
    padding: 7px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 5px;
    color: var(--text-dim);
    font-size: 11px;
    cursor: pointer;
    font-family: 'Share Tech Mono', monospace;
    transition: all 0.15s;
    text-align: center;
  }

  .orient-btn.active, .orient-btn:hover {
    background: var(--accent);
    color: #000;
    border-color: var(--accent);
  }

  /* Color picker */
  .color-row {
    display: flex;
    gap: 6px;
    flex-wrap: wrap;
  }

  .color-dot {
    width: 22px;
    height: 22px;
    border-radius: 50%;
    cursor: pointer;
    border: 2px solid transparent;
    transition: transform 0.15s;
  }

  .color-dot:hover, .color-dot.active { transform: scale(1.3); border-color: #fff; }

  /* Actions */
  .btn {
    width: 100%;
    padding: 9px;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-family: 'Barlow', sans-serif;
    font-weight: 700;
    font-size: 13px;
    letter-spacing: 1px;
    text-transform: uppercase;
    transition: all 0.15s;
  }

  .btn-danger {
    background: transparent;
    border: 1px solid var(--danger);
    color: var(--danger);
  }

  .btn-danger:hover { background: var(--danger); color: #fff; }

  .btn-accent {
    background: var(--accent);
    color: #000;
  }

  .btn-accent:hover { background: #e09400; }

  /* MAIN AREA */
  .main {
    flex: 1;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  .toolbar {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 8px 16px;
    display: flex;
    align-items: center;
    gap: 12px;
    font-size: 12px;
    color: var(--text-dim);
    font-family: 'Share Tech Mono', monospace;
  }

  .toolbar .mode-info {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .toolbar .dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--c20);
    animation: pulse 1.5s infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  .grid-wrap {
    flex: 1;
    overflow: auto;
    padding: 20px;
    background: var(--bg);
    background-image:
      radial-gradient(circle at 50% 50%, rgba(245,166,35,0.03) 0%, transparent 60%);
  }

  .yard-label {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 13px;
    letter-spacing: 3px;
    color: var(--text-dim);
    margin-bottom: 8px;
< truncated lines 292-773 >
  for (const c of containers) {
    if (c.id === excludeId) continue;
    const cs = containerSize(c.type, c.orient);
    if (col < c.col+cs.w && col+w > c.col && row < c.row+cs.h && row+h > c.row) return false;
  }
  return true;
}

function createPiece(container) {
  const cs = containerSize(container.type, container.orient);
  const el = document.createElement('div');
  el.className = 'container-piece';
  el.id = 'piece-'+container.id;
  el.style.left = (container.col * CELL) + 'px';
  el.style.top = (container.row * CELL) + 'px';
  el.style.width = (cs.w * CELL - 2) + 'px';
  el.style.height = (cs.h * CELL - 2) + 'px';
  
  const col = container.color || (container.type === '20' ? '#2ecc71' : '#3498db');
  el.style.background = hexToRgba(col, 0.18);
  el.style.borderColor = col;
  el.style.color = col;
  el.style.boxShadow = `inset 0 0 20px ${hexToRgba(col,0.08)}`;
  
  el.innerHTML = `<div class="piece-id">${container.label}</div><div class="piece-size">${container.type}'</div>`;
  el.draggable = true;
  el.dataset.id = container.id;

  el.addEventListener('click', (e) => { e.stopPropagation(); selectContainer(container.id); });
  el.addEventListener('dragstart', (e) => {
    dragType = 'move';
    dragContainer = container;
    selectedId = container.id;
    e.dataTransfer.effectAllowed = 'move';
    e.dataTransfer.setData('text/plain', container.id);
    setTimeout(() => { el.style.opacity = '0.3'; }, 0);
  });
  el.addEventListener('dragend', () => { el.style.opacity = '1'; clearHighlights(); });
  return el;
}

function hexToRgba(hex, alpha) {
  const r = parseInt(hex.slice(1,3),16), g = parseInt(hex.slice(3,5),16), b = parseInt(hex.slice(5,7),16);
  return `rgba(${r},${g},${b},${alpha})`;
}

function renderAll() {
  document.querySelectorAll('.container-piece').forEach(e => e.remove());
  for (const c of containers) grid.appendChild(createPiece(c));
  updateStats();
}

function selectContainer(id) {
  selectedId = id;
  document.querySelectorAll('.container-piece').forEach(el => el.classList.toggle('selected', el.dataset.id == id));
  const c = containers.find(x => x.id == id);
  if (c) {
    document.getElementById('infoSelected').textContent = `▶ ${c.label} — ${c.type}' — ${c.orient === 'H' ? 'Horizontal' : 'Vertical'}`;
  }
}

function clearHighlights() {
  document.querySelectorAll('.cell').forEach(c => c.classList.remove('highlight','invalid'));
}

let lastHighlightedCells = [];

function highlightCells(col, row, w, h, valid) {
  clearHighlights();
  lastHighlightedCells = [];
  for (let r = row; r < row+h && r < ROWS; r++) {
    for (let c = col; c < col+w && c < COLS; c++) {
      const cell = grid.querySelector(`[data-r="${r}"][data-c="${c}"]`);
      if (cell) {
        cell.classList.add(valid ? 'highlight' : 'invalid');
        lastHighlightedCells.push(cell);
      }
    }
  }
}

function onCellDragOver(e) {
  e.preventDefault();
  e.dataTransfer.dropEffect = 'move';
  const cell = e.currentTarget;
  const col = parseInt(cell.dataset.c);
  const row = parseInt(cell.dataset.r);
  
  const type = dragContainer ? dragContainer.type : dragType;
  const orient = dragContainer ? dragContainer.orient : orientation;
  if (!type) return;
  const cs = containerSize(type, orient);
  const valid = canPlace(col, row, cs.w, cs.h, dragContainer ? dragContainer.id : null);
  highlightCells(col, row, cs.w, cs.h, valid);
  document.getElementById('infoPos').textContent = `Col ${col+1} / Fila ${String.fromCharCode(65+row)}`;
}

function onCellDragLeave(e) {
  // only clear if leaving grid entirely
}

function onCellDrop(e) {
  e.preventDefault();
  const cell = e.currentTarget;
  const col = parseInt(cell.dataset.c);
  const row = parseInt(cell.dataset.r);
  clearHighlights();

  if (dragType === 'new') {
    // from sidebar
    const cs = containerSize(dragType === 'new' ? pendingType : '20', orientation);
    if (canPlace(col, row, cs.w, cs.h)) {
      addContainer(pendingType, col, row, orientation, selectedColor);
    }
  } else if (dragType === 'move' && dragContainer) {
    const cs = containerSize(dragContainer.type, dragContainer.orient);
    if (canPlace(col, row, cs.w, cs.h, dragContainer.id)) {
      dragContainer.col = col;
      dragContainer.row = row;
      renderAll();
    }
  }
  dragType = null;
  dragContainer = null;
}

function onCellClick(e) {
  selectedId = null;
  document.querySelectorAll('.container-piece').forEach(el => el.classList.remove('selected'));
  document.getElementById('infoSelected').textContent = '';
}

let pendingType = '20';

// Sidebar card drag
document.getElementById('card20').addEventListener('dragstart', (e) => {
  dragType = 'new'; dragContainer = null; pendingType = '20';
  e.dataTransfer.effectAllowed = 'copy';
});
document.getElementById('card40').addEventListener('dragstart', (e) => {
  dragType = 'new'; dragContainer = null; pendingType = '40';
  e.dataTransfer.effectAllowed = 'copy';
});

// Sidebar card click (add at first available spot)
document.getElementById('card20').addEventListener('click', () => quickAdd('20'));
document.getElementById('card40').addEventListener('click', () => quickAdd('40'));

function quickAdd(type) {
  const cs = containerSize(type, orientation);
  for (let r = 0; r < ROWS; r++) {
    for (let c = 0; c < COLS; c++) {
      if (canPlace(c, r, cs.w, cs.h)) {
        addContainer(type, c, r, orientation, selectedColor);
        return;
      }
    }
  }
}

function addContainer(type, col, row, orient, color) {
  counter++;
  const label = type === '20' ? `C${counter}` : `L${counter}`;
  containers.push({ id: counter, type, col, row, orient, color, label });
  renderAll();
}

function updateStats() {
  const t20 = containers.filter(c => c.type === '20').length;
  const t40 = containers.filter(c => c.type === '40').length;
  document.getElementById('stat-20').textContent = t20;
  document.getElementById('stat-40').textContent = t40;
  document.getElementById('stat-total').textContent = t20 + t40;
  document.getElementById('stat-teu').textContent = t20 + t40*2;
}

// Delete zone
const deleteZone = document.getElementById('deleteZone');
deleteZone.addEventListener('dragover', (e) => { e.preventDefault(); deleteZone.classList.add('drag-over'); });
deleteZone.addEventListener('dragleave', () => deleteZone.classList.remove('drag-over'));
deleteZone.addEventListener('drop', (e) => {
  e.preventDefault();
  deleteZone.classList.remove('drag-over');
  if (dragContainer) {
    containers = containers.filter(c => c.id !== dragContainer.id);
    dragContainer = null; dragType = null;
    selectedId = null;
    renderAll();
  }
});

deleteZone.addEventListener('click', () => {
  if (selectedId) {
    containers = containers.filter(c => c.id != selectedId);
    selectedId = null;
    document.getElementById('infoSelected').textContent = '';
    renderAll();
  }
});

// Keyboard delete
document.addEventListener('keydown', (e) => {
  if ((e.key === 'Delete' || e.key === 'Backspace') && selectedId && !['INPUT','SELECT','TEXTAREA'].includes(e.target.tagName)) {
    containers = containers.filter(c => c.id != selectedId);
    selectedId = null;
    document.getElementById('infoSelected').textContent = '';
    renderAll();
  }
  // Rotate with R key
  if (e.key === 'r' || e.key === 'R') {
    if (selectedId) {
      const c = containers.find(x => x.id == selectedId);
      if (c) {
        const newOrient = c.orient === 'H' ? 'V' : 'H';
        const cs = containerSize(c.type, newOrient);
        if (canPlace(c.col, c.row, cs.w, cs.h, c.id)) {
          c.orient = newOrient;
          renderAll();
          selectContainer(selectedId);
        }
      }
    }
  }
});

// Modal
function openModal() {
  document.getElementById('modalOverlay').classList.add('show');
  document.getElementById('modalLabel').value = '';
  document.getElementById('modalLabel').focus();
}

function closeModal() {
  document.getElementById('modalOverlay').classList.remove('show');
}

function addFromModal() {
  const type = document.getElementById('modalSize').value;
  const label = document.getElementById('modalLabel').value.trim() || null;
  const orient = document.getElementById('modalOrient').value;
  const cs = containerSize(type, orient);
  for (let r = 0; r < ROWS; r++) {
    for (let c = 0; c < COLS; c++) {
      if (canPlace(c, r, cs.w, cs.h)) {
        counter++;
        containers.push({ id: counter, type, col: c, row: r, orient, color: selectedColor, label: label || (type==='20'?`C${counter}`:`L${counter}`) });
        renderAll();
        closeModal();
        return;
      }
    }
  }
  alert('No hay espacio disponible en el patio.');
}

document.getElementById('modalOverlay').addEventListener('click', (e) => {
  if (e.target === e.currentTarget) closeModal();
});

document.getElementById('modalLabel').addEventListener('keydown', (e) => {
  if (e.key === 'Enter') addFromModal();
  if (e.key === 'Escape') closeModal();
});

function clearAll() {
  if (confirm('¿Eliminar todos los contenedores?')) {
    containers = [];
    selectedId = null;
    document.getElementById('infoSelected').textContent = '';
    renderAll();
  }
}

// Init
buildGrid();

// Add some demo containers
setTimeout(() => {
  addContainer('40', 0, 0, 'H', '#3498db');
  addContainer('40', 4, 0, 'H', '#3498db');
  addContainer('20', 8, 0, 'H', '#2ecc71');
  addContainer('20', 10, 0, 'H', '#2ecc71');
  addContainer('20', 12, 0, 'H', '#e74c3c');
  addContainer('40', 0, 1, 'H', '#9b59b6');
  addContainer('20', 4, 1, 'H', '#f5a623');
  addContainer('20', 6, 1, 'H', '#2ecc71');
  addContainer('40', 8, 2, 'H', '#1abc9c');
}, 100);
</script>
</body>
</html>
