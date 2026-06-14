<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>全球雲端資產與風險管家</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #f8fafc; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-28">

    <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-200/80 px-4 py-3.5 shadow-sm">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div class="flex items-center space-x-2.5">
                <div class="bg-indigo-600 text-white p-2 rounded-xl shadow-md">
                    <i data-lucide="cloud-lightning" class="w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold text-slate-900 tracking-wide">雲端多端無縫管家</h1>
                    <p class="text-[10px] text-indigo-600 font-medium tracking-wider">CROSS-DEVICE CLOUD SYNCHRONIZED</p>
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
                <p class="text-[11px] text-slate-400 font-medium tracking-wider uppercase mb-1">雲端跨裝置總資產淨值 (TWD)</p>
                <h3 id="total-assets" class="text-3xl font-black tracking-tight mb-4 text-transparent bg-clip-text bg-gradient-to-r from-white via-slate-100 to-indigo-200">$0</h3>
                <div class="grid grid-cols-3 gap-2 pt-4 border-t border-slate-800 text-center">
                    <div><p class="text-[10px] text-slate-400 mb-0.5">現金儲蓄</p><p id="total-cash" class="text-sm font-bold text-emerald-400">$0</p></div>
                    <div><p class="text-[10px] text-slate-400 mb-0.5">外幣(折台幣)</p><p id="total-forex-worth" class="text-sm font-bold text-cyan-400">$0</p></div>
                    <div><p class="text-[10px] text-slate-400 mb-0.5">證券市值</p><p id="total-stock-worth" class="text-sm font-bold text-amber-400">$0</p></div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm flex items-center justify-between">
                <div class="flex items-center space-x-3">
                    <div class="bg-indigo-50 text-indigo-600 p-2.5 rounded-xl"><i data-lucide="refresh-cw" class="w-5 h-5" id="sync-icon"></i></div>
                    <div>
                        <h4 class="text-xs font-bold text-slate-800">多裝置無縫雲端同步</h4>
                        <p class="text-[10px] text-slate-400">拉取最新雲端記帳明細與全球匯率</p>
                    </div>
                </div>
                <button onclick="syncCloudData()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-xl text-xs font-bold shadow-md transition-all">同步資料</button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-4">
                <div class="flex items-center justify-between border-b border-slate-50 pb-2">
                    <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1">
                        <i data-lucide="pie-chart" class="w-3.5 h-3.5 text-indigo-500"></i>
                        <span>資產分配比例與風險控制</span>
                    </h4>
                    <span id="risk-level-badge" class="text-[9px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded-md font-bold">尚無數據</span>
                </div>
                <div class="w-full flex justify-center items-center py-2" style="position: relative; height:180px;"><canvas id="allocationChart"></canvas></div>
                <div id="risk-advice" class="bg-slate-50 border border-slate-100/80 rounded-xl p-3 text-[11px] text-slate-500 leading-relaxed">💡 請先設定 API 並點擊同步。</div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-3 flex items-center space-x-1"><i data-lucide="globe" class="w-3.5 h-3.5 text-cyan-500"></i><span>雲端海外帳戶資產</span></h4>
                <div id="forex-list-container" class="space-y-2"><p class="text-xs text-slate-400 text-center py-4">無資料</p></div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1.5 mb-3"><i data-lucide="list-ordered" class="w-4 h-4 text-slate-400"></i><span>雲端即時同步收支流水帳</span></h4>
                <div id="recent-transactions" class="space-y-2.5 max-h-[150px] overflow-y-auto no-scrollbar"></div>
            </div>
        </section>

        <section id="tab-ledger" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3"><i data-lucide="cloud-upload" class="w-4 h-4 text-indigo-500"></i><span>新增記帳明細 (直傳雲端)</span></h4>
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1.5">交易類型</label>
                    <div class="grid grid-cols-2 gap-2">
                        <button type="button" id="btn-type-expense" class="py-2.5 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600 shadow-sm" onclick="setLedgerType('expense')">支出 (-)</button>
                        <button type="button" id="btn-type-income" class="py-2.5 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600" onclick="setLedgerType('income')">收入 (+)</button>
                    </div>
                </div>
                <div>
                    <label for="ledger-amount" class="block text-xs font-semibold text-slate-500 mb-1">金額 (TWD)</label>
                    <input type="number" id="ledger-amount" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm font-bold" placeholder="0">
                </div>
                <div>
                    <label for="ledger-note" class="block text-xs font-semibold text-slate-500 mb-1">摘要說明</label>
                    <input type="text" id="ledger-note" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm" placeholder="例如：午餐、買美金點心">
                </div>
                <div>
                    <label for="ledger-date" class="block text-xs font-semibold text-slate-500 mb-1">交易日期</label>
                    <input type="date" id="ledger-date" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-sm">
                </div>
                <button onclick="saveLedgerToCloud()" id="btn-submit-ledger" class="w-full bg-indigo-600 text-white font-bold text-sm py-3 rounded-xl shadow-lg">發送至雲端試算表</button>
            </div>
        </section>

        <section id="tab-stocks" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-4">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1.5 border-b border-slate-50 pb-2">
                    <i data-lucide="plus-circle" class="w-4 h-4 text-amber-500"></i>
                    <span>新增/調整 證券持股庫存</span>
                </h5>
                <div class="grid grid-cols-3 gap-2">
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-1 font-medium">股票代碼</label>
                        <input type="text" id="stock-symbol" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-black uppercase font-mono text-slate-800" placeholder="2330">
                    </div>
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-1 font-medium">持有股數</label>
                        <input type="number" id="stock-shares" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-bold text-slate-800" placeholder="1000">
                    </div>
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-1 font-medium">平均成本</label>
                        <input type="number" id="stock-cost" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-bold text-slate-800" placeholder="成本">
                    </div>
                </div>
                <button onclick="addNewStock()" class="w-full bg-slate-900 text-white font-bold text-xs py-2.5 rounded-xl shadow-md">保存本機持股設定</button>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <div class="flex items-center justify-between mb-3 border-b border-slate-50 pb-2">
                    <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1.5">
                        <i data-lucide="trending-up" class="w-4 h-4 text-indigo-500"></i>
                        <span>證券資產清單與管理</span>
                    </h5>
                    <span id="stock-total-profit" class="text-xs font-bold text-slate-500">$0</span>
                </div>
                <div id="stock-list-container" class="space-y-3">
                    <p class="text-xs text-slate-400 text-center py-6">無持股明細</p>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1.5 border-b border-slate-50 pb-2">
                    <i data-lucide="pencil-line" class="w-4 h-4 text-cyan-500"></i>
                    <span>快速手動調整外幣現額</span>
                </h5>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-1">外幣幣別</label>
                        <select id="adj-forex-currency" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-bold text-slate-800">
                            <option value="USD">USD - 美金</option>
                            <option value="JPY">JPY - 日圓</option>
                            <option value="EUR">EUR - 歐元</option>
                            <option value="GBP">GBP - 英鎊</option>
                            <option value="KRW">KRW - 韓元</option>
                            <option value="AUD">AUD - 澳幣</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-[10px] text-slate-400 mb-1">調整後新原幣總量</label>
                        <input type="number" id="adj-forex-amount" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-bold" placeholder="例如: 5000">
                    </div>
                </div>
                <button onclick="updateLocalForex()" class="w-full bg-cyan-600 text-white font-bold text-xs py-2 rounded-xl shadow-sm">修正本機外幣快取</button>
                <p class="text-[9px] text-slate-400 text-center">💡 註：外幣最終仍會於雲端同步時以試算表數值為主。</p>
            </div>
        </section>

        <section id="tab-settings" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3"><i data-lucide="key-round" class="w-4 h-4 text-indigo-500"></i><span>跨裝置同步密鑰 (API 設定)</span></h4>
                <p class="text-[11px] text-slate-400 leading-relaxed">💡 <b>如何實現多裝置無縫使用？</b><br>在您的智慧型手機、平板或電腦打開此網頁，貼上同一個下方 GAS URL，所有裝置即可實時讀寫同一個 Google 雲端記帳簿！</p>
                <div>
                    <label for="gas-api-url" class="block text-xs font-semibold text-slate-500 mb-1">您的專屬雲端同步密鑰 (GAS URL)</label>
                    <input type="url" id="gas-api-url" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-xs font-mono" placeholder="https://script.google.com/macros/s/.../exec">
                </div>
                <button onclick="saveSettings()" class="w-full bg-slate-900 text-white font-bold text-sm py-2.5 rounded-xl">啟動多端雲端連結</button>
            </div>
        </section>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t border-slate-200/60 py-2.5 shadow-lg z-40">
        <div class="max-w-md mx-auto grid grid-cols-4 text-center">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold cursor-pointer py-1">
                <i data-lucide="layout-dashboard" class="w-5 h-5 pointer-events-none"></i>
                <span class="text-[10px] pointer-events-none">資產配置</span>
            </button>
            <button onclick="switchTab('ledger')" id="nav-ledger" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="plus-circle" class="w-5 h-5 pointer-events-none"></i>
                <span class="text-[10px] pointer-events-none">記帳</span>
            </button>
            <button onclick="switchTab('stocks')" id="nav-stocks" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="line-chart" class="w-5 h-5 pointer-events-none"></i>
                <span class="text-[10px] pointer-events-none">股票</span>
            </button>
            <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="settings" class="w-5 h-5 pointer-events-none"></i>
                <span class="text-[10px] pointer-events-none">多端對接</span>
            </button>
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
            updateSyncBadge();
            initChart();
            if(appState.gasUrl) { syncCloudData(); } else { renderDashboard(); }
            renderStocks();
        });

        // 修復優化：確實切換顯示，並確保每次切換都重新載入 Lucide SVG 避免消失
        function switchTab(tabId) {
            ['dashboard', 'ledger', 'stocks', 'settings'].forEach(t => {
                const sect = document.getElementById(`tab-${t}`);
                const navBtn = document.getElementById(`nav-${t}`);
                if(sect) sect.classList.add('hidden');
                if(navBtn) navBtn.className = "flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1";
            });

            const targetSect = document.getElementById(`tab-${tabId}`);
            const targetNav = document.getElementById(`nav-${tabId}`);
            if(targetSect) targetSect.classList.remove('hidden');
            if(targetNav) targetNav.className = "flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold cursor-pointer py-1";
            
            lucide.createIcons();
        }

        function setLedgerType(type) {
            appState.ledgerType = type;
            document.getElementById('btn-type-expense').className = type === 'expense' ? "py-2.5 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600 shadow-sm" : "py-2.5 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
            document.getElementById('btn-type-income').className = type === 'income' ? "py-2.5 text-xs font-bold rounded-xl border text-center bg-emerald-50 border-emerald-200 text-emerald-600 shadow-sm" : "py-2.5 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
        }

        function updateSyncBadge() {
            document.getElementById('sync-status-text').innerText = appState.gasUrl ? "雲端已連線" : "未對接雲端";
            document.getElementById('sync-badge').className = appState.gasUrl ? "flex items-center space-x-1 bg-emerald-50 text-emerald-600 px-2.5 py-1 rounded-full text-xs font-medium" : "flex items-center space-x-1 bg-slate-100 text-slate-600 px-2.5 py-1 rounded-full text-xs font-medium";
        }

        function saveSettings() {
            appState.gasUrl = document.getElementById('gas-api-url').value.trim();
            localStorage.setItem('FIN_GAS_URL', appState.gasUrl);
            updateSyncBadge();
            syncCloudData();
        }

        function saveLedgerToCloud() {
            if(!appState.gasUrl) return alert('請先到「多端對接」設定您的雲端密鑰！');
            const amt = document.getElementById('ledger-amount').value;
            const note = document.getElementById('ledger-note').value.trim();
            const date = document.getElementById('ledger-date').value;
            if(!amt || !note) return alert('請填寫完整金額與摘要！');

            const btn = document.getElementById('btn-submit-ledger');
            btn.innerText = "正在同步傳送至雲端...";
            btn.disabled = true;

            const newLog = {
                id: String(Date.now()),
                type: appState.ledgerType,
                amount: Math.floor(Number(amt)),
                note: note,
                date: date
            };

            fetch(appState.gasUrl, {
                method: "POST",
                mode: "no-cors",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(newLog)
            })
            .then(() => {
                document.getElementById('ledger-amount').value = '';
                document.getElementById('ledger-note').value = '';
                alert('雲端記帳成功！正在重整數據...');
                syncCloudData();
                switchTab('dashboard');
            })
            .catch(err => alert('雲端傳送失敗: ' + err))
            .finally(() => {
                btn.innerText = "發送至雲端試算表";
                btn.disabled = false;
            });
        }

        function initChart() {
            const ctx = document.getElementById('allocationChart').getContext('2d');
            allocationChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['現金儲蓄', '外幣資產', '證券市值'],
                    datasets: [{
                        data: [0, 0, 0],
                        backgroundColor: ['#10b981', '#06b6d4', '#f59e0b'],
                        borderWidth: 2,
                        borderColor: '#ffffff'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { position: 'bottom', labels: { boxWidth: 12, font: { size: 11 } } } },
                    cutout: '65%'
                }
            });
        }

        function renderDashboard() {
            let cash = 0;
            appState.ledger.forEach(item => cash += (item.type === 'income' ? item.amount : -item.amount));
            if(cash < 0) cash = 0; 

            let stockWorth = 0;
            appState.stocks.forEach(s => stockWorth += (s.shares * (s.currentPrice || s.cost)));

            let forexWorth = 0;
            appState.forex.forEach(f => forexWorth += (f.twdValue || 0));

            const total = cash + stockWorth + forexWorth;

            document.getElementById('total-cash').innerText = `$${cash.toLocaleString()}`;
            document.getElementById('total-stock-worth').innerText = `$${Math.floor(stockWorth).toLocaleString()}`;
            document.getElementById('total-forex-worth').innerText = `$${Math.floor(forexWorth).toLocaleString()}`;
            document.getElementById('total-assets').innerText = `$${Math.floor(total).toLocaleString()}`;

            if(allocationChart) {
                allocationChart.data.datasets[0].data = [cash, forexWorth, stockWorth];
                allocationChart.update();
            }

            const adviceBox = document.getElementById('risk-advice');
            const badge = document.getElementById('risk-level-badge');
            
            if (total === 0) {
                adviceBox.innerHTML = "💡 正在分析您的資產配置...請先點擊上方同步按鈕以載入數據。";
                badge.className = "text-[9px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded-md font-bold";
                badge.innerText = "尚無數據";
            } else {
                const stockRatio = (stockWorth / total) * 100;
                const cashRatio = (cash / total) * 100;

                if (stockRatio > 60) {
                    badge.className = "text-[9px] bg-rose-50 text-rose-600 px-2 py-0.5 rounded-md font-bold";
                    badge.innerText = "風險評估：高風險配置";
                    adviceBox.innerHTML = `⚠️ <b>資產警訊：</b>您的證券股票資產佔比達 <b>${stockRatio.toFixed(1)}%</b>，屬於偏高風險的「攻擊型」配置。建議定期檢視高波動標的。`;
                } else if (stockRatio > 30) {
                    badge.className = "text-[9px] bg-amber-50 text-amber-600 px-2 py-0.5 rounded-md font-bold";
                    badge.innerText = "風險評估：穩健配置";
                    adviceBox.innerHTML = `⚖️ <b>配置健康：</b>您的證券比率佔 <b>${stockRatio.toFixed(1)}%</b>，屬於理想的「穩健增長型」平衡。`;
                } else {
                    badge.className = "text-[9px] bg-emerald-50 text-emerald-600 px-2 py-0.5 rounded-md font-bold";
                    badge.innerText = "風險評估：保守安全";
                    adviceBox.innerHTML = `🛡️ <b>防守型配置：</b>您的現金與外幣資產佔比高達 <b>${(cashRatio + (forexWorth/total*100)).toFixed(1)}%</b>，抗風險能力極強。`;
                }
            }

            const forexContainer = document.getElementById('forex-list-container');
            if(appState.forex.length === 0) {
                forexContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">無雲端外幣資產資料</p>`;
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

            const recentContainer = document.getElementById('recent-transactions');
            if (appState.ledger.length === 0) {
                recentContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">雲端目前尚無流水帳紀錄</p>`;
            } else {
                recentContainer.innerHTML = appState.ledger.slice().reverse().slice(0, 5).map(item => `
                    <div class="flex items-center justify-between p-2 hover:bg-slate-50 border border-slate-100/50 rounded-xl text-xs">
                        <div>
                            <span class="font-medium text-slate-700 block">${item.note}</span>
                            <span class="text-[9px] text-slate-400">${item.date}</span>
                        </div>
                        <span class="font-bold ${item.type === 'income' ? 'text-emerald-600' : 'text-slate-600'}">${item.type === 'income' ? '+' : '-'}$${item.amount}</span>
                    </div>
                `).join('');
            }
        }

        function addNewStock() {
            const sym = document.getElementById('stock-symbol').value.trim().toUpperCase();
            const sh = document.getElementById('stock-shares').value;
            const co = document.getElementById('stock-cost').value;
            if(!sym || !sh) return alert('請完整填寫股票代碼與股數！');

            const existingIndex = appState.stocks.findIndex(s => s.symbol === sym);
            if (existingIndex >= 0) {
                appState.stocks[existingIndex].shares = Number(sh);
                appState.stocks[existingIndex].cost = Number(co) || appState.stocks[existingIndex].cost;
            } else {
                appState.stocks.push({ symbol: sym, shares: Number(sh), cost: Number(co)||0, currentPrice: Number(co)||0 });
            }

            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            renderStocks();
            renderDashboard();
            document.getElementById('stock-symbol').value = '';
            document.getElementById('stock-shares').value = '';
            document.getElementById('stock-cost').value = '';
            alert(`${sym} 持股庫存調整成功！`);
        }

        function removeStock(symbol) {
            if(!confirm(`確定要將股票代碼 ${symbol} 從您的資產庫存中移除嗎？`)) return;
            appState.stocks = appState.stocks.filter(s => s.symbol !== symbol);
            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            renderStocks();
            renderDashboard();
        }

        function updateLocalForex() {
            const curr = document.getElementById('adj-forex-currency').value;
            const amt = document.getElementById('adj-forex-amount').value;
            if(!amt) return alert('請輸入調整後的外幣新總額！');

            const existingIndex = appState.forex.findIndex(f => f.currency === curr);
            if (existingIndex >= 0) {
                appState.forex[existingIndex].amount = Number(amt);
                const oldRate = appState.forex[existingIndex].twdValue / (appState.forex[existingIndex].amount || 1);
                appState.forex[existingIndex].twdValue = Number(amt) * (oldRate || 30);
            } else {
                appState.forex.push({ currency: curr, amount: Number(amt), twdValue: Number(amt) * 32 });
            }

            localStorage.setItem('FIN_FOREX', JSON.stringify(appState.forex));
            renderDashboard();
            document.getElementById('adj-forex-amount').value = '';
            alert(`${curr} 額度已於本地調整完成！`);
            switchTab('dashboard');
        }

        function renderStocks() {
            const container = document.getElementById('stock-list-container');
            const totalProfitBadge = document.getElementById('stock-total-profit');
            
            if(appState.stocks.length === 0) {
                container.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">無持股明細</p>`;
                totalProfitBadge.innerText = "$0";
                totalProfitBadge.className = "text-xs font-bold text-slate-500";
                return;
            }

            let globalTotalProfit = 0;

            container.innerHTML = appState.stocks.map((s) => {
                const currentPrice = s.currentPrice || s.cost;
                const totalCost = s.shares * s.cost;
                const currentWorth = s.shares * currentPrice;
                const profit = currentWorth - totalCost; 
                const roi = totalCost > 0 ? (profit / totalCost) * 100 : 0; 
                
                globalTotalProfit += profit;

                const isPositive = profit >= 0;
                const textColorClass = isPositive ? 'text-emerald-600' : 'text-rose-600';
                const sign = isPositive ? '+' : '';

                return `
                    <div class="bg-slate-50/80 border border-slate-100/60 rounded-2xl p-3 text-xs flex justify-between items-center shadow-2xs hover:bg-slate-100/50 transition-all">
                        <div class="space-y-0.5 flex-1">
                            <div class="flex items-center space-x-2">
                                <span class="font-black text-slate-800 font-mono text-sm tracking-wide">${s.symbol}</span>
                                <span class="text-[10px] text-slate-400 font-medium bg-slate-200/60 px-1.5 py-0.5 rounded-md">${s.shares} 股</span>
                            </div>
                            <p class="text-[10px] text-slate-400">成本: ${s.cost} | 現價: <span class="font-semibold text-slate-600">${s.currentPrice || '未同步'}</span></p>
                        </div>
                        <div class="text-right space-y-0.5 mr-3">
                            <p class="font-bold text-slate-800 text-sm">$${Math.floor(currentWorth).toLocaleString()}</p>
                            <p class="text-[10px] font-black ${textColorClass}">
                                ${sign}${Math.floor(profit).toLocaleString()} (${sign}${roi.toFixed(2)}%)
                            </p>
                        </div>
                        <button onclick="removeStock('${s.symbol}')" class="text-slate-300 hover:text-rose-500 p-1 transition-colors cursor-pointer">
                            <i data-lucide="trash-2" class="w-4 h-4"></i>
                        </button>
                    </div>
                `;
            }).join('');

            lucide.createIcons();

            const isGlobalPositive = globalTotalProfit >= 0;
            totalProfitBadge.innerText = `總損益: ${isGlobalPositive ? '+' : ''}${Math.floor(globalTotalProfit).toLocaleString()}`;
            totalProfitBadge.className = `text-xs font-black ${isGlobalPositive ? 'text-emerald-600' : 'text-rose-600'}`;
        }

        function syncCloudData() {
            if(!appState.gasUrl) return;
            const icon = document.getElementById('sync-icon');
            if(icon) icon.classList.add('animate-spin');

            fetch(appState.gasUrl)
                .then(res => res.json())
                .then(cloudData => {
                    if(cloudData.stocks) {
                        appState.stocks.forEach(localStock => {
                            const found = cloudData.stocks.find(c => c.symbol === localStock.symbol || c.symbol.endsWith(':' + localStock.symbol));
                            if(found) localStock.currentPrice = Number(found.price);
                        });
                        localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
                    }
                    if(cloudData.currencies) {
                        appState.forex = cloudData.currencies;
                        localStorage.setItem('FIN_FOREX', JSON.stringify(appState.forex));
                    }
                    if(cloudData.ledger) appState.ledger = cloudData.ledger;

                    renderStocks();
                    renderDashboard();
                })
                .catch(err => console.error('連線失敗:', err))
                .finally(() => {
                    if(icon) icon.classList.remove('animate-spin');
                });
        }
    </script>
</body>
</html>
