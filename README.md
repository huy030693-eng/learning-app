<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Math Mayor: Fisherman City Builder</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;500;600;700&display=swap" rel="stylesheet">
    
    <style>
        * {
            font-family: 'Fredoka', cursive, sans-serif;
            user-select: none;
        }

        @keyframes floatSlow {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-8px) rotate(1deg); }
        }

        @keyframes popIn {
            0% { transform: scale(0.7); opacity: 0; }
            70% { transform: scale(1.05); }
            100% { transform: scale(1); opacity: 1; }
        }

        @keyframes goldFly {
            0% { opacity: 1; transform: translateY(0) scale(1); }
            50% { opacity: 1; transform: translateY(-40px) scale(1.3); }
            100% { opacity: 0; transform: translateY(-90px) scale(0.8); }
        }

        .animate-float { animation: floatSlow 4s ease-in-out infinite; }
        .animate-pop { animation: popIn 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards; }
        .gold-pop-anim { animation: goldFly 0.85s ease-out forwards; }

        .game-panel {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(8px);
            border: 3px solid #0284c7;
            box-shadow: 0 12px 28px -6px rgba(0, 0, 0, 0.15);
        }

        .nav-btn.active {
            background-color: #0284c7;
            color: white;
            box-shadow: 0 4px 12px rgba(2, 132, 199, 0.4);
            transform: translateY(-2px);
        }

        /* Custom Scrollbars */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #e0f2fe; border-radius: 8px; }
        ::-webkit-scrollbar-thumb { background: #0284c7; border-radius: 8px; }
    </style>
</head>
<body class="bg-gradient-to-b from-slate-900 via-sky-950 to-blue-900 min-h-screen text-slate-800 flex flex-col justify-between overflow-x-hidden">

    <!-- TOP HEADER / MAYOR STATS BAR -->
    <header class="bg-sky-950/90 border-b-4 border-cyan-500 text-white sticky top-0 z-40 backdrop-blur-md shadow-2xl">
        <div class="max-w-7xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-3">
            
            <!-- Brand Logo -->
            <div class="flex items-center gap-3">
                <div class="bg-gradient-to-tr from-amber-500 to-yellow-300 p-2.5 rounded-2xl border-2 border-amber-300 text-slate-950 shadow-lg animate-float">
                    <i class="fa-solid fa-city text-2xl"></i>
                </div>
                <div>
                    <h1 class="text-xl sm:text-2xl font-black tracking-wide bg-gradient-to-r from-amber-300 via-yellow-200 to-cyan-300 bg-clip-text text-transparent drop-shadow">
                        MATH MAYOR
                    </h1>
                    <p class="text-xs text-cyan-200 font-medium">Fisherman City Builder (Grades 1-12)</p>
                </div>
            </div>

            <!-- Header Quick Grade Picker & Stats Bar -->
            <div class="flex items-center gap-2 sm:gap-3 flex-wrap">
                <!-- Grade Switcher Badge -->
                <button onclick="openGradePickerModal()" class="bg-gradient-to-r from-indigo-600 to-purple-600 hover:from-indigo-500 hover:to-purple-500 px-3.5 py-1.5 rounded-2xl border-2 border-purple-400 flex items-center gap-2 shadow transition active:scale-95">
                    <i class="fa-solid fa-graduation-cap text-yellow-300 text-base"></i>
                    <div class="text-left">
                        <span id="header-grade-badge" class="text-xs font-black text-white block leading-tight">Grade 3</span>
                        <span class="text-[9px] block text-purple-200 uppercase font-bold">Change Grade</span>
                    </div>
                </button>

                <!-- City Gold -->
                <div class="bg-slate-900/80 px-3.5 py-1.5 rounded-2xl border-2 border-amber-400 flex items-center gap-2 shadow-inner">
                    <i class="fa-solid fa-coins text-amber-400 text-lg animate-pulse"></i>
                    <div>
                        <span id="gold-count" class="text-base font-black text-amber-300 tracking-wider">0</span>
                        <span class="text-[9px] block text-amber-200 font-bold uppercase leading-none">Earned Gold</span>
                    </div>
                </div>

                <!-- Population -->
                <div class="bg-slate-900/80 px-3 py-1.5 rounded-2xl border-2 border-cyan-400 flex items-center gap-2 shadow-inner">
                    <i class="fa-solid fa-users text-cyan-400 text-base"></i>
                    <span id="population-count" class="text-sm font-bold text-cyan-200">0 Pop</span>
                </div>

                <!-- Fish Inventory -->
                <div class="bg-slate-900/80 px-3 py-1.5 rounded-2xl border-2 border-blue-400 flex items-center gap-2 shadow-inner">
                    <i class="fa-solid fa-fish text-blue-300 text-base"></i>
                    <span id="basket-count" class="text-sm font-bold text-white">0 Fish</span>
                </div>

                <!-- Math Combo Streak -->
                <div class="bg-slate-900/80 px-3 py-1.5 rounded-2xl border-2 border-orange-500 flex items-center gap-2 shadow-inner">
                    <i class="fa-solid fa-fire text-orange-400 text-base animate-bounce"></i>
                    <span id="streak-count" class="text-sm font-bold text-orange-300">0x Streak</span>
                </div>

                <!-- Controls -->
                <button id="sound-toggle-btn" class="bg-sky-800 hover:bg-sky-700 text-white w-9 h-9 rounded-xl flex items-center justify-center border-2 border-sky-400 transition shadow">
                    <i id="sound-icon" class="fa-solid fa-volume-high"></i>
                </button>
                <button id="reset-game-btn" title="Reset Progress" class="bg-red-600 hover:bg-red-700 text-white w-9 h-9 rounded-xl flex items-center justify-center border-2 border-red-400 transition shadow">
                    <i class="fa-solid fa-rotate-right"></i>
                </button>
            </div>
        </div>

        <!-- Tab Navigation -->
        <nav class="bg-sky-900/80 border-t border-cyan-800/60 px-4 py-2">
            <div class="max-w-5xl mx-auto flex justify-center gap-2 sm:gap-3 flex-wrap">
                <button onclick="switchTab('lake')" id="tab-lake" class="nav-btn active px-4 py-2 rounded-xl font-bold text-slate-700 bg-white hover:bg-cyan-50 transition flex items-center gap-2 text-xs sm:text-sm shadow">
                    <i class="fa-solid fa-water text-cyan-600"></i> Ocean Lake
                </button>
                <button onclick="switchTab('market')" id="tab-market" class="nav-btn px-4 py-2 rounded-xl font-bold text-slate-700 bg-white hover:bg-cyan-50 transition flex items-center gap-2 text-xs sm:text-sm shadow">
                    <i class="fa-solid fa-store text-amber-600"></i> Harbor Market
                </button>
                <button onclick="switchTab('city')" id="tab-city" class="nav-btn px-4 py-2 rounded-xl font-bold text-slate-700 bg-white hover:bg-cyan-50 transition flex items-center gap-2 text-xs sm:text-sm shadow">
                    <i class="fa-solid fa-city text-emerald-600"></i> City Builder
                </button>
                <button onclick="switchTab('stats')" id="tab-stats" class="nav-btn px-4 py-2 rounded-xl font-bold text-slate-700 bg-white hover:bg-cyan-50 transition flex items-center gap-2 text-xs sm:text-sm shadow">
                    <i class="fa-solid fa-chart-pie text-purple-600"></i> Mayor Analytics
                </button>
            </div>
        </nav>
    </header>

    <!-- MAIN GAME WORKSPACE -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full">

        <!-- SCREEN 1: OCEAN LAKE VIEW -->
        <section id="screen-lake" class="game-screen">
            <!-- Math Controls & Settings Bar -->
            <div class="game-panel p-4 rounded-2xl mb-4 flex flex-wrap items-center justify-between gap-4">
                <div class="flex items-center gap-2 text-sky-950 font-bold text-sm">
                    <i class="fa-solid fa-graduation-cap text-cyan-600 text-lg"></i> Math Level Settings:
                </div>
                <div class="flex flex-wrap items-center gap-3">
                    <!-- Grade Selection Dropdown -->
                    <div class="flex items-center gap-1.5">
                        <label for="filter-grade" class="text-xs font-bold text-slate-600">Grade Level:</label>
                        <select id="filter-grade" onchange="updateMathSettings()" class="bg-sky-50 border-2 border-sky-300 rounded-xl px-3 py-1.5 text-xs font-bold text-sky-900 focus:outline-none focus:border-cyan-500">
                            <option value="1">Grade 1 (Addition & Subtraction to 20)</option>
                            <option value="2">Grade 2 (Numbers to 100 & Skip Count)</option>
                            <option value="3">Grade 3 (Multiplication & Division 10x10)</option>
                            <option value="4">Grade 4 (Multi-digit & Fractions)</option>
                            <option value="5">Grade 5 (Decimals & Order of Operations)</option>
                            <option value="6">Grade 6 (Percentages, Ratios, Negative #s)</option>
                            <option value="7">Grade 7 (Linear Equations & Integers)</option>
                            <option value="8">Grade 8 (Slope, Pythagorean Theorem)</option>
                            <option value="9">Grade 9 (Algebra 1 & Quadratics)</option>
                            <option value="10">Grade 10 (Geometry & Trigonometry)</option>
                            <option value="11">Grade 11 (Algebra 2 & Pre-Calculus)</option>
                            <option value="12">Grade 12 (Calculus & Derivatives)</option>
                        </select>
                    </div>

                    <button onclick="openGradePickerModal()" class="bg-cyan-600 hover:bg-cyan-700 text-white font-bold px-3 py-1.5 rounded-xl text-xs transition shadow flex items-center gap-1.5">
                        <i class="fa-solid fa-list-ol"></i> Visual Grade Grid
                    </button>
                </div>
            </div>

            <!-- Ocean Canvas Container -->
            <div class="relative rounded-3xl border-4 border-cyan-500 bg-gradient-to-b from-sky-400 via-blue-600 to-indigo-950 overflow-hidden shadow-2xl p-4">
                <div class="flex justify-between items-center mb-3 bg-slate-900/80 backdrop-blur-md px-4 py-2.5 rounded-2xl border border-cyan-400 text-xs text-white">
                    <div class="flex items-center gap-2 font-bold text-cyan-300">
                        <i class="fa-solid fa-fish text-cyan-400"></i> Active Marine Habitat
                    </div>
                    <div>
                        Active Sea Creatures: <span id="lake-fish-count" class="font-bold text-amber-300">0</span>
                    </div>
                </div>

                <!-- Canvas -->
                <div class="relative w-full h-[450px] rounded-2xl border-2 border-cyan-300/40 overflow-hidden cursor-pointer">
                    <canvas id="lakeCanvas" class="w-full h-full block"></canvas>
                    <div class="absolute bottom-3 left-1/2 -translate-x-1/2 bg-slate-950/80 text-cyan-200 text-xs px-4 py-2 rounded-full border border-cyan-400 backdrop-blur pointer-events-none flex items-center gap-2 shadow-lg">
                        <i class="fa-solid fa-hand-pointer text-amber-300 animate-pulse"></i> Click any swimming fish to solve a Grade-level math problem and catch it!
                    </div>
                </div>
            </div>
        </section>

        <!-- SCREEN 2: HARBOR & FISH MARKET -->
        <section id="screen-market" class="game-screen hidden">
            <div class="game-panel p-4 sm:p-6 rounded-3xl">
                <!-- Header -->
                <div class="flex flex-wrap justify-between items-center gap-3 mb-6 bg-amber-500/10 p-4 rounded-2xl border border-amber-300">
                    <div>
                        <h2 class="text-xl font-bold text-amber-900 flex items-center gap-2">
                            <i class="fa-solid fa-store text-amber-600"></i> Mayor's Harbor Fish Market
                        </h2>
                        <p class="text-xs text-slate-600">Sell your caught fish for City Gold, or complete Grade-adapted Math Tax Multipliers for extra profit!</p>
                    </div>
                    <div class="flex gap-2">
                        <button id="multiplier-tax-btn" class="bg-gradient-to-r from-purple-600 to-indigo-600 hover:from-purple-700 hover:to-indigo-700 text-white font-bold px-4 py-2.5 rounded-xl border-2 border-purple-800 shadow transition flex items-center gap-2 text-xs sm:text-sm active:scale-95">
                            <i class="fa-solid fa-bolt text-yellow-300"></i> 2x Math Tax Multiplier Challenge!
                        </button>
                        <button id="sell-all-btn" class="bg-amber-500 hover:bg-amber-600 text-white font-bold px-4 py-2.5 rounded-xl border-2 border-amber-700 shadow transition flex items-center gap-2 text-xs sm:text-sm active:scale-95">
                            <i class="fa-solid fa-sack-dollar"></i> Sell All Fish
                        </button>
                    </div>
                </div>

                <!-- Boat Navigation Canvas -->
                <div class="relative w-full h-[260px] rounded-2xl border-4 border-amber-600/40 bg-gradient-to-b from-sky-400 to-blue-700 overflow-hidden shadow-inner mb-6">
                    <canvas id="marketCanvas" class="w-full h-full block"></canvas>

                    <!-- Touch Controls -->
                    <div class="absolute bottom-3 right-3 flex gap-2 z-20">
                        <div class="grid grid-cols-3 gap-1 bg-slate-950/60 p-2 rounded-2xl backdrop-blur">
                            <div></div>
                            <button id="btn-up" class="w-9 h-9 bg-amber-400 hover:bg-amber-300 text-slate-900 font-bold rounded-lg shadow active:scale-90 flex items-center justify-center">
                                <i class="fa-solid fa-arrow-up"></i>
                            </button>
                            <div></div>
                            <button id="btn-left" class="w-9 h-9 bg-amber-400 hover:bg-amber-300 text-slate-900 font-bold rounded-lg shadow active:scale-90 flex items-center justify-center">
                                <i class="fa-solid fa-arrow-left"></i>
                            </button>
                            <button id="btn-down" class="w-9 h-9 bg-amber-400 hover:bg-amber-300 text-slate-900 font-bold rounded-lg shadow active:scale-90 flex items-center justify-center">
                                <i class="fa-solid fa-arrow-down"></i>
                            </button>
                            <button id="btn-right" class="w-9 h-9 bg-amber-400 hover:bg-amber-300 text-slate-900 font-bold rounded-lg shadow active:scale-90 flex items-center justify-center">
                                <i class="fa-solid fa-arrow-right"></i>
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Fish Inventory List -->
                <h3 class="font-bold text-slate-800 mb-3 flex items-center gap-2 text-sm uppercase tracking-wider">
                    <i class="fa-solid fa-box-open text-amber-600"></i> Fish Inventory in Cargo Hold
                </h3>
                <div id="market-inventory" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-3">
                    <!-- Dynamic Items -->
                </div>
            </div>
        </section>

        <!-- SCREEN 3: ISOMETRIC CITY BUILDER CANVAS -->
        <section id="screen-city" class="game-screen hidden">
            <div class="game-panel p-4 sm:p-6 rounded-3xl">
                <!-- Header -->
                <div class="flex flex-wrap justify-between items-center gap-3 mb-4 bg-emerald-500/10 p-4 rounded-2xl border border-emerald-300">
                    <div>
                        <h2 class="text-xl font-bold text-emerald-950 flex items-center gap-2">
                            <i class="fa-solid fa-city text-emerald-600"></i> Isometric Math Megacity Visualizer
                        </h2>
                        <p class="text-xs text-slate-600">Spend strictly math-earned coins to build housing, bullet rail, power grids, and landmarks across city districts!</p>
                    </div>
                    <div class="text-right">
                        <span id="city-title-badge" class="text-xs font-black text-emerald-900 bg-emerald-100 px-3 py-1.5 rounded-full border border-emerald-300">
                            Town Stage: Fisherman Village
                        </span>
                    </div>
                </div>

                <!-- City Isometric Map Canvas -->
                <div class="relative w-full h-[420px] rounded-2xl border-4 border-emerald-600/40 bg-gradient-to-b from-sky-200 via-emerald-100 to-green-300 overflow-hidden shadow-inner mb-6">
                    <canvas id="cityCanvas" class="w-full h-full block"></canvas>
                    <div class="absolute top-3 left-3 bg-slate-900/80 text-white text-xs px-3 py-1.5 rounded-xl border border-white/20 backdrop-blur pointer-events-none">
                        <i class="fa-solid fa-map-pin text-emerald-400"></i> Live Isometric City Rendering
                    </div>
                </div>

                <!-- District Upgrades Navigation Tabs -->
                <div class="flex border-b border-emerald-200 mb-4 overflow-x-auto gap-2 pb-2">
                    <button onclick="switchDistrictTab('residential')" id="dist-tab-residential" class="dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-emerald-600 text-white shadow">
                        <i class="fa-solid fa-house-user"></i> Residential
                    </button>
                    <button onclick="switchDistrictTab('infrastructure')" id="dist-tab-infrastructure" class="dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-white text-slate-700 hover:bg-emerald-50">
                        <i class="fa-solid fa-road"></i> Infrastructure
                    </button>
                    <button onclick="switchDistrictTab('commerce')" id="dist-tab-commerce" class="dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-white text-slate-700 hover:bg-emerald-50">
                        <i class="fa-solid fa-building-columns"></i> Commerce
                    </button>
                    <button onclick="switchDistrictTab('landmarks')" id="dist-tab-landmarks" class="dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-white text-slate-700 hover:bg-emerald-50">
                        <i class="fa-solid fa-monument"></i> Culture & Landmarks
                    </button>
                    <button onclick="switchDistrictTab('public')" id="dist-tab-public" class="dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-white text-slate-700 hover:bg-emerald-50">
                        <i class="fa-solid fa-hospital"></i> Public Services
                    </button>
                </div>

                <!-- District Upgrades Cards Grid -->
                <div id="district-upgrades-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                    <!-- Dynamic Upgrade Cards -->
                </div>
            </div>
        </section>

        <!-- SCREEN 4: MAYOR ANALYTICS -->
        <section id="screen-stats" class="game-screen hidden">
            <div class="game-panel p-4 sm:p-6 rounded-3xl">
                <div class="mb-6 bg-purple-500/10 p-4 rounded-2xl border border-purple-300">
                    <h2 class="text-xl font-bold text-purple-950 flex items-center gap-2">
                        <i class="fa-solid fa-chart-pie text-purple-600"></i> Math Mayor Performance Dashboard
                    </h2>
                    <p class="text-xs text-slate-600">Review your math accuracy, grade level metrics, and total earned math revenue!</p>
                </div>

                <!-- Stats Overview Grid -->
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mb-6">
                    <div class="bg-white p-5 rounded-2xl border-2 border-purple-200 shadow-sm text-center">
                        <i class="fa-solid fa-circle-check text-emerald-500 text-3xl mb-2"></i>
                        <h4 class="text-xs font-bold text-slate-500 uppercase">Total Problems Solved</h4>
                        <p id="stat-total-q" class="text-3xl font-black text-slate-800 mt-1">0</p>
                    </div>

                    <div class="bg-white p-5 rounded-2xl border-2 border-purple-200 shadow-sm text-center">
                        <i class="fa-solid fa-bullseye text-cyan-500 text-3xl mb-2"></i>
                        <h4 class="text-xs font-bold text-slate-500 uppercase">Math Accuracy Rate</h4>
                        <p id="stat-accuracy" class="text-3xl font-black text-cyan-600 mt-1">0%</p>
                    </div>

                    <div class="bg-white p-5 rounded-2xl border-2 border-purple-200 shadow-sm text-center">
                        <i class="fa-solid fa-fire text-orange-500 text-3xl mb-2"></i>
                        <h4 class="text-xs font-bold text-slate-500 uppercase">Max Combo Streak</h4>
                        <p id="stat-best-streak" class="text-3xl font-black text-orange-500 mt-1">0x</p>
                    </div>

                    <div class="bg-white p-5 rounded-2xl border-2 border-purple-200 shadow-sm text-center">
                        <i class="fa-solid fa-coins text-amber-500 text-3xl mb-2"></i>
                        <h4 class="text-xs font-bold text-slate-500 uppercase">Lifetime Math Revenue</h4>
                        <p id="stat-total-gold" class="text-3xl font-black text-amber-500 mt-1">0</p>
                    </div>
                </div>

                <!-- Grade Activity Breakdown -->
                <div class="bg-white p-5 rounded-2xl border-2 border-purple-200 shadow-sm">
                    <h3 class="font-bold text-slate-800 text-sm mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-graduation-cap text-purple-600"></i> Solved Problems by Grade Level
                    </h3>
                    <div id="stats-grade-list" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-6 gap-3">
                        <!-- Dynamic Grade Stats -->
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- FOOTER -->
    <footer class="bg-sky-950 text-cyan-300 text-xs text-center py-3 border-t-2 border-cyan-800">
        <p>Math Mayor: Fisherman City Builder &copy; 2026 • Grade 1 to 12 Educational Game</p>
    </footer>

    <!-- GRADE SELECTOR MODAL -->
    <div id="grade-picker-modal" class="fixed inset-0 bg-slate-950/85 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl border-4 border-indigo-600 max-w-2xl w-full p-6 shadow-2xl relative animate-pop">
            <div class="flex justify-between items-center mb-4 pb-3 border-b border-indigo-100">
                <div class="flex items-center gap-3">
                    <div class="bg-indigo-600 text-white w-10 h-10 rounded-2xl flex items-center justify-center text-xl">
                        <i class="fa-solid fa-graduation-cap"></i>
                    </div>
                    <div>
                        <h3 class="text-xl font-black text-indigo-950">Select Math Grade Level</h3>
                        <p class="text-xs text-slate-500">Choose your current grade level (Grade 1 to Grade 12)</p>
                    </div>
                </div>
                <button onclick="closeGradePickerModal()" class="text-slate-400 hover:text-slate-600 text-xl font-bold px-2">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>

            <!-- Grade Grid (1 to 12) -->
            <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-3 mb-4 max-h-[60vh] overflow-y-auto p-1">
                <button onclick="selectGrade(1)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 1</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Addition & Subtraction to 20</span>
                </button>
                <button onclick="selectGrade(2)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 2</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Numbers to 100 & Skip Count</span>
                </button>
                <button onclick="selectGrade(3)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 3</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Multiplication & Division 10x10</span>
                </button>
                <button onclick="selectGrade(4)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 4</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Multi-digit & Fraction Addition</span>
                </button>
                <button onclick="selectGrade(5)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 5</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Decimals, PEMDAS, Fractions</span>
                </button>
                <button onclick="selectGrade(6)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 6</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Percentages, Negative #s, Exponents</span>
                </button>
                <button onclick="selectGrade(7)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 7</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Linear Equations & Integers</span>
                </button>
                <button onclick="selectGrade(8)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 8</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Slope, Pythagorean Theorem</span>
                </button>
                <button onclick="selectGrade(9)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 9 (Algebra 1)</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Quadratics & Linear Systems</span>
                </button>
                <button onclick="selectGrade(10)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 10 (Geometry)</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Trigonometry & Volumes</span>
                </button>
                <button onclick="selectGrade(11)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 11 (Alg 2/Pre-Calc)</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Logarithms & Complex #s</span>
                </button>
                <button onclick="selectGrade(12)" class="grade-card border-2 border-indigo-200 hover:border-indigo-500 bg-indigo-50/50 hover:bg-indigo-100 p-3 rounded-2xl text-left transition active:scale-95">
                    <span class="text-xs font-black text-indigo-600 block">Grade 12 (Calculus)</span>
                    <span class="text-[11px] text-slate-600 font-bold block mt-1">Derivatives, Integrals, Limits</span>
                </button>
            </div>
        </div>
    </div>

    <!-- MATH QUESTION MODAL -->
    <div id="math-modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl border-4 border-cyan-500 max-w-lg w-full p-6 shadow-2xl relative animate-pop">
            <div class="flex items-center justify-between mb-4 pb-3 border-b border-sky-100">
                <div class="flex items-center gap-3">
                    <span id="modal-fish-icon" class="text-4xl">🐟</span>
                    <div>
                        <h3 id="modal-fish-name" class="text-lg font-bold text-sky-950">Marine Species</h3>
                        <div class="flex gap-2 items-center mt-0.5">
                            <span id="modal-fish-tier" class="text-xs font-bold text-amber-600 bg-amber-50 px-2 py-0.5 rounded-full border border-amber-200 uppercase">Tier 1</span>
                            <span id="modal-grade-label" class="text-xs font-bold text-indigo-600 bg-indigo-50 px-2 py-0.5 rounded-full border border-indigo-200 uppercase">Grade 3</span>
                        </div>
                    </div>
                </div>
                <div id="modal-fish-reward" class="bg-amber-100 text-amber-950 text-sm font-bold px-3 py-1.5 rounded-xl border border-amber-300">
                    +0 Gold
                </div>
            </div>

            <div class="mb-6">
                <span class="text-xs font-bold text-cyan-700 uppercase tracking-wider">Solve math problem to catch:</span>
                <div id="modal-question-box" class="text-xl sm:text-2xl font-black text-slate-800 mt-2 bg-sky-50 p-5 rounded-2xl border-2 border-sky-200 text-center shadow-inner leading-relaxed">
                    Loading problem...
                </div>
            </div>

            <div id="modal-options" class="grid grid-cols-2 gap-3">
                <!-- Answer buttons -->
            </div>
        </div>
    </div>

    <!-- MATH TAX MULTIPLIER MODAL -->
    <div id="multiplier-modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl border-4 border-purple-600 max-w-md w-full p-6 shadow-2xl relative animate-pop text-center">
            <div class="bg-purple-100 text-purple-800 w-12 h-12 rounded-full flex items-center justify-center mx-auto mb-3 border-2 border-purple-300">
                <i class="fa-solid fa-bolt text-xl"></i>
            </div>
            <h3 class="text-xl font-black text-purple-950 mb-1">Mayor's Math Tax Multiplier!</h3>
            <p class="text-xs text-slate-600 mb-4">Solve this Grade-adapted equation to double your harbor payout!</p>

            <div id="multiplier-question" class="text-2xl font-black text-slate-800 bg-purple-50 p-4 rounded-2xl border-2 border-purple-200 mb-4">
                Loading problem...
            </div>

            <div id="multiplier-options" class="grid grid-cols-2 gap-3">
                <!-- Options -->
            </div>
        </div>
    </div>

    <!-- FLOATING GOLD ANIMATION CONTAINER -->
    <div id="gold-fly-container" class="fixed inset-0 pointer-events-none z-50 overflow-hidden"></div>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        /* -------------------------------------------------------------------------- */
        /*                           SYNTHETIC AUDIO ENGINE                           */
        /* -------------------------------------------------------------------------- */
        class AudioEngine {
            constructor() {
                this.ctx = null;
                this.muted = false;
            }

            init() {
                if (!this.ctx) {
                    const AudioCtx = window.AudioContext || window.webkitAudioContext;
                    if (AudioCtx) this.ctx = new AudioCtx();
                }
            }

            playTone(freq, type = 'sine', duration = 0.1, gainVal = 0.15) {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = type;
                    osc.frequency.value = freq;
                    gain.gain.setValueAtTime(gainVal, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.0001, this.ctx.currentTime + duration);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + duration);
                } catch (e) {}
            }

            playCatch() {
                this.playTone(523.25, 'triangle', 0.08, 0.2); // C5
                setTimeout(() => this.playTone(659.25, 'triangle', 0.08, 0.2), 60); // E5
                setTimeout(() => this.playTone(783.99, 'triangle', 0.15, 0.2), 120); // G5
            }

            playCoin() {
                this.playTone(987.77, 'sine', 0.06, 0.15); // B5
                setTimeout(() => this.playTone(1318.51, 'sine', 0.18, 0.2), 60); // E6
            }

            playBuild() {
                this.playTone(180, 'square', 0.08, 0.2);
                setTimeout(() => this.playTone(260, 'triangle', 0.1, 0.2), 70);
                setTimeout(() => this.playTone(340, 'triangle', 0.15, 0.2), 140);
            }

            playWrong() {
                this.playTone(220, 'sawtooth', 0.15, 0.2);
                setTimeout(() => this.playTone(150, 'sawtooth', 0.25, 0.2), 90);
            }
        }

        const sound = new AudioEngine();

        /* -------------------------------------------------------------------------- */
        /*                               GAME STATE                                   */
        /* -------------------------------------------------------------------------- */
        const DEFAULT_STATE = {
            gold: 100,
            basket: [],
            streak: 0,
            bestStreak: 0,
            hasChosenInitialGrade: false,
            grade: 3,
            stats: {
                totalAnswered: 0,
                correctCount: 0,
                totalGoldEarned: 100,
                byGrade: { 1:0, 2:0, 3:0, 4:0, 5:0, 6:0, 7:0, 8:0, 9:0, 10:0, 11:0, 12:0 }
            },
            city: {
                huts: 1, suburbs: 0, apartments: 0, skyscrapers: 0, penthouses: 0,
                pavedRoads: 0, solarFarm: 0, hydroDam: 0, bulletRail: 0,
                marketStall: 1, harborDock: 0, tradeCenter: 0, techPark: 0, financialDist: 0,
                park: 0, university: 0, aquarium: 0, ferrisWheel: 0, statue: 0,
                hospital: 0, fireStation: 0, recyclingHub: 0, airport: 0
            }
        };

        let gameState = JSON.parse(localStorage.getItem('math_mayor_save_g12')) || DEFAULT_STATE;

        function saveGame() {
            localStorage.setItem('math_mayor_save_g12', JSON.stringify(gameState));
            updateUI();
        }

        /* -------------------------------------------------------------------------- */
        /*                           FISH SPECIES CATALOG                             */
        /* -------------------------------------------------------------------------- */
        const FISH_SPECIES = [
            { id: 'minnow', name: 'Small Minnow', emoji: '🐟', baseVal: 20, size: 22, color: '#f87171', speed: 1.2, tier: 'Tier 1' },
            { id: 'clown', name: 'Clownfish', emoji: '🐠', baseVal: 45, size: 26, color: '#fb923c', speed: 1.6, tier: 'Tier 2' },
            { id: 'trout', name: 'Rainbow Trout', emoji: '🎏', baseVal: 90, size: 32, color: '#38bdf8', speed: 2.1, tier: 'Tier 3' },
            { id: 'bass', name: 'Golden Bass', emoji: '🌟', baseVal: 180, size: 38, color: '#fbbf24', speed: 2.6, tier: 'Tier 4' },
            { id: 'kraken', name: 'Legendary Kraken', emoji: '🐙', baseVal: 450, size: 48, color: '#a855f7', speed: 3.2, tier: 'Tier 5' }
        ];

        /* -------------------------------------------------------------------------- */
        /*                       CITY BUILDINGS & UPGRADES CATALOG                     */
        /* -------------------------------------------------------------------------- */
        const CITY_CATALOG = {
            residential: [
                { key: 'huts', name: 'Fisherman Huts', price: 0, basePop: 10, bonus: 'Basic Shelter', desc: 'Cozy coastal homes for early settlers.' },
                { key: 'suburbs', name: 'Suburban District', price: 250, basePop: 80, bonus: '+80 Population', desc: 'Quiet residential neighborhoods.' },
                { key: 'apartments', name: 'Apartment Complex', price: 750, basePop: 250, bonus: '+250 Population', desc: 'High-density modern living.' },
                { key: 'skyscrapers', name: 'Eco-Skyscrapers', price: 2200, basePop: 800, bonus: '+800 Population', desc: 'Green towers towering over the bay.' },
                { key: 'penthouses', name: 'Luxury Penthouses', price: 6000, basePop: 2000, bonus: '+2000 Population', desc: 'Ultra-luxurious oceanfront skyline.' }
            ],
            infrastructure: [
                { key: 'pavedRoads', name: 'Paved Avenues', price: 150, basePop: 20, bonus: 'City Transport Grid', desc: 'Smooth concrete transit network.' },
                { key: 'solarFarm', name: 'Solar Power Farm', price: 600, basePop: 50, bonus: 'Clean Energy', desc: 'Clean renewable energy grid.' },
                { key: 'hydroDam', name: 'Hydroelectric Dam', price: 1800, basePop: 150, bonus: 'Water Reserve', desc: 'Massive water power station.' },
                { key: 'bulletRail', name: 'High-Speed Bullet Rail', price: 5000, basePop: 600, bonus: 'Rapid Transit', desc: 'Super-fast magnetic levitation train.' }
            ],
            commerce: [
                { key: 'marketStall', name: 'Harbor Fish Market', price: 100, basePop: 15, bonus: 'Market Stall', desc: 'Bustling daily seafood bazaar.' },
                { key: 'harborDock', name: 'Commercial Cargo Dock', price: 500, basePop: 60, bonus: 'Deep Harbor', desc: 'Deep-water shipping cargo terminal.' },
                { key: 'tradeCenter', name: 'World Trade Center', price: 1500, basePop: 200, bonus: 'Global Hub', desc: 'Hub for international ocean commerce.' },
                { key: 'techPark', name: 'Silicon Bay Tech Park', price: 4000, basePop: 500, bonus: 'Innovation Zone', desc: 'Research labs and tech start-ups.' },
                { key: 'financialDist', name: 'Wall Street Financial Hub', price: 10000, basePop: 1200, bonus: 'Capital Core', desc: 'Global banking and stock exchange.' }
            ],
            landmarks: [
                { key: 'park', name: 'City Central Park', price: 300, basePop: 40, bonus: 'Civic Joy', desc: 'Lush greenery and decorative fountain.' },
                { key: 'university', name: 'Math Mayor University', price: 1200, basePop: 180, bonus: 'Math Research', desc: 'Top mathematics research college.' },
                { key: 'aquarium', name: 'Oceanarium Stadium', price: 3200, basePop: 450, bonus: 'Tourism Attraction', desc: 'World-class marine life exhibition.' },
                { key: 'ferrisWheel', name: 'Bay Ferris Wheel', price: 4500, basePop: 700, bonus: 'Skyline Landmark', desc: 'Giant tourist observation wheel.' },
                { key: 'statue', name: 'Grand Mayor Golden Statue', price: 12000, basePop: 2500, bonus: 'Golden Prestige', desc: 'Shining golden statue of the Mayor.' }
            ],
            public: [
                { key: 'hospital', name: 'Modern Medical Center', price: 800, basePop: 100, bonus: 'Public Health', desc: 'Advanced regional healthcare facility.' },
                { key: 'fireStation', name: 'Fire & Rescue Station', price: 400, basePop: 50, bonus: 'Safety Grid', desc: 'First-responder emergency team.' },
                { key: 'recyclingHub', name: 'Zero-Waste Recycling Hub', price: 1000, basePop: 120, bonus: 'Eco Sustainability', desc: 'Green waste processing facility.' },
                { key: 'airport', name: 'International Airport', price: 8500, basePop: 1500, bonus: 'Global Air Network', desc: 'Global aviation connection hub.' }
            ]
        };

        let currentDistrictTab = 'residential';

        /* -------------------------------------------------------------------------- */
        /*                       GRADE 1 TO GRADE 12 MATH ENGINE                      */
        /* -------------------------------------------------------------------------- */
        function generateMathProblemForGrade(gradeLevel) {
            const g = parseInt(gradeLevel) || 3;
            let questionText, answer, rewardMult = 1.0 + (g * 0.25);

            switch(g) {
                case 1: { // Grade 1: Addition & Subtraction up to 20
                    if (Math.random() < 0.5) {
                        let a = rand(2, 12), b = rand(1, 8);
                        answer = a + b;
                        questionText = `${a} + ${b} = ?`;
                    } else {
                        let a = rand(6, 18), b = rand(1, a);
                        answer = a - b;
                        questionText = `${a} - ${b} = ?`;
                    }
                    break;
                }
                case 2: { // Grade 2: Add/Sub to 100, skip counting
                    let type = rand(1, 3);
                    if (type === 1) {
                        let a = rand(15, 50), b = rand(15, 45);
                        answer = a + b;
                        questionText = `${a} + ${b} = ?`;
                    } else if (type === 2) {
                        let a = rand(40, 95), b = rand(10, a - 5);
                        answer = a - b;
                        questionText = `${a} - ${b} = ?`;
                    } else {
                        let step = [2, 5, 10][rand(0, 2)];
                        let start = rand(2, 6) * step;
                        answer = start + step * 3;
                        questionText = `Skip count by ${step}: ${start}, ${start+step}, ${start+step*2}, __?`;
                    }
                    break;
                }
                case 3: { // Grade 3: Mult/Div facts 10x10, 3-digit add
                    if (Math.random() < 0.4) {
                        let a = rand(2, 10), b = rand(2, 10);
                        answer = a * b;
                        questionText = `${a} × ${b} = ?`;
                    } else if (Math.random() < 0.8) {
                        let b = rand(2, 10), ans = rand(2, 10);
                        let a = b * ans;
                        answer = ans;
                        questionText = `${a} ÷ ${b} = ?`;
                    } else {
                        let a = rand(120, 450), b = rand(110, 400);
                        answer = a + b;
                        questionText = `${a} + ${b} = ?`;
                    }
                    break;
                }
                case 4: { // Grade 4: Multi-digit mult, division with remainder, fraction add
                    let type = rand(1, 3);
                    if (type === 1) {
                        let a = rand(12, 35), b = rand(11, 22);
                        answer = a * b;
                        questionText = `${a} × ${b} = ?`;
                    } else if (type === 2) {
                        let den = rand(5, 12);
                        let n1 = rand(1, den - 2), n2 = rand(1, den - n1);
                        answer = `${n1 + n2}/${den}`;
                        questionText = `${n1}/${den} + ${n2}/${den} = ?`;
                    } else {
                        let divisor = rand(4, 9);
                        let ans = rand(12, 30);
                        let rem = rand(1, divisor - 1);
                        let dividend = divisor * ans + rem;
                        answer = `${ans} R${rem}`;
                        questionText = `Divide: ${dividend} ÷ ${divisor} = ?`;
                    }
                    break;
                }
                case 5: { // Grade 5: Decimals, PEMDAS, fraction multiplication
                    let type = rand(1, 3);
                    if (type === 1) {
                        let a = (rand(12, 85) / 10).toFixed(1);
                        let b = (rand(11, 65) / 10).toFixed(1);
                        answer = (parseFloat(a) + parseFloat(b)).toFixed(1);
                        questionText = `${a} + ${b} = ?`;
                    } else if (type === 2) {
                        let a = rand(5, 20), b = rand(2, 6), c = rand(2, 5);
                        answer = a + (b * c);
                        questionText = `Evaluate: ${a} + ${b} × ${c} = ?`;
                    } else {
                        answer = `6/20`;
                        questionText = `Simplify: 3/4 × 2/5 = 6/?`;
                        answer = `20`;
                    }
                    break;
                }
                case 6: { // Grade 6: Percentages, ratios, negative numbers, exponents
                    let type = rand(1, 4);
                    if (type === 1) {
                        let pct = [10, 20, 25, 50][rand(0, 3)];
                        let num = rand(4, 20) * 10;
                        answer = (pct / 100) * num;
                        questionText = `What is ${pct}% of ${num}?`;
                    } else if (type === 2) {
                        let a = rand(-15, -2), b = rand(3, 18);
                        answer = a + b;
                        questionText = `Solve: (${a}) + ${b} = ?`;
                    } else if (type === 3) {
                        let base = rand(2, 5), exp = rand(2, 4);
                        answer = Math.pow(base, exp);
                        questionText = `Evaluate: ${base}^${exp} = ?`;
                    } else {
                        let a = rand(2, 8);
                        answer = a * 4;
                        questionText = `Ratio 1 : 4 = ${a} : ?`;
                    }
                    break;
                }
                case 7: { // Grade 7: One/two step equations, integers, unit rate
                    let type = rand(1, 3);
                    if (type === 1) {
                        let x = rand(2, 12), m = rand(2, 6), b = rand(2, 15);
                        answer = x;
                        questionText = `Solve for x: ${m}x + ${b} = ${m * x + b}`;
                    } else if (type === 2) {
                        let a = rand(-12, -2), b = rand(-8, -2);
                        answer = a * b;
                        questionText = `Solve: (${a}) × (${b}) = ?`;
                    } else {
                        let miles = rand(120, 300), hours = rand(2, 5);
                        answer = miles / hours;
                        questionText = `Unit rate: ${miles} miles in ${hours} hours = ? mph`;
                    }
                    break;
                }
                case 8: { // Grade 8: Linear equations, slope, Pythagorean theorem
                    let type = rand(1, 3);
                    if (type === 1) {
                        let a = rand(3, 8), b = rand(4, 10);
                        if (a === 3 && b === 4) answer = 5;
                        else if (a === 6 && b === 8) answer = 10;
                        else {
                            a = 3; b = 4; answer = 5;
                        }
                        questionText = `Pythagorean Theorem: Leg a=${a}, Leg b=${b}. Hypotenuse c = ?`;
                    } else if (type === 2) {
                        let x = rand(3, 10);
                        answer = x;
                        questionText = `Solve for x: 3x - 7 = 2x + ${x - 7}`;
                    } else {
                        let m = rand(2, 5), b = rand(1, 9);
                        answer = m;
                        questionText = `In linear function y = ${m}x + ${b}, what is the slope (m)?`;
                    }
                    break;
                }
                case 9: { // Grade 9: Algebra 1 (Quadratics, factoring, systems)
                    let type = rand(1, 3);
                    if (type === 1) {
                        let r1 = rand(1, 5), r2 = rand(1, 6);
                        answer = `(x + ${r1})(x + ${r2})`;
                        questionText = `Factor: x² + ${r1 + r2}x + ${r1 * r2}`;
                    } else if (type === 2) {
                        let x = rand(2, 6);
                        answer = x;
                        questionText = `Solve positive x: 2x² = ${2 * x * x}`;
                    } else {
                        let x = rand(4, 10), y = rand(1, 5);
                        answer = x;
                        questionText = `System: x + y = ${x + y}, x - y = ${x - y}. Solve for x:`;
                    }
                    break;
                }
                case 10: { // Grade 10: Geometry & Trigonometry
                    let type = rand(1, 3);
                    if (type === 1) {
                        let angle = [30, 45, 60][rand(0, 2)];
                        if (angle === 30) { questionText = `Trigonometry: sin(30°) = ?`; answer = `1/2`; }
                        else if (angle === 45) { questionText = `Trigonometry: tan(45°) = ?`; answer = `1`; }
                        else { questionText = `Trigonometry: cos(60°) = ?`; answer = `1/2`; }
                    } else if (type === 2) {
                        let r = rand(2, 5), h = rand(4, 10);
                        answer = Math.round(Math.PI * r * r * h);
                        questionText = `Volume of cylinder (r=${r}, h=${h}) ≈ ? (use π≈3.14)`;
                    } else {
                        let deg = rand(20, 70);
                        answer = 90 - deg;
                        questionText = `What is the complementary angle of ${deg}°?`;
                    }
                    break;
                }
                case 11: { // Grade 11: Algebra 2 & Pre-Calculus (Logs, Complex, Identities)
                    let type = rand(1, 3);
                    if (type === 1) {
                        let base = rand(2, 4), exp = rand(2, 4);
                        let val = Math.pow(base, exp);
                        answer = exp;
                        questionText = `Logarithms: Evaluate log_${base}(${val}) = ?`;
                    } else if (type === 2) {
                        answer = `7 - 3i`;
                        questionText = `Simplify complex sum: (3 + 2i) + (4 - 5i) = ?`;
                    } else {
                        answer = `1`;
                        questionText = `Trig Identity: sin²(x) + cos²(x) = ?`;
                    }
                    break;
                }
                case 12: { // Grade 12: Calculus (Derivatives, Integrals, Limits, Matrix)
                    let type = rand(1, 4);
                    if (type === 1) {
                        let a = rand(2, 6), b = rand(2, 8);
                        answer = `${2*a}x + ${b}`;
                        questionText = `Calculus Derivative: d/dx (${a}x² + ${b}x) = ?`;
                    } else if (type === 2) {
                        answer = `x² + C`;
                        questionText = `Indefinite Integral: ∫ 2x dx = ?`;
                    } else if (type === 3) {
                        let c = rand(2, 5);
                        answer = 2 * c;
                        questionText = `Limit: lim (x->${c}) (x² - ${c*c}) / (x - ${c}) = ?`;
                    } else {
                        let a = rand(2, 5), b = rand(1, 4), c1 = rand(1, 3), d = rand(2, 6);
                        answer = (a * d) - (b * c1);
                        questionText = `Determinant of 2x2 matrix [[${a}, ${b}], [${c1}, ${d}]] = ?`;
                    }
                    break;
                }
            }

            // Distractors
            const choices = [answer];
            while (choices.length < 4) {
                let dummy;
                if (typeof answer === 'number') {
                    let delta = rand(1, 6) * (Math.random() < 0.5 ? 1 : -1);
                    dummy = Math.max(0, answer + delta);
                } else if (answer.includes && answer.includes('/')) {
                    let parts = answer.split('/');
                    dummy = `${Math.max(1, parseInt(parts[0]) + rand(1, 2))}/${parts[1]}`;
                } else {
                    dummy = `Option ${choices.length + 1}`;
                }
                if (!choices.includes(dummy)) choices.push(dummy);
            }

            choices.sort(() => Math.random() - 0.5);

            return {
                grade: g,
                text: questionText,
                correct: answer,
                options: choices,
                rewardMult: rewardMult
            };
        }

        function rand(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        /* -------------------------------------------------------------------------- */
        /*                           GRADE PICKER MODAL LOGIC                         */
        /* -------------------------------------------------------------------------- */
        function openGradePickerModal() {
            document.getElementById('grade-picker-modal').classList.remove('hidden');
        }

        function closeGradePickerModal() {
            document.getElementById('grade-picker-modal').classList.add('hidden');
        }

        function selectGrade(g) {
            gameState.grade = parseInt(g);
            gameState.hasChosenInitialGrade = true;
            document.getElementById('filter-grade').value = gameState.grade;
            closeGradePickerModal();
            saveGame();
            initLake();
        }

        /* -------------------------------------------------------------------------- */
        /*                           SCREEN 1: OCEAN CANVAS                           */
        /* -------------------------------------------------------------------------- */
        const lakeCanvas = document.getElementById('lakeCanvas');
        const lakeCtx = lakeCanvas.getContext('2d');
        let swimmingCreatures = [];
        let activeFish = null;
        let activeMathProblem = null;
        let activeFishIndex = -1;

        function resizeLakeCanvas() {
            if (!lakeCanvas.parentElement) return;
            lakeCanvas.width = lakeCanvas.parentElement.clientWidth;
            lakeCanvas.height = lakeCanvas.parentElement.clientHeight;
        }

        class MarineCreature {
            constructor() {
                const sp = FISH_SPECIES[Math.floor(Math.random() * FISH_SPECIES.length)];
                Object.assign(this, sp);

                this.problem = generateMathProblemForGrade(gameState.grade);
                this.goldReward = Math.round(this.baseVal * this.problem.rewardMult);

                this.x = Math.random() * (lakeCanvas.width || 600);
                this.y = 70 + Math.random() * ((lakeCanvas.height || 450) - 130);
                this.dir = Math.random() < 0.5 ? 1 : -1;
                this.swimPhase = Math.random() * Math.PI * 2;
            }

            update() {
                this.x += this.speed * this.dir;
                this.swimPhase += 0.07;
                this.y += Math.sin(this.swimPhase) * 0.8;

                if (this.x > lakeCanvas.width + 50) this.dir = -1;
                if (this.x < -50) this.dir = 1;
            }

            draw(ctx) {
                ctx.save();
                ctx.translate(this.x, this.y);
                if (this.dir === -1) ctx.scale(-1, 1);

                // Body
                ctx.beginPath();
                ctx.ellipse(0, 0, this.size, this.size * 0.6, 0, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.fill();
                ctx.lineWidth = 2.5;
                ctx.strokeStyle = '#ffffff';
                ctx.stroke();

                // Tail
                ctx.beginPath();
                ctx.moveTo(-this.size + 4, 0);
                ctx.lineTo(-this.size - 16, -this.size * 0.6);
                ctx.lineTo(-this.size - 16, this.size * 0.6);
                ctx.closePath();
                ctx.fillStyle = this.color;
                ctx.fill();

                // Eye
                ctx.beginPath();
                ctx.arc(this.size * 0.45, -this.size * 0.2, 4, 0, Math.PI * 2);
                ctx.fillStyle = '#ffffff';
                ctx.fill();
                ctx.beginPath();
                ctx.arc(this.size * 0.45 + 1, -this.size * 0.2, 2, 0, Math.PI * 2);
                ctx.fillStyle = '#000000';
                ctx.fill();

                ctx.restore();

                // Gold Tag Label
                ctx.save();
                ctx.font = 'bold 11px Fredoka';
                ctx.fillStyle = '#fde047';
                ctx.textAlign = 'center';
                ctx.fillText(`${this.name} (${this.goldReward}G)`, this.x, this.y - this.size - 8);
                ctx.restore();
            }

            isClicked(cx, cy) {
                const dx = this.x - cx;
                const dy = this.y - cy;
                return Math.sqrt(dx * dx + dy * dy) <= this.size + 14;
            }
        }

        function initLake() {
            resizeLakeCanvas();
            swimmingCreatures = [];
            for (let i = 0; i < 8; i++) {
                swimmingCreatures.push(new MarineCreature());
            }
        }

        function animateLake() {
            lakeCtx.clearRect(0, 0, lakeCanvas.width, lakeCanvas.height);

            // Water Waves Effect
            lakeCtx.fillStyle = 'rgba(2, 132, 199, 0.2)';
            for (let i = 0; i < lakeCanvas.width; i += 80) {
                lakeCtx.beginPath();
                lakeCtx.arc(i, lakeCanvas.height, 60, 0, Math.PI * 2);
                lakeCtx.fill();
            }

            swimmingCreatures.forEach(c => {
                c.update();
                c.draw(lakeCtx);
            });

            document.getElementById('lake-fish-count').innerText = swimmingCreatures.length;
            requestAnimationFrame(animateLake);
        }

        lakeCanvas.addEventListener('click', (e) => {
            const rect = lakeCanvas.getBoundingClientRect();
            const clickX = e.clientX - rect.left;
            const clickY = e.clientY - rect.top;

            for (let i = swimmingCreatures.length - 1; i >= 0; i--) {
                const c = swimmingCreatures[i];
                if (c.isClicked(clickX, clickY)) {
                    openMathModal(c, i);
                    break;
                }
            }
        });

        function updateMathSettings() {
            gameState.grade = parseInt(document.getElementById('filter-grade').value);
            saveGame();
            initLake();
        }

        /* -------------------------------------------------------------------------- */
        /*                          MATH QUESTION MODAL LOGIC                         */
        /* -------------------------------------------------------------------------- */
        function openMathModal(fish, idx) {
            activeFish = fish;
            activeMathProblem = fish.problem;
            activeFishIndex = idx;

            document.getElementById('modal-fish-icon').innerText = fish.emoji;
            document.getElementById('modal-fish-name').innerText = fish.name;
            document.getElementById('modal-fish-tier').innerText = fish.tier;
            document.getElementById('modal-grade-label').innerText = `Grade ${fish.problem.grade}`;
            document.getElementById('modal-fish-reward').innerText = `+${fish.goldReward} Gold`;
            document.getElementById('modal-question-box').innerText = fish.problem.text;

            const optsBox = document.getElementById('modal-options');
            optsBox.innerHTML = '';

            fish.problem.options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = "w-full bg-sky-50 hover:bg-cyan-100 border-2 border-sky-300 font-bold p-4 rounded-2xl transition active:scale-95 text-slate-800 text-base sm:text-lg shadow-sm";
                btn.innerText = opt;
                btn.onclick = () => submitMathAnswer(opt);
                optsBox.appendChild(btn);
            });

            document.getElementById('math-modal').classList.remove('hidden');
        }

        function submitMathAnswer(chosen) {
            document.getElementById('math-modal').classList.add('hidden');
            const isCorrect = String(chosen) === String(activeMathProblem.correct);

            gameState.stats.totalAnswered++;

            if (isCorrect) {
                gameState.stats.correctCount++;
                gameState.streak++;
                if (gameState.streak > gameState.bestStreak) gameState.bestStreak = gameState.streak;

                if (gameState.stats.byGrade[activeMathProblem.grade] !== undefined) {
                    gameState.stats.byGrade[activeMathProblem.grade]++;
                }

                sound.playCatch();
                gameState.basket.push({
                    name: activeFish.name,
                    value: activeFish.goldReward,
                    emoji: activeFish.emoji
                });

                swimmingCreatures.splice(activeFishIndex, 1);
                setTimeout(() => swimmingCreatures.push(new MarineCreature()), 1500);

                showGoldFlyEffect(window.innerWidth / 2, window.innerHeight / 2, `Caught! (+${activeFish.goldReward}G)`);
            } else {
                gameState.streak = 0;
                sound.playWrong();
                if (swimmingCreatures[activeFishIndex]) {
                    swimmingCreatures[activeFishIndex].speed *= 1.8;
                }
            }

            saveGame();
        }

        /* -------------------------------------------------------------------------- */
        /*                           SCREEN 2: HARBOR & MARKET                        */
        /* -------------------------------------------------------------------------- */
        const marketCanvas = document.getElementById('marketCanvas');
        const marketCtx = marketCanvas.getContext('2d');
        const boatPos = { x: 50, y: 130, targetX: 50, targetY: 130 };

        function resizeMarketCanvas() {
            if (!marketCanvas.parentElement) return;
            marketCanvas.width = marketCanvas.parentElement.clientWidth;
            marketCanvas.height = marketCanvas.parentElement.clientHeight;
        }

        function animateMarket() {
            marketCtx.clearRect(0, 0, marketCanvas.width, marketCanvas.height);

            // Harbor Market Stall Dock
            marketCtx.fillStyle = '#92400e';
            marketCtx.fillRect(marketCanvas.width - 100, 0, 100, marketCanvas.height);

            marketCtx.fillStyle = '#ffffff';
            marketCtx.font = 'bold 14px Fredoka';
            marketCtx.fillText('HARBOR STALL', marketCanvas.width - 95, 30);

            // Smooth boat move
            boatPos.x += (boatPos.targetX - boatPos.x) * 0.12;
            boatPos.y += (boatPos.targetY - boatPos.y) * 0.12;

            marketCtx.save();
            marketCtx.translate(boatPos.x, boatPos.y);
            marketCtx.font = '26px serif';
            marketCtx.fillText('⛵', -10, 5);
            marketCtx.restore();

            requestAnimationFrame(animateMarket);
        }

        function moveBoat(dx, dy) {
            boatPos.targetX = Math.max(30, Math.min(marketCanvas.width - 50, boatPos.targetX + dx));
            boatPos.targetY = Math.max(30, Math.min(marketCanvas.height - 30, boatPos.targetY + dy));
        }

        document.getElementById('btn-up').onclick = () => moveBoat(0, -35);
        document.getElementById('btn-down').onclick = () => moveBoat(0, 35);
        document.getElementById('btn-left').onclick = () => moveBoat(-35, 0);
        document.getElementById('btn-right').onclick = () => moveBoat(35, 0);

        function renderMarketScreen() {
            resizeMarketCanvas();
            const container = document.getElementById('market-inventory');
            container.innerHTML = '';

            if (gameState.basket.length === 0) {
                container.innerHTML = `<div class="col-span-full text-center py-8 bg-white/80 rounded-2xl border-2 border-dashed border-amber-300 text-slate-500 font-bold">Cargo hold is empty! Catch fish in the Ocean Lake first.</div>`;
                return;
            }

            gameState.basket.forEach((fish, idx) => {
                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-2xl border-2 border-amber-200 shadow-sm flex items-center justify-between";
                card.innerHTML = `
                    <div class="flex items-center gap-3">
                        <span class="text-3xl">${fish.emoji}</span>
                        <div>
                            <h4 class="font-bold text-slate-800 text-sm">${fish.name}</h4>
                            <span class="text-xs font-bold text-amber-600">+${fish.value} Gold</span>
                        </div>
                    </div>
                    <button onclick="sellSingleFish(${idx})" class="bg-emerald-500 hover:bg-emerald-600 text-white text-xs font-bold px-3.5 py-2 rounded-xl border border-emerald-700 active:scale-95 transition">
                        Sell
                    </button>
                `;
                container.appendChild(card);
            });
        }

        function sellSingleFish(idx) {
            const fish = gameState.basket[idx];
            if (!fish) return;

            gameState.gold += fish.value;
            gameState.stats.totalGoldEarned += fish.value;
            gameState.basket.splice(idx, 1);
            sound.playCoin();
            showGoldFlyEffect(window.innerWidth / 2, window.innerHeight / 2, `+${fish.value} Gold!`);
            saveGame();
            renderMarketScreen();
        }

        document.getElementById('sell-all-btn').onclick = () => {
            if (gameState.basket.length === 0) return;
            let total = 0;
            gameState.basket.forEach(f => total += f.value);
            gameState.gold += total;
            gameState.stats.totalGoldEarned += total;
            gameState.basket = [];

            sound.playCoin();
            showGoldFlyEffect(window.innerWidth / 2, window.innerHeight / 2, `+${total} Gold!`);
            saveGame();
            renderMarketScreen();
        };

        /* Math Tax Multiplier Challenge */
        document.getElementById('multiplier-tax-btn').onclick = () => {
            if (gameState.basket.length === 0) {
                alert("Catch fish in your inventory first to multiply your earnings!");
                return;
            }

            const mathQ = generateMathProblemForGrade(gameState.grade);
            document.getElementById('multiplier-question').innerText = mathQ.text;

            const optsBox = document.getElementById('multiplier-options');
            optsBox.innerHTML = '';

            mathQ.options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = "bg-purple-50 hover:bg-purple-200 border-2 border-purple-300 font-bold p-3.5 rounded-xl transition active:scale-95 text-slate-800 text-base shadow-sm";
                btn.innerText = opt;
                btn.onclick = () => {
                    document.getElementById('multiplier-modal').classList.add('hidden');
                    if (String(opt) === String(mathQ.correct)) {
                        sound.playCatch();
                        gameState.basket.forEach(f => f.value *= 2);
                        showGoldFlyEffect(window.innerWidth / 2, window.innerHeight / 2, "2X EARNINGS MULTIPLIED!");
                        saveGame();
                        renderMarketScreen();
                    } else {
                        sound.playWrong();
                    }
                };
                optsBox.appendChild(btn);
            });

            document.getElementById('multiplier-modal').classList.remove('hidden');
        };

        /* -------------------------------------------------------------------------- */
        /*                     SCREEN 3: ISOMETRIC CITY BUILDER                       */
        /* -------------------------------------------------------------------------- */
        const cityCanvas = document.getElementById('cityCanvas');
        const cityCtx = cityCanvas.getContext('2d');

        function resizeCityCanvas() {
            if (!cityCanvas.parentElement) return;
            cityCanvas.width = cityCanvas.parentElement.clientWidth;
            cityCanvas.height = cityCanvas.parentElement.clientHeight;
        }

        function drawIsoTile(ctx, x, y, width, height, color) {
            ctx.beginPath();
            ctx.moveTo(x, y - height / 2);
            ctx.lineTo(x + width / 2, y);
            ctx.lineTo(x, y + height / 2);
            ctx.lineTo(x - width / 2, y);
            ctx.closePath();
            ctx.fillStyle = color;
            ctx.fill();
            ctx.strokeStyle = 'rgba(0,0,0,0.15)';
            ctx.stroke();
        }

        function animateCity() {
            cityCtx.clearRect(0, 0, cityCanvas.width, cityCanvas.height);

            const tileW = 60;
            const tileH = 30;
            const originX = cityCanvas.width / 2;
            const originY = 120;

            // 6x6 Isometric City Grid
            for (let row = 0; row < 6; row++) {
                for (let col = 0; col < 6; col++) {
                    const isoX = originX + (col - row) * (tileW / 2);
                    const isoY = originY + (col + row) * (tileH / 2);

                    let tileColor = (row + col) % 2 === 0 ? '#86efac' : '#4ade80'; // Grass
                    if (gameState.city.pavedRoads && (row === 2 || col === 2)) tileColor = '#64748b'; // Roads

                    drawIsoTile(cityCtx, isoX, isoY, tileW, tileH, tileColor);

                    // Render City Upgrades visually on grid positions
                    if (row === 0 && col === 0 && gameState.city.huts) drawIsoBuilding(cityCtx, isoX, isoY, '⛺', '#f97316', 20);
                    if (row === 0 && col === 1 && gameState.city.suburbs) drawIsoBuilding(cityCtx, isoX, isoY, '🏡', '#eab308', 30);
                    if (row === 0 && col === 3 && gameState.city.apartments) drawIsoBuilding(cityCtx, isoX, isoY, '🏢', '#3b82f6', 45);
                    if (row === 1 && col === 4 && gameState.city.skyscrapers) drawIsoBuilding(cityCtx, isoX, isoY, '🏙️', '#10b981', 60);
                    if (row === 0 && col === 5 && gameState.city.penthouses) drawIsoBuilding(cityCtx, isoX, isoY, '🏰', '#a855f7', 75);

                    if (row === 1 && col === 1 && gameState.city.solarFarm) drawIsoBuilding(cityCtx, isoX, isoY, '☀️', '#f59e0b', 25);
                    if (row === 3 && col === 0 && gameState.city.hydroDam) drawIsoBuilding(cityCtx, isoX, isoY, '🌊', '#06b6d4', 35);
                    if (row === 2 && col === 2 && gameState.city.bulletRail) drawIsoBuilding(cityCtx, isoX, isoY, '🚅', '#ef4444', 30);

                    if (row === 4 && col === 1 && gameState.city.marketStall) drawIsoBuilding(cityCtx, isoX, isoY, '🏪', '#d97706', 25);
                    if (row === 4 && col === 3 && gameState.city.techPark) drawIsoBuilding(cityCtx, isoX, isoY, '💻', '#6366f1', 50);
                    if (row === 5 && col === 4 && gameState.city.financialDist) drawIsoBuilding(cityCtx, isoX, isoY, '🏦', '#059669', 65);

                    if (row === 3 && col === 3 && gameState.city.park) drawIsoBuilding(cityCtx, isoX, isoY, '⛲', '#22c55e', 25);
                    if (row === 3 && col === 5 && gameState.city.university) drawIsoBuilding(cityCtx, isoX, isoY, '🎓', '#8b5cf6', 45);
                    if (row === 5 && col === 5 && gameState.city.statue) drawIsoBuilding(cityCtx, isoX, isoY, '🗿', '#eab308', 55);

                    if (row === 5 && col === 0 && gameState.city.hospital) drawIsoBuilding(cityCtx, isoX, isoY, '🏥', '#f43f5e', 40);
                    if (row === 5 && col === 1 && gameState.city.airport) drawIsoBuilding(cityCtx, isoX, isoY, '✈️', '#0284c7', 45);
                }
            }

            requestAnimationFrame(animateCity);
        }

        function drawIsoBuilding(ctx, x, y, icon, color, height) {
            ctx.fillStyle = color;
            ctx.fillRect(x - 12, y - height, 24, height);
            ctx.font = '20px serif';
            ctx.fillText(icon, x - 10, y - height - 4);
        }

        function switchDistrictTab(districtKey) {
            currentDistrictTab = districtKey;
            document.querySelectorAll('.dist-btn').forEach(b => {
                b.className = "dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-white text-slate-700 hover:bg-emerald-50";
            });
            document.getElementById(`dist-tab-${districtKey}`).className = "dist-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-emerald-600 text-white shadow";
            renderCityScreen();
        }

        function renderCityScreen() {
            resizeCityCanvas();

            // Calculate Population Metrics strictly (NO PASSIVE TICKING)
            let totalPop = 0;

            Object.keys(CITY_CATALOG).forEach(cat => {
                CITY_CATALOG[cat].forEach(item => {
                    const count = gameState.city[item.key] || 0;
                    totalPop += count * item.basePop;
                });
            });

            document.getElementById('population-count').innerText = `${totalPop.toLocaleString()} Pop`;

            let stageName = "Fisherman Village";
            if (totalPop > 200) stageName = "Coastal Town";
            if (totalPop > 1000) stageName = "Commercial City";
            if (totalPop > 5000) stageName = "Math Megacity";
            document.getElementById('city-title-badge').innerText = `Stage: ${stageName}`;

            const grid = document.getElementById('district-upgrades-grid');
            grid.innerHTML = '';

            const items = CITY_CATALOG[currentDistrictTab] || [];

            items.forEach(item => {
                const count = gameState.city[item.key] || 0;
                const canAfford = gameState.gold >= item.price;

                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-2xl border-2 border-emerald-200 shadow-sm flex flex-col justify-between";
                card.innerHTML = `
                    <div>
                        <div class="flex justify-between items-center mb-1">
                            <h4 class="font-bold text-slate-800 text-sm">${item.name}</h4>
                            <span class="text-xs font-bold text-emerald-700 bg-emerald-50 px-2 py-0.5 rounded-full border border-emerald-200">Built: ${count}</span>
                        </div>
                        <p class="text-xs text-slate-500 mb-3">${item.desc}</p>
                        <div class="flex gap-2 text-[11px] font-bold text-slate-600 mb-3">
                            <span class="bg-cyan-50 text-cyan-800 px-2 py-0.5 rounded">+${item.basePop} Pop</span>
                            <span class="bg-emerald-50 text-emerald-800 px-2 py-0.5 rounded">${item.bonus}</span>
                        </div>
                    </div>
                    <button ${!canAfford ? 'disabled' : ''} onclick="buyCityUpgrade('${item.key}', ${item.price})" 
                        class="w-full py-2.5 rounded-xl font-bold text-xs transition active:scale-95 flex items-center justify-center gap-2 ${
                            canAfford 
                                ? 'bg-amber-500 hover:bg-amber-600 text-white shadow-md border border-amber-600' 
                                : 'bg-slate-100 text-slate-400 border border-slate-200 cursor-not-allowed'
                        }">
                        <i class="fa-solid fa-hammer"></i> Build (${item.price} Gold)
                    </button>
                `;
                grid.appendChild(card);
            });
        }

        function buyCityUpgrade(key, cost) {
            if (gameState.gold >= cost) {
                gameState.gold -= cost;
                gameState.city[key] = (gameState.city[key] || 0) + 1;
                sound.playBuild();
                saveGame();
                renderCityScreen();
            }
        }

        /* -------------------------------------------------------------------------- */
        /*                           SCREEN 4: MAYOR ANALYTICS                        */
        /* -------------------------------------------------------------------------- */
        function renderStatsScreen() {
            document.getElementById('stat-total-q').innerText = gameState.stats.totalAnswered;

            const accuracy = gameState.stats.totalAnswered > 0 
                ? Math.round((gameState.stats.correctCount / gameState.stats.totalAnswered) * 100) 
                : 0;
            document.getElementById('stat-accuracy').innerText = `${accuracy}%`;
            document.getElementById('stat-best-streak').innerText = `${gameState.bestStreak}x`;
            document.getElementById('stat-total-gold').innerText = gameState.stats.totalGoldEarned.toLocaleString();

            const breakdownBox = document.getElementById('stats-grade-list');
            breakdownBox.innerHTML = '';

            for (let g = 1; g <= 12; g++) {
                const count = gameState.stats.byGrade[g] || 0;
                const card = document.createElement('div');
                card.className = "bg-purple-50 p-3 rounded-xl border border-purple-200 text-center";
                card.innerHTML = `
                    <div class="text-[11px] font-bold text-purple-700">Grade ${g}</div>
                    <div class="text-base font-black text-slate-800 mt-1">${count} Correct</div>
                `;
                breakdownBox.appendChild(card);
            }
        }

        /* -------------------------------------------------------------------------- */
        /*                           NAVIGATION & GENERAL UI                          */
        /* -------------------------------------------------------------------------- */
        function switchTab(tab) {
            sound.playTone(600, 'sine', 0.05, 0.05);
            document.querySelectorAll('.game-screen').forEach(s => s.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));

            document.getElementById(`screen-${tab}`).classList.remove('hidden');
            document.getElementById(`tab-${tab}`).classList.add('active');

            if (tab === 'market') renderMarketScreen();
            if (tab === 'city') renderCityScreen();
            if (tab === 'stats') renderStatsScreen();
        }

        function updateUI() {
            document.getElementById('gold-count').innerText = gameState.gold.toLocaleString();
            document.getElementById('basket-count').innerText = `${gameState.basket.length} Fish`;
            document.getElementById('streak-count').innerText = `${gameState.streak}x Streak`;
            document.getElementById('header-grade-badge').innerText = `Grade ${gameState.grade}`;
            document.getElementById('filter-grade').value = gameState.grade;
        }

        function showGoldFlyEffect(x, y, text) {
            const container = document.getElementById('gold-fly-container');
            const el = document.createElement('div');
            el.className = "absolute font-black text-amber-300 text-xl drop-shadow-[0_2px_4px_rgba(0,0,0,0.9)] gold-pop-anim pointer-events-none";
            el.style.left = `${x - 60}px`;
            el.style.top = `${y - 20}px`;
            el.innerHTML = `<i class="fa-solid fa-coins mr-1"></i> ${text}`;
            container.appendChild(el);
            setTimeout(() => el.remove(), 850);
        }

        document.getElementById('sound-toggle-btn').onclick = () => {
            sound.muted = !sound.muted;
            document.getElementById('sound-icon').className = sound.muted ? "fa-solid fa-volume-xmark" : "fa-solid fa-volume-high";
        };

        document.getElementById('reset-game-btn').onclick = () => {
            if (confirm("Reset all Mayor progress, Gold, and City structures?")) {
                gameState = JSON.parse(JSON.stringify(DEFAULT_STATE));
                saveGame();
                renderMarketScreen();
                renderCityScreen();
                renderStatsScreen();
                openGradePickerModal();
            }
        };

        window.addEventListener('resize', () => {
            resizeLakeCanvas();
            resizeMarketCanvas();
            resizeCityCanvas();
        });

        window.onload = () => {
            updateUI();
            initLake();
            animateLake();
            animateMarket();
            animateCity();

            if (!gameState.hasChosenInitialGrade) {
                openGradePickerModal();
            }
        };
    </script>
</body>
</html>
