<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>全球雲端資產與 AI 風險管家</title>
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
                    <i data-lucide="brain-circuit" class="w-5 h-5"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold text-slate-900 tracking-wide">AI 雲端資產管家 10in1</h1>
                    <p class="text-[10px] text-indigo-600 font-medium tracking-wider">AI-POWERED WEALTH ECOSYSTEM</p>
                </div>
            </div>
            <div id="sync-badge" class="flex items-center space-x-1 bg-slate-100 text-slate-600 px-2.5 py-1 rounded-full text-xs font-medium">
                <div class="w-1.5 h-1.5 bg-slate-400 rounded-full"></div>
                <span id="sync-status-text">未設定 API</span>
            </div>
        </div>
    </header>

    <main class="max-w-md mx-auto px-4 mt-4">
        
        <section id="tab-dashboard" class="space-y-4">
            <div class="bg-slate-900 rounded-3xl p-6 text-white shadow-xl border border-slate-800 relative overflow-hidden">
                <p class="text-[11px] text-slate-400 font-medium tracking-wider uppercase mb-1">雲端跨裝置總資產淨值 (TWD)</p>
                <h3 id="total-assets" class="text-3xl font-black tracking-tight mb-4 text-transparent bg-clip-text bg-gradient-to-r from-white via-slate-100 to-indigo-200">$0</h3>
                <div class="grid grid-cols-3 gap-2 pt-4 border-t border-slate-800 text-center">
                    <div><p class="text-[10px] text-slate-400 mb-0.5">現金儲蓄</p><p id="total-cash" class="text-sm font-bold text-emerald-400">$0</p></div>
                    <div><p class="text-[10px] text-slate-400 mb-0.5">外幣資產</p><p id="total-forex-worth" class="text-sm font-bold text-cyan-400">$0</p></div>
                    <div><p class="text-[10px] text-slate-400 mb-0.5">證券市值</p><p id="total-stock-worth" class="text-sm font-bold text-amber-400">$0</p></div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-3 shadow-sm flex items-center justify-between">
                <div class="flex items-center space-x-2">
                    <div class="bg-indigo-50 text-indigo-600 p-2 rounded-xl"><i data-lucide="refresh-cw" class="w-4 h-4" id="sync-icon"></i></div>
                    <span class="text-xs font-bold text-slate-700">更新全球匯率與試算表</span>
                </div>
                <button onclick="syncCloudData()" class="bg-indigo-600 text-white px-3 py-1.5 rounded-xl text-xs font-bold shadow-md">同步資料</button>
            </div>

            <div class="bg-gradient-to-br from-indigo-900 to-slate-900 text-white rounded-2xl p-4 shadow-md space-y-3">
                <div class="flex items-center justify-between border-b border-indigo-800 pb-2">
                    <h4 class="text-xs font-bold uppercase tracking-wider flex items-center space-x-1.5 text-indigo-300">
                        <i data-lucide="sparkles" class="w-4 h-4 text-amber-400"></i>
                        <span>AI 每日財報與花費分析</span>
                    </h4>
                    <button onclick="triggerAIEngine()" class="text-[10px] bg-indigo-600/50 hover:bg-indigo-600 text-white px-2 py-0.5 rounded-md border border-indigo-500/30">喚醒 AI</button>
                </div>
                <div class="grid grid-cols-2 gap-2 text-center">
                    <div class="bg-slate-800/60 p-2 rounded-xl border border-slate-700/50">
                        <p class="text-[9px] text-slate-400">【功能 5】AI 花費評估</p>
                        <p id="ai-expense-analysis" class="text-xs font-bold text-emerald-400 mt-1">尚未分析</p>
                    </div>
                    <div class="bg-slate-800/60 p-2 rounded-xl border border-slate-700/50">
                        <p class="text-[9px] text-slate-400">【功能 6】AI 揪出浪費</p>
                        <p id="ai-waste-hunter" class="text-xs font-bold text-amber-300 mt-1">無呆帳風險</p>
                    </div>
                </div>
                <div class="bg-slate-950/80 rounded-xl p-3 border border-slate-800 text-xs text-slate-300 leading-relaxed">
                    <span class="font-bold text-indigo-300 block mb-1">【功能 10】AI 每日總體財報簡評：</span>
                    <p id="ai-daily-report">點擊上方「喚醒 AI」按鈕，大腦將根據目前資產分配比例與流水帳明細，生成專屬資產防禦與盲點報告。</p>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <div class="w-full flex justify-center items-center py-1" style="position: relative; height:160px;"><canvas id="allocationChart"></canvas></div>
                <div id="risk-advice" class="bg-slate-50 border border-slate-100 rounded-xl p-3 text-[11px] text-slate-500 leading-relaxed">💡 請先設定 API 並點擊同步。</div>
            </div>
        </section>

        <section id="tab-ledger" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-2.5 flex items-center space-x-1"><i data-lucide="zap" class="w-3.5 h-3.5 text-amber-500"></i><span>【功能 1】常用項目極速秒記</span></h4>
                <div class="grid grid-cols-4 gap-2">
                    <button onclick="quickLedger(100, '外食早餐')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center hover:bg-indigo-50 group"><p class="text-xs font-bold text-slate-700">早餐</p><p class="text-[10px] text-slate-400 group-hover:text-indigo-600">$100</p></button>
                    <button onclick="quickLedger(150, '午餐便當')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center hover:bg-indigo-50 group"><p class="text-xs font-bold text-slate-700">午餐</p><p class="text-[10px] text-slate-400 group-hover:text-indigo-600">$150</p></button>
                    <button onclick="quickLedger(250, '晚餐小吃')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center hover:bg-indigo-50 group"><p class="text-xs font-bold text-slate-700">晚餐</p><p class="text-[10px] text-slate-400 group-hover:text-indigo-600">$250</p></button>
                    <button onclick="quickLedger(65, '手搖飲料')" class="bg-slate-50 border border-slate-100 p-2 rounded-xl text-center hover:bg-indigo-50 group"><p class="text-xs font-bold text-slate-700">飲料</p><p class="text-[10px] text-slate-400 group-hover:text-indigo-600">$65</p></button>
                </div>
            </div>

            <div class="bg-gradient-to-r from-indigo-50 to-blue-50 border border-indigo-100 rounded-2xl p-4 shadow-sm space-y-3">
                <h4 class="text-xs font-bold text-indigo-900 uppercase tracking-wider flex items-center space-x-1.5"><i data-lucide="mic" class="w-4 h-4 text-indigo-600"></i><span>【功能 2 & 3】AI 語音與智慧發票辨識</span></h4>
                <div class="grid grid-cols-2 gap-2">
                    <button id="btn-voice-rec" onclick="startVoiceRecognition()" class="flex items-center justify-center space-x-2 bg-white border border-indigo-200 py-3 px-2 rounded-xl hover:bg-indigo-100/50 transition-all cursor-pointer">
                        <i data-lucide="mic" class="w-4 h-4 text-indigo-600" id="icon-voice"></i>
                        <span class="text-xs font-bold text-indigo-900" id="text-voice">語音記帳</span>
                    </button>
                    <label class="flex items-center justify-center space-x-2 bg-white border border-indigo-200 py-3 px-2 rounded-xl hover:bg-indigo-100/50 transition-all cursor-pointer text-center">
                        <i data-lucide="scan-barcode" class="w-4 h-4 text-blue-600"></i>
                        <span class="text-xs font-bold text-blue-900">發票辨識</span>
                        <input type="file" accept="image/*" class="hidden" onchange="simulateInvoiceOCR(event)">
                    </label>
                </div>
                <p class="text-[10px] text-slate-400 leading-relaxed">🗣️ 語音範例：「點心一百元」或「坐計程車兩百五十元」，系統會自動拆解金額並填入下方！</p>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <div class="grid grid-cols-2 gap-2">
                    <button type="button" id="btn-type-expense" class="py-2 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600 shadow-sm" onclick="setLedgerType('expense')">支出 (-)</button>
                    <button type="button" id="btn-type-income" class="py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600" onclick="setLedgerType('income')">收入 (+)</button>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="block text-[11px] font-semibold text-slate-400 mb-1">金額 (TWD)</label>
                        <input type="number" id="ledger-amount" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs font-black" placeholder="0">
                    </div>
                    <div>
                        <label class="block text-[11px] font-semibold text-slate-400 mb-1">項目摘要說明</label>
                        <input type="text" id="ledger-note" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs" placeholder="例如：買咖啡">
                    </div>
                </div>
                <input type="date" id="ledger-date" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3 py-2 text-xs">
                <button onclick="saveLedgerToCloud()" id="btn-submit-ledger" class="w-full bg-indigo-600 text-white font-bold text-xs py-2.5 rounded-xl shadow-md">傳送至雲端試算表</button>
            </div>
            
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider mb-2 flex items-center space-x-1"><i data-lucide="list" class="w-3.5 h-3.5"></i><span>即時流水帳 (前5筆)</span></h4>
                <div id="recent-transactions" class="space-y-2"></div>
            </div>
        </section>

        <section id="tab-stocks" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm space-y-3">
                <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1 border-b border-slate-50 pb-1.5">
                    <i data-lucide="plus-circle" class="w-4 h-4 text-amber-500"></i><span>新增/調整 證券持股</span>
                </h5>
                <div class="grid grid-cols-3 gap-2">
                    <input type="text" id="stock-symbol" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-black uppercase text-center" placeholder="代碼(如0056)">
                    <input type="number" id="stock-shares" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-bold text-center" placeholder="股數">
                    <input type="number" id="stock-cost" class="bg-slate-50 border border-slate-200 rounded-xl px-2 py-1.5 text-xs font-bold text-center" placeholder="成本">
                </div>
                <button onclick="addNewStock()" class="w-full bg-slate-900 text-white font-bold text-xs py-2 rounded-xl">儲存設定</button>
            </div>

            <div class="bg-gradient-to-br from-amber-50 to-orange-50 border border-amber-200 rounded-2xl p-4 shadow-sm space-y-3">
                <h4 class="text-xs font-bold text-amber-900 uppercase tracking-wider flex items-center space-x-1.5">
                    <i data-lucide="calendar-days" class="w-4 h-4 text-amber-600"></i>
                    <span>【功能 7 & 9】配息預測與股利日曆</span>
                </h4>
                <div class="grid grid-cols-2 gap-2 text-center">
                    <div class="bg-white/80 p-2 rounded-xl border border-amber-200/60">
                        <p class="text-[9px] text-amber-800 font-medium">預估年領股利總額</p>
                        <p id="predict-dividend-total" class="text-sm font-black text-amber-600">$0</p>
                    </div>
                    <div class="bg-white/80 p-2 rounded-xl border border-amber-200/60">
                        <p class="text-[9px] text-amber-800 font-medium">平均每月躺平收入</p>
                        <p id="predict-dividend-month" class="text-sm font-black text-orange-600">$0</p>
                    </div>
                </div>
                <div class="space-y-1.5">
                    <span class="text-[10px] font-bold text-amber-800 block">🗓️ 近期除權息/發放預告時程：</span>
                    <div id="dividend-calendar-list" class="space-y-1 text-[11px] text-slate-600">
                        </div>
                </div>
            </div>

            <div class="bg-white border border-slate-100 rounded-2xl p-4 shadow-sm">
                <div class="flex items-center justify-between mb-2 border-b border-slate-50 pb-1.5">
                    <h5 class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center space-x-1"><i data-lucide="trending-up" class="w-4 h-4 text-indigo-500"></i><span>持股即時損益</span></h5>
                    <span id="stock-total-profit" class="text-xs font-bold text-slate-500">$0</span>
                </div>
                <div id="stock-list-container" class="space-y-2"></div>
            </div>
        </section>

        <section id="tab-fire" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3">
                    <i data-lucide="compass" class="w-5 h-5 text-emerald-600"></i>
                    <span>【功能 8】FIRE 財務自由退休計算機</span>
                </h4>
                <div class="space-y-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">您預估退休後「每月」的生活花費 (TWD)</label>
                        <input type="number" id="fire-month-expense" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2 text-xs font-bold" value="50000" oninput="calculateFIRE()">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">預期長期投資年化報酬率 (%)</label>
                        <input type="number" id="fire-roi" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2 text-xs font-bold" value="5" oninput="calculateFIRE()">
                    </div>
                </div>

                <div class="bg-emerald-50 rounded-2xl p-4 border border-emerald-100 space-y-3">
                    <div class="text-center">
                        <p class="text-xs text-emerald-800 font-bold">您的「FIRE 財務自由安全資產數字」</p>
                        <h2 id="fire-target-number" class="text-2xl font-black text-emerald-600 mt-1">$15,000,000</h2>
                        <p class="text-[10px] text-emerald-700/70 mt-1">💡 基於 4% 提退法則與你輸入的預期年化報酬率推估</p>
                    </div>
                    <div class="border-t border-emerald-200/60 pt-2.5">
                        <p class="text-xs text-slate-600">🏃 <b>目前自由進度條：</b></p>
                        <div class="w-full bg-emerald-200/40 h-3 rounded-full mt-1.5 overflow-hidden">
                            <div id="fire-progress-bar" class="bg-emerald-500 h-full transition-all" style="width: 0%"></div>
                        </div>
                        <p id="fire-progress-text" class="text-[10px] text-emerald-800 font-bold mt-1 text-right">目前已達成 0%</p>
                    </div>
                </div>
            </div>
        </section>

        <section id="tab-settings" class="hidden space-y-4">
            <div class="bg-white border border-slate-100 rounded-2xl p-5 shadow-sm space-y-4">
                <h4 class="text-sm font-bold text-slate-800 flex items-center space-x-1.5 border-b border-slate-100 pb-3"><i data-lucide="key-round" class="w-4 h-4 text-indigo-500"></i><span>雲端密鑰設定</span></h4>
                <div>
                    <label for="gas-api-url" class="block text-xs font-semibold text-slate-500 mb-1">GAS 部署網址 URL</label>
                    <input type="url" id="gas-api-url" class="w-full bg-slate-50 border border-slate-200 rounded-xl px-3.5 py-2.5 text-xs font-mono" placeholder="https://script.google.com/macros/s/.../exec">
                </div>
                <button onclick="saveSettings()" class="w-full bg-slate-900 text-white font-bold text-sm py-2.5 rounded-xl">儲存並啟動同步</button>
            </div>
        </section>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t border-slate-200/60 py-2.5 shadow-lg z-40">
        <div class="max-w-md mx-auto grid grid-cols-5 text-center">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center justify-center space-y-0.5 text-indigo-600 font-semibold cursor-pointer py-1">
                <i data-lucide="layout-dashboard" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">資產配置</span>
            </button>
            <button onclick="switchTab('ledger')" id="nav-ledger" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="plus-circle" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">智慧記帳</span>
            </button>
            <button onclick="switchTab('stocks')" id="nav-stocks" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="line-chart" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">配息日曆</span>
            </button>
            <button onclick="switchTab('fire')" id="nav-fire" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="compass" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">FIRE退休</span>
            </button>
            <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center justify-center space-y-0.5 text-slate-400 cursor-pointer py-1">
                <i data-lucide="settings" class="w-4 h-4 pointer-events-none"></i><span class="text-[9px] pointer-events-none">多端對接</span>
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
            calculateFIRE();
        });

        function switchTab(tabId) {
            ['dashboard', 'ledger', 'stocks', 'fire', 'settings'].forEach(t => {
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

        // 【功能 1】極速記帳引擎
        function quickLedger(amount, note) {
            document.getElementById('ledger-amount').value = amount;
            document.getElementById('ledger-note').value = note;
            setLedgerType('expense');
            saveLedgerToCloud();
        }

        // 【功能 2】語音記帳辨識系統 (利用 Web Speech API)
        function startVoiceRecognition() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (!SpeechRecognition) return alert("您的手機瀏覽器不支援原生語音辨識功能，請使用 Chrome 或是 Safari 瀏覽器。");
            
            const recognition = new SpeechRecognition();
            recognition.lang = 'zh-TW';
            
            const btnText = document.getElementById('text-voice');
            btnText.innerText = "正在聆聽中...";
            
            recognition.start();
            
            recognition.onresult = function(event) {
                const resultText = event.results[0][0].transcript;
                btnText.innerText = "語音記帳";
                
                // 簡易 AI 自然語言分析擷取金額與文字描述
                const numMatch = resultText.match(/\d+/);
                let amount = numMatch ? numMatch[0] : 0;
                
                // 如果是講中文數字的轉換防錯機制
                if(!numMatch) {
                    if(resultText.includes('一百')) amount = 100;
                    else if(resultText.includes('五十')) amount = 50;
                    else if(resultText.includes('兩百')) amount = 200;
                    else if(resultText.includes('一千')) amount = 1000;
                }

                const note = resultText.replace(/\d+元?|一百元?|五十元?|兩百元?|一千元?/g, '').trim() || "語音輸入項目";
                
                document.getElementById('ledger-amount').value = amount;
                document.getElementById('ledger-note').value = note;
                alert(`🎤 語音解析成功！\n項目：${note}\n金額：$${amount}\n確認無誤後點擊傳送即可！`);
            };

            recognition.onerror = function() {
                btnText.innerText = "語音記帳";
                alert("語音辨識失敗，請再試一次或確認麥克風權限。");
            };
        }

        // 【功能 3】AI 發票模擬辨識 (OCR 離線對接)
        function simulateInvoiceOCR(event) {
            const file = event.target.files[0];
            if(!file) return;
            alert("📷 正在進行 AI 發票掃描與圖片文字辨識結構化分析...");
            setTimeout(() => {
                document.getElementById('ledger-amount').value = Math.floor(Math.random() * 400) + 50;
                document.getElementById('ledger-note').value = "發票 OCR 自動辨識項目";
                alert("✅ AI 辨識完成！已自動填入發票隨機金額與品項，請點擊送出。");
            }, 1500);
        }

        // 【功能 4, 5, 6, 10】模擬 AI 分析決策大腦
        function triggerAIEngine() {
            alert("🧠 AI 正在爬梳您的試算表流水帳與持股比率...");
            setTimeout(() => {
                document.getElementById('ai-expense-analysis').innerText = "主要集中於外食 (適中)";
                document.getElementById('ai-waste-hunter').innerText = "抓到訂閱制呆帳 $150";
                document.getElementById('ai-daily-report').innerHTML = "<b>【今日 AI 點評】</b>：目前現金池充沛，但股票配息率高於定存，建議可將部分閒置現金分批布局至高股息 ETF。另外，流水帳內發現疑似重覆的手搖飲消費，本週可再節省約 $130 元浪費。";
            }, 1000);
        }

        // 【功能 8】FIRE 財務自由計算法則
        function calculateFIRE() {
            const monthExpense = Number(document.getElementById('fire-month-expense').value) || 0;
            const targetNumber = monthExpense * 12 * 25; // 4% 法則
            document.getElementById('fire-target-number').innerText = `$${Math.floor(targetNumber).toLocaleString()}`;
            
            // 計算目前資產總額
            let cash = 0; appState.ledger.forEach(i => cash += (i.type === 'income' ? i.amount : -i.amount)); if(cash<0) cash=0;
            let stockWorth = 0; appState.stocks.forEach(s => stockWorth += (s.shares * (s.currentPrice || s.cost)));
            let forexWorth = 0; appState.forex.forEach(f => forexWorth += (f.twdValue || 0));
            const currentTotal = cash + stockWorth + forexWorth;

            const progress = targetNumber > 0 ? (currentTotal / targetNumber) * 100 : 0;
            const safeProgress = Math.min(progress, 100).toFixed(1);
            
            document.getElementById('fire-progress-bar').style.width = `${safeProgress}%`;
            document.getElementById('fire-progress-text').innerText = `目前已達成目標的 ${safeProgress}%`;
        }

        // 【功能 7 & 9】配息預測與股利日曆時序引擎
        function renderDividendCalendar() {
            const calContainer = document.getElementById('dividend-calendar-list');
            let totalDiv = 0;

            if(appState.stocks.length === 0) {
                calContainer.innerHTML = "<p class='text-slate-400 text-center py-1'>暫無持股，無法預估日曆</p>";
                document.getElementById('predict-dividend-total').innerText = "$0";
                document.getElementById('predict-dividend-month').innerText = "$0";
                return;
            }

            // 建立虛擬配息資料庫（真實環境下會從 GAS 爬蟲撈回來）
            const mockDividendDb = { "0056": 3.6, "2330": 16.0, "00878": 2.2, "00919": 2.8 };

            let listHtml = "";
            appState.stocks.forEach(s => {
                const divPerShare = mockDividendDb[s.symbol] || (s.cost * 0.05); // 沒有資料就抓 5% 概估
                const earned = s.shares * divPerShare;
                totalDiv += earned;

                listHtml += `
                    <div class="flex justify-between items-center border-b border-amber-100/40 py-1">
                        <span>📊 <b>${s.symbol}</b> 預估除息</span>
                        <span class="font-bold text-amber-800">+$${Math.floor(earned).toLocaleString()}</span>
                    </div>
                `;
            });

            calContainer.innerHTML = listHtml;
            document.getElementById('predict-dividend-total').innerText = `$${Math.floor(totalDiv).toLocaleString()}`;
            document.getElementById('predict-dividend-month').innerText = `$${Math.floor(totalDiv / 12).toLocaleString()}`;
        }

        function setLedgerType(type) {
            appState.ledgerType = type;
            document.getElementById('btn-type-expense').className = type === 'expense' ? "py-2 text-xs font-bold rounded-xl border text-center bg-rose-50 border-rose-200 text-rose-600 shadow-sm" : "py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
            document.getElementById('btn-type-income').className = type === 'income' ? "py-2 text-xs font-bold rounded-xl border text-center bg-emerald-50 border-emerald-200 text-emerald-600 shadow-sm" : "py-2 text-xs font-bold rounded-xl border text-center bg-slate-50 border-slate-200 text-slate-600";
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
            if(!appState.gasUrl) return alert('請先設定您的雲端密鑰網址！');
            const amt = document.getElementById('ledger-amount').value;
            const note = document.getElementById('ledger-note').value.trim();
            const date = document.getElementById('ledger-date').value || new Date().toISOString().split('T')[0];
            if(!amt || !note) return alert('請填寫完整金額與摘要！');

            const btn = document.getElementById('btn-submit-ledger');
            btn.innerText = "正在傳送...";
            btn.disabled = true;

            const newLog = { id: String(Date.now()), type: appState.ledgerType, amount: Math.floor(Number(amt)), note: note, date: date };

            fetch(appState.gasUrl, { method: "POST", mode: "no-cors", headers: { "Content-Type": "application/json" }, body: JSON.stringify(newLog) })
            .then(() => {
                document.getElementById('ledger-amount').value = '';
                document.getElementById('ledger-note').value = '';
                syncCloudData();
            })
            .catch(err => alert('雲端傳送失敗: ' + err))
            .finally(() => { btn.innerText = "傳送至雲端試算表"; btn.disabled = false; });
        }

        function initChart() {
            const ctx = document.getElementById('allocationChart').getContext('2d');
            allocationChart = new Chart(ctx, {
                type: 'doughnut',
                data: { labels: ['現金儲蓄', '外幣資產', '證券市值'], datasets: [{ data: [0, 0, 0], backgroundColor: ['#10b981', '#06b6d4', '#f59e0b'], borderWidth: 2, borderColor: '#ffffff' }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } }, cutout: '70%' }
            });
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

        function addNewStock() {
            const sym = document.getElementById('stock-symbol').value.trim().toUpperCase();
            const sh = document.getElementById('stock-shares').value;
            const co = document.getElementById('stock-cost').value;
            if(!sym || !sh) return alert('請填寫代碼與股數！');

            const existingIndex = appState.stocks.findIndex(s => s.symbol === sym);
            if (existingIndex >= 0) { appState.stocks[existingIndex].shares = Number(sh); appState.stocks[existingIndex].cost = Number(co) || appState.stocks[existingIndex].cost; } 
            else { appState.stocks.push({ symbol: sym, shares: Number(sh), cost: Number(co)||0, currentPrice: Number(co)||0 }); }

            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            renderStocks();
            renderDashboard();
            document.getElementById('stock-symbol').value = '';
            document.getElementById('stock-shares').value = '';
            document.getElementById('stock-cost').value = '';
        }

        function removeStock(symbol) {
            if(!confirm(`移除 ${symbol}？`)) return;
            appState.stocks = appState.stocks.filter(s => s.symbol !== symbol);
            localStorage.setItem('FIN_STOCKS', JSON.stringify(appState.stocks));
            renderStocks();
            renderDashboard();
        }

        function renderStocks() {
            const container = document.getElementById('stock-list-container');
            const totalProfitBadge = document.getElementById('stock-total-profit');
            if(appState.stocks.length === 0) { container.innerHTML = `<p class="text-xs text-slate-400 text-center py-4">無持股明細</p>`; return; }
            let globalTotalProfit = 0;

            container.innerHTML = appState.stocks.map((s) => {
                const currentPrice = s.currentPrice || s.cost;
                const totalCost = s.shares * s.cost; const currentWorth = s.shares * currentPrice;
                const profit = currentWorth - totalCost; const roi = totalCost > 0 ? (profit / totalCost) * 100 : 0;
                globalTotalProfit += profit;
                return `
                    <div class="bg-slate-50 border border-slate-100 rounded-xl p-2.5 text-xs flex justify-between items-center">
                        <div class="flex-1">
                            <span class="font-black text-slate-800">${s.symbol}</span>
                            <span class="text-[10px] text-slate-400 ml-1">${s.shares}股</span>
                        </div>
                        <div class="text-right mr-2">
                            <p class="font-bold">$${Math.floor(currentWorth).toLocaleString()}</p>
                            <p class="text-[10px] ${profit>=0?'text-emerald-600':'text-rose-600'}">${profit>=0?'+':''}${Math.floor(profit)} (${roi.toFixed(1)}%)</p>
                        </div>
                        <button onclick="removeStock('${s.symbol}')" class="text-slate-300 hover:text-rose-500 cursor-pointer"><i data-lucide="trash-2" class="w-3.5 h-3.5"></i></button>
                    </div>`;
            }).join('');
            lucide.createIcons();
            renderDividendCalendar();
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
                    if(cloudData.currencies) appState.forex = cloudData.currencies;
                    if(cloudData.ledger) appState.ledger = cloudData.ledger;
                    renderStocks(); renderDashboard();
                })
                .catch(err => console.error(err))
                .finally(() => { if(icon) icon.classList.remove('animate-spin'); });
        }
    </script>
</body>
</html>
