<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kartwarz | Race Control</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://unpkg.com/lucide@latest"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        
        [v-cloak] { display: none !important; }
        
        body { 
            font-family: 'Inter', sans-serif; 
            overflow-x: hidden; 
            -webkit-tap-highlight-color: transparent; 
            background: #0f172a;
            margin: 0;
        }

        .racing-italic { font-style: italic; font-weight: 900; }
        
        .glass { 
            background: rgba(255, 255, 255, 0.95); 
            backdrop-filter: blur(12px); 
            -webkit-backdrop-filter: blur(12px); 
        }

        .stat-card { 
            background: white; 
            padding: 1.5rem; 
            border-radius: 2.5rem; 
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
            border: 1px solid #e2e8f0;
            transition: all 0.3s;
            border-bottom: 8px solid #f1f5f9;
        }

        .stat-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.1);
            border-bottom-color: #4f46e5;
        }

        .btn-primary { 
            @apply bg-indigo-600 text-white font-black uppercase tracking-widest py-4 rounded-2xl shadow-lg active:scale-95 transition-all flex items-center justify-center gap-2; 
        }
    </style>
</head>
<body class="bg-slate-900">

<div id="app" v-cloak class="min-h-screen w-full p-6 md:p-12">
    
    <header class="max-w-6xl mx-auto flex flex-col md:flex-row justify-between items-center mb-12 gap-6">
        <div class="flex flex-col items-center md:items-start">
            <div class="bg-indigo-600 p-3 rounded-2xl mb-4 shadow-lg inline-block">
                <i data-lucide="trophy" class="w-8 h-8 text-white"></i>
            </div>
            <h1 class="text-5xl md:text-6xl racing-italic text-white uppercase tracking-tighter">
                Kartwarz <span class="text-indigo-500">Live</span>
            </h1>
            <p class="text-indigo-300 font-bold tracking-[0.3em] uppercase text-xs mt-2">
                Official Race Management System
            </p>
        </div>

        <div class="glass px-8 py-4 rounded-[2rem] flex items-center gap-4 border border-white/10">
            <div class="h-3 w-3 bg-green-500 rounded-full animate-pulse"></div>
            <span class="font-black uppercase tracking-widest text-slate-900 text-sm">System Online</span>
        </div>
    </header>

    <main class="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        
        <div class="stat-card lg:col-span-2">
            <div class="flex justify-between items-start mb-6">
                <div>
                    <h2 class="text-2xl font-black uppercase tracking-tight text-slate-900">Current Session</h2>
                    <p class="text-slate-500 font-bold uppercase text-xs tracking-widest">FTRG Stock Car League</p>
                </div>
                <span class="bg-slate-100 text-slate-600 px-4 py-1 rounded-full text-xs font-black uppercase">Standby</span>
            </div>
            
            <div class="bg-slate-50 rounded-[2rem] p-8 text-center border-2 border-dashed border-slate-200">
                <p class="text-slate-400 font-bold uppercase tracking-widest mb-4">Waiting for Green Flag</p>
                <button class="btn-primary w-full max-w-xs mx-auto">
                    <i data-lucide="play" class="w-5 h-5 fill-current"></i>
                    Launch Official Season
                </button>
            </div>
        </div>

        <div class="space-y-6">
            <div class="stat-card border-b-indigo-500">
                <div class="flex items-center gap-4 text-indigo-600 mb-2">
                    <i data-lucide="layout" class="w-5 h-5"></i>
                    <h3 class="font-black uppercase text-sm tracking-widest text-slate-400">FastTrack Ledger</h3>
                </div>
                <p class="text-3xl font-black text-slate-900">0.00 UPX</p>
                <p class="text-xs font-bold text-slate-400 uppercase mt-1">Current Pool Balance</p>
            </div>

            <div class="stat-card border-b-emerald-500">
                <div class="flex items-center gap-4 text-emerald-600 mb-2">
                    <i data-lucide="users" class="w-5 h-5"></i>
                    <h3 class="font-black uppercase text-sm tracking-widest text-slate-400">Active Drivers</h3>
                </div>
                <p class="text-3xl font-black text-slate-900">0 Registered</p>
                <p class="text-xs font-bold text-slate-400 uppercase mt-1">World Engine Tour #1</p>
            </div>
        </div>

        <div class="stat-card lg:col-span-3">
            <div class="flex items-center justify-between mb-8">
                <h3 class="text-xl font-black uppercase tracking-tight">Race Official Archives</h3>
                <i data-lucide="database" class="text-slate-300 w-6 h-6"></i>
            </div>
            
            <div class="overflow-hidden rounded-2xl border border-slate-100">
                <table class="w-full text-left border-collapse">
                    <thead class="bg-slate-50">
                        <tr>
                            <th class="p-4 text-xs font-black uppercase tracking-widest text-slate-400">Date</th>
                            <th class="p-4 text-xs font-black uppercase tracking-widest text-slate-400">Event Name</th>
                            <th class="p-4 text-xs font-black uppercase tracking-widest text-slate-400">Winner</th>
                            <th class="p-4 text-xs font-black uppercase tracking-widest text-slate-400">Status</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-100">
                        <tr class="text-slate-400 italic">
                            <td colspan="4" class="p-12 text-center">No recent race data found.</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </main>

    <footer class="max-w-6xl mx-auto mt-12 pt-8 border-t border-white/5 flex flex-col md:flex-row justify-between items-center gap-4 text-slate-500">
        <p class="text-[10px] font-black uppercase tracking-[0.3em]">© 2026 Raytown Construction Digital</p>
        <div class="flex gap-6">
            <a href="#" class="hover:text-white transition-colors"><i data-lucide="github" class="w-5 h-5"></i></a>
            <a href="#" class="hover:text-white transition-colors"><i data-lucide="settings" class="w-5 h-5"></i></a>
        </div>
    </footer>

</div>

<script>
    const { createApp } = Vue;

    createApp({
        data() {
            return {
                // System is now always authorized
                isSystemReady: true
            }
        },
        mounted() {
            // Initialize icons immediately
            if (window.lucide) {
                lucide.createIcons();
            }
        }
    }).mount('#app');
</script>

</body>
</html>
