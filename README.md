# Calculator-
This is all in one calculator 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Calculator - Final Layout</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500&display=swap" rel="stylesheet">
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <style>
        :root {
            --bg-dark: #1a1a1a; --bg-med: #2b2b2b; --bg-light: #3c3c3c;
            --text-primary: #f0f0f0; --text-secondary: #b0b0b0;
            --accent-blue: #007bff; --accent-orange: #ff9f0a; --accent-green: #28a745;
            --accent-grey: #6c757d; --accent-red: #dc3545;
            --selection-bg: rgba(0, 123, 255, 0.2);
        }
        * {
            box-sizing: border-box;
        }
        body {
            font-family: 'Poppins', sans-serif; background-color: var(--bg-dark); color: var(--text-primary);
            display: flex; flex-direction: column; align-items: center; justify-content: flex-start;
            min-height: 100vh; margin: 0; 
            /* MODIFICATION: Adjusted padding */
            padding: 20px 10px; 
            overflow-y: hidden;
        }
        body.history-active {
            overflow-y: auto;
        }
        .container {
            background-color: var(--bg-med); padding: 25px; border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.4); width: 100%; max-width: 420px;
            /* MODIFICATION: Removed top margin */
            margin: 0 auto 20px; 
        }
        .tabs {
            display: flex; background-color: var(--bg-light); border-radius: 10px;
            margin-bottom: 20px; overflow: hidden;
        }
        .tab-button {
            flex-grow: 1; padding: 12px 10px; cursor: pointer; border: none;
            background-color: transparent; 
            /* MODIFICATION: Reduced font size */
            font-size: 14px; 
            color: var(--text-secondary);
            transition: all 0.3s ease; font-family: 'Poppins', sans-serif;
        }
        .tab-button:hover { background-color: #4f4f4f; color: #ffffff; }
        .tab-button.active { background-color: var(--accent-blue); color: #ffffff; font-weight: 500; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .display {
            width: 100%; padding: 15px 12px; font-size: 44px;
            text-align: right; border: none; border-radius: 10px; margin-bottom: 20px;
            background-color: var(--bg-dark); color: var(--text-primary);
            box-shadow: inset 0 2px 5px rgba(0,0,0,0.3); 
            overflow-x: hidden; 
        }
        .calculator-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; }
        .calculator-grid button {
            padding: 16px 10px; font-size: 20px; border-radius: 12px; border: none;
            background-color: var(--bg-light); color: var(--text-primary); cursor: pointer;
            transition: all 0.2s ease; box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        }
        .calculator-grid button:active { transform: scale(0.95); box-shadow: 0 2px 4px rgba(0,0,0,0.2); }
        .calculator-grid .operator { background-color: var(--accent-orange); color: #fff; }
        .calculator-grid .function { background-color: var(--accent-grey); color: #fff; }
        .calculator-grid .equals { background-color: var(--accent-blue); color: #fff; }
        .form-group { margin-bottom: 15px; }
        .form-group label { display: block; margin-bottom: 6px; font-size: 15px; }
        .form-group input, .form-group select { padding: 10px; font-size: 15px; width: 100%; box-sizing: border-box; border-radius: 8px; border: 1px solid #4f4f4f; background-color: var(--bg-light); color: var(--text-primary); font-family: 'Poppins', sans-serif; }
        .result { margin-top: 15px; padding: 12px; font-size: 16px; border-radius: 8px; background-color: var(--bg-light); line-height: 1.6; min-height: 22px;}
        .history-container { margin-top: 25px; border-top: 1px solid #4f4f4f; padding-top: 15px; }
        .history-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
        .history-actions button {
            background-color: var(--accent-grey); color: white; border: none; border-radius: 8px;
            display: flex; align-items: center; justify-content: center; cursor: pointer;
            transition: all 0.3s ease;
        }
        #normal-actions button { width: 38px; height: 38px; }
        #normal-actions button:hover { background-color: #5a6268; transform: translateY(-2px); }
        #normal-actions svg { width: 18px; height: 18px; fill: white; }
        #select-actions button { font-size: 13px; padding: 8px 12px; }
        .history-actions .btn-danger { background-color: var(--accent-red); }
        .history-actions .btn-danger:hover { background-color: #c82333; }
        .history-actions .btn-confirm { background-color: var(--accent-blue); }
        .history-actions .btn-confirm:hover { background-color: #0056b3; }
        #select-actions { display: none; gap: 8px; }
        .history-container.selecting #normal-actions { display: none; }
        .history-container.selecting #select-actions { display: flex; }
        #history-list {
            min-height: 50px; max-height: 200px; overflow-y: auto; background-color: var(--bg-dark);
            border-radius: 8px; padding: 5px; border: 1px solid #4f4f4f;
        }
        .history-item {
            display: flex; align-items: center; padding: 8px; font-size: 14px;
            border-bottom: 1px solid var(--bg-light); cursor: default;
            transition: background-color 0.2s;
        }
        .history-container.selecting .history-item { cursor: pointer; }
        .history-item:last-child { border-bottom: none; }
        .history-item.selected { background-color: var(--selection-bg); }
        .history-item .checkbox {
            display: none; width: 18px; height: 18px; margin-right: 12px;
            border: 2px solid var(--accent-grey); border-radius: 4px;
            transition: all 0.2s;
        }
        .history-container.selecting .history-item .checkbox { display: block; }
        .history-item.selected .checkbox { background-color: var(--accent-blue); border-color: var(--accent-blue); }
        .history-item span { flex-grow: 1; word-break: break-all; }
        .history-item .controls { display: flex; gap: 8px; }
        .history-container.selecting .controls { display: none; }
        .history-item .controls button {
            width: 28px; height: 28px; background: #4f4f4f; border: none; border-radius: 50%;
            display: flex; align-items: center; justify-content: center; cursor: pointer; transition: background-color .2s;
        }
        .history-item .controls button:hover { background-color: #5a6268; }
        .history-item .controls button svg { width: 13px; height: 13px; fill: white; }
        .gst-buttons { display: flex; gap: 10px; margin-bottom: 15px; }
        .btn-gst {
            flex: 1; padding: 12px 5px; font-size: 15px; font-weight: 500; border: none;
            border-radius: 8px; cursor: pointer; transition: all 0.2s ease; color: white;
        }
        .btn-gst.add { background-color: var(--accent-green); }
        .btn-gst.add:hover { background-color: #218838; }
        .btn-gst.subtract { background-color: var(--accent-orange); }
        .btn-gst.subtract:hover { background-color: #e08e0b; }
        .btn-primary {
            width: 100%; padding: 12px; font-size: 18px; font-weight: 500;
            background-color: var(--accent-blue); color: white; border: none;
            border-radius: 8px; cursor: pointer; transition: background-color 0.2s ease;
        }
        .btn-primary:hover { background-color: #0069d9; }
        .conversion-ui { display: flex; align-items: flex-end; justify-content: space-between; gap: 10px; }
        .conversion-ui .form-group { flex: 1; margin-bottom: 0; }
        .conversion-ui .equals-sign { font-size: 24px; font-weight: 500; padding-bottom: 10px; }
        .dimension-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
    </style>
</head>
<body>

    <div class="container">
        <!-- Tabs -->
        <div class="tabs">
            <button class="tab-button active" onclick="openTab(event, 'standard')">Standard</button>
            <button class="tab-button" onclick="openTab(event, 'gst')">GST</button>
            <button class="tab-button" onclick="openTab(event, 'measurement')">Measurement</button>
            <button class="tab-button" onclick="openTab(event, 'sip')">SIP</button>
        </div>

        <!-- Calculators -->
        <div id="standard" class="tab-content active">
            <input type="text" id="std-display" class="display" readonly>
            <div class="calculator-grid">
                <button class="function" onclick="memoryControl()">MRC</button><button class="function" onclick="memorySubtract()">M-</button><button class="function" onclick="memoryAdd()">M+</button><button class="operator" onclick="stdAppend('/')">÷</button>
                <button class="function" onclick="stdClear()">C</button><button class="function" onclick="stdBackspace()">←</button><button class="function" onclick="stdAppend('%')">%</button><button class="operator" onclick="stdAppend('*')">x</button>
                <button onclick="stdAppend('7')">7</button><button onclick="stdAppend('8')">8</button><button onclick="stdAppend('9')">9</button><button class="operator" onclick="stdAppend('-')">-</button>
                <button onclick="stdAppend('4')">4</button><button onclick="stdAppend('5')">5</button><button onclick="stdAppend('6')">6</button><button class="operator" onclick="stdAppend('+')" style="grid-row: span 2;">+</button>
                <button onclick="stdAppend('1')">1</button><button onclick="stdAppend('2')">2</button><button onclick="stdAppend('3')">3</button>
                <button onclick="stdAppend('0')">0</button><button onclick="stdAppend('00')">00</button><button onclick="stdAppend('.')">.</button><button class="equals" onclick="stdCalculate()">=</button>
            </div>
        </div>
        <div id="gst" class="tab-content">
            <div class="form-group"><label for="gst-amount">Amount</label><input type="number" id="gst-amount" placeholder="Enter amount"></div>
            <div class="form-group"><label for="gst-rate">GST Rate (%)</label><input type="number" id="gst-rate" placeholder="Enter GST rate"></div>
            <div class="gst-buttons">
                <button class="btn-gst add" onclick="calculateGST('add')">Add GST</button>
                <button class="btn-gst subtract" onclick="calculateGST('subtract')">Subtract GST</button>
            </div>
            <div id="gst-result" class="result"></div>
        </div>
        
        <div id="measurement" class="tab-content">
            <div class="form-group">
                <label for="measurement-mode-select">Calculator Type</label>
                <select id="measurement-mode-select">
                    <option value="conversion">Unit Conversion</option>
                    <option value="dimension">Find Missing Dimension</option>
                    <option value="calculate_area">Calculate Area</option>
                </select>
            </div>
            
            <div id="unit-conversion-section">
                <div class="form-group">
                    <label for="measurement-type">Type of Measurement</label>
                    <select id="measurement-type">
                        <option value="length">Length</option>
                        <option value="weight">Weight</option>
                        <option value="temperature">Temperature</option>
                        <option value="area">Area (Land)</option>
                    </select>
                </div>
                <div class="conversion-ui">
                    <div class="form-group"><label>From</label><input type="number" id="measurement-from-value" placeholder="Enter value"></div>
                    <div class="equals-sign">→</div>
                    <div class="form-group"><label>To</label><input type="text" id="measurement-to-value" readonly></div>
                </div>
                <div class="conversion-ui" style="margin-top: -10px;">
                     <div class="form-group"><select id="measurement-from-unit"></select></div>
                     <div class="equals-sign" style="opacity:0;">→</div>
                    <div class="form-group"><select id="measurement-to-unit"></select></div>
                </div>
                <button class="btn-primary" onclick="calculateMeasurement()">Calculate</button>
                <div id="measurement-result" class="result"></div>
            </div>

            <div id="find-dimension-section" style="display:none;">
                <div class="form-group"><label for="area-total">Total Area</label><input type="number" id="area-total" placeholder="e.g., 1000"></div>
                <div class="form-group" style="margin-top: -10px;"><select id="area-unit"></select></div>
                <div class="dimension-grid">
                    <div class="form-group"><label for="known-dimension">Known Side (Length/Width)</label><input type="number" id="known-dimension" placeholder="e.g., 20"></div>
                    <div class="form-group"><label for="result-dimension">Missing Side</label><input type="text" id="result-dimension" readonly></div>
                    <div class="form-group" style="margin-top: -10px;"><select id="known-unit"></select></div>
                    <div class="form-group" style="margin-top: -10px;"><select id="result-unit"></select></div>
                </div>
                <button class="btn-primary" onclick="calculateMissingDimension()">Find Missing Side</button>
                <div id="dimension-result" class="result"></div>
            </div>

            <div id="calculate-area-section" style="display:none;">
                <div class="dimension-grid">
                    <div class="form-group"><label for="area-length">Length</label><input type="number" id="area-length" placeholder="e.g., 50"></div>
                    <div class="form-group"><label for="area-width">Width</label><input type="number" id="area-width" placeholder="e.g., 20"></div>
                    <div class="form-group" style="margin-top: -10px;"><select id="area-length-unit"></select></div>
                    <div class="form-group" style="margin-top: -10px;"><select id="area-width-unit"></select></div>
                </div>
                <div class="form-group"><label for="area-result-unit">Result In</label><select id="area-result-unit"></select></div>
                <button class="btn-primary" onclick="calculateArea()">Calculate Area</button>
                <div id="area-result" class="result"></div>
            </div>
        </div>

        <div id="sip" class="tab-content">
            <div class="form-group"><label for="sip-amount">Monthly Investment (₹)</label><input type="number" id="sip-amount" placeholder="e.g., 5000"></div>
            <div class="form-group"><label for="sip-rate">Expected Annual Return (%)</label><input type="number" id="sip-rate" placeholder="e.g., 12"></div>
            <div class="form-group"><label for="sip-years">Time Period (Years)</label><input type="number" id="sip-years" placeholder="e.g., 10"></div>
            <button class="btn-primary" onclick="calculateSIP()">Calculate</button>
            <div id="sip-result" class="result"></div>
        </div>

        <button class="btn-primary" onclick="toggleHistory()" style="margin-top: 20px;">View History</button>
        
        <div id="history-section" class="history-container" style="display: none;">
            <div class="history-header">
                <h2 id="history-title">History</h2>
                <div class="history-actions">
                    <div id="normal-actions" style="display: flex; gap: 8px;">
                        <button onclick="startSelection('save')" title="Download Selected"><svg viewBox="0 0 24 24"><path d="M19,9H5c-1.1,0-2,0.9-2,2v7c0,1.1,0.9,2,2,2h14c1.1,0,2-0.9,2-2v-7C21,9.9,20.1,9,19,9z M19,18H5v-5h14V18z M12,14.5l-4-4h2.5V8h3v2.5H16L12,14.5z"/></svg></button>
                        <button onclick="startSelection('send')" title="Send Selected"><svg viewBox="0 0 24 24"><path d="M18,16.08c-0.76,0-1.44,0.3-1.96,0.77L8.91,12.7c0.05-0.23,0.09-0.46,0.09-0.7s-0.04-0.47-0.09-0.7l7.05-4.11C16.5,7.79,17.21,8.08,18,8.08c1.66,0,3-1.34,3-3s-1.34-3-3-3s-3,1.34-3,3c0,0.24,0.04,0.47,0.09,0.7L8.04,9.81C7.5,9.31,6.79,9,6,9c-1.66,0-3,1.34-3,3s1.34,3,3,3c0.79,0,1.5-0.31,2.04-0.81l7.12,4.16c-0.05,0.21-0.08,0.43-0.08,0.65c0,1.66,1.34,3,3,3s3-1.34,3-3S19.66,16.08,18,16.08z"/></svg></button>
                        <button onclick="startSelection('print')" title="Print Selected"><svg viewBox="0 0 24 24"><path d="M19 8H5c-1.66 0-3 1.34-3 3v6h4v4h12v-4h4v-6c0-1.66-1.34-3-3-3zm-3 11H8v-5h8v5zm3-7c-.55 0-1-.45-1-1s.45-1 1-1 1 .45 1 1-.45 1-1 1zm-1-9H6v4h12V3z"/></svg></button>
                        <button onclick="clearHistory()" class="btn-danger" title="Clear All History"><svg viewBox="0 0 24 24"><path d="M6,19c0,1.1,0.9,2,2,2h8c1.1,0,2-0.9,2-2V7H6V19z M19,4h-3.5l-1-1h-5l-1-1H5v2h14V4z"/></svg></button>
                    </div>
                    <div id="select-actions">
                        <button onclick="selectAll()">Select All</button>
                        <button class="btn-confirm" onclick="confirmSelection()">Confirm</button>
                        <button class="btn-danger" onclick="cancelSelection()">Cancel</button>
                    </div>
                </div>
            </div>
            <div id="history-list"></div>
        </div>
    </div>
    
    <script>
        const { jsPDF } = window.jspdf;
        // CORRECTED CODE: Ensure all history categories exist when loading from localStorage
        const defaultHistory = { standard: [], gst: [], sip: [], measurement: [] };
        const savedHistory = JSON.parse(localStorage.getItem('calcHistory_v3')) || {};
        let history = { ...defaultHistory, ...savedHistory };
        
        let currentTab = 'standard';
        let isSelectionMode = false;
        let currentSelectionAction = '';
        let selectedIndices = new Set();

        const svg_icon_edit = `<svg viewBox="0 0 24 24"><path d="M3,17.25V21h3.75L17.81,9.94l-3.75-3.75L3,17.25z M20.71,7.04c0.39-0.39,0.39-1.02,0-1.41l-2.34-2.34c-0.39-0.39-1.02-0.39-1.41,0l-1.83,1.83l3.75,3.75L20.71,7.04z"/></svg>`;
        const svg_icon_delete = `<svg viewBox="0 0 24 24"><path d="M6,19c0,1.1,0.9,2,2,2h8c1.1,0,2-0.9,2-2V7H6V19z M19,4h-3.5l-1-1h-5l-1-1H5v2h14V4z"/></svg>`;
        let memoryValue = 0;
        let mrcPressedLast = false;
        
        let stdDisplay;
        let currentOperand = '';
        let previousOperand = '';
        let currentOperation = null;
        let displayNeedsReset = false;
        
        let measurementType, fromValueInput, fromUnitSelect, toValueInput, toUnitSelect;
        
        const units = {
            length: { 'Meters (m)': 1, 'Kilometers (km)': 1000, 'Centimeters (cm)': 0.01, 'Millimeters (mm)': 0.001, 'Miles (mi)': 1609.34, 'Yards (yd)': 0.9144, 'Feet (ft)': 0.3048, 'Inches (in)': 0.0254, },
            weight: { 'Grams (g)': 1, 'Kilograms (kg)': 1000, 'Milligrams (mg)': 0.001, 'Pounds (lb)': 453.592, 'Ounces (oz)': 28.3495, },
            temperature: { 'Celsius (°C)': 'celsius', 'Fahrenheit (°F)': 'fahrenheit', 'Kelvin (K)': 'kelvin' },
            area: { 'Square Meters (m²)': 1, 'Square Feet (ft²)': 0.092903,'Square Yards (Gaj)': 0.836127, 'Acres': 4046.86, 'Hectares': 10000, 'Bigha (Standard)': 2529.29, 'Dismil': 40.4686, }
        };

        document.addEventListener('DOMContentLoaded', () => {
            stdDisplay = document.getElementById("std-display");
            stdClear(); 
            
            measurementType = document.getElementById('measurement-type');
            fromValueInput = document.getElementById('measurement-from-value');
            fromUnitSelect = document.getElementById('measurement-from-unit');
            toValueInput = document.getElementById('measurement-to-value');
            toUnitSelect = document.getElementById('measurement-to-unit');
            
            document.getElementById('measurement-mode-select').addEventListener('change', toggleMeasurementMode);
            measurementType.addEventListener('change', populateUnits);

            populateUnits();
            toggleMeasurementMode();

            openTab(null, 'standard');
            document.getElementById('history-list').addEventListener('click', handleHistoryItemClick);
        });

        function openTab(event, tabName) { cancelSelection(); currentTab = tabName; document.querySelectorAll(".tab-content").forEach(tc => tc.style.display = "none"); document.querySelectorAll(".tab-button").forEach(tb => tb.classList.remove("active")); document.getElementById(tabName).style.display = "block"; const activeButton = event ? event.currentTarget : document.querySelector(`.tab-button[onclick*="'${tabName}'"]`); activeButton.classList.add("active"); renderHistory(); }
        function updateHistory() { localStorage.setItem('calcHistory_v3', JSON.stringify(history)); renderHistory(); }
        function addToHistory(calculation, type) { if(history[type][0] === calculation) return; history[type].unshift(calculation); if (history[type].length > 50) history[type].pop(); updateHistory(); }
        function renderHistory() { const historyContainer = document.getElementById('history-section'); const historyList = document.getElementById('history-list'); const historyTitle = document.getElementById('history-title'); historyList.innerHTML = ''; const historyToRender = history[currentTab] || []; historyTitle.textContent = `${currentTab.charAt(0).toUpperCase() + currentTab.slice(1)} History`; historyContainer.classList.toggle('selecting', isSelectionMode); if (historyToRender.length === 0) { historyList.innerHTML = `<div style="text-align:center; padding: 20px; color: var(--text-secondary);">No history yet.</div>`; return; } historyToRender.forEach((item, index) => { const isSelected = selectedIndices.has(index); const historyItem = document.createElement('div'); historyItem.className = `history-item ${isSelected ? 'selected' : ''}`; historyItem.dataset.index = index; historyItem.innerHTML = ` <div class="checkbox"></div> <span>${item}</span> <div class="controls"> <button onclick="editHistoryItem(event, ${index})">${svg_icon_edit}</button> <button onclick="deleteHistoryItem(event, ${index})">${svg_icon_delete}</button> </div>`; historyList.appendChild(historyItem); }); }
        function startSelection(action) { if (history[currentTab].length === 0) { alert("History is empty."); return; } isSelectionMode = true; currentSelectionAction = action; renderHistory(); }
        function cancelSelection() { isSelectionMode = false; currentSelectionAction = ''; selectedIndices.clear(); renderHistory(); }
        function handleHistoryItemClick(event) { if (!isSelectionMode) return; const item = event.target.closest('.history-item'); if (!item) return; const index = parseInt(item.dataset.index, 10); if (selectedIndices.has(index)) selectedIndices.delete(index); else selectedIndices.add(index); item.classList.toggle('selected'); }
        function selectAll() { const allIndicesSelected = selectedIndices.size === history[currentTab].length; history[currentTab].forEach((_, index) => { if (allIndicesSelected) selectedIndices.delete(index); else selectedIndices.add(index); }); renderHistory(); }
        async function confirmSelection() { if (selectedIndices.size === 0) { alert("Please select at least one item."); return; } const selectedItems = Array.from(selectedIndices).sort((a,b) => a-b).map(index => history[currentTab][index]); switch (currentSelectionAction) { case 'print': _printHistory(selectedItems); break; case 'save': await _generateAndSavePDF(selectedItems); break; case 'send': await _generateAndSendPDF(selectedItems); break; } cancelSelection(); }
        function editHistoryItem(event, index) { event.stopPropagation(); const originalText = history[currentTab][index]; const newText = prompt("Edit history entry:", originalText); if (newText !== null && newText.trim() !== '') { history[currentTab][index] = newText; updateHistory(); } }
        function deleteHistoryItem(event, index) { event.stopPropagation(); if (confirm("Are you sure you want to delete this entry?")) { history[currentTab].splice(index, 1); updateHistory(); } }
        function clearHistory() { if (confirm(`Are you sure you want to clear the entire ${currentTab} history?`)) { history[currentTab] = []; updateHistory(); } }
        function _printHistory(items) { const printContent = ` <!DOCTYPE html><html><head><title>Print History</title> <style> body { font-family: 'Poppins', sans-serif; margin: 25px; } h1 { text-align: center; border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px; } div { padding: 10px 5px; border-bottom: 1px dotted #888; font-size: 16px; } div:last-child { border-bottom: none; } </style></head><body> <h1>Calculation History</h1> ${items.map(item => `<div>${item}</div>`).join('')} </body></html>`; const iframe = document.createElement('iframe'); iframe.style.display = 'none'; document.body.appendChild(iframe); const doc = iframe.contentDocument || iframe.contentWindow.document; doc.write(printContent); doc.close(); iframe.contentWindow.focus(); iframe.contentWindow.print(); setTimeout(() => { document.body.removeChild(iframe); }, 500); }
        async function _generatePDFBlob(items){ let html = items.map(item => `<div style="padding: 5px; border-bottom: 1px solid #444;">${item}</div>`).join(''); const temp = document.createElement('div'); Object.assign(temp.style, { position: 'absolute', left: '-9999px', background: '#2b2b2b', color: '#f0f0f0', padding: '20px', fontFamily: 'Poppins, sans-serif', width: '400px', fontSize: '14px' }); temp.innerHTML = html; document.body.appendChild(temp); const canvas = await html2canvas(temp, { scale: 2 }); document.body.removeChild(temp); const imgData = canvas.toDataURL('image/png'); const pdf = new jsPDF('p', 'mm', 'a4'); const pdfW = pdf.internal.pageSize.getWidth(), pdfH = (canvas.height * pdfW) / canvas.width; pdf.addImage(imgData, 'PNG', 0, 0, pdfW, pdfH); return pdf.output('blob'); }
        async function _generateAndSavePDF(items) { const blob = await _generatePDFBlob(items); if (blob) { const url = URL.createObjectURL(blob); const a = document.createElement("a"); a.href = url; a.download = `calculator-history.pdf`; a.click(); URL.revokeObjectURL(url); } }
        async function _generateAndSendPDF(items) { const blob = await _generatePDFBlob(items); if (!blob) return; const file = new File([blob], `calculator-history.pdf`, { type: "application/pdf" }); if (navigator.canShare && navigator.canShare({ files: [file] })) { await navigator.share({ title: 'Calculator History', text: 'Here is the calculation history.', files: [file] }); } else { alert("Direct sharing not supported. PDF will download instead."); _generateAndSavePDF(items); } }

        function stdClear() { mrcPressedLast = false; currentOperand = '0'; previousOperand = ''; currentOperation = null; displayNeedsReset = true; stdDisplay.value = "0"; }
        function stdBackspace() { mrcPressedLast = false; if (displayNeedsReset) return; currentOperand = currentOperand.toString().slice(0, -1); if (currentOperand === '' || currentOperand === '-') { currentOperand = '0'; displayNeedsReset = true; } stdDisplay.value = currentOperand; }
        function stdAppend(value) { mrcPressedLast = false; if (stdDisplay.value === "Error") stdClear(); const isOperator = ['+', '-', '*', '/'].includes(value); if (!isNaN(value) || value === '.' || value === '00') { if (value === '.' && currentOperand.includes('.')) return; if (displayNeedsReset) { currentOperand = value === '.' ? '0.' : value; displayNeedsReset = false; } else { currentOperand = currentOperand.toString() + value.toString(); } stdDisplay.value = currentOperand; } else if (isOperator) { if (currentOperand === '' && previousOperand === '') return; if (currentOperand === '' && !displayNeedsReset) return; if (displayNeedsReset && currentOperand === previousOperand) { currentOperation = value; return; } if (currentOperation !== null) { stdCalculate(true); } currentOperation = value; previousOperand = currentOperand; displayNeedsReset = true; } else if (value === '%') { if (currentOperand === '') return; let result; const currentVal = parseFloat(currentOperand); if (previousOperand !== '' && currentOperation) { const prevVal = parseFloat(previousOperand); let historyStr = `${previousOperand} ${currentOperation} ${currentOperand}%`; if (currentOperation === '+' || currentOperation === '-') { const percentageOfPrevious = prevVal * (currentVal / 100); result = (currentOperation === '+') ? prevVal + percentageOfPrevious : prevVal - percentageOfPrevious; } else if (currentOperation === '*' || currentOperation === '/') { const percentageValue = currentVal / 100; result = (currentOperation === '*') ? prevVal * percentageValue : (currentVal === 0 ? "Error" : prevVal / percentageValue); } else { return; } if(result === "Error") { stdDisplay.value = "Error"; stdClear(); return; } const formattedResult = parseFloat(result.toPrecision(14)); addToHistory(`${historyStr} = ${formattedResult}`, 'standard'); currentOperand = formattedResult.toString(); stdDisplay.value = currentOperand; previousOperand = ''; currentOperation = null; displayNeedsReset = true; } else { result = currentVal / 100; const formattedResult = parseFloat(result.toPrecision(14)); currentOperand = formattedResult.toString(); stdDisplay.value = currentOperand; } } }
        function stdCalculate(isIntermediate = false) { mrcPressedLast = false; if (currentOperation == null || previousOperand === '') return; let computation; const prev = parseFloat(previousOperand); const current = parseFloat(currentOperand || previousOperand); if (isNaN(prev) || isNaN(current)) return; switch (currentOperation) { case '+': computation = prev + current; break; case '-': computation = prev - current; break; case '*': computation = prev * current; break; case '/': if (current === 0) { stdDisplay.value = "Error"; stdClear(); return; } computation = prev / current; break; default: return; } if (!isFinite(computation)) { stdDisplay.value = "Error"; stdClear(); return; } const formattedResult = parseFloat(computation.toPrecision(14)); if (!isIntermediate) { addToHistory(`${prev} ${currentOperation} ${current} = ${formattedResult}`, 'standard'); } currentOperand = formattedResult.toString(); stdDisplay.value = currentOperand; previousOperand = isIntermediate ? formattedResult.toString() : ''; currentOperation = isIntermediate ? currentOperation : null; displayNeedsReset = true; }
        function memoryAdd() { const val = parseFloat(stdDisplay.value); if (!isNaN(val)) memoryValue += val; mrcPressedLast = false; }
        function memorySubtract() { const val = parseFloat(stdDisplay.value); if (!isNaN(val)) memoryValue -= val; mrcPressedLast = false; }
        function memoryControl() { if (mrcPressedLast) { memoryValue = 0; mrcPressedLast = false; } else { stdDisplay.value = memoryValue; currentOperand = memoryValue.toString(); displayNeedsReset = false; mrcPressedLast = true; } }
        function calculateGST(type){ const amount = parseFloat(document.getElementById("gst-amount").value), rate = parseFloat(document.getElementById("gst-rate").value); const resultDiv = document.getElementById("gst-result"); if (isNaN(amount) || isNaN(rate)) { resultDiv.innerText = "Invalid input."; return; } let resultHTML = "", historyLog = ""; if (type === 'add') { const gst = amount * (rate / 100), total = amount + gst; resultHTML = `Base: ₹${amount.toFixed(2)}<br>GST: ₹${gst.toFixed(2)}<br>Total: ₹${total.toFixed(2)}`; historyLog = `Add GST: ${amount.toFixed(2)} @ ${rate}% = Total ${total.toFixed(2)}`; } else { const base = amount / (1 + rate / 100), gst = amount - base; resultHTML = `Gross: ₹${amount.toFixed(2)}<br>Base: ₹${base.toFixed(2)}<br>GST: ₹${gst.toFixed(2)}`; historyLog = `Sub GST from: ${amount.toFixed(2)} @ ${rate}% = Base ${base.toFixed(2)}`; } resultDiv.innerHTML = resultHTML; addToHistory(historyLog, 'gst'); }
        function calculateSIP(){ const p = parseFloat(document.getElementById("sip-amount").value), r = parseFloat(document.getElementById("sip-rate").value), t = parseFloat(document.getElementById("sip-years").value); const resultDiv = document.getElementById("sip-result"); if (isNaN(p) || isNaN(r) || isNaN(t) || p <= 0 || r < 0 || t <= 0) { resultDiv.innerText = "Invalid positive numbers required."; return; } const n = 12, i = r / 100 / n, num = t * n, M = p * ((Math.pow(1 + i, num) - 1) / i) * (1 + i); const I = p * num, W = M - I, fmt = { maximumFractionDigits: 0 }; resultDiv.innerHTML = `Invested: ₹${I.toLocaleString("en-IN", fmt)}<br>Gained: ₹${W.toLocaleString("en-IN", fmt)}<br>Maturity: ₹${M.toLocaleString("en-IN", fmt)}`; addToHistory(`SIP: ₹${p}/mo @ ${r}% for ${t} yrs = Val ₹${M.toLocaleString("en-IN", fmt)}`, 'sip'); }
        
        function toggleHistory() {
            const historySection = document.getElementById('history-section');
            const body = document.body;
            if (historySection.style.display === 'none' || historySection.style.display === '') {
                historySection.style.display = 'block';
                body.classList.add('history-active'); 
            } else {
                historySection.style.display = 'none';
                body.classList.remove('history-active'); 
                cancelSelection(); 
            }
        }

        function toggleMeasurementMode() {
            const mode = document.getElementById('measurement-mode-select').value;
            document.getElementById('unit-conversion-section').style.display = (mode === 'conversion') ? 'block' : 'none';
            document.getElementById('find-dimension-section').style.display = (mode === 'dimension') ? 'block' : 'none';
            document.getElementById('calculate-area-section').style.display = (mode === 'calculate_area') ? 'block' : 'none';
        }

        function populateUnits() {
            const selectedType = measurementType.value;
            const unitOptions = Object.keys(units[selectedType]);
            fromUnitSelect.innerHTML = ''; toUnitSelect.innerHTML = '';
            unitOptions.forEach(unit => { fromUnitSelect.innerHTML += `<option value="${unit}">${unit}</option>`; toUnitSelect.innerHTML += `<option value="${unit}">${unit}</option>`; });
            if (selectedType === 'length') { fromUnitSelect.value = 'Meters (m)'; toUnitSelect.value = 'Feet (ft)'; }
            else if (selectedType === 'weight') { fromUnitSelect.value = 'Kilograms (kg)'; toUnitSelect.value = 'Pounds (lb)'; }
            else if (selectedType === 'area') { fromUnitSelect.value = 'Square Feet (ft²)'; toUnitSelect.value = 'Dismil'; }
            else { fromUnitSelect.value = 'Celsius (°C)'; toUnitSelect.value = 'Fahrenheit (°F)'; }
            
            const areaUnitSelect = document.getElementById('area-unit');
            const knownUnitSelect = document.getElementById('known-unit');
            const resultUnitSelect = document.getElementById('result-unit');
            const areaLengthUnit = document.getElementById('area-length-unit');
            const areaWidthUnit = document.getElementById('area-width-unit');
            const areaResultUnit = document.getElementById('area-result-unit');
            areaUnitSelect.innerHTML = ''; knownUnitSelect.innerHTML = ''; resultUnitSelect.innerHTML = '';
            areaLengthUnit.innerHTML = ''; areaWidthUnit.innerHTML = ''; areaResultUnit.innerHTML = '';
            Object.keys(units.area).forEach(unit => { areaUnitSelect.innerHTML += `<option value="${unit}">${unit}</option>`; areaResultUnit.innerHTML += `<option value="${unit}">${unit}</option>`; });
            Object.keys(units.length).forEach(unit => { knownUnitSelect.innerHTML += `<option value="${unit}">${unit}</option>`; resultUnitSelect.innerHTML += `<option value="${unit}">${unit}</option>`; areaLengthUnit.innerHTML += `<option value="${unit}">${unit}</option>`; areaWidthUnit.innerHTML += `<option value="${unit}">${unit}</option>`; });
            areaUnitSelect.value = 'Square Feet (ft²)'; knownUnitSelect.value = 'Feet (ft)'; resultUnitSelect.value = 'Feet (ft)';
            areaLengthUnit.value = 'Feet (ft)'; areaWidthUnit.value = 'Feet (ft)'; areaResultUnit.value = 'Square Feet (ft²)';
        }

        function calculateMeasurement() {
            const fromValue = parseFloat(fromValueInput.value);
            const fromUnit = fromUnitSelect.value;
            const toUnit = toUnitSelect.value;
            const type = measurementType.value;
            const resultDiv = document.getElementById('measurement-result');
            
            if (isNaN(fromValue)) { resultDiv.innerText = "Invalid input."; return; }

            let result;
            if (type === 'temperature') {
                let tempInC;
                if (fromUnit === 'Celsius (°C)') tempInC = fromValue;
                else if (fromUnit === 'Fahrenheit (°F)') tempInC = (fromValue - 32) * 5 / 9;
                else if (fromUnit === 'Kelvin (K)') tempInC = fromValue - 273.15;
                if (toUnit === 'Celsius (°C)') result = tempInC;
                else if (toUnit === 'Fahrenheit (°F)') result = (tempInC * 9 / 5) + 32;
                else if (toUnit === 'Kelvin (K)') result = tempInC + 273.15;
            } else {
                const valueInBase = fromValue * units[type][fromUnit];
                result = valueInBase / units[type][toUnit];
            }
            
            const finalResult = parseFloat(result.toFixed(5));
            toValueInput.value = finalResult;
            const historyLog = `${fromValue} ${fromUnit} = ${finalResult} ${toUnit}`;
            resultDiv.innerText = historyLog;
            addToHistory(historyLog, 'measurement');
        }
        
        function calculateMissingDimension() {
            const totalArea = parseFloat(document.getElementById('area-total').value);
            const areaUnit = document.getElementById('area-unit').value;
            const knownDim = parseFloat(document.getElementById('known-dimension').value);
            const knownUnit = document.getElementById('known-unit').value;
            const resultUnit = document.getElementById('result-unit').value;
            const resultInput = document.getElementById('result-dimension');
            const resultDiv = document.getElementById('dimension-result');

            if (isNaN(totalArea) || isNaN(knownDim)) { resultDiv.innerText = "Please fill all fields."; return; }
            if (knownDim <= 0) { resultDiv.innerText = "Known side must be positive."; return; }

            const totalAreaInM2 = totalArea * units.area[areaUnit];
            const knownDimInM = knownDim * units.length[knownUnit];
            
            const missingDimInM = totalAreaInM2 / knownDimInM;
            const finalResult = missingDimInM / units.length[resultUnit];
            
            const formattedResult = parseFloat(finalResult.toFixed(4));
            resultInput.value = formattedResult;
            
            const historyLog = `Area: ${totalArea} ${areaUnit}, Side: ${knownDim} ${knownUnit} = Missing Side: ${formattedResult} ${resultUnit}`;
            resultDiv.innerText = `The missing side is ${formattedResult} ${resultUnit}.`;
            addToHistory(historyLog, 'measurement');
        }

        function calculateArea() {
            const length = parseFloat(document.getElementById('area-length').value);
            const width = parseFloat(document.getElementById('area-width').value);
            const lengthUnit = document.getElementById('area-length-unit').value;
            const widthUnit = document.getElementById('area-width-unit').value;
            const resultUnit = document.getElementById('area-result-unit').value;
            const resultDiv = document.getElementById('area-result');
            
            if (isNaN(length) || isNaN(width)) { resultDiv.innerText = "Please provide both length and width."; return; }

            const lengthInM = length * units.length[lengthUnit];
            const widthInM = width * units.length[widthUnit];
            const areaInM2 = lengthInM * widthInM;
            const finalArea = areaInM2 / units.area[resultUnit];
            
            const formattedResult = parseFloat(finalArea.toFixed(4));
            const historyLog = `Area of ${length} ${lengthUnit} x ${width} ${widthUnit} = ${formattedResult} ${resultUnit}`;
            resultDiv.innerText = `Total Area: ${formattedResult} ${resultUnit}`;
            addToHistory(historyLog, 'measurement');
        }
    </script>
</body>
</html>
