<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>अनंत आदत एवं कार्य ट्रैकर (21-Day Habit Tracker)</title>
    <style>
        * { box-sizing: border-box; }
        body { font-family: Arial, sans-serif; background-color: #eef2f5; margin: 0; padding: 10px; }
        .container { max-width: 900px; margin: 0 auto; background: #fff; padding: 15px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); }
        h2 { text-align: center; color: #1a73e8; margin-top: 5px; margin-bottom: 15px; }
        
        .input-group { display: flex; gap: 8px; margin-bottom: 20px; }
        input[type="text"] { flex-grow: 1; padding: 12px; border: 2px solid #1a73e8; border-radius: 8px; font-size: 16px; outline: none; }
        button.add-btn { padding: 12px 20px; background: #28a745; color: white; border: none; border-radius: 8px; font-weight: bold; font-size: 16px; cursor: pointer; }

        .table-wrapper { overflow-x: auto; max-width: 100%; border: 1px solid #ddd; border-radius: 8px; }
        table { border-collapse: collapse; width: 100%; min-width: 700px; background: #fff; }
        th, td { border: 1px solid #e0e0e0; text-align: center; padding: 8px 4px; vertical-align: middle; }
        
        /* Sticky Columns */
        th.col-sn, td.col-sn { width: 40px; font-weight: bold; background: #f8f9fa; position: sticky; left: 0; z-index: 2; }
        th.col-habit, td.col-habit { min-width: 140px; text-align: left; padding-left: 8px; background: #f8f9fa; position: sticky; left: 40px; z-index: 2; box-shadow: 2px 0 5px rgba(0,0,0,0.05); }
        th.col-action, td.col-action { width: 60px; background: #f8f9fa; }

        .day-header { font-size: 12px; font-weight: bold; color: #333; }
        .date-header { font-size: 10px; color: #666; margin-top: 2px; }

        .habit-title { font-weight: bold; color: #222; font-size: 14px; }
        .habit-progress { font-size: 11px; color: #28a745; font-weight: bold; margin-top: 2px; }

        .mark-btn { width: 36px; height: 36px; border: 1px solid #ccc; border-radius: 6px; background: #fff; font-size: 16px; font-weight: bold; cursor: pointer; margin: 0 auto; display: block; }
        .mark-btn.correct { background: #d4edda; color: #155724; border-color: #c3e6cb; }
        .mark-btn.wrong { background: #f8d7da; color: #721c24; border-color: #f5c6cb; }

        .delete-btn { background: #dc3545; color: white; border: none; border-radius: 6px; padding: 6px 10px; cursor: pointer; font-size: 14px; }
    </style>
</head>
<body>

<div class="container">
    <h2>♾️ अनंत आदत ट्रैकर (21 दिन चक्र)</h2>
    
    <div class="input-group">
        <input type="text" id="habitInput" placeholder="नई आदत/कार्य लिखें (जैसे: जिम जाना, जूस पीना)...">
        <button class="add-btn" id="addBtn">+ जोड़ें</button>
    </div>

    <div class="table-wrapper">
        <table>
            <thead>
                <tr id="headerRow">
                    <th class="col-sn">S.N</th>
                    <th class="col-habit">आदत (21 दिन लक्ष्य)</th>
                    <!-- दिनांक और दिन यहाँ डायनामिक रूप से जुड़ेंगे -->
                    <th class="col-action">हटाएं</th>
                </tr>
            </thead>
            <tbody id="habitBody"></tbody>
        </table>
    </div>
</div>

<script>
    const daysHindi = ['रवि', 'सोम', 'मंगल', 'बुध', 'गुरु', 'शुक्र', 'शनि'];
    
    // पिछले 7 दिन से लेकर आने वाले 30 दिनों का कैलेंडर (कुल 37 कॉलम)
    const datesArr = [];
    const today = new Date();
    for (let i = -7; i <= 30; i++) {
        const d = new Date();
        d.setDate(today.getDate() + i);
        datesArr.push({
            dateStr: d.toISOString().split('T')[0],
            dayName: daysHindi[d.getDay()],
            displayDate: `${d.getDate()}/${d.getMonth() + 1}`
        });
    }

    let habits = JSON.parse(localStorage.getItem('infiniteHabits')) || [];

    function renderHeader() {
        const headerRow = document.getElementById('headerRow');
        headerRow.innerHTML = `
            <th class="col-sn">S.N</th>
            <th class="col-habit">आदत (21 दिन लक्ष्य)</th>
        `;
        datesArr.forEach(item => {
            const th = document.createElement('th');
            th.innerHTML = `<div class="day-header">${item.dayName}</div><div class="date-header">${item.displayDate}</div>`;
            headerRow.appendChild(th);
        });
        const thAction = document.createElement('th');
        thAction.className = 'col-action';
        thAction.innerText = 'हटाएं';
        headerRow.appendChild(thAction);
    }

    function renderBody() {
        const tbody = document.getElementById('habitBody');
        tbody.innerHTML = '';

        if (habits.length === 0) {
            tbody.innerHTML = `<tr><td colspan="${datesArr.length + 3}" style="padding:20px; color:#888;">अभी कोई आदत नहीं है। ऊपर नई आदत लिखें और '+ जोड़ें' पर क्लिक करें!</td></tr>`;
            return;
        }

        habits.forEach((habit, hIndex) => {
            const tr = document.createElement('tr');

            // 21 दिनों की प्रगति गिनना
            let successCount = 0;
            if (habit.marks) {
                Object.values(habit.marks).forEach(val => {
                    if (val === 'correct') successCount++;
                });
            }

            let progressText = `${successCount} / 21 दिन पूर्ण`;
            if (successCount >= 21) {
                progressText = '🎉 21 दिन पूरे हुए!';
            }

            let daysCellsHtml = '';
            datesArr.forEach(item => {
                const status = (habit.marks && habit.marks[item.dateStr]) || '';
                let btnClass = '';
                let btnText = '-';

                if (status === 'correct') {
                    btnClass = 'correct';
                    btnText = '✓';
                } else if (status === 'wrong') {
                    btnClass = 'wrong';
                    btnText = '✕';
                }

                daysCellsHtml += `
                    <td>
                        <button class="mark-btn ${btnClass}" onclick="toggleMark(${hIndex}, '${item.dateStr}')">${btnText}</button>
                    </td>
                `;
            });

            tr.innerHTML = `
                <td class="col-sn">${hIndex + 1}</td>
                <td class="col-habit">
                    <div class="habit-title">${habit.name}</div>
                    <div class="habit-progress">${progressText}</div>
                </td>
                ${daysCellsHtml}
                <td class="col-action">
                    <button class="delete-btn" onclick="deleteHabit(${hIndex})">🗑️</button>
                </td>
            `;
            tbody.appendChild(tr);
        });

        localStorage.setItem('infiniteHabits', JSON.stringify(habits));
    }

    document.getElementById('addBtn').addEventListener('click', () => {
        const input = document.getElementById('habitInput');
        if (!input.value.trim()) return alert('कृपया आदत का नाम लिखें!');
        habits.push({ name: input.value.trim(), startDate: new Date().toISOString().split('T')[0], marks: {} });
        input.value = '';
        renderBody();
    });

    function toggleMark(hIndex, dateStr) {
        if (!habits[hIndex].marks) habits[hIndex].marks = {};
        
        const current = habits[hIndex].marks[dateStr] || '';
        if (current === '') {
            habits[hIndex].marks[dateStr] = 'correct';
        } else if (current === 'correct') {
            habits[hIndex].marks[dateStr] = 'wrong';
        } else {
            habits[hIndex].marks[dateStr] = '';
        }
        renderBody();
    }

    function deleteHabit(hIndex) {
        if (confirm('क्या आप इस आदत को हटाना (Delete) चाहते हैं?')) {
            habits.splice(hIndex, 1);
            renderBody();
        }
    }

    renderHeader();
    renderBody();
</script>

</body>
</html>
