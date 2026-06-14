<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>全球資產配置與風險管家</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #f8fafc; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-28">

    <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-200/80 px-4 py-3.5 shadow-sm">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div class="flex items-center space-x-2.5">
                <div class="bg-indigo-600 text-white p-2 rounded-xl shadow-md">
                    <i data-lucide="shield-check" class="w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold text-slate-900 tracking-wide">資產配置與風險管家</h1>
                    <p class="text-[10px] text-indigo-600 font-medium tracking-wider">GLOBAL ASSET ALLOCATION & RISK CONTROL</p>
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
            
            <div class="bg-slate-900 rounded-3xl p-6 text-white shadow-xl border border-slate-800 relative overflow-hidden">
                <div class="absolute right-0 top-0 -mt-4 -mr-4 w-24 h-24 bg-gradient-to-br from-indigo-500/20 to-purple-500/0 rounded-full blur-xl"></div>
                <p class="text-[11px] text-slate-400 font-medium tracking-wider uppercase mb-1">目前全球估計總資產 (TWD)</p>
                <h3 id="total-assets" class="text-3xl font-black tracking-tight mb-4 text-transparent bg-clip-text bg-gradient-to-r from-white via-slate-100 to-indigo-200">$0</h3>
                
                <div class="grid grid-cols-3 gap-2 pt-4 border-t border-slate-800 text-center">
                    <div>
                        <p class="text-[10px] text-slate-400 mb-0.5">現金儲蓄</p>
                        <p id="total-cash" class="text-sm font-bold text-emerald-400">$0</p>
                    </div>
                    <div>
                        <p class="text-[10px] text-slate-400 mb-0.5">外幣(折台幣)</p>
                        <p id="total-forex-worth" class="text-sm font-bold text-cyan-400">$0</p>
                    </div>
                    <div>
                        <p class="text-[10px] text-slate-400 mb-0.5">證券市值</p>
                        <p id="total-stock-worth" class="text-sm font-bold text-amber-400">$0</p>
                    </div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center justify-between">
                <div class="flex items-center space-x-3">
                    <div class="bg-indigo-50 text-indigo-600 p-2.5 rounded-xl">
                        <i data-lucide="refresh-cw" class="w-5 h-5" id="sync-icon"></i>
                    </div>
                    <div>
                        <h4 class="text-xs font-bold text-slate-800">一鍵同步雲端資產</h4>
                        <p class="text-[10px] text-slate-400">更新試算表內即時股價與各國匯率</p>
                    </div>
                </div>
                <button onclick="syncCloudData()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-xl text-xs font-bold shadow-md transition-all">
                    同步更新
                </button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-4">
                <div class="flex items-center justify-between border-b border-slate-50 pb-2">
                    <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1">
                        <i data-lucide="pie-chart" class="w-3.5 h-3.5 text-indigo-500"></i>
                        <span>資產分配比例與風險控制</span>
                    </h4>
                    <span id="risk-level-badge" class="text-[9px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded-md font-bold">尚無數據</span>
                </div>
                
                <div class="w-full flex justify-center items-center py-2" style="position: relative; height:180px;">
                    <canvas id="allocationChart"></canvas>
                </div>

                <div id="risk-advice" class="bg-slate-50 border border-slate-100/80 rounded-xl p-3 text-[11px] text-slate-500 leading-relaxed">
                    💡 正在分析您的資產配置...請先點擊上方同步按鈕以載入數據。
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-3 flex items-center space-x-1">
                    <i data-lucide="globe" class="w-3.5 h-3.5 text-cyan-500"></i>
                    <span>海外帳戶與外幣資產</span>
                </h4>
                <div id="forex-list-container" class="space-y-2">
                    <p class="text-xs text-slate-400 text-center py-4">無外幣資料，請在試算表設定後點擊同步</p>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <div class="flex items-center justify-between mb-3.5">
                    <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1.5">
                        <i data-lucide="list-ordered" class="w-4 h-4 text-slate-400"></i>
                        <span>本地新台幣收支流水帳</span>
                    </h4>
                </div>
                <div id="recent-transactions" class="space-y-2.5 max-h-[150px] overflow-y-auto no-scrollbar"></div>
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
                    <input type="text" id="ledger-note" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm" placeholder="例如：午餐、買美金點心">
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
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold"><i data-lucide="layout-dashboard" class="w-5 h-5"></i><span class="text-[10px]">資產配置</span></button>
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

        let allocationChart = null;

        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            document.getElementById('ledger-date').valueAsDate = new Date();
            if(appState.gasUrl) document.getElementById('gas-api-url').value = appState.gasUrl;
            updateSyncBadge();
            initChart(); // 初始化圖表引擎
            renderDashboard();
            renderStocks();
        });

        function switchTab(tabId) {
            ['dashboard', 'ledger', 'stocks', 'settings'].forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                document.getElementById(`nav-${t}`).className = "flex flex-col items-center justify-center space-y-0.5 text-slate-400";
            });
            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            document.getElementById(`nav-${tabId}`).className = "flex flex-col items-center justify-center space-
