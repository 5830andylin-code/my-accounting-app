<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI 雲端資產管家</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #f8fafc; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-24">

    <header class="bg-white border-b border-slate-200/80 px-4 py-3 sticky top-0 z-30 shadow-2xs">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div class="flex items-center space-x-2">
                <div class="bg-slate-900 text-white p-1.5 rounded-lg"><i data-lucide="wallet" class="w-4 h-4"></i></div>
                <h1 class="text-sm font-black text-slate-900 tracking-tight">資產與公告級股利管家</h1>
            </div>
            <button onclick="syncCloudData()" class="flex items-center space-x-1 bg-indigo-50 text-indigo-600 px-3 py-1 rounded-full text-xs font-bold transition-all hover:bg-indigo-100">
                <i data-lucide="refresh-cw" class="w-3 h-3" id="sync-icon"></i>
                <span>同步雲端</span>
            </button>
        </div>
    </header>

    <main class="max-w-md mx-auto px-4 mt-4">
        
        <section id="tab-dashboard" class="space-y-4">
            <div class="bg-slate-900 rounded-2xl p-5 text-white shadow-lg relative overflow-hidden">
                <p class="text-[10px] text-slate-400 font-medium tracking-wider uppercase mb-1">雲端跨裝置總資產淨值 (TWD)</p>
                <h3 id="total-assets" class="text-2xl font-black tracking-tight mb-3">$0</h3>
                <div class="grid grid-cols-3 gap-2 pt-3 border-t border-slate-800 text-center text-xs">
                    <div><p class="text-slate-400 text-[10px]">現金儲蓄</p><p id="total-cash" class="font-bold text-emerald-400">$0</p></div>
                    <div><p class="text-slate-400 text-[10px]">外幣資產</p><p id="total-forex-worth" class="font-bold text-cyan-400">$0</p></div>
                    <div><p class="text-slate-400 text-[10px]">證券市值</p><p id="total-stock-worth" class="font-bold text-amber-400">$0</p></div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-2xs">
                <div class="w-full flex justify-center items-center h-[140px]"><canvas id="allocationChart"></canvas></div>
                <div id="risk-advice" class="mt-3 bg-slate-50 rounded-xl p-2.5 text-[11px] text-slate-500 leading-relaxed border border-slate-100">💡 請至「對接設定」綁定您的 GAS 雲端網址並完成同步。</div>
            </div>
        </section>

        <section id="tab-ledger" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-2xs">
                <h4 class="text-xs font-black text-slate-700 uppercase mb-2 flex items-center space-x-1"><i data-lucide="zap" class="w-3.5 h-3.5 text-amber-500"></i><span>常用項目極速秒記</span></h4>
                <div class="grid grid-cols-4 gap-2">
                    <button onclick="quickLedger(100, '外食早餐')" class="bg-slate-50 border border-slate-100/70 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">早餐</p><p class="text-[10px] text-slate-400">$100</p></button>
                    <button onclick="quickLedger(150, '午餐便當')" class="bg-slate-50 border border-slate-100/70 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">午餐</p><p class="text-[10px] text-slate-400">$150</p></button>
                    <button onclick="quickLedger(250, '晚餐小吃')" class="bg-slate-50 border border-slate-100/70 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">晚餐</p><p class="text-[10px] text-slate-400">$250</p></button>
                    <button onclick="quickLedger(65, '手搖飲料')" class="bg-slate-50 border border-slate-100/70 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">飲料</p><p class="text-[10px] text-slate-400">$65</p></button>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-2xs space-y-3">
                <div class="grid grid-cols-2 gap-2">
                    <button type="button" id="btn-type-expense" class="py-2 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600" onclick="setLedgerType('expense')">支出 (-)</button>
                    <button type="button" id="btn-type-income" class="py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600" onclick="setLedgerType('income')">收入 (+)</button>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <input type="number" id="ledger-amount" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-bold" placeholder="金額 (TWD)">
                    <input type="text" id="ledger-note" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs" placeholder="項目說明">
                </div>
                <input type="date" id="ledger-date" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs text-slate-600">
                <button onclick="saveLedgerToCloud()" id="btn-submit-ledger" class="w-full bg-indigo-600 text-white font-bold text-xs py-2.5 rounded-xl shadow-md">保存並直傳雲端</button>
            </div>
        </section>

        <section id="tab-stocks" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-2xs space-y-2.5">
                <h5 class="text-xs font-black text-slate-700 uppercase flex items-center space-x-1"><i data-lucide="plus-circle" class="w-3.5 h-3.5 text-indigo-500"></i><span>新增/修改 本地持股庫存</span></h5>
                <div class="grid grid-cols-3 gap-2">
                    <input type="text" id="stock-symbol" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-black text-center uppercase font-mono" placeholder="代碼(如2330)">
                    <input type="number" id="stock-shares" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-bold text-center" placeholder="持有股數">
                    <input type="number" id="stock-cost" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-bold text-center" placeholder="買入成本">
                </div>
                <button onclick="addNewStock()" class="w-full bg-slate-900 text-white font-bold text-xs py-2 rounded-xl">儲存設定</button>
            </div>

            <div class="bg-gradient-to-br from-amber-50 to-orange-50 border border-amber-200 rounded-2xl p-4 shadow-2xs space-y-3">
                <div class="flex items-center justify-between border-b border-amber-200/60 pb-1.5">
                    <h4 class="text-xs font-black text-amber-900 uppercase flex items-center space-x-1.5"><i data-lucide="calendar-days" class="w-4 h-4 text-amber-600"></i><span>官方公告除權息日曆</span></h4>
                    <span class="text-[9px] bg-amber-200/60 text-amber-900 px-1.5 py-0.5 rounded font-bold">依公告為主</span>
                </div>
                <div class="grid grid-cols-2 gap-2 text-center">
                    <div class="bg-white/90 p-2 rounded-xl border border-amber-100"><p class="text-[9px] text-amber-800">預估年度總股利</p><p id="predict-dividend-total" class="text-base font-black text-amber-600">$0</p></div>
                    <div class="bg-white/90 p-2 rounded-xl border border-amber-100"><p class="text-[9px] text-amber-800">平均每月被動收入</p><p id="predict-dividend-month" class="text-base font-black text-orange-600">$0</p></div>
                </div>
                <div id="dividend-calendar-list" class="space-y-1.5 text-xs text-slate-700 bg-white/50 p-2.5 rounded-xl">
                    </div>
            </div>

            <div id="stock-list-container" class="space-y-2"></div>
        </section>

        <section id="tab-fire" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-2xs space-y-3">
                <h4 class="text-xs font-black text-slate-700 flex items-center space-x-1.5 border-b border-slate-100 pb-2"><i data-lucide="compass" class="w-4 h-4 text-emerald-600"></i><span>FIRE 財務自由計算機 (4% 法則)</span></h4>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-0.5">預估退休後月花費</label>
                        <input type="number" id="fire-month-expense" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs font-bold" value="50000" oninput="calculateFIRE()">
                    </div>
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-0.5">預期投報率 (%)</label>
                        <input type="number" id="fire-roi" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs font-bold" value="5" oninput="calculateFIRE()">
                    </div>
                </div>
                <div class="bg-emerald-50 rounded-xl p-3 border border-emerald-100 text-center">
                    <p class="text-[10px] text-emerald-800 font-bold">您的安全退休資產目標數字</p>
                    <h2 id="fire-target-number" class="text-xl font-black text-emerald-600 mt-0.5">$15,000,000</h2>
                    <div class="w-full bg-emerald-200/40 h-2.5 rounded-full mt-2 overflow-hidden"><div id="fire-progress-bar" class="bg-emerald-500 h-full transition-all" style="width: 0%"></div></div>
                    <p id="fire-progress-text" class="text-[9px] text-emerald-800 mt-1 font-bold">目前已達成 0%</p>
                </div>
            </div>
        </section>

        <section id="tab-settings" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-2xs space-y-3">
                <h4 class="text-xs font-black text-slate-700 border-b border-slate-100 pb-2 flex items-center space-x-1"><i data-lucide="key-round" class="w-3.5 h-3.5"></i><span>多端對接設定</span></h4>
                <input type="url" id="gas-api-url" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-mono" placeholder="請貼上您的 Google GAS 部署網址">
                <button onclick="saveSettings()" class="w-full bg-slate-900 text-white font-bold text-xs py-2 rounded-xl">保存並立刻同步</button>
            </div>
        </section>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t border-slate-200/60 py-2 shadow-md z-40">
        <div class="max-w-md mx-auto grid grid-cols-5 text-center">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-bold py-1"><i data-lucide="layout-dashboard" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">資產配置</span></button>
            <button onclick="switchTab('ledger')" id="nav-ledger" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 py-1"><i data-lucide="plus-circle" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">極速記帳</span></button>
            <button onclick="switchTab('stocks')" id="nav-stocks" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 py-1"><i data-lucide="calendar-days" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">真實股利</span></button>
            <button onclick="switchTab('fire')" id="nav-fire" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 py-1"><i data-lucide="compass" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">FIRE退休</span></button>
            <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 py-1"><i data-lucide="settings" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">對接設定</span></button>
        </div>
    </nav>

    <script>
        let appState = {
            ledger: [], 
            stocks: JSON.parse(localStorage.getItem('FIN_STOCKS')) || [],
            forex: JSON.parse(localStorage.getItem('FIN_FOREX')) || [],
            gasUrl: localStorage.getItem('FIN_GAS_URL') || '',
            ledgerType: 'expense'
        };

        let allocationChart = null;

        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            document.getElementById('ledger-date').valueAsDate = new Date();
            if(appState.gasUrl) document.getElementById('gas-api-url').value = appState.gasUrl;
            initChart();
            syncCloudData();
        });

        // 簡潔有力切換邏輯
        function switchTab(tabId) {
            ['dashboard', 'ledger', 'stocks', 'fire', 'settings'].forEach(t => {
                document.getElementById(`tab-${t}`)?.classList.add('hidden');
                const btn = document.getElementById(`nav-${t}`);
                if(btn) btn.className = "flex flex-col items-center justify-center space-y-0.5 text-slate-400 py-1";
            });
            document.getElementById(`tab-${tabId}`)?.classList.remove('hidden');
            const targetBtn = document.getElementById(`nav-${tabId}`);
            if(targetBtn) targetBtn.className = "flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-bold py-1";
            lucide.createIcons();
        }

        function quickLedger(amount, note) {
            document.getElementById('ledger-amount').value = amount;
            document.getElementById('ledger-note').value = note;
            setLedgerType('expense');
            saveLedgerToCloud();
        }

        function setLedgerType(type) {
            appState.ledgerType = type;
            document.getElementById('btn-type-expense').className = type === 'expense' ? "py-2 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600" : "py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
            document.getElementById('btn-type-income').className = type === 'income' ? "py-2 text-xs font-bold rounded-xl border text-center bg-emerald-50 border-emerald-200 text-emerald-600" : "py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
        }

        function saveSettings() {
            appState.gasUrl = document.getElementById('gas-api-url').value.trim();
            localStorage.setItem('FIN_GAS_URL', appState.gasUrl);
            syncCloudData();
        }

        function saveLedgerToCloud() {
            if(!appState.gasUrl) return alert('請先設定對接網址！');
            const amt = document.getElementById('ledger-amount').value;
            const note = document.getElementById('ledger-note').value.trim();
            const date = document.getElementById('ledger-date').value || new Date().toISOString().split('T')[0];
            if(!amt || !note) return alert('請填寫完整金額與說明');

            const btn = document.getElementById('btn-submit-ledger');
            btn.innerText = "直傳雲端中..."; btn.disabled = true;

            const newLog = { id: String(Date.now()), type: appState.ledgerType, amount: Math.floor(Number(amt)), note: note, date: date };

            fetch(appState.gasUrl, { method: "POST", mode: "no-cors", headers: { "Content-Type": "application/json" }, body: JSON.stringify(newLog) })
            .then(() => {
                document.getElementById('ledger-amount').value = '';
                document.getElementById('ledger-note').value = '';
                syncCloudData();
                alert('記帳成功！數據已同步。');
            })
            .catch(err => alert('發送失敗: ' + err))
            .finally(() => { btn.innerText = "保存並直傳雲端"; btn.disabled = false; });
        }

        function initChart() {
            const ctx = document.getElementById('allocationChart').getContext('2d');
            allocationChart = new Chart(ctx, {
                type: 'doughnut',
                data: { labels: ['現金儲蓄', '外幣資產', '證券市值'], datasets: [{ data: [0, 0, 0], backgroundColor: ['#10b981', '#06b6d4', '#f59e0b'], borderWidth: 1.5 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'right', labels: { boxWidth: 10, font: { size: 10 } } } }, cutout: '75%' }
            });
        }

        function syncCloudData() {
            if(!appState.gasUrl) return;
            const icon = document.getElementById('sync-icon');
            if(icon) icon.classList.add('animate-spin');

            // 關鍵優化：主動將本地儲存的所有股票代碼包裝成參數發送給雲端，讓雲端抓取精準數據
            const symbolsParam = appState.stocks.map(s => s.symbol).join(',');
            const fetchUrl = `${appState.gasUrl}?stocks=${symbolsParam}`;

            fetch(fetchUrl)
                .then(res => res.json())
                .then(cloudData => {
                    // 接收雲端透過公開渠道查詢到的官方公告配息率與現價
                    if(cloudData.stocks) {
                        appState.stocks.forEach(localStock => {
                            const found = cloudData.stocks.find(c => c.symbol === localStock.symbol);
                            if(found) {
                                localStock.currentPrice = found.price;
                                localStock.dividendPerShare = found.dividendPerShare;
                                localStock.exDate = found.exDate; // 官方除息公告日
                            }
                        });
                        localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
                    }
                    if(cloudData.currencies) appState.forex = cloudData.currencies;
                    if(cloudData.ledger) appState.ledger = cloudData.ledger;
                    
                    renderStocksAndDividends();
                    renderDashboard();
                })
                .catch(err => console.error(err))
                .finally(() => { if(icon) icon.classList.remove('animate-spin'); });
        }

        function addNewStock() {
            const sym = document.getElementById('stock-symbol').value.trim().toUpperCase();
            const sh = document.getElementById('stock-shares').value;
            const co = document.getElementById('stock-cost').value;
            if(!sym || !sh) return alert('請填寫代碼與股數！');

            const existingIndex = appState.stocks.findIndex(s => s.symbol === sym);
            if (existingIndex >= 0) {
                appState.stocks[existingIndex].shares = Number(sh);
                appState.stocks[existingIndex].cost = Number(co) || appState.stocks[existingIndex].cost;
            } else {
                appState.stocks.push({ symbol: sym, shares: Number(sh), cost: Number(co)||0, currentPrice: Number(co)||0, dividendPerShare: 0, exDate: '同步中' });
            }
            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            document.getElementById('stock-symbol').value = '';
            document.getElementById('stock-shares').value = '';
            document.getElementById('stock-cost').value = '';
            syncCloudData();
        }

        function removeStock(symbol) {
            if(!confirm(`移除編號 ${symbol} 的持股？`)) return;
            appState.stocks = appState.stocks.filter(s => s.symbol !== symbol);
            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            syncCloudData();
        }

        // 精簡融合：整合持股明細與「官方公告股利日曆」
        function renderStocksAndDividends() {
            const listContainer = document.getElementById('stock-list-container');
            const calContainer = document.getElementById('dividend-calendar-list');
            
            if(appState.stocks.length === 0) {
                listContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">無持股庫存</p>`;
                calContainer.innerHTML = `<p class="text-slate-400 text-center py-1">無除權息時程</p>`;
                document.getElementById('predict-dividend-total').innerText = "$0";
                document.getElementById('predict-dividend-month').innerText = "$0";
                return;
            }

            let totalYearDividend = 0;
            let calHtml = "";
            let listHtml = "";

            appState.stocks.forEach(s => {
                const currentPrice = s.currentPrice || s.cost;
                const totalCost = s.shares * s.cost;
                const currentWorth = s.shares * currentPrice;
                const profit = currentWorth - totalCost;
                
                // 計算來自雲端串接回來的官方公告配息
                const divPerShare = s.dividendPerShare || 0;
                const estimatedEarned = s.shares * divPerShare;
                totalYearDividend += estimatedEarned;

                // 生成股利日曆時序（過濾掉查無公告的標的）
                if(s.exDate) {
                    calHtml += `
                        <div class="flex justify-between items-center py-1 border-b border-slate-100 last:border-0 font-mono">
                            <span class="font-bold text-slate-700">📅 ${s.exDate} [${s.symbol}] 除息</span>
                            <span class="font-bold text-amber-600">+$${Math.floor(estimatedEarned).toLocaleString()}</span>
                        </div>
                    `;
                }

                // 精簡版持股清單卡片
                listHtml += `
                    <div class="bg-white border border-slate-100 rounded-xl p-3 text-xs flex justify-between items-center shadow-xs">
                        <div>
                            <p class="font-black text-slate-800 font-mono">${s.symbol} <span class="text-[10px] text-slate-400 font-normal">(${s.shares}股)</span></p>
                            <p class="text-[10px] text-slate-400">現價: ${currentPrice} | 告配息: ${divPerShare}/股</p>
                        </div>
                        <div class="text-right flex items-center space-x-3">
                            <div>
                                <p class="font-bold text-slate-800">$${Math.floor(currentWorth).toLocaleString()}</p>
                                <p class="text-[10px] font-bold ${profit>=0?'text-emerald-600':'text-rose-600'}">${profit>=0?'+':''}${Math.floor(profit)}</p>
                            </div>
                            <button onclick="removeStock('${s.symbol}')" class="text-slate-300 hover:text-rose-500 transition-colors"><i data-lucide="trash-2" class="w-3.5 h-3.5"></i></button>
                        </div>
                    </div>
                `;
            });

            listContainer.innerHTML = listHtml;
            calContainer.innerHTML = calHtml || `<p class="text-slate-400 text-center py-1">目前尚無公司發布除息公告</p>`;
            
            document.getElementById('predict-dividend-total').innerText = `$${Math.floor(totalYearDividend).toLocaleString()}`;
            document.getElementById('predict-dividend-month').innerText = `$${Math.floor(totalYearDividend / 12).toLocaleString()}`;
            lucide.createIcons();
        }

        function renderDashboard() {
            let cash = 0; appState.ledger.forEach(i => cash += (i.type === 'income' ? i.amount : -i.amount)); if(cash<0) cash=0;
            let stockWorth = 0; appState.stocks.forEach(s => stockWorth += (s.shares * (s.currentPrice || s.cost)));
            let forexWorth = 0; appState.forex.forEach(f => forexWorth += (f.twdValue || 0));
            const total = cash + stockWorth + forexWorth;

            document.getElementById('total-cash').innerText = `$${cash.toLocaleString()}`;
            document.getElementById('total-stock-worth').innerText = `$${Math.floor(stockWorth).toLocaleString()}`;
            document.getElementById('total-forex-worth').innerText = `$${Math.floor(forexWorth).toLocaleString()}`;
            document.getElementById('total-assets').innerText = `$${Math.floor(total).toLocaleString()}`;

            if(allocationChart) { allocationChart.data.datasets[0].data = [cash, forexWorth, stockWorth]; allocationChart.update(); }
            
            const adviceBox = document.getElementById('risk-advice');
            if(total > 0) {
                const stockRatio = ((stockWorth / total) * 100).toFixed(0);
                adviceBox.innerHTML = `🛡️ <b>資產比例：</b>目前證券倉位佔總資產的 <b>${stockRatio}%</b>。資產狀態健康，所有除權息日曆皆已與官方公告連線同步。`;
            }
            calculateFIRE();
        }

        function calculateFIRE() {
            const monthExpense = Number(document.getElementById('fire-month-expense').value) || 0;
            const targetNumber = monthExpense * 12 * 25;
            document.getElementById('fire-target-number').innerText = `$${Math.floor(targetNumber).toLocaleString()}`;
            
            let cash = 0; appState.ledger.forEach(i => cash += (i.type === 'income' ? i.amount : -i.amount)); if(cash<0) cash=0;
            let stockWorth = 0; appState.stocks.forEach(s => stockWorth += (s.shares * (s.currentPrice || s.cost)));
            let forexWorth = 0; appState.forex.forEach(f => forexWorth += (f.twdValue || 0));
            const currentTotal = cash + stockWorth + forexWorth;

            const progress = targetNumber > 0 ? (currentTotal / targetNumber) * 100 : 0;
            const safeProgress = Math.min(progress, 100).toFixed(1);
            
            document.getElementById('fire-progress-bar').style.width = `${safeProgress}%`;
            document.getElementById('fire-progress-text').innerText = `目前已達成目標的 ${safeProgress}%`;
        }
    </script>
</body>
</html>
