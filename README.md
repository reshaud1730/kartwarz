[kart_warz_engine.html](https://github.com/user-attachments/files/25743143/kart_warz_engine.html)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- Primary Meta Tags -->
    <title>Kart Warz Manager | FastTrack Tools</title>
    <meta name="title" content="Kart Warz Manager | FastTrack Tools">
    <meta name="description" content="The official tournament engine for Kart Warz. Track teams, points, and race brackets live.">

    <!-- Open Graph / Facebook -->
    <meta property="og:type" content="website">
    <meta property="og:title" content="Kart Warz Tournament Manager">
    <meta property="og:description" content="Live tournament tracking, brackets, and point systems for Kart Warz racers.">
    <meta property="og:image" content="kwlogo.jpeg">

    <!-- Twitter -->
    <meta property="twitter:card" content="summary_large_image">
    <meta property="twitter:title" content="Kart Warz Tournament Manager">
    <meta property="twitter:description" content="Live tournament tracking, brackets, and point systems for Kart Warz racers.">
    <meta property="twitter:image" content="kwlogo.jpeg">

    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        
        [v-cloak] { display: none !important; }
        
        body { 
            font-family: 'Inter', sans-serif; 
            overflow-x: hidden;
            -webkit-tap-highlight-color: transparent;
        }
        
        .racing-italic { font-style: italic; font-weight: 900; }
        
        .glass { 
            background: rgba(255, 255, 255, 0.95); 
            backdrop-filter: blur(12px); 
            -webkit-backdrop-filter: blur(12px);
        }
        
        .stat-card { 
            @apply bg-white p-6 md:p-8 rounded-[2.5rem] shadow-sm border border-slate-200 transition-all duration-300 hover:shadow-xl hover:-translate-y-1 group border-b-8 border-b-slate-100; 
        }
        
        .btn-primary { 
            @apply bg-indigo-600 text-white font-black uppercase tracking-widest py-4 rounded-2xl shadow-lg shadow-indigo-500/20 active:scale-95 transition-all flex items-center justify-center gap-2; 
        }

        .input-field {
            @apply w-full p-4 border rounded-2xl bg-slate-50 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-500/10 outline-none font-bold transition-all;
        }

        /* Prevent layout shift during icon load */
        i[data-lucide] {
            display: inline-block;
            width: 20px;
            height: 20px;
        }

        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen pb-24 md:pb-12 md:pt-20">

<div id="app" v-cloak class="max-w-7xl mx-auto p-4 md:p-8">
    <!-- Navigation -->
    <nav class="fixed bottom-0 left-0 right-0 md:top-0 md:bottom-auto glass border-t md:border-t-0 md:border-b border-slate-200 z-50">
        <div class="max-w-7xl mx-auto flex items-center justify-between h-20 px-4 md:px-6">
            <div class="flex items-center gap-3">
                <img src="kwlogo.jpeg" alt="KW" class="h-10 w-10 object-cover rounded-xl shadow-lg border-2 border-white" onerror="this.src='https://via.placeholder.com/40?text=KW'">
                <div class="hidden lg:flex items-center gap-2 racing-italic text-2xl tracking-tighter text-indigo-600 uppercase">
                    KART WARZ
                </div>
            </div>
            
            <div class="flex gap-1 md:gap-6 w-full md:w-auto justify-around">
                <button @click="activeTab = 'dashboard'" :class="navClass('dashboard')">
                    <i data-lucide="trophy"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Stats</span>
                </button>
                <button @click="activeTab = 'teams'" :class="navClass('teams')">
                    <i data-lucide="users"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Racers</span>
                </button>
                <button @click="activeTab = 'bracket'" :class="navClass('bracket')">
                    <i data-lucide="list-ordered"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">{{ settings.tournamentType === 'points' ? 'Table' : 'Bracket' }}</span>
                </button>
                <button @click="activeTab = 'history'" :class="navClass('history')">
                    <i data-lucide="medal"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Logs</span>
                </button>
                <button @click="activeTab = 'admin'" :class="navClass('admin')">
                    <i data-lucide="settings"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Control</span>
                </button>
            </div>

            <div class="hidden md:flex items-center gap-6 border-l pl-6 border-slate-200">
                <div class="flex flex-col items-end opacity-70">
                    <span class="text-[10px] font-black text-slate-400 uppercase tracking-tighter">Built By</span>
                    <img src="fasttracktoolslogo.png" alt="FTT" class="h-7 object-contain" onerror="this.src='https://via.placeholder.com/80x20?text=FTT'">
                </div>
                <div v-if="isAdmin" class="text-green-600 flex items-center gap-2 text-[10px] font-black bg-green-50 px-3 py-1.5 rounded-full border border-green-200 uppercase tracking-widest">
                    <i data-lucide="shield-check" class="w-3.5 h-3.5"></i> Director
                </div>
                <button v-else @click="showAdminLogin = true" class="text-[10px] font-black text-slate-400 hover:text-indigo-600 uppercase tracking-widest border border-slate-200 px-3 py-1.5 rounded-full transition-colors">Login</button>
            </div>
        </div>
    </nav>

    <!-- Header -->
    <header class="mb-12 flex flex-col md:flex-row md:items-end md:justify-between gap-8 bg-white p-6 md:p-10 rounded-[2.5rem] md:rounded-[4rem] shadow-sm border border-slate-100 relative overflow-hidden group">
        <div class="absolute top-0 right-0 w-64 h-64 bg-indigo-500/5 rounded-full -mr-32 -mt-32 blur-3xl"></div>
        <div class="flex items-center gap-6 md:gap-10 relative z-10">
            <img src="kwlogo.jpeg" alt="Logo" class="h-24 md:h-36 w-24 md:w-36 object-contain rounded-3xl shadow-2xl border-4 border-white transform group-hover:rotate-2 transition-transform duration-500" onerror="this.src='https://via.placeholder.com/150?text=KW'">
            <div>
                <h1 class="text-4xl md:text-7xl racing-italic tracking-tighter uppercase mb-2 leading-none break-words">{{ settings.tournamentName }}</h1>
                <div class="flex items-center gap-3 text-slate-400 font-black uppercase text-[10px] md:text-sm tracking-[0.2em]">
                    <span class="bg-indigo-600 text-white px-2 py-0.5 rounded text-[10px]">LIVE</span>
                    {{ settings.tournamentType === 'points' ? 'Championship Format' : '2-Game Elimination' }}
                </div>
            </div>
        </div>
        <button v-if="isAdmin && settings.isStarted" @click="showScoringModal = true" class="btn-primary relative z-10 px-8 py-5 md:w-auto w-full group">
            <i data-lucide="zap" class="group-hover:text-yellow-300 transition-colors"></i> Record Result
        </button>
    </header>

    <!-- Content Sections -->
    <div class="min-h-[60vh]">
        <!-- Dashboard Tab -->
        <div v-if="activeTab === 'dashboard'" class="space-y-6">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="stat-card">
                    <div class="p-4 bg-yellow-50 rounded-2xl mb-8 w-fit"><i data-lucide="trophy" class="text-yellow-500"></i></div>
                    <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-[0.3em] mb-2">Prize Pool</h4>
                    <div class="text-3xl font-black racing-italic uppercase text-slate-800">{{ (teams.length * 15000).toLocaleString() }} UPX</div>
                    <p class="text-[10px] text-slate-400 mt-4 font-black uppercase tracking-widest border-t border-slate-50 pt-4 flex justify-between">
                        <span>Winner (2/3):</span> <span class="text-indigo-600 font-black">{{ Math.floor(teams.length * 15000 * (2/3)).toLocaleString() }}</span>
                    </p>
                </div>
                <div class="stat-card">
                    <div class="p-4 bg-blue-50 rounded-2xl mb-8 w-fit"><i data-lucide="flag" class="text-blue-500"></i></div>
                    <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-[0.3em] mb-2">Registered Teams</h4>
                    <div class="text-3xl font-black racing-italic uppercase text-slate-800">{{ teams.length }} Teams</div>
                    <p class="text-[10px] text-slate-400 mt-4 font-black uppercase tracking-widest border-t border-slate-50 pt-4">{{ teams.length * 3 }} Racers Participating</p>
                </div>
                <div class="stat-card">
                    <div class="p-4 bg-green-50 rounded-2xl mb-8 w-fit"><i data-lucide="dollar-sign" class="text-green-500"></i></div>
                    <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-[0.3em] mb-2">Staking Fund</h4>
                    <div class="text-3xl font-black racing-italic uppercase text-slate-800">{{ (teams.length * 250).toLocaleString() }} UPX</div>
                    <p class="text-[10px] text-slate-400 mt-4 font-black uppercase tracking-widest border-t border-slate-50 pt-4">Blockchain Verification Fund</p>
                </div>
            </div>

            <div class="bg-white p-8 rounded-[3rem] shadow-sm border border-slate-100 grid grid-cols-1 md:grid-cols-2 gap-10">
                <div>
                    <h3 class="text-2xl font-black racing-italic uppercase mb-6 text-indigo-600 flex items-center gap-3">
                        <i data-lucide="info"></i> The Rules
                    </h3>
                    <ul class="space-y-4 text-slate-600 font-bold text-sm">
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> 3 members per team, same kart model.</li>
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Objective: Get 2 or 3 teammates in Top 3.</li>
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Bonus: Get all 3 drivers in Top 3 for random prize.</li>
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Dirty tricks & Power-ups allowed. Anything goes!</li>
                    </ul>
                </div>
                <div class="bg-indigo-600 rounded-[2rem] p-8 text-white relative overflow-hidden group">
                    <div class="relative z-10">
                        <h3 class="text-xl font-black uppercase mb-2">Built by FastTrack Tools</h3>
                        <p class="text-indigo-100 text-sm mb-6">Automated Tournament Management for Kart Warz Creators.</p>
                        <img src="fasttracktoolslogo.png" alt="FTT" class="h-16 object-contain transition-transform group-hover:scale-110" onerror="this.src='https://via.placeholder.com/200x60?text=FTT+LOGO'">
                    </div>
                    <div class="absolute -right-10 -bottom-10 p-10 bg-white/10 rounded-full group-hover:bg-white/20 transition-all">
                        <i data-lucide="settings" class="w-20 h-20 rotate-12"></i>
                    </div>
                </div>
            </div>
        </div>

        <!-- Teams Tab -->
        <div v-if="activeTab === 'teams'" class="space-y-8">
            <div v-if="!settings.isStarted" class="bg-white p-8 rounded-[2.5rem] shadow-md border border-slate-200">
                <h3 class="text-2xl font-black racing-italic uppercase mb-6 flex items-center gap-3">
                    <i data-lucide="plus-circle" class="text-indigo-600"></i> New Registration
                </h3>
                <form @submit.prevent="addTeam" class="space-y-6">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div>
                            <label class="block text-[10px] font-black uppercase text-slate-400 mb-2 tracking-widest">Team Name</label>
                            <input v-model="teamForm.name" required class="input-field" placeholder="e.g. Turbo Racers">
                        </div>
                        <div>
                            <label class="block text-[10px] font-black uppercase text-slate-400 mb-2 tracking-widest">Tournament Type</label>
                            <select v-model="settings.tournamentType" class="input-field cursor-pointer">
                                <option value="elimination">2-Game Loss Elimination</option>
                                <option value="points">Point System Championship</option>
                            </select>
                        </div>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                        <div v-for="(m, i) in teamForm.members" :key="i">
                            <label class="block text-[10px] font-black uppercase text-slate-400 mb-2 tracking-widest">{{ i === 0 ? 'Captain' : 'Racer ' + (i+1) }}</label>
                            <input v-model="m.name" required class="input-field" placeholder="Name">
                        </div>
                    </div>
                    <button type="submit" class="btn-primary w-full">Register Team (15k UPX Entry)</button>
                </form>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <div v-for="team in teams" :key="team.id" class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 relative group transition-all hover:shadow-lg">
                    <div class="flex justify-between items-start mb-4">
                        <h4 class="font-black text-xl italic uppercase">{{ team.name }}</h4>
                        <button v-if="isAdmin && !settings.isStarted" @click="deleteTeam(team.id)" class="text-slate-300 hover:text-red-500 transition-colors"><i data-lucide="trash-2"></i></button>
                    </div>
                    <div class="space-y-2 mb-6">
                        <div v-for="m in team.members" :key="m.name" class="flex items-center gap-2 text-sm font-bold text-slate-700">
                            <i v-if="m.isCaptain" data-lucide="shield-check" class="w-4 h-4 text-yellow-500"></i>
                            <i v-else data-lucide="activity" class="w-4 h-4 text-slate-300"></i>
                            <span>{{ m.name }}</span>
                        </div>
                    </div>
                    <div class="pt-4 border-t border-slate-50 flex justify-between items-center text-[10px] font-black uppercase tracking-widest">
                        <div v-if="settings.tournamentType === 'points'">
                            PTS: <span class="text-indigo-600 text-lg">{{ team.points }}</span>
                        </div>
                        <div v-else>
                            LOSSES: <span class="text-red-500 text-lg">{{ team.losses }}/2</span>
                        </div>
                        <div :class="team.status === 'active' ? 'text-green-500 font-black' : 'text-red-500 font-black italic'">{{ team.status }}</div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Bracket Tab -->
        <div v-if="activeTab === 'bracket'" class="animate-in fade-in zoom-in-95 duration-300">
            <div v-if="!settings.isStarted" class="text-center py-20 bg-white rounded-[3rem] border-2 border-dashed border-slate-200">
                <img src="kwlogo.jpeg" class="h-40 mx-auto mb-8 grayscale opacity-50" onerror="this.style.display='none'">
                <h3 class="text-3xl font-black racing-italic uppercase text-slate-300">Standby for Green Flag...</h3>
                <button v-if="isAdmin" @click="startTournament" class="btn-primary px-12 mt-8 mx-auto">Launch Official Season</button>
            </div>
            
            <div v-else-if="settings.tournamentType === 'points'" class="bg-white rounded-[3rem] shadow-xl overflow-hidden border">
                <table class="w-full text-left">
                    <thead class="bg-slate-50">
                        <tr class="text-[10px] font-black uppercase text-slate-400">
                            <th class="p-6">Rank</th>
                            <th class="p-6">Team</th>
                            <th class="p-6 text-center">Sweeps</th>
                            <th class="p-6 text-right">Points</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y">
                        <tr v-for="(team, idx) in sortedTeams" :key="team.id" :class="idx === 0 ? 'bg-yellow-50/50' : ''">
                            <td class="p-6 text-3xl font-black italic text-slate-300">#{{ idx + 1 }}</td>
                            <td class="p-6">
                                <div class="font-black text-xl italic uppercase tracking-tighter">{{ team.name }}</div>
                                <div class="text-[10px] text-slate-400 font-bold uppercase tracking-widest">{{ team.members.map(m => m.name).join(' • ') }}</div>
                            </td>
                            <td class="p-6 text-center">
                                <span class="bg-indigo-100 text-indigo-600 px-3 py-1 rounded-full font-black text-xs">{{ team.sweeps }}</span>
                            </td>
                            <td class="p-6 text-right text-4xl font-black italic text-indigo-600">{{ team.points }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div v-else class="space-y-12 pb-20">
                <h3 class="text-3xl font-black racing-italic uppercase text-indigo-600">Elimination Brackets</h3>
                <div class="bg-white p-12 rounded-[3rem] border shadow-sm text-center">
                    <p class="text-slate-400 font-black uppercase tracking-widest italic">Bracket visualizer active. Use 'Record Result' to advance teams.</p>
                    <div class="mt-8 flex justify-center gap-10">
                        <div class="text-center">
                            <div class="text-[10px] font-black uppercase text-slate-400 mb-1">Winners Bracket</div>
                            <div class="text-3xl font-black italic text-green-500">{{ teams.filter(t => t.losses === 0).length }} Active</div>
                        </div>
                        <div class="text-center">
                            <div class="text-[10px] font-black uppercase text-slate-400 mb-1">Losers Bracket</div>
                            <div class="text-3xl font-black italic text-red-500">{{ teams.filter(t => t.losses === 1).length }} Active</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- History Tab -->
        <div v-if="activeTab === 'history'" class="space-y-4">
            <h3 class="text-2xl font-black italic uppercase tracking-widest mb-6">Race Archives</h3>
            <div v-if="history.length === 0" class="p-12 text-center text-slate-400 italic font-bold bg-white rounded-3xl">Waiting for the first green flag...</div>
            <div v-for="item in history" :key="item.id" class="bg-white p-6 rounded-3xl flex justify-between items-center shadow-sm border border-slate-100 group hover:border-indigo-500/50 transition-all duration-300">
                <div class="flex items-center gap-6">
                    <div :class="['p-4 rounded-2xl transition-transform group-hover:scale-110', item.type === 'SWEEP' ? 'bg-yellow-100 text-yellow-600' : item.type === 'WIN' ? 'bg-green-100 text-green-600' : 'bg-red-100 text-red-600']">
                        <i v-if="item.type === 'SWEEP'" data-lucide="zap"></i>
                        <i v-else-if="item.type === 'WIN'" data-lucide="check-circle"></i>
                        <i v-else data-lucide="alert-circle"></i>
                    </div>
                    <div>
                        <div class="font-black text-2xl italic uppercase leading-none mb-1">{{ item.teamName }}</div>
                        <div class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em]">{{ item.type }} {{ item.pts ? '| +' + item.pts + ' PTS' : '' }}</div>
                    </div>
                </div>
                <div class="text-[10px] font-black uppercase bg-slate-50 px-4 py-2 rounded-full border border-slate-100">{{ item.time }}</div>
            </div>
        </div>

        <!-- Admin Tab -->
        <div v-if="activeTab === 'admin'" class="max-w-2xl mx-auto space-y-8">
            <div class="bg-white p-10 rounded-[3rem] border shadow-xl">
                <h3 class="text-2xl font-black uppercase italic border-b pb-6 mb-10 tracking-tighter">Director Console</h3>
                <div class="space-y-8">
                    <div>
                        <label class="block text-[10px] font-black text-slate-400 uppercase tracking-widest mb-2">Tournament Display Name</label>
                        <input v-model="settings.tournamentName" @change="save" class="w-full p-4 border rounded-2xl font-black text-2xl italic bg-slate-50 focus:border-indigo-600 outline-none transition-all">
                    </div>
                    <div class="grid grid-cols-2 gap-4">
                        <button @click="exportData" class="bg-black text-white p-5 rounded-2xl font-black uppercase tracking-widest shadow-xl hover:bg-slate-900 transition-all">Export CSV</button>
                        <button v-if="isAdmin" @click="resetScores" class="border-4 border-red-50 text-red-500 p-5 rounded-2xl font-black uppercase tracking-widest hover:bg-red-500 hover:text-white transition-all">Reset Season</button>
                    </div>
                </div>
            </div>

            <div class="bg-indigo-600 p-10 rounded-[3rem] text-white flex flex-col items-center text-center gap-6 relative overflow-hidden group">
                <img src="fasttracktoolslogo.png" alt="FTT" class="h-20 object-contain drop-shadow-xl relative z-10 transition-transform group-hover:scale-110" onerror="this.src='https://via.placeholder.com/200x60?text=FTT'">
                <div class="relative z-10">
                    <p class="text-[10px] font-black uppercase tracking-[0.4em] mb-2 opacity-70">Development Engine</p>
                    <p class="text-xl font-black uppercase">FastTrack Tools Terminal</p>
                    <p class="text-xs opacity-60 mt-2 font-medium">Independent Offline Tournament Architecture</p>
                </div>
                <div class="absolute inset-0 bg-white/5 opacity-0 group-hover:opacity-100 transition-opacity pointer-events-none"></div>
            </div>

            <button v-if="!isAdmin" @click="showAdminLogin = true" class="w-full p-8 bg-white rounded-[2.5rem] font-black uppercase tracking-[0.3em] text-indigo-600 border-4 border-double border-indigo-100 shadow-xl hover:scale-[1.02] transition-all">Unlock Director Panel</button>
        </div>
    </div>

    <!-- Modals -->
    <!-- Login Modal -->
    <div v-if="showAdminLogin" class="fixed inset-0 bg-black/95 backdrop-blur-2xl z-[110] flex items-center justify-center p-4">
        <div class="bg-white p-10 md:p-16 rounded-[3rem] shadow-2xl w-full max-w-lg border text-center relative overflow-hidden">
            <div class="absolute top-0 left-0 w-full h-2 bg-indigo-600"></div>
            <div class="mb-10 flex justify-center">
                <img src="kwlogo.jpeg" class="h-40 w-40 object-contain rounded-3xl shadow-2xl rotate-2 border-4 border-slate-50" onerror="this.src='https://via.placeholder.com/150?text=KW'">
            </div>
            <h2 class="text-4xl font-black racing-italic uppercase mb-2 tracking-tighter">Director Access</h2>
            <p class="text-xs font-black text-slate-400 uppercase tracking-widest mb-12">Authorization Required</p>
            <input type="password" v-model="adminInput" @keyup.enter="verifyAdmin" class="w-full p-8 mb-10 border-4 border-slate-100 bg-slate-50 rounded-3xl text-center text-5xl tracking-[0.6em] font-black focus:border-indigo-600 outline-none transition-all shadow-inner" placeholder="••••">
            <div class="flex gap-4">
                <button @click="showAdminLogin = false" class="flex-1 py-5 text-slate-400 font-black uppercase tracking-widest hover:text-slate-600 transition-colors">Cancel</button>
                <button @click="verifyAdmin" class="flex-1 py-5 bg-indigo-600 text-white font-black uppercase tracking-widest rounded-2xl shadow-xl shadow-indigo-500/40 hover:bg-indigo-700 transition-all">Authorize</button>
            </div>
        </div>
    </div>

    <!-- Scoring Modal -->
    <div v-if="showScoringModal" class="fixed inset-0 bg-black/90 backdrop-blur-xl z-[100] flex items-center justify-center p-4">
        <div class="bg-white p-8 rounded-[2.5rem] shadow-2xl w-full max-w-2xl max-h-[90vh] overflow-y-auto">
            <div class="flex justify-between items-center mb-8 border-b pb-4">
                <h2 class="text-3xl font-black racing-italic uppercase italic">Race Result</h2>
                <button @click="showScoringModal = false" class="p-3 bg-slate-100 hover:bg-red-500 hover:text-white rounded-full transition-all"><i data-lucide="x"></i></button>
            </div>
            <div class="space-y-10">
                <div>
                    <label class="block text-xs font-black text-indigo-500 uppercase tracking-widest mb-6 text-center">Victorious Team</label>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div v-for="t in activeTeams" :key="t.id" class="p-5 border-2 rounded-3xl bg-slate-50 hover:border-indigo-500 transition-all group">
                            <div class="font-black uppercase italic mb-3 text-lg">{{ t.name }}</div>
                            <div class="flex gap-2">
                                <button @click="recordResult(t.id, 'WIN')" class="flex-1 bg-white hover:bg-indigo-600 hover:text-white py-3 rounded-xl text-[10px] font-black uppercase shadow-sm transition-all">Standard Win</button>
                                <button @click="recordResult(t.id, 'SWEEP')" class="flex-1 bg-yellow-400 hover:bg-yellow-500 text-yellow-900 py-3 rounded-xl text-[10px] font-black uppercase shadow-sm transition-all">SWEEP!</button>
                            </div>
                        </div>
                    </div>
                </div>
                <div v-if="settings.tournamentType === 'elimination'" class="pt-8 border-t">
                    <label class="block text-xs font-black text-red-500 uppercase tracking-widest mb-6 text-center">Record Team Loss</label>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <button v-for="t in activeTeams" :key="t.id" @click="recordLoss(t.id)" class="p-5 border-2 border-red-50 text-red-500 rounded-3xl text-left hover:bg-red-600 hover:text-white transition-all font-black uppercase italic group">
                            {{ t.name }} <span class="text-[10px] block opacity-60">Add Loss #{{ t.losses + 1 }}</span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
    const { createApp, ref, onMounted, computed, watch, nextTick } = Vue;

    createApp({
        setup() {
            const activeTab = ref('dashboard');
            const isAdmin = ref(false);
            const adminInput = ref('');
            const showAdminLogin = ref(false);
            const showScoringModal = ref(false);
            
            const teams = ref([]);
            const history = ref([]);
            const teamForm = ref({ 
                name: '', 
                members: [
                    { name: '', isCaptain: true }, 
                    { name: '', isCaptain: false }, 
                    { name: '', isCaptain: false }
                ] 
            });
            const settings = ref({
                tournamentName: "Kart Warz Season 1",
                adminKey: "jah324",
                isStarted: false,
                tournamentType: 'elimination'
            });

            const activeTeams = computed(() => teams.value.filter(t => t.status === 'active'));
            const sortedTeams = computed(() => [...teams.value].sort((a, b) => b.points - a.points || b.sweeps - a.sweeps));

            const navClass = (tab) => {
                const isActive = activeTab.value === tab;
                return `flex flex-col md:flex-row items-center gap-1 md:gap-4 px-4 py-2 rounded-xl transition-all duration-300 relative ${isActive ? 'text-indigo-600 bg-indigo-50 font-black' : 'text-slate-400 font-bold hover:text-slate-900'}`;
            };

            const addTeam = () => {
                teams.value.push({
                    id: Date.now(),
                    name: teamForm.value.name,
                    members: JSON.parse(JSON.stringify(teamForm.value.members)),
                    points: 0,
                    losses: 0,
                    sweeps: 0,
                    status: 'active'
                });
                teamForm.value.name = '';
                teamForm.value.members.forEach(m => m.name = '');
                save();
            };

            const deleteTeam = (id) => {
                teams.value = teams.value.filter(t => t.id !== id);
                save();
            };

            const recordResult = (id, type) => {
                const team = teams.value.find(t => t.id === id);
                if (!team) return;

                const pts = type === 'SWEEP' ? 3 : 1;
                
                if (settings.value.tournamentType === 'points') {
                    team.points += pts;
                    if (type === 'SWEEP') team.sweeps += 1;
                }

                history.value.unshift({
                    id: Date.now(),
                    teamName: team.name,
                    type,
                    pts: settings.value.tournamentType === 'points' ? pts : null,
                    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
                });

                showScoringModal.value = false;
                save();
            };

            const recordLoss = (id) => {
                const team = teams.value.find(t => t.id === id);
                if (!team) return;

                team.losses += 1;
                if (team.losses >= 2) team.status = 'eliminated';
                
                history.value.unshift({
                    id: Date.now(),
                    teamName: team.name,
                    type: 'LOSS',
                    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
                });

                showScoringModal.value = false;
                save();
            };

            const startTournament = () => {
                if (teams.value.length < 2) return alert('Register at least 2 teams to begin');
                settings.value.isStarted = true;
                save();
            };

            const verifyAdmin = () => {
                if (adminInput.value === settings.value.adminKey) {
                    isAdmin.value = true;
                    showAdminLogin.value = false;
                    adminInput.value = '';
                } else {
                    alert('Invalid Key - Access Denied');
                }
            };

            const resetScores = () => {
                if (!confirm('WARNING: This will wipe all current scores and race history. Registered teams will be kept. Continue?')) return;
                teams.value.forEach(t => {
                    t.points = 0; t.losses = 0; t.sweeps = 0; t.status = 'active';
                });
                history.value = [];
                settings.value.isStarted = false;
                save();
            };

            const exportData = () => {
                const headers = 'Time,Team,Type,Points\n';
                const rows = history.value.map(h => [h.time, `"${h.teamName}"`, h.type, h.pts || ''].join(','));
                const csv = headers + rows.join('\n');
                const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `kart_warz_log_${new Date().toISOString().split('T')[0]}.csv`;
                a.click();
                URL.revokeObjectURL(url);
            };

            const save = () => {
                localStorage.setItem('kw_data', JSON.stringify({
                    teams: teams.value,
                    history: history.value,
                    settings: settings.value
                }));
            };

            onMounted(() => {
                const saved = localStorage.getItem('kw_data');
                if (saved) {
                    try {
                        const parsed = JSON.parse(saved);
                        teams.value = parsed.teams || [];
                        history.value = parsed.history || [];
                        settings.value = { ...settings.value, ...parsed.settings };
                    } catch (e) {
                        console.error("Data load error:", e);
                    }
                }
                nextTick(() => lucide.createIcons());
            });

            // Watch for changes to ensure Icons are always refreshed
            watch([activeTab, showAdminLogin, showScoringModal, teams], () => {
                nextTick(() => lucide.createIcons());
            }, { deep: true });

            return {
                activeTab, isAdmin, adminInput, showAdminLogin, showScoringModal, teams, history, settings, teamForm,
                activeTeams, sortedTeams,
                navClass, addTeam, deleteTeam, verifyAdmin, startTournament, recordResult, recordLoss, resetScores, exportData, save
            }
        }
    }).mount('#app');
</script>

</body><img width="1024" height="1024" alt="fasttracktoolslogo" src="https://github.com/user-attachments/assets/89864691-01e3-4b0a-8139-2c78cc1131bc" />

</html>![kwlogo](https://github.com/user-attachments/assets/5a57b06b-3a84-4f6a-aa79-80b0a9a7c49a)
