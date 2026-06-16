<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>그림자 군주: 던전 슬래셔</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700;800&family=Orbitron:wght@700;900&family=Noto+Sans+KR:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans KR', 'Inter', sans-serif;
            background-color: #0b0f19;
            user-select: none;
            -webkit-user-select: none;
        }
        .orbitron {
            font-family: 'Orbitron', sans-serif;
        }
        /* Custom scrollbar for RPG logs */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #111827;
        }
        ::-webkit-scrollbar-thumb {
            background: #374151;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #4b5563;
        }
        /* Glow animations */
        @keyframes pulse-glow {
            0%, 100% { box-shadow: 0 0 15px rgba(239, 68, 68, 0.4); }
            50% { box-shadow: 0 0 25px rgba(239, 68, 68, 0.8); }
        }
        .boss-glow {
            animation: pulse-glow 2s infinite;
        }
        canvas {
            image-rendering: pixelated;
        }
    </style>
</head>
<body class="text-gray-100 min-h-screen flex flex-col items-center justify-between p-2 md:p-4 overflow-x-hidden">

    <!-- MAIN CONTAINER -->
    <div id="game-container" class="w-full max-w-6xl bg-gray-900 border border-gray-800 rounded-2xl shadow-2xl overflow-hidden flex flex-col">
        
        <!-- HEADER / NAVIGATION STATS -->
        <header class="bg-gray-950 px-6 py-4 border-b border-gray-800 flex flex-wrap justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <div class="bg-red-600 p-2.5 rounded-lg shadow-lg flex items-center justify-center">
                    <i class="fa-solid fa-dragon text-xl text-white"></i>
                </div>
                <div>
                    <h1 class="text-xl md:text-2xl font-black text-red-500 tracking-wider orbitron">SHADOW SLAYER</h1>
                    <p class="text-xs text-gray-400">그림자 던전의 수호자들을 토벌하십시오</p>
                </div>
            </div>
            
            <!-- Sound & Music Toggle -->
            <div class="flex items-center gap-4">
                <button id="sound-toggle" class="bg-gray-800 hover:bg-gray-700 px-4 py-2 rounded-lg text-sm transition flex items-center gap-2 border border-gray-700">
                    <i id="sound-icon" class="fa-solid fa-volume-high text-green-400"></i>
                    <span>효과음: <strong id="sound-status">켜짐</strong></span>
                </button>
                <div class="text-right">
                    <span class="text-xs text-gray-400 block">현재 층수</span>
                    <span id="current-floor-badge" class="text-lg font-bold text-amber-400 orbitron">FLOOR 1</span>
                </div>
            </div>
        </header>

        <!-- GAMEPLAY / DASHBOARD REGION -->
        <div class="flex flex-col lg:flex-row min-h-[500px]">
            
            <!-- LEFT: GAME CANVAS & VISUAL CONTROLS -->
            <div class="flex-1 bg-gray-950 p-4 flex flex-col items-center justify-center relative border-r border-gray-800">
                
                <!-- START SCREEN OVERLAY -->
                <div id="start-screen" class="absolute inset-0 bg-gray-950 z-30 flex flex-col items-center justify-center p-6 text-center">
                    <div class="max-w-md w-full">
                        <i class="fa-solid fa-dice-d20 text-6xl text-red-500 mb-4 animate-bounce"></i>
                        <h2 class="text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-red-500 via-amber-400 to-yellow-500 mb-2">클래스 선택</h2>
                        <p class="text-gray-400 text-sm mb-6">던전에 도전할 영웅을 선택하고 심연을 격파하세요.</p>
                        
                        <!-- Class Cards -->
                        <div class="grid grid-cols-3 gap-3 mb-6">
                            <!-- Warrior -->
                            <button onclick="selectClass('WARRIOR')" class="class-card bg-gray-900 hover:bg-gray-800 border-2 border-red-600 rounded-xl p-3 transition flex flex-col items-center gap-2 group transform hover:-translate-y-1">
                                <div class="w-12 h-12 rounded-full bg-red-900/40 flex items-center justify-center border border-red-500/30 text-red-400 group-hover:scale-110 transition">
                                    <i class="fa-solid fa-shield-halved text-2xl"></i>
                                </div>
                                <span class="font-bold text-sm text-gray-200">전사</span>
                                <span class="text-[10px] text-gray-400">높은 체력/방어</span>
                            </button>
                            <!-- Mage -->
                            <button onclick="selectClass('MAGE')" class="class-card bg-gray-900 hover:bg-gray-800 border-2 border-transparent rounded-xl p-3 transition flex flex-col items-center gap-2 group transform hover:-translate-y-1">
                                <div class="w-12 h-12 rounded-full bg-blue-900/40 flex items-center justify-center border border-blue-500/30 text-blue-400 group-hover:scale-110 transition">
                                    <i class="fa-solid fa-wand-sparkles text-2xl"></i>
                                </div>
                                <span class="font-bold text-sm text-gray-200">마법사</span>
                                <span class="text-[10px] text-gray-400">강력한 범위 마법</span>
                            </button>
                            <!-- Rogue -->
                            <button onclick="selectClass('ROGUE')" class="class-card bg-gray-900 hover:bg-gray-800 border-2 border-transparent rounded-xl p-3 transition flex flex-col items-center gap-2 group transform hover:-translate-y-1">
                                <div class="w-12 h-12 rounded-full bg-purple-900/40 flex items-center justify-center border border-purple-500/30 text-purple-400 group-hover:scale-110 transition">
                                    <i class="fa-solid fa-bolt text-2xl"></i>
                                </div>
                                <span class="font-bold text-sm text-gray-200">도적</span>
                                <span class="text-[10px] text-gray-400">치명타/빠른 이동</span>
                            </button>
                        </div>

                        <!-- Class Description Details -->
                        <div id="class-desc-box" class="bg-gray-900 border border-gray-800 p-4 rounded-xl text-left mb-6 text-xs text-gray-300">
                            <!-- Dynamic Content -->
                            <p class="font-semibold text-sm text-red-400 mb-1"><i class="fa-solid fa-shield-halved mr-1"></i> 전사 (Warrior)</p>
                            <p class="mb-2">검과 방패를 장비한 용사입니다. 균형 잡힌 피해량과 우수한 방어도로 전선에서 장기적인 돌파 능력을 가집니다.</p>
                            <div class="grid grid-cols-2 gap-2 text-[11px] text-gray-400">
                                <div><i class="fa-solid fa-heart text-red-500 mr-1"></i> 시작 HP: 120</div>
                                <div><i class="fa-solid fa-bolt text-blue-400 mr-1"></i> 시작 MP: 40</div>
                                <div><i class="fa-solid fa-hand-fist text-amber-500 mr-1"></i> 주능력: 근접 파괴</div>
                                <div><i class="fa-solid fa-shield text-emerald-400 mr-1"></i> 특화: 피해 감소 15%</div>
                            </div>
                        </div>

                        <button id="btn-start" onclick="startGame()" class="w-full bg-gradient-to-r from-red-600 to-amber-500 hover:from-red-500 hover:to-amber-400 text-white py-3 rounded-xl font-extrabold text-base tracking-wider transition shadow-lg shadow-red-900/30">
                            모험 시작하기
                        </button>
                    </div>
                </div>

                <!-- GAMEOVER SCREEN -->
                <div id="gameover-screen" class="absolute inset-0 bg-gray-950/90 z-30 hidden flex flex-col items-center justify-center p-6 text-center">
                    <div class="max-w-md w-full bg-gray-900 p-8 rounded-2xl border-2 border-red-600 shadow-2xl boss-glow">
                        <i class="fa-solid fa-skull-crossbones text-6xl text-red-600 mb-4 animate-pulse"></i>
                        <h2 class="text-3xl font-extrabold text-red-500 mb-2">사망하였습니다</h2>
                        <p class="text-gray-400 text-sm mb-6">심연의 힘이 당신을 지배했습니다. 던전에 더 강한 장비를 갖춰 재도전하세요.</p>
                        
                        <!-- Stats Grid -->
                        <div class="bg-gray-950 p-4 rounded-xl text-left mb-6 text-sm grid grid-cols-2 gap-3 border border-gray-800">
                            <div><span class="text-gray-400">최종 레벨:</span> <strong id="go-level" class="text-amber-400">1</strong></div>
                            <div><span class="text-gray-400">도달 층수:</span> <strong id="go-floor" class="text-amber-400">1</strong></div>
                            <div><span class="text-gray-400">처치한 몬스터:</span> <strong id="go-kills" class="text-red-400">0</strong></div>
                            <div><span class="text-gray-400">획득 골드:</span> <strong id="go-gold" class="text-yellow-400">0</strong></div>
                        </div>

                        <button onclick="resetGameToStart()" class="w-full bg-red-600 hover:bg-red-500 text-white py-3 rounded-xl font-bold transition">
                            메인 화면으로
                        </button>
                    </div>
                </div>

                <!-- VICTORY SCREEN -->
                <div id="victory-screen" class="absolute inset-0 bg-gray-950/95 z-30 hidden flex flex-col items-center justify-center p-6 text-center">
                    <div class="max-w-md w-full bg-gray-900 p-8 rounded-2xl border-2 border-yellow-500 shadow-2xl">
                        <i class="fa-solid fa-crown text-6xl text-yellow-500 mb-4 animate-bounce"></i>
                        <h2 class="text-3xl font-extrabold text-yellow-400 mb-2">던전 정복 성공!</h2>
                        <p class="text-gray-400 text-sm mb-6">지하 20층의 그림자 파괴 군주를 격파하고 진정한 모험가로 명성을 떨치게 되었습니다!</p>
                        
                        <!-- Stats Grid -->
                        <div class="bg-gray-950 p-4 rounded-xl text-left mb-6 text-sm grid grid-cols-2 gap-3 border border-gray-800">
                            <div><span class="text-gray-400">영웅 클래스:</span> <strong id="vic-class" class="text-blue-400">전사</strong></div>
                            <div><span class="text-gray-400">최종 레벨:</span> <strong id="vic-level" class="text-amber-400">1</strong></div>
                            <div><span class="text-gray-400">처치한 적:</span> <strong id="vic-kills" class="text-red-400">0</strong></div>
                            <div><span class="text-gray-400">골드 잔액:</span> <strong id="vic-gold" class="text-yellow-400">0</strong></div>
                        </div>

                        <button onclick="resetGameToStart()" class="w-full bg-yellow-500 hover:bg-yellow-400 text-slate-950 py-3 rounded-xl font-bold transition">
                            다시 플레이하기
                        </button>
                    </div>
                </div>

                <!-- GAME CANVAS WRAPPER -->
                <div class="relative w-full max-w-[640px] aspect-[4/3] bg-gray-900 rounded-xl overflow-hidden border border-gray-800 shadow-inner">
                    <canvas id="game-canvas" class="w-full h-full block"></canvas>
                    
                    <!-- Floor Level Notification Banner -->
                    <div id="floor-toast" class="absolute top-4 left-1/2 transform -translate-x-1/2 bg-gray-950/90 border border-amber-500 text-amber-400 px-6 py-2 rounded-full font-bold text-sm tracking-wider opacity-0 transition-opacity duration-500 pointer-events-none z-10">
                        깊은 동굴 - 제 1층 진입
                    </div>
                </div>

                <!-- MOBILE CONTROLS OVERLAY -->
                <div class="w-full max-w-[640px] mt-4 flex justify-between items-center select-none lg:hidden">
                    <!-- Left: Virtual D-Pad (Touch movement helper) -->
                    <div class="flex flex-col items-center gap-1">
                        <button id="ctrl-up" class="w-12 h-12 bg-gray-800 active:bg-red-600 rounded-lg flex items-center justify-center border border-gray-700 active:border-red-500 transition-all text-xl"><i class="fa-solid fa-chevron-up"></i></button>
                        <div class="flex gap-4">
                            <button id="ctrl-left" class="w-12 h-12 bg-gray-800 active:bg-red-600 rounded-lg flex items-center justify-center border border-gray-700 active:border-red-500 transition-all text-xl"><i class="fa-solid fa-chevron-left"></i></button>
                            <button id="ctrl-down" class="w-12 h-12 bg-gray-800 active:bg-red-600 rounded-lg flex items-center justify-center border border-gray-700 active:border-red-500 transition-all text-xl"><i class="fa-solid fa-chevron-down"></i></button>
                            <button id="ctrl-right" class="w-12 h-12 bg-gray-800 active:bg-red-600 rounded-lg flex items-center justify-center border border-gray-700 active:border-red-500 transition-all text-xl"><i class="fa-solid fa-chevron-right"></i></button>
                        </div>
                    </div>
                    
                    <!-- Right: Quick Attack/Skill Buttons -->
                    <div class="flex flex-col gap-2">
                        <div class="flex gap-2">
                            <button id="touch-skill-1" class="w-12 h-12 bg-indigo-900 hover:bg-indigo-800 rounded-full flex flex-col items-center justify-center border border-indigo-500 text-xs text-white relative">
                                <span class="text-[10px]">Q</span>
                                <i class="fa-solid fa-wind"></i>
                            </button>
                            <button id="touch-skill-2" class="w-12 h-12 bg-purple-900 hover:bg-purple-800 rounded-full flex flex-col items-center justify-center border border-purple-500 text-xs text-white relative">
                                <span class="text-[10px]">W</span>
                                <i class="fa-solid fa-fire-burner"></i>
                            </button>
                        </div>
                        <button id="touch-attack" class="w-24 h-12 bg-red-600 active:bg-red-500 rounded-xl flex items-center justify-center gap-1 text-white font-bold text-sm shadow-md border border-red-400">
                            <i class="fa-solid fa-wand-magic-sparkles"></i>공격 (Space)
                        </button>
                    </div>
                </div>

            </div>

            <!-- RIGHT: DASHBOARD TAB / UPGRADE / INVENTORY -->
            <div class="w-full lg:w-96 bg-gray-900/40 p-6 flex flex-col justify-between gap-6">
                
                <!-- TOP PANEL: CHARACTER PROFILE STATUS -->
                <div>
                    <div class="flex justify-between items-center mb-4">
                        <div class="flex items-center gap-2">
                            <span id="char-class-badge" class="px-2.5 py-1 bg-red-950/60 border border-red-800 text-red-400 rounded-md text-xs font-semibold">전사</span>
                            <span class="text-sm font-bold text-gray-400">레벨 <span id="char-level" class="text-white orbitron">1</span></span>
                        </div>
                        <div class="text-xs text-gray-500">
                            경험치: <span id="char-xp-label" class="orbitron">0/100</span>
                        </div>
                    </div>

                    <!-- XP Progressive bar -->
                    <div class="w-full bg-gray-950 h-2 rounded-full overflow-hidden mb-4 border border-gray-800">
                        <div id="char-xp-bar" class="bg-green-500 h-full transition-all duration-300" style="width: 0%"></div>
                    </div>

                    <!-- HP and MP Status Bars -->
                    <div class="space-y-3 mb-6">
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-red-400 font-semibold flex items-center gap-1"><i class="fa-solid fa-heart"></i> 체력 (HP)</span>
                                <span id="char-hp-label" class="orbitron text-red-400">120 / 120</span>
                            </div>
                            <div class="w-full bg-gray-950 h-3 rounded-full overflow-hidden border border-gray-800">
                                <div id="char-hp-bar" class="bg-gradient-to-r from-red-600 to-rose-400 h-full transition-all duration-300" style="width: 100%"></div>
                            </div>
                        </div>
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-blue-400 font-semibold flex items-center gap-1"><i class="fa-solid fa-bolt"></i> 마나 (MP)</span>
                                <span id="char-mp-label" class="orbitron text-blue-400">40 / 40</span>
                            </div>
                            <div class="w-full bg-gray-950 h-3 rounded-full overflow-hidden border border-gray-800">
                                <div id="char-mp-bar" class="bg-gradient-to-r from-blue-600 to-indigo-400 h-full transition-all duration-300" style="width: 100%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- HERO STATS / UPGRADES -->
                    <div class="bg-gray-950/80 rounded-xl p-4 border border-gray-800 mb-6">
                        <div class="flex justify-between items-center mb-3">
                            <h3 class="text-sm font-bold text-gray-200 flex items-center gap-2"><i class="fa-solid fa-chart-simple text-amber-500"></i> 능력치 스탯</h3>
                            <div id="stat-points-container" class="hidden text-xs bg-amber-500/10 border border-amber-500/30 text-amber-400 px-2 py-0.5 rounded-full font-semibold">
                                스탯 포인트: <span id="stat-points">0</span>
                            </div>
                        </div>
                        <div class="space-y-3">
                            <div class="flex items-center justify-between text-xs">
                                <div class="flex items-center gap-2">
                                    <span class="w-16 text-gray-400">힘 (STR)</span>
                                    <strong id="stat-str" class="text-white orbitron">10</strong>
                                </div>
                                <button onclick="upgradeStat('STR')" class="stat-up-btn hidden bg-amber-500 hover:bg-amber-400 text-slate-950 px-2 py-0.5 rounded font-extrabold transition">투자 +1</button>
                                <span class="text-[10px] text-gray-500">공격력 증가</span>
                            </div>
                            <div class="flex items-center justify-between text-xs">
                                <div class="flex items-center gap-2">
                                    <span class="w-16 text-gray-400">민첩 (DEX)</span>
                                    <strong id="stat-dex" class="text-white orbitron">10</strong>
                                </div>
                                <button onclick="upgradeStat('DEX')" class="stat-up-btn hidden bg-amber-500 hover:bg-amber-400 text-slate-950 px-2 py-0.5 rounded font-extrabold transition">투자 +1</button>
                                <span class="text-[10px] text-gray-500">공속/치명타</span>
                            </div>
                            <div class="flex items-center justify-between text-xs">
                                <div class="flex items-center gap-2">
                                    <span class="w-16 text-gray-400">지능 (INT)</span>
                                    <strong id="stat-int" class="text-white orbitron">10</strong>
                                </div>
                                <button onclick="upgradeStat('INT')" class="stat-up-btn hidden bg-amber-500 hover:bg-amber-400 text-slate-950 px-2 py-0.5 rounded font-extrabold transition">투자 +1</button>
                                <span class="text-[10px] text-gray-500">마법 공격/MP</span>
                            </div>
                        </div>
                    </div>

                    <!-- INVENTORY & GOLD -->
                    <div class="bg-gray-950/80 rounded-xl p-4 border border-gray-800">
                        <div class="flex justify-between items-center mb-3">
                            <h3 class="text-sm font-bold text-gray-200 flex items-center gap-2">
                                <i class="fa-solid fa-briefcase text-amber-500"></i> 인벤토리 & 장비
                            </h3>
                            <span class="text-xs text-yellow-400 font-bold flex items-center gap-1">
                                <i class="fa-solid fa-coins"></i> <span id="char-gold" class="orbitron">100</span> 골드
                            </span>
                        </div>
                        
                        <!-- Gear slots -->
                        <div class="grid grid-cols-4 gap-2 mb-4">
                            <!-- Slot 1: Weapon -->
                            <div id="slot-weapon" onclick="unequipItem('weapon')" class="aspect-square bg-gray-900 border border-gray-800 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:border-amber-500 transition group">
                                <i class="fa-solid fa-sword text-gray-600 text-xl group-hover:scale-110 transition"></i>
                                <span class="text-[8px] text-gray-500 absolute bottom-1">무기</span>
                            </div>
                            <!-- Slot 2: Armor -->
                            <div id="slot-armor" onclick="unequipItem('armor')" class="aspect-square bg-gray-900 border border-gray-800 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:border-amber-500 transition group">
                                <i class="fa-solid fa-shirt text-gray-600 text-xl group-hover:scale-110 transition"></i>
                                <span class="text-[8px] text-gray-500 absolute bottom-1">갑옷</span>
                            </div>
                            <!-- Slot 3: Accessory -->
                            <div id="slot-accessory" onclick="unequipItem('accessory')" class="aspect-square bg-gray-900 border border-gray-800 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:border-amber-500 transition group">
                                <i class="fa-solid fa-ring text-gray-600 text-xl group-hover:scale-110 transition"></i>
                                <span class="text-[8px] text-gray-500 absolute bottom-1">반지</span>
                            </div>
                            <!-- Slot 4: Quick Potion -->
                            <div onclick="useQuickPotion()" class="aspect-square bg-gray-900 border border-red-900/40 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:bg-red-950/20 transition group">
                                <i class="fa-solid fa-flask-potion text-red-500 text-xl group-hover:scale-110 transition"></i>
                                <span class="text-[8px] text-red-400 absolute bottom-1 font-bold">포션 (<span id="potion-count">3</span>)</span>
                            </div>
                        </div>

                        <!-- Item Bag Quicklist (Inventory items) -->
                        <div class="bg-gray-900 p-2.5 rounded-lg border border-gray-800 min-h-[100px] max-h-[140px] overflow-y-auto">
                            <p class="text-[10px] text-gray-500 mb-2 border-b border-gray-800 pb-1">가방 (클릭하여 착용/사용)</p>
                            <div id="bag-list" class="grid grid-cols-1 gap-1.5 text-xs text-gray-300">
                                <!-- Dynamic List items go here -->
                                <p class="text-center text-gray-500 text-[11px] py-4">아이템이 비어있습니다.</p>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- BOTTOM PANEL: GAME LOG -->
                <div class="mt-auto border-t border-gray-800 pt-4">
                    <div class="flex justify-between items-center mb-2">
                        <span class="text-xs font-semibold text-gray-400 uppercase tracking-wide">모험 기록 및 메세지</span>
                        <button onclick="clearLogs()" class="text-[10px] text-gray-500 hover:text-gray-300">로그 지우기</button>
                    </div>
                    <div id="game-logs" class="bg-gray-950 border border-gray-800 rounded-lg p-3 h-28 overflow-y-auto text-[11px] font-mono space-y-1.5">
                        <p class="text-green-400">[안내] 클래스를 고른 후 "모험 시작하기"를 눌러주세요.</p>
                        <p class="text-gray-500">[안내] 이동: WASD, 공격: 스페이스 바, 스킬: Q / W 키</p>
                    </div>
                </div>

            </div>
        </div>

        <!-- FOOTER INFO -->
        <footer class="bg-gray-950 px-6 py-3 border-t border-gray-800 text-center text-xs text-gray-500 flex justify-between items-center">
            <span>&copy; 2026 그림자 군주 RPG Project. All rights reserved.</span>
            <span class="text-[11px] text-amber-500/70"><i class="fa-solid fa-gamepad mr-1"></i> 데스크탑 키보드 플레이 최적화 (이동: W,A,S,D / 공격: Space / 스킬: Q,W)</span>
        </footer>
    </div>

    <!-- SOUND GENERATION SCRIPT -->
    <script>
        // Web Audio Synthesizer for Retro Game Effects (Zero External Dependency)
        const AudioHelper = {
            ctx: null,
            enabled: true,

            init() {
                try {
                    window.AudioContext = window.AudioContext || window.webkitAudioContext;
                    this.ctx = new AudioContext();
                } catch(e) {
                    console.log("Web Audio not supported", e);
                }
            },

            toggle() {
                this.enabled = !this.enabled;
                const statusLabel = document.getElementById('sound-status');
                const soundIcon = document.getElementById('sound-icon');
                if (this.enabled) {
                    statusLabel.innerText = '켜짐';
                    statusLabel.className = 'text-green-400';
                    soundIcon.className = 'fa-solid fa-volume-high text-green-400';
                } else {
                    statusLabel.innerText = '꺼짐';
                    statusLabel.className = 'text-red-400';
                    soundIcon.className = 'fa-solid fa-volume-xmark text-red-400';
                }
            },

            playSlash() {
                if (!this.enabled || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(400, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(80, this.ctx.currentTime + 0.15);

                gain.gain.setValueAtTime(0.15, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.15);

                osc.start();
                osc.stop(this.ctx.currentTime + 0.15);
            },

            playMagic() {
                if (!this.enabled || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.type = 'sine';
                osc.frequency.setValueAtTime(300, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(900, this.ctx.currentTime + 0.25);

                gain.gain.setValueAtTime(0.12, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.25);

                osc.start();
                osc.stop(this.ctx.currentTime + 0.25);
            },

            playHit() {
                if (!this.enabled || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.type = 'triangle';
                osc.frequency.setValueAtTime(180, this.ctx.currentTime);
                osc.frequency.linearRampToValueAtTime(40, this.ctx.currentTime + 0.12);

                gain.gain.setValueAtTime(0.25, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.12);

                osc.start();
                osc.stop(this.ctx.currentTime + 0.12);
            },

            playCoin() {
                if (!this.enabled || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.type = 'sine';
                osc.frequency.setValueAtTime(987.77, this.ctx.currentTime); // B5
                osc.frequency.setValueAtTime(1318.51, this.ctx.currentTime + 0.08); // E6

                gain.gain.setValueAtTime(0.1, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.25);

                osc.start();
                osc.stop(this.ctx.currentTime + 0.25);
            },

            playHeal() {
                if (!this.enabled || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.type = 'sine';
                osc.frequency.setValueAtTime(440, this.ctx.currentTime);
                osc.frequency.setValueAtTime(554.37, this.ctx.currentTime + 0.07);
                osc.frequency.setValueAtTime(659.25, this.ctx.currentTime + 0.14);
                osc.frequency.setValueAtTime(880, this.ctx.currentTime + 0.21);

                gain.gain.setValueAtTime(0.15, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.35);

                osc.start();
                osc.stop(this.ctx.currentTime + 0.35);
            },

            playLevelUp() {
                if (!this.enabled || !this.ctx) return;
                // High celebratory triad arpeggio
                const now = this.ctx.currentTime;
                const freqs = [523.25, 659.25, 783.99, 1046.50]; // C5 E5 G5 C6
                freqs.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);

                    osc.type = 'square';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.1);
                    gain.gain.setValueAtTime(0.08, now + idx * 0.1);
                    gain.gain.linearRampToValueAtTime(0.01, now + idx * 0.1 + 0.3);

                    osc.start(now + idx * 0.1);
                    osc.stop(now + idx * 0.1 + 0.3);
                });
            },

            playGameOver() {
                if (!this.enabled || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(300, this.ctx.currentTime);
                osc.frequency.linearRampToValueAtTime(80, this.ctx.currentTime + 0.8);

                gain.gain.setValueAtTime(0.2, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.8);

                osc.start();
                osc.stop(this.ctx.currentTime + 0.8);
            }
        };

        // Attach mute toggle
        document.getElementById('sound-toggle').addEventListener('click', () => {
            if (!AudioHelper.ctx) AudioHelper.init();
            AudioHelper.toggle();
        });
    </script>

    <!-- CORE RPG ENGINE SCRIPT -->
    <script>
        // Setup Screen elements
        const startScreen = document.getElementById('start-screen');
        const gameoverScreen = document.getElementById('gameover-screen');
        const victoryScreen = document.getElementById('victory-screen');
        const canvas = document.getElementById('game-canvas');
        const ctx = canvas.getContext('2d');

        // Setup logical coordinate sizing for 4:3 canvas
        canvas.width = 640;
        canvas.height = 480;

        // Class Archetype specifications
        const CLASS_TEMPLATES = {
            WARRIOR: {
                name: '전사',
                icon: 'fa-shield-halved',
                color: '#ef4444',
                maxHp: 120,
                maxMp: 40,
                str: 14,
                dex: 9,
                int: 6,
                skills: [
                    { name: '용사의 검기', key: 'Q', cd: 2000, cost: 10, type: 'aoe_slash', lastUsed: 0, icon: 'fa-wind', desc: '주변 넓은 범위에 강력한 회전 물리 격파' },
                    { name: '대지 분쇄', key: 'W', cd: 5000, cost: 15, type: 'shockwave', lastUsed: 0, icon: 'fa-volcano', desc: '직선의 적에게 피해를 입히고 기절 파동 유발' }
                ]
            },
            MAGE: {
                name: '마법사',
                icon: 'fa-wand-sparkles',
                color: '#3b82f6',
                maxHp: 80,
                maxMp: 100,
                str: 5,
                dex: 7,
                int: 16,
                skills: [
                    { name: '화염의 불꽃', key: 'Q', cd: 1500, cost: 15, type: 'fireball_ring', lastUsed: 0, icon: 'fa-fire', desc: '팔방으로 날라가는 고위력 불꽃 투사체 발사' },
                    { name: '서리 폭발', key: 'W', cd: 4000, cost: 20, type: 'frost_nova', lastUsed: 0, icon: 'fa-snowflake', desc: '주변의 적을 일시적으로 대거 얼리고 강력한 지속 비전 데미지' }
                ]
            },
            ROGUE: {
                name: '도적',
                icon: 'fa-bolt',
                color: '#a855f7',
                maxHp: 95,
                maxMp: 55,
                str: 9,
                dex: 15,
                int: 8,
                skills: [
                    { name: '그림자 습격', key: 'Q', cd: 1800, cost: 12, type: 'dash_slash', lastUsed: 0, icon: 'fa-bolt-lightning', desc: '가장 가까운 적의 배후로 순간이동하며 속도 타격' },
                    { name: '맹독 투척', key: 'W', cd: 3500, cost: 18, type: 'poison_cloud', lastUsed: 0, icon: 'fa-skull', desc: '부채꼴로 3개의 독 수리검을 뿌려 지속성 중독 발생' }
                ]
            }
        };

        // Dynamic Leveling Exp thresholds mapping (up to Level 40)
        const EXP_NEEDED_PER_LEVEL = Array.from({length: 41}, (_, i) => i === 0 ? 0 : Math.floor(60 * Math.pow(i, 1.6)));

        // Main Game State Variables
        let selectedArchetype = 'WARRIOR';
        let player = {};
        let currentFloor = 1;
        let totalFloorCount = 20; // Expanded to massive 20 stages!
        let enemies = [];
        let items = [];
        let projectiles = [];
        let particles = [];
        let keyState = {};
        let gameRunning = false;
        let monsterKills = 0;
        let portalActive = false;
        let portalX = 0;
        let portalY = 0;
        let obstacles = [];

        // Meta structures for 20 unique stages with specific themes, environments & colors
        function getFloorMeta(floor) {
            if (floor === 20) return { name: "그림자 군주의 알현실 (최종 보스)", bg: "#09050d", grid: "#261533", color: "#a855f7" };
            if (floor === 15) return { name: "지옥 화염의 심장 (수호자 보스)", bg: "#1f0505", grid: "#401010", color: "#f43f5e" };
            if (floor === 10) return { name: "망령 군주의 방 (망령 보스)", bg: "#051410", grid: "#103328", color: "#10b981" };
            if (floor === 5) return { name: "수문장의 제단 (가고일 보스)", bg: "#141005", grid: "#332610", color: "#eab308" };
            
            if (floor >= 16) return { name: `심연의 틈새 - ${floor}층`, bg: "#0c0d17", grid: "#20253f", color: "#6366f1" };
            if (floor >= 11) return { name: `뜨거운 화염 동굴 - ${floor}층`, bg: "#1a0b0b", grid: "#3a1717", color: "#ef4444" };
            if (floor >= 6) return { name: `비명 지르는 감옥 - ${floor}층`, bg: "#0a1111", grid: "#172b2b", color: "#06b6d4" };
            return { name: `그림자 초소 - ${floor}층`, bg: "#0b0f19", grid: "#1e293b", color: "#38bdf8" };
        }

        // Choose player class on start screen
        function selectClass(className) {
            selectedArchetype = className;
            const cards = document.querySelectorAll('.class-card');
            cards.forEach(card => card.classList.remove('border-red-600', 'border-blue-600', 'border-purple-600', 'border-2'));
            
            // Add style to selected card
            const selectedCardBtn = event.currentTarget;
            let themeColor = 'border-red-600';
            if (className === 'MAGE') themeColor = 'border-blue-600';
            if (className === 'ROGUE') themeColor = 'border-purple-600';
            selectedCardBtn.classList.add(themeColor, 'border-2');

            // Set info text
            const infoBox = document.getElementById('class-desc-box');
            const data = CLASS_TEMPLATES[className];
            let titleColor = className === 'WARRIOR' ? 'text-red-400' : (className === 'MAGE' ? 'text-blue-400' : 'text-purple-400');
            
            infoBox.innerHTML = `
                <p class="font-semibold text-sm ${titleColor} mb-1"><i class="fa-solid ${data.icon} mr-1"></i> ${data.name}</p>
                <p class="mb-2">그림자 던전을 개척할 전설적인 지휘자입니다. 특화된 주능력치에 투자하며 점층적으로 생존력을 확대하십시오.</p>
                <div class="grid grid-cols-2 gap-2 text-[11px] text-gray-400">
                    <div><i class="fa-solid fa-heart text-red-500 mr-1"></i> 시작 HP: ${data.maxHp}</div>
                    <div><i class="fa-solid fa-bolt text-blue-400 mr-1"></i> 시작 MP: ${data.maxMp}</div>
                    <div><i class="fa-solid fa-hand-fist text-amber-500 mr-1"></i> 힘: ${data.str} | 민첩: ${data.dex}</div>
                    <div><i class="fa-solid fa-brain text-purple-400 mr-1"></i> 지능: ${data.int}</div>
                </div>
            `;

            // Adjust CTA button color
            const startBtn = document.getElementById('btn-start');
            startBtn.className = `w-full text-white py-3 rounded-xl font-extrabold text-base tracking-wider transition shadow-lg ${
                className === 'WARRIOR' ? 'bg-gradient-to-r from-red-600 to-amber-500 hover:from-red-500 hover:to-amber-400 shadow-red-950/40' :
                (className === 'MAGE' ? 'bg-gradient-to-r from-blue-600 to-cyan-500 hover:from-blue-500 hover:to-cyan-400 shadow-blue-950/40' :
                'bg-gradient-to-r from-purple-600 to-pink-500 hover:from-purple-500 hover:to-pink-400 shadow-purple-950/40')
            }`;
            
            if (!AudioHelper.ctx) AudioHelper.init();
            AudioHelper.playSlash();
        }

        // Start Game Init
        function startGame() {
            if (!AudioHelper.ctx) AudioHelper.init();
            
            // Build fresh player object
            const base = CLASS_TEMPLATES[selectedArchetype];
            player = {
                x: 80,
                y: 240,
                radius: 14,
                speed: 3 + (base.dex * 0.05),
                archetype: selectedArchetype,
                name: base.name,
                level: 1,
                exp: 0,
                gold: 150,
                hp: base.maxHp,
                maxHp: base.maxHp,
                mp: base.maxMp,
                maxMp: base.maxMp,
                str: base.str,
                dex: base.dex,
                int: base.int,
                statPoints: 0,
                skills: JSON.parse(JSON.stringify(base.skills)), // Deep clone skills
                lastAttackTime: 0,
                attackCooldown: 600 - (base.dex * 12), // Higher DEX reduces CD
                direction: { x: 1, y: 0 },
                inventory: [
                    { id: 'pot_1', name: '치유 물약', type: 'potion', hpHeal: 60, icon: 'fa-flask-potion', desc: '체력을 60 회복합니다.', count: 3 }
                ],
                equipment: {
                    weapon: null,
                    armor: null,
                    accessory: null
                }
            };

            currentFloor = 1;
            monsterKills = 0;
            portalActive = false;
            
            startScreen.classList.add('hidden');
            gameoverScreen.classList.add('hidden');
            victoryScreen.classList.add('hidden');
            
            // Build clean map and loop
            generateFloorMap();
            syncDashboardUI();
            
            gameRunning = true;
            addLog(`[${player.name}]으로 그림자 모험을 개시했습니다! 최종 20층의 보스를 격파하세요.`, 'text-yellow-400 font-bold');
            addLog('조작법: W,A,S,D 이동 | Space 기본 공격 | Q, W 스킬 발동', 'text-gray-400');
            AudioHelper.playHeal();
            
            requestAnimationFrame(gameLoop);
        }

        // Reset game to selection
        function resetGameToStart() {
            startScreen.classList.remove('hidden');
            gameoverScreen.classList.add('hidden');
            victoryScreen.classList.add('hidden');
            gameRunning = false;
        }

        // Build procedural floor mapping
        function generateFloorMap() {
            enemies = [];
            projectiles = [];
            particles = [];
            items = [];
            portalActive = false;
            
            // Reset player coordinates
            player.x = 80;
            player.y = 240;

            // Generate map obstacles (Pillars)
            obstacles = [];
            const rockCount = 3 + Math.floor(Math.random() * 3);
            for (let i = 0; i < rockCount; i++) {
                obstacles.push({
                    x: 180 + Math.random() * 260,
                    y: 80 + Math.random() * 320,
                    width: 40,
                    height: 40
                });
            }

            // Spawn monsters depending on floor (Specific bosses spawn on floors 5, 10, 15, 20)
            const meta = getFloorMeta(currentFloor);

            if (currentFloor === 5) {
                // BOSS 1 - Gargoyle Sentinel
                enemies.push({
                    name: '수문장 가고일 (보스)',
                    x: 500,
                    y: 240,
                    radius: 22,
                    speed: 1.4,
                    hp: 350,
                    maxHp: 350,
                    damage: 10,
                    isBoss: true,
                    shootCooldown: 1500,
                    lastShoot: 0,
                    color: '#eab308'
                });
                addLog('가고일 제단에 도달했습니다. [수문장 가고일]이 사방으로 날카로운 불꽃을 날려 공격합니다!', 'text-red-400 font-extrabold');
            } else if (currentFloor === 10) {
                // BOSS 2 - Wraith Lord
                enemies.push({
                    name: '지옥 망령군주 (보스)',
                    x: 500,
                    y: 240,
                    radius: 24,
                    speed: 1.5,
                    hp: 700,
                    maxHp: 700,
                    damage: 16,
                    isBoss: true,
                    shootCooldown: 1400,
                    lastShoot: 0,
                    color: '#10b981'
                });
                addLog('유령 감옥 깊은 방에서 [지옥 망령군주]가 서늘한 심연 Orbs를 무수히 방출합니다!', 'text-emerald-400 font-extrabold');
            } else if (currentFloor === 15) {
                // BOSS 3 - Magma Golem
                enemies.push({
                    name: '지옥용암 고렘 (보스)',
                    x: 500,
                    y: 240,
                    radius: 26,
                    speed: 1.2,
                    hp: 1200,
                    maxHp: 1200,
                    damage: 22,
                    isBoss: true,
                    shootCooldown: 1600,
                    lastShoot: 0,
                    color: '#f43f5e'
                });
                addLog('분노하는 화염 구덩이 속에서 [지옥용암 고렘]의 지진 폭발이 감지되었습니다!', 'text-rose-400 font-extrabold');
            } else if (currentFloor === 20) {
                // ULTIMATE FINAL BOSS - Shadow Lord
                enemies.push({
                    name: '그림자 파괴 군주 (최종 보스)',
                    x: 500,
                    y: 240,
                    radius: 30,
                    speed: 1.7,
                    hp: 2500,
                    maxHp: 2500,
                    damage: 32,
                    isBoss: true,
                    shootCooldown: 1200,
                    lastShoot: 0,
                    color: '#a855f7'
                });
                addLog('★ 경고!! 최종 20층의 지배자 [그림자 파괴 군주]가 광기 서린 파동을 내뿜습니다!', 'text-purple-500 font-black animate-pulse text-sm');
            } else {
                // Regular mobs scale cleanly with floors (max 12 to avoid canvas crowding)
                let enemyCount = Math.min(12, 3 + Math.floor(currentFloor * 0.4));
                for (let i = 0; i < enemyCount; i++) {
                    const r = Math.random();
                    let mName = '고블린';
                    let mHp = 25 + currentFloor * 12;
                    let mSpeed = 1.2 + Math.random() * 0.4;
                    let mDmg = 4 + currentFloor * 1.5;
                    let mColor = '#10b981';
                    let isRanged = false;

                    if (r > 0.75) {
                        mName = '어둠의 은둔거미';
                        mHp = 18 + currentFloor * 10;
                        mSpeed = 1.7 + Math.random() * 0.5;
                        mDmg = 6 + currentFloor * 1.8;
                        mColor = '#fb923c';
                    } else if (r > 0.45) {
                        mName = '어둠 주술 마법사';
                        mHp = 20 + currentFloor * 9;
                        mSpeed = 0.9;
                        mDmg = 3 + currentFloor * 1.4;
                        mColor = '#c084fc';
                        isRanged = true;
                    }

                    enemies.push({
                        name: mName,
                        x: 250 + Math.random() * 330,
                        y: 50 + Math.random() * 380,
                        radius: 12 + Math.random() * 3,
                        speed: mSpeed,
                        hp: mHp,
                        maxHp: mHp,
                        damage: mDmg,
                        color: mColor,
                        isRanged: isRanged,
                        shootCooldown: 2200 + Math.random() * 1000,
                        lastShoot: 0
                    });
                }
            }

            // Portal location setup
            portalX = 580;
            portalY = 240;

            // Show Toast Banner
            const toast = document.getElementById('floor-toast');
            toast.innerText = meta.name;
            toast.style.borderColor = meta.color;
            toast.style.color = meta.color;
            toast.className = `absolute top-4 left-1/2 transform -translate-x-1/2 bg-gray-950/90 border-2 px-6 py-2 rounded-full font-bold text-sm tracking-wider opacity-100 transition-opacity duration-500 pointer-events-none z-10`;
            setTimeout(() => {
                toast.classList.add('opacity-0');
            }, 2500);

            // Assign skill tactile visual keys
            document.getElementById('touch-skill-1').innerHTML = `<span class="text-[10px]">Q</span><i class="fa-solid ${player.skills[0].icon}"></i>`;
            document.getElementById('touch-skill-2').innerHTML = `<span class="text-[10px]">W</span><i class="fa-solid ${player.skills[1].icon}"></i>`;
        }

        // Message log helper
        function addLog(message, cssClass = 'text-gray-300') {
            const container = document.getElementById('game-logs');
            const now = new Date();
            const timeStr = `[${now.toTimeString().split(' ')[0]}]`;
            
            const p = document.createElement('p');
            p.className = cssClass;
            p.innerHTML = `<span class="text-gray-500 mr-1">${timeStr}</span> ${message}`;
            
            container.appendChild(p);
            container.scrollTop = container.scrollHeight;
        }

        function clearLogs() {
            document.getElementById('game-logs').innerHTML = '';
        }

        // Sync Dashboard stats to state
        function syncDashboardUI() {
            document.getElementById('char-class-badge').innerText = player.name;
            document.getElementById('char-class-badge').className = `px-2.5 py-1 rounded-md text-xs font-semibold ${
                player.archetype === 'WARRIOR' ? 'bg-red-950/60 border border-red-800 text-red-400' :
                (player.archetype === 'MAGE' ? 'bg-blue-950/60 border border-blue-800 text-blue-400' :
                'bg-purple-950/60 border border-purple-800 text-purple-400')
            }`;
            document.getElementById('char-level').innerText = player.level;
            
            // XP thresholds
            const neededXp = EXP_NEEDED_PER_LEVEL[player.level] || 999999;
            document.getElementById('char-xp-label').innerText = `${player.exp} / ${neededXp}`;
            const xpPct = Math.min(100, (player.exp / neededXp) * 100);
            document.getElementById('char-xp-bar').style.width = `${xpPct}%`;

            // HP/MP label & bars
            document.getElementById('char-hp-label').innerText = `${Math.ceil(player.hp)} / ${player.maxHp}`;
            const hpPct = Math.max(0, Math.min(100, (player.hp / player.maxHp) * 100));
            document.getElementById('char-hp-bar').style.width = `${hpPct}%`;

            document.getElementById('char-mp-label').innerText = `${Math.ceil(player.mp)} / ${player.maxMp}`;
            const mpPct = Math.max(0, Math.min(100, (player.mp / player.maxMp) * 100));
            document.getElementById('char-mp-bar').style.width = `${mpPct}%`;

            // Stats
            document.getElementById('stat-str').innerText = player.str;
            document.getElementById('stat-dex').innerText = player.dex;
            document.getElementById('stat-int').innerText = player.int;
            
            // Skill cooldown indicators
            const btnQ = document.getElementById('touch-skill-1');
            const btnW = document.getElementById('touch-skill-2');
            const now = Date.now();
            
            if (now - player.skills[0].lastUsed >= player.skills[0].cd) {
                btnQ.className = "w-12 h-12 bg-indigo-900 hover:bg-indigo-800 rounded-full flex flex-col items-center justify-center border-2 border-green-400 text-xs text-white relative shadow-lg";
            } else {
                btnQ.className = "w-12 h-12 bg-gray-800 rounded-full flex flex-col items-center justify-center border border-gray-700 text-xs text-gray-500 relative opacity-60";
            }

            if (now - player.skills[1].lastUsed >= player.skills[1].cd) {
                btnW.className = "w-12 h-12 bg-purple-900 hover:bg-purple-800 rounded-full flex flex-col items-center justify-center border-2 border-green-400 text-xs text-white relative shadow-lg";
            } else {
                btnW.className = "w-12 h-12 bg-gray-800 rounded-full flex flex-col items-center justify-center border border-gray-700 text-xs text-gray-500 relative opacity-60";
            }

            // Stat Point system
            const ptsContainer = document.getElementById('stat-points-container');
            const ptsVal = document.getElementById('stat-points');
            const upBtns = document.querySelectorAll('.stat-up-btn');
            
            if (player.statPoints > 0) {
                ptsContainer.classList.remove('hidden');
                ptsVal.innerText = player.statPoints;
                upBtns.forEach(btn => btn.classList.remove('hidden'));
            } else {
                ptsContainer.classList.add('hidden');
                upBtns.forEach(btn => btn.classList.add('hidden'));
            }

            document.getElementById('char-gold').innerText = player.gold;
            document.getElementById('current-floor-badge').innerText = `FLOOR ${currentFloor}`;

            renderInventoryList();
        }

        // Render inventory list html
        function renderInventoryList() {
            const weaponSlot = document.getElementById('slot-weapon');
            const armorSlot = document.getElementById('slot-armor');
            const accessorySlot = document.getElementById('slot-accessory');

            if (player.equipment.weapon) {
                weaponSlot.className = "aspect-square bg-amber-900/40 border-2 border-amber-500 rounded-lg flex flex-col items-center justify-center relative cursor-pointer group text-amber-300";
                weaponSlot.innerHTML = `<i class="fa-solid fa-sword text-xl"></i><span class="text-[8px] absolute bottom-1 truncate px-1">${player.equipment.weapon.name}</span>`;
            } else {
                weaponSlot.className = "aspect-square bg-gray-900 border border-gray-800 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:border-amber-500 text-gray-600";
                weaponSlot.innerHTML = `<i class="fa-solid fa-sword text-xl"></i><span class="text-[8px] text-gray-500 absolute bottom-1">무기</span>`;
            }

            if (player.equipment.armor) {
                armorSlot.className = "aspect-square bg-blue-900/40 border-2 border-blue-500 rounded-lg flex flex-col items-center justify-center relative cursor-pointer group text-blue-300";
                armorSlot.innerHTML = `<i class="fa-solid fa-shirt text-xl"></i><span class="text-[8px] absolute bottom-1 truncate px-1">${player.equipment.armor.name}</span>`;
            } else {
                armorSlot.className = "aspect-square bg-gray-900 border border-gray-800 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:border-amber-500 text-gray-600";
                armorSlot.innerHTML = `<i class="fa-solid fa-shirt text-xl"></i><span class="text-[8px] text-gray-500 absolute bottom-1">갑옷</span>`;
            }

            if (player.equipment.accessory) {
                accessorySlot.className = "aspect-square bg-purple-900/40 border-2 border-purple-500 rounded-lg flex flex-col items-center justify-center relative cursor-pointer group text-purple-300";
                accessorySlot.innerHTML = `<i class="fa-solid fa-ring text-xl"></i><span class="text-[8px] absolute bottom-1 truncate px-1">${player.equipment.accessory.name}</span>`;
            } else {
                accessorySlot.className = "aspect-square bg-gray-900 border border-gray-800 rounded-lg flex flex-col items-center justify-center relative cursor-pointer hover:border-amber-500 text-gray-600";
                accessorySlot.innerHTML = `<i class="fa-solid fa-ring text-xl"></i><span class="text-[8px] text-gray-500 absolute bottom-1">반지</span>`;
            }

            const potionItem = player.inventory.find(i => i.type === 'potion');
            const pCount = potionItem ? potionItem.count : 0;
            document.getElementById('potion-count').innerText = pCount;

            const bagContainer = document.getElementById('bag-list');
            bagContainer.innerHTML = '';
            
            const bagItems = player.inventory.filter(item => item.type !== 'potion' || item.count > 0);
            if (bagItems.length === 0) {
                bagContainer.innerHTML = `<p class="text-center text-gray-500 text-[11px] py-4">가방이 비어있습니다.</p>`;
                return;
            }

            bagItems.forEach((item) => {
                const row = document.createElement('div');
                let colorClass = 'text-gray-300';
                if (item.rarity === 'Legendary') colorClass = 'text-amber-400 font-bold animate-pulse';
                else if (item.rarity === 'Epic') colorClass = 'text-purple-400 font-bold';
                else if (item.rarity === 'Rare') colorClass = 'text-blue-400 font-semibold';

                row.className = `flex items-center justify-between p-1.5 hover:bg-gray-800 rounded cursor-pointer border border-transparent hover:border-gray-700 transition`;
                row.onclick = () => handleItemInteraction(item);
                
                let bonusStr = '';
                if (item.bonusStr) bonusStr += ` 힘+${item.bonusStr}`;
                if (item.bonusDex) bonusStr += ` 민+${item.bonusDex}`;
                if (item.bonusInt) bonusStr += ` 지+${item.bonusInt}`;
                if (item.bonusHp) bonusStr += ` 체+${item.bonusHp}`;

                row.innerHTML = `
                    <div class="flex items-center gap-1.5 min-w-0">
                        <i class="fa-solid ${item.icon || 'fa-gem'} ${colorClass} w-4 text-center"></i>
                        <span class="truncate ${colorClass}">${item.name} <span class="text-[10px] text-gray-500 font-normal">${bonusStr}</span></span>
                    </div>
                    <span class="text-[10px] text-gray-500 uppercase">${item.type === 'potion' ? '물약' : (item.type === 'weapon' ? '무기' : item.type === 'armor' ? '갑옷' : '반지')}</span>
                `;
                bagContainer.appendChild(row);
            });
        }

        // Item interaction handler
        function handleItemInteraction(item) {
            if (item.type === 'potion') {
                useQuickPotion();
            } else {
                const slot = item.type;
                const oldEquipped = player.equipment[slot];
                
                if (oldEquipped) {
                    unequipItem(slot, false);
                }

                player.equipment[slot] = item;
                if (item.bonusStr) player.str += item.bonusStr;
                if (item.bonusDex) player.dex += item.bonusDex;
                if (item.bonusInt) player.int += item.bonusInt;
                if (item.bonusHp) {
                    player.maxHp += item.bonusHp;
                    player.hp += item.bonusHp;
                }

                player.inventory = player.inventory.filter(i => i !== item);
                addLog(`[${item.name}] 장비 착용 완료! 성능이 크게 강화되었습니다.`, 'text-blue-400');
                AudioHelper.playHeal();
                syncDashboardUI();
            }
        }

        // Unequip item
        function unequipItem(slot, updateUI = true) {
            const item = player.equipment[slot];
            if (!item) return;

            if (item.bonusStr) player.str -= item.bonusStr;
            if (item.bonusDex) player.dex -= item.bonusDex;
            if (item.bonusInt) player.int -= item.bonusInt;
            if (item.bonusHp) {
                player.maxHp -= item.bonusHp;
                player.hp = Math.max(1, player.hp - item.bonusHp);
            }

            player.inventory.push(item);
            player.equipment[slot] = null;

            if (updateUI) {
                addLog(`[${item.name}] 장비를 해제하여 가방에 보관했습니다.`, 'text-gray-400');
                AudioHelper.playSlash();
                syncDashboardUI();
            }
        }

        // Upgrade character stat using leveled reward point
        function upgradeStat(statName) {
            if (player.statPoints <= 0) return;
            
            player.statPoints--;
            if (statName === 'STR') {
                player.str++;
                addLog('힘(STR) 투자 완료! 기본 물리공격 피해량이 강해집니다.', 'text-amber-400');
            } else if (statName === 'DEX') {
                player.dex++;
                player.speed = 3 + (player.dex * 0.05);
                player.attackCooldown = Math.max(250, 600 - (player.dex * 12));
                addLog('민첩(DEX) 투자 완료! 공격속도와 이동 속도가 소폭 상승합니다.', 'text-amber-400');
            } else if (statName === 'INT') {
                player.int++;
                player.maxMp += 6;
                player.mp += 6;
                addLog('지능(INT) 투자 완료! 고유 스킬 파괴력이 크게 증가합니다.', 'text-amber-400');
            }

            AudioHelper.playCoin();
            syncDashboardUI();
        }

        // Potion Quickbar Use
        function useQuickPotion() {
            const pot = player.inventory.find(i => i.type === 'potion');
            if (!pot || pot.count <= 0) {
                addLog('가방에 비축된 치유 물약이 없습니다!', 'text-red-400');
                return;
            }

            if (player.hp >= player.maxHp) {
                addLog('이미 체력이 최고 수준입니다.', 'text-gray-400');
                return;
            }

            pot.count--;
            const amount = 60 + player.level * 5; // Scaling healing by level
            player.hp = Math.min(player.maxHp, player.hp + amount);
            addLog(`치유 물약을 복용하여 체력을 ${amount}만큼 회복했습니다.`, 'text-green-400 font-bold');
            AudioHelper.playHeal();
            
            if (pot.count <= 0) {
                player.inventory = player.inventory.filter(i => i.id !== 'pot_1');
            }

            syncDashboardUI();
        }

        // KEYBOARD EVENT BINDINGS
        window.addEventListener('keydown', (e) => {
            keyState[e.code] = true;

            if (e.code === 'Space') {
                triggerPlayerAttack();
            }
            if (e.code === 'KeyQ') {
                useSkill(0);
            }
            if (e.code === 'KeyW') {
                useSkill(1);
            }
        });

        window.addEventListener('keyup', (e) => {
            keyState[e.code] = false;
        });

        // Tactile/Touch Controls Click Listeners
        document.getElementById('touch-attack').addEventListener('mousedown', (e) => {
            e.preventDefault();
            triggerPlayerAttack();
        });
        document.getElementById('touch-skill-1').addEventListener('mousedown', (e) => {
            e.preventDefault();
            useSkill(0);
        });
        document.getElementById('touch-skill-2').addEventListener('mousedown', (e) => {
            e.preventDefault();
            useSkill(1);
        });

        // Virtual D-pad touch configurations
        const bindDirectionBtn = (elemId, keyCode) => {
            const btn = document.getElementById(elemId);
            btn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                keyState[keyCode] = true;
            });
            btn.addEventListener('touchend', (e) => {
                e.preventDefault();
                keyState[keyCode] = false;
            });
            btn.addEventListener('mousedown', (e) => {
                e.preventDefault();
                keyState[keyCode] = true;
            });
            btn.addEventListener('mouseup', (e) => {
                e.preventDefault();
                keyState[keyCode] = false;
            });
        };
        bindDirectionBtn('ctrl-up', 'KeyW');
        bindDirectionBtn('ctrl-down', 'KeyS');
        bindDirectionBtn('ctrl-left', 'KeyA');
        bindDirectionBtn('ctrl-right', 'KeyD');

        // Default attack trigger
        function triggerPlayerAttack() {
            if (!gameRunning) return;
            const now = Date.now();
            if (now - player.lastAttackTime < player.attackCooldown) return;
            
            player.lastAttackTime = now;
            
            if (player.archetype === 'WARRIOR') {
                AudioHelper.playSlash();
                const range = 52;
                const particleCount = 6;
                const angleOffset = Math.atan2(player.direction.y, player.direction.x);
                for (let i = 0; i < particleCount; i++) {
                    const ang = angleOffset - 0.5 + (i / particleCount) * 1.0;
                    particles.push({
                        x: player.x + Math.cos(ang) * 20,
                        y: player.y + Math.sin(ang) * 20,
                        vx: Math.cos(ang) * 5,
                        vy: Math.sin(ang) * 5,
                        life: 15,
                        color: 'rgba(239, 68, 68, 0.7)',
                        size: 3
                    });
                }

                enemies.forEach(enemy => {
                    const dx = enemy.x - player.x;
                    const dy = enemy.y - player.y;
                    const dist = Math.sqrt(dx*dx + dy*dy);
                    if (dist < range + enemy.radius) {
                        const dot = dx * player.direction.x + dy * player.direction.y;
                        if (dot > 0 || dist < 25) {
                            damageEnemy(enemy, Math.floor(player.str * 1.25), false);
                        }
                    }
                });
            } else if (player.archetype === 'MAGE') {
                AudioHelper.playMagic();
                projectiles.push({
                    x: player.x,
                    y: player.y,
                    vx: player.direction.x * 6.5,
                    vy: player.direction.y * 6.5,
                    radius: 7,
                    damage: Math.floor(player.int * 1.55),
                    isEnemy: false,
                    color: '#60a5fa',
                    glow: 'rgba(96, 165, 250, 0.6)'
                });
            } else if (player.archetype === 'ROGUE') {
                AudioHelper.playSlash();
                const range = 38;
                particles.push({
                    x: player.x + player.direction.x * 20,
                    y: player.y + player.direction.y * 20,
                    vx: player.direction.x * 8.5,
                    vy: player.direction.y * 8.5,
                    life: 10,
                    color: '#c084fc',
                    size: 4
                });

                enemies.forEach(enemy => {
                    const dx = enemy.x - player.x;
                    const dy = enemy.y - player.y;
                    const dist = Math.sqrt(dx*dx + dy*dy);
                    if (dist < range + enemy.radius) {
                        const isCrit = Math.random() < 0.28;
                        const baseDmg = Math.floor(player.str * 0.9 + player.dex * 0.45);
                        const dmg = isCrit ? Math.floor(baseDmg * 2.2) : baseDmg;
                        damageEnemy(enemy, dmg, isCrit);
                    }
                });
            }
        }

        // Use Active Skills (Q: Skill 1, W: Skill 2)
        function useSkill(skillIdx) {
            if (!gameRunning) return;
            const skill = player.skills[skillIdx];
            const now = Date.now();
            
            if (now - skill.lastUsed < skill.cd) {
                addLog(`[${skill.name}] 스킬이 재충전 중입니다.`, 'text-gray-500');
                return;
            }
            if (player.mp < skill.cost) {
                addLog('마나(MP)가 부족합니다!', 'text-blue-400');
                return;
            }

            player.mp -= skill.cost;
            skill.lastUsed = now;
            addLog(`시전: [${skill.name}] (${skill.cost} MP 소모)`, 'text-indigo-400 font-semibold');
            
            if (skill.type === 'aoe_slash') {
                AudioHelper.playSlash();
                const radius = 95;
                for (let a = 0; a < Math.PI * 2; a += 0.3) {
                    particles.push({
                        x: player.x,
                        y: player.y,
                        vx: Math.cos(a) * 4,
                        vy: Math.sin(a) * 4,
                        life: 25,
                        color: '#ef4444',
                        size: 5
                    });
                }
                enemies.forEach(enemy => {
                    const dist = Math.hypot(enemy.x - player.x, enemy.y - player.y);
                    if (dist < radius + enemy.radius) {
                        damageEnemy(enemy, Math.floor(player.str * 2.3), false);
                    }
                });
            } 
            else if (skill.type === 'shockwave') {
                AudioHelper.playMagic();
                const vX = player.direction.x * 5.5;
                const vY = player.direction.y * 5.5;
                for (let i = 0; i < 3; i++) {
                    projectiles.push({
                        x: player.x + player.direction.x * (i * 15),
                        y: player.y + player.direction.y * (i * 15),
                        vx: vX,
                        vy: vY,
                        radius: 12 + i * 4,
                        damage: Math.floor(player.str * 1.6 + 18),
                        isEnemy: false,
                        color: '#f59e0b',
                        glow: 'rgba(245, 158, 11, 0.4)'
                    });
                }
            }
            else if (skill.type === 'fireball_ring') {
                AudioHelper.playMagic();
                const bulletCount = 8;
                for (let i = 0; i < bulletCount; i++) {
                    const angle = (i / bulletCount) * Math.PI * 2;
                    projectiles.push({
                        x: player.x,
                        y: player.y,
                        vx: Math.cos(angle) * 4.8,
                        vy: Math.sin(angle) * 4.8,
                        radius: 8,
                        damage: Math.floor(player.int * 1.3),
                        isEnemy: false,
                        color: '#f97316',
                        glow: 'rgba(249, 115, 22, 0.6)'
                    });
                }
            }
            else if (skill.type === 'frost_nova') {
                AudioHelper.playMagic();
                const radius = 130;
                for (let a = 0; a < Math.PI * 2; a += 0.15) {
                    particles.push({
                        x: player.x,
                        y: player.y,
                        vx: Math.cos(a) * 3,
                        vy: Math.sin(a) * 3,
                        life: 30,
                        color: '#06b6d4',
                        size: 4
                    });
                }
                enemies.forEach(enemy => {
                    const dist = Math.hypot(enemy.x - player.x, enemy.y - player.y);
                    if (dist < radius + enemy.radius) {
                        damageEnemy(enemy, Math.floor(player.int * 2.7), false);
                        enemy.speed *= 0.35;
                    }
                });
            }
            else if (skill.type === 'dash_slash') {
                let closest = null;
                let minDist = 300;
                enemies.forEach(enemy => {
                    const dist = Math.hypot(enemy.x - player.x, enemy.y - player.y);
                    if (dist < minDist) {
                        minDist = dist;
                        closest = enemy;
                    }
                });

                if (closest) {
                    for(let i=0; i<8; i++) {
                        particles.push({
                            x: player.x, y: player.y,
                            vx: (Math.random()-0.5)*4, vy: (Math.random()-0.5)*4,
                            life: 20, color: '#a855f7', size: 3
                        });
                    }
                    player.x = closest.x - (player.direction.x * 25);
                    player.y = closest.y - (player.direction.y * 25);
                    AudioHelper.playSlash();
                    damageEnemy(closest, Math.floor(player.dex * 3.0), true);
                } else {
                    addLog('시전 가능한 적이 사거리에 없습니다.', 'text-gray-500');
                    player.mp += skill.cost;
                    skill.lastUsed = 0;
                }
            }
            else if (skill.type === 'poison_cloud') {
                AudioHelper.playSlash();
                const baseAngle = Math.atan2(player.direction.y, player.direction.x);
                const angles = [baseAngle - 0.25, baseAngle, baseAngle + 0.25];
                angles.forEach(ang => {
                    projectiles.push({
                        x: player.x,
                        y: player.y,
                        vx: Math.cos(ang) * 5.8,
                        vy: Math.sin(ang) * 5.8,
                        radius: 5,
                        damage: Math.floor(player.dex * 1.6),
                        isEnemy: false,
                        color: '#22c55e',
                        glow: 'rgba(34, 197, 94, 0.4)'
                    });
                });
            }

            syncDashboardUI();
        }

        // Damage application & combat logs
        function damageEnemy(enemy, rawDamage, isCrit) {
            enemy.hp -= rawDamage;
            AudioHelper.playHit();

            particles.push({
                x: enemy.x,
                y: enemy.y - 12,
                vx: (Math.random() - 0.5) * 1.5,
                vy: -2,
                life: 35,
                text: `${rawDamage}${isCrit ? '!' : ''}`,
                color: isCrit ? '#f59e0b' : '#ef4444',
                size: isCrit ? 16 : 12,
                isText: true
            });

            if (enemy.hp <= 0) {
                enemies = enemies.filter(e => e !== enemy);
                monsterKills++;
                
                const xpGain = enemy.isBoss ? (100 + currentFloor * 40) : (15 + currentFloor * 6);
                const goldGain = enemy.isBoss ? (150 + currentFloor * 30) : Math.floor(10 + Math.random() * (10 + currentFloor * 5));
                
                player.exp += xpGain;
                player.gold += goldGain;
                
                addLog(`[${enemy.name}] 처치 완료! (경험치 +${xpGain}, 골드 +${goldGain})`, 'text-green-400');
                AudioHelper.playCoin();

                // Roll loot items drop (35% regular, 100% Boss)
                if (Math.random() < 0.35 || enemy.isBoss) {
                    spawnDroppedLoot(enemy.x, enemy.y, enemy.isBoss);
                }

                checkLevelUp();

                if (enemies.length === 0) {
                    portalActive = true;
                    addLog('동굴의 수호자들을 모두 소탕했습니다! 신비로운 주술 문양(포탈)이 열렸습니다.', 'text-amber-400 font-bold');
                }
            }
        }

        // Spawn actual loot drops with Floor Stats Scaling
        function spawnDroppedLoot(x, y, isBoss) {
            const r = Math.random();
            let itemType = 'potion';
            let itemName = '치유 물약';
            let itemRarity = 'Common';
            let icon = 'fa-flask-potion';
            let bonus = {};

            // Scaling multiplier based on Current Floor (1.0x to 3.5x at Floor 20)
            const scale = 1 + (currentFloor - 1) * 0.13;

            if (isBoss) {
                if (Math.random() < 0.4) {
                    itemName = `${getFloorPrefix(currentFloor)}그림자 칼날`;
                    itemType = 'weapon';
                    itemRarity = 'Legendary';
                    icon = 'fa-sword';
                    bonus = { bonusStr: Math.round(10 * scale), bonusDex: Math.round(5 * scale) };
                } else {
                    itemName = `${getFloorPrefix(currentFloor)}수호자의 갑옷`;
                    itemType = 'armor';
                    itemRarity = 'Epic';
                    icon = 'fa-shirt';
                    bonus = { bonusHp: Math.round(45 * scale), bonusStr: Math.round(4 * scale) };
                }
            } else {
                if (r > 0.8) {
                    itemName = `${getFloorPrefix(currentFloor)}영혼의 가락지`;
                    itemType = 'accessory';
                    itemRarity = 'Rare';
                    icon = 'fa-ring';
                    bonus = { bonusHp: Math.round(20 * scale), bonusInt: Math.round(3 * scale) };
                } else if (r > 0.55) {
                    itemName = `${getFloorPrefix(currentFloor)}기사의 장검`;
                    itemType = 'weapon';
                    itemRarity = 'Rare';
                    icon = 'fa-sword';
                    bonus = { bonusStr: Math.round(5 * scale), bonusDex: Math.round(2 * scale) };
                } else if (r > 0.3) {
                    itemName = `${getFloorPrefix(currentFloor)}가죽 자켓`;
                    itemType = 'armor';
                    itemRarity = 'Common';
                    icon = 'fa-shirt';
                    bonus = { bonusHp: Math.round(15 * scale), bonusDex: Math.round(2 * scale) };
                } else {
                    itemType = 'potion';
                    itemName = '치유 물약';
                    icon = 'fa-flask-potion';
                }
            }

            items.push({
                x: x,
                y: y,
                name: itemName,
                type: itemType,
                rarity: itemRarity,
                icon: icon,
                bonus: bonus,
                radius: 10
            });
        }

        // Dynamic prefix text for scalable gears
        function getFloorPrefix(floor) {
            if (floor >= 16) return '심연의 ';
            if (floor >= 11) return '지옥용암 ';
            if (floor >= 6) return '원혼서린 ';
            return '투박한 ';
        }

        // XP Check Level up
        function checkLevelUp() {
            const needed = EXP_NEEDED_PER_LEVEL[player.level];
            if (!needed) return;

            if (player.exp >= needed) {
                player.exp -= needed;
                player.level++;
                player.statPoints += 3;
                
                player.maxHp += 12;
                player.hp = player.maxHp;
                player.maxMp += 6;
                player.mp = player.maxMp;

                addLog(`★★ LEVEL UP! 레벨 ${player.level} 달성! 3개의 스탯 분배 포인트를 얻었습니다. ★★`, 'text-yellow-400 font-extrabold');
                AudioHelper.playLevelUp();

                checkLevelUp();
            }
        }

        // Damage the Player
        function damagePlayer(damageAmount) {
            let finalDamage = Math.max(1, Math.floor(damageAmount));
            if (player.archetype === 'WARRIOR') {
                finalDamage = Math.max(1, Math.floor(finalDamage * 0.85));
            }

            player.hp -= finalDamage;
            AudioHelper.playHit();

            particles.push({
                x: player.x,
                y: player.y - 12,
                vx: (Math.random() - 0.5) * 1.5,
                vy: -2,
                life: 35,
                text: `${finalDamage}`,
                color: '#ef4444',
                size: 13,
                isText: true
            });

            if (player.hp <= 0) {
                triggerGameOver();
            }

            syncDashboardUI();
        }

        // Death screen trigger
        function triggerGameOver() {
            gameRunning = false;
            AudioHelper.playGameOver();
            
            document.getElementById('go-level').innerText = player.level;
            document.getElementById('go-floor').innerText = currentFloor;
            document.getElementById('go-kills').innerText = monsterKills;
            document.getElementById('go-gold').innerText = player.gold;
            
            gameoverScreen.classList.remove('hidden');
        }

        // Ultimate victory
        function triggerVictory() {
            gameRunning = false;
            AudioHelper.playLevelUp();

            document.getElementById('vic-class').innerText = player.name;
            document.getElementById('vic-level').innerText = player.level;
            document.getElementById('vic-kills').innerText = monsterKills;
            document.getElementById('vic-gold').innerText = player.gold;

            victoryScreen.classList.remove('hidden');
        }

        // MAIN PHYSICS LOOP & CANVAS DRAWING
        function gameLoop() {
            if (!gameRunning) return;

            updatePhysics();
            drawFrame();

            requestAnimationFrame(gameLoop);
        }

        // Physics, movement and attack triggers
        function updatePhysics() {
            let dx = 0;
            let dy = 0;
            if (keyState['KeyW'] || keyState['ArrowUp']) dy -= 1;
            if (keyState['KeyS'] || keyState['ArrowDown']) dy += 1;
            if (keyState['KeyA'] || keyState['ArrowLeft']) dx -= 1;
            if (keyState['KeyD'] || keyState['ArrowRight']) dx += 1;

            if (dx !== 0 || dy !== 0) {
                const len = Math.sqrt(dx*dx + dy*dy);
                const normX = dx / len;
                const normY = dy / len;

                player.direction = { x: normX, y: normY };

                const nextX = player.x + normX * player.speed;
                const nextY = player.y + normY * player.speed;

                let collision = false;
                if (nextX < player.radius || nextX > 640 - player.radius) collision = true;
                if (nextY < player.radius || nextY > 480 - player.radius) collision = true;

                obstacles.forEach(obs => {
                    if (nextX + player.radius > obs.x && 
                        nextX - player.radius < obs.x + obs.width &&
                        nextY + player.radius > obs.y &&
                        nextY - player.radius < obs.y + obs.height) {
                        collision = true;
                    }
                });

                if (!collision) {
                    player.x = nextX;
                    player.y = nextY;
                }
            }

            if (Math.random() < 0.015) {
                player.mp = Math.min(player.maxMp, player.mp + 1 + Math.floor(player.int * 0.1));
                syncDashboardUI();
            }

            // Projectiles collision mechanics
            projectiles.forEach((proj, idx) => {
                proj.x += proj.vx;
                proj.y += proj.vy;

                if (proj.x < 0 || proj.x > 640 || proj.y < 0 || proj.y > 480) {
                    projectiles.splice(idx, 1);
                    return;
                }

                obstacles.forEach(obs => {
                    if (proj.x > obs.x && proj.x < obs.x + obs.width &&
                        proj.y > obs.y && proj.y < obs.y + obs.height) {
                        projectiles.splice(idx, 1);
                        return;
                    }
                });

                if (!proj.isEnemy) {
                    enemies.forEach(enemy => {
                        const dist = Math.hypot(enemy.x - proj.x, enemy.y - proj.y);
                        if (dist < enemy.radius + proj.radius) {
                            damageEnemy(enemy, proj.damage, false);
                            projectiles.splice(idx, 1);
                        }
                    });
                } else {
                    const dist = Math.hypot(player.x - proj.x, player.y - proj.y);
                    if (dist < player.radius + proj.radius) {
                        damagePlayer(proj.damage);
                        projectiles.splice(idx, 1);
                    }
                }
            });

            // Enemy AI Chase
            enemies.forEach(enemy => {
                const dx = player.x - enemy.x;
                const dy = player.y - enemy.y;
                const dist = Math.sqrt(dx*dx + dy*dy);

                if (dist < 250) { 
                    const vx = (dx / dist) * enemy.speed;
                    const vy = (dy / dist) * enemy.speed;

                    let nextX = enemy.x + vx;
                    let nextY = enemy.y + vy;
                    let collision = false;
                    obstacles.forEach(obs => {
                        if (nextX + enemy.radius > obs.x && nextX - enemy.radius < obs.x + obs.width &&
                            nextY + enemy.radius > obs.y && nextY - enemy.radius < obs.y + obs.height) {
                            collision = true;
                        }
                    });

                    if (!collision) {
                        enemy.x = nextX;
                        enemy.y = nextY;
                    }

                    if (enemy.isRanged) {
                        const now = Date.now();
                        if (now - enemy.lastShoot > enemy.shootCooldown && dist < 180) {
                            enemy.lastShoot = now;
                            projectiles.push({
                                x: enemy.x,
                                y: enemy.y,
                                vx: (dx / dist) * 4,
                                vy: (dy / dist) * 4,
                                radius: 5,
                                damage: enemy.damage,
                                isEnemy: true,
                                color: '#a855f7',
                                glow: 'rgba(168, 85, 247, 0.4)'
                            });
                        }
                    } else if (enemy.isBoss) {
                        const now = Date.now();
                        if (now - enemy.lastShoot > enemy.shootCooldown) {
                            enemy.lastShoot = now;
                            const bullets = 6 + Math.floor(currentFloor * 0.4);
                            for (let i = 0; i < bullets; i++) {
                                const ang = (i / bullets) * Math.PI * 2;
                                projectiles.push({
                                    x: enemy.x,
                                    y: enemy.y,
                                    vx: Math.cos(ang) * 3.8,
                                    vy: Math.sin(ang) * 3.8,
                                    radius: 7,
                                    damage: enemy.damage * 0.7,
                                    isEnemy: true,
                                    color: '#f43f5e',
                                    glow: 'rgba(244, 63, 94, 0.5)'
                                });
                            }
                        }
                    }

                    if (dist < enemy.radius + player.radius) {
                        if (Math.random() < 0.08) {
                            damagePlayer(enemy.damage);
                        }
                    }
                }
            });

            // Loot pick up range
            items.forEach((item, idx) => {
                const dist = Math.hypot(player.x - item.x, player.y - item.y);
                if (dist < player.radius + item.radius) {
                    if (item.type === 'potion') {
                        const existingPot = player.inventory.find(i => i.type === 'potion');
                        if (existingPot) {
                            existingPot.count++;
                        } else {
                            player.inventory.push({ id: 'pot_1', name: '치유 물약', type: 'potion', hpHeal: 60, icon: 'fa-flask-potion', desc: '체력을 회복합니다.', count: 1 });
                        }
                        addLog('[치유 물약]을 비축창에 추가했습니다.', 'text-green-400');
                    } else {
                        player.inventory.push(item);
                        addLog(`장비 입수! [${item.name}] (${item.rarity})`, 'text-blue-300 font-semibold');
                    }
                    
                    AudioHelper.playCoin();
                    items.splice(idx, 1);
                    syncDashboardUI();
                }
            });

            // Exit Portal activation
            if (portalActive) {
                const dist = Math.hypot(player.x - portalX, player.y - portalY);
                if (dist < player.radius + 20) {
                    if (currentFloor === totalFloorCount) {
                        triggerVictory();
                    } else {
                        currentFloor++;
                        addLog(`던전 포탈 통과! 제 ${currentFloor}층 깊이로 돌입합니다...`, 'text-yellow-400 font-bold');
                        AudioHelper.playHeal();
                        generateFloorMap();
                        syncDashboardUI();
                    }
                }
            }

            particles.forEach((p, idx) => {
                if (p.vx) p.x += p.vx;
                if (p.vy) p.y += p.vy;
                p.life--;
                if (p.life <= 0) particles.splice(idx, 1);
            });
        }

        // Main canvas renderer with Theme specific colors
        function drawFrame() {
            const meta = getFloorMeta(currentFloor);

            // Theme visual background
            ctx.fillStyle = meta.bg;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw tile grid pattern
            ctx.strokeStyle = meta.grid;
            ctx.lineWidth = 1;
            const tileSize = 40;
            for (let x = 0; x < canvas.width; x += tileSize) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, canvas.height);
                ctx.stroke();
            }
            for (let y = 0; y < canvas.height; y += tileSize) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(canvas.width, y);
                ctx.stroke();
            }

            // Draw Obstacles
            obstacles.forEach(obs => {
                ctx.fillStyle = '#1e293b'; 
                ctx.strokeStyle = meta.color;
                ctx.lineWidth = 1.5;
                ctx.beginPath();
                ctx.roundRect(obs.x, obs.y, obs.width, obs.height, 6);
                ctx.fill();
                ctx.stroke();

                ctx.fillStyle = meta.grid;
                ctx.fillRect(obs.x + 6, obs.y + 6, 8, 8);
                ctx.fillRect(obs.x + 22, obs.y + 24, 12, 8);
            });

            // Draw Exit portal
            if (portalActive) {
                const time = Date.now() * 0.005;
                ctx.shadowBlur = 15;
                ctx.shadowColor = meta.color;
                
                ctx.strokeStyle = meta.color;
                ctx.lineWidth = 4;
                ctx.beginPath();
                ctx.arc(portalX, portalY, 22, 0, Math.PI*2);
                ctx.stroke();

                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.arc(portalX, portalY, 14, time, time + Math.PI * 1.5);
                ctx.stroke();

                ctx.shadowBlur = 0;
            }

            // Draw Drops
            items.forEach(item => {
                let color = '#ef4444';
                if (item.type !== 'potion') {
                    if (item.rarity === 'Legendary') color = '#fbbf24';
                    else if (item.rarity === 'Epic') color = '#a855f7';
                    else if (item.rarity === 'Rare') color = '#3b82f6';
                    else color = '#9ca3af';
                }

                ctx.shadowBlur = 8;
                ctx.shadowColor = color;
                ctx.fillStyle = color;
                ctx.beginPath();
                ctx.arc(item.x, item.y, item.radius, 0, Math.PI*2);
                ctx.fill();

                ctx.fillStyle = '#ffffff';
                ctx.fillRect(item.x - 2, item.y - 2, 4, 4);
                ctx.shadowBlur = 0;

                ctx.fillStyle = '#cbd5e1';
                ctx.font = '8px Inter, sans-serif';
                ctx.textAlign = 'center';
                ctx.fillText(item.name, item.x, item.y - 12);
            });

            // Draw Projectiles
            projectiles.forEach(proj => {
                if (proj.glow) {
                    ctx.shadowBlur = 10;
                    ctx.shadowColor = proj.glow;
                }
                ctx.fillStyle = proj.color;
                ctx.beginPath();
                ctx.arc(proj.x, proj.y, proj.radius, 0, Math.PI*2);
                ctx.fill();
                ctx.shadowBlur = 0;
            });

            // Draw Hero Player
            let playerColor = CLASS_TEMPLATES[player.archetype].color;
            ctx.shadowBlur = 12;
            ctx.shadowColor = playerColor;
            ctx.fillStyle = playerColor;
            ctx.beginPath();
            ctx.arc(player.x, player.y, player.radius, 0, Math.PI*2);
            ctx.fill();
            ctx.shadowBlur = 0;

            // Direction facing sweep
            ctx.strokeStyle = '#f8fafc';
            ctx.lineWidth = 3;
            ctx.beginPath();
            ctx.arc(
                player.x, 
                player.y, 
                player.radius + 4, 
                Math.atan2(player.direction.y, player.direction.x) - 0.4, 
                Math.atan2(player.direction.y, player.direction.x) + 0.4
            );
            ctx.stroke();

            // Draw Enemies
            enemies.forEach(enemy => {
                ctx.shadowBlur = enemy.isBoss ? 20 : 6;
                ctx.shadowColor = enemy.color;
                ctx.fillStyle = enemy.color;
                ctx.beginPath();
                ctx.arc(enemy.x, enemy.y, enemy.radius, 0, Math.PI*2);
                ctx.fill();
                ctx.shadowBlur = 0;

                // Enemy HP layout
                const barW = enemy.radius * 2;
                const barH = 4;
                const barX = enemy.x - enemy.radius;
                const barY = enemy.y - enemy.radius - 8;

                ctx.fillStyle = '#1e293b';
                ctx.fillRect(barX, barY, barW, barH);

                const hpRatio = enemy.hp / enemy.maxHp;
                ctx.fillStyle = '#ef4444';
                ctx.fillRect(barX, barY, barW * hpRatio, barH);

                // Boss Crown Visualizer
                if (enemy.isBoss) {
                    ctx.fillStyle = '#fbbf24';
                    ctx.beginPath();
                    ctx.moveTo(enemy.x - 10, enemy.y - 34);
                    ctx.lineTo(enemy.x - 5, enemy.y - 40);
                    ctx.lineTo(enemy.x, enemy.y - 34);
                    ctx.lineTo(enemy.x + 5, enemy.y - 40);
                    ctx.lineTo(enemy.x + 10, enemy.y - 34);
                    ctx.lineTo(enemy.x + 10, enemy.y - 42);
                    ctx.lineTo(enemy.x - 10, enemy.y - 42);
                    ctx.closePath();
                    ctx.fill();
                }
            });

            // Particles
            particles.forEach(p => {
                if (p.isText) {
                    ctx.fillStyle = p.color;
                    ctx.font = `bold ${p.size}px Orbitron, sans-serif`;
                    ctx.textAlign = 'center';
                    ctx.fillText(p.text, p.x, p.y);
                } else {
                    ctx.fillStyle = p.color;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, p.size, 0, Math.PI*2);
                    ctx.fill();
                }
            });
        }
    </script>
</body>
</html>
