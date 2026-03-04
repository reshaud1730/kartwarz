# kartwarz
go-kart team racing management app
[KartWarz.html](https://github.com/user-attachments/files/25736170/KartWarz.html)
<!DOCTYPE html<img w<img width="1024" height="1536" alt="S E A  WARFLEET" src="https://github.com/user-attachments/assets/6a<img width="1024" height="1024" alt="RAYTOWN REBELZ" src="https://github.com/user-attachments/assets/4f4<img width="1002" height="742" alt="neo vmax kartwarz" src="https://github.com/user-attachments/assets/236e20dd-ec7c-4df3-a33a-1274bce08339" />
719b0-8290-4ed4-bd41-8c648527107b" />
754dcc-a74e-4276-9852-c0ad4a73770b" />
idth="1024" height="1024" alt="fasttracktoolslogo" src="https://github.com/user-attachments/assets/7d49b8cc-97e8-4471-aa98-2b3f20857b67" />
>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta![kwlogo](https://github.com/user-attachments/assets/2015b223-6633-4756-9650-03b29b05c4c8)
 name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- Primary Meta Tags -->
    <title>Kart Warz Manager | FastTrack Tools</title>
    <meta name="title" content="Kart Warz Manager | FastTrack Tools">
    <meta name="description" content="The official tournament engine for Kart Warz. Track teams, points, and race brackets live.">

    <!-- Open Graph / Facebook / Discord -->
    <meta property="og:type" content="website">
    <meta property="og:title" content="Kart Warz Tournament Manager">
    <meta property="og:description" content="Live tournament tracking, brackets, and point systems for Kart Warz racers.">
    <meta property="og:image" content="kwlogo.jpeg">

    <!-- Discord Embedded App SDK -->
    <script src="https://cdn.jsdelivr.net/npm/@discord/embedded-app-sdk@1.0.0/dist/index.global.js"></script>
    
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

        i[data-lucide] {
            display: inline-block;
            width: 20px;
            height: 20px;
        }

        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #6366f1; border-radius: 10px; }

        .sweep-glow {
            box-shadow: 0 0 20px rgba(234, 179, 8, 0.3);
            animation: pulse-glow 2s infinite;
        }

        @keyframes pulse-glow {
            0% { box-shadow: 0 0 10px rgba(234, 179, 8, 0.2); }
            50% { box-shadow: 0 0 25px rgba(234, 179, 8, 0.5); }
            100% { box-shadow: 0 0 10px rgba(234, 179, 8, 0.2); }
        }

        .team-logo-container {
            @apply rounded-2xl overflow-hidden bg-slate-50 border border-slate-200 flex items-center justify-center shrink-0 relative;
        }
        .team-logo-img { @apply w-full h-full object-cover relative z-10; }
        .team-logo-fallback { @apply absolute inset-0 flex items-center justify-center bg-slate-50 text-slate-300 z-0; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen pb-24 md:pb-12 md:pt-20">

<div id="app" v-cloak class="max-w-7xl mx-auto p-4 md:p-8">
    <!-- Navigation -->
    <nav class="fixed bottom-0 left-0 right-0 md:top-0 md:bottom-auto glass border-t md:border-t-0 md:border-b border-slate-200 z-50">
        <div class="max-w-7xl mx-auto flex items-center justify-between h-20 px-4 md:px-6">
            <div class="flex items-center gap-3">
                <img src="kwlogo.jpeg" alt="KW" class="h-10 w-10 object-cover rounded-xl shadow-lg border-2 border-white" onerror="this.src='https://via.placeholder.com/40?text=KW'">
                <div class="hidden lg:flex items-center gap-2 racing-italic text-2xl tracking-tighter text-indigo-600 uppercase leading-none">
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
        
        <div v-if="isAdmin && settings.isStarted" class="relative z-20">
            <button @click="showScoringModal = true" class="btn-primary px-10 py-6 md:w-auto w-full group border-4 border-white shadow-2xl hover:scale-110">
                <i data-lucide="zap" class="group-hover:text-yellow-300 transition-colors w-6 h-6"></i> 
                <span class="text-xl racing-italic uppercase tracking-tighter">Record Race Result</span>
            </button>
        </div>
        
        <div v-else-if="isAdmin && !settings.isStarted" class="relative z-10 bg-indigo-50 border border-indigo-100 p-6 rounded-[2rem] text-center md:w-80 shadow-inner">
             <p class="text-[10px] font-black uppercase tracking-[0.2em] text-indigo-400 mb-2 leading-tight">Director Notification</p>
             <p class="text-xs font-bold text-indigo-600 uppercase italic">Go to "Table/Bracket" and click "Launch Event" to start scoring.</p>
        </div>
    </header>

    <!-- Content Sections -->
    <div class="min-h-[60vh]">
        <!-- Dashboard Tab -->
        <div v-if="activeTab === 'dashboard'" class="space-y-6 animate-in fade-in duration-300">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="stat-card">
                    <div class="p-4 bg-yellow-50 rounded-2xl mb-8 w-fit"><i data-lucide="trophy" class="text-yellow-500"></i></div>
                    <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-[0.3em] mb-2">Total Prize Pool</h4>
                    <div class="text-3xl font-black racing-italic uppercase text-slate-800">{{ (teams.length * settings.entryFee).toLocaleString() }} UPX</div>
                    <p class="text-[10px] text-slate-400 mt-4 font-black uppercase tracking-widest border-t border-slate-50 pt-4 flex justify-between">
                        <span>Winner (2/3):</span> <span class="text-indigo-600 font-black">{{ Math.floor(teams.length * settings.entryFee * (2/3)).toLocaleString() }}</span>
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
                    <p class="text-[10px] text-slate-400 mt-4 font-black uppercase tracking-widest border-t border-slate-50 pt-4">Blockchain Recording</p>
                </div>
            </div>

            <div class="bg-white p-8 rounded-[3rem] shadow-sm border border-slate-100 grid grid-cols-1 md:grid-cols-2 gap-10">
                <div>
                    <h3 class="text-2xl font-black racing-italic uppercase mb-6 text-indigo-600 flex items-center gap-3">
                        <i data-lucide="info"></i> Event Directives
                    </h3>
                    <ul class="space-y-4 text-slate-600 font-bold text-sm uppercase tracking-tight">
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Entry Fee: {{ settings.entryFee.toLocaleString() }} UPX per Team.</li>
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Team Requirement: 3 members, same kart model.</li>
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Win: Get 2 or 3 teammates in the Top 3 finishes.</li>
                        <li class="flex gap-3"><i data-lucide="check-circle" class="text-green-500 shrink-0" size="18"></i> Sweep: Get all 3 teammates into Top 3 finishes.</li>
                    </ul>
                </div>
                <div class="bg-indigo-600 rounded-[2rem] p-8 text-white relative overflow-hidden group">
                    <div class="relative z-10">
                        <h3 class="text-xl font-black uppercase mb-2">Built by FastTrack Tools</h3>
                        <p class="text-indigo-100 text-sm mb-6 font-medium">Tournament Management Engine.</p>
                        <img src="fasttracktoolslogo.png" alt="FTT" class="h-16 object-contain transition-transform group-hover:scale-110" onerror="this.src='https://via.placeholder.com/200x60?text=FTT+LOGO'">
                    </div>
                    <div class="absolute -right-10 -bottom-10 p-10 bg-white/10 rounded-full group-hover:bg-white/20 transition-all">
                        <i data-lucide="settings" class="w-20 h-20 rotate-12"></i>
                    </div>
                </div>
            </div>
        </div>

        <!-- Racers Tab -->
        <div v-if="activeTab === 'teams'" class="space-y-8 animate-in fade-in duration-300">
            <!-- Registration Form -->
            <div v-if="!settings.isStarted" class="bg-white p-8 rounded-[2.5rem] shadow-md border border-slate-200">
                <div class="flex justify-between items-center mb-6 text-indigo-600">
                    <h3 class="text-2xl font-black racing-italic uppercase flex items-center gap-3 leading-tight">
                        <i data-lucide="plus-circle"></i> New Team Entry
                    </h3>
                    <div class="bg-slate-100 px-4 py-2 rounded-xl text-[10px] font-black uppercase tracking-widest text-slate-500">
                        Fee: {{ settings.entryFee.toLocaleString() }} UPX
                    </div>
                </div>
                <form @submit.prevent="addTeam" class="space-y-6">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div>
                            <label class="block text-[10px] font-black uppercase text-slate-400 mb-2 tracking-widest">Team Name</label>
                            <input v-model="teamForm.name" required class="input-field" placeholder="e.g. Speed Demons">
                            <p class="text-[9px] text-indigo-500 mt-2 font-black uppercase tracking-widest leading-none">Team Logo should be: "{{ teamForm.name || 'TeamName' }}.png"</p>
                        </div>
                        <div>
                            <label class="block text-[10px] font-black uppercase text-slate-400 mb-2 tracking-widest">Scoring Style</label>
                            <div class="p-4 bg-slate-50 border rounded-2xl font-black uppercase italic text-indigo-600">
                                {{ settings.tournamentType === 'points' ? 'Point Championship' : 'Elimination' }}
                            </div>
                        </div>
                    </div>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                        <div v-for="(m, i) in teamForm.members" :key="i">
                            <label class="block text-[10px] font-black uppercase text-slate-400 mb-2 tracking-widest">{{ i === 0 ? 'Team Captain' : 'Racer ' + (i+1) }}</label>
                            <input v-model="m.name" required class="input-field" placeholder="Name">
                        </div>
                    </div>
                    <button type="submit" class="btn-primary w-full text-lg">Confirm Registration ({{ settings.entryFee.toLocaleString() }} UPX)</button>
                </form>
            </div>

            <!-- Team Cards List -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <div v-for="team in teams" :key="team.id" class="bg-white p-6 md:p-8 rounded-[3rem] shadow-sm border border-slate-100 relative group transition-all hover:shadow-lg overflow-hidden">
                    
                    <!-- Top Header: Name and Control -->
                    <div class="flex justify-between items-start mb-6">
                        <div class="flex-1 min-w-0">
                            <h4 class="font-black text-2xl italic uppercase tracking-tighter leading-none break-words mb-1">{{ team.name }}</h4>
                            <div :class="team.status === 'active' ? 'text-green-500' : 'text-red-500'" class="text-[10px] font-black uppercase tracking-[0.2em] italic">{{ team.status }}</div>
                        </div>
                        <button v-if="isAdmin && !settings.isStarted" @click="deleteTeam(team.id)" class="text-slate-200 hover:text-red-500 transition-colors p-1"><i data-lucide="trash-2" size="18"></i></button>
                    </div>

                    <!-- Content Row: Logo and Names Side-by-Side -->
                    <div class="flex gap-6 items-center mb-8">
                        <!-- THE TEAM LOGO (Under the team name) -->
                        <div class="team-logo-container border-indigo-100 w-24 h-24 shadow-sm">
                            <img :src="team.name + '.png'" class="team-logo-img" @error="handleLogoError">
                            <div class="team-logo-fallback"><i data-lucide="users" size="32"></i></div>
                        </div>
                        
                        <!-- Racers List (Ensuring full visibility) -->
                        <div class="flex-1 space-y-3">
                            <div v-for="m in team.members" :key="m.name" class="flex items-center gap-2 text-sm font-bold text-slate-700 leading-tight">
                                <i v-if="m.isCaptain" data-lucide="shield-check" class="w-4 h-4 text-yellow-500 shrink-0"></i>
                                <i v-else data-lucide="activity" class="w-4 h-4 text-slate-200 shrink-0"></i>
                                <span class="break-words">{{ m.name }}</span>
                            </div>
                        </div>
                    </div>

                    <!-- Footer: Status -->
                    <div class="pt-5 border-t border-slate-50 flex justify-between items-center text-[10px] font-black uppercase tracking-[0.2em] italic">
                        <div v-if="settings.tournamentType === 'points'">
                            SCORE: <span class="text-indigo-600 text-lg">{{ team.points }} PTS</span>
                        </div>
                        <div v-else>
                            STRIKES: <span class="text-red-500 text-lg font-black">{{ team.losses }}/2</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Table/Bracket Tab -->
        <div v-if="activeTab === 'bracket'" class="animate-in fade-in zoom-in-95 duration-300">
            <div v-if="!settings.isStarted" class="text-center py-20 bg-white rounded-[3rem] border-2 border-dashed border-slate-200">
                <img src="kwlogo.jpeg" class="h-40 mx-auto mb-8 grayscale opacity-50" onerror="this.style.display='none'">
                <h3 class="text-4xl font-black racing-italic uppercase text-slate-300 italic">Standby for Green Flag...</h3>
                <button v-if="isAdmin" @click="startTournament" class="btn-primary px-16 py-6 text-xl mx-auto shadow-indigo-500/50 mt-10">
                    <i data-lucide="rocket"></i> Launch Official Season
                </button>
            </div>
            
            <div v-else-if="settings.tournamentType === 'points'" class="bg-white rounded-[3rem] shadow-xl overflow-hidden border border-slate-200">
                <table class="w-full text-left border-collapse">
                    <thead class="bg-slate-50">
                        <tr class="text-[10px] font-black uppercase text-slate-400 border-b border-slate-100">
                            <th class="p-6">Rank</th>
                            <th class="p-6">Team</th>
                            <th class="p-6 text-center">Sweeps</th>
                            <th class="p-6 text-right">Total Points</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-50">
                        <tr v-for="(team, idx) in sortedTeams" :key="team.id" :class="idx === 0 ? 'bg-yellow-50/50' : ''">
                            <td class="p-6 text-3xl font-black italic text-slate-300">#{{ idx + 1 }}</td>
                            <td class="p-6">
                                <div class="font-black text-xl italic uppercase tracking-tighter leading-tight">{{ team.name }}</div>
                                <div class="text-[10px] text-slate-400 font-black uppercase tracking-widest">Captain: {{ team.members[0].name }}</div>
                            </td>
                            <td class="p-6 text-center">
                                <span class="bg-indigo-100 text-indigo-600 px-4 py-1.5 rounded-full font-black text-xs">{{ team.sweeps }}</span>
                            </td>
                            <td class="p-6 text-right text-4xl font-black italic text-indigo-600">{{ team.points }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div v-else class="space-y-12 pb-20">
                <h3 class="text-3xl font-black racing-italic uppercase text-indigo-600 italic">Live Standings</h3>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div class="bg-white p-8 rounded-[3rem] border shadow-sm border-slate-100">
                        <h4 class="text-xl font-black uppercase tracking-tighter italic mb-6 text-green-500 flex items-center gap-3">
                            <i data-lucide="check-circle" size="20"></i> Winners Bracket
                        </h4>
                        <div class="space-y-3">
                            <div v-for="t in teams.filter(team => team.losses === 0)" :key="t.id" class="p-5 bg-slate-50 rounded-2xl font-black uppercase italic border-l-8 border-green-500 shadow-sm text-lg">{{ t.name }}</div>
                        </div>
                    </div>
                    <div class="bg-white p-8 rounded-[3rem] border shadow-sm border-slate-100">
                        <h4 class="text-xl font-black uppercase tracking-tighter italic mb-6 text-red-500 flex items-center gap-3">
                            <i data-lucide="alert-circle" size="20"></i> Losers Bracket
                        </h4>
                        <div class="space-y-3">
                            <div v-for="t in teams.filter(team => team.losses === 1)" :key="t.id" class="p-5 bg-slate-50 rounded-2xl font-black uppercase italic border-l-8 border-red-500 shadow-sm text-lg">{{ t.name }}</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- History Tab -->
        <div v-if="activeTab === 'history'" class="space-y-4 animate-in fade-in duration-300">
            <h3 class="text-2xl font-black italic uppercase tracking-widest mb-6 italic">Race Official Archives</h3>
            <div v-if="history.length === 0" class="p-12 text-center text-slate-400 italic font-black uppercase tracking-widest bg-white rounded-3xl border border-dashed">Lobby empty. Waiting for green flag...</div>
            <div v-for="item in history" :key="item.id" class="bg-white p-6 rounded-3xl flex justify-between items-center shadow-sm border border-slate-100 group hover:border-indigo-500/50 transition-all duration-300">
                <div class="flex items-center gap-6">
                    <div :class="['p-4 rounded-2xl shadow-lg', item.type === 'SWEEP' ? 'bg-yellow-100 text-yellow-600' : item.type === 'WIN' ? 'bg-green-100 text-green-600' : 'bg-red-100 text-red-600']">
                        <i v-if="item.type === 'SWEEP'" data-lucide="zap"></i>
                        <i v-else-if="item.type === 'WIN'" data-lucide="check-circle"></i>
                        <i v-else data-lucide="alert-circle"></i>
                    </div>
                    <div>
                        <div class="font-black text-2xl italic uppercase leading-none mb-1 tracking-tighter">{{ item.teamName }}</div>
                        <div class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em]">{{ item.type }} {{ item.pts ? '| +' + item.pts + ' PTS' : '' }}</div>
                    </div>
                </div>
                <div class="text-[10px] font-black uppercase bg-slate-50 px-4 py-2 rounded-full border border-slate-100 text-slate-400">{{ item.time }}</div>
            </div>
        </div>

        <!-- Control Tab -->
        <div v-if="activeTab === 'admin'" class="max-w-2xl mx-auto space-y-8 animate-in fade-in duration-300">
            <div class="bg-white p-10 rounded-[3rem] border shadow-xl border-slate-200">
                <h3 class="text-2xl font-black uppercase italic border-b pb-6 mb-10 tracking-tighter leading-none">Event Configuration</h3>
                <div class="space-y-10">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div>
                            <label class="block text-[10px] font-black text-slate-400 uppercase tracking-widest mb-2">Tournament Name</label>
                            <input v-model="settings.tournamentName" @change="save" class="input-field italic">
                        </div>
                        <div>
                            <label class="block text-[10px] font-black text-slate-400 uppercase tracking-widest mb-2">Entry Fee (UPX)</label>
                            <input type="number" v-model.number="settings.entryFee" @change="save" class="input-field text-indigo-600">
                        </div>
                    </div>
                    <div>
                        <label class="block text-[10px] font-black text-slate-400 uppercase tracking-widest mb-2">Active Scoring Model</label>
                        <select v-model="settings.tournamentType" @change="save" class="input-field cursor-pointer uppercase tracking-widest">
                            <option value="elimination">2-Loss Elimination (Default)</option>
                            <option value="points">Point Championship</option>
                        </select>
                    </div>
                    <div class="grid grid-cols-2 gap-4 pt-6 border-t border-slate-100">
                        <button @click="exportData" class="bg-black text-white p-5 rounded-2xl font-black uppercase tracking-widest shadow-xl hover:bg-slate-900 transition-all">Export Logs</button>
                        <button v-if="isAdmin" @click="resetScores" class="border-4 border-red-50 text-red-500 p-5 rounded-2xl font-black uppercase tracking-widest hover:bg-red-500 hover:text-white transition-all shadow-xl">Reset Season</button>
                    </div>
                </div>
            </div>

            <div class="bg-indigo-600 p-10 rounded-[3rem] text-white flex flex-col items-center text-center gap-6 relative overflow-hidden group shadow-2xl">
                <img src="fasttracktoolslogo.png" alt="FTT" class="h-20 object-contain drop-shadow-xl relative z-10 transition-transform group-hover:scale-110" onerror="this.src='https://via.placeholder.com/200x60?text=FTT'">
                <div class="relative z-10">
                    <p class="text-[10px] font-black uppercase tracking-[0.4em] mb-2 opacity-70">Development Engine</p>
                    <p class="text-xl font-black uppercase italic tracking-tighter leading-tight">FastTrack Tools Terminal</p>
                    <p class="text-xs opacity-60 mt-2 font-medium italic">Powered by Racing Automation</p>
                </div>
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
            <h2 class="text-4xl font-black racing-italic uppercase mb-2 tracking-tighter italic leading-none">Director Access</h2>
            <p class="text-xs font-black text-slate-400 uppercase tracking-widest mb-12 italic leading-none">Security Identity Verification</p>
            <input type="password" v-model="adminInput" @keyup.enter="verifyAdmin" class="w-full p-8 mb-10 border-4 border-slate-100 bg-slate-50 rounded-3xl text-center text-5xl tracking-[0.6em] font-black focus:border-indigo-600 outline-none transition-all shadow-inner" placeholder="••••">
            <div class="flex gap-4">
                <button @click="showAdminLogin = false" class="flex-1 py-5 text-slate-400 font-black uppercase tracking-widest hover:text-slate-600 transition-colors">Cancel</button>
                <button @click="verifyAdmin" class="flex-1 py-5 bg-indigo-600 text-white font-black uppercase tracking-widest rounded-2xl shadow-xl shadow-indigo-500/40 hover:bg-indigo-700 transition-all">Authorize</button>
            </div>
        </div>
    </div>

    <!-- Scoring Modal -->
    <div v-if="showScoringModal" class="fixed inset-0 bg-black/90 backdrop-blur-xl z-[100] flex items-center justify-center p-4 animate-in fade-in zoom-in-95 duration-200">
        <div class="bg-white p-8 rounded-[2.5rem] shadow-2xl w-full max-w-3xl max-h-[90vh] overflow-y-auto">
            <div class="flex justify-between items-center mb-8 border-b border-slate-100 pb-4">
                <h2 class="text-3xl font-black racing-italic uppercase italic">Record Outcome</h2>
                <button @click="showScoringModal = false" class="p-3 bg-slate-100 hover:bg-red-500 hover:text-white rounded-full transition-all duration-300"><i data-lucide="x"></i></button>
            </div>
            <div class="space-y-10 pb-10">
                <div>
                    <label class="block text-[10px] font-black text-indigo-600 uppercase tracking-[0.3em] mb-6 text-center italic">Which Team Won the Race?</label>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div v-for="t in activeTeams" :key="t.id" class="p-5 border-2 rounded-3xl bg-slate-50 hover:border-indigo-500 transition-all group flex gap-4 border-slate-100">
                            <div class="flex-1">
                                <div class="font-black uppercase italic mb-4 text-xl leading-tight tracking-tighter">{{ t.name }}</div>
                                <div class="flex gap-2">
                                    <button @click="recordResult(t.id, 'WIN')" class="flex-1 bg-white hover:bg-indigo-600 hover:text-white py-3 rounded-xl text-[10px] font-black uppercase shadow-sm border border-slate-200 transition-all">Win</button>
                                    <button @click="recordResult(t.id, 'SWEEP')" class="flex-1 bg-yellow-400 hover:bg-yellow-500 text-yellow-900 py-3 rounded-xl text-[10px] font-black uppercase shadow-sm sweep-glow transition-all">Sweep!</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div v-if="settings.tournamentType === 'elimination'" class="pt-8 border-t border-slate-100">
                    <label class="block text-[10px] font-black text-red-500 uppercase tracking-[0.3em] mb-6 text-center italic">Which Team Earned a Loss?</label>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <button v-for="t in activeTeams" :key="t.id" @click="recordLoss(t.id)" class="p-5 border-2 border-slate-100 text-slate-800 rounded-3xl text-left hover:bg-red-600 hover:text-white transition-all font-black uppercase italic group shadow-sm flex flex-col">
                            <div class="text-xl leading-tight tracking-tighter">{{ t.name }}</div>
                            <span class="text-[9px] block opacity-60 font-black tracking-widest mt-1">Assign Strike #{{ t.losses + 1 }}</span>
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
                tournamentType: 'elimination',
                entryFee: 15000
            });

            const handleLogoError = (e) => {
                e.target.style.display = 'none';
            };

            const initDiscord = async () => {
                if (window.discordSDK) {
                    try {
                        const discordSdk = new window.discordSDK.DiscordSDK(null);
                        await discordSdk.ready();
                    } catch (e) { }
                }
            };

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
                if (settings.value.tournamentType === 'points') team.points += pts;
                if (type === 'SWEEP') team.sweeps += 1;

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
                if (!confirm('This will wipe all race history. Proceed?')) return;
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
                initDiscord();
                const saved = localStorage.getItem('kw_data');
                if (saved) {
                    try {
                        const parsed = JSON.parse(saved);
                        teams.value = parsed.teams || [];
                        history.value = parsed.history || [];
                        settings.value = { ...settings.value, ...parsed.settings };
                    } catch (e) { }
                }
                nextTick(() => lucide.createIcons());
            });

            watch([activeTab, showAdminLogin, showScoringModal, teams], () => {
                nextTick(() => lucide.createIcons());
            }, { deep: true });

            return {
                activeTab, isAdmin, adminInput, showAdminLogin, showScoringModal, teams, history, settings, teamForm,
                activeTeams, sortedTeams,
                navClass, addTeam, deleteTeam, verifyAdmin, startTournament, recordResult, recordLoss, resetScores, exportData, save, handleLogoError
            }
        }
    }).mount('#app');
</script>

</body>
</html>
