<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Stremio CineMatch Ultimate</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Outfit', sans-serif; background: #050505; color: #e5e5e5; overflow: hidden; }
        [x-cloak] { display: none !important; }
        
        /* Custom Scrollbar */
        .scroller::-webkit-scrollbar { width: 4px; }
        .scroller::-webkit-scrollbar-track { background: transparent; }
        .scroller::-webkit-scrollbar-thumb { background: #4f46e5; border-radius: 4px; }

        /* Glassmorphism */
        .glass { background: rgba(20, 20, 20, 0.7); backdrop-filter: blur(25px); border-right: 1px solid rgba(255, 255, 255, 0.08); }
        .glass-panel { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(10px); border: 1px solid rgba(255, 255, 255, 0.05); }

        /* Input Styling */
        input[type="number"] { -moz-appearance: textfield; }
        input::-webkit-outer-spin-button, input::-webkit-inner-spin-button { -webkit-appearance: none; margin: 0; }
        
        /* Range Slider */
        input[type=range] { -webkit-appearance: none; background: transparent; }
        input[type=range]::-webkit-slider-thumb { -webkit-appearance: none; height: 16px; width: 16px; border-radius: 50%; background: #6366f1; margin-top: -6px; box-shadow: 0 0 10px rgba(99, 102, 241, 0.5); }
        input[type=range]::-webkit-slider-runnable-track { width: 100%; height: 4px; cursor: pointer; background: #333; border-radius: 2px; }

        /* Animations */
        @keyframes pulse-ring { 0% { box-shadow: 0 0 0 0 rgba(99, 102, 241, 0.7); } 70% { box-shadow: 0 0 0 10px rgba(99, 102, 241, 0); } 100% { box-shadow: 0 0 0 0 rgba(99, 102, 241, 0); } }
        .finding-ring { animation: pulse-ring 2s infinite; }
    </style>
</head>

<body class="h-screen w-screen flex flex-col md:flex-row" x-data="cineMatch()">

    <div class="fixed inset-0 z-0 transition-all duration-1000 transform scale-105"
         :style="`background-image: url('${current.background || ''}'); background-size: cover; background-position: center; opacity: ${current.background ? '0.25' : '0'};`">
    </div>
    <div class="fixed inset-0 z-0 bg-gradient-to-r from-[#050505] via-[#050505]/95 to-[#050505]/40"></div>

    <aside class="relative z-20 w-full md:w-80 h-auto md:h-full glass flex flex-col shadow-2xl shrink-0">
        <div class="p-6 pb-4 border-b border-white/5">
            <h1 class="text-2xl font-black tracking-tighter text-white">STREMIO <span class="text-indigo-500">ULTIMATE</span></h1>
            <p class="text-[10px] text-gray-500 uppercase tracking-widest mt-1">Discovery Engine v3.0</p>
        </div>

        <div class="flex-1 overflow-y-auto scroller px-6 py-4 space-y-7">
            
            <div>
                <label class="text-[10px] font-bold text-gray-500 uppercase tracking-widest mb-3 block">Quick Presets</label>
                <div class="grid grid-cols-2 gap-2">
                    <button @click="preset('recent')" class="text-xs bg-white/5 hover:bg-indigo-600 hover:text-white transition py-2 px-3 rounded border border-white/5">🔥 Modern Hits</button>
                    <button @click="preset('gold')" class="text-xs bg-white/5 hover:bg-indigo-600 hover:text-white transition py-2 px-3 rounded border border-white/5">🏆 90s Gold</button>
                    <button @click="preset('classics')" class="text-xs bg-white/5 hover:bg-indigo-600 hover:text-white transition py-2 px-3 rounded border border-white/5">📽️ Classics</button>
                    <button @click="preset('scifi')" class="text-xs bg-white/5 hover:bg-indigo-600 hover:text-white transition py-2 px-3 rounded border border-white/5">👽 Best Sci-Fi</button>
                </div>
            </div>

            <div>
                <label class="text-[10px] font-bold text-gray-500 uppercase tracking-widest mb-2 block">Genre</label>
                <select x-model="filters.genre" class="w-full bg-[#111] border border-white/10 rounded px-3 py-2.5 text-sm text-gray-300 focus:border-indigo-500 focus:text-white outline-none transition">
                    <option value="">All Genres (Random)</option>
                    <option value="Action">Action</option>
                    <option value="Adventure">Adventure</option>
                    <option value="Animation">Animation</option>
                    <option value="Comedy">Comedy</option>
                    <option value="Crime">Crime</option>
                    <option value="Documentary">Documentary</option>
                    <option value="Drama">Drama</option>
                    <option value="Fantasy">Fantasy</option>
                    <option value="Horror">Horror</option>
                    <option value="Mystery">Mystery</option>
                    <option value="Romance">Romance</option>
                    <option value="Science Fiction">Sci-Fi</option>
                    <option value="Thriller">Thriller</option>
                    <option value="War">War</option>
                    <option value="Western">Western</option>
                </select>
            </div>

            <div>
                <label class="text-[10px] font-bold text-gray-500 uppercase tracking-widest mb-2 block">Year Range</label>
                <div class="flex items-center gap-2">
                    <div class="relative w-full">
                        <input type="number" x-model="filters.yearFrom" min="1900" max="2030" class="w-full bg-[#111] border border-white/10 rounded px-3 py-2 text-sm text-center focus:border-indigo-500 outline-none">
                        <span class="absolute right-2 top-2 text-[10px] text-gray-600">FROM</span>
                    </div>
                    <span class="text-gray-600">-</span>
                    <div class="relative w-full">
                        <input type="number" x-model="filters.yearTo" min="1900" max="2030" class="w-full bg-[#111] border border-white/10 rounded px-3 py-2 text-sm text-center focus:border-indigo-500 outline-none">
                        <span class="absolute right-2 top-2 text-[10px] text-gray-600">TO</span>
                    </div>
                </div>
            </div>

            <div>
                <div class="flex justify-between mb-2">
                    <label class="text-[10px] font-bold text-gray-500 uppercase tracking-widest">Min IMDB Rating</label>
                    <span class="text-xs font-bold text-indigo-400" x-text="filters.minRating"></span>
                </div>
                <input type="range" min="0" max="9" step="0.5" x-model="filters.minRating" class="w-full h-2 bg-gray-800 rounded-lg appearance-none cursor-pointer">
            </div>

            <div class="flex items-center justify-between pt-2">
                <label class="text-[10px] font-bold text-gray-500 uppercase tracking-widest">Never Show Twice</label>
                <button @click="excludeSeen = !excludeSeen" 
                        class="relative inline-flex h-5 w-9 items-center rounded-full transition-colors focus:outline-none" 
                        :class="excludeSeen ? 'bg-indigo-600' : 'bg-gray-700'">
                    <span class="inline-block h-3 w-3 transform rounded-full bg-white transition shadow-sm" 
                          :class="excludeSeen ? 'translate-x-5' : 'translate-x-1'"></span>
                </button>
            </div>
            
            <div class="pt-4 border-t border-white/10 text-center">
                 <button @click="resetBlacklist()" class="text-[10px] text-gray-600 hover:text-red-500 underline decoration-dotted">Reset "Seen" History (<span x-text="seenList.length"></span>)</button>
            </div>
        </div>

        <div class="p-6 border-t border-white/10 bg-[#080808]">
            <button @click="findMovie()" 
                    :disabled="loading"
                    class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-4 rounded-xl shadow-lg shadow-indigo-900/30 transition transform active:scale-95 disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-3 group">
                <span x-show="!loading" class="text-base uppercase tracking-wider">Find Movie</span>
                <span x-show="loading" class="animate-spin">◌</span>
            </button>
            
            <div class="mt-3 text-center">
                 <span x-show="loading" class="text-[10px] text-indigo-400 animate-pulse block">Scanning Catalog... (Attempt <span x-text="attempts"></span>)</span>
                 <span x-show="!loading" class="text-[10px] text-gray-600">Filters applied strictly client-side</span>
            </div>
        </div>
    </aside>

    <main class="relative z-10 flex-1 flex flex-col md:flex-row h-full overflow-hidden">
        
        <div x-show="loading" class="absolute inset-0 z-50 flex flex-col items-center justify-center bg-black/90 backdrop-blur-sm" x-transition.opacity>
            <div class="w-20 h-20 rounded-full border-4 border-indigo-500/30 border-t-indigo-500 animate-spin mb-6"></div>
            <h2 class="text-2xl font-bold text-white tracking-tight">Searching the Archives...</h2>
            <p class="text-gray-500 mt-2 text-sm">Matching Year: <span x-text="filters.yearFrom"></span>-<span x-text="filters.yearTo"></span> | Rating: <span x-text="filters.minRating"></span>+</p>
        </div>

        <div x-show="!current.id && !loading" class="flex-1 flex flex-col items-center justify-center text-center p-8 opacity-40 select-none">
            <svg class="w-24 h-24 mb-6 text-gray-700" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M7 4v16M17 4v16M3 8h4m10 0h4M3 12h18M3 16h4m10 0h4M4 20h16a1 1 0 001-1V5a1 1 0 00-1-1H4a1 1 0 00-1 1v14a1 1 0 001 1z" /></svg>
            <h2 class="text-3xl font-bold mb-2">Awaiting Input</h2>
            <p class="text-sm">Configure your filters on the left to begin discovery.</p>
        </div>

        <template x-if="current.id">
            <div class="flex-1 flex flex-col md:flex-row h-full w-full animate-fade-in">
                
                <div class="w-full md:w-[450px] h-1/2 md:h-full p-8 flex items-center justify-center relative">
                    <div class="relative w-full max-w-[300px] aspect-[2/3] rounded-xl overflow-hidden shadow-2xl border border-white/10 group">
                        <img :src="current.poster" class="w-full h-full object-cover">
                        <div class="absolute inset-0 bg-black/60 opacity-0 group-hover:opacity-100 transition duration-300 flex items-center justify-center gap-4 backdrop-blur-sm">
                            <a :href="current.deepLink" class="bg-white text-black w-12 h-12 rounded-full flex items-center justify-center hover:scale-110 transition">
                                <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                            </a>
                        </div>
                    </div>
                </div>

                <div class="flex-1 h-1/2 md:h-full overflow-y-auto scroller p-8 md:pl-0 flex flex-col justify-center">
                    
                    <div class="max-w-3xl">
                        <div class="flex flex-wrap gap-2 mb-6">
                            <span class="px-2 py-1 bg-white/5 border border-white/10 rounded text-[10px] font-bold uppercase tracking-wider text-indigo-300" x-text="current.year"></span>
                            <span class="px-2 py-1 bg-white/5 border border-white/10 rounded text-[10px] font-bold uppercase tracking-wider text-gray-400" x-text="current.runtime"></span>
                            <span class="px-2 py-1 bg-yellow-500/10 text-yellow-500 rounded text-[10px] font-bold border border-yellow-500/20 flex items-center gap-1">
                                ★ <span x-text="current.imdbRating"></span>
                            </span>
                        </div>

                        <h1 class="text-4xl md:text-6xl font-black leading-tight mb-3 text-white tracking-tight" x-text="current.name"></h1>
                        
                        <div class="flex items-center gap-2 text-gray-400 mb-8 font-light text-lg">
                            <span x-show="current.director">Dir. <span class="text-white font-medium" x-text="current.director"></span></span>
                        </div>

                        <div class="glass-panel p-8 rounded-2xl mb-8">
                            <p class="text-gray-300 leading-relaxed text-lg" x-text="current.description"></p>
                        </div>

                        <div class="flex flex-wrap gap-4">
                            <a :href="current.deepLink" class="bg-white text-black px-8 py-4 rounded-xl font-bold text-lg hover:bg-gray-200 transition shadow-lg shadow-white/10 flex items-center gap-2 transform hover:-translate-y-1">
                                <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                                Open in Stremio
                            </a>
                            <a x-show="current.trailer" :href="current.trailer" target="_blank" class="px-8 py-4 rounded-xl font-bold text-lg transition flex items-center gap-2 border border-white/20 hover:bg-white/10 text-white">
                                Trailer
                            </a>
                            <button @click="markAsUnseen(current.id)" class="px-4 py-4 rounded-xl border border-red-500/30 text-red-400 hover:bg-red-500/10 text-sm font-medium ml-auto" title="Remove from 'Seen' list and try again">
                                ✖
                            </button>
                        </div>

                        <div class="mt-10 pt-8 border-t border-white/5">
                            <h4 class="text-[10px] font-bold text-gray-500 uppercase tracking-widest mb-4">Starring</h4>
                            <div class="flex flex-wrap gap-2">
                                <template x-for="actor in current.cast">
                                    <span class="text-xs text-gray-400 bg-white/5 px-3 py-1.5 rounded-full border border-white/5 hover:border-white/20 transition cursor-default" x-text="actor"></span>
                                </template>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </template>
    </main>

    <script>
        function cineMatch() {
            return {
                loading: false,
                attempts: 0,
                excludeSeen: true,
                seenList: JSON.parse(localStorage.getItem('stremio_seen_blacklist')) || [],

                filters: {
                    genre: '',
                    minRating: 6.0,
                    yearFrom: 1990,
                    yearTo: new Date().getFullYear()
                },

                current: { id: null },

                init() {
                    // Watch for changes to persist blacklist if modified manually
                    this.$watch('seenList', val => localStorage.setItem('stremio_seen_blacklist', JSON.stringify(val)));
                },

                preset(type) {
                    if (type === 'recent') {
                        this.filters.yearFrom = 2020; this.filters.yearTo = 2026; this.filters.minRating = 7.0; this.filters.genre = '';
                    } else if (type === 'gold') {
                        this.filters.yearFrom = 1990; this.filters.yearTo = 1999; this.filters.minRating = 7.5; this.filters.genre = '';
                    } else if (type === 'classics') {
                        this.filters.yearFrom = 1950; this.filters.yearTo = 1980; this.filters.minRating = 8.0; this.filters.genre = '';
                    } else if (type === 'scifi') {
                        this.filters.yearFrom = 1980; this.filters.yearTo = 2026; this.filters.minRating = 7.2; this.filters.genre = 'Science Fiction';
                    }
                },

                resetBlacklist() {
                    if(confirm('Clear your history of seen movies?')) {
                        this.seenList = [];
                        localStorage.removeItem('stremio_seen_blacklist');
                    }
                },

                markAsUnseen(id) {
                    this.seenList = this.seenList.filter(x => x !== id);
                    this.current = { id: null };
                },

                async findMovie() {
                    this.loading = true;
                    this.attempts = 0;
                    const MAX_ATTEMPTS = 25; // Safety break
                    let found = false;

                    // Ensure valid year range
                    if (this.filters.yearFrom > this.filters.yearTo) {
                        const temp = this.filters.yearFrom;
                        this.filters.yearFrom = this.filters.yearTo;
                        this.filters.yearTo = temp;
                    }

                    while (!found && this.attempts < MAX_ATTEMPTS) {
                        this.attempts++;
                        
                        // Smart Skip: If range is huge, skip deep. If range is small, skip shallow.
                        const rangeSize = this.filters.yearTo - this.filters.yearFrom;
                        const maxSkip = rangeSize < 5 ? 50 : 150; 
                        const skip = Math.floor(Math.random() * maxSkip);
                        
                        const baseUrl = "https://v3-cinemeta.strem.io/catalog/movie";
                        const genrePart = this.filters.genre ? `genre=${encodeURIComponent(this.filters.genre)}&` : '';
                        const url = `${baseUrl}/top/${genrePart}skip=${skip}.json`;

                        try {
                            const res = await fetch(url);
                            const data = await res.json();
                            
                            if (!data.metas || data.metas.length === 0) continue;

                            // Shuffle batch to avoid always picking the first result of a page
                            const shuffled = data.metas.sort(() => 0.5 - Math.random());

                            // CLIENT SIDE FILTERING
                            const winner = shuffled.find(m => {
                                // 1. Blacklist Check
                                if (this.excludeSeen && this.seenList.includes(m.imdb_id)) return false;

                                // 2. Rating Check
                                const rating = parseFloat(m.imdbRating) || 0;
                                if (rating < this.filters.minRating) return false;

                                // 3. Year Bracket Check
                                // Note: m.year usually exists, but sometimes we need to parse releaseInfo
                                let y = parseInt(m.year);
                                if (!y && m.releaseInfo) y = parseInt(m.releaseInfo.substring(0,4));
                                if (!y) return false;

                                if (y < this.filters.yearFrom || y > this.filters.yearTo) return false;

                                return true;
                            });

                            if (winner) {
                                await this.loadRichDetails(winner.imdb_id);
                                found = true;
                                // Add to blacklist immediately so it doesn't show again
                                if (!this.seenList.includes(winner.imdb_id)) {
                                    this.seenList.push(winner.imdb_id);
                                }
                            }
                        } catch (e) { console.error(e); }
                    }

                    if (!found) {
                        alert(`No movies matched your filters after checking ${this.attempts} pages. Try widening your Year Bracket or lowering the Rating.`);
                    }
                    this.loading = false;
                },

                async loadRichDetails(imdbId) {
                    try {
                        const res = await fetch(`https://v3-cinemeta.strem.io/meta/movie/${imdbId}.json`);
                        const data = await res.json();
                        const meta = data.meta;
                        
                        // Extract Trailer
                        const yt = meta.trailers ? meta.trailers.find(t => t.source === 'youtube') : null;

                        this.current = {
                            id: meta.imdb_id,
                            name: meta.name,
                            year: meta.year,
                            poster: meta.poster,
                            background: meta.background, // High-res fanart
                            description: meta.description,
                            imdbRating: meta.imdbRating || 'N/A',
                            runtime: meta.runtime,
                            director: meta.director ? meta.director.join(", ") : null,
                            cast: meta.cast ? meta.cast.slice(0, 5).map(c => c.name) : [],
                            deepLink: `stremio:///detail/movie/${meta.imdb_id}`,
                            trailer: yt ? `https://www.youtube.com/watch?v=${yt.sourceId}` : null
                        };
                    } catch (e) {
                        console.error("Meta fetch error");
                    }
                }
            }
        }
    </script>
</body>
</html>
