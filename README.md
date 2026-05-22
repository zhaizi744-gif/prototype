[celestial_chart.html](https://github.com/user-attachments/files/28132315/celestial_chart.html)

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Celestial Chart - 斎子の神託儀式</title>
    <!-- Tailwind CSS (CDN) -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Noto+Serif+JP:wght@300;500;700&display=swap" rel="stylesheet">
    
    <style>
        body {
            font-family: 'Noto Serif JP', Georgia, 'Hiragino Mincho ProN', 'MS Mincho', serif;
            background: #030408;
            background-image: 
                radial-gradient(circle at 50% 20%, rgba(139, 0, 0, 0.18) 0%, transparent 60%),
                radial-gradient(circle at 10% 80%, rgba(197, 168, 128, 0.05) 0%, transparent 45%);
            color: #f4f6f9;
            overflow-x: hidden;
        }
        .cinzel {
            font-family: 'Cinzel', serif;
        }
        /* 呪術的なゴールド・深紅の境界線 */
        .shrine-border {
            border: 1px solid rgba(197, 168, 128, 0.25);
            box-shadow: 0 0 15px rgba(139, 0, 0, 0.1);
        }
        .shrine-border-focus:focus {
            outline: none;
            border-color: rgba(239, 68, 68, 0.8);
            box-shadow: 0 0 15px rgba(239, 68, 68, 0.3);
        }
        .blend-screen {
            mix-blend-mode: screen;
        }
        /* 揺らめく篝火のようなアニメーション */
        @keyframes bonfire {
            0%, 100% { transform: scale(1) translateY(0) rotate(-2deg); opacity: 0.85; filter: blur(2px); }
            50% { transform: scale(1.08) translateY(-4px) rotate(2deg); opacity: 1; filter: blur(1px); }
        }
        .animate-bonfire {
            animation: bonfire 4s ease-in-out infinite;
        }
        /* サークルの浮遊アニメーション */
        @keyframes float-gentle {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-12px) rotate(4deg); }
        }
        .animate-float {
            animation: float-gentle 7s ease-in-out infinite;
        }
        .animate-float-delayed {
            animation: float-gentle 9s ease-in-out infinite;
            animation-delay: 2s;
        }
        /* 直接編集可能な要素のスタイル */
        [contenteditable="true"] {
            position: relative;
            cursor: pointer;
            border: 1px dashed rgba(229, 195, 152, 0.4) !important;
            padding: 2px 4px;
            border-radius: 4px;
            background: rgba(229, 195, 152, 0.05);
            transition: all 0.2s ease;
        }
        [contenteditable="true"]:focus {
            outline: none;
            border: 1px solid #e5c398 !important;
            background: rgba(229, 195, 152, 0.1);
            box-shadow: 0 0 10px rgba(229, 195, 152, 0.2);
        }
        /* スクロールバーのカスタマイズ */
        .custom-scrollbar::-webkit-scrollbar {
            width: 5px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(3, 4, 8, 0.5);
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #8b0000;
            border-radius: 4px;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between relative pb-8">

    <!-- ナビゲーションヘッダー -->
    <nav class="w-full py-4 px-6 border-b border-red-950 flex flex-col md:flex-row gap-4 justify-between items-center bg-[#05060b]/90 backdrop-blur-md sticky top-0 z-50">
        <div class="flex items-center space-x-3">
            <span class="cinzel text-xl md:text-2xl font-bold tracking-widest text-[#e5c398] flex items-center gap-2">
                <!-- 古代鏡風のSVG -->
                <svg class="w-6 h-6 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <circle cx="12" cy="12" r="10" stroke-width="1.5"></circle>
                    <circle cx="12" cy="12" r="6" stroke-width="1" stroke-dasharray="2,2"></circle>
                    <path d="M12 2v2M12 20v2M2 12h2M20 12h2" stroke-width="1.5"></path>
                </svg>
                Celestial Chart
            </span>
            <span class="text-[10px] tracking-widest bg-red-950 text-[#e5c398] px-2 py-0.5 rounded border border-red-800/30">斎子の神託儀式</span>
        </div>
        
        <!-- 神託エディターコントローラー -->
        <div class="flex items-center gap-4 bg-[#090b14] border border-red-950/60 px-4 py-2 rounded-full shadow-lg">
            <label class="relative inline-flex items-center cursor-pointer select-none">
                <input type="checkbox" id="edit-mode-toggle" class="sr-only peer" onchange="toggleEditMode()">
                <div class="w-9 h-5 bg-gray-800 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:border-gray-300 after:border after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-red-700"></div>
                <span class="ml-2 text-xs font-medium text-[#e5c398] tracking-widest">神託直接編集</span>
            </label>
            <button id="btn-export-json" onclick="openExportModal()" class="hidden text-[10px] bg-red-950/80 hover:bg-red-900 border border-red-800/60 text-[#e5c398] px-3 py-1 rounded-full tracking-widest transition-all flex items-center gap-1">
                <svg class="w-3 h-3 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12"></path>
                </svg>
                <span>変更を抽出</span>
            </button>
        </div>
    </nav>

    <!-- メインコンテンツ -->
    <main class="flex-grow max-w-6xl w-full mx-auto px-4 py-6 md:py-12 flex items-center justify-center relative">

        <!-- ========================================== -->
        <!-- SCREEN 1: 結界解除（儀式の始まりオープニング） -->
        <!-- ========================================== -->
        <div id="seq-opening" class="w-full max-w-xl bg-[#05060b]/95 rounded-3xl shrine-border p-8 md:p-12 text-center relative overflow-hidden transition-all duration-1000 ease-out z-10 flex flex-col items-center">
            <div class="absolute -top-24 -left-24 w-64 h-64 rounded-full border border-red-950/10 animate-spin" style="animation-duration: 60s;"></div>
            
            <span class="cinzel text-xs tracking-[0.3em] text-[#e5c398]/60 mb-2">SACRED RITUAL OPENING</span>
            <h2 class="text-xl md:text-2xl font-bold text-[#e5c398] tracking-[0.2em] mb-8" data-editable-key="opening.title">斎子の神託儀式</h2>
            
            <!-- 結界の鳥居と蝋燭ビジュアル -->
            <div class="relative w-48 h-48 flex items-center justify-center my-6">
                <!-- 鳥居風SVG -->
                <svg class="w-32 h-32 text-red-950/50 absolute" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M3 6h18M5 6v14m14-14v14M8 6V3h8v3M2 20h20"></path>
                </svg>
                <!-- 妖しく揺れる炎（魂球） -->
                <div class="w-16 h-16 rounded-full bg-gradient-to-t from-red-900 to-amber-500/80 filter blur-[3px] animate-bonfire relative flex items-center justify-center shadow-[0_0_30px_rgba(239,68,68,0.4)] cursor-pointer" onclick="startOracleRitual()">
                    <svg class="w-6 h-6 text-red-950" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path>
                    </svg>
                </div>
                <!-- 揺らめく灯火 -->
                <span class="absolute left-4 bottom-10 w-2 h-6 bg-red-800 rounded-full animate-bounce"></span>
                <span class="absolute right-4 bottom-10 w-2 h-6 bg-red-800 rounded-full animate-bounce" style="animation-delay: 0.5s;"></span>
            </div>

            <p class="text-sm text-red-100/70 leading-relaxed font-light mb-8 max-w-sm tracking-wide" data-editable-key="opening.subtitle">
                「これより、そなたの小宇宙を写し取り、運命の重なりを視る儀式を執り行う。覚悟ができたなら、結界を解くが良い…」
            </p>

            <button onclick="startOracleRitual()" class="w-full py-4 rounded-xl bg-gradient-to-r from-red-950 via-[#8b0000] to-red-950 text-[#e5c398] font-bold text-sm hover:opacity-90 transition-all border border-red-800/40 shadow-[0_0_15px_rgba(139,0,0,0.4)] tracking-widest flex justify-center items-center gap-2">
                <svg class="w-4 h-4 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9"></path>
                </svg>
                <span>結界を解除し、入室する</span>
            </button>
        </div>

        <!-- ========================================== -->
        <!-- SCREEN 2: 神託儀式（チャット風対話式入力画面） -->
        <!-- ========================================== -->
        <div id="shrine-gate" class="w-full max-w-2xl bg-[#070912]/95 rounded-3xl shrine-border p-6 md:p-10 relative overflow-hidden transition-all duration-1000 ease-out z-10 hidden opacity-0">
            <!-- 呪術の魔法陣背景 -->
            <div class="absolute -top-32 -left-32 w-80 h-80 rounded-full border border-red-950/20 animate-spin" style="animation-duration: 40s;"></div>
            <div class="absolute -bottom-32 -right-32 w-96 h-96 rounded-full border border-red-950/25 animate-spin" style="animation-duration: 60s; animation-direction: reverse;"></div>
            
            <!-- 斎子のアバター演出 -->
            <div class="flex flex-col items-center text-center mb-8 relative z-10">
                <div class="relative w-24 h-24 rounded-full flex items-center justify-center bg-gradient-to-b from-[#1a0b12] to-[#04060c] border-2 border-[#e5c398]/40 shadow-[0_0_25px_rgba(139,0,0,0.5)]">
                    <div class="absolute inset-1.5 rounded-full border border-dashed border-red-900/40 animate-spin" style="animation-duration: 20s;"></div>
                    <svg class="w-14 h-14 text-red-500 animate-bonfire" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 2L4 6c0 6.63 8 13 8 13s8-6.37 8-13L12 2z"></path>
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M12 6a3 3 0 100 6 3 3 0 000-6z"></path>
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M12 12c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"></path>
                        <circle cx="12" cy="9" r="1.5" fill="#e5c398"></circle>
                    </svg>
                    <span class="absolute -left-2 top-1/2 w-2 h-4 bg-orange-600 rounded-full animate-pulse filter blur-[1px]"></span>
                    <span class="absolute -right-2 top-1/2 w-2 h-4 bg-orange-600 rounded-full animate-pulse filter blur-[1px]"></span>
                </div>
                <h2 class="text-sm tracking-[0.2em] text-[#e5c398] font-bold mt-4" data-editable-key="dialogue.deity_name">古代の神託者・斎子（Saiko）</h2>
                <div class="h-0.5 w-16 bg-gradient-to-r from-transparent via-red-800 to-transparent my-2"></div>
            </div>

            <!-- 斎子の語り掛けバルーン -->
            <div class="bg-[#0b0e1b] border border-red-950/80 rounded-2xl p-5 md:p-6 mb-8 relative">
                <div class="absolute -top-3 left-1/2 -translate-x-1/2 w-6 h-6 bg-[#0b0e1b] border-l border-t border-red-950/80 rotate-45"></div>
                <p id="deity-speech" class="text-sm md:text-base leading-relaxed text-red-100 tracking-wide font-light min-h-[64px]" data-editable-key="dialogue.speech_current">
                </p>
            </div>

            <!-- 1問ずつ入力する対話フォーム -->
            <div id="mystic-form" class="space-y-6 relative z-10">
                <!-- ステップ0: 名前入力 -->
                <div id="step-name" class="input-step transition-all duration-500">
                    <label class="block text-xs text-gray-400 tracking-widest mb-2 text-center" data-editable-key="step_labels.name">そなたの呼び名（任意）</label>
                    <input type="text" id="input-username" placeholder="例：今日も素敵な斎子さん" 
                           class="w-full bg-[#04060c] text-white p-4 rounded-xl border border-red-950/60 shrine-border-focus text-center font-bold tracking-widest transition-all">
                </div>

                <!-- ステップ1: 生年月日（分割セレクトボックス） -->
                <div id="step-birthdate" class="input-step hidden transition-all duration-500">
                    <label class="block text-xs text-gray-400 tracking-widest mb-3 text-center" data-editable-key="step_labels.birthdate">天と地を結びし【生年月日】</label>
                    <div class="grid grid-cols-3 gap-3">
                        <div>
                            <span class="block text-[10px] text-gray-500 text-center mb-1">生年 (西暦)</span>
                            <select id="input-birth-year" class="w-full bg-[#04060c] text-[#e5c398] p-4 rounded-xl border border-red-950/60 shrine-border-focus text-center font-bold tracking-widest transition-all">
                            </select>
                        </div>
                        <div>
                            <span class="block text-[10px] text-gray-500 text-center mb-1">生まれ月</span>
                            <select id="input-birth-month" class="w-full bg-[#04060c] text-[#e5c398] p-4 rounded-xl border border-red-950/60 shrine-border-focus text-center font-bold tracking-widest transition-all">
                            </select>
                        </div>
                        <div>
                            <span class="block text-[10px] text-gray-500 text-center mb-1">生まれ日</span>
                            <select id="input-birth-day" class="w-full bg-[#04060c] text-[#e5c398] p-4 rounded-xl border border-red-950/60 shrine-border-focus text-center font-bold tracking-widest transition-all">
                            </select>
                        </div>
                    </div>
                </div>

                <!-- ステップ2: 出生時間 -->
                <div id="step-birthtime" class="input-step hidden transition-all duration-500">
                    <label class="block text-xs text-gray-400 tracking-widest mb-2 text-center" data-editable-key="step_labels.birthtime">この地に産声を上げし【時刻】</label>
                    <input type="time" id="input-birthtime" value="06:00"
                           class="w-full bg-[#04060c] text-[#e5c398] p-4 rounded-xl border border-red-950/60 shrine-border-focus text-center font-bold tracking-widest transition-all">
                    <p class="text-center text-[10px] text-gray-500 mt-2">※分からぬ場合は、そのまま進むがよい</p>
                </div>

                <!-- ステップ3: 性別 -->
                <div id="step-gender" class="input-step hidden transition-all duration-500">
                    <label class="block text-xs text-gray-400 tracking-widest mb-2 text-center" data-editable-key="step_labels.gender">天運の波を定める【陰陽の性】</label>
                    <div class="grid grid-cols-2 gap-4">
                        <button id="btn-gender-f" onclick="selectMysticGender('F')" 
                                class="py-4 rounded-xl bg-red-950/20 text-[#e5c398] border border-[#e5c398]/50 font-bold hover:bg-red-950/40 transition-all shadow-md flex items-center justify-center gap-2">
                            <svg class="w-4 h-4 text-pink-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <circle cx="12" cy="10" r="5" stroke-width="2"></circle>
                                <path d="M12 15v6M9 18h6" stroke-width="2"></path>
                            </svg>
                            女性
                        </button>
                        <button id="btn-gender-m" onclick="selectMysticGender('M')" 
                                class="py-4 rounded-xl bg-[#04060c] text-gray-500 border border-red-950/60 font-bold hover:bg-red-950/20 transition-all flex items-center justify-center gap-2">
                            <svg class="w-4 h-4 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <circle cx="10" cy="14" r="5" stroke-width="2"></circle>
                                <path d="M14 10l5-5M14 5h5v5" stroke-width="2"></path>
                            </svg>
                            男性
                        </button>
                    </div>
                </div>

                <!-- 操作ボタン類 -->
                <div class="flex justify-between items-center pt-6 border-t border-red-950/30 mt-8 gap-4">
                    <button id="btn-back" onclick="previousStep()" class="text-xs text-gray-500 hover:text-[#e5c398] transition-all py-2 px-4 invisible flex items-center gap-1">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"></path>
                        </svg>
                        前に戻る
                    </button>
                    <button id="btn-next" onclick="nextStep()" 
                            class="flex-1 py-4 rounded-xl bg-gradient-to-r from-red-950 via-[#8b0000] to-red-950 text-[#e5c398] font-bold text-sm hover:opacity-90 transition-all border border-red-800/40 shadow-lg tracking-widest flex justify-center items-center gap-2">
                        <span>次なる問いへ</span>
                        <span id="next-icon">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path>
                            </svg>
                        </span>
                    </button>
                </div>
            </div>

            <!-- プログレスバー -->
            <div class="mt-8 flex justify-center gap-3">
                <span id="dot-0" class="w-3.5 h-3.5 rounded-full bg-red-600 shadow-[0_0_8px_rgba(239,68,68,0.8)] transition-all duration-300"></span>
                <span id="dot-1" class="w-3.5 h-3.5 rounded-full bg-gray-800 transition-all duration-300"></span>
                <span id="dot-2" class="w-3.5 h-3.5 rounded-full bg-gray-800 transition-all duration-300"></span>
                <span id="dot-3" class="w-3.5 h-3.5 rounded-full bg-gray-800 transition-all duration-300"></span>
            </div>
        </div>

        <!-- ========================================== -->
        <!-- SCREEN 3: 魂の五行錬成（儀式ローディング画面） -->
        <!-- ========================================== -->
        <div id="seq-ritual" class="w-full max-w-xl bg-[#05060b]/98 rounded-3xl shrine-border p-8 md:p-12 text-center relative overflow-hidden transition-all duration-1000 ease-out z-10 hidden opacity-0 flex flex-col items-center">
            <span class="cinzel text-xs tracking-[0.3em] text-red-500/80 mb-2 animate-pulse">SACRED RITUAL IN PROGRESS</span>
            <h2 class="text-lg font-bold text-[#e5c398] tracking-[0.2em] mb-8" data-editable-key="ritual.loading_title">魂の五行錬成中</h2>

            <div class="relative w-56 h-56 flex items-center justify-center my-6">
                <div class="absolute w-48 h-48 border border-dashed border-[#e5c398]/30 rounded-full animate-spin" style="animation-duration: 25s;"></div>
                <div class="absolute w-36 h-36 border border-red-900/40 rounded-full animate-spin" style="animation-duration: 15s; animation-direction: reverse;"></div>
                
                <div class="relative w-16 h-16 rounded-full bg-red-950 border border-[#e5c398]/30 flex items-center justify-center shadow-[0_0_20px_rgba(139,0,0,0.6)]">
                    <svg class="w-6 h-6 text-red-500 animate-pulse" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"></path>
                    </svg>
                </div>

                <!-- 浮遊・集束する五行の粒子 -->
                <span id="load-wood" class="absolute top-4 w-6 h-6 rounded-full bg-green-700/60 flex items-center justify-center text-[9px] text-white border border-green-400 opacity-20 transition-all duration-500">木</span>
                <span id="load-fire" class="absolute right-4 top-1/3 w-6 h-6 rounded-full bg-red-700/60 flex items-center justify-center text-[9px] text-white border border-red-400 opacity-20 transition-all duration-500">火</span>
                <span id="load-earth" class="absolute right-12 bottom-4 w-6 h-6 rounded-full bg-yellow-700/60 flex items-center justify-center text-[9px] text-white border border-yellow-400 opacity-20 transition-all duration-500">土</span>
                <span id="load-metal" class="absolute left-12 bottom-4 w-6 h-6 rounded-full bg-gray-500/60 flex items-center justify-center text-[9px] text-white border border-white opacity-20 transition-all duration-500">金</span>
                <span id="load-water" class="absolute left-4 top-1/3 w-6 h-6 rounded-full bg-blue-700/60 flex items-center justify-center text-[9px] text-white border border-blue-400 opacity-20 transition-all duration-500">水</span>
            </div>

            <p id="ritual-status" class="text-sm text-red-200/90 leading-relaxed font-light mb-8 h-8 tracking-wide">
                「そなたの器に流れる木気を融合中…」
            </p>

            <button id="btn-reveal-destiny" onclick="transitionToResult()" class="w-full py-4 rounded-xl bg-gradient-to-r from-red-950 via-[#8b0000] to-red-950 text-[#e5c398] font-bold text-sm hover:opacity-90 transition-all border border-red-800/40 shadow-lg tracking-widest flex justify-center items-center gap-2 hidden">
                <span>呪術を完了させ、運命を開帳する</span>
                <svg class="w-4 h-4 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M13 10V3L4 14h7v7l9-11h-7z"></path>
                </svg>
            </button>
        </div>

        <!-- ========================================== -->
        <!-- SCREEN 4, 5, 6: 鑑定結果＆詳細画面（レイアウト結合） -->
        <!-- ========================================== -->
        <div id="destiny-result" class="w-full grid grid-cols-1 lg:grid-cols-12 gap-8 hidden opacity-0 transition-all duration-1000 ease-out z-10">
            
            <!-- 結果左側：主要データ＆重ね合わせサークル -->
            <div class="lg:col-span-7 bg-[#070912]/95 rounded-3xl shrine-border p-6 md:p-8 relative overflow-hidden flex flex-col justify-between">
                <div class="absolute inset-0 bg-[radial-gradient(circle_at_50%_30%,rgba(139,0,0,0.1),transparent_60%)] pointer-events-none"></div>
                
                <!-- ヘッダー -->
                <div class="flex flex-col md:flex-row md:justify-between md:items-start pb-6 border-b border-red-950/40 gap-4 relative z-10">
                    <div>
                        <span class="text-[10px] tracking-[0.2em] text-[#e5c398] block mb-1 uppercase">CELESTIAL SACRED CHART</span>
                        <h1 class="text-xl md:text-2xl font-bold tracking-wide text-white" id="user-display-name">今日も素敵な斎子さん の星図</h1>
                    </div>
                    <div class="text-right">
                        <span class="text-xs px-3 py-1.5 rounded-full bg-red-950/30 text-red-200 border border-red-900/30" id="display-birth">1993年4月15日 06:00生まれ (女)</span>
                    </div>
                </div>

                <!-- サークルビジュアル ＆ 凡例 -->
                <div class="grid grid-cols-1 md:grid-cols-12 gap-6 my-6 items-center relative z-10">
                    
                    <!-- 5行重ね合わせサークルUI -->
                    <div class="md:col-span-7 flex flex-col items-center justify-center">
                        <div class="text-[10px] text-red-400/60 mb-2 tracking-widest cinzel">ORGANIC ELEMENT COEXISTENCE</div>
                        
                        <!-- サークルコンテナ -->
                        <div class="relative w-[280px] h-[280px] flex items-center justify-center bg-[#030408] rounded-full shadow-[inset_0_0_35px_rgba(139,0,0,0.15)] border border-red-950/50 overflow-hidden">
                            <div class="absolute w-[92%] h-[92%] border border-dashed border-red-900/30 rounded-full animate-spin" style="animation-duration: 150s;"></div>
                            <div class="absolute w-[65%] h-[65%] border border-dashed border-[#e5c398]/10 rounded-full animate-spin" style="animation-duration: 85s; animation-direction: reverse;"></div>
                            
                            <!-- エレメントの浮遊重ね合わせ円 -->
                            <!-- 木 -->
                            <div id="node-wood" class="element-node blend-screen animate-float absolute rounded-full transition-all duration-1000 flex flex-col items-center justify-center text-white font-bold text-xs"
                                 style="background: radial-gradient(circle, rgba(46,125,50,0.4) 0%, rgba(27,94,32,0.85) 100%); border: 1px solid rgba(129,199,132,0.4);">
                                <span class="tracking-widest text-[11px] md:text-xs">木</span><span class="text-[9px] font-light opacity-80" id="ratio-wood">20%</span>
                            </div>
                            <!-- 火 -->
                            <div id="node-fire" class="element-node blend-screen animate-float-delayed absolute rounded-full transition-all duration-1000 flex flex-col items-center justify-center text-white font-bold text-xs"
                                 style="background: radial-gradient(circle, rgba(216,67,21,0.55) 0%, rgba(183,28,28,0.9) 100%); border: 1px solid rgba(255,138,101,0.5); box-shadow: 0 0 25px rgba(216,67,21,0.35);">
                                <span class="tracking-widest text-[11px] md:text-xs">火</span><span class="text-[9px] font-light opacity-80" id="ratio-fire">40%</span>
                            </div>
                            <!-- 土 -->
                            <div id="node-earth" class="element-node blend-screen animate-float absolute rounded-full transition-all duration-1000 flex flex-col items-center justify-center text-white font-bold text-xs"
                                 style="background: radial-gradient(circle, rgba(249,168,37,0.45) 0%, rgba(230,81,0,0.85) 100%); border: 1px solid rgba(255,245,157,0.4);">
                                <span class="tracking-widest text-[11px] md:text-xs">土</span><span class="text-[9px] font-light opacity-80" id="ratio-earth">10%</span>
                            </div>
                            <!-- 金 -->
                            <div id="node-metal" class="element-node blend-screen animate-float-delayed absolute rounded-full transition-all duration-1000 flex flex-col items-center justify-center text-white font-bold text-xs"
                                 style="background: radial-gradient(circle, rgba(238,238,238,0.4) 0%, rgba(66,66,66,0.85) 100%); border: 1px solid rgba(255,255,255,0.4);">
                                <span class="tracking-widest text-[11px] md:text-xs">金</span><span class="text-[9px] font-light opacity-80" id="ratio-metal">20%</span>
                            </div>
                            <!-- 水 -->
                            <div id="node-water" class="element-node blend-screen animate-float absolute rounded-full transition-all duration-1000 flex flex-col items-center justify-center text-white font-bold text-xs"
                                 style="background: radial-gradient(circle, rgba(21,101,192,0.45) 0%, rgba(13,71,161,0.85) 100%); border: 1px solid rgba(144,202,249,0.4);">
                                <span class="tracking-widest text-[11px] md:text-xs">水</span><span class="text-[9px] font-light opacity-80" id="ratio-water">10%</span>
                            </div>
                        </div>

                        <!-- 凡例 -->
                        <div class="mt-4 flex flex-wrap justify-center gap-3 text-[11px] text-gray-400">
                            <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-green-700 inline-block"></span>木</span>
                            <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-red-700 inline-block"></span>火</span>
                            <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-orange-500 inline-block"></span>土</span>
                            <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-gray-500 inline-block"></span>金</span>
                            <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-blue-700 inline-block"></span>水</span>
                        </div>
                    </div>

                    <!-- 宿命星マトリクス -->
                    <div class="md:col-span-5 space-y-3">
                        <h3 class="text-xs font-bold text-[#e5c398] border-b border-red-950 pb-2 tracking-widest uppercase flex items-center gap-1.5">
                            <svg class="w-4 h-4 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                            </svg>
                            魂の構造（命式）
                        </h3>
                        
                        <!-- 日干 -->
                        <div class="bg-[#0b0e1b]/80 p-3 rounded-xl border border-red-950/60 hover:border-[#e5c398]/30 transition-all">
                            <span class="text-[9px] text-[#c5a880] uppercase tracking-widest block">日干 (生まれ持った魂の性質)</span>
                            <div class="text-sm font-bold mt-1 text-white" id="ui-nikkan">
                                <!-- JSで動的合致 -->
                            </div>
                        </div>

                        <!-- 中心星 -->
                        <div class="bg-[#0b0e1b]/80 p-3 rounded-xl border border-red-950/60 hover:border-[#e5c398]/30 transition-all">
                            <span class="text-[9px] text-[#c5a880] uppercase tracking-widest block">中心星 (最も色濃い魂の欲求)</span>
                            <div class="text-sm font-bold mt-1 text-white" id="ui-chushin">
                                <!-- JSで動的合致 -->
                            </div>
                        </div>

                        <!-- 十二運星 -->
                        <div class="bg-[#0b0e1b]/80 p-3 rounded-xl border border-red-950/60 hover:border-[#e5c398]/30 transition-all">
                            <span class="text-[9px] text-[#c5a880] uppercase tracking-widest block">大運の器 (行動力のエネルギー)</span>
                            <div class="text-sm font-bold mt-1 text-white" id="ui-energy">
                                <!-- JSで動的合致 -->
                            </div>
                        </div>

                        <!-- 歴史上の人物に例えるとアイコンボタン -->
                        <button onclick="openHeroModal()" class="w-full py-3 px-4 mt-2 rounded-xl bg-gradient-to-r from-amber-950/40 via-red-950/40 to-amber-950/40 hover:from-amber-900/60 hover:to-red-900/60 text-[#e5c398] border border-[#e5c398]/30 hover:border-[#e5c398]/60 font-bold text-xs tracking-widest transition-all shadow-md flex items-center justify-center gap-2">
                            <svg class="w-4.5 h-4.5 text-amber-400 animate-pulse" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path>
                            </svg>
                            <span>あなたを歴史上の人物に例えると</span>
                        </button>
                    </div>
                </div>

                <!-- ナビゲーション・シークエンス切り替えボタン（星図内） -->
                <div class="flex flex-col sm:flex-row justify-between items-center border-t border-red-950/40 pt-4 mt-2 gap-4">
                    <button onclick="resetShrine()" class="py-2 px-4 rounded-full bg-red-950/10 hover:bg-red-950/20 text-[10px] text-red-400 border border-red-900/20 tracking-widest transition-all flex items-center gap-1">
                        <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 1121.21 19.623M7 16a4 4 0 00.582-7.855"></path>
                        </svg>
                        初めからやり直す
                    </button>
                    <div class="flex flex-wrap gap-2 w-full sm:w-auto">
                        <button id="tab-btn-detail" onclick="switchResultTab('detail')" class="flex-1 sm:flex-initial py-2.5 px-4 rounded-full bg-[#e5c398] text-red-950 text-xs font-bold tracking-widest transition-all shadow-[0_0_10px_rgba(229,195,152,0.3)] flex items-center justify-center gap-1">
                            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path>
                            </svg>
                            深層神託
                        </button>
                        <button id="tab-btn-monthly" onclick="switchResultTab('monthly')" class="flex-1 sm:flex-initial py-2.5 px-4 rounded-full bg-red-950/40 hover:bg-red-950/80 text-[#e5c398] text-xs border border-red-800/40 tracking-widest transition-all flex items-center justify-center gap-1">
                            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <circle cx="12" cy="12" r="10" stroke-width="1.5"></circle>
                                <path d="M12 8v4l3 3" stroke-width="1.5"></path>
                            </svg>
                            今月の神託
                        </button>
                        <button id="tab-btn-biorhythm" onclick="switchResultTab('biorhythm')" class="flex-1 sm:flex-initial py-2.5 px-4 rounded-full bg-red-950/40 hover:bg-red-950/80 text-[#e5c398] text-xs border border-red-800/40 tracking-widest transition-all flex items-center justify-center gap-1">
                            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"></path>
                            </svg>
                            宿命バイオリズム
                        </button>
                    </div>
                </div>
            </div>

            <!-- ========================================== -->
            <!-- 結果右側：たっぷりと紡がれた詳細神託書（タブ1：深層神託） -->
            <!-- ========================================== -->
            <div id="result-tab-detail" class="lg:col-span-5 flex flex-col h-[75vh] lg:h-[700px] overflow-y-auto pr-2 custom-scrollbar space-y-6 transition-all duration-500">
                
                <!-- ① あなたの魅力（陽）カード -->
                <div class="bg-gradient-to-br from-green-950/20 via-[#070e14] to-[#04060c] p-6 rounded-3xl shrine-border relative border-l-4 border-l-emerald-600/60">
                    <span class="absolute right-4 top-4 text-xs font-bold text-emerald-500/30 cinzel">YANG ASPECT</span>
                    <h3 class="text-sm font-bold text-emerald-400 mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-emerald-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364-6.364l-.707.707M6.343 17.657l-.707.707m12.728 0l-.707-.707M6.343 6.343l-.707-.707m12.728 12.728L12 12m0 0a3 3 0 11-6 0 3 3 0 016 0z"></path>
                        </svg>
                        天から授かりし【陽の魅力】
                    </h3>
                    <div class="space-y-3">
                        <p class="text-sm text-gray-200 leading-relaxed font-light" id="ui-pro-detail" data-editable-key="oracle_detail.pro">
                        </p>
                    </div>
                </div>

                <!-- ② 注意点・戒め（陰）カード -->
                <div class="bg-gradient-to-br from-red-950/20 via-[#10070c] to-[#04060c] p-6 rounded-3xl shrine-border relative border-l-4 border-l-rose-700/60">
                    <span class="absolute right-4 top-4 text-xs font-bold text-rose-500/30 cinzel">YIN ASPECT</span>
                    <h3 class="text-sm font-bold text-rose-400 mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-rose-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"></path>
                        </svg>
                        背中合わせの【陰の戒め】
                    </h3>
                    <div class="space-y-3">
                        <p class="text-sm text-gray-200 leading-relaxed font-light" id="ui-con-detail" data-editable-key="oracle_detail.con">
                        </p>
                    </div>
                </div>

                <!-- ③ 魂が求める【深層の欲求】カード -->
                <div class="bg-gradient-to-br from-blue-950/20 via-[#070b14] to-[#04060c] p-6 rounded-3xl shrine-border relative border-l-4 border-l-blue-600/60">
                    <span class="absolute right-4 top-4 text-xs font-bold text-blue-500/30 cinzel">DEEP DESIRE</span>
                    <h3 class="text-sm font-bold text-blue-400 mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path>
                        </svg>
                        魂が求める【深層の欲求】
                    </h3>
                    <div class="space-y-3">
                        <p class="text-sm text-gray-200 leading-relaxed font-light" id="ui-desire-detail" data-editable-key="oracle_detail.desire">
                        </p>
                    </div>
                </div>

                <!-- ④ 現世の【天職・使命】カード -->
                <div class="bg-gradient-to-br from-amber-950/20 via-[#0e0c07] to-[#04060c] p-6 rounded-3xl shrine-border relative border-l-4 border-l-amber-600/60">
                    <span class="absolute right-4 top-4 text-xs font-bold text-amber-500/30 cinzel">VOCATION</span>
                    <h3 class="text-sm font-bold text-amber-400 mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-amber-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M11.049 2.927c.3-.921 1.603-.921 1.902 0l1.519 4.674a1 1 0 00.95.69h4.907c.961 0 1.36 1.243.577 1.835l-3.97 2.88a1 1 0 00-.364 1.118l1.518 4.674c.3.922-.755 1.688-1.538 1.118l-3.971-2.88a1 1 0 00-1.176 0l-3.97 2.88c-.783.57-1.838-.197-1.538-1.118l1.518-4.674a1 1 0 00-.364-1.118l-3.97-2.88c-.783-.57-.384-1.835.577-1.835h4.907a1 1 0 00.95-.69l1.519-4.674z"></path>
                        </svg>
                        現世の【天職・使命】
                    </h3>
                    <div class="space-y-3">
                        <p class="text-sm text-gray-200 leading-relaxed font-light" id="ui-mission-detail" data-editable-key="oracle_detail.mission">
                        </p>
                    </div>
                </div>

                <!-- ⑤ 歴史上の人物に例える偉人カード -->
                <div id="hero-card-anchor" class="bg-gradient-to-br from-[#1c0d12] via-[#0b0e1b] to-[#030408] p-6 rounded-3xl border border-[#e5c398]/30 relative overflow-hidden shadow-[0_0_20px_rgba(229,195,152,0.15)]">
                    <div class="absolute -right-8 -bottom-8 text-8xl text-[#e5c398]/5 font-serif select-none cinzel">DEITY</div>
                    <span class="absolute right-4 top-4 text-xs font-bold text-[#e5c398]/30 cinzel">SOUL MIRROR</span>
                    
                    <h3 class="text-sm font-bold text-[#e5c398] mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 12l2 2 4-4M7.835 4.697a3.42 3.42 0 001.946-.806 3.42 3.42 0 014.438 0 3.42 3.42 0 001.946.806 3.42 3.42 0 013.138 3.138 3.42 3.42 0 00.806 1.946 3.42 3.42 0 010 4.438 3.42 3.42 0 00-.806 1.946 3.42 3.42 0 01-3.138 3.138 3.42 3.42 0 00-1.946.806 3.42 3.42 0 01-4.438 0 3.42 3.42 0 00-1.946-.806 3.42 3.42 0 01-3.138-3.138 3.42 3.42 0 00-.806-1.946 3.42 3.42 0 010-4.438 3.42 3.42 0 00.806-1.946 3.42 3.42 0 013.138-3.138z"></path>
                        </svg>
                        現世に重なる【歴史の偉人】
                    </h3>
                    
                    <div class="space-y-4 relative z-10">
                        <div class="bg-red-500/5 text-[#e5c398] text-center p-4 rounded-2xl border border-[#e5c398]/20">
                            <span class="text-[9px] block text-gray-500 tracking-widest mb-1 uppercase">HERO INTERPRETATION</span>
                            <span class="text-lg font-bold tracking-widest" id="ui-hero-name" data-editable-key="hero.name">額田王</span>
                        </div>
                        <p class="text-xs text-[#e5c398]/80 text-center italic" id="ui-hero-title" data-editable-key="hero.title">
                            〜宮廷全体を照らした、万葉集の天才カリスマ歌人〜
                        </p>
                        <p class="text-sm text-gray-300 leading-relaxed font-light" id="ui-hero-desc" data-editable-key="hero.desc">
                        </p>
                    </div>
                </div>
            </div>

            <!-- ========================================== -->
            <!-- 結果右側：今月の神託（タブ2：今月の運勢） -->
            <!-- ========================================== -->
            <div id="result-tab-monthly" class="lg:col-span-5 flex flex-col h-[75vh] lg:h-[700px] overflow-y-auto pr-2 custom-scrollbar space-y-6 transition-all duration-500 hidden">
                
                <!-- 今月の運勢総合ボード -->
                <div class="bg-gradient-to-br from-[#1b0b14] via-[#05060b] to-[#030408] p-6 rounded-3xl border border-red-900/50 relative overflow-hidden">
                    <div class="absolute right-4 top-4 text-[10px] text-red-400/40 font-bold tracking-widest cinzel">MONTHLY WAVE</div>
                    <span class="text-[9px] text-[#c5a880] tracking-wider block mb-1">THIS MONTH'S ENERGY</span>
                    <h3 class="text-lg font-bold text-red-400 mb-2 tracking-widest" id="ui-monthly-title" data-editable-key="monthly.title">
                        五行巡る今月のご神託
                    </h3>
                    <p class="text-xs text-gray-400 leading-relaxed mb-4" id="ui-monthly-transit">
                        ※現在のトランジットの星気との共鳴を鑑定（丙午年 × 癸巳月）
                    </p>
                    <div class="h-0.5 w-full bg-gradient-to-r from-red-950 via-[#8b0000] to-transparent my-4"></div>
                    <p id="ui-monthly-desc" class="text-sm text-gray-200 leading-relaxed font-light" data-editable-key="monthly.desc">
                        <!-- 動的に挿入 -->
                    </p>
                </div>

                <!-- 斎子の開運アクション -->
                <div class="bg-gradient-to-br from-amber-950/15 via-[#0e0c15] to-[#030408] p-6 rounded-3xl shrine-border relative border-l-4 border-l-amber-500/50">
                    <h3 class="text-xs font-bold text-amber-400 mb-3 uppercase tracking-widest flex items-center gap-1">
                        <svg class="w-4 h-4 text-amber-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"></path>
                        </svg>
                        斎子より【此の月の開運アクション】
                    </h3>
                    <p id="ui-monthly-action" class="text-sm text-amber-100/90 leading-relaxed font-light" data-editable-key="monthly.action">
                        <!-- 動的に挿入 -->
                    </p>
                </div>
            </div>

            <!-- ========================================== -->
            <!-- 結果右側：宿命バイオリズム（タブ3：大運＆年の運勢） -->
            <!-- ========================================== -->
            <div id="result-tab-biorhythm" class="lg:col-span-5 flex flex-col h-[75vh] lg:h-[700px] overflow-y-auto pr-2 custom-scrollbar space-y-6 transition-all duration-500 hidden">
                
                <!-- 大運のテーマカード -->
                <div class="bg-gradient-to-br from-[#120a16] via-[#05060b] to-[#030408] p-6 rounded-3xl border border-purple-950/50 relative overflow-hidden">
                    <div class="absolute right-4 top-4 text-[10px] text-purple-400/40 font-bold tracking-widest cinzel">FORTUNE PERIOD</div>
                    <h3 class="text-sm font-bold text-purple-400 mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"></path>
                        </svg>
                        現在の大運（10年ごとのテーマ）
                    </h3>
                    <div class="bg-purple-950/20 border border-purple-900/30 p-4 rounded-xl text-center mb-4">
                        <span class="text-[9px] text-gray-500 tracking-wider block mb-1">現在のあなたの立ち位置</span>
                        <span id="biorhythm-daiun-title" class="text-base font-bold text-purple-200 tracking-wide" data-editable-key="biorhythm.daiun_title">30歳〜39歳 【大運：食神期】</span>
                    </div>
                    <p id="biorhythm-daiun-desc" class="text-xs text-gray-300 leading-relaxed font-light" data-editable-key="biorhythm.daiun_desc">
                    </p>
                </div>

                <!-- 運勢バイオリズム折れ線グラフ（SVG表現） -->
                <div class="bg-gradient-to-br from-[#0c0f1e] to-[#04060c] p-6 rounded-3xl shrine-border relative">
                    <div class="absolute right-4 top-4 text-[10px] text-blue-400/40 font-bold tracking-widest cinzel">WAVE DIAGRAM</div>
                    <h3 class="text-sm font-bold text-blue-400 mb-4 uppercase tracking-widest flex items-center gap-2">
                        <svg class="w-4 h-4 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M16 8v8m-4-5v5m-4-2v2m-2 4h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"></path>
                        </svg>
                        生涯の天星波動 (宿命リズム)
                    </h3>
                    
                    <!-- SVG折れ線グラフ -->
                    <div class="w-full h-36 bg-[#030408]/90 rounded-xl border border-red-950/30 p-2 relative">
                        <svg class="w-full h-full" viewBox="0 0 100 50" preserveAspectRatio="none">
                            <line x1="0" y1="25" x2="100" y2="25" stroke="rgba(139,0,0,0.15)" stroke-width="0.5" stroke-dasharray="1,1" />
                            <path id="biopath" d="M 0 45 Q 25 10 50 35 T 100 15" fill="none" stroke="url(#biograd)" stroke-width="2.5" stroke-linecap="round"/>
                            <defs>
                                <linearGradient id="biograd" x1="0%" y1="0%" x2="100%" y2="100%">
                                    <stop offset="0%" stop-color="#ef4444" />
                                    <stop offset="50%" stop-color="#e5c398" />
                                    <stop offset="100%" stop-color="#3b82f6" />
                                </linearGradient>
                            </defs>
                            <circle id="biodot" cx="55" cy="27" r="2.5" fill="#e5c398" class="animate-pulse" />
                        </svg>
                        <div class="absolute inset-x-2 bottom-1 flex justify-between text-[8px] text-gray-500 tracking-widest">
                            <span>大運1〜2旬</span>
                            <span>大運3〜4旬 (現在)</span>
                            <span>大運5〜6旬</span>
                            <span>大運7〜8旬</span>
                        </div>
                    </div>
                    <p id="biorhythm-wave-info" class="text-[10px] text-gray-400 text-center mt-3 italic">
                    </p>
                </div>

                <!-- 斎子から「今年の一言神託」 -->
                <div class="bg-gradient-to-br from-red-950/15 via-[#0e0c15] to-[#030408] p-6 rounded-3xl shrine-border relative border-l-4 border-l-red-600/50">
                    <h3 class="text-xs font-bold text-red-400 mb-2 uppercase tracking-widest flex items-center gap-1">
                        <svg class="w-4 h-4 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"></path>
                        </svg>
                        斎子より【此の年の一言神託】
                    </h3>
                    <p id="biostat-year" class="text-sm text-red-100 leading-relaxed font-light" data-editable-key="biorhythm.year_stat">
                    </p>
                </div>

            </div>
        </div>

    </main>

    <!-- フッター -->
    <footer class="w-full text-center text-[10px] text-gray-600 tracking-widest relative z-10 pt-4">
        Celestial Chart App Project &copy; 2026 | Powered by Deity Oracle Engine
    </footer>

    <!-- ========================================== -->
    <!-- 編集内容 JSON エクスポート用モーダル画面 -->
    <!-- ========================================== -->
    <div id="export-modal" class="fixed inset-0 bg-black/80 backdrop-blur-sm flex items-center justify-center z-[100] hidden opacity-0 transition-all duration-300">
        <div class="bg-[#070912] border border-[#e5c398]/30 rounded-3xl p-6 md:p-8 max-w-xl w-full mx-4 shadow-[0_0_50px_rgba(139,0,0,0.3)] flex flex-col justify-between">
            <div>
                <div class="flex justify-between items-center pb-4 border-b border-red-950/40 mb-4">
                    <h3 class="text-[#e5c398] font-bold text-sm tracking-widest cinzel">EXPORT ORACLE DATABASE (JSON)</h3>
                    <button onclick="closeExportModal()" class="text-gray-500 hover:text-white transition-all text-xl">&times;</button>
                </div>
                <p class="text-xs text-gray-400 leading-relaxed mb-4">
                    画面上で書き替えた最新の「神話的セリフ」や「深層神託テキスト」一式を、プログラムコード（データベース配列）としてそのまま再利用できるJSONデータに整えました。コピーして本番開発にお役立てください。
                </p>
                <textarea id="json-export-area" readonly class="w-full h-64 bg-[#030408] text-green-400 p-4 font-mono text-xs rounded-xl border border-red-950/60 focus:outline-none custom-scrollbar select-all"></textarea>
            </div>
            <div class="flex gap-4 pt-4 mt-4 border-t border-red-950/40">
                <button onclick="closeExportModal()" class="flex-1 py-3 rounded-xl bg-gray-900 border border-red-950 hover:bg-gray-800 text-xs text-gray-300 tracking-widest transition-all">
                    閉じる
                </button>
                <button onclick="copyJsonToClipboard()" class="flex-1 py-3 rounded-xl bg-gradient-to-r from-red-950 via-[#8b0000] to-red-950 text-[#e5c398] font-bold text-xs tracking-widest border border-red-800/40 shadow-md transition-all flex items-center justify-center gap-2">
                    <svg class="w-4 h-4 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m-5 10h6m-6 4h6m2-10h4m-4 4h4"></path>
                    </svg>
                    <span>クリップボードにコピー</span>
                </button>
            </div>
        </div>
    </div>

    <!-- ========================================== -->
    <!-- 歴史上の人物に例えると モーダル -->
    <!-- ========================================== -->
    <div id="hero-modal" class="fixed inset-0 bg-black/85 backdrop-blur-sm flex items-center justify-center z-[100] hidden opacity-0 transition-all duration-300">
        <div class="bg-[#0b0e1b] border-2 border-[#e5c398]/40 rounded-3xl p-6 md:p-8 max-w-lg w-full mx-4 shadow-[0_0_50px_rgba(229,195,152,0.25)] flex flex-col justify-between">
            <div>
                <div class="flex justify-between items-center pb-4 border-b border-red-950/60 mb-4">
                    <h3 class="text-[#e5c398] font-bold text-sm tracking-widest cinzel flex items-center gap-2">
                        <svg class="w-4 h-4 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 12l2 2 4-4M7.835 4.697a3.42 3.42 0 001.946-.806 3.42 3.42 0 014.438 0 3.42 3.42 0 001.946.806 3.42 3.42 0 013.138 3.138 3.42 3.42 0 00.806 1.946 3.42 3.42 0 010 4.438 3.42 3.42 0 00-.806 1.946 3.42 3.42 0 01-3.138 3.138 3.42 3.42 0 00-1.946.806 3.42 3.42 0 01-4.438 0 3.42 3.42 0 00-1.946-.806 3.42 3.42 0 01-3.138-3.138 3.42 3.42 0 00-.806-1.946 3.42 3.42 0 010-4.438 3.42 3.42 0 00.806-1.946 3.42 3.42 0 013.138-3.138z"></path>
                        </svg>
                        あなたを歴史上の人物に例えると
                    </h3>
                    <button onclick="closeHeroModal()" class="text-[#e5c398] hover:text-white transition-all text-2xl">&times;</button>
                </div>
                
                <div class="space-y-4 my-6 text-center">
                    <div class="bg-red-500/5 text-[#e5c398] p-5 rounded-2xl border border-[#e5c398]/25 shadow-inner">
                        <span class="text-[9px] block text-gray-500 tracking-widest mb-1 uppercase">HERO RITUAL SHADOW</span>
                        <span class="text-2xl font-bold tracking-widest block text-transparent bg-clip-text bg-gradient-to-r from-[#e5c398] via-[#fff] to-[#e5c398]" id="modal-hero-name">織田信長</span>
                    </div>
                    <p class="text-xs text-[#e5c398]/80 italic" id="modal-hero-title">
                        〜すべてを照らし、古い秩序を焼き尽くす圧倒的な太陽〜
                    </p>
                    <div class="h-0.5 w-full bg-gradient-to-r from-transparent via-[#e5c398]/20 to-transparent"></div>
                    <p class="text-sm text-gray-300 leading-relaxed font-light text-left p-4 bg-[#04060c]/60 rounded-xl border border-red-950/30 custom-scrollbar max-h-48 overflow-y-auto" id="modal-hero-desc">
                        <!-- 動的挿入 -->
                    </p>
                </div>
            </div>
            <button onclick="closeHeroModal()" class="w-full py-3 rounded-xl bg-gradient-to-r from-red-950 via-[#8b0000] to-red-950 text-[#e5c398] border border-red-800/40 text-xs font-bold tracking-widest transition-all shadow-md">
                神託を胸に刻み、閉じる
            </button>
        </div>
    </div>

    <!-- JavaScript ロジック -->
    <script>
        // 十干の配列
        const KAN = ["甲", "乙", "丙", "丁", "戊", "己", "庚", "辛", "壬", "癸"];
        const KAN_DESC = [
            "（まっすぐに伸びる大木）",
            "（しなやかで美しい草花）",
            "（すべてを照らすエネルギッシュな太陽）",
            "（暗闇を温めるキャンドルの灯火）",
            "（すべてを優しく包み込む大いなる山）",
            "（生命を優しく育む豊かな大地）",
            "（意志を貫く強靭な鋼の剣）",
            "（繊細な感性を持つ磨き上げられた宝石）",
            "（すべてを飲み込むダイナミックな大海）",
            "（優しく静かに潤す恵みの雨）"
        ];
        
        // 各日干の動的な本質の解説マトリクス
        const KAN_ESSENCE = [
            "まっすぐ天に伸びる大木のように、強い信念と成長への渇望を持っています。誠実で、他者を惹きつける安心感があります。",
            "しなやかで周囲と調和する美しい草花のように、卓越した協調性と豊かな社交性を持っています。内面は非常に粘り強いです。",
            "世界を温かく照らす太陽のように、明るくオープンで強烈な存在感を持っています。情熱的で物事を前向きに進める力があります。",
            "暗闇を優しく温めるキャンドルのように、深い洞察力と静かな情熱を持っています。細やかな気配りと独特のカリスマ性があります。",
            "全てを包み込む壮大な大山のように、どっしりとした包容力と安定感を持っています。信頼が厚く、周囲の支えとなります。",
            "生命を優しく育む豊かな大地のように、温和で包容力があり、他者の才能を引き出し育てることに長けています。",
            "意志を貫く強靭な鋼の剣のように、決断力と実行力に満ちています。不屈の精神で困難を切り開く強さがあります。",
            "繊細な美しさを持つ磨き上げられた宝石のように、鋭い感性と独自の美学を持っています。完璧主義で高い品格を誇ります。",
            "全てを飲み込み広がる大海のように、自由奔放でスケールの大きな知性を持っています。臨機応変でダイナミックです。",
            "静かに大地を潤す恵みの雨のように、優しく知的で、優れた直感力と共感力を持っています。人々の心を潤します。"
        ];

        // 十二支の配列
        const ZHI = ["子", "丑", "寅", "卯", "辰", "巳", "午", "未", "申", "酉", "戌", "亥"];
        const ZHI_ELEMENTS = ["water", "earth", "wood", "wood", "earth", "fire", "fire", "earth", "metal", "metal", "earth", "water"];

        // 節入り角度（太陽黄経）
        const TARGET_ANGLES = [285, 315, 345, 15, 45, 75, 105, 135, 165, 195, 225, 255];

        // 各日干に対応する「今月の神託（2026年5月・癸巳月）」
        const monthlyFortunes = [
            {
                title: "水生木の開恵（印綬の月）",
                desc: "今月は『癸（雨露）』の恵みがあなたの『甲（大木）』を内側から深く潤す『印綬』の運気じゃ。これまでの努力が知識や実力として統合され、周囲から知的なアドバイスを求められる知の守護神の月。焦って行動するよりも、じっくりと本を読み、精神的なエネルギーを蓄えるのに最適な静寂と学びの時期となるぞ。",
                action: "新しい資格の勉強を始めるか、歴史的な古書や神社仏閣に足を運び、静かな時を過ごすが良い。"
            },
            {
                title: "優美なる潤い（偏印の月）",
                desc: "あなたの『乙（草花）』に清らかな水滴が降り注ぐ『偏印』の運気。常識にとらわれないユニークな直感や、クリエイティブなアイデアが次っと湧き出る不思議な月じゃ。伝統的なルールに縛られず、自分の『おもしろそう！』というオタク的なパッションをハックする楽しさに満ち溢れておるぞ。",
                action: "普段行かないユニークな美術館を訪れるか、斬新なデジタルアートや占いなどのサブカルチャーに触れるべし。"
            },
            {
                title: "日光と雨露の干渉（正官の月）",
                desc: "あなたの持つギラギラとした『丙（太陽）』の熱量を、優しく涼やかに和らげる『正官』の気流が巡る月。周囲からの信頼が劇的に高まり、責任ある役割や品格ある立ち位置を任される安定繁栄の兆しじゃ。自分勝手な行動は抑えられ、規律正しく社会に貢献することで大いなる名誉を得る。",
                action: "お気に入りの高級感ある洋服を身にまなり、オフィシャルな席でのマナーや上品な言葉遣いを意識せよ。"
            },
            {
                title: "火水対峙の緊張（偏官の月）",
                desc: "あなたの『丁（灯火）』の炎と、今月の『癸（雨露）』が激しく火花を散らす『偏官』の闘争運気。非常に多忙を極め、急な予定変更やタフなミッションを電光石火のスピードで片付ける必要に迫られるぞ。肉体的には少し疲れやすいが、持ち前の覚悟とシャープな決断力で難局を見事にハックできる。",
                action: "スケジュールを詰め込みすぎず、1日の終わりにサウナやハーブティーで自律神経を深く癒やしなさい。"
            },
            {
                title: "大いなる大地の保水（正財の月）",
                desc: "乾いた『戊（大山）』が恵みの雨を含み、豊かな森を育み始める『正財』の堅実な運気じゃ。派手な投資や冒険をするのではなく、これまでの人間関係や家族、割と保守的な蓄財のベースをコツコツと美しく整えるのに適切なタイミング。誠実で丁寧なあなたの態度が、そのまま強力な信頼貯金へと変わる。",
                action: "家計簿やアプリの収支を細かく見直し、身近な大切な人へ小さな手料理や感謝のプレゼントを贈るが良い。"
            },
            {
                title: "湿潤なる育生（偏財の月）",
                desc: "温和な『己（大地）』に水が豊かに巡り、生命が活発に躍動する『偏財』の大循環運気じゃ。多くの魅力的な人脈とお金がダイナミックに行き交い、社交性が最大化してフットワークが非常に軽くなる。誰かを喜ばせたいというピュアなサービス精神が、巡り巡ってあなたに豊かな豊穣をもたらすぞ。",
                action: "懐かしい友人たちに自分から連絡を取って楽しい食事会を主催するか、新しいコミュニティのイベントに顔を出せ。"
            },
            {
                title: "鋼の試練と開拓（傷官の月）",
                desc: "あなたの持つ強靭な『庚（鋼剣）』が水で磨かれ、鋭い美意識を放つ『傷官』の天才アーティスト月。感性が極限まで研ぎ澄まされ、クリエイティブな表現や技術開発で圧倒的な頭角を現すことができる。ただし、言葉の刃が鋭くなりすぎて、悪気なく上司や相棒に噛み付いてしまう危うさも同居する。",
                action: "言葉を発する前に『これは相手を傷つけないか』と一瞬沈黙を置き、創作活動や文章表現にその全エネルギーを昇華せよ。"
            },
            {
                title: "気高き宝石の洗浄（食神の月）",
                desc: "あなたの繊細な『辛（宝石）』の曇りが『癸（雨露）』によって清らかに洗い流され、本来の美しさを取り戻す『食神』の黄金月。衣食住の楽しさや五感の快楽、豊かな美食を心からエンジョイできる最高に穏やかな運気じゃ。無理に頑張る必要はなく、ありのままの自分を全肯定して、ただ人生を遊ぶが良い。",
                action: "少し奮発して一流の美味しいレストランで美食を堪能するか、ラグジュアリーなスパで五感を贅沢に満たしなさい。"
            },
            {
                title: "大海の合流と拡大（劫財の月）",
                desc: "あなたの壮大な『壬（大海）』に、さらなる水気が加わり、驚異的なスケールへと広がる『劫財』の野心月。一人では成し遂げられない大きな目標に向け、同じ志を持つ熱い仲間を結集してダイナミックに挑戦・投資を行う転換期じゃ。リーダーシップを堂々と発揮し、時代の荒波をハックして進め。",
                action: "自分の大いなるビジョンを周囲に堂々と語り、同じ目標に向かって役割を分担する組織の仕組み作りに着手せよ。"
            },
            {
                title: "同気相求の共鳴（比肩の月）",
                desc: "あなたの『癸（雨露）』に同じ癸の星気が重なり合う、原点回帰の『比肩』の自立運気。周囲の雑音やアドバイスに惑わされることなく、純粋に『自分はこれからどう生きたいのか』というこだわりと主体性を取り戻す季節じゃ。一匹狼のように独自の信念を貫くことで、魂の核が極めて強固になる。",
                action: "1人だけの静かな旅に出るか、スマホの通知を完全にオフにする『デジタルデトックス』の時間を作りなさい。"
            }
        ];

        // 斎子の対話シナリオデータ
        let SAIKO_DIALOGUES = [
            {
                speech: "よくぞ参った、迷える魂よ。我は此の祭壇の主・斎子。まずは、そなたが現世で用いる【魂の仮名】をここに記すがよい。心を開き、運命を重ね合わせようぞ…",
                targetId: "step-name"
            },
            {
                speech: "…なるほど、魂の呼び名を受け取った。次なる問いじゃ。そなたがこの次元へ、肉体を持って生み落とされた【陽の日（生年月日）】を細かく選択するがよい…",
                targetId: "step-birthdate"
            },
            {
                speech: "天の川の星々が、そなたの誕生をどのように祝福したか…さらに詳しく知るため、【生まれし刻限（時間）】も分かれば我に示して見せよ…",
                targetId: "step-birthtime"
            },
            {
                speech: "これが最後の問い。そなたの宿命の波（大運）を陰陽で紡ぎ分けよう。そなたの【陰陽の性】を我に選ぶがよい。女子（おなご）か、男子（おのこ）か…",
                targetId: "step-gender"
            }
        ];

        let currentStep = 0;
        let selectedGender = 'F';
        let isEditModeActive = false;
        let editedTexts = {};

        // ページ読み込み時の初期化
        window.onload = function() {
            initBirthdateSelects();
            syncEditableDomToCache();
        };

        // グレゴリオ暦から太陽黄経を算出（略算式）
        function getSolarLongitude(year, month, day, decimalHour) {
            const dateUTC = Date.UTC(year, month - 1, day, 0, 0, 0) + (decimalHour - 9) * 60 * 60 * 1000;
            const epochUTC = Date.UTC(2000, 0, 1, 12, 0, 0) - 9 * 60 * 60 * 1000; 
            const d = (dateUTC - epochUTC) / (1000 * 60 * 60 * 24);

            let L = 280.460 + 0.9856474 * d;
            let M = 357.528 + 0.9856003 * d;

            L = (L % 360 + 360) % 360;
            M = (M % 360 + 360) % 360;

            const M_rad = M * Math.PI / 180;
            let lambda = L + 1.915 * Math.sin(M_rad) + 0.020 * Math.sin(2 * M_rad);
            lambda = (lambda % 360 + 360) % 360;

            return lambda;
        }

        // 指定年月の節入り日時を二分探索
        function findSetsuiri(year, month) {
            const targetAngle = TARGET_ANGLES[month - 1];
            let low = Date.UTC(year, month - 1, 1, 0, 0, 0);
            let high = Date.UTC(year, month - 1, 15, 23, 59, 59);

            for (let i = 0; i < 24; i++) {
                let mid = (low + high) / 2;
                let date = new Date(mid);
                let decHour = date.getUTCHours() + date.getUTCMinutes() / 60 + 9; // JST
                let lon = getSolarLongitude(date.getUTCFullYear(), date.getUTCMonth() + 1, date.getUTCDate(), decHour);

                let diff = lon - targetAngle;
                if (diff < -180) diff += 360;
                if (diff > 180) diff -= 360;

                if (diff > 0) {
                    high = mid;
                } else {
                    low = mid;
                }
            }
            return new Date(low);
        }

        // セレクトボックスの動的生成
        function initBirthdateSelects() {
            const yearSelect = document.getElementById('input-birth-year');
            const monthSelect = document.getElementById('input-birth-month');
            const daySelect = document.getElementById('input-birth-day');

            for (let y = 1930; y <= 2026; y++) {
                const opt = document.createElement('option');
                opt.value = y;
                opt.textContent = `${y}年`;
                if (y === 1993) opt.selected = true;
                yearSelect.appendChild(opt);
            }

            for (let m = 1; m <= 12; m++) {
                const opt = document.createElement('option');
                opt.value = m;
                opt.textContent = `${m}月`;
                if (m === 4) opt.selected = true;
                monthSelect.appendChild(opt);
            }

            for (let d = 1; d <= 31; d++) {
                const opt = document.createElement('option');
                opt.value = d;
                opt.textContent = `${d}日`;
                if (d === 15) opt.selected = true;
                daySelect.appendChild(opt);
            }
        }

        // 神秘的なサウンド（Web Audio API）
        function playMysticSound(freq, type, vol, duration) {
            try {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                if (!AudioContext) return;
                const ctx = new AudioContext();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                
                osc.type = type;
                osc.frequency.value = freq;
                
                gain.gain.setValueAtTime(vol, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + duration);
                
                osc.connect(gain);
                gain.connect(ctx.destination);
                
                osc.start();
                osc.stop(ctx.currentTime + duration);
            } catch(e) {
                // 音声未対応時は無視
            }
        }

        // 結界解除
        function startOracleRitual() {
            playMysticSound(150, 'sine', 0.2, 1.5);
            
            const opScreen = document.getElementById('seq-opening');
            opScreen.classList.add('scale-[0.95]', 'opacity-0');
            
            setTimeout(() => {
                opScreen.classList.add('hidden');
                const gate = document.getElementById('shrine-gate');
                gate.classList.remove('hidden');
                setTimeout(() => {
                    gate.classList.remove('opacity-0');
                    triggerSaikoSpeech();
                }, 50);
            }, 1000);
        }

        // 斎子のセリフタイプライター効果
        function triggerSaikoSpeech() {
            const defaultText = SAIKO_DIALOGUES[currentStep].speech;
            const key = `dialogue.step_${currentStep}`;
            const text = editedTexts[key] || defaultText;
            
            const container = document.getElementById("deity-speech");
            container.setAttribute('data-editable-key', key);
            container.innerHTML = "";
            
            if (window.speechInterval) clearInterval(window.speechInterval);
            playMysticSound(330, 'sine', 0.08, 0.4);

            if (isEditModeActive) {
                container.innerText = text;
            } else {
                let index = 0;
                window.speechInterval = setInterval(() => {
                    if (index < text.length) {
                        container.innerHTML += text.charAt(index);
                        index++;
                    } else {
                        clearInterval(window.speechInterval);
                    }
                }, 25);
            }
        }

        // 性別の選択
        function selectMysticGender(gender) {
            selectedGender = gender;
            const btnF = document.getElementById('btn-gender-f');
            const btnM = document.getElementById('btn-gender-m');
            
            playMysticSound(220, 'sine', 0.1, 0.3);

            if (gender === 'F') {
                btnF.className = "py-4 rounded-xl bg-red-950/20 text-[#e5c398] border border-[#e5c398]/50 font-bold hover:bg-red-950/40 transition-all shadow-md flex items-center justify-center gap-2";
                btnM.className = "py-4 rounded-xl bg-[#04060c] text-gray-500 border border-red-950/60 font-bold hover:bg-red-950/20 transition-all flex items-center justify-center gap-2";
            } else {
                btnM.className = "py-4 rounded-xl bg-red-950/20 text-[#e5c398] border border-[#e5c398]/50 font-bold hover:bg-red-950/40 transition-all shadow-md flex items-center justify-center gap-2";
                btnF.className = "py-4 rounded-xl bg-[#04060c] text-gray-500 border border-red-950/60 font-bold hover:bg-red-950/20 transition-all flex items-center justify-center gap-2";
            }
        }

        // 次のステップへ
        function nextStep() {
            if (currentStep < 3) {
                const currentEl = document.getElementById(SAIKO_DIALOGUES[currentStep].targetId);
                currentEl.classList.add("hidden");

                currentStep++;
                
                const nextEl = document.getElementById(SAIKO_DIALOGUES[currentStep].targetId);
                nextEl.classList.remove("hidden");

                updateProgressDots();
                document.getElementById("btn-back").classList.remove("invisible");
                triggerSaikoSpeech();

                if (currentStep === 3) {
                    const btnSpan = document.getElementById("btn-next").querySelector("span");
                    btnSpan.innerText = "五行錬成の儀へ";
                    document.getElementById("next-icon").innerHTML = `
                        <svg class="w-4 h-4 text-[#e5c398]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"></path>
                        </svg>
                    `;
                }
            } else {
                startRitualLoading();
            }
        }

        // 前のステップへ
        function previousStep() {
            if (currentStep > 0) {
                const currentEl = document.getElementById(SAIKO_DIALOGUES[currentStep].targetId);
                currentEl.classList.add("hidden");

                currentStep--;

                const prevEl = document.getElementById(SAIKO_DIALOGUES[currentStep].targetId);
                prevEl.classList.remove("hidden");

                updateProgressDots();

                if (currentStep === 0) {
                    document.getElementById("btn-back").classList.add("invisible");
                }

                const btnSpan = document.getElementById("btn-next").querySelector("span");
                btnSpan.innerText = "次なる問いへ";
                document.getElementById("next-icon").innerHTML = `
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path>
                    </svg>
                `;

                triggerSaikoSpeech();
            }
        }

        // 進捗勾玉の明滅
        function updateProgressDots() {
            for (let i = 0; i <= 3; i++) {
                const dot = document.getElementById(`dot-${i}`);
                if (i === currentStep) {
                    dot.className = "w-3.5 h-3.5 rounded-full bg-red-600 shadow-[0_0_8px_rgba(239,68,68,0.8)] transition-all duration-300";
                } else if (i < currentStep) {
                    dot.className = "w-3.5 h-3.5 rounded-full bg-red-950 border border-red-800/40 transition-all duration-300";
                } else {
                    dot.className = "w-3.5 h-3.5 rounded-full bg-gray-800 transition-all duration-300";
                }
            }
        }

        // 魂の五行錬成シークエンス
        function startRitualLoading() {
            playMysticSound(110, 'triangle', 0.15, 1.0);

            const shrineGate = document.getElementById("shrine-gate");
            shrineGate.classList.add("scale-[0.95]", "opacity-0");

            setTimeout(() => {
                shrineGate.classList.add("hidden");
                
                const loadScreen = document.getElementById("seq-ritual");
                loadScreen.classList.remove("hidden");
                setTimeout(() => { loadScreen.classList.remove("opacity-0"); }, 50);

                processRitualProgress();
            }, 1000);
        }

        // 五行の元素が読み込まれる演出
        function processRitualProgress() {
            const elements = ["wood", "fire", "earth", "metal", "water"];
            const labels = {
                wood: "「そなたの器に流れる【木気（大木の生命力）】を抽出し、重ね合わせ中…」",
                fire: "「魂の燃料となる【火気（太陽のエネルギー）】に呪術を点火中…」",
                earth: "「すべてを包み込みし【土気（大地を育む母性）】を固定中…」",
                metal: "「知性と誇り高き【金気（鋭き宝石の結晶）】を錬成中…」",
                water: "「感情を潤し流す【水気（清らかな恵みの水流）】を重ね中…」"
            };

            let index = 0;
            const textContainer = document.getElementById("ritual-status");

            function nextElementInject() {
                if (index < elements.length) {
                    const elKey = elements[index];
                    const node = document.getElementById(`load-${elKey}`);
                    
                    node.classList.remove("opacity-20");
                    node.classList.add("opacity-100", "scale-125", "shadow-[0_0_15px_rgba(255,255,255,0.8)]");
                    
                    textContainer.innerText = labels[elKey];
                    playMysticSound(180 + (index * 40), 'sine', 0.08, 0.4);

                    index++;
                    setTimeout(nextElementInject, 1300);
                } else {
                    textContainer.innerHTML = "<span class='text-amber-300 font-bold tracking-widest animate-pulse'>「…よし、すべての調和が整った。運命の蓋を開くぞ！」</span>";
                    playMysticSound(440, 'triangle', 0.2, 1.5);
                    document.getElementById("btn-reveal-destiny").classList.remove("hidden");
                }
            }

            setTimeout(nextElementInject, 400);
        }

        // 錬成から結果開示への移行
        function transitionToResult() {
            playMysticSound(110, 'sine', 0.25, 2.0);

            const loadScreen = document.getElementById("seq-ritual");
            loadScreen.classList.add("scale-[0.95]", "opacity-0");

            setTimeout(() => {
                loadScreen.classList.add("hidden");

                // 命式計算エンジン始動
                calculateDestinyData();

                const result = document.getElementById("destiny-result");
                result.classList.remove("hidden");
                
                setTimeout(() => {
                    result.classList.remove("opacity-0");
                }, 50);

            }, 1000);
        }

        // 呪術式・四柱推命完全対応＆算出エンジン
        function calculateDestinyData() {
            const userName = document.getElementById('input-username').value.trim() || "今日も素敵な斎子さん";
            const year = parseInt(document.getElementById('input-birth-year').value);
            const month = parseInt(document.getElementById('input-birth-month').value);
            const day = parseInt(document.getElementById('input-birth-day').value);
            const timeInput = document.getElementById('input-birthtime').value;
            const hour = timeInput ? parseInt(timeInput.split(":")[0]) : 12;
            const minute = timeInput ? parseInt(timeInput.split(":")[1]) : 0;

            // 1. 各種干支の動的算出 (基準点：2000年1月1日 = 54「戊午」)
            const baseDate = Date.UTC(2000, 0, 1);
            const targetDate = Date.UTC(year, month - 1, day);
            const diffDays = Math.round((targetDate - baseDate) / (1000 * 60 * 60 * 24));
            let dayKanJiIndex = (54 + diffDays) % 60;
            if (dayKanJiIndex < 0) dayKanJiIndex += 60;

            // 年干支インデックスの計算 (節入り2月立春基準)
            const setsuiriFeb = findSetsuiri(year, 2);
            const birthDateTime = new Date(year, month - 1, day, hour, minute);
            let activeYear = year;
            if (birthDateTime < setsuiriFeb) {
                activeYear = year - 1;
            }
            let yearKanJiIndex = (activeYear - 4) % 60;
            if (yearKanJiIndex < 0) yearKanJiIndex += 60;

            // 月干支の決定 (天文学的な節入り時間と五虎群法に基づく)
            let setsuiriDates = [];
            for (let m = 1; m <= 12; m++) {
                setsuiriDates.push({ month: m, date: findSetsuiri(year, m) });
            }
            setsuiriDates.unshift({ month: 12, date: findSetsuiri(year - 1, 12) });
            setsuiriDates.push({ month: 1, date: findSetsuiri(year + 1, 1) });

            let activeMonthIndex = -1;
            for (let i = 0; i < setsuiriDates.length - 1; i++) {
                if (birthDateTime >= setsuiriDates[i].date && birthDateTime < setsuiriDates[i+1].date) {
                    activeMonthIndex = setsuiriDates[i].month;
                    break;
                }
            }
            if (activeMonthIndex === -1) {
                activeMonthIndex = 1; 
            }

            let zhiIndex = (activeMonthIndex === 12) ? 0 : (activeMonthIndex === 1) ? 1 : activeMonthIndex;
            const yearKan = yearKanJiIndex % 10;
            let startKan = 0;
            if (yearKan === 0 || yearKan === 5) startKan = 2; // 丙
            else if (yearKan === 1 || yearKan === 6) startKan = 4; // 戊
            else if (yearKan === 2 || yearKan === 7) startKan = 6; // 庚
            else if (yearKan === 3 || yearKan === 8) startKan = 8; // 壬
            else startKan = 0; // 甲

            let offset = 0;
            if (activeMonthIndex >= 2) offset = activeMonthIndex - 2;
            else if (activeMonthIndex === 1) offset = 11;
            else if (activeMonthIndex === 12) offset = 10;

            let monthKanIndex = (startKan + offset) % 10;
            let monthKanJiIndex = monthKanIndex * 12 + zhiIndex;

            // 時干支の決定 (五子元法)
            let hourZhiIndex = Math.floor((hour + 1) / 2) % 12;
            const dayKan = dayKanJiIndex % 10;
            let startHourKan = 0;
            if (dayKan === 0 || dayKan === 5) startHourKan = 0;
            else if (dayKan === 1 || dayKan === 6) startHourKan = 2;
            else if (dayKan === 2 || dayKan === 7) startHourKan = 4;
            else if (dayKan === 3 || dayKan === 8) startHourKan = 6;
            else startHourKan = 8;

            let hourKanIndex = (startHourKan + hourZhiIndex) % 10;
            let hourKanJiIndex = hourKanIndex * 12 + hourZhiIndex;

            const nikkan = dayKan; // 基準日干

            // 通変星算出関数
            function getTuhenstar(targetKan) {
                const diff = (targetKan - nikkan + 10) % 10;
                const sign = (targetKan % 2 === nikkan % 2) ? "same" : "diff";
                
                if (diff === 0) return sign === "same" ? "比肩" : "劫財";
                if (diff === 2) return sign === "same" ? "食神" : "傷官";
                if (diff === 4) return sign === "same" ? "偏財" : "正財";
                if (diff === 6) return sign === "same" ? "偏官" : "正官";
                if (diff === 8) return sign === "same" ? "偏印" : "印綬";
                if (diff === 1) return sign === "same" ? "劫財" : "比肩";
                if (diff === 3) return sign === "same" ? "傷官" : "食神";
                if (diff === 5) return sign === "same" ? "正財" : "偏財";
                if (diff === 7) return sign === "same" ? "正官" : "偏官";
                if (diff === 9) return sign === "same" ? "印綬" : "偏印";
                return "比肩";
            }

            // 本気地支の宿る蔵干
            const HONKI_KAN = [9, 5, 0, 1, 4, 2, 3, 5, 6, 7, 4, 8]; // 子〜亥に対応する天干インデックス
            
            // 十二運星テーブル (日干0-9 × 地支0-11)
            const JYUNI_UN = [
                ["沐浴", "冠帯", "建禄", "帝旺", "衰", "病", "死", "墓", "絶", "胎", "養", "長生"], // 甲
                ["病", "衰", "帝旺", "建禄", "冠帯", "沐浴", "長生", "養", "胎", "絶", "墓", "死"], // 乙
                ["胎", "養", "長生", "沐浴", "冠帯", "建禄", "帝旺", "衰", "病", "死", "墓", "絶"], // 丙
                ["絶", "墓", "死", "病", "衰", "帝旺", "建禄", "冠帯", "沐浴", "長生", "養", "胎"], // 丁
                ["胎", "養", "長生", "沐浴", "冠帯", "建禄", "帝旺", "衰", "病", "死", "墓", "絶"], // 戊
                ["絶", "墓", "死", "病", "衰", "帝旺", "建禄", "冠帯", "沐浴", "長生", "養", "胎"], // 己
                ["死", "墓", "絶", "胎", "養", "長生", "沐浴", "冠帯", "建禄", "帝旺", "衰", "病"], // 庚
                ["長生", "養", "胎", "絶", "墓", "死", "病", "衰", "帝旺", "建禄", "冠帯", "沐浴"], // 辛
                ["帝旺", "衰", "病", "死", "墓", "絶", "胎", "養", "長生", "沐浴", "冠帯", "建禄"], // 壬
                ["建禄", "冠帯", "沐浴", "長生", "養", "胎", "絶", "墓", "死", "病", "衰", "帝旺"]  // 癸
            ];

            const nikkanChar = KAN[nikkan];
            const nikkanDescChar = KAN_DESC[nikkan];
            const chushinStar = getTuhenstar(HONKI_KAN[monthKanJiIndex % 12]); // 月支の本気
            const activeEnergy = JYUNI_UN[nikkan][dayKanJiIndex % 12]; // 日支から十二運

            // 魂の構造 - 分かりやすい中心星の説明（魂の本音）
            const chushinSimpleDesc = {
                "比肩": "こだわりを貫く、独立独歩の星",
                "劫財": "大きな野心を秘め、仲間と挑戦する星",
                "食神": "人生を楽しみ尽くす、遊びと表現の星",
                "傷官": "鋭い感性で本質を突く、美意識の星",
                "偏財": "人とお金をダイナミックに循環させる星",
                "正財": "地道に信頼を積み重ねる、誠実な星",
                "偏官": "圧倒的なスピードで道を切り拓く星",
                "正官": "高い品格と正義感で社会を守る星",
                "偏印": "常識に囚われない、未知なる開拓者の星",
                "印綬": "深い知恵と愛情で伝統を継承する星"
            };

            // 魂の構造 - イメージが湧く大運の器（行動スタイル）
            const energySimpleDesc = {
                "胎": "これから何にでもなれる、無限の可能性を秘めたエネルギー",
                "養": "周囲から愛され、助けられながら育つエネルギー",
                "長生": "素直で信頼され、着実にステップアップするエネルギー",
                "沐浴": "ロマンを追い求め、新しい世界へ飛び出すエネルギー",
                "冠帯": "華やかな自信をまとい、前線で輝く女王様のエネルギー",
                "建禄": "地道に実力を磨き、確固たる基盤を築くエネルギー",
                "帝旺": "圧倒的なカリスマで頂点に立つ、王者・主役のエネルギー",
                "衰": "一歩引いて全体を見渡し、冷静にサポートする長老のエネルギー",
                "病": "豊かなイマジネーションと、鋭い感性を持つアーティストのエネルギー",
                "死": "余計な執着を捨て去り、一つのことを極限まで極めるエネルギー",
                "墓": "コツコツと知識や財産を蓄える、秘密基地のようなエネルギー",
                "絶": "一瞬で全てをリセットし、奇跡を起こす瞬間移動のエネルギー"
            };

            // 3. 大運（宿命バイオリズム）と現在年齢への精密な対応
            const isYangYear = (yearKan % 2 === 0);
            const isFemale = (selectedGender === 'F');
            const isForward = (isYangYear && !isFemale) || (!isYangYear && isFemale);

            let targetSetsuiri;
            if (isForward) {
                let nextMonth = activeMonthIndex + 1;
                let nextYear = year;
                if (nextMonth > 12) { nextMonth = 1; nextYear = year + 1; }
                targetSetsuiri = findSetsuiri(nextYear, nextMonth);
            } else {
                targetSetsuiri = findSetsuiri(year, activeMonthIndex);
            }
            
            const diffMs = Math.abs(birthDateTime.getTime() - targetSetsuiri.getTime());
            const diffDaysVal = diffMs / (1000 * 60 * 60 * 24);
            const startAge = Math.max(1, Math.round(diffDaysVal / 3)); // 何歳運 (最小1歳運)

            let daiunSteps = [];
            let currentDaiunKanIndex = monthKanIndex;
            let currentDaiunZhiIndex = monthKanJiIndex % 12;

            for (let i = 1; i <= 8; i++) {
                if (isForward) {
                    currentDaiunKanIndex = (currentDaiunKanIndex + 1) % 10;
                    currentDaiunZhiIndex = (currentDaiunZhiIndex + 1) % 12;
                } else {
                    currentDaiunKanIndex = (currentDaiunKanIndex - 1 + 10) % 10;
                    currentDaiunZhiIndex = (currentDaiunZhiIndex - 1 + 12) % 12;
                }
                const ageStart = startAge + (i - 1) * 10;
                const tuhen = getTuhenstar(currentDaiunKanIndex);
                
                let tuhenDesc = "";
                if (tuhen === "比肩") tuhenDesc = "自分自身を見つめ直し、強烈な個性とこだわりで独り立ちをする季節じゃ。";
                else if (tuhen === "劫財") tuhenDesc = "大いなる目標に向かって仲間を集め、野心的に挑戦と投資を行う勝負の季節じゃ。";
                else if (tuhen === "食神") tuhenDesc = "心が穏やかに潤い、衣食住の豊かさやクリエイティブな表現活動を最高に楽しむ黄金の季節じゃ。";
                else if (tuhen === "傷官") tuhenDesc = "鋭い美意識と感性が暴れ回り、一風変わった革新や芸術・技術で頭角を現すスリリングな季節じゃ。";
                else if (tuhen === "偏財") tuhenDesc = "多くの人脈とダイナミックなお金が激しく巡り、社交性が最大化して飛び回る大循環の季節じゃ。";
                else if (tuhen === "正財") tuhenDesc = "コツコツと地道な努力が大きな信頼へと変わり、家庭や蓄財の基盤を磐石に整える確実の季節じゃ。";
                else if (tuhen === "偏官") tuhenDesc = "多忙を極め、責任の大きな役割を任される激しい季節。圧倒的な決断力とスピードで状況をハックする運気じゃ。";
                else if (tuhen === "正官") tuhenDesc = "高い名誉と品格を獲得し、社会のルールや秩序に守られながら重要なポストへと収まる安定繁栄の季節じゃ。";
                else if (tuhen === "偏印") tuhenDesc = "誰もやったことのないユニークなアイデアやハッキング、あくなき探求心で未知の領域を開拓する創造の季節じゃ。";
                else if (tuhen === "印綬") tuhenDesc = "体系的な学びや伝統、精神世界の学術を深く継承し、他者にその知恵を教え育てる知の守護神の季節じゃ。";

                daiunSteps.push({
                    ageRange: `${ageStart}歳〜${ageStart + 9}歳`,
                    title: `大運：${tuhen}期`,
                    desc: `${ageStart}歳から10年間は、そなたの魂が「${tuhen}」の気流に乗る季節じゃ。${tuhenDesc}自己の限界に挑み、宿命の波を乗りこなして開運する重要な転換期となるであろう。`
                });
            }

            // 【完全年齢連動】2026年現在の年齢
            const currentYear = 2026;
            const currentAge = currentYear - year; 
            let activeDaiun = null;
            for (let step of daiunSteps) {
                const range = step.ageRange.replace(/歳/g, "").split("〜");
                const s = parseInt(range[0]);
                const e = parseInt(range[1]);
                if (currentAge >= s && currentAge <= e) {
                    activeDaiun = step;
                    break;
                }
            }
            if (!activeDaiun) {
                activeDaiun = daiunSteps[2]; // フォールバック
            }

            // 4. 重ね合わせ五行比率の算出 (点数重み付けシステム - 100%リアル対応バグ修正)
            const weightScores = { wood: 0, fire: 0, earth: 0, metal: 0, water: 0 };
            
            function getElementOfKan(k) {
                if (k === 0 || k === 1) return "wood";
                if (k === 2 || k === 3) return "fire";
                if (k === 4 || k === 5) return "earth";
                if (k === 6 || k === 7) return "metal";
                return "water";
            }

            function addWeight(kanIndex, zhiIndex, factor) {
                const kElement = getElementOfKan(kanIndex);
                weightScores[kElement] += factor;
                const zElement = ZHI_ELEMENTS[zhiIndex];
                weightScores[zElement] += factor;
            }
            
            // 干支（0〜59）から正しい天干（%10）と地支（%12）を取得してスコア化
            const yKan = yearKanJiIndex % 10;
            const yZhi = yearKanJiIndex % 12;
            const mKan = monthKanJiIndex % 10;
            const mZhi = monthKanJiIndex % 12;
            const dKan = dayKanJiIndex % 10;
            const dZhi = dayKanJiIndex % 12;
            const hKan = hourKanJiIndex % 10;
            const hZhi = hourKanJiIndex % 12;

            addWeight(yKan, yZhi, 1); 
            addWeight(mKan, mZhi, 1); 
            weightScores[ZHI_ELEMENTS[mZhi]] += 2; // 月支（月令）に重み
            addWeight(dKan, dZhi, 1); 
            weightScores[getElementOfKan(dKan)] += 1; // 日干（自分自身）に特別重み
            addWeight(hKan, hZhi, 1); 

            const totalScore = Object.values(weightScores).reduce((a, b) => a + b, 0);
            const ratios = {
                wood: Math.round((weightScores.wood / totalScore) * 100),
                fire: Math.round((weightScores.fire / totalScore) * 100),
                earth: Math.round((weightScores.earth / totalScore) * 100),
                metal: Math.round((weightScores.metal / totalScore) * 100),
                water: Math.round((weightScores.water / totalScore) * 100)
            };

            const sum = ratios.wood + ratios.fire + ratios.earth + ratios.metal + ratios.water;
            if (sum !== 100) { ratios.fire += (100 - sum); } 

            // 5. 歴史上の有名人（偉人）マトリクス (生年月日完全対応)
            const HEROES_DATABASE = [
                { name: "坂本龍馬", title: "〜まっすぐに未来へ突き進む、維新の大木〜", desc: "甲（大木）の持つ真っ直ぐで力強い生命力と、偏印（ユニークなアイデア）などの星が結びついた姿。これまでの古い藩の境界線を壊し、日本全体を一新するほどの規格外の行動力と熱量を持っています。自分の信じた道を不屈の意志で開拓し、関わるすべての人を魅了した志士の魂です。" },
                { name: "お市の方", title: "〜周囲と美しく調和して咲いた、戦国の美しき草花〜", desc: "乙（草花）の持つしなやかで調和のとれた社交性と、偏財などの星が響き合う姿。過酷な戦国乱世の中であっても、周囲との人間関係を美しく整え、誰からも愛される華やかさを持っていました。自分の意思を内に秘めながら、しなやかに立ち続けた粘り強い美学を持っています。" },
                { name: "織田信長", title: "〜天下統一を夢見た、破壊と革新の圧倒的な太陽〜", desc: "丙（太陽）の持つ爆発的な存在感と、劫財や偏官といった強烈な星が融合した姿。これまでの古いルールや枠組みを完全に吹き飛ばし、新しい秩序を作り出す圧倒的な推進力を誇ります。そなたの内に秘められた、圧倒的な自己の存在感とプライドが最大化された姿です。" },
                { name: "宮沢賢治", title: "〜暗闇を静かに温め続けた、心優しき灯火〜", desc: "丁（灯火）の持つ静かで熱い情熱と、印綬（文学・知恵）や食神が美しく重なり合った姿。周囲を照らす優しいキャンドルの灯のように、多くの人の心に深く寄り添う芸術と哲学を遺しました。そなたの内に宿る豊かな情緒、どこまでも細やかで温かな視線そのものと言えます。" },
                { name: "武田信玄", title: "〜どっしりとした包容力で、人を引きつけ離さない信濃の大山〜", desc: "戊（大山）の持つ、びくともしない安定感と、比肩や正官の持つ統率力が一体となった姿。「人は城、人は石垣」と言った通り、関わるすべての人材を信頼して大らかに包み込む器量があります。周囲から絶大な信頼を置かれ、組織を強固に支える絶対的な存在です。" },
                { name: "徳川家康", title: "〜鳴かぬなら鳴くまで待った、大器晩成の大地〜", desc: "己（大地）の持つ実直で豊かな生命育成力と、正財などの堅実な蓄積の星からなる姿。時代の移り変わりを静かに観察し、チャンスが来るまで徹底して「守り」に徹する実直さ。組織を確実に存続させ、豊かな実りの基盤を築くための天才的な忍耐力を有しています。" },
                { name: "源義経", title: "〜電光石火のひらめきと、意志を貫く強靭な天才剣士〜", desc: "庚（刀）の持つ鋭い決断力、実行力に、傷官（天才的な直感）が重なり合った姿。常識にとらわれない驚異的な発想（鵯越の逆落としなど）で、不可能とされた逆境を瞬時に切り開きます。誇り高く、自らの美学と刃の鋭さをもって道を拓く宿命にあります。" },
                { name: "紫式部", title: "〜冷徹なまでの観察眼と、極限まで磨き抜かれた美の宝石〜", desc: "辛（宝石）の持つ繊細な美学と、傷官や印綬の持つシャープな知性が融合した姿。美しい世界を冷静に、かつ官能的に描き出す『源氏物語』の天才女流作家。周囲との馴れ合いを嫌い、自分の芸術と知的領域を極限まで磨き上げた崇高な美意識の職人です。" },
                { name: "葛飾北斎", title: "〜規格外の知性とスケールで、世界を躍動させた大海の絵師〜", desc: "壬（大海）の持つ留まることのないダイナミックな創造力と、偏印（未知へのあくなき好奇心）が結びついた姿。一カ所に留らず生涯にわたって絵を描き続け、波濤の生命力を描いて世界を驚かせました。そなたの内に秘めた圧倒的な行動スケールと知的好奇心そのものです。" },
                { name: "千利休", title: "〜静寂の中で一滴の水を滴らせ、人々の乾いた心を潤した茶聖〜", desc: "癸（雨露）の持つ静かで知的な浸透力に、食神や傷官が結びついて「侘び茶」の美学を完成させた姿。多くの言葉を語らずとも、そっと置いた一輪の花や茶碗の美しさで、戦国大名たちの心を深く潤し統制しました。優れた直感と、静寂の美学で世界を支配する芸術の姿です。" }
            ];

            let heroRes = HEROES_DATABASE[nikkan]; 
            // Saiko-sama（丙・太陽 × 食神）の特別イースターエッグ
            if (nikkan === 2 && chushinStar === "食神") {
                heroRes = {
                    name: "額田王",
                    title: "〜宮廷全体を照らした、万葉集 of 天才カリスマ歌人〜",
                    desc: "丙（太陽）のような強烈な輝きを持ち、食神（表現の天才）の豊かな感性で万葉集に美しい歌を遺した天才歌人。冠帯（女王様）の気高さを持ち、多くの人々を魅了する天性のカリスマ。そなたの内に宿りし豊かな表現衝動は、彼女が宮廷全体を包み込んだ陽のエネルギーそのものと言えましょう。"
                };
            }

            // 6. 動的なたっぷりと深掘りした項目別テキストの生成 (個人に合わせてフレキシブルに可変合成)
            const chushinTextMap = {
                "比肩": "自分の力で道を切り拓く自立心の強さ。他人に頼らず、独自の信念を貫くことに最大の喜びを感じる資質を持っています。",
                "劫財": "大きな目標を達成するための組織力と強い意志。困難な状況をも味方に変えるタフさを持っています。",
                "食神": "人生を楽しく表現し、五感で味わい尽くす才能。明るく親しみやすく、周囲に癒やしと笑顔を届けます。",
                "傷官": "極めて鋭い感性と完璧な美意識。妥協を許さない表現力と、物事の本質を瞬時に見抜く聡明さがあります。",
                "偏財": "豊かな人脈を築き、エネルギーを循環させる社交の才能。人を喜ばせることが好きで、フットワークが軽いです。",
                "正財": "誠実で堅実な信頼関係の構築と蓄積の才能。丁寧な仕事ぶりで周囲から抜群の安定感を持たれます。",
                "偏官": "圧倒的な行動力と責任感。逆境に強く、リーダーシップを発揮してスピーディに課題を解決します。",
                "正官": "高い気品と義務感。秩序やルールを重んじ、社会的な名誉と信頼を重んじる品格があります。",
                "偏印": "常識にとらわれないユニークな発想力と知的好奇心。新しいものをハックし、開拓するアイデアマンです。",
                "印綬": "体系的な学びと、伝統的な知恵の継承。知的で落ち着きがあり、他者に知識を分かりやすく教える知性派です。"
            };
            const chushinText = chushinTextMap[chushinStar] || chushinTextMap["食神"];

            // 個人の日干（10種）×中心星（10種）を高度に掛け合わせた「深層神託」のフレキシブル自動合成
            const nikkanAesthetics = [
                "そなたは内なる生命力の源流たる【甲・大木】を宿しており、天に届かんとする真っ直ぐな大志と、周囲をそっと雨宿りさせる優しい包容力を本質的に兼ね備えています。",
                "そなたは調和と優美の結晶たる【乙・草花】を宿しており、どんな過酷な冬の土壌であっても、密やかに、かつ誰よりも粘り強くしなやかに美しきコミュニティを根付かせる社交性に優れています。",
                "そなたは世界の闇をすべて消し去る【丙・太陽】を宿しており、圧倒的なカリスマ的熱量とオープンで無邪気な陽気さを放ち、凍りついた人々の心を溶かす天性の生命力を有しています。",
                "そなたは暗闇を密やかに温める【丁・灯火】を宿しており、深い洞察力に根ざした独自の美意識と、人々の感情にそっと寄り添い照らす繊細な気配り・独特の引力を有しています。",
                "そなたは全てを大らかに包み込む【戊・大山】を宿しており、圧倒的な安定感と揺るぎない包容力によって、自然と多くの人脈や有益な資源を豊かに磁石のように引き寄せる圧倒的な信頼性を持っています。",
                "そなたは万物を静かに育み実らせる【己・大地】を宿しており、極めて温和で親しみやすく、他者の才能の種を預かり見事に開花させる、教育と育成の天性たる偉大な包容力を持っています。",
                "そなたは不屈の決断と意志の結晶たる【庚・鋼剣】を宿しており、いかなる逆境や古いルールに行く手を阻まれようとも、己の知性と覚悟の刃をもって瞬時に新時代を切り開く強さを持っています。",
                "そなたは極上の気品と芸術を秘めし【辛・宝石】を宿しており、誰とも群れない崇高な美意識と、冷徹なまでの観察眼をもって世界を官能的かつ知的に再定義する比類なき品格を誇っています。",
                "そなたは全てを呑み込み果てなく広がる【壬・大海】を宿しており、常識の枠組みを悠々と飛び越えるスケールの大きな知性と、臨機応変に自らを変幻自在にハックするダイナミックな推進力を誇ります。",
                "そなたは静かに優しく大地に滴る【癸・雨露】を宿しており、極めて深い共感力と優れた直感力によって、人々の乾いた心の奥底に染み入り潤す、精神世界の高い守護神たる知恵を持っています。"
            ];

            const nikkanShadows = [
                "しかし、大木が一度折れると修復に時間がかかるように、予期せぬ挫折やプライドの衝突に直面した際、意固地になって周囲のアドバイスを完全に拒絶してしまう頑固さが影を潜めています。",
                "しかし、草花が周囲の環境（土壌）に激しく影響を受けるように、過度に伴侶や他人の顔色、流行の風潮に依存しすぎて自分自身の本音（軸）を見失ってしまう過保護さが影を潜めています。",
                "しかし、太陽がギラギラと照りすぎると砂漠化を引き起こすように、熱量過多によって一方的に自分の感情を他者へぶつけ、気分やモチベーションが冷めると急に冷淡にシャッターを下ろしてしまう極端さが影を潜めています。",
                "しかし、灯火の炎が風に繊細に揺れるように、極めてデリケートなガラスのハートを有しており、ささいな批判や無神経な言葉によって深く内にこもり、自傷的な暗闇の執着を募らせてしまう危うさが影を潜めています。",
                "しかし、大山がめったに動かないように、重すぎる腰と変化への激しい抵抗感があり、自ら動くチャンスを「安定維持」という名目のもとに不意にし、思考停止に陥りやすい強情さが影を潜めています。",
                "しかし、大地がすべてを無条件で受け入れるがゆえに、不誠実な存在や自分をすり減らす人間関係まで抱え込んでしまい、都合よく利用されながら内側に泥濘のような不満を溜めてしまう自己犠牲の危うさがあります。",
                "しかし、研ぎ澄まされた刃が周囲を威嚇するように、その決断のスピーディさと無駄を嫌う冷徹さが、時に他者への「言葉の刃」となって周囲を必要以上に萎縮させ、孤立を招いてしまう苛烈さが影を潜めています。",
                "しかし、宝石が少しの傷や曇りも許さないように、極端な完璧主義と神経質な美意識があり、他人の大雑把な振る舞いや不潔さに耐えかねて、傲慢でマウントを取りがちな女王様気質が影を潜めています。",
                "しかし、大海が一度暴れ出すと大洪水を引き起こすように、自由奔放さが行き過ぎて責任やルールを完全に放棄し、周りの地道な手続きやフォローを無自覚に軽視して周囲をパニックに陥れる身勝手さが影を潜めています。",
                "しかし、静かな雨が時に視界を塞ぐ深い霧となるように、自分の内側だけで複雑な不安や被害妄想をぐるぐると煮詰めてしまい、冷たい沈黙でもって他者をコントロールしようとする湿った執着が影を潜めています。"
            ];

            const nikkanDesires = [
                "あなたの魂は、ただ義務で他人の世話をするのではなく、自身の信じた価値ある信念を堂々と社会に表明し、尊敬を持って認められることを求めています。自分軸を自由に伸ばせる環境こそが、魂を最も開運に導くのです。",
                "あなたの魂は、殺伐とした競争の世界ではなく、信頼できる温かいコミュニティに守られ、そこへ自分の美しい気配りや調整能力を惜しみなく提供して、調和に満ちた笑顔に囲まれることを求めています。",
                "あなたの魂は、他人が作った古いルールや型に無理やりはめ込まれることを拒絶し、いつでも自分のパッション（直感）の赴くままに自己のエネルギーを最大解放し、その輝きを無条件で全肯定されることを求めています。",
                "あなたの魂は、ガサツで騒がしい日常の雑音から離れ、自分の優れた感性と深い精神性を理解してくれる限られた心優しきソウルメイトと、静寂の中で深い美の精神を語り合える安全な隠れ家を求めています。",
                "あなたの魂は、毎日の不確実な変動に右往左往することなく、自分がどっしりと支配できる磐石な土俵を整え、そこに豊かな人や資産、情報が集積して、周囲から絶対的な頼れる砦として頼られることを求めています。",
                "あなたの魂は、目立つことよりも、自分が心を注いだ対象（人、植物、作品など）が美しく豊かに育ちゆく成長のプロセスを特等席で見届け、その実りを仲間と大らかに分かち合う慈愛の空間を求めています。",
                "あなたの魂は、惰性や馴れ合いのぬるま湯に浸かり続けることを極度に嫌い、常に自分の限界に挑戦できる高い壁と、自らの刃で状況をハックして勝ち取った「真実の勝利」を渇望しています。",
                "あなたの魂は、ありふれた量産品や凡庸な生き方を心の底から嫌い、自分の妥協なき美学や知の領域を極限まで磨き上げ、誰も侵すことのできない「唯一無二の気高き美意識の城」を現世に打ち建てることを求めています。",
                "あなたの魂は、一箇所に留まる拘束を極度に恐れ、常に新しい知の航路（未知の旅）へと自らの知的好奇心を躍動させ、ダイナミックに変幻自在に自分をアップデートし続けられる絶対的な自由を求めています。",
                "あなたの魂は、物質的な豊かさだけでは決して満たされず、心の深層にある見えない真実や直感を頼りに、人々の傷ついた精神を静かに癒やし、神秘的な愛の泉で全てを包み込んで一体化することを求めています。"
            ];

            const nikkanMissions = [
                "その使命は、誰かが作ったレールの上を走ることではありません。あなた自身の真っ直ぐな意志によって、新しい企画やプロジェクト、組織の軸となる「大黒柱」としての基盤を創出し、関わる人々に安心という豊かな木陰を提供することです。",
                "その使命は、対立する人々の間にしなやかに立ち入り、優れた社交性と独自の美的コーディネート力を発揮して、バラバラだった組織や概念を美しく一つのタペストリーのように紡ぎ合わせる「美しき調和のプロデューサー」となることです。",
                "その使命は、停滞した組織や暗闇の迷路に迷い込んだ人々の前に立ち、あなたの圧倒的なクリエイティブな表現力と明るさをもって、そこに新しいエネルギーを点火し、関わるすべての人を巻き込んで祭りを先導する「太陽の開拓リーダー」となることです。",
                "その使命は、誰もが素通りしてしまう日常の細やかな美や他者の繊細な感情をすくい上げ、あなたにしか描けない芸術、文学、あるいは深く寄り添うマンツーマンの精神的サポートを通じて、人々の心に静かに火を灯す「美と精神の紡ぎ手」となることです。",
                "その使命は、時代の流行り廃りに流されない「王道の仕組み・基盤」をどっしりと社会に打ち建て、そこにバラバラに存在していた才能やリソースを統合し、関わるすべての存在を守り存続させる「大いなる資産と信頼の統治者」となることです。",
                "その使命は、世の中に散らばる未完の才能やアイデアをあなたの豊かな土壌で預かり、丁寧な教育やプロデュースによって立派な価値あるものへと育て上げる、優れた「プラットフォームの構築者・偉大なるメンター」となることです。",
                "その使命は、古い常識や不要になった既得権益をあなたの鋭い決断の刃で一刀両断に粉砕し、不可能と言われた状況に電光石火のハッキングを仕掛け、誰も見たことのない革新の道を鮮やかに切り拓く「新時代のイノベーター」となることです。",
                "その使命は、どこにでもある凡庸な世界に「極上の美学と本物の品格」をもたらすことです。クリエイティブ、ブランディング、デザイン、専門的コンサルタントなど、あなたの研ぎ澄まされた独自の視点が100%宿る高級な成果物で世界を驚かせることです。",
                "その使命は、古い国境やジャンル、既成概念の枠組みをあなたの圧倒的な知性の奔流で押し流し、より自由でグローバル、かつダイナミックな新しい選択肢を世界に発信・提供し、人々の視野を宇宙スケールへと拡大させることです。",
                "その使命は、目に見える形ある世界の裏側に潜む「真の知恵・精神性・癒やし」を言語化、または感覚的に人々に継承することです。スピリチュアルな学術、カウンセリング、茶道やアートなど、言葉にならない美の精神で乾いた現代社会を潤すことです。"
            ];

            const proDetailText = `${nikkanAesthetics[nikkan]} さらに、${chushinText} これらが相乗効果を発揮することで、そなただけの大いなるカリスマ性と独自の立ち位置が社会で花開くはずじゃ。`;
            const conDetailText = `楽しむ力や高い推進力の裏側には、どうしても裏返しの弱点（陰の戒め）が存在するもの。そなたの課題は、${nikkanShadows[nikkan]} これらを克服するには、あらかじめ客観的な「仕組み化」を行うか、フォローしてくれる誠実な相棒を側に置くことが、そなたの天星を最大化する秘訣となります。`;
            const desireDetailText = `そなたの深層心理は、「${chushinStar}」のエネルギーを完全に解放し、ありのままの自分を肯定されることを求めています。${nikkanDesires[nikkan]}`;
            const missionDetailText = `この現世においてあなたが果たすべき使命は、これまでの枠組みにとらわれない新しい視点や、優れた美的な感性を社会に吹き込むことに他なりません。具体的には、${nikkanMissions[nikkan]}`;

            const activeMonthly = monthlyFortunes[nikkan] || monthlyFortunes[2];

            const yearText = `2026年は【丙午】の強い火が巡る活動期。そなたの${nikkanChar}の個性を社会にダイレクトに打ち出し、古い自分をダイナミックに着替える最高の季節じゃ。焦らず、内に秘めた熱量を形にしていきなさい。`;

            // 折れ線グラフSVG波形の自動生成
            const waveMap = {
                0: "M 0 45 Q 25 10 50 35 T 100 15",
                1: "M 0 35 Q 25 40 50 20 T 100 30",
                2: "M 0 20 Q 25 30 50 15 T 100 40",
                3: "M 0 40 Q 25 10 50 30 T 100 25",
                4: "M 0 30 Q 25 20 50 40 T 100 15",
                5: "M 0 25 Q 25 35 50 10 T 100 35",
                6: "M 0 45 Q 25 15 50 30 T 100 20",
                7: "M 0 30 Q 25 45 50 25 T 100 10",
                8: "M 0 15 Q 25 30 50 40 T 100 15",
                9: "M 0 40 Q 25 25 50 15 T 100 30"
            };
            const currentWave = waveMap[nikkan] || waveMap[2];

            // 編集モード対応：動的算出された最新文章でキャッシュを即時更新 (上書き先祖返りバグ解決)
            editedTexts["oracle_detail.pro"] = proDetailText;
            editedTexts["oracle_detail.con"] = conDetailText;
            editedTexts["oracle_detail.desire"] = desireDetailText;
            editedTexts["oracle_detail.mission"] = missionDetailText;
            editedTexts["hero.name"] = heroRes.name;
            editedTexts["hero.title"] = heroRes.title;
            editedTexts["hero.desc"] = heroRes.desc;
            editedTexts["monthly.title"] = `五行巡る今月の神託：【${activeMonthly.title}】`;
            editedTexts["monthly.desc"] = activeMonthly.desc;
            editedTexts["monthly.action"] = activeMonthly.action;
            editedTexts["biorhythm.daiun_title"] = activeDaiun.ageRange + " " + activeDaiun.title;
            editedTexts["biorhythm.daiun_desc"] = activeDaiun.desc;
            editedTexts["biorhythm.year_stat"] = yearText;

            setAppValues(
                userName,
                `${year}年${month}月${day}日 ${timeInput || "時間不明"}生まれ (${isFemale ? '女' : '男'})`,
                nikkanChar, nikkanDescChar,
                chushinStar, `（魂の本音：${chushinSimpleDesc[chushinStar] || "本音"}）`,
                activeEnergy, `（行動スタイル：${energySimpleDesc[activeEnergy] || "エネルギー"}）`,
                heroRes.name, heroRes.title,
                heroRes.desc,
                proDetailText,
                conDetailText,
                desireDetailText,
                missionDetailText,
                activeDaiun.ageRange + " " + activeDaiun.title,
                activeDaiun.desc,
                yearText,
                ratios,
                currentWave,
                activeMonthly
            );
        }

        // 画面の値を更新する関数
        function setAppValues(name, birthInfo, nikkan, nikkanDesc, chushin, chushinDesc, energy, energyDesc, heroName, heroTitle, heroDesc, pro, con, desire, mission, daiunTitle, d_desc, y_desc, ratios, wavePath, activeMonthly) {
            document.getElementById('user-display-name').innerText = `${name} の星図`;
            document.getElementById('display-birth').innerText = birthInfo;
            
            // 命式更新（占いの結果を100%正確に、わかりやすい補足テキストと合致させて反映）
            document.getElementById('ui-nikkan').innerHTML = `${nikkan} <span class="text-xs text-gray-400 font-normal">${nikkanDesc}</span>`;
            document.getElementById('ui-chushin').innerHTML = `${chushin} <span class="text-xs text-gray-400 font-normal">${chushinDesc}</span>`;
            document.getElementById('ui-energy').innerHTML = `${energy} <span class="text-xs text-gray-400 font-normal">${energyDesc}</span>`;
            
            // 有名人更新
            document.getElementById('ui-hero-name').innerText = heroName;
            document.getElementById('ui-hero-title').innerText = heroTitle;
            document.getElementById('ui-hero-desc').innerText = heroDesc;
            
            // 長所・短所・深層・天職の詳細長文流し込み
            document.getElementById('ui-pro-detail').innerText = pro;
            document.getElementById('ui-con-detail').innerText = con;
            document.getElementById('ui-desire-detail').innerText = desire;
            document.getElementById('ui-mission-detail').innerText = mission;

            // 今月の運勢のアップデート
            document.getElementById('ui-monthly-title').innerText = `五行巡る今月の神託：【${activeMonthly.title}】`;
            document.getElementById('ui-monthly-desc').innerText = activeMonthly.desc;
            document.getElementById('ui-monthly-action').innerText = activeMonthly.action;

            // バイオリズムデータ流し込み
            document.getElementById('biorhythm-daiun-title').innerText = daiunTitle;
            document.getElementById('biorhythm-daiun-desc').innerText = d_desc;
            document.getElementById('biostat-year').innerText = y_desc;

            // 波形のSVGアップデート
            document.getElementById('biopath').setAttribute("d", wavePath);
            document.getElementById('biorhythm-wave-info').innerText = `※あなたの魂のエネルギー波形は現在、美しく活性化する上昇バイオリズムに連動しています。`;

            // 重ね合わせ五行サークルのサイズ・位置の動的アップデート
            updateCircle('node-wood', ratios.wood, 'wood', 'ratio-wood');
            updateCircle('node-fire', ratios.fire, 'fire', 'ratio-fire');
            updateCircle('node-earth', ratios.earth, 'earth', 'ratio-earth');
            updateCircle('node-metal', ratios.metal, 'metal', 'ratio-metal');
            updateCircle('node-water', ratios.water, 'water', 'ratio-water');

            // 編集用メモリデータの更新
            syncEditableDomToCache();
        }

        // サークルの描画サイズ変更ロジック (比率・五行100%動的リサイズ連携)
        function updateCircle(id, ratio, element, ratioTextId) {
            const node = document.getElementById(id);
            const textSpan = document.getElementById(ratioTextId);
            textSpan.innerText = `${ratio}%`;

            // 完全に算出比率に基づいて滑らかに大きさを可変
            const minSize = 45;
            const maxSize = 175;
            const size = minSize + (ratio / 100) * (maxSize - minSize);
            
            node.style.width = `${size}px`;
            node.style.height = `${size}px`;

            // 重なりブレンドが最美に見えるポジションへの連動
            let top, left;
            switch(element) {
                case 'wood':  
                    top = `${55 - (ratio/2)}%`; left = `${30 - (ratio/2)}%`;
                    break;
                case 'fire':  
                    top = `${30 - (ratio/2)}%`; left = `${50 - (ratio/2)}%`;
                    break;
                case 'earth': 
                    top = `${35 - (ratio/2)}%`; left = `${65 - (ratio/2)}%`;
                    break;
                case 'metal': 
                    top = `${55 - (ratio/2)}%`; left = `${60 - (ratio/2)}%`;
                    break;
                case 'water': 
                    top = `${60 - (ratio/2)}%`; left = `${45 - (ratio/2)}%`;
                    break;
            }

            node.style.top = top;
            node.style.left = left;
        }

        // ==========================================
        // タブの切り替え制御（詳細神託 vs 今月の神託 vs バイオリズム）
        function switchResultTab(tabName) {
            const btnDetail = document.getElementById("tab-btn-detail");
            const btnMonthly = document.getElementById("tab-btn-monthly");
            const btnBio = document.getElementById("tab-btn-biorhythm");
            
            const panelDetail = document.getElementById("result-tab-detail");
            const panelMonthly = document.getElementById("result-tab-monthly");
            const panelBio = document.getElementById("result-tab-biorhythm");

            playMysticSound(330, 'sine', 0.05, 0.2);

            const inactiveClass = "flex-1 sm:flex-initial py-2.5 px-4 rounded-full bg-red-950/40 hover:bg-red-950/80 text-[#e5c398] text-xs border border-red-800/40 tracking-widest transition-all flex items-center justify-center gap-1";
            const activeClass = "flex-1 sm:flex-initial py-2.5 px-4 rounded-full bg-[#e5c398] text-red-950 text-xs font-bold tracking-widest transition-all shadow-[0_0_10px_rgba(229,195,152,0.3)] flex items-center justify-center gap-1";

            btnDetail.className = inactiveClass;
            btnMonthly.className = inactiveClass;
            btnBio.className = inactiveClass;

            panelDetail.classList.add("hidden");
            panelMonthly.classList.add("hidden");
            panelBio.classList.add("hidden");

            if (tabName === 'detail') {
                btnDetail.className = activeClass;
                panelDetail.classList.remove("hidden");
            } else if (tabName === 'monthly') {
                btnMonthly.className = activeClass;
                panelMonthly.classList.remove("hidden");
            } else {
                btnBio.className = activeClass;
                panelBio.classList.remove("hidden");
            }
        }

        // 儀式のリセット（最初からやり直し）
        function resetShrine() {
            playMysticSound(440, 'sine', 0.1, 0.2);

            const result = document.getElementById("destiny-result");
            result.classList.add("opacity-0");
            setTimeout(() => {
                result.classList.add("hidden");

                const prevEl = document.getElementById(SAIKO_DIALOGUES[currentStep].targetId);
                prevEl.classList.add("hidden");

                const elements = ["wood", "fire", "earth", "metal", "water"];
                elements.forEach(el => {
                    const node = document.getElementById(`load-${el}`);
                    node.className = "absolute w-6 h-6 rounded-full opacity-20 transition-all duration-500 text-[9px] text-white flex items-center justify-center";
                    if(el === "wood") node.className += " top-4 bg-green-700/60 border border-green-400";
                    if(el === "fire") node.className += " right-4 top-1/3 bg-red-700/60 border border-red-400";
                    if(el === "earth") node.className += " right-12 bottom-4 bg-yellow-700/60 border border-yellow-400";
                    if(el === "metal") node.className += " left-12 bottom-4 bg-gray-500/60 border border-white";
                    if(el === "water") node.className += " left-4 top-1/3 bg-blue-700/60 border border-blue-400";
                });

                document.getElementById("btn-reveal-destiny").classList.add("hidden");

                currentStep = 0;
                switchResultTab('detail'); 
                
                const startEl = document.getElementById(SAIKO_DIALOGUES[0].targetId);
                startEl.classList.remove("hidden");

                updateProgressDots();

                document.getElementById("btn-back").classList.add("invisible");

                const btnSpan = document.getElementById("btn-next").querySelector("span");
                btnSpan.innerText = "次なる問いへ";
                document.getElementById("next-icon").innerHTML = `
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path>
                    </svg>
                `;

                const opScreen = document.getElementById('seq-opening');
                opScreen.classList.remove('hidden');
                setTimeout(() => {
                    opScreen.classList.remove('scale-[0.95]', 'opacity-0');
                }, 50);

            }, 500);
        }

        // ==========================================
        // 直接編集モードの切り替え
        function toggleEditMode() {
            const toggle = document.getElementById('edit-mode-toggle');
            isEditModeActive = toggle.checked;
            
            const editableElements = document.querySelectorAll('[data-editable-key]');
            const exportBtn = document.getElementById('btn-export-json');

            playMysticSound(220, 'sine', 0.1, 0.2);

            if (isEditModeActive) {
                exportBtn.classList.remove('hidden');
                editableElements.forEach(el => {
                    el.setAttribute('contenteditable', 'true');
                    el.addEventListener('blur', handleEditableBlur);
                });
            } else {
                exportBtn.classList.add('hidden');
                editableElements.forEach(el => {
                    el.removeAttribute('contenteditable');
                    el.removeEventListener('blur', handleEditableBlur);
                });
            }
        }

        // 編集完了時のローカルキャッシュ同期
        function handleEditableBlur(event) {
            const el = event.target;
            const key = el.getAttribute('data-editable-key');
            if (key) {
                editedTexts[key] = el.innerText.trim();
                
                if (key.startsWith('dialogue.step_')) {
                    const idx = parseInt(key.split('_')[1]);
                    if (!isNaN(idx) && SAIKO_DIALOGUES[idx]) {
                        SAIKO_DIALOGUES[idx].speech = el.innerText.trim();
                    }
                }
            }
        }

        // 現在表示されているEditableなDOM要素からキャッシュを更新する
        function syncEditableDomToCache() {
            const editableElements = document.querySelectorAll('[data-editable-key]');
            editableElements.forEach(el => {
                const key = el.getAttribute('data-editable-key');
                if (key) {
                    if (!editedTexts[key]) {
                        editedTexts[key] = el.innerText.trim();
                    } else {
                        el.innerText = editedTexts[key];
                    }
                }
            });
        }

        // JSON出力モーダルの表示
        function openExportModal() {
            playMysticSound(330, 'triangle', 0.15, 0.5);
            
            const area = document.getElementById('json-export-area');
            area.value = JSON.stringify(editedTexts, null, 4);

            const modal = document.getElementById('export-modal');
            modal.classList.remove('hidden');
            setTimeout(() => {
                modal.classList.remove('opacity-0');
            }, 50);
        }

        // モーダル閉じる
        function closeExportModal() {
            playMysticSound(220, 'sine', 0.1, 0.2);
            const modal = document.getElementById('export-modal');
            modal.classList.add('opacity-0');
            setTimeout(() => {
                modal.classList.add('hidden');
            }, 300);
        }

        // 歴史上の人物に例えるとモーダル表示
        function openHeroModal() {
            playMysticSound(330, 'triangle', 0.15, 0.5);
            
            // 現在の結果から動的に値を引っ張って設定
            const name = document.getElementById('ui-hero-name').innerText;
            const title = document.getElementById('ui-hero-title').innerText;
            const desc = document.getElementById('ui-hero-desc').innerText;
            
            document.getElementById('modal-hero-name').innerText = name;
            document.getElementById('modal-hero-title').innerText = title;
            document.getElementById('modal-hero-desc').innerText = desc;
            
            const modal = document.getElementById('hero-modal');
            modal.classList.remove('hidden');
            setTimeout(() => {
                modal.classList.remove('opacity-0');
            }, 50);
        }

        // 歴史上の人物に例えるとモーダル非表示
        function closeHeroModal() {
            playMysticSound(220, 'sine', 0.1, 0.2);
            const modal = document.getElementById('hero-modal');
            modal.classList.add('opacity-0');
            setTimeout(() => {
                modal.classList.add('hidden');
            }, 300);
        }

        // クリップボードコピー
        function copyJsonToClipboard() {
            playMysticSound(440, 'sine', 0.15, 0.3);
            const area = document.getElementById('json-export-area');
            area.select();
            document.execCommand('copy');
            
            alert("編集された神託データベース（JSON）をクリップボードにコピーしました！");
            closeExportModal();
        }
    </script>
</body>
</html>
