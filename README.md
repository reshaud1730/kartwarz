<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kartwarz | Director Access</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://unpkg.com/lucide@latest"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        
        /* Prevents raw code from flashing on load */
        [v-cloak] { display: none !important; }
        
        body { 
            font-family: 'Inter', sans-serif; 
            overflow-x: hidden; 
            -webkit-tap-highlight-color: transparent; 
            background: #0f172a; /* Slate-900 background */
            display: flex;
            align-items: center;
            justify-content: center;
            min-height: screen;
            margin: 0;
        }

        .racing-italic { font-style: italic; font-weight: 900; }
        
        .glass { 
            background: rgba(255, 255, 255, 0.95); 
            backdrop-filter: blur(12px); 
            -webkit-backdrop-filter: blur(12px); 
        }

        /* Custom UI Elements */
        .stat-card { 
            background: white; 
            padding: 1.5rem; 
            border-radius: 2.5rem; 
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1);
            border: 1px solid #e2e8f0;
            transition: all 0.3s;
        }

        @keyframes pulse-glow { 
            0%, 100% { box-shadow: 0 0 10px rgba(79, 70, 229, 0.2); } 
            50% { box-shadow: 0 0 25px rgba(79, 70, 229, 0.5); } 
        }
        .auth-glow { animation: pulse-glow 2s infinite; }
    </style>
</head>
<body class="bg-slate-900">

<div id="app" v-cloak class="min-h-screen w-full flex items-center justify-center p-4">
    
    <div v-if="!isAuthorized" class="glass max-w-md w-full rounded-[3rem] p-10 shadow-2xl border border-white/20 relative overflow-hidden">
        <div class="absolute top-0 left-0 right-0 h-2 bg-indigo-600"></div>

        <div class="text-center mb-10">
            <h1 class="text-3xl racing-italic text-slate-900 tracking-tight uppercase">
                Director Access
            </h1>
            <p class="text-slate-400 text-xs font-bold tracking-[0.2em] uppercase mt-2">
                Identity Verification Required
            </p>
        </div>

        <div class="space-y-6">
            <div class="relative">
                <input 
                    type="password" 
                    v-model="adminInput"
                    @keyup.enter="verifyAdmin"
                    class="w-full p-8 border-4 border-slate-100 bg-slate-50 rounded-[2.5rem] text-center text-5xl tracking-[0.6em] font-black focus:border-indigo-600 outline-none shadow-inner transition-all"
                    placeholder="••••"
                    autofocus
                    maxlength="4"
                >
            </div>

            <div class="flex gap-4">
                <button 
                    @click="adminInput = ''"
                    class="flex-1 py-5 text-slate-400 font-black uppercase tracking-widest hover:text-slate-600 transition-colors"
                >
                    Clear
                </button>
                
                <button 
                    @click="verifyAdmin"
                    class="flex-1 py-5 bg-indigo-600 text-white font-black uppercase tracking-widest rounded-2xl shadow-xl hover:bg-indigo-700 active:scale-95 transition-all auth-glow"
                >
                    Authorize
                </button>
            </div>
        </div>

        <div class="mt-10 pt-6 border-t border-slate-100 text-center">
            <p class="text-[10px] text-slate-400 font-bold uppercase tracking-widest">
                Race Official Archives | Kartwarz System
            </p>
        </div>
    </div>

    <div v-else class="w-full max-w-4xl animate-in fade-in zoom-in duration-500">
        <div class="flex flex-col items-center text-center text-white mb-12">
            <div class="bg-indigo-600 p-4 rounded-full mb-4 shadow-lg">
                <i data-lucide="shield-check" class="w-8 h-8"></i>
            </div>
            <h2 class="text-5xl racing-italic uppercase mb-2">Director Dashboard</h2>
            <p class="text-indigo-300 font-bold tracking-widest uppercase text-sm">Race Control Initialized</p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div class="stat-card hover:scale-105 transition-transform cursor-pointer">
                <div class="text-indigo-600 mb-4"><i data-lucide="play-circle"></i></div>
                <h3 class="font-black uppercase tracking-tighter text-xl">Start Race</h3>
                <p class="text-slate-500 text-sm">Launch Official Season</p>
            </div>
            
            <div class="stat-card hover:scale-105 transition-transform cursor-pointer">
                <div class="text-indigo-600 mb-4"><i data-lucide="users"></i></div>
                <h3 class="font-black uppercase tracking-tighter text-xl">Manage Drivers</h3>
                <p class="text-slate-500 text-sm">FTRG League Registry</p>
            </div>

            <div class="stat-card hover:scale-105 transition-transform cursor-pointer border-b-8 border-b-red-500">
                <div class="text-red-500 mb-4"><i data-lucide="log-out"></i></div>
                <h3 @click="logout" class="font-black uppercase tracking-tighter text-xl">Exit System</h3>
                <p class="text-slate-500 text-sm">Secure Logout</p>
            </div>
        </div>
    </div>

</div>

<script>
    const { createApp } = Vue;

    createApp({
        data() {
            return {
                isAuthorized: false,
                adminInput: '',
                // Change '1234' to your desired secret 4-digit PIN
                correctPin: '1234' 
            }
        },
        methods: {
            verifyAdmin() {
                if (this.adminInput === this.correctPin) {
                    this.isAuthorized = true;
                    this.adminInput = '';
                    // Re-initialize icons for the new view
                    this.$nextTick(() => {
                        if (window.lucide) lucide.createIcons();
                    });
                } else {
                    alert("Invalid PIN. Access Denied.");
                    this.adminInput = '';
                }
            },
            logout() {
                this.isAuthorized = false;
                this.adminInput = '';
            }
        },
        mounted() {
            // Initialize Lucide icons on load
            if (window.lucide) {
                lucide.createIcons();
            }
        }
    }).mount('#app');
</script>

</body>
</html>
