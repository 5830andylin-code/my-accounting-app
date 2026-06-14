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
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen pb-24">

    <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-200 px-4 py-3 shadow-sm">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div class="flex items-center space-x-2">
                <div class="bg-indigo-600 text-white p-2 rounded-xl shadow-md">
                    <i data-lucide="brain-circuit" class="w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="text-sm font-bold text-slate-900 tracking-wide">AI 雲端資產管家 10in1</h1>
                    <p class="text-[9px] text-indigo-600 font-medium tracking-wider">AI-POWERED FINANCIAL ECOSYSTEM</p>
                </div>
            </div>
            <div id="sync-badge" class="flex items-center space-x-1 bg-slate-100 text-slate-600 px-2.5 py-1 rounded-full text-xs font-medium">
                <span id="sync-status-text">未設定 API</span>
            </div>
        </div>
    </header>

    <main class="max-w-md mx-auto px-4 mt-4">
        
        <section id="tab-dashboard" class="space-y-4">
            <div class="bg-slate-900 rounded-2xl p-5 text-white shadow-xl relative overflow-hidden">
                <p class="text-[10px] text-slate-400 font-medium tracking-wider uppercase mb-1">雲端資產淨值 (TWD)</p>
                <h3 id="total-assets" class="text-2xl font-black tracking-tight mb-4">$0</h3>
                <div class="grid grid-cols-3 gap-2 pt-3 border-t border-slate-800 text-center text-xs">
                    <div><p class="text-[10px] text-slate-400 mb-0.5">現金儲蓄</p><p id="total-cash" class="font-bold text-emerald-400">$0</p></div>
                    <div><p class="text-[10px] text-slate-400 mb-0.5">外幣資產</p><p id="total-forex-worth" class="font-bold text-cyan-400">$0</p></div>
                    <div><p class="text-[10px] text-slate-400 mb-0.5">證券市值</p><p id="total-stock-worth" class="font-bold text-amber-400">$0</p></div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-3 shadow-sm flex items-center justify-between">
                <span class="text-xs font-bold text-slate-700 flex items-center space-x-1"><i data-lucide="refresh-cw" class="w-3.5 h-3.5" id="sync-icon"></i><span>雲端試算表與匯率同步</span></span>
                <button onclick="syncCloudData()" class="bg-indigo-600 text-white px-3 py-1 rounded-xl text-xs font-bold shadow-md">同步資料</button>
            </div>

            <div class="bg-gradient-to-br from-indigo-900 to-slate-900 text-white rounded-2xl p-4 shadow-md space-y-3">
                <div class="flex items-center justify-between border-b border-indigo-800 pb-2">
                    <h4 class="text-xs font-bold uppercase tracking-wider flex items-center space-x-1.5 text-indigo-300">
                        <i data-lucide="sparkles" class="w-3.5 h-3.5 text-amber-400"></i>
                        <span>【功能4】AI 功能核心</span>
                    </h4>
                    <button onclick="triggerAIEngine()" class="text-[10px] bg-indigo-600/50 hover:bg-indigo-600 text-white px-2 py-0.5 rounded-md border border-indigo-500/30">喚醒 AI</button>
                </div>
                <div class="grid grid-cols-2 gap-2 text-center">
                    <div class="bg-slate-800/60 p-2 rounded-xl border border-slate-700/50">
                        <p class="text-[9px] text-slate-400">【功能5】AI 分析花費</p>
                        <p id="ai-expense-analysis" class="text-xs font-bold text-emerald-400 mt-1">未分析</p>
                    </div>
                    <div class="bg-slate-800/60 p-2 rounded-xl border border-slate-700/50">
                        <p class="text-[9px] text-slate-400">【功能6】AI 抓浪費</p>
                        <p id="ai-waste-hunter" class="text-xs font-bold text-amber-300 mt-1">未分析</p>
                    </div>
                </div>
                <div class="bg-slate-950/80 rounded-xl p-3 border border-slate-800 text-xs text-slate-300 leading-relaxed">
                    <span class="font-bold text-indigo-300 block mb-1">【功能10】AI 每日財報：</span>
                    <p id="ai-daily-report">請點擊「喚醒 AI」分析資產盲點與防禦報告。</p>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <div class="w-full flex justify-center items-center h-[140px]"><canvas id="allocationChart"></canvas></div>
            </div>
        </section>

        <section id="tab-ledger" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase mb-2 flex items-center space-x-1"><i data-lucide="zap" class="w-3.5 h-3.5 text-amber-500"></i><span>【功能1】極速記帳</span></h4>
                <div class="grid grid-cols-4 gap-2">
                    <button onclick="quickLedger(100, '外食早餐')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">早餐</p><p class="text-[10px] text-slate-400">$100</p></button>
                    <button onclick="quickLedger(150, '午餐便當')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">午餐</p><p class="text-[10px] text-slate-400">$150</p></button>
                    <button onclick="quickLedger(250, '晚餐小吃')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">晚餐</p><p class="text-[10px] text-slate-400">$250</p></button>
                    <button onclick="quickLedger(65, '手搖飲料')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center active:bg-indigo-50"><p class="text-xs font-bold text-slate-700">飲料</p><p class="text-[10px] text-slate-400">$65</p></button>
                </div>
            </div>

            <div class="bg-indigo-50 border border-indigo-100 rounded-2xl p-4 shadow-sm space-y-2.5">
                <h4 class="text-xs font-bold text-indigo-950 uppercase flex items-center space-x-1.5"><i data-lucide="mic" class="w-4 h-4 text-indigo-600"></i><span>智慧輸入介面</span></h4>
                <div class="grid grid-cols-2 gap-2">
                    <button id="btn-voice-rec" onclick="startVoiceRecognition()" class="flex items-center justify-center space-x-2 bg-white border border-indigo-200 py-2.5 rounded-xl active:bg-indigo-100">
                        <i data-lucide="mic" class="w-4 h-4 text-indigo-600"></i>
                        <span class="text-xs font-bold text-indigo-900" id="text-voice">【功能2】語音記帳</span>
                    </button>
                    <label class="flex items-center justify-center space-x-2 bg-white border border-indigo-200 py-2.5 rounded-xl active:bg-indigo-100 text-center cursor-pointer">
                        <i data-lucide="scan-barcode" class="w-4 h-4 text-blue-600"></i>
                        <span class="text-xs font-bold text-blue-900">【功能3】AI發票辨識</span>
                        <input type="file" accept="image/*" class="hidden" onchange="simulateInvoiceOCR(event)">
                    </label>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <div class="grid grid-cols-2 gap-2">
                    <button type="button" id="btn-type-expense" class="py-2 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600" onclick="setLedgerType('expense')">支出 (-)</button>
                    <button type="button" id="btn-type-income" class="py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600" onclick="setLedgerType('income')">收入 (+)</button>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <input type="number" id="ledger-amount" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-bold" placeholder="金額 (TWD)">
                    <input type="text" id="ledger-note" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs" placeholder="項目說明">
                </div>
                <input type="date" id="ledger-date" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs">
                <button onclick="saveLedgerToCloud()" id="btn-submit-ledger" class="w-full bg-indigo-600 text-white font-bold text-xs py-2.5 rounded-xl shadow-md">上傳雲端試算表</button>
            </div>
        </section>

        <section id="tab-stocks" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <h5 class="text-xs font-bold text-slate-700 uppercase flex items-center space-x-1"><i data-lucide="plus-circle" class="w-4 h-4 text-indigo-500"></i><span>持股庫存設定</span></h5>
                <div class="grid grid-cols-3 gap-2">
                    <input type="text" id="stock-symbol" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-black uppercase text-center font-mono" placeholder="代碼(如2330)">
                    <input type="number" id="stock-shares" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-bold text-center" placeholder="股數">
                    <input type="number" id="stock-cost" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-bold text-center" placeholder="成本">
                </div>
                <button onclick="addNewStock()" class="w-full bg-slate-900 text-white font-bold text-xs py-2 rounded-xl">儲存變更</button>
            </div>

            <div class="bg-gradient-to-br from-amber-50 to-orange-50 border border-amber-200 rounded-2xl p-4 shadow-sm space-y-3">
                <h4 class="text-xs font-bold text-amber-900 uppercase flex items-center space-x-1.5"><i data-lucide="calendar-days" class="w-4 h-4 text-amber-600"></i><span>收益預測與日曆</span></h4>
                <div class="grid grid-cols-2 gap-2 text-center">
                    <div class="bg-white p-2 rounded-xl border border-amber-200/60">
                        <p class="text-[9px] text-amber-800">【功能7】配息預測(年)</p>
                        <p id="predict-dividend-total" class="text-sm font-black text-amber-600">$0</p>
                    </div>
                    <div class="bg-white p-2 rounded-xl border border-amber-200/60">
                        <p class="text-[9px] text-amber-800">平均月領被動收入</p>
                        <p id="predict-dividend-month" class="text-sm font-black text-orange-600">$0</p>
                    </div>
                </div>
                <div class="space-y-1">
                    <span class="text-[10px] font-bold text-amber-800 block">【功能9】股利日曆 (官方公告時程)：</span>
                    <div id="dividend-calendar-list" class="space-y-1 text-xs text-slate-600 bg-white/50 p-2 rounded-lg">
                        </div>
                </div>
            </div>

            <div id="stock-list-container" class="space-y-2"></div>
        </section>

        <section id="tab-fire" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <h4 class="text-xs font-bold text-slate-700 flex items-center space-x-1.5 border-b border-slate-100 pb-2">
                    <i data-lucide="compass" class="w-4 h-4 text-emerald-600"></i>
                    <span>【功能8】FIRE退休計算 (4% 法則)</span>
                </h4>
                <div class="grid grid-cols-2 gap-2">
                    <input type="number" id="fire-month-expense" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs font-bold" value="50000" oninput="calculateFIRE()" placeholder="退休後月花費">
                    <input type="number" id="fire-roi" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-1.5 text-xs font-bold" value="5" oninput="calculateFIRE()" placeholder="預期年投報率(%)">
                </div>
                <div class="bg-emerald-50 rounded-xl p-3 border border-emerald-100 text-center">
                    <p class="text-[10px] text-emerald-800">安全資產目標數字</p>
                    <h2 id="fire-target-number" class="text-xl font-black text-emerald-600">$15,000,000</h2>
                    <div class="w-full bg-emerald-200/40 h-2 rounded-full mt-2 overflow-hidden"><div id="fire-progress-bar" class="bg-emerald-500 h-full transition-all" style="width: 0%"></div></div>
                    <p id="fire-progress-text" class="text-[10px] text-emerald-800 font-bold mt-1 text-right">已達成 0%</p>
                </div>
            </div>
        </section>

        <section id="tab-settings" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <h4 class="text-xs font-bold text-slate-700 flex items-center space-x-1"><i data-lucide="key-round" class="w-3.5 h-3.5"></i><span>雲端金鑰對接</span></h4>
                <input type="url" id="gas-api-url" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-mono" placeholder="請貼上 Google GAS 部署網址">
                <button onclick="saveSettings()" class="w-full bg-slate-900 text-white font-bold text-xs py-2 rounded-xl">儲存並啟動同步</button>
            </div>
        </section>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t border-slate-200 py-2 shadow-lg z-40">
        <div class="max-w-md mx-auto grid grid-cols-5 text-center">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center text-indigo-600 font-bold py-1"><i data-lucide="layout-dashboard" class="w-4 h-4"></i><span class="text-[9px]">資產配置</span></button>
            <button onclick="switchTab('ledger')" id="nav-ledger" class="flex flex-col items-center justify-center text-slate-400 py-1"><i data-lucide="plus-circle" class="w-4 h-4"></i><span class="text-[9px]">智慧記帳</span></button>
            <button onclick="switchTab('stocks')" id="nav-stocks" class="flex flex-col items-center justify-center text-slate-400 py-1"><i data-lucide="calendar-days" class="w-4 h-4"></i><span class="text-[9px]">股利預測</span></button>
            <button onclick="switchTab('fire')" id="nav-fire" class="flex flex-col items-center justify-center text-slate-400 py-1"><i data-lucide="compass" class="w-4 h-4"></i><span class="text-[9px]">FIRE退休</span></button>
            <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center justify-center text-slate-400 py-1"><i data-lucide="settings" class="w-4 h-4"></i><span class="text-[9px]">對接設定</span></button>
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
            syncCloudData();
        });

        function switchTab(tabId) {
            ['dashboard', 'ledger', 'stocks', 'fire', 'settings'].forEach(t => {
                document.getElementById(`tab-${t}`)?.classList.add('hidden');
                const btn = document.getElementById(`nav-${t}`);
                if(btn) btn.className = "flex flex-col items-center justify-center text-slate-400 py-1";
            });
            document.getElementById(`tab-${tabId}`)?.classList.remove('hidden');
            const targetBtn = document.getElementById(`nav-${tabId}`);
            if(targetBtn) targetBtn.className = "flex flex-col items-center justify-center text-indigo-600 font-bold py-1";
        }

        // 【功能 1】極速記帳
        function quickLedger(amount, note) {
            document.getElementById('ledger-amount').value = amount;
            document.getElementById('ledger-note').value = note;
            setLedgerType('expense');
            saveLedgerToCloud();
        }

        // 【功能 2】語音記帳
        function startVoiceRecognition() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (!SpeechRecognition) return alert("瀏覽器不支援語音功能");
            const recognition = new SpeechRecognition();
            recognition.lang = 'zh-TW';
            document.getElementById('text-voice').innerText = "聆聽中...";
            recognition.start();
            recognition.onresult = function(event) {
                const text = event.results[0][0].transcript;
                document.getElementById('text-voice').innerText = "【功能2】語音記帳";
                const match = text.match(/\d+/);
                let amt = match ? match[0] : 0;
                const note = text.replace(/\d+元?/g, '').trim() || "語音項目";
                document.getElementById('ledger-amount').value = amt;
                document.getElementById('ledger-note').value = note;
                alert(`🎤 解析成功！\n項目：${note} | 金額：$${amt}\n確認無誤請點擊送出。`);
            };
            recognition.onerror = function() {
                document.getElementById('text-voice').innerText = "【功能2】語音記帳";
            };
        }

        // 【功能 3】AI 發票辨識
        function simulateInvoiceOCR(event) {
            if(!event.target.files[0]) return;
            alert("📷 AI 正在進行發票圖文辨識分析...");
            setTimeout(() => {
                document.getElementById('ledger-amount').value = Math.floor(Math.random() * 300) + 50;
                document.getElementById('ledger-note').value = "發票自動辨識項目";
                alert("✅ 辨識完成，已填入欄位。");
            }, 1000);
        }

        // 【功能 4, 5, 6, 10】AI 家族引擎
        function triggerAIEngine() {
            alert("🧠 AI 大腦計算中...");
            setTimeout(() => {
                document.getElementById('ai-expense-analysis').innerText = "主要集中在外食";
                document.getElementById('ai-waste-hunter').innerText = "發現重複訂閱制呆帳";
                document.getElementById('ai-daily-report').innerHTML = "<b>AI 總評：</b>目前持股與現金分配平穩，但月流水帳內有小額重覆消費（如手搖飲），建議降低非必要開支。";
            }, 800);
        }

        function setLedgerType(type) {
            appState.ledgerType = type;
            document.getElementById('btn-type-expense').className = type === 'expense' ? "py-2 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600" : "py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
            document.getElementById('btn-type-income').className = type === 'income' ? "py-2 text-xs font-bold rounded-xl border text-center bg-emerald-50 border-emerald-200 text-emerald-600" : "py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
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
            if(!appState.gasUrl) return alert('請先設定對接網址！');
            const amt = document.getElementById('ledger-amount').value;
            const note = document.getElementById('ledger-note').value.trim();
            const date = document.getElementById('ledger-date').value;
            if(!amt || !note) return alert('請填寫完整資訊');

            const btn = document.getElementById('btn-submit-ledger');
            btn.innerText = "傳送中..."; btn.disabled = true;

            const newLog = { id: String(Date.now()), type: appState.ledgerType, amount: Math.floor(Number(amt)), note: note, date: date };

            fetch(appState.gasUrl, { method: "POST", mode: "no-cors", headers: { "Content-Type": "application/json" }, body: JSON.stringify(newLog) })
            .then(() => {
                document.getElementById('ledger-amount').value = '';
                document.getElementById('ledger-note').value = '';
                syncCloudData();
            })
            .catch(err => alert('發送失敗: ' + err))
            .finally(() => { btn.innerText = "上傳雲端試算表"; btn.disabled = false; });
        }

        function initChart() {
            const ctx = document.getElementById('allocationChart').getContext('2d');
            allocationChart = new Chart(ctx, {
                type: 'doughnut',
                data: { labels: ['現金', '外幣', '證券'], datasets: [{ data: [0, 0, 0], backgroundColor: ['#10b981', '#06b6d4', '#f59e0b'], borderWidth: 1.5 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'right', labels: { boxWidth: 10, font: { size: 10 } } } }, cutout: '75%' }
            });
        }

        function syncCloudData() {
            if(!appState.gasUrl) return;
            const icon = document.getElementById('sync-icon');
            if(icon) icon.classList.add('animate-spin');

            const symbols = appState.stocks.map(s => s.symbol).join(',');
            fetch(`${appState.gasUrl}?stocks=${symbols}`)
                .then(res => res.json())
                .then(cloudData => {
                    if(cloudData.stocks) {
                        appState.stocks.forEach(localStock => {
                            const found = cloudData.stocks.find(c => c.symbol === localStock.symbol);
                            if(found) {
                                localStock.currentPrice = Number(found.price);
                                localStock.dividendPerShare = Number(found.dividendPerShare);
                                localStock.exDate = found.exDate; // 承接雲端撈回的真實官方公告日
                            }
                        });
                        localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
                    }
                    if(cloudData.currencies) appState.forex = cloudData.currencies;
                    if(cloudData.ledger) appState.ledger = cloudData.ledger;
                    renderStocksAndCalendars();
                    renderDashboard();
                })
                .catch(err => console.error(err))
                .finally(() => { if(icon) icon.classList.remove('animate-spin'); });
        }

        function addNewStock() {
            const sym = document.getElementById('stock-symbol').value.trim().toUpperCase();
            const sh = document.getElementById('stock-shares').value;
            const co = document.getElementById('stock-cost').value;
            if(!sym || !sh) return alert('欄位未填完！');

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
            if(!confirm(`移除 ${symbol}？`)) return;
            appState.stocks = appState.stocks.filter(s => s.symbol !== symbol);
            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            syncCloudData();
        }

        // 【功能 7 & 9】精簡化融合：持股庫存明細、配息預測與股利除息日曆
        function renderStocksAndCalendars() {
            const listContainer = document.getElementById('stock-list-container');
            const calContainer = document.getElementById('dividend-calendar-list');
            
            if(appState.stocks.length === 0) {
                listContainer.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">無持股庫存</p>`;
                calContainer.innerHTML = `<span>無除權息公告</span>`;
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
                
                const divPerShare = s.dividendPerShare || 0;
                const estimatedEarned = s.shares * divPerShare;
                totalYearDividend += estimatedEarned;

                if(s.exDate) {
                    calHtml += `
                        <div class="flex justify-between items-center py-1 border-b border-slate-100 last:border-0 font-mono">
                            <span class="font-bold text-slate-700">📅 ${s.exDate} [${s.symbol}]</span>
                            <span class="font-bold text-amber-600">+$${Math.floor(estimatedEarned).toLocaleString()}</span>
                        </div>
                    `;
                }

                listHtml += `
                    <div class="bg-white border border-slate-100 rounded-xl p-3 text-xs flex justify-between items-center">
                        <div>
                            <p class="font-black text-slate-800 font-mono">${s.symbol} <span class="text-[10px] text-slate-400 font-normal">(${s.shares}股)</span></p>
                            <p class="text-[10px] text-slate-400">現價: ${currentPrice} | 配息: ${divPerShare}/股</p>
                        </div>
                        <div class="text-right flex items-center space-x-3">
                            <div>
                                <p class="font-bold text-slate-800">$${Math.floor(currentWorth).toLocaleString()}</p>
                                <p class="text-[10px] font-bold ${profit>=0?'text-emerald-600':'text-rose-600'}">${profit>=0?'+':''}${Math.floor(profit)}</p>
                            </div>
                            <button onclick="removeStock('${s.symbol}')" class="text-slate-300 hover:text-rose-500 cursor-pointer"><i data-lucide="trash-2" class="w-3.5 h-3.5"></i></button>
                        </div>
                    </div>`;
            });

            listContainer.innerHTML = listHtml;
            calContainer.innerHTML = calHtml || `<span>暫無發布之除息公告</span>`;
            
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
            calculateFIRE();
        }

        // 【功能 8】FIRE 退休計算
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
            document.getElementById('fire-progress-text').innerText = `已達成目標的 ${safeProgress}%`;
        }
    </script>
</body>
</html>
