```html
<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VibeTube — Watch, Share & Vibe</title>
    <!-- Tailwind CSS via CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Lucide Icons via CDN -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        vibe: {
                            950: '#0f0f0f',
                            900: '#181818',
                            850: '#212121',
                            800: '#272727',
                            700: '#383838',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        /* Custom sleek scrollbar for dark mode */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #0f0f0f;
        }
        ::-webkit-scrollbar-thumb {
            background: #272727;
            border-radius: 9999px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #3f3f46;
        }
        /* Hide scrollbar for category pills on mobile while keeping scrollability */
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
        /* Subtle video hover transition */
        .video-card:hover .thumbnail-img {
            transform: scale(1.04);
        }
        .thumbnail-img {
            transition: transform 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94);
        }
    </style>
</head>
<body class="bg-vibe-950 text-zinc-100 font-sans antialiased min-h-screen flex flex-col pb-16 md:pb-0 selection:bg-rose-500/30 selection:text-rose-200">

    <!-- ==================== HEADER / NAVBAR ==================== -->
    <header class="sticky top-0 z-40 bg-vibe-950/95 backdrop-blur-md border-b border-zinc-800/80">
        <div class="max-w-[1800px] mx-auto px-3 sm:px-6 h-16 flex items-center justify-between gap-3 sm:gap-4">
            
            <!-- Left: Menu Toggle (Desktop) & Brand Logo -->
            <div class="flex items-center gap-2 sm:gap-3 shrink-0">
                <button 
                    onclick="toggleDesktopSidebar()" 
                    class="hidden md:flex p-2 rounded-full text-zinc-300 hover:bg-vibe-800 hover:text-white transition"
                    title="Toggle Navigation"
                >
                    <i data-lucide="menu" class="w-5 h-5"></i>
                </button>

                <button onclick="switchFeedView('home')" class="flex items-center gap-2.5 group focus:outline-none" title="VibeTube Home">
                    <div class="w-9 h-9 rounded-xl bg-gradient-to-tr from-rose-600 via-pink-600 to-amber-500 flex items-center justify-center shadow-lg shadow-rose-600/25 group-hover:scale-105 transition-transform">
                        <i data-lucide="play" class="w-5 h-5 text-white fill-white ml-0.5"></i>
                    </div>
                    <span class="text-xl font-extrabold tracking-tight text-white">
                        Vibe<span class="bg-gradient-to-r from-rose-500 to-amber-400 bg-clip-text text-transparent">Tube</span>
                    </span>
                </button>
            </div>

            <!-- Center: Search Bar -->
            <div class="flex-1 max-w-2xl">
                <div class="relative flex items-center">
                    <i data-lucide="search" class="w-4 h-4 text-zinc-400 absolute left-3.5 pointer-events-none"></i>
                    <input 
                        type="text" 
                        id="searchInput" 
                        placeholder="Search videos, channels, or tags..." 
                        oninput="handleSearch(this.value)"
                        class="w-full bg-vibe-900 border border-zinc-800 focus:border-rose-500/70 focus:ring-2 focus:ring-rose-500/20 text-sm text-zinc-100 placeholder-zinc-500 rounded-full pl-10 pr-10 py-2 sm:py-2.5 transition-all outline-none"
                    >
                    <button 
                        id="clearSearchBtn" 
                        onclick="clearSearch()" 
                        class="hidden absolute right-3 text-zinc-400 hover:text-zinc-200 p-1 rounded-full"
                        title="Clear search"
                    >
                        <i data-lucide="x" class="w-4 h-4"></i>
                    </button>
                </div>
            </div>

            <!-- Right: Action Buttons -->
            <div class="flex items-center gap-2 sm:gap-3 shrink-0">
                <button 
                    onclick="openUploadModal()" 
                    class="flex items-center gap-1.5 sm:gap-2 bg-vibe-800 hover:bg-vibe-700 border border-zinc-700/80 text-white font-semibold text-sm px-3.5 sm:px-4 py-2 rounded-full transition-all shadow-sm active:scale-95"
                >
                    <i data-lucide="plus" class="w-4 h-4 text-rose-500"></i>
                    <span>Create</span>
                </button>
                <div class="w-9 h-9 rounded-full bg-gradient-to-tr from-rose-500 via-purple-500 to-amber-500 p-0.5 hidden sm:block cursor-pointer" title="Your VibeTube Account">
                    <div class="w-full h-full bg-vibe-900 rounded-full flex items-center justify-center text-xs font-bold text-zinc-100">
                        VT
                    </div>
                </div>
            </div>
        </div>
    </header>

    <!-- ==================== MAIN APP SHELL (SIDEBAR + CONTENT) ==================== -->
    <div class="flex-1 flex max-w-[1800px] w-full mx-auto">

        <!-- DESKTOP COLLAPSIBLE SIDEBAR -->
        <aside id="desktopSidebar" class="hidden md:flex flex-col w-60 shrink-0 border-r border-zinc-800/70 bg-vibe-950 px-3 py-4 space-y-1.5 sticky top-16 h-[calc(100vh-4rem)] overflow-y-auto transition-all duration-200">
            <button onclick="switchFeedView('home')" data-nav="home" class="nav-item w-full flex items-center gap-3.5 px-3.5 py-2.5 rounded-xl text-sm font-medium bg-vibe-800 text-white transition-colors">
                <i data-lucide="home" class="w-5 h-5 shrink-0 text-rose-500"></i>
                <span class="sidebar-label truncate">Home</span>
            </button>
            <button onclick="switchFeedView('trending')" data-nav="trending" class="nav-item w-full flex items-center gap-3.5 px-3.5 py-2.5 rounded-xl text-sm font-medium text-zinc-300 hover:bg-vibe-900 hover:text-white transition-colors">
                <i data-lucide="flame" class="w-5 h-5 shrink-0"></i>
                <span class="sidebar-label truncate">Trending</span>
            </button>
            <button onclick="switchFeedView('liked')" data-nav="liked" class="nav-item w-full flex items-center justify-between px-3.5 py-2.5 rounded-xl text-sm font-medium text-zinc-300 hover:bg-vibe-900 hover:text-white transition-colors">
                <div class="flex items-center gap-3.5 min-w-0">
                    <i data-lucide="thumbs-up" class="w-5 h-5 shrink-0"></i>
                    <span class="sidebar-label truncate">Liked Videos</span>
                </div>
                <span id="sidebarLikedCount" class="sidebar-label text-xs font-semibold bg-vibe-800 text-zinc-300 px-2 py-0.5 rounded-full">0</span>
            </button>

            <div class="pt-4 mt-4 border-t border-zinc-800/80 sidebar-label">
                <p class="px-3.5 text-[11px] font-semibold uppercase tracking-wider text-zinc-500 mb-2">Explore Categories</p>
                <div id="sidebarCategoryShortcuts" class="space-y-1">
                    <!-- Populated via JS -->
                </div>
            </div>
        </aside>

        <!-- MAIN CONTENT AREA -->
        <main class="flex-1 min-w-0 px-4 sm:px-6 py-5">

            <!-- VIEW 1: HOME / TRENDING / LIKED VIDEO GRID VIEW -->
            <section id="homeView" class="space-y-5">
                
                <!-- View Heading (Shown for Trending or Liked Views) -->
                <div id="feedHeaderBanner" class="hidden items-center justify-between pb-1">
                    <div class="flex items-center gap-3">
                        <div id="feedHeaderIcon" class="w-10 h-10 rounded-xl bg-rose-500/15 text-rose-500 flex items-center justify-center">
                            <i data-lucide="flame" class="w-5 h-5"></i>
                        </div>
                        <div>
                            <h1 id="feedHeaderTitle" class="text-xl font-bold text-white">Trending Videos</h1>
                            <p id="feedHeaderSubtitle" class="text-xs text-zinc-400">Most watched videos right now on VibeTube</p>
                        </div>
                    </div>
                </div>

                <!-- Category Filter Pills -->
                <div class="flex items-center gap-2 overflow-x-auto no-scrollbar pb-1" id="categoryPillsContainer">
                    <!-- Populated dynamically via JS -->
                </div>

                <!-- Search / Filter Status Banner (shown when filtering) -->
                <div id="filterStatusBanner" class="hidden flex items-center justify-between bg-vibe-900 border border-zinc-800/80 px-4 py-2.5 rounded-xl text-sm">
                    <span id="filterStatusText" class="text-zinc-300"></span>
                    <button onclick="resetAllFilters()" class="text-rose-400 hover:text-rose-300 font-medium text-xs uppercase tracking-wider">
                        Reset Filters
                    </button>
                </div>

                <!-- Video Grid -->
                <div id="videoGrid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-x-5 gap-y-8">
                    <!-- Video Cards populated via JS -->
                </div>

                <!-- Empty State when no videos match search/category/liked -->
                <div id="emptyState" class="hidden flex-col items-center justify-center py-20 text-center">
                    <div class="w-16 h-16 rounded-2xl bg-vibe-900 border border-zinc-800 flex items-center justify-center mb-4">
                        <i id="emptyStateIcon" data-lucide="film" class="w-8 h-8 text-zinc-500"></i>
                    </div>
                    <h3 id="emptyStateTitle" class="text-lg font-semibold text-zinc-200">No videos found</h3>
                    <p id="emptyStateDesc" class="text-sm text-zinc-400 max-w-md mt-1">We couldn't find any videos matching your current search or category filter.</p>
                    <button onclick="resetAllFilters()" class="mt-5 px-5 py-2 bg-rose-600 hover:bg-rose-500 text-white text-sm font-medium rounded-full transition">
                        Explore All Videos
                    </button>
                </div>
            </section>

            <!-- VIEW 2: WATCH / VIDEO PLAYER VIEW -->
            <section id="watchView" class="hidden">
                <!-- Back to Feed Breadcrumb -->
                <div class="mb-4 flex items-center justify-between">
                    <button onclick="goHome()" class="inline-flex items-center gap-1.5 text-sm text-zinc-400 hover:text-white transition-colors">
                        <i data-lucide="arrow-left" class="w-4 h-4"></i>
                        <span>Back to Feed</span>
                    </button>
                    <button onclick="toggleTheaterMode()" id="theaterToggleBtn" class="hidden lg:inline-flex items-center gap-1.5 text-xs font-medium bg-vibe-900 hover:bg-vibe-800 border border-zinc-800 text-zinc-300 px-3 py-1.5 rounded-lg transition">
                        <i data-lucide="rectangle-horizontal" class="w-4 h-4"></i>
                        <span id="theaterBtnLabel">Theater Mode</span>
                    </button>
                </div>

                <!-- Theater Mode Full-Width Player Slot (Populated when Theater Mode is active) -->
                <div id="theaterPlayerContainer" class="hidden mb-6"></div>

                <div id="watchLayoutWrapper" class="flex flex-col lg:flex-row gap-7">
                    <!-- Left Column: Primary Player, Metadata, Description & Comments -->
                    <div class="flex-1 min-w-0">
                        <!-- Standard Player Slot -->
                        <div id="standardPlayerContainer">
                            <div id="playerFrame" class="relative w-full aspect-video bg-black rounded-2xl overflow-hidden shadow-2xl border border-zinc-800/80">
                                <video 
                                    id="mainVideoPlayer" 
                                    class="w-full h-full object-contain" 
                                    controls 
                                    autoplay 
                                    playsinline
                                >
                                    Your browser does not support the HTML5 video tag.
                                </video>
                            </div>
                        </div>

                        <!-- Video Title & Category Badge -->
                        <div class="mt-4">
                            <div class="flex items-center gap-2 mb-1.5 flex-wrap">
                                <span id="playerCategoryBadge" class="text-xs font-semibold px-2.5 py-0.5 rounded-md bg-rose-500/15 text-rose-400 border border-rose-500/20">
                                    Category
                                </span>
                                <span id="playerMetaStats" class="text-xs text-zinc-400">
                                    0 views • Just now
                                </span>
                            </div>
                            <h1 id="playerTitle" class="text-xl sm:text-2xl font-bold text-white leading-snug">
                                Video Title
                            </h1>
                        </div>

                        <!-- Channel Info & Interactive Action Bar -->
                        <div class="mt-4 pb-4 border-b border-zinc-800/80 flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                            <!-- Channel Avatar, Name, Subs & Subscribe Button -->
                            <div class="flex items-center gap-3.5">
                                <div id="playerChannelAvatar" class="w-11 h-11 rounded-full bg-gradient-to-br from-zinc-700 to-zinc-800 flex items-center justify-center font-bold text-white text-sm shrink-0 border border-zinc-700">
                                    CH
                                </div>
                                <div class="min-w-0">
                                    <h3 id="playerChannelName" class="font-semibold text-zinc-100 text-base truncate flex items-center gap-1.5">
                                        Channel Name
                                    </h3>
                                    <p id="playerSubscriberCount" class="text-xs text-zinc-400">120K subscribers</p>
                                </div>
                                <button 
                                    id="subscribeBtn" 
                                    onclick="toggleSubscribe()" 
                                    class="ml-2 px-4 py-2 rounded-full text-sm font-semibold transition-all active:scale-95 bg-white text-zinc-950 hover:bg-zinc-200"
                                >
                                    Subscribe
                                </button>
                            </div>

                            <!-- Like / Dislike & Share Actions -->
                            <div class="flex items-center gap-2 flex-wrap">
                                <!-- Like / Dislike Pill -->
                                <div class="flex items-center bg-vibe-850 border border-zinc-800 rounded-full overflow-hidden">
                                    <button 
                                        id="likeBtn" 
                                        onclick="toggleLike()" 
                                        class="flex items-center gap-2 px-4 py-2 text-sm font-medium text-zinc-200 hover:bg-vibe-800 transition-colors"
                                    >
                                        <i data-lucide="thumbs-up" id="likeIcon" class="w-4 h-4"></i>
                                        <span id="likeCount">0</span>
                                    </button>
                                    <div class="w-px h-5 bg-zinc-700/80"></div>
                                    <button 
                                        id="dislikeBtn" 
                                        onclick="toggleDislike()" 
                                        class="flex items-center gap-1.5 px-3.5 py-2 text-sm font-medium text-zinc-200 hover:bg-vibe-800 transition-colors"
                                        title="Dislike"
                                    >
                                        <i data-lucide="thumbs-down" id="dislikeIcon" class="w-4 h-4"></i>
                                    </button>
                                </div>

                                <!-- Share Button -->
                                <button 
                                    onclick="shareCurrentVideo()" 
                                    class="flex items-center gap-2 bg-vibe-850 hover:bg-vibe-800 border border-zinc-800 text-zinc-200 px-4 py-2 rounded-full text-sm font-medium transition-colors active:scale-95"
                                >
                                    <i data-lucide="share-2" class="w-4 h-4"></i>
                                    <span>Share</span>
                                </button>
                            </div>
                        </div>

                        <!-- Expandable Description Box -->
                        <div class="mt-4 bg-vibe-900 hover:bg-vibe-850 transition-colors border border-zinc-800/80 rounded-2xl p-4 cursor-pointer" onclick="toggleDescription()">
                            <div class="flex items-center gap-3 text-xs font-semibold text-zinc-300 mb-1.5">
                                <span id="descViews">0 views</span>
                                <span>•</span>
                                <span id="descDate">Uploaded recently</span>
                            </div>
                            <p id="playerDescription" class="text-sm text-zinc-300 whitespace-pre-line line-clamp-2 leading-relaxed">
                                Video description goes here...
                            </p>
                            <div id="playerTagsList" class="flex flex-wrap gap-1.5 mt-2.5"></div>
                            <button id="descToggleBtn" class="mt-2 text-xs font-bold text-zinc-100 hover:text-rose-400 transition-colors">
                                Show more
                            </button>
                        </div>

                        <!-- Interactive Comments Section -->
                        <div class="mt-7">
                            <div class="flex items-center gap-2.5 mb-5">
                                <h3 class="text-lg font-bold text-white">Comments</h3>
                                <span id="commentsCountBadge" class="text-xs font-semibold bg-vibe-800 text-zinc-300 px-2.5 py-0.5 rounded-full">0</span>
                            </div>

                            <!-- Add New Comment Form -->
 
