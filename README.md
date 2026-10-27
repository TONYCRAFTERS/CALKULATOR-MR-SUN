# CALKULATOR-MR-SUN
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Budget Intelligence PRO</title>
    <style>
        :root {
            --bg-main: #121214;
            --bg-card: #1a1a1e;
            --accent-blue: #3b82f6;
            --accent-red: #ff4757;
            --accent-green: #2ed573;
            --text-main: #ffffff;
            --text-muted: #a4b0be;
            --border: #2f3542;
            --bg-input: #222733;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: var(--bg-main);
            color: var(--text-main);
            margin: 0;
            padding: 16px;
            display: flex;
            justify-content: center;
        }
        .container { width: 100%; max-width: 500px; box-sizing: border-box; }
        .card { background-color: var(--bg-card); border: 1px solid var(--border); border-radius: 12px; padding: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.5); margin-bottom: 20px; }
        h2 { font-size: 18px; font-weight: 600; margin: 0 0 16px 0; color: var(--accent-blue); border-bottom: 2px solid var(--accent-blue); padding-bottom: 6px; }
        h3 { font-size: 13px; font-weight: 600; margin: 20px 0 10px 0; text-transform: uppercase; letter-spacing: 0.5px; color: var(--text-muted); }
        .grid-3 { display: grid; grid-template-columns: 2fr 1fr 1fr 40px; gap: 8px; align-items: end; margin-bottom: 10px; }
        .grid-2 { display: grid; grid-template-columns: 2fr 1fr; gap: 8px; align-items: end; margin-bottom: 10px; }
        .grid-2-exp { display: grid; grid-template-columns: 2fr 1fr 40px; gap: 8px; align-items: end; margin-bottom: 10px; }
        label { display: block; font-size: 11px; font-weight: 700; color: var(--text-muted); margin-bottom: 4px; text-transform: uppercase; }
        input, select { width: 100%; padding: 10px; font-size: 15px; background-color: var(--bg-input); color: var(--text-main); border: 1px solid var(--border); border-radius: 8px; box-sizing: border-box; outline: none; }
        input:focus { border-color: var(--accent-blue); }
        .results-box { background-color: var(--bg-input); border: 1px solid var(--border); border-radius: 10px; padding: 16px; margin-top: 20px; }
        .row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; font-size: 14px; }
        .row.profit span:last-child { font-size: 20px; font-weight: 700; color: var(--accent-green); }
        .divider { height: 1px; background-color: var(--border); margin: 8px 0; }
        .btn { width: 100%; padding: 12px; font-size: 14px; font-weight: 600; border: none; border-radius: 8px; cursor: pointer; margin-top: 10px; text-transform: uppercase; }
        .btn-primary { background-color: var(--accent-blue); color: white; }
        .btn-add { padding: 6px 12px; font-size: 11px; background: transparent; color: var(--accent-blue); border: 1px solid var(--accent-blue); border-radius: 6px; cursor: pointer; margin-bottom: 15px; text-transform: uppercase; font-weight: 600; }
        .btn-del { background: transparent; color: var(--accent-red); border: 1px solid var(--border); padding: 10px; border-radius: 8px; cursor: pointer; font-weight: bold; height: 40px; display: flex; justify-content: center; align-items: center; }
        .btn-secondary { background-color: transparent; color: var(--accent-red); border: 1px solid var(--accent-red); margin-top: 15px; }
    </style>
