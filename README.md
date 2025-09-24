<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>종로 08번 근무현황표 생성기</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 10px; [cite_start]/* [cite: 2] */
            background-color: #f4f4f4; color: #333;
            font-size: 12pt;
        }
        .controls {
            background-color: #fff;
            padding: 15px; border-radius: 8px; [cite_start]/* [cite: 3] */
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); margin-bottom: 15px;
            display: flex; flex-wrap: wrap; gap: 10px;
            align-items: center; [cite_start]/* [cite: 4] */
        }
        .controls label { font-weight: bold; margin-right: 5px; [cite_start]/* [cite: 5] */ }
        .controls input[type="number"], .controls select {
            padding: 6px; [cite_start]/* [cite: 6] */
            border: 1px solid #ddd; border-radius: 4px; [cite_start]/* [cite: 6] */
            width: 70px; box-sizing: border-box; [cite_start]/* [cite: 7] */
        }
        .controls input[type="radio"] { margin-left: 10px; [cite_start]/* [cite: 8] */ }
        .controls button, .controls a.button-link {
            padding: 8px 15px; [cite_start]/* [cite: 9] */
            color: white; border: none; border-radius: 4px; [cite_start]/* [cite: 9] */
            cursor: pointer; font-size: 15px; margin-right: 8px;
            text-decoration: none; display: inline-block; text-align: center; white-space: nowrap; [cite_start]/* [cite: 10] */
        }
        .controls button { background-color: #007bff; [cite_start]/* [cite: 11] */ }
        .controls button:hover { background-color: #0056b3; [cite_start]/* [cite: 12] */ }
        .controls a.button-link { background-color: #17a2b8; [cite_start]/* [cite: 13] */ }
        .controls a.button-link:hover { background-color: #138496; [cite_start]/* [cite: 14] */ }
        .controls button.btn-danger { background-color: #dc3545; [cite_start]/* [cite: 15] */ }
        .controls button.btn-danger:hover { background-color: #c82333; [cite_start]/* [cite: 16] */ }
        
        #scheduleContainer {
            background-color: white;
            padding: 10px; box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); [cite_start]/* [cite: 17] */
            margin: 20px auto; max-width: 95%; overflow-x: auto; [cite_start]/* [cite: 18] */
        }
        table {
            width: 100%;
            min-width: 1200px; border-collapse: collapse; [cite_start]/* [cite: 19] */
            margin: 0 auto; font-size: 0.9em; background-color: #fff; table-layout: fixed; [cite_start]/* [cite: 20] */
        }
        th, td {
            border: 1px solid #ddd; [cite_start]/* [cite: 21] */
            padding: 6px; text-align: center; [cite_start]/* [cite: 21] */
            vertical-align: top; line-height: 1.1; word-break: keep-all;
            height: 38px; box-sizing: border-box;
            font-size: 12pt; [cite_start]/* [cite: 22] */
        }
        th { background-color: #f2f2f2; font-weight: bold; [cite_start]/* [cite: 23] */ }
        caption {
            font-size: 1.2em;
            margin-bottom: 10px; font-weight: bold; [cite_start]/* [cite: 24] */
            color: #333; text-decoration: underline;
        }
        .sunday { background-color: #ffe0e0; [cite_start]/* [cite: 25] */ }
        .saturday { background-color: #e0e0ff; [cite_start]/* [cite: 26] */ }
        .holiday { background-color: #ffcccc; font-weight: bold; color: #cc0000; [cite_start]/* [cite: 27] */ }
        .edit-mode {
            position: relative; [cite_start]/* [cite: 28] */
            padding: 2px; display: flex; flex-direction: column; [cite_start]/* [cite: 28] */
            justify-content: center; height: 100%; [cite_start]/* [cite: 29] */
        }
        .edit-mode input {
            width: calc(100% - 8px); [cite_start]/* [cite: 30] */
            margin-bottom: 2px; [cite_start]/* [cite: 30] */
            padding: 2px; box-sizing: border-box;
        }
        .edit-mode .cell-buttons { display: flex; [cite_start]/* [cite: 31] */
            justify-content: space-around; margin-top: 2px; [cite_start]} /* [cite: 31] */
        .edit-mode .cell-buttons button {
            padding: 3px 5px; [cite_start]/* [cite: 32] */
            font-size: 0.7em; color: white; border: none; [cite_start]/* [cite: 32] */
            border-radius: 3px; cursor: pointer; white-space: nowrap; [cite_start]/* [cite: 33] */
        }
        .edit-mode .cell-buttons button.btn-fixed { background-color: #28a745; [cite_start]/* [cite: 34] */ }
        .edit-mode .cell-buttons button.btn-temp { background-color: #ffc107; [cite_start]/* [cite: 35] */ }
        .edit-mode .cell-buttons button.btn-highlight-am { background-color: #ff8c00; [cite_start]/* [cite: 36] */ }
        .edit-mode .cell-buttons button.btn-highlight-pm { background-color: #ff8c00; [cite_start]/* [cite: 37] */ }
        .edit-mode button:disabled { cursor: not-allowed; opacity: 0.6; [cite_start]/* [cite: 38] */ }
        .edit-mode .cell-buttons button.btn-delete { background-color: #dc3545; [cite_start]/* [cite: 39] */ }

        .name-entry { display: block; margin-top: 2px; margin-bottom: 2px; [cite_start]/* [cite: 40] */ }
        .name-entry.bold-name { font-weight: bold; [cite_start]/* [cite: 41] */ }
        .hidden-x {
            color: white; [cite_start]/* [cite: 42] */
        }
        table th:nth-child(1),
        table td:nth-child(1),
        table th:nth-child(2),
        table td:nth-child(2) {
            width: 4%; [cite_start]/* [cite: 43] */
            min-width: 60px; [cite_start]/* [cite: 43] */
        }

        .modal {
            display: none; [cite_start]/* [cite: 44] */
            position: fixed; [cite_start]/* [cite: 44] */
            z-index: 1000; left: 0; top: 0;
            width: 100%; height: 100%;
            background-color: rgba(0,0,0,0.6);
            align-items: center; justify-content: center; [cite_start]/* [cite: 45] */
        }
        .modal-content {
            background-color: #fff; [cite_start]/* [cite: 46] */
            padding: 20px; border-radius: 10px; [cite_start]/* [cite: 46] */
            max-height: 90%; overflow-y: auto;
            position: relative; box-shadow: 0 4px 8px rgba(0,0,0,0.2); [cite_start]/* [cite: 47] */
            /* 수정된 부분: 창 크기 조절 */
            width: 450px;
            max-width: 90%;
        }
        .modal-content h3 { margin-top: 0; [cite_start]/* [cite: 48] */ }
        .close-button {
            position: absolute; [cite_start]/* [cite: 49] */
            right: 10px; top: 5px; [cite_start]/* [cite: 49] */
            font-size: 25px; font-weight: bold; [cite_start]/* [cite: 50] */
            color: #aaa; cursor: pointer; [cite_start]/* [cite: 50] */
        }
        .count-table {
            width: 100%; [cite_start]/* [cite: 51] */
            border-collapse: collapse; [cite_start]/* [cite: 51] */
            margin-top: 15px;
        }
        .count-table th, .count-table td {
            border: 1px solid #ddd; [cite_start]/* [cite: 52] */
            padding: 8px; text-align: center; [cite_start]/* [cite: 52] */
        }
        .count-table th { background-color: #f2f2f2; [cite_start]/* [cite: 53] */ }
        
        @media print {
            @page {
                size: A4;
                margin: 10mm; [cite_start]/* [cite: 54] */
            }
            body {
                background-color: white; [cite_start]/* [cite: 55] */
                margin: 0; padding: 0; [cite_start]/* [cite: 55] */
                -webkit-print-color-adjust: exact;
            }
            .controls { display: none; [cite_start]/* [cite: 56] */ }
            #scheduleContainer { box-shadow: none; margin: 0; padding: 0; [cite_start]/* [cite: 57] */ }
            table {
                width: 100%; [cite_start]/* [cite: 58] */
                min-width: unset; [cite_start]/* [cite: 58] */
                font-size: 0.7em;
                border: 1px solid #000;
                table-layout: fixed; [cite_start]/* [cite: 59] */
            }
            th, td { padding: 1px; height: auto; [cite_start]/* [cite: 60] */ word-break: break-all; [cite_start]} /* [cite: 60] */

            table th:nth-child(1),
            table td:nth-child(1),
            table th:nth-child(2),
            table td:nth-child(2) {
                width: 4%; [cite_start]/* [cite: 61] */
            }
            .modal-content {
                position: static; [cite_start]/* [cite: 62] */
                width: 100%; [cite_start]/* [cite: 62] */
                max-width: none; [cite_start]/* [cite: 63] */
                max-height: none; [cite_start]/* [cite: 63] */
                box-shadow: none; [cite_start]/* [cite: 63] */
                overflow-y: visible; [cite_start]/* [cite: 63] */
                border-radius: 0;
            }
            .modal {
                display: flex; [cite_start]/* [cite: 64] */
                position: static; [cite_start]/* [cite: 64] */
                background-color: transparent;
            }
            .modal .close-button { display: none; [cite_start]/* [cite: 65] */ }
            /* 추가된 부분: 근무일수 인쇄 스타일 */
            body.printing-modal #scheduleContainer,
            body.printing-modal .controls,
            body.printing-modal .modal .close-button,
            body.printing-modal #modalPrintButton {
                display: none;
            }
            body.printing-modal #workdayModal {
                display: flex !important;
                position: absolute;
                left: 0; top: 0;
                width: 100%; height: auto;
                background-color: white;
            }
        }
        @media screen and (max-width: 768px) {
            .controls { flex-direction: column; [cite_start]/* [cite: 66] */ align-items: stretch; [cite_start]} /* [cite: 66] */
            .controls input[type="number"], .controls select, .controls button, .controls a.button-link { width: 100%; [cite_start]/* [cite: 67] */ box-sizing: border-box; margin: 5px 0; [cite_start]} /* [cite: 67] */
            #scheduleContainer { padding: 5px; [cite_start]/* [cite: 68] */ }
            table { font-size: 0.8em; [cite_start]/* [cite: 69] */ }
            th, td { padding: 4px; [cite_start]/* [cite: 70] */ }
        }
    </style>
</head>
<body>
    <div class="controls">
        <label for="yearInput">년도:</label>
        <input type="number" id="yearInput" value="2025">
        <label for="monthInput">월:</label>
        <input type="number" id="monthInput" value="9">
        
        <input type="radio" id="option1" name="displayOption" value="1">
        <label for="option1">1일 ~ 15일</label>
        <input type="radio" id="option2" name="displayOption" 
               value="2" checked> <label for="option2">16일 ~ 말일</label>
        
        <button onclick="generateSchedule()">생성</button>
        <button onclick="calculateWorkdays()">08번 근무일수계산</button>
        <button onclick="printPage()">인쇄</button>
        <button class="btn-danger" onclick="resetAllFixedSchedules()">전체 고정 초기화</button>
        <a href="https://waryong2025.github.io/townbus/" class="button-link" target="_blank">08번 배차표</a>
        <a href="https://waryong2025.github.io/timeman/" class="button-link" target="_blank">07번 배차표</a>
        <a href="https://waryong2025.github.io/time" class="button-link" target="_blank">07번 근무현황생성</a>
    </div> <div id="scheduleContainer">
        <table>
            <caption id="tableCaption">종로 08번 2025년 9월 16일 ~ 30일까지 근무현황표</caption>
            <thead id="scheduleHeader">
            </thead>
            <tbody id="scheduleBody">
            </tbody>
        </table>
    </div>

    <div id="workdayModal" class="modal"> <div class="modal-content">
            <span class="close-button" onclick="closeModal()">&times;</span>
            <h3 id="modalTitle"></h3>
            <div id="modalTableContainer"></div>
            <button id="modalPrintButton" onclick="printWorkdayModal()" style="margin-top: 15px; width: 100%; padding: 10px; font-size: 16px;">근무일수 인쇄</button>
        </div>
    </div>

    <script>
        const VEHICLE_COLUMNS = ['3611호', '5510호', '3615호', '스피아', '3612호', '6201호', '5504호', '5535호', '5536호', '3614호', '비고'];
        [cite_start]const STORAGE_KEY_FIXED = 'jongro08FixedSchedule'; /* [cite: 74] */
        const STORAGE_KEY_TEMP = 'jongro08TempSchedule';

        let fixedScheduleData = JSON.parse(localStorage.getItem(STORAGE_KEY_FIXED)) || {};
        let tempScheduleData = JSON.parse(localStorage.getItem(STORAGE_KEY_TEMP)) || {};
        let scheduleData = {}; [cite_start]/* [cite: 75] */
        
        function getDayName(dayOfWeek) {
            const days = ['일', '월', '화', '수', '목', '금', '토']; [cite_start]/* [cite: 76] */
            return days[dayOfWeek]; [cite_start]/* [cite: 76] */
        }

        function isPublicHoliday(date) {
            const year = date.getFullYear(); [cite_start]/* [cite: 77] */
            const month = date.getMonth() + 1; [cite_start]/* [cite: 77] */
            const day = date.getDate(); [cite_start]/* [cite: 78] */
            const holidays = []; [cite_start]/* [cite: 79] */
            return holidays.some(h => h.month === month && h.day === day); [cite_start]/* [cite: 80] */
        }

        function renderCellContent(names) {
            if (!names || names.length === 0) return ''; [cite_start]/* [cite: 81] */
            [cite_start]return names.map(item => { /* [cite: 81] */
                const text = item.text.trim();
                const display = text === '' ? 'X' : text;
                const hiddenClass = text === '' ? ' hidden-x' : '';
                return `<span class="name-entry${hiddenClass} ${item.bold ? 'bold-name' : ''}">${display}</span>`;
            }).join(''); [cite_start]/* [cite: 82] */
        }
        
        function editCell(cell, colId, dateString, dayOfWeek) {
            if (cell.classList.contains('edit-mode')) return; [cite_start]/* [cite: 83] */
            [cite_start]document.querySelectorAll('.edit-mode').forEach(editCell => { /* [cite: 83] */
                const prevColId = editCell.dataset.colId;
                const prevDateString = editCell.dataset.dateString;
                const originalNames = (scheduleData[prevDateString] && scheduleData[prevDateString][prevColId]) || [{ text: '', bold: false }, { text: '', bold: false }];
                editCell.classList.remove('edit-mode');
                editCell.innerHTML = renderCellContent(originalNames); [cite_start]/* [cite: 84] */
            });
            const currentNames = (scheduleData[dateString] && scheduleData[dateString][colId]) || [{ text: '', bold: false }, { text: '', bold: false }]; [cite_start]/* [cite: 85] */
            const morningValue = currentNames[0] ? currentNames[0].text : ''; [cite_start]/* [cite: 86] */
            const afternoonValue = currentNames[1] ? currentNames[1].text : '';
            const isSunday3614 = (colId === '3614호' && dayOfWeek === 0); [cite_start]/* [cite: 87] */

            cell.classList.add('edit-mode');
            cell.dataset.colId = colId;
            cell.dataset.dateString = dateString;
            cell.dataset.dayOfWeek = dayOfWeek;
            cell.innerHTML = `
                <input type="text" class="morning-input" value="${morningValue}" placeholder="오전" data-bold="${currentNames[0].bold}">
                <input type="text" class="afternoon-input" value="${afternoonValue}" placeholder="오후" data-bold="${currentNames[1].bold}">
                <div class="cell-buttons">
                    <button class="btn-fixed" onclick="saveFixedCell(this.parentNode.parentNode)" ${isSunday3614 ? 'disabled title="일요일은 고정 저장이 불가능합니다."' : ''}>고정</button> <button class="btn-temp" onclick="saveTempCell(this.parentNode.parentNode)">임시</button>
                    <button class="btn-highlight-am" onclick="highlightCell(this.parentNode.parentNode, 'am')">오전 강조</button>
                    <button class="btn-highlight-pm" onclick="highlightCell(this.parentNode.parentNode, 'pm')">오후 강조</button>
                    <button class="btn-delete" onclick="deleteCell(this.parentNode.parentNode)">삭제</button>
                </div> `;
            cell.querySelector('.morning-input').focus(); [cite_start]/* [cite: 91] */
        }
        
        function getNamesAndBoldStateFromCell(cellElement) {
            const morningInput = cellElement.querySelector('.morning-input'); [cite_start]/* [cite: 92] */
            const afternoonInput = cellElement.querySelector('.afternoon-input'); [cite_start]/* [cite: 92] */
            const morningText = morningInput.value.trim();
            const afternoonText = afternoonInput.value.trim();

            const morningBold = morningInput.dataset.bold === 'true'; [cite_start]/* [cite: 93] */
            const afternoonBold = afternoonInput.dataset.bold === 'true'; [cite_start]/* [cite: 93] */
            
            return [
                { text: morningText, bold: morningBold },
                { text: afternoonText, bold: afternoonBold }
            ]; [cite_start]/* [cite: 94] */
        }
        
        function highlightCell(cellElement, timeOfDay) {
            let inputElement;
            [cite_start]if (timeOfDay === 'am') { /* [cite: 95] */
                inputElement = cellElement.querySelector('.morning-input'); [cite_start]/* [cite: 95] */
            [cite_start]} else if (timeOfDay === 'pm') { /* [cite: 96] */
                inputElement = cellElement.querySelector('.afternoon-input'); [cite_start]/* [cite: 96] */
            }

            [cite_start]if (inputElement) { /* [cite: 97] */
                const isCurrentlyBold = inputElement.dataset.bold === 'true'; [cite_start]/* [cite: 98] */
                inputElement.dataset.bold = !isCurrentlyBold; [cite_start]/* [cite: 98] */
                inputElement.style.fontWeight = inputElement.dataset.bold === 'true' ? 'bold' : 'normal'; [cite_start]/* [cite: 99] */
            }
        }

        function saveFixedCell(cellElement) {
            const { colId, dayOfWeek, dateString } = cellElement.dataset; [cite_start]/* [cite: 100] */
            const namesToSave = getNamesAndBoldStateFromCell(cellElement); [cite_start]/* [cite: 100] */

            if (colId === '3614호' && dayOfWeek === '0') {
                alert('일요일은 휴무이므로 저장할 수 없습니다.'); [cite_start]/* [cite: 101] */
                return; [cite_start]/* [cite: 101] */
            }
            
            const currentRecurringKey = `${dayOfWeek}-${colId}`; [cite_start]/* [cite: 102] */
            [cite_start]if (!fixedScheduleData[currentRecurringKey]) { /* [cite: 102] */
                fixedScheduleData[currentRecurringKey] = []; [cite_start]/* [cite: 103] */
            }
            fixedScheduleData[currentRecurringKey] = fixedScheduleData[currentRecurringKey].filter(
                entry => entry.dateString !== dateString
            ); [cite_start]/* [cite: 104] */
            [cite_start]fixedScheduleData[currentRecurringKey].push({ /* [cite: 104] */
                dateString: dateString,
                names: namesToSave
            });
            [cite_start]if (colId === '3614호' && dayOfWeek !== '0') { /* [cite: 105] */
                const nextWeekDate = new Date(dateString); [cite_start]/* [cite: 105] */
                nextWeekDate.setDate(nextWeekDate.getDate() + 7); [cite_start]/* [cite: 106] */
                const nextWeekDateString = nextWeekDate.toISOString().slice(0, 10);
                const nextWeekDayOfWeek = nextWeekDate.getDay(); [cite_start]/* [cite: 107] */
                [cite_start]const rotatedNames = [ /* [cite: 107] */
                    namesToSave[1],
                    namesToSave[0]
                ];
                const nextWeekRecurringKey = `${nextWeekDayOfWeek}-${colId}`; [cite_start]/* [cite: 108] */
                [cite_start]if (!fixedScheduleData[nextWeekRecurringKey]) { /* [cite: 109] */
                    fixedScheduleData[nextWeekRecurringKey] = []; [cite_start]/* [cite: 109] */
                }
                fixedScheduleData[nextWeekRecurringKey] = fixedScheduleData[nextWeekRecurringKey].filter(
                    entry => entry.dateString !== nextWeekDateString
                ); [cite_start]/* [cite: 110] */
                [cite_start]fixedScheduleData[nextWeekRecurringKey].push({ /* [cite: 110] */
                    dateString: nextWeekDateString,
                    names: rotatedNames
                });
            }


            [cite_start]for (const key in fixedScheduleData) { /* [cite: 111] */
                fixedScheduleData[key].sort((a, b) => new Date(b.dateString) - new Date(a.dateString)); [cite_start]/* [cite: 112] */
            }

            localStorage.setItem(STORAGE_KEY_FIXED, JSON.stringify(fixedScheduleData)); [cite_start]/* [cite: 113] */
            generateSchedule(); [cite_start]/* [cite: 113] */
        }
        
        function saveTempCell(cellElement) {
            const { colId, dateString } = cellElement.dataset; [cite_start]/* [cite: 114] */
            const namesToSave = getNamesAndBoldStateFromCell(cellElement); [cite_start]/* [cite: 114] */

            [cite_start]if (!tempScheduleData[dateString]) { /* [cite: 115] */
                tempScheduleData[dateString] = {}; [cite_start]/* [cite: 115] */
            }
            tempScheduleData[dateString][colId] = namesToSave;
            
            localStorage.setItem(STORAGE_KEY_TEMP, JSON.stringify(tempScheduleData)); [cite_start]/* [cite: 116] */
            generateSchedule(); [cite_start]/* [cite: 116] */
        }
        
        function deleteCell(cellElement) {
            const { colId, dayOfWeek, dateString } = cellElement.dataset; [cite_start]/* [cite: 117] */
            const recurringKey = `${dayOfWeek}-${colId}`; [cite_start]/* [cite: 117] */

            if (fixedScheduleData[recurringKey]) {
                fixedScheduleData[recurringKey] = fixedScheduleData[recurringKey].filter(
                    entry => entry.dateString !== dateString
                );
                [cite_start]if (fixedScheduleData[recurringKey].length === 0) { /* [cite: 118] */
                    delete fixedScheduleData[recurringKey]; [cite_start]/* [cite: 119] */
                }
                localStorage.setItem(STORAGE_KEY_FIXED, JSON.stringify(fixedScheduleData)); [cite_start]/* [cite: 120] */
            }

            if (tempScheduleData[dateString] && tempScheduleData[dateString][colId]) {
                delete tempScheduleData[dateString][colId]; [cite_start]/* [cite: 121] */
                [cite_start]if (Object.keys(tempScheduleData[dateString]).length === 0) { /* [cite: 122] */
                    delete tempScheduleData[dateString]; [cite_start]/* [cite: 122] */
                }
                localStorage.setItem(STORAGE_KEY_TEMP, JSON.stringify(tempScheduleData)); [cite_start]/* [cite: 123] */
            }
            
            generateSchedule(); [cite_start]/* [cite: 124] */
        }
        
        function resetAllFixedSchedules() {
            const password = prompt("저장된 모든 고정 근무표를 삭제하시려면 비밀번호를 입력하세요."); [cite_start]/* [cite: 125] */
            [cite_start]if (password === "201023") { /* [cite: 125] */
                if (confirm("모든 고정 근무표를 삭제합니다. 계속하시겠습니까?")) {
                    fixedScheduleData = {}; [cite_start]/* [cite: 126] */
                    localStorage.removeItem(STORAGE_KEY_FIXED); [cite_start]/* [cite: 126] */
                    if (confirm("임시 근무표도 함께 초기화하시겠습니까?")) {
                        tempScheduleData = {}; [cite_start]/* [cite: 127] */
                        localStorage.removeItem(STORAGE_KEY_TEMP); [cite_start]/* [cite: 127] */
                    }
                    generateSchedule(); [cite_start]/* [cite: 128] */
                    alert("모든 근무표가 초기화되었습니다."); [cite_start]/* [cite: 128] */
                }
            } else {
                alert("비밀번호 확인 후 신청하세요."); [cite_start]/* [cite: 129] */
            }
        }

        function generateSchedule() {
            const year = parseInt(document.getElementById('yearInput').value); [cite_start]/* [cite: 130] */
            const month = parseInt(document.getElementById('monthInput').value); [cite_start]/* [cite: 130] */
            const selectedOption = document.querySelector('input[name="displayOption"]:checked').value;
            
            const scheduleHeader = document.getElementById('scheduleHeader');
            const scheduleBody = document.getElementById('scheduleBody');
            const tableCaption = document.getElementById('tableCaption');
            const headerHTML = `<tr><th style="width: 4%;">월일</th><th style="width: 4%;">요일</th>${VEHICLE_COLUMNS.map(c => `<th>${c}</th>`).join('')}</tr>`; [cite_start]/* [cite: 131] */
            scheduleHeader.innerHTML = headerHTML;
            scheduleBody.innerHTML = ''; [cite_start]/* [cite: 132] */
            const startDate = new Date(year, month - 1, selectedOption === '1' ? 1 : 16); [cite_start]/* [cite: 132] */
            const endDate = (selectedOption === '1') ? new Date(year, month - 1, 15) : new Date(year, month, 0); [cite_start]/* [cite: 133] */
            tableCaption.textContent = `종로 08번 ${year}년 ${month}월 ${startDate.getDate()}일 ~ ${endDate.getDate()}일까지 근무현황표`; [cite_start]/* [cite: 134] */
            
            scheduleData = {}; [cite_start]/* [cite: 135] */
            [cite_start]for (let d = new Date(startDate); d <= endDate; d.setDate(d.getDate() + 1)) { /* [cite: 135] */
                const dateString = d.toISOString().slice(0, 10); [cite_start]/* [cite: 135] */
                const dayOfWeek = d.getDay(); [cite_start]/* [cite: 136] */
                
                let cellsContent = {};
                VEHICLE_COLUMNS.forEach(colId => {
                    let appliedNames = [{ text: '', bold: false }, { text: '', bold: false }];

                    if (tempScheduleData[dateString] && tempScheduleData[dateString][colId]) {
                        appliedNames = tempScheduleData[dateString][colId];
                    [cite_start]} /* [cite: 137] */
                    else {
                        const recurringKey = `${dayOfWeek}-${colId}`;
                        if (fixedScheduleData[recurringKey]) {
                            [cite_start]for (const entry of fixedScheduleData[recurringKey]) { /* [cite: 138] */
                                if (new Date(dateString) >= new Date(entry.dateString)) {
                                    appliedNames = entry.names;
                                    break; [cite_start]/* [cite: 139] */
                                }
                            }
                        [cite_start]} /* [cite: 140] */
                    }
                    cellsContent[colId] = appliedNames;
                });
                scheduleData[dateString] = cellsContent; [cite_start]/* [cite: 141] */
                
                let rowClass = isPublicHoliday(d) ? 'holiday' : ''; [cite_start]/* [cite: 142] */
                [cite_start]if (!rowClass) { /* [cite: 142] */
                    if (dayOfWeek === 0) rowClass = 'sunday'; [cite_start]/* [cite: 143] */
                    else if (dayOfWeek === 6) rowClass = 'saturday'; [cite_start]/* [cite: 143] */
                }

                const dataCellsHTML = VEHICLE_COLUMNS.map(colId =>
                    `<td onclick="editCell(this, '${colId}', '${dateString}', ${dayOfWeek})">${renderCellContent(cellsContent[colId])}</td>`
                ).join(''); [cite_start]/* [cite: 144] */
                scheduleBody.insertAdjacentHTML('beforeend', `
                    <tr class="${rowClass}">
                        <td>${d.getDate()}</td>
                        <td>${getDayName(dayOfWeek)}</td>
                        ${dataCellsHTML}
                    </tr>`); [cite_start]/* [cite: 145] */
            }
        }

        /**
         * 수정된 함수: calculateWorkdays
         * 1. 1일부터 말일까지 전체 계산
         * 2. 오전/오후 근무일수 분리 계산
         */
        function calculateWorkdays() {
            const year = parseInt(document.getElementById('yearInput').value);
            const month = parseInt(document.getElementById('monthInput').value);
            
            const workdayCounts = {};
            const startDate = new Date(year, month - 1, 1);
            const endDate = new Date(year, month, 0);

            // 1일부터 말일까지 데이터 수집 및 계산
            for (let d = new Date(startDate); d <= endDate; d.setDate(d.getDate() + 1)) {
                const dateString = d.toISOString().slice(0, 10);
                const dayOfWeek = d.getDay();
                
                VEHICLE_COLUMNS.forEach(colId => {
                    if (colId === '비고') return;

                    let appliedNames = [{ text: '', bold: false }, { text: '', bold: false }];
                    
                    if (tempScheduleData[dateString] && tempScheduleData[dateString][colId]) {
                        appliedNames = tempScheduleData[dateString][colId];
                    } else {
                        const recurringKey = `${dayOfWeek}-${colId}`;
                        if (fixedScheduleData[recurringKey]) {
                            for (const entry of fixedScheduleData[recurringKey]) {
                                if (new Date(dateString) >= new Date(entry.dateString)) {
                                    appliedNames = entry.names;
                                    break;
                                }
                            }
                        }
                    }

                    // 이름별 오전/오후 카운트
                    appliedNames.forEach((item, index) => {
                        const name = item.text.trim();
                        if (name !== '') {
                            if (!workdayCounts[name]) {
                                workdayCounts[name] = { morning: 0, afternoon: 0, total: 0 };
                            }
                            if (index === 0) { // 오전
                                workdayCounts[name].morning++;
                            } else { // 오후
                                workdayCounts[name].afternoon++;
                            }
                            workdayCounts[name].total++;
                        }
                    });
                });
            }
            
            const sortedCounts = Object.entries(workdayCounts).sort((a, b) => b[1].total - a[1].total);
            
            const modalTitle = document.getElementById('modalTitle'); [cite_start]/* [cite: 155] */
            const modalTableContainer = document.getElementById('modalTableContainer'); [cite_start]/* [cite: 155] */
            modalTitle.textContent = `${year}년 ${month}월 근무일수`;
            
            let tableHTML = `<table class="count-table"><thead><tr><th>이름</th><th>오전/오후</th><th>총 근무일수</th></tr></thead><tbody>`;
            sortedCounts.forEach(([name, counts]) => {
                tableHTML += `<tr>
                                <td>${name}</td>
                                <td style="text-align: left; padding-left: 20px; line-height: 1.4;">
                                    <div>오전: ${counts.morning}일</div>
                                    <div>오후: ${counts.afternoon}일</div>
                                </td>
                                <td>${counts.total}일</td>
                              </tr>`;
            });
            tableHTML += `</tbody></table>`;
            
            modalTableContainer.innerHTML = tableHTML;
            document.getElementById('workdayModal').style.display = 'flex';
        }

        function printPage() {
            document.querySelectorAll('.edit-mode').forEach(cell => {
                const { colId, dateString } = cell.dataset;
                const originalNames = (scheduleData[dateString] && scheduleData[dateString][colId]) || [{ text: '', bold: false }, { text: '', bold: false }];
                [cite_start]cell.classList.remove('edit-mode'); /* [cite: 158] */
                cell.innerHTML = renderCellContent(originalNames);
            });
            window.print(); [cite_start]/* [cite: 159] */
        }
        
        /**
         * 추가된 함수: printWorkdayModal
         * 근무일수 창 인쇄 기능
         */
        function printWorkdayModal() {
            document.body.classList.add('printing-modal');
            window.print();
            document.body.classList.remove('printing-modal');
        }

        function closeModal() {
            document.getElementById('workdayModal').style.display = 'none'; [cite_start]/* [cite: 160] */
        }
        
        document.addEventListener('DOMContentLoaded', () => {
            const today = new Date();
            document.getElementById('yearInput').value = today.getFullYear();
            document.getElementById('monthInput').value = today.getMonth() + 1;
            const currentDay = today.getDate();
            if (currentDay <= 15) {
                [cite_start]document.getElementById('option1').checked = true; /* [cite: 161] */
            } else {
                document.getElementById('option2').checked = true;
            }
            generateSchedule();
        });
    </script> </body>
</html>
