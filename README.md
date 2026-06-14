<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>智慧財務與 GAS 股票管家</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
            background-color: #f8fafc;
        }
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-24">

    <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-200/80 px-4 py-3.5 shadow-sm">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div class="flex items-center space-x-2.5">
                <div class="bg-indigo-600 text-white p-2 rounded-xl shadow-md shadow-indigo-100">
                    <i data-lucide="wallet" class="w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold text-slate-900 tracking-wide">智慧財務管家</h1>
                    <p class="text-[10px] text-indigo-600 font-medium tracking-wider">SECURE LOCAL LEDGER & STOCK</p>
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
            <div class="bg-gradient-to-br from-slate-900 via-indigo-950 to-slate-900 rounded-3xl p-6 text-white shadow-xl relative overflow-hidden border border-slate-800">
                <div class="absolute -right-6 -bottom-6 w-32 h-32 bg-indigo-500/10 rounded-full blur-2xl"></div>
                <p class="text-xs text-indigo-200/80 font-medium tracking-wider mb-1">估計總資產淨值 (TWD)</p>
                <h3 id="total-assets" class="text-3xl font-black tracking-tight mb-4">$0</h3>
                <div class="grid grid-cols-2 gap-4 pt-4 border-t border-white/10">
                    <div>
                        <p class="text-[11px] text-slate-400 mb-0.5">現金儲蓄</p>
                        <p id="total-cash" class="text-base font-bold text-emerald-400">$0</p>
                    </div>
                    <div>
                        <p class="text-[11px] text-slate-400 mb-0.5">股票證券市值</p>
                        <p id="total-stock-worth" class="text-base font-bold text-amber-400">$0</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3.5">
                <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center space-x-3">
                    <div class="bg-emerald-50 text-emerald-600 p-2.5 rounded-xl">
                        <i data-lucide="trending-up" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <p class="text-[11px] text-slate-400 font-medium">本月總收入</p>
                        <p id="month-income" class="text-base font-bold text-slate-800">$0</p>
                    </div>
                </div>
                <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center space-x-3">
                    <div class="bg-rose-50 text-rose-600 p-2.5 rounded-xl">
                        <i data-lucide="trending-down" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <p class="text-[11px] text-slate-400 font-medium">本月總支出</p>
                        <p id="month-expense" class="text-base font-bold text-slate-800">$0</p>
                    </div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <div class="flex items-center justify-between mb-3.5">
                    <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5">
                        <i data-lucide="list-ordered" class="w-4 h-4 text-indigo-500"></i>
                        <span>最新收支流水帳</span>
                    </h4>
                    <span class="text-[11px] text-slate-400">僅顯示後端格式化資產</span>
                </div>
                <div id="recent-transactions" class="space-y-2.5 max-h-[220px] overflow-y-auto no-scrollbar">
                    <p class="text-xs text-slate-400 text-center py-6">目前尚無記帳紀錄，請先至「記帳」頁籤新增</p>
                </div>
            </div>
        </section>

        <section id="tab-ledger" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3">
                    <i data-lucide="pen-tool" class="w-4 h-4 text-indigo-500"></i>
                    <span>新增收支項目</span>
                </h4>
                
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1.5">交易類型</label>
                    <div class="grid grid-cols-2 gap-2">
                        <button type="button" id="btn-type-expense" class="py-2.5 text-xs font-bold rounded-xl border text-center transition-all bg-rose-50 border-rose-200 text-rose-600 shadow-sm" onclick="setLedgerType('expense')">
                            支出 (-)
                        </button>
                        <button type="button" id="btn-type-income" class="py-2.5 text-xs font-bold rounded-xl border text-center transition-all bg-slate-50 border-slate-200 text-slate-600" onclick="setLedgerType('income')">
                            收入 (+)
                        </button>
                    </div>
                </div>

                <div>
                    <label for="ledger-amount" class="block text-xs font-semibold text-slate-500 mb-1">金額 (TWD)</label>
                    <div class="relative">
                        <span class="absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 font-bold text-sm">$</span>
                        <input type="number" id="ledger-amount" pattern="[0-9]*" inputmode="numeric" class="w-full bg-slate-50/50 border border-slate-200 rounded-xl pl-8 pr-4 py-2.5 text-sm font-bold focus:outline-none focus:border-indigo-500 focus:bg-white transition-all text-slate-800" placeholder="0">
                    </div>
                </div>

                <div>
                    <label for="ledger-note" class="block text-xs font-semibold text-slate-500 mb-1">摘要 / 用途說明</label>
                    <input type="text" id="ledger-note" class="w-full bg-slate-50/50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm focus:outline-none focus:border-indigo-500 focus:bg-white transition-all text-slate-800" placeholder="例如：午餐、薪資、加油">
                </div>

                <div>
                    <label for="ledger-date" class="block text-xs font-semibold text-slate-500 mb-1">交易日期</label>
                    <input type="date" id="ledger-date" class="w-full bg-slate-50/50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm focus:outline-none focus:border-indigo-500 focus:bg-white transition-all text-slate-800">
                </div>

                <button onclick="saveLedgerItem()" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-sm py-3 rounded-xl shadow-lg shadow-indigo-100 transition-all flex items-center justify-center space-x-1.5 mt-2">
                    <i data-lucide="check" class="w-4 h-4"></i>
                    <span>安全記錄至本機</span>
                </button>
            </div>
        </section>

        <section id="tab-stocks" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center justify-between">
                <div>
                    <h4 class="text-sm font-bold text-slate-800">雲端庫存同步</h4>
                    <p class="text-[11px] text-slate-400">一鍵串接試算表抓取即時價</p>
                </div>
                <button onclick="syncCloudStocks()" class="bg-amber-500 hover:bg-amber-600 text-white px-4 py-2 rounded-xl text-xs font-bold shadow-md shadow-amber-100 transition-all flex items-center space-x-1">
                    <i data-lucide="refresh-cw" class="w-3.5 h-3.5" id="sync-icon"></i>
                    <span>一鍵同步雲端股價</span>
                </button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3.5">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1">
                    <i data-lucide="plus-circle" class="w-3.5 h-3.5 text-amber-500"></i>
                    <span>新增證券持股</span>
                </h5>
                <div class="grid grid-cols-3 gap-2">
                    <div>
                        <label class="block text-[10px] text-slate-400 font-medium mb-1">股票代碼</label>
                        <input type="text" id="stock-symbol" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-2 py-1.5 text-xs focus:outline-none font-bold placeholder:font-normal placeholder:text-slate-300" placeholder="例: 2330 / 6881">
                    </div>
                    <div>
                        <label class="block text-[10px] text-slate-400 font-medium mb-1">持有股數</label>
                        <input type="number" id="stock-shares" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-2 py-1.5 text-xs focus:outline-none font-bold" placeholder="0">
                    </div>
                    <div>
                        <label class="block text-[10px] text-slate-400 font-medium mb-1">平均成本</label>
                        <input type="number" id="stock-cost" class="w-full bg-slate-50 border border-slate-200 rounded-lg px-2 py-1.5 text-xs focus:outline-none font-bold" placeholder="0.0">
                    </div>
                </div>
                <button onclick="addNewStock()" class="w-full bg-slate-800 hover:bg-slate-900 text-white font-semibold text-xs py-2 rounded-xl transition-all">
                    加入庫存明細
                </button>
            </div>

            <div id="sync-log-card" class="hidden bg-slate-900 text-slate-200 rounded-2xl p-4 shadow-sm text-xs space-y-1.5 font-mono">
                <div class="flex items-center justify-between border-b border-slate-800 pb-1.5 mb-1">
                    <span class="text-slate-400 font-bold">API 同步日誌</span>
                    <span class="text-[10px] bg-indigo-900 text-indigo-300 px-1.5 py-0.5 rounded">GAS LIVE</span>
                </div>
                <div id="sync-log-content" class="space-y-1 max-h-[100px] overflow-y-auto no-scrollbar"></div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-3 flex items-center space-x-1">
                    <i data-lucide="pie-chart" class="w-3.5 h-3.5 text-indigo-500"></i>
                    <span>證券資產庫存清單</span>
                </h5>
                <div id="stock-list-container" class="space-y-3">
                    <p class="text-xs text-slate-400 text-center py-6">目前無證券持股，請在上表輸入並進行雲端同步</p>
                </div>
            </div>
        </section>

        <section id="tab-settings" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3">
                    <i data-lucide="sliders" class="w-4 h-4 text-indigo-500"></i>
                    <span>API 與系統參數設定</span>
                </h4>
                
                <div>
                    <label for="gas-api-url" class="block text-xs font-semibold text-slate-500 mb-1">Google Apps Script (GAS) 部署網址</label>
                    <input type="url" id="gas-api-url" class="w-full bg-slate-50/50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-xs text-slate-700 focus:outline-none focus:border-indigo-500 focus:bg-white transition-all font-mono" placeholder="https://script.google.com/macros/s/.../exec">
                    <p class="text-[10px] text-slate-400 mt-1 leading-relaxed">請填入您在 Google 試算表部署「網頁應用程式」時獲得的公開 URL。此網址用於向試算表提取最新市價數字。</p>
                </div>

                <button onclick="saveSettings()" class="w-full bg-slate-900 hover:bg-black text-white font-bold text-sm py-2.5 rounded-xl transition-all">
                    儲存 API 串接設定
                </button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center justify-between">
                <div>
                    <h4 class="text-xs font-bold text-slate-700">清除本機快取</h4>
                    <p class="text-[10px] text-slate-400">刪除所有手機內的記帳與股票資料</p>
                </div>
                <button onclick="clearAllData()" class="bg-rose-50 hover:bg-rose-100 text-rose-600 px-3 py-1.5 rounded-xl text-xs font-bold transition-all border border-rose-100">
                    完全重置
                </button>
            </div>
        </section>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t border-slate-200/60 py-2.5 shadow-lg z-40">
        <div class="max-w-md mx-auto grid grid-cols-4 text-center">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold transition-all">
                <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
                <span class="text-[10px]">總覽</span>
            </button>
            <button onclick="switchTab('ledger')" id="nav-ledger" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 transition-all">
                <i data-lucide="plus-circle" class="w-5 h-5"></i>
                <span class="text-[10px]">記帳</span>
            </button>
            <button onclick="switchTab('stocks')" id="nav-stocks" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 transition-all">
                <i data-lucide="line-chart" class="w-5 h-5"></i>
                <span class="text-[10px]">股票證券</span>
            </button>
            <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 transition-all">
                <i data-lucide="settings" class="w-5 h-5"></i>
                <span class="text-[10px]">設定</span>
            </button>
        </div>
    </nav>

    <script>
        // 核心資料狀態庫 (自動讀取 LocalStorage)
        let appState = {
            ledger: JSON.parse(localStorage.getItem('FIN_LEDGER')) || [],
            stocks: JSON.parse(localStorage.getItem('FIN_STOCKS')) || [],
            gasUrl: localStorage.getItem('FIN_GAS_URL') || '',
            ledgerType: 'expense' // 預設新增類型為支出
        };

        // 初始化
        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            // 填入預設日期
            document.getElementById('ledger-date').valueAsDate = new Date();
            // 載入預設設定網址
            if(appState.gasUrl) {
                document.getElementById('gas-api-url').value = appState.gasUrl;
            }
            updateSyncBadge();
            renderDashboard();
            renderStocks();
        });

        // 頁籤切換核心
        function switchTab(tabId) {
            const tabs = ['dashboard', 'ledger', 'stocks', 'settings'];
            tabs.forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                const navBtn = document.getElementById(`nav-${t}`);
                navBtn.classList.remove('text-indigo-600', 'font-semibold');
                navBtn.classList.add('text-slate-400');
            });

            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            const activeNav = document.getElementById(`nav-${tabId}`);
            activeNav.classList.remove('text-slate-400');
            activeNav.classList.add('text-indigo-600', 'font-semibold');
        }

        // 記帳類型控制開關
        function setLedgerType(type) {
            appState.ledgerType = type;
            const expenseBtn = document.getElementById('btn-type-expense');
            const incomeBtn = document.getElementById('btn-type-income');

            if(type === 'expense') {
                expenseBtn.className = "py-2.5 text-xs font-bold rounded-xl border text-center transition-all bg-rose-50 border-rose-200 text-rose-600 shadow-sm";
                incomeBtn.className = "py-2.5 text-xs font-bold rounded-xl border text-center transition-all bg-slate-50 border-slate-200 text-slate-600";
            } else {
                incomeBtn.className = "py-2.5 text-xs font-bold rounded-xl border text-center transition-all bg-emerald-50 border-emerald-200 text-emerald-600 shadow-sm";
                expenseBtn.className = "py-2.5 text-xs font-bold rounded-xl border text-center transition-all bg-slate-50 border-slate-200 text-slate-600";
            }
        }

        // 更新頂部 API 連線燈號
        function updateSyncBadge() {
            const badge = document.getElementById('sync-badge');
            const text = document.getElementById('sync-status-text');
            if(!appState.gasUrl) {
                badge.className = "flex items-center space-x-1 bg-slate-100 text-slate-600 px-2.5 py-1 rounded-full text-xs font-medium";
                text.innerText = "未設定 API";
            } else {
                badge.className = "flex items-center space-x-1 bg-emerald-50 text-emerald-600 px-2.5 py-1 rounded-full text-xs font-medium";
                text.innerText = "API 已就緒";
            }
        }

        // 儲存設定
        function saveSettings() {
            const urlInput = document.getElementById('gas-api-url').value.trim();
            appState.gasUrl = urlInput;
            localStorage.setItem('FIN_GAS_URL', urlInput);
            updateSyncBadge();
            alert('API 串接密鑰網址已儲存，請至股票分頁執行同步！');
        }

        // 儲存記帳項目
        function saveLedgerItem() {
            const amountInput = document.getElementById('ledger-amount').value;
            const noteInput = document.getElementById('ledger-note').value.trim();
            const dateInput = document.getElementById('ledger-date').value;

            if(!amountInput || Number(amountInput) <= 0) {
                alert('請輸入大於 0 的正確金額！');
                return;
            }
            if(!noteInput) {
                alert('請填寫摘要說明（如：晚餐）');
                return;
            }

            const item = {
                id: Date.now(),
                type: appState.ledgerType,
                amount: Math.floor(Number(amountInput)),
                note: noteInput,
                date: dateInput || new Date().toISOString().split('T')[0]
            };

            appState.ledger.unshift(item);
            localStorage.setItem('FIN_LEDGER', JSON.stringify(appState.ledger));

            // 清空輸入欄位
            document.getElementById('ledger-amount').value = '';
            document.getElementById('ledger-note').value = '';
            
            renderDashboard();
            alert('記錄成功！');
            switchTab('dashboard');
        }

        // 渲染主頁面看板與流水帳
        function renderDashboard() {
            let cash = 0;
            let monthlyIncome = 0;
            let monthlyExpense = 0;

            const currentMonthStr = new Date().toISOString().slice(0, 7); // "YYYY-MM"

            appState.ledger.forEach(item => {
                const amt = Number(item.amount);
                const isCurrentMonth = item.date.startsWith(currentMonthStr);

                if(item.type === 'income') {
                    cash += amt;
                    if(isCurrentMonth) monthlyIncome += amt;
                } else {
                    cash -= amt;
                    if(isCurrentMonth) monthlyExpense += amt;
                }
            });

            // 計算股票總市值
            let stockWorth = 0;
            appState.stocks.forEach(s => {
                stockWorth += (Number(s.shares) * (Number(s.currentPrice) || Number(s.cost)));
            });

            // 寫入 UI
            document.getElementById('total-cash').innerText = `$${cash.toLocaleString()}`;
            document.getElementById('total-stock-worth').innerText = `$${Math.floor(stockWorth).toLocaleString()}`;
            document.getElementById('total-assets').innerText = `$${Math.floor(cash + stockWorth).toLocaleString()}`;
            document.getElementById('month-income').innerText = `$${monthlyIncome.toLocaleString()}`;
            document.getElementById('month-expense').innerText = `$${monthlyExpense.toLocaleString()}`;

            // 渲染最新 5 筆流水明細
            const recentContainer = document.getElementById('recent-transactions');
            if(appState.ledger.length === 0) {
                recentContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-6">目前尚無記帳紀錄</p>`;
                return;
            }

            recentContainer.innerHTML = appState.ledger.slice(0, 5).map(item => `
                <div class="flex items-center justify-between p-2.5 hover:bg-slate-50 rounded-xl transition-all border border-slate-100/50">
                    <div class="flex items-center space-x-2.5">
                        <div class="${item.type === 'income' ? 'bg-emerald-50 text-emerald-600' : 'bg-rose-50 text-rose-600'} p-1.5 rounded-lg text-xs font-bold">
                            ${item.type === 'income' ? '收' : '支'}
                        </div>
                        <div>
                            <p class="text-xs font-bold text-slate-800">${item.note}</p>
                            <p class="text-[9px] text-slate-400 font-mono">${item.date}</p>
                        </div>
                    </div>
                    <span class="text-xs font-bold ${item.type === 'income' ? 'text-emerald-600' : 'text-slate-700'}">
                        ${item.type === 'income' ? '+' : '-'}$${Number(item.amount).toLocaleString()}
                    </span>
                </div>
            `).join('');
        }

        // 新增股票庫存
        function addNewStock() {
            const symInput = document.getElementById('stock-symbol').value.trim().toUpperCase();
            const sharesInput = document.getElementById('stock-shares').value;
            const costInput = document.getElementById('stock-cost').value;

            if(!symInput) {
                alert('請輸入股票代碼！');
                return;
            }
            if(!sharesInput || Number(sharesInput) <= 0) {
                alert('請輸入正確股數');
                return;
            }

            // 格式自動標準化
            let finalSymbol = symInput;

            const newStock = {
                symbol: finalSymbol,
                shares: Number(sharesInput),
                cost: Number(costInput) || 0,
                currentPrice: Number(costInput) || 0 // 未同步前先等於成本
            };

            // 檢查是否重複，重複則覆蓋
            const existingIdx = appState.stocks.findIndex(s => s.symbol === finalSymbol);
            if(existingIdx >= 0) {
                appState.stocks[existingIdx] = newStock;
            } else {
                appState.stocks.push(newStock);
            }

            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            
            // 清空
            document.getElementById('stock-symbol').value = '';
            document.getElementById('stock-shares').value = '';
            document.getElementById('stock-cost').value = '';

            renderStocks();
            renderDashboard();
            alert(`股票 ${finalSymbol} 已加入待同步庫存表！`);
        }

        // 渲染股票明細清單
        function renderStocks() {
            const container = document.getElementById('stock-list-container');
            if(appState.stocks.length === 0) {
                container.innerHTML = `<p class="text-xs text-slate-400 text-center py-6">目前無證券持股</p>`;
                return;
            }

            container.innerHTML = appState.stocks.map((s, idx) => {
                const totalCost = s.shares * s.cost;
                const totalWorth = s.shares * (s.currentPrice || s.cost);
                const profit = totalWorth - totalCost;
                const profitRate = totalCost > 0 ? ((profit / totalCost) * 100).toFixed(1) : '0.0';
                
                return `
                    <div class="bg-slate-50/60 border border-slate-100 rounded-xl p-3 space-y-2 relative">
                        <button onclick="deleteStock(${idx})" class="absolute top-2 right-2 text-slate-300 hover:text-rose-500 transition-all">
                            <i data-lucide="trash-2" class="w-3.5 h-3.5"></i>
                        </button>
                        <div class="flex items-center justify-between">
                            <div>
                                <span class="text-xs font-black text-slate-800 tracking-wide font-mono">${s.symbol}</span>
                                <span class="text-[10px] text-slate-400 ml-1.5">${s.shares.toLocaleString()} 股</span>
                            </div>
                            <div class="text-right">
                                <span class="text-xs font-bold text-slate-800">$${Math.floor(totalWorth).toLocaleString()}</span>
                            </div>
                        </div>
                        <div class="grid grid-cols-3 gap-1 pt-1.5 border-t border-slate-200/40 text-[10px] text-slate-500">
                            <div>成本價: <span class="font-bold font-mono">${s.cost}</span></div>
                            <div>現價: <span class="font-bold text-indigo-600 font-mono">${s.currentPrice || '未同步'}</span></div>
                            <div class="text-right font-bold ${profit >= 0 ? 'text-rose-600' : 'text-emerald-600'}">
                                ${profit >= 0 ? '+' : ''}${Math.floor(profit).toLocaleString()} (${profitRate}%)
                            </div>
                        </div>
                    </div>
                `;
            }).join('');
            lucide.createIcons();
        }

        // 刪除持股
        function deleteStock(index) {
            if(confirm('確定要刪除此持股明細嗎？')) {
                appState.stocks.splice(index, 1);
                localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
                renderStocks();
                renderDashboard();
            }
        }

        // 核心密鑰！一鍵異地同步雲端試算表股價
        function syncCloudStocks() {
            if(!appState.gasUrl) {
                alert('錯誤！請先至「設定」頁籤填入您佈署的 Google GAS 網頁應用程式 URL！');
                switchTab('settings');
                return;
            }

            const syncIcon = document.getElementById('sync-icon');
            const logCard = document.getElementById('sync-log-card');
            const logContent = document.getElementById('sync-log-content');

            syncIcon.classList.add('animate-spin');
            logCard.classList.remove('hidden');
            logContent.innerHTML = `<p class="text-amber-400">[發送連線] 正在呼叫遠端雲端試算表網關...</p>`;

            // 發動跨網域請求 (Fetch JSON)
            fetch(appState.gasUrl)
                .then(res => {
                    if(!res.ok) throw new Error('網路回應不成功');
                    return res.json();
                })
                .then(cloudData => {
                    logContent.innerHTML += `<p class="text-emerald-400">[回傳成功] 成功獲取 ${cloudData.length} 筆雲端證券行情資料！</p>`;
                    
                    let matchCount = 0;

                    // 比對本機與雲端代碼
                    appState.stocks.forEach(localStock => {
                        // 彈性模糊比對：不論本機輸入 6881 或是 GTSM:6881，都能聰明對上試算表
                        const found = cloudData.find(cloudItem => {
                            const cSym = String(cloudItem.symbol).toUpperCase().trim();
                            const lSym = String(localStock.symbol).toUpperCase().trim();
                            return cSym === lSym || cSym.endsWith(':' + lSym) || lSym.endsWith(':' + cSym);
                        });

                        if(found) {
                            localStock.currentPrice = Number(found.price);
                            logContent.innerHTML += `<p class="text-slate-300"> ➔ 匹配成功: ${localStock.symbol} 更新市值價為 ${found.price}</p>`;
                            matchCount++;
                        } else {
                            logContent.innerHTML += `<p class="text-amber-300/70"> ➔ 未匹配: ${localStock.symbol} 雲端表內無此代號</p>`;
                        }
                    });

                    // 儲存狀態
                    localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
                    renderStocks();
                    renderDashboard();
                    
                    logContent.innerHTML += `<p class="text-indigo-400 font-bold">[流程終點] 同步結束。共精準對接 ${matchCount} 檔持股明細！</p>`;
                    alert(`雲端價格同步大成功！共更新 ${matchCount} 檔證券市價。`);
                })
                .catch(err => {
                    console.error(err);
                    logContent.innerHTML += `<p class="text-rose-400 font-bold">[連線失敗] 發生錯誤: 請確認 GAS 權限或 URL 網址是否正確。</p>`;
                    alert('串接失敗！請確認您的 Google Apps Script 後台部署時，是否有將「誰有存取權」設為「所有人 (Anyone)」，並重新發布最新版本！');
                })
                .finally(() => {
                    syncIcon.classList.remove('animate-spin');
                });
        }

        // 徹底清除所有本機快取
        function clearAllData() {
            if(confirm('⚠️ 警告！這將永久刪除您儲存在這台手機上的所有收支流水帳、股票清單以及 API 金鑰網址，且不可復原！確定要重置嗎？')) {
                localStorage.clear();
                appState = { ledger: [], stocks: [], gasUrl: '', ledgerType: 'expense' };
                document.getElementById('gas-api-url').value = '';
                updateSyncBadge();
                renderDashboard();
                renderStocks();
                alert('本機緩存已完全抹除。');
                switchTab('dashboard');
            }
        }
    </script>
</body>
</html>
