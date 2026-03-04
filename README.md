# kartwarz
go-kart team racing management app<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kart Warz Manager | FastTrack Tools</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        [v-cloak] { display: none !important; }
        body { font-family: 'Inter', sans-serif; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .racing-italic { font-style: italic; font-weight: 900; }
        .glass { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); }
        .stat-card { @apply bg-white p-6 md:p-8 rounded-[2.5rem] shadow-sm border border-slate-200 transition-all duration-300 hover:shadow-xl hover:-translate-y-1 border-b-8 border-b-slate-100; }
        .btn-primary { @apply bg-indigo-600 text-white font-black uppercase tracking-widest py-4 rounded-2xl shadow-lg shadow-indigo-500/20 active:scale-95 transition-all flex items-center justify-center gap-2; }
        .input-field { @apply w-full p-4 border rounded-2xl bg-slate-50 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-500/10 outline-none font-bold transition-all; }
        .sweep-glow { box-shadow: 0 0 20px rgba(234, 179, 8, 0.3); animation: pulse-glow 2s infinite; }
        @keyframes pulse-glow { 0%, 100% { box-shadow: 0 0 10px rgba(234, 179, 8, 0.2); } 50% { box-shadow: 0 0 25px rgba(234, 179, 8, 0.5); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 min-h-screen pb-24 md:pb-12 md:pt-20">

<div id="app" v-cloak class="max-w-7xl mx-auto p-4 md:p-8">
    <nav class="fixed bottom-0 left-0 right-0 md:top-0 md:bottom-auto glass border-t md:border-t-0 md:border-b border-slate-200 z-[100]">
        <div class="max-w-7xl mx-auto flex items-center justify-between h-20 px-4 md:px-6">
            <div class="flex items-center gap-3">
                <img src="kwlogo.jpeg" alt="KW" class="h-10 w-10 object-cover rounded-xl shadow-lg border-2 border-white" onerror="this.src='https://via.placeholder.com/40?text=KW'">
                <div class="hidden lg:flex items-center gap-2 racing-italic text-2xl tracking-tighter text-indigo-600 uppercase leading-none">KART WARZ</div>
            </div>
            
            <div class="flex gap-1 md:gap-6 w-full md:w-auto justify-around">
                <button @click="activeTab = 'dashboard'" :class="navClass('dashboard')"><i data-lucide="trophy"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Stats</span></button>
                <button @click="activeTab = 'teams'" :class="navClass('teams')"><i data-lucide="users"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Racers</span></button>
                <button @click="activeTab = 'bracket'" :class="navClass('bracket')"><i data-lucide="list-ordered"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Standings</span></button>
                <button @click="activeTab = 'history'" :class="navClass('history')"><i data-lucide="medal"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Logs</span></button>
                <button @click="activeTab = 'admin'" :class="navClass('admin')"><i data-lucide="settings"></i><span class="text-[9px] uppercase font-black tracking-widest hidden md:inline">Control</span></button>
            </div>

            <div class="hidden md:flex items-center gap-6 border-l pl-6 border-slate-200">
                <img src="fasttracktoolslogo.png" alt="FTT" class="h-7 object-contain opacity-70" onerror="this.src='https://via.placeholder.com/80x20?text=FTT'">
                <div v-if="isAdmin" class="text-green-600 text-[10px] font-black bg-green-50 px-3 py-1.5 rounded-full border border-green-200 uppercase tracking-widest">Director</div>
                <button v-else @click="showAdminLogin = true" class="text-[10px] font-black text-slate-400 hover:text-indigo-600 uppercase tracking-widest border border-slate-200 px-3 py-1.5 rounded-full">Login</button>
            </div>
        </div>
    </nav>

    <header class="mb-12 flex flex-col md:flex-row md:items-end md:justify-between gap-8 bg-white p-6 md:p-10 rounded-[2.5rem] md:rounded-[4rem] shadow-sm border border-slate-100 relative overflow-hidden">
        <div class="flex items-center gap-6 md:gap-10 relative z-10">
            <img src="kwlogo.jpeg" alt="Logo" class="h-24 md:h-36 w-24 md:w-36 object-contain rounded-3xl shadow-2xl border-4 border-white" onerror="this.src='https://via.placeholder.com/150?text=KW'">
            <div>
                <h1 class="text-4xl md:text-7xl racing-italic tracking-tighter uppercase mb-2 leading-none">{{ settings.tournamentName }}</h1>
                <div class="flex items-center gap-3 text-slate-400 font-black uppercase text-[10px] md:text-sm tracking-[0.2em]">
                    <span class="bg-indigo-600 text-white px-2 py-0.5 rounded text-[10px]">LIVE</span>
                    {{ settings.tournamentType === 'points' ? 'Championship' : '2-Loss Elimination' }}
                </div>
            </div>
        </div>
        
        <div v-if="isAdmin && settings.isStarted" class="relative z-20">
            <button @click="showScoringModal = true" class="btn-primary px-10 py-6 group border-4 border-white shadow-2xl hover:scale-110">
                <i data-lucide="zap" class="w-6 h-6"></i> 
                <span class="text-xl racing-italic uppercase tracking-tighter">Record Result</span>
            </button>
        </div>
    </header>

    <div class="min-h-[60vh]">
        <div v-if="activeTab === 'dashboard'" class="grid grid-cols-1 md:grid-cols-3 gap-6 animate-in fade-in duration-300">
            <div class="stat-card">
                <div class="p-4 bg-yellow-50 rounded-2xl mb-8 w-fit"><i data-lucide="trophy" class="text-yellow-500"></i></div>
                <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-widest mb-2">Prize Pool</h4>
                <div class="text-3xl font-black racing-italic uppercase text-slate-800">{{ (teams.length * settings.entryFee).toLocaleString() }} UPX</div>
            </div>
            <div class="stat-card">
                <div class="p-4 bg-blue-50 rounded-2xl mb-8 w-fit"><i data-lucide="users" class="text-blue-500"></i></div>
                <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-widest mb-2">Teams</h4>
                <div class="text-3xl font-black racing-italic uppercase text-slate-800">{{ teams.length }} Registered</div>
            </div>
            <div class="stat-card">
                <div class="p-4 bg-indigo-50 rounded-2xl mb-8 w-fit"><i data-lucide="activity" class="text-indigo-500"></i></div>
                <h4 class="text-slate-400 text-[10px] font-black uppercase tracking-widest mb-2">Format</h4>
                <div class="text-2xl font-black racing-italic uppercase text-slate-800">{{ settings.tournamentType.toUpperCase() }}</div>
            </div>
        </div>

        <div v-if="activeTab === 'teams'" class="space-y-8 animate-in fade-in">
            <div v-if="!settings.isStarted" class="bg-white p-8 rounded-[2.5rem] shadow-md border border-slate-200">
                <h3 class="text-2xl font-black racing-italic uppercase text-indigo-600 mb-6">Register New Team</h3>
                <form @submit.prevent="addTeam" class="space-y-6">
                    <input v-model="teamForm.name" required class="input-field" placeholder="Team Name">
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <input v-for="(m, i) in teamForm.members" :key="i" v-model="m.name" required class="input-field" :placeholder="i === 0 ? 'Captain' : 'Racer ' + (i+1)">
                    </div>
                    <button type="submit" class="btn-primary w-full">Confirm Team Registration</button>
                </form>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div v-for="team in teams" :key="team.id" class="stat-card relative">
                    <button v-if="isAdmin && !settings.isStarted" @click="deleteTeam(team.id)" class="absolute top-4 right-4 text-slate-300 hover:text-red-500"><i data-lucide="trash-2" size="16"></i></button>
                    <h4 class="text-2xl font-black racing-italic uppercase mb-4">{{ team.name }}</h4>
                    <div class="space-y-2 mb-6">
                        <div v-for="m in team.members" :key="m.name" class="text-sm font-bold text-slate-600 flex items-center gap-2">
                            <i data-lucide="check" class="w-3 h-3 text-indigo-400"></i> {{ m.name }}
                        </div>
                    </div>
                    <div class="pt-4 border-t border-slate-100 flex justify-between font-black uppercase italic text-xs">
                        <span>{{ settings.tournamentType === 'points' ? 'Points:' : 'Strikes:' }}</span>
                        <span class="text-indigo-600">{{ settings.tournamentType === 'points' ? team.points : team.losses + '/2' }}</span>
                    </div>
                </div>
            </div>
        </div>

        <div v-if="activeTab === 'bracket'" class="animate-in fade-in">
            <div v-if="!settings.isStarted" class="text-center py-20 bg-white rounded-[3rem] border-2 border-dashed border-slate-200">
                <h3 class="text-4xl font-black racing-italic uppercase text-slate-300">Standby for Green Flag...</h3>
                <button v-if="isAdmin" @click="startTournament" class="btn-primary px-16 py-6 mt-8 mx-auto">Launch Season</button>
            </div>
            <div v-else class="bg-white rounded-[3rem] shadow-xl overflow-hidden border border-slate-200">
                <table class="w-full text-left">
                    <thead class="bg-slate-50 text-[10px] font-black uppercase text-slate-400">
                        <tr><th class="p-6">Rank</th><th class="p-6">Team</th><th class="p-6 text-right">Score</th></tr>
                    </thead>
                    <tbody class="divide-y divide-slate-50">
                        <tr v-for="(team, idx) in sortedTeams" :key="team.id">
                            <td class="p-6 text-2xl font-black italic text-slate-200">#{{ idx + 1 }}</td>
                            <td class="p-6 font-black uppercase italic text-xl">{{ team.name }}</td>
                            <td class="p-6 text-right text-2xl font-black text-indigo-600">{{ settings.tournamentType === 'points' ? team.points : (team.status === 'active' ? 'ALIVE' : 'OUT') }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <div v-if="activeTab === 'history'" class="space-y-4 animate-in fade-in">
            <h3 class="text-2xl font-black racing-italic uppercase italic mb-6">Race Archives</h3>
            <div v-for="item in history" :key="item.id" class="bg-white p-6 rounded-3xl flex justify-between items-center shadow-sm border border-slate-100">
                <div class="flex items-center gap-6">
                    <div class="font-black text-2xl italic uppercase">{{ item.teamName }}</div>
                    <div class="text-[10px] font-black text-slate-400 uppercase tracking-widest">{{ item.type }}</div>
                </div>
                <div class="text-[10px] font-black bg-slate-50 px-4 py-2 rounded-full border border-slate-100 text-slate-400">{{ item.time }}</div>
            </div>
        </div>

        <div v-if="activeTab === 'admin'" class="max-w-2xl mx-auto space-y-8 animate-in fade-in">
            <div class="bg-white p-10 rounded-[3rem] border shadow-xl border-slate-200">
                <h3 class="text-2xl font-black uppercase italic border-b pb-6 mb-10">Configuration</h3>
                <div class="space-y-6">
                    <label class="block text-xs font-black text-slate-400 uppercase tracking-widest">Tournament Name</label>
                    <input v-model="settings.tournamentName" @change="save" class="input-field">
                    <label class="block text-xs font-black text-slate-400 uppercase tracking-widest">Scoring Model</label>
                    <select v-model="settings.tournamentType" @change="save" class="input-field uppercase">
                        <option value="elimination">2-Loss Elimination</option>
                        <option value="points">Point Championship</option>
                    </select>
                    <button v-if="isAdmin" @click="resetScores" class="w-full border-4 border-red-50 text-red-500 p-5 rounded-2xl font-black uppercase tracking-widest hover:bg-red-500 hover:text-white transition-all">Reset Season</button>
                </div>
            </div>
            <button v-if="!isAdmin" @click="showAdminLogin = true" class="w-full p-8 bg-white rounded-[2.5rem] font-black uppercase tracking-[0.3em] text-indigo-600 border-4 border-double border-indigo-100 shadow-xl">Unlock Director Panel</button>
        </div>
    </div>

    <div v-if="showAdminLogin" class="fixed inset-0 bg-black/95 backdrop-blur-2xl z-[9999] flex items-center justify-center p-4">
        <div class="bg-white p-10 md:p-16 rounded-[3rem] shadow-2xl w-full max-w-lg border text-center relative overflow-hidden">
            <div class="absolute top-0 left-0 w-full h-2 bg-indigo-600"></div>
            <h2 class="text-4xl font-black racing-italic uppercase mb-2 italic">Director Access</h2>
            <p class="text-xs font-black text-slate-400 uppercase tracking-widest mb-12 italic">Identity Verification Required</p>
            <input type="password" v-model="adminInput" @keyup.enter="verifyAdmin" class="w-full p-8 mb-10 border-4 border-slate-100 bg-slate-50 rounded-3xl text-center text-5xl tracking-[0.6em] font-black focus:border-indigo-600 outline-none shadow-inner" placeholder="••••" autofocus>
            <div class="flex gap-4">
                <button @click="showAdminLogin = false" class="flex-1 py-5 text-slate-400 font-black uppercase tracking-widest">Cancel</button>
                <button @click="verifyAdmin" class="flex-1 py-5 bg-indigo-600 text-white font-black uppercase tracking-widest rounded-2xl shadow-xl hover:bg-indigo-700">Authorize</button>
            </div>
        </div>
    </div>

    <div v-if="showScoringModal" class="fixed inset-0 bg-black/90 backdrop-blur-xl z-[999] flex items-center justify-center p-4">
        <div class="bg-white p-8 rounded-[2.5rem] shadow-2xl w-full max-w-3xl">
            <div class="flex justify-between items-center mb-8 pb-4 border-b">
                <h2 class="text-3xl font-black racing-italic uppercase">Outcome</h2>
                <button @click="showScoringModal = false" class="p-2 bg-slate-100 rounded-full"><i data-lucide="x"></i></button>
            </div>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                <div v-for="t in activeTeams" :key="t.id" class="p-6 border-2 rounded-3xl bg-slate-50 flex flex-col gap-4">
                    <div class="font-black uppercase italic text-xl">{{ t.name }}</div>
                    <div class="flex gap-2">
                        <button @click="recordResult(t.id, 'WIN')" class="flex-1 bg-indigo-600 text-white py-3 rounded-xl text-[10px] font-black uppercase">Win</button>
                        <button @click="recordResult(t.id, 'SWEEP')" class="flex-1 bg-yellow-400 py-3 rounded-xl text-[10px] font-black uppercase shadow-lg">Sweep!</button>
                        <button v-if="settings.tournamentType === 'elimination'" @click="recordLoss(t.id)" class="flex-1 bg-red-100 text-red-600 py-3 rounded-xl text-[10px] font-black uppercase">Loss</button>
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
            const teamForm = ref({ name: '', members: [{ name: '' }, { name: '' }, { name: '' }] });
            const settings = ref({ tournamentName: "Kart Warz Season 1", adminKey: "jah324", isStarted: false, tournamentType: 'elimination', entryFee: 15000 });

            const activeTeams = computed(() => teams.value.filter(t => t.status === 'active'));
            const sortedTeams = computed(() => [...teams.value].sort((a, b) => b.points - a.points || b.sweeps - a.sweeps));
            const navClass = (tab) => `flex flex-col md:flex-row items-center gap-4 px-4 py-2 rounded-xl transition-all ${activeTab.value === tab ? 'text-indigo-600 bg-indigo-50 font-black' : 'text-slate-400 font-bold hover:text-slate-900'}`;

            const addTeam = () => {
                teams.value.push({ id: Date.now(), name: teamForm.value.name, members: JSON.parse(JSON.stringify(teamForm.value.members)), points: 0, losses: 0, sweeps: 0, status: 'active' });
                teamForm.value.name = ''; teamForm.value.members.forEach(m => m.name = '');
                save();
            };

            const deleteTeam = (id) => { teams.value = teams.value.filter(t => t.id !== id); save(); };
            const startTournament = () => { settings.value.isStarted = true; save(); };
            const verifyAdmin = () => { if (adminInput.value === settings.value.adminKey) { isAdmin.value = true; showAdminLogin.value = false; adminInput.value = ''; } else { alert('Invalid Key'); } };
            const recordResult = (id, type) => {
                const team = teams.value.find(t => t.id === id);
                const pts = type === 'SWEEP' ? 3 : 1;
                if (settings.value.tournamentType === 'points') team.points += pts;
                if (type === 'SWEEP') team.sweeps += 1;
                history.value.unshift({ id: Date.now(), teamName: team.name, type, time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) });
                showScoringModal.value = false; save();
            };

            const recordLoss = (id) => {
                const team = teams.value.find(t => t.id === id);
                team.losses += 1; if (team.losses >= 2) team.status = 'eliminated';
                history.value.unshift({ id: Date.now(), teamName: team.name, type: 'LOSS', time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) });
                showScoringModal.value = false; save();
            };

            const resetScores = () => { if (!confirm('Wipe everything?')) return; teams.value.forEach(t => { t.points = 0; t.losses = 0; t.sweeps = 0; t.status = 'active'; }); history.value = []; settings.value.isStarted = false; save(); };
            const save = () => localStorage.setItem('kw_data', JSON.stringify({ teams: teams.value, history: history.value, settings: settings.value }));

            onMounted(() => {
                const saved = localStorage.getItem('kw_data');
                if (saved) { const p = JSON.parse(saved); teams.value = p.teams || []; history.value = p.history || []; settings.value = { ...settings.value, ...p.settings }; }
                nextTick(() => lucide.createIcons());
            });

            watch([activeTab, showAdminLogin, showScoringModal, teams], () => nextTick(() => lucide.<img width="1024" height="1024" alt="fasttracktoolslogo" src="https://github.com/user-at<img width="1024" height="1536" alt="S E A  WARFLEET" src="https://github.com/user-attachments/assets/7e73e081-fffa-477d-a34f-d0562026a22f" />
tachments/assets/3dc7549e-73fb-4c61-a80a![kwlogo](https://github.com/user-attachments/assets/567a7042-50c2-4344-a5ae-d4355d4102b4)
-0a3c<img width="1024" height="1024" alt="RAYTOWN REBELZ" src="https://github.com/user-attachments/assets/fcb8cd04-359a-4cc5-929e-c983aff7fb64" />
88fbffb9" /<img width="1002" height="742" alt="neo vmax kartwarz" src="https://github.com/user-attachments/assets/cb3a68fc-8ac4-466c-971f-15e611da071f" />
>
createIcons()));
            return { activeTab, isAdmin, adminInput, showAdminLogin, showScoringModal, teams, history, settings, teamForm, activeTeams, sortedTeams, navClass, addTeam, deleteTeam, verifyAdmin, startTournament, recordResult, recordLoss, resetScores, save };
        }
    }).mount('#app');
</script>
</body>
</html>
