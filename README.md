<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>智慧財務與多元貨幣管家</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #f8fafc; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-24">

    <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-200/80 px-4 py-3.5 shadow-sm">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div class="flex items-center space-x-2.5">
                <div class="bg-indigo-600 text-white p-2 rounded-xl shadow-md">
                    <i data-lucide="wallet" class="w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold text-slate-900 tracking-wide">多元貨幣財務管家</h1>
                    <p class="text-[10px] text-indigo-600 font-medium tracking-wider">SECURE MULTI-CURRENCY GLOBAL</p>
                </div>
            </div>
            <div id="sync-badge" class="flex items-center space-x-1 bg-slate-100 text-slate-600 px-2.5 py-1 rounded-full text-xs font-medium">
                <div class="w-1.5 h-1.5 bg-slate-400 rounded-full"></div>
                <span id="sync-status-text">未設定 API</span>
            </div>
        </div>
    </header>

    <main class="max-w-md mx-auto px-4 mt-5">
        
        <section id="tab-dashboard" class="space-y-5">
            <div class="bg-gradient-to-br from-slate-900 via-indigo-950 to-slate-900 rounded-3xl p-6 text-white shadow-xl border border-slate-800">
                <p class="text-xs text-indigo-200/80 font-medium tracking-wider mb-1">估計總資產淨值 (TWD計價)</p>
                <h3 id="total-assets" class="text-3xl font-black tracking-tight mb-4">$0</h3>
                <div class="grid grid-cols-3 gap-2 pt-4 border-t border-white/10 text-center">
                    <div>
                        <p class="text-[10px] text-slate-400 mb-0.5">本機現金</p>
                        <p id="total-cash" class="text-sm font-bold text-emerald-400">$0</p>
                    </div>
                    <div>
                        <p class="text-[10px] text-slate-400 mb-0.5">外幣資產(折台幣)</p>
                        <p id="total-forex-worth" class="text-sm font-bold text-cyan-400">$0</p>
                    </div>
                    <div>
                        <p class="text-[10px] text-slate-400 mb-0.5">股票市值</p>
                        <p id="total-stock-worth" class="text-sm font-bold text-amber-400">$0</p>
                    </div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center justify-between">
                <div>
                    <h4 class="text-xs font-bold text-slate-800">雲端資產一鍵同步</h4>
                    <p class="text-[10px] text-slate-400">同步更新試算表內即時股價與各國匯率</p>
                </div>
                <button onclick="syncCloudData()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-xl text-xs font-bold shadow-md transition-all flex items-center space-x-1">
                    <i data-lucide="refresh-cw" class="w-3.5 h-3.5" id="sync-icon"></i>
                    <span>一鍵同步全球資產</span>
                </button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-3 flex items-center space-x-1">
                    <i data-lucide="globe" class="w-3.5 h-3.5 text-cyan-500"></i>
                    <span>外幣帳戶資產 (即時匯率換算)</span>
                </h4>
                <div id="forex-list-container" class="space-y-2">
                    <p class="text-xs text-slate-400 text-center py-4">目前無外幣資料，請在試算表設定後點擊同步</p>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <div class="flex items-center justify-between mb-3.5">
                    <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5">
                        <i data-lucide="list-ordered" class="w-4 h-4 text-indigo-500"></i>
                        <span>本機新台幣流水帳</span>
                    </h4>
                </div>
                <div id="recent-transactions" class="space-y-2.5 max-h-[180px] overflow-y-auto no-scrollbar"></div>
            </div>
        </section>

        <section id="tab-ledger" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3">
                    <i data-lucide="pen-tool" class="w-4 h-4 text-indigo-500"></i>
                    <span>新增本地收支 (TWD)</span>
                </h4>
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1.5">交易類型</label>
                    <div class="grid grid-cols-2 gap-2">
                        <button type="button" id="btn-type-expense" class="py-2.5 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600 shadow-sm" onclick="setLedgerType('expense')">支出 (-)</button>
                        <button type="button" id="btn-type-income" class="py-2.5 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600" onclick="setLedgerType('income')">收入 (+)</button>
                    </div>
                </div>
                <div>
                    <label for="ledger-amount" class="block text-xs font-semibold text-slate-500 mb-1">金額 (TWD)</label>
                    <div class="relative">
                        <span class="absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 font-bold text-sm">$</span>
                        <input type="number" id="ledger-amount" class="w-full bg-slate-50 border border-slate-200 rounded-xl pl-8 pr-4 py-2.5 text-sm font-bold text-slate-800" placeholder="0">
                    </div>
                </div>
                <div>
                    <label for="ledger-note" class="block text-xs font-semibold text-slate-500 mb-1">摘要說明</label>
                    <input type="text" id="ledger-note" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm" placeholder="例如：晚餐、買美金點心">
                </div>
                <div>
                    <label for="ledger-date" class="block text-xs font-semibold text-slate-500 mb-1">交易日期</label>
                    <input type="date" id="ledger-date" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm">
                </div>
                <button onclick="saveLedgerItem()" class="w-full bg-indigo-600 text-white font-bold text-sm py-3 rounded-xl shadow-lg">安全記錄至本機</button>
            </div>
        </section>

        <section id="tab-stocks" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3.5">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1">
                    <i data-lucide="plus-circle" class="w-3.5 h-3.5 text-amber-500"></i>
                    <span>新增證券持股 (對應試算表)</span>
                </h5>
                <div class="grid grid-cols-3 gap-2">
                    <input type="text" id="stock-symbol" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-2 py-1.5 text-xs font-bold" placeholder="代碼: 2330">
                    <input type="number" id="stock-shares" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-2 py-1.5 text-xs font-bold" placeholder="股數">
                    <input type="number" id="stock-cost" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-2 py-1.5 text-xs font-bold" placeholder="成本價">
                </div>
                <button onclick="addNewStock()" class="w-full bg-slate-800 text-white font-semibold text-xs py-2 rounded-xl">加入庫存明細</button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-3 flex items-center space-x-1">
                    <i data-lucide="pie-chart" class="w-3.5 h-3.5 text-indigo-500"></i>
                    <span>證券資產庫存清單</span>
                </h5>
                <div id="stock-list-container" class="space-y-3">
                    <p class="text-xs text-slate-400 text-center py-6">目前無證券持股，請先同步雲端</p>
                </div>
            </div>
        </section>

        <section id="tab-settings" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3">
                    <i data-lucide="sliders" class="w-4 h-4 text-indigo-500"></i>
                    <span>API 設定</span>
                </h4>
                <div>
                    <label for="gas-api-url" class="block text-xs font-semibold text-slate-500 mb-1">GAS 網頁應用程式 URL</label>
                    <input type="url" id="gas-api-url" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-xs font-mono" placeholder="https://script.google.com/macros/s/.../exec">
                </div>
                <button onclick="saveSettings()" class="w-full bg-slate-900 text-white font-bold text-sm py-2.5 rounded-xl">儲存 API 串接設定</button>
            </div>
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center justify-between">
                <div>
                    <h4 class="text-xs font-bold text-slate-700">清除本機快取</h4>
                    <p class="text-[10px] text-slate-400">刪除所有本機資料</p>
                </div>
                <button onclick="clearAllData()" class="bg-rose-50 text-rose-600 px-3 py-1.5 rounded-xl text-xs font-bold border border-rose-100">完全重置</button>
            </div>
        </section>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t border-slate-200/60 py-2.5 shadow-lg z-40">
        <div class="max-w-md mx-auto grid grid-cols-4 text-center">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold"><i data-lucide="layout-dashboard" class="w-5 h-5"></i><span class="text-[10px]">總覽</span></button>
            <button onclick="switchTab('ledger')" id="nav-ledger" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400"><i data-lucide="plus-circle" class="w-5 h-5"></i><span class="text-[10px]">記帳</span></button>
            <button onclick="switchTab('stocks')" id="nav-stocks" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400"><i data-lucide="line-chart" class="w-5 h-5"></i><span class="text-[10px]">股票</span></button>
            <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400"><i data-lucide="settings" class="w-5 h-5"></i><span class="text-[10px]">設定</span></button>
        </div>
    </nav>

    <script>
        let appState = {
            ledger: JSON.parse(localStorage.getItem('FIN_LEDGER')) || [],
            stocks: JSON.parse(localStorage.getItem('FIN_STOCKS')) || [],
            forex: JSON.parse(localStorage.getItem('FIN_FOREX')) || [],
            gasUrl: localStorage.getItem('FIN_GAS_URL') || '',
            ledgerType: 'expense'
        };

        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            document.getElementById('ledger-date').valueAsDate = new Date();
            if(appState.gasUrl) document.getElementById('gas-api-url').value = appState.gasUrl;
            updateSyncBadge();
            renderDashboard();
            renderStocks();
        });

        function switchTab(tabId) {
            ['dashboard', 'ledger', 'stocks', 'settings'].forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                document.getElementById(`nav-${t}`).className = "flex flex-col items-center justify-center space-y-0.5 text-slate-400";
            });
            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            document.getElementById(`nav-${tabId}`).className = "flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold";
        }

        function setLedgerType(type) {
            appState.ledgerType = type;
            document.getElementById('btn-type-expense').className = type === 'expense' ? "py-2.5 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600 shadow-sm" : "py-2.5 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
            document.getElementById('btn-type-income').className = type === 'income' ? "py-2.5 text-xs font-bold rounded-xl border text-center bg-emerald-50 border-emerald-200 text-emerald-600 shadow-sm" : "py-2.5 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
        }

        function updateSyncBadge() {
            document.getElementById('sync-status-text').innerText = appState.gasUrl ? "API 已就緒" : "未設定 API";
            document.getElementById('sync-badge').className = appState.gasUrl ? "flex items-center space-x-1 bg-emerald-50 text-emerald-600 px-2.5 py-1 rounded-full text-xs font-medium" : "flex items-center space-x-1 bg-slate-100 text-slate-600 px-2.5 py-1 rounded-full text-xs font-medium";
        }

        function saveSettings() {
            appState.gasUrl = document.getElementById('gas-api-url').value.trim();
            localStorage.setItem('FIN_GAS_URL', appState.gasUrl);
            updateSyncBadge();
            alert('設定成功！');
        }

        function saveLedgerItem() {
            const amt = document.getElementById('ledger-amount').value;
            const note = document.getElementById('ledger-note').value.trim();
            const date = document.getElementById('ledger-date').value;
            if(!amt || !note) return alert('請填寫完整金額與摘要！');

            appState.ledger.unshift({
                id: Date.now(), type: appState.ledgerType, amount: Math.floor(Number(amt)), note: note, date: date
            });
            localStorage.setItem('FIN_LEDGER', JSON.stringify(appState.ledger));
            document.getElementById('ledger-amount').value = '';
            document.getElementById('ledger-note').value = '';
            renderDashboard();
            switchTab('dashboard');
        }

        function renderDashboard() {
            let cash = 0;
            appState.ledger.forEach(item => cash += (item.type === 'income' ? item.amount : -item.amount));

            let stockWorth = 0;
            appState.stocks.forEach(s => stockWorth += (s.shares * (s.currentPrice || s.cost)));

            let forexWorth = 0;
            appState.forex.forEach(f => forexWorth += (f.twdValue || 0));

            document.getElementById('total-cash').innerText = `$${cash.toLocaleString()}`;
            document.getElementById('total-stock-worth').innerText = `$${Math.floor(stockWorth).toLocaleString()}`;
            document.getElementById('total-forex-worth').innerText = `$${Math.floor(forexWorth).toLocaleString()}`;
            document.getElementById('total-assets').innerText = `$${Math.floor(cash + stockWorth + forexWorth).toLocaleString()}`;

            // 外幣清單渲染
            const forexContainer = document.getElementById('forex-list-container');
            if(appState.forex.length === 0) {
                forexContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">目前無外幣資料，請先同步雲端</p>`;
            } else {
                forexContainer.innerHTML = appState.forex.map(f => `
                    <div class="flex items-center justify-between p-2 hover:bg-slate-50 border border-slate-100 rounded-xl">
                        <div class="flex items-center space-x-2">
                            <span class="text-xs font-black text-slate-700 font-mono">${f.currency}</span>
                            <span class="text-[11px] text-slate-400">${Number(f.amount).toLocaleString()} 原幣</span>
                        </div>
                        <span class="text-xs font-bold text-cyan-600">≈ NT$${Math.floor(f.twdValue).toLocaleString()}</span>
                    </div>
                `).join('');
            }

            // 流水帳
            const recentContainer = document.getElementById('recent-transactions');
            recentContainer.innerHTML = appState.ledger.slice(0, 5).map(item => `
                <div class="flex items-center justify-between p-2 hover:bg-slate-50 border border-slate-100/50 rounded-xl text-xs">
                    <span class="font-medium text-slate-700">${item.note}</span>
                    <span class="font-bold ${item.type === 'income' ? 'text-emerald-600' : 'text-slate-600'}">${item.type === 'income' ? '+' : '-'}$${item.amount}</span>
                </div>
            `).join('');
        }

        function addNewStock() {
            const sym = document.getElementById('stock-symbol').value.trim().toUpperCase();
            const sh = document.getElementById('stock-shares').value;
            const co = document.getElementById('stock-cost').value;
            if(!sym || !sh) return alert('請完整填寫！');

            appState.stocks.push({ symbol: sym, shares: Number(sh), cost: Number(co)||0, currentPrice: Number(co)||0 });
            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            renderStocks();
            renderDashboard();
            document.getElementById('stock-symbol').value = '';
        }

        function renderStocks() {
            const container = document.getElementById('stock-list-container');
            if(appState.stocks.length === 0) {
                container.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">無持股明細</p>`;
                return;
            }
            container.innerHTML = appState.stocks.map((s, idx) => `
                <div class="bg-slate-50/80 border border-slate-100 rounded-xl p-2.5 text-xs flex justify-between items-center">
                    <div>
                        <p class="font-black text-slate-800 font-mono">${s.symbol} (${s.shares}股)</p>
                        <p class="text-[10px] text-slate-400">現價: ${s.currentPrice || '未同步'}</p>
                    </div>
                    <span class="font-bold text-slate-700">$${Math.floor(s.shares * (s.currentPrice||s.cost)).toLocaleString()}</span>
                </div>
            `).join('');
        }

        function syncCloudData() {
            if(!appState.gasUrl) return alert('請先設定 API 網址！');
            const icon = document.getElementById('sync-icon');
            icon.classList.add('animate-spin');

            fetch(appState.gasUrl)
                .then(res => res.json())
                .then(cloudData => {
                    // 同步股票
                    if(cloudData.stocks) {
                        appState.stocks.forEach(localStock => {
                            const found = cloudData.stocks.find(c => c.symbol === localStock.symbol || c.symbol.endsWith(':' + localStock.symbol));
                            if(found) localStock.currentPrice = Number(found.price);
                        });
                        localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
                    }
                    // 同步外幣匯率
                    if(cloudData.currencies) {
                        appState.forex = cloudData.currencies;
                        localStorage.setItem('FIN_FOREX', JSON.stringify(appState.forex));
                    }
                    renderStocks();
                    renderDashboard();
                    alert('全球資產與當日匯率同步大成功！');
                })
                .catch(err => alert('連線失敗，請檢查試算表權限！'))
                .finally(() => icon.classList.remove('animate-spin'));
        }

        function clearAllData() {
            if(confirm('確定清除？')) {
                localStorage.clear();
                appState = { ledger: [], stocks: [], forex: [], gasUrl: '', ledgerType: 'expense' };
                renderDashboard(); renderStocks(); updateSyncBadge();
            }
        }
    </script>
</body>
</html>