</head>
<body>
<div class="container">
    <div class="card">
        <h2>Budget Intelligence PRO</h2>
        
        <h3>ℹ️ Карточка клиента</h3>
        <div class="grid-2">
            <div>
                <label>Клиент</label>
                <input type="text" id="client_name" value="ООО Вектор">
            </div>
            <div>
                <label>Категория</label>
                <select id="client_status" onchange="calculate()">
                    <option value="regular">Стандарт</option>
                    <option value="vip">VIP (-10% расходов)</option>
                </select>
            </div>
        </div>

        <h3>📦 Продукты и Услуги</h3>
        <div id="products_container"></div>
        <button class="btn-add" onclick="addProductRow('', 0, 1)">+ Добавить позицию</button>

        <h3>💸 Расходы и Подрядчики</h3>
        <div id="expenses_container"></div>
        <button class="btn-add" onclick="addExpenseRow('', 0)">+ Добавить статью</button>

        <div class="results-box">
            <div class="row"><span>Выручка</span><span id="revenue">0 ₽</span></div>
            <div class="row"><span>Расходы</span><span id="total_expenses">0 ₽</span></div>
            <div class="divider"></div>
            <div class="row profit"><span>Чистая прибыль</span><span id="profit">0 ₽</span></div>
            <div class="divider"></div>
            <div class="row"><span>ROI</span><span id="margin">0%</span></div>
        </div>

        <button class="btn btn-primary" onclick="window.print()">Сохранить в PDF</button>
        <button class="btn btn-secondary" onclick="resetFields()">Сбросить данные</button>
    </div>
</div>

<script>
    function addProductRow(name = '', price = 0, qty = 1) {
        const container = document.getElementById('products_container');
        const div = document.createElement('div');
        div.className = 'grid-3';
        div.innerHTML = '<div><label>Наименование</label><input type="text" class="prod-name" value="'+name+'"></div><div><label>Цена</label><input type="number" class="prod-price" value="'+price+'" oninput="calculate()"></div><div><label>Кол-во</label><input type="number" class="prod-qty" value="'+qty+'" oninput="calculate()"></div><button class="btn-del" onclick="this.closest(\'.grid-3\').remove(); calculate();">×</button>';
        container.appendChild(div);
        calculate();
    }

    function addExpenseRow(name = '', cost = 0) {
        const container = document.getElementById('expenses_container');
        const div = document.createElement('div');
        div.className = 'grid-2-exp';
        div.innerHTML = '<div><label>Статья расходов</label><input type="text" class="exp-name" value="'+name+'"></div><div><label>Сумма</label><input type="number" class="exp-cost" value="'+cost+'" oninput="calculate()"></div><button class="btn-del" onclick="this.closest(\'.grid-2-exp\').remove(); calculate();">×</button>';
        container.appendChild(div);
        calculate();
    }

    function calculate() {
        let totalRevenue = 0, totalExpenses = 0;
        const prices = document.querySelectorAll('.prod-price');
        const qties = document.querySelectorAll('.prod-qty');
        
        let basePrice = 0, multiplier = 1;
        prices.forEach((el, index) => {
            const p = parseFloat(el.value) || 0;
            const q = parseFloat(qties[index].value) || 0;
            if (index === 0) basePrice = p * q;
            else if (index === 1 && q === 1) multiplier = p;
            else totalRevenue += p * q;
        });
        totalRevenue = (basePrice * multiplier) + totalRevenue;

        document.querySelectorAll('.exp-cost').forEach(el => {
            totalExpenses += parseFloat(el.value) || 0;
        });

        if (document.getElementById('client_status').value === 'vip') totalExpenses *= 0.9;

        const netProfit = totalRevenue - totalExpenses;
        const roi = totalRevenue > 0 ? ((netProfit / totalRevenue) * 100).toFixed(1) : 0;

        document.getElementById('revenue').innerText = Math.round(totalRevenue).toLocaleString('ru-RU') + ' ₽';
        document.getElementById('total_expenses').innerText = Math.round(totalExpenses).toLocaleString('ru-RU') + ' ₽';
        document.getElementById('profit').innerText = Math.round(netProfit).toLocaleString('ru-RU') + ' ₽';
        document.getElementById('margin').innerText = roi + '%';
    }

    function resetFields() {
        document.getElementById('products_container').innerHTML = '';
        document.getElementById('expenses_container').innerHTML = '';
        document.getElementById('client_name').value = '';
        document.getElementById('client_status').value = 'regular';
        calculate();
    }

    window.onload = function() {
        addProductRow('Шоу-программа (в час)', 1500, 3);
        addProductRow('Участники (множитель)', 20, 1);
        addExpenseRow('Ведущий', 15000);
        addExpenseRow('Диджей', 8000);
        addExpenseRow('Декор', 6500);
    }
</script>
</body>
</html>

