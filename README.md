# MLBL-ADX-GAME-
Design something for good 
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>ShellBrowser · A Practical Browser</title>
   <script src="https://cdn.tailwindcss.com"></script>
   <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
   <style>
       @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap');

       body {
           font-family: 'Inter', sans-serif;
           overflow: hidden; /* Prevent body scroll, manage inside components */
       }

       /* Custom Scrollbar */
       ::-webkit-scrollbar {
           width: 8px;
           height: 8px;
       }
       ::-webkit-scrollbar-track {
           background: #f1f1f1;
       }
       ::-webkit-scrollbar-thumb {
           background: #cbd5e1;
           border-radius: 4px;
       }
       ::-webkit-scrollbar-thumb:hover {
           background: #94a3b8;
       }

       /* Animations */
       .slide-enter {
           transform: translateX(100%);
       }
       .slide-enter-active {
           transform: translateX(0);
           transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
       }
       .slide-exit {
           transform: translateX(0);
       }
       .slide-exit-active {
           transform: translateX(100%);
           transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
       }

       .fade-in {
           animation: fadeIn 0.2s ease-in-out;
       }

       @keyframes fadeIn {
           from { opacity: 0; transform: translateY(10px); }
           to { opacity: 1; transform: translateY(0); }
       }

       /* Glassmorphism */
       .glass {
           background: rgba(255, 255, 255, 0.85);
           backdrop-filter: blur(12px);
           -webkit-backdrop-filter: blur(12px);
           border: 1px solid rgba(255, 255, 255, 0.3);
       }

       /* Loader for iframe */
       .loader {
           border: 3px solid #f3f3f3;
           border-top: 3px solid #ec4899; /* Pink-500 */
           border-radius: 50%;
           width: 24px;
           height: 24px;
           animation: spin 1s linear infinite;
       }

       @keyframes spin {
           0% { transform: rotate(0deg); }
           100% { transform: rotate(360deg); }
       }
       <a href="rexxon-model.html" target="_blank" class="text-pink-600 underline">Launch Rexon Mobile Legend</a>
       /* Code block styling */
       pre {
           background: #1e293b;
           color: #e2e8f0;
           padding: 1rem;
           border-radius: 0.5rem;
           overflow-x: auto;
           font-family: 'Courier New', Courier, monospace;
       }

       /* Hologram Mode */
       .hologram {
           filter: brightness(1.1) contrast(1.2);
           box-shadow: 0 0 20px rgba(0, 243, 255, 0.3);
       }
       .hologram::before {
           content: '';
           position: absolute;
           top: 0;
           left: 0;
           right: 0;
           bottom: 0;
           background: repeating-linear-gradient(to bottom, rgba(0, 243, 255, 0.05) 0px, rgba(0, 243, 255, 0.05) 2px, transparent 2px, transparent 4px);
           pointer-events: none;
           z-index: 10;
           opacity: 0.8;
       }
   </style>
</head>
<body class="bg-slate-50 text-slate-800 h-screen flex flex-col relative">

   <!-- 1. Top Toolbar -->
   <header class="h-16 glass border-b border-slate-200 flex items-center px-4 gap-3 z-20 shadow-sm shrink-0">
       <!-- Navigation Controls -->
       <div class="flex items-center gap-2 text-slate-500">
           <button id="btnBack" class="p-2 hover:bg-slate-200 rounded-full transition-colors" title="Back">
               <i class="fas fa-arrow-left"></i>
           </button>
           <button id="btnForward" class="p-2 hover:bg-slate-200 rounded-full transition-colors" title="Forward">
               <i class="fas fa-arrow-right"></i>
           </button>
           <button id="btnRefresh" class="p-2 hover:bg-slate-200 rounded-full transition-colors" title="Refresh">
               <i class="fas fa-redo-alt"></i>
           </button>
       </div>

       <!-- Address Bar -->
       <div class="flex-1 relative group">
           <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
               <i class="fas fa-lock text-slate-400 text-xs"></i>
           </div>
           <input type="text" id="urlInput" 
               class="w-full bg-slate-100 border border-transparent focus:bg-white focus:border-pink-400 text-slate-700 text-sm rounded-full py-2.5 pl-8 pr-20 outline-none transition-all shadow-inner"
               placeholder="Search or enter address" value="https://example.com">
           <div class="absolute inset-y-0 right-0 flex items-center pr-1">
               <button id="btnGo" class="bg-pink-500 hover:bg-pink-600 text-white text-xs font-bold py-1.5 px-4 rounded-full transition-colors shadow-md">
                   GO
               </button>
           </div>
       </div>

       <!-- Right Side Tools -->
       <div class="flex items-center gap-2 ml-2">
           <button id="btnHoloMode" class="p-2 text-slate-500 hover:text-cyan-500 hover:bg-cyan-50 rounded-full transition-all relative" title="Hologram Mode">
               <i class="fas fa-hologram" style="font-family: 'Font Awesome 6 Free'; content: '\f749';"></i> <!-- Assuming FA has hologram icon; replace if needed -->
           </button>
           <button id="btnPricePanel" class="p-2 text-slate-500 hover:text-pink-500 hover:bg-pink-50 rounded-full transition-all relative" title="Price Comparison">
               <i class="fas fa-tags"></i>
               <span class="absolute top-1 right-1 w-2 h-2 bg-pink-500 rounded-full animate-pulse"></span>
           </button>
           <button class="p-2 text-slate-500 hover:text-slate-700 hover:bg-slate-200 rounded-full transition-colors">
               <i class="fas fa-ellipsis-v"></i>
           </button>
       </div>
   </header>

   <!-- 2. Main Web Display Area -->
   <main class="flex-1 relative bg-white overflow-hidden">
       <!-- Loader Overlay -->
       <div id="pageLoader" class="absolute inset-0 bg-white z-10 flex flex-col items-center justify-center hidden">
           <div class="loader mb-4"></div>
           <p class="text-slate-400 text-sm animate-pulse">Loading content...</p>
       </div>

       <!-- Simulated Browser Viewport -->
       <div id="viewport" class="w-full h-full overflow-y-auto bg-white p-8">
           <!-- Dynamic Content Injected Here -->
           <div class="max-w-4xl mx-auto space-y-8 fade-in">
               <div class="h-64 bg-gradient-to-r from-pink-100 to-purple-100 rounded-2xl flex items-center justify-center shadow-sm">
                   <div class="text-center">
                       <h1 class="text-4xl font-bold text-slate-800 mb-2">Welcome to ShellBrowser</h1>
                       <p class="text-slate-500">A practical, feature-rich browsing experience.</p>
                   </div>
               </div>
               <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                   <div class="bg-slate-50 p-6 rounded-xl border border-slate-100 hover:shadow-md transition-shadow cursor-pointer" onclick="app.navigate('https://news.ycombinator.com')">
                       <div class="w-10 h-10 bg-orange-100 text-orange-500 rounded-lg flex items-center justify-center mb-4"><i class="fab fa-hacker-news"></i></div>
                       <h3 class="font-bold text-slate-800">Tech News</h3>
                       <p class="text-xs text-slate-400 mt-1">Latest updates from HN</p>
                   </div>
                   <div class="bg-slate-50 p-6 rounded-xl border border-slate-100 hover:shadow-md transition-shadow cursor-pointer" onclick="app.navigate('https://www.wikipedia.org')">
                       <div class="w-10 h-10 bg-gray-100 text-gray-600 rounded-lg flex items-center justify-center mb-4"><i class="fab fa-wikipedia-w"></i></div>
                       <h3 class="font-bold text-slate-800">Knowledge</h3>
                       <p class="text-xs text-slate-400 mt-1">Wikipedia Portal</p>
                   </div>
                   <div class="bg-slate-50 p-6 rounded-xl border border-slate-100 hover:shadow-md transition-shadow cursor-pointer" onclick="document.getElementById('btnPricePanel').click()">
                       <div class="w-10 h-10 bg-pink-100 text-pink-500 rounded-lg flex items-center justify-center mb-4"><i class="fas fa-shopping-bag"></i></div>
                       <h3 class="font-bold text-slate-800">Shopping</h3>
                       <p class="text-xs text-slate-400 mt-1">Compare prices instantly</p>
                   </div>
               </div>
           </div>
       </div>
   </main>

   <!-- 3. Bottom Comment Bar -->
   <section class="h-48 bg-white border-t border-slate-200 flex flex-col shrink-0 z-20 shadow-[0_-4px_6px_-1px_rgba(0,0,0,0.05)]">
       <div class="px-4 py-2 bg-slate-50 border-b border-slate-100 flex justify-between items-center">
           <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider"><i class="fas fa-comments mr-2"></i>Page Discussion</h3>
           <span class="text-xs text-slate-400" id="commentCount">0 comments</span>
       </div>
       <div id="commentsList" class="flex-1 overflow-y-auto p-4 space-y-3 bg-slate-50/50">
           <!-- Comments injected here -->
           <div class="text-center text-slate-400 text-sm mt-8 italic">No comments yet. Be the first to share your thoughts!</div>
       </div>
       <div class="p-3 bg-white border-t border-slate-100 flex gap-2">
           <input type="text" id="commentInput" class="flex-1 bg-slate-100 border-none rounded-full px-4 py-2 text-sm focus:ring-2 focus:ring-pink-300 outline-none" placeholder="Write a comment...">
           <button id="btnPostComment" class="bg-slate-800 hover:bg-slate-900 text-white rounded-full w-10 h-10 flex items-center justify-center transition-colors">
               <i class="fas fa-paper-plane text-xs"></i>
           </button>
       </div>
   </section>

   <!-- 4. Right Slide-out Price Comparison Panel -->
   <aside id="pricePanel" class="fixed top-16 right-0 bottom-0 w-80 bg-white shadow-2xl transform translate-x-full transition-transform duration-300 z-30 border-l border-slate-100 flex flex-col">
       <div class="p-4 border-b border-slate-100 flex justify-between items-center bg-pink-50">
           <h2 class="font-bold text-slate-800"><i class="fas fa-robot text-pink-500 mr-2"></i>Price Assistant</h2>
           <button id="btnClosePrice" class="text-slate-400 hover:text-slate-600"><i class="fas fa-times"></i></button>
       </div>
       <div class="p-4 bg-white">
           <div class="relative">
               <input type="text" id="priceSearchInput" class="w-full border border-slate-200 rounded-lg pl-9 pr-3 py-2 text-sm focus:border-pink-500 outline-none" placeholder="Search product (e.g., 'Headphones')">
               <i class="fas fa-search absolute left-3 top-2.5 text-slate-400 text-xs"></i>
           </div>
       </div>
       <div id="priceResults" class="flex-1 overflow-y-auto p-4 space-y-4">
           <!-- Price items injected here -->
           <div class="text-center text-slate-400 text-sm mt-10">
               <i class="fas fa-search-dollar text-3xl mb-2 opacity-50"></i>
               <p>Search for a product to compare prices.</p>
           </div>
       </div>
   </aside>

   <!-- 5. Bottom Left Download Management Panel -->
   <div class="fixed bottom-52 left-4 w-72 bg-white rounded-xl shadow-2xl border border-slate-100 z-30 overflow-hidden hidden flex-col" id="downloadPanel">
       <div class="bg-slate-800 text-white px-4 py-2 flex justify-between items-center">
           <span class="text-xs font-bold uppercase tracking-wider"><i class="fas fa-download mr-2"></i>Downloads</span>
           <button id="btnCloseDownloads" class="text-slate-400 hover:text-white"><i class="fas fa-times text-xs"></i></button>
       </div>
       <div id="downloadList" class="max-h-48 overflow-y-auto p-2 space-y-2">
           <!-- Download items -->
       </div>
   </div>

   <!-- 6. Floating Action Button (FAB) -->
   <div class="fixed bottom-56 right-6 z-40 flex flex-col items-end gap-3">
       <!-- Menu Items (Hidden by default) -->
       <div id="fabMenu" class="flex flex-col items-end gap-3 mb-2 hidden">
           <button onclick="app.openSnippetModal()" class="flex items-center gap-2 group">
               <span class="bg-slate-800 text-white text-xs px-2 py-1 rounded opacity-0 group-hover:opacity-100 transition-opacity">Code Snippet</span>
               <div class="w-10 h-10 bg-white text-slate-700 rounded-full shadow-lg flex items-center justify-center hover:bg-slate-50 transition-colors border border-slate-100">
                   <i class="fas fa-code"></i>
               </div>
           </button>
           <button onclick="app.triggerUpload('file')" class="flex items-center gap-2 group">
               <span class="bg-slate-800 text-white text-xs px-2 py-1 rounded opacity-0 group-hover:opacity-100 transition-opacity">Upload File</span>
               <div class="w-10 h-10 bg-white text-slate-700 rounded-full shadow-lg flex items-center justify-center hover:bg-slate-50 transition-colors border border-slate-100">
                   <i class="fas fa-file-upload"></i>
               </div>
           </button>
           <button onclick="app.triggerUpload('image')" class="flex items-center gap-2 group">
               <span class="bg-slate-800 text-white text-xs px-2 py-1 rounded opacity-0 group-hover:opacity-100 transition-opacity">Post Image</span>
               <div class="w-10 h-10 bg-white text-slate-700 rounded-full shadow-lg flex items-center justify-center hover:bg-slate-50 transition-colors border border-slate-100">
                   <i class="fas fa-image"></i>
               </div>
           </button>
       </div>
       
       <!-- Main FAB -->
       <button id="fabMain" class="w-14 h-14 bg-pink-500 hover:bg-pink-600 text-white rounded-full shadow-lg shadow-pink-500/40 flex items-center justify-center text-2xl transition-all transform hover:scale-110 active:scale-95">
           <i class="fas fa-plus transition-transform duration-300" id="fabIcon"></i>
       </button>
   </div>

   <!-- Modals -->
   
   <!-- Snippet Modal -->
   <div id="snippetModal" class="fixed inset-0 bg-black/50 z-50 hidden flex items-center justify-center backdrop-blur-sm">
       <div class="bg-white rounded-xl shadow-2xl w-full max-w-lg m-4 overflow-hidden transform transition-all scale-95 opacity-0" id="snippetContent">
           <div class="bg-slate-50 px-4 py-3 border-b border-slate-100 flex justify-between items-center">
               <h3 class="font-bold text-slate-700">Share Code Snippet</h3>
               <button onclick="app.closeSnippetModal()" class="text-slate-400 hover:text-slate-600"><i class="fas fa-times"></i></button>
           </div>
           <div class="p-4">
               <textarea id="codeInput" class="w-full h-32 bg-slate-800 text-green-400 font-mono text-sm p-3 rounded-lg outline-none focus:ring-2 focus:ring-pink-500" placeholder="// Paste your code here..."></textarea>
               <div class="mt-4 flex justify-end gap-2">
                   <button onclick="app.closeSnippetModal()" class="px-4 py-2 text-sm text-slate-600 hover:bg-slate-100 rounded-lg">Cancel</button>
                   <button onclick="app.postSnippet()" class="px-4 py-2 text-sm bg-pink-500 text-white rounded-lg hover:bg-pink-600 shadow-md">Post to Comments</button>
               </div>
           </div>
       </div>
   </div>

   <!-- Hidden File Input -->
   <input type="file" id="fileInput" class="hidden" onchange="app.handleFileSelect(this)">

   <script>
       /**
        * ShellBrowser Application Logic
        */
       const app = {
           state: {
               currentUrl: 'https://example.com',
               history: ['https://example.com'],
               historyIndex: 0,
               comments: [],
               downloads: [],
               fabOpen: false,
               priceData: [
                   { name: "Sony WH-1000XM5", vendor: "TechStore", price: 348.00, icon: "fa-headphones" },
                   { name: "Sony WH-1000XM5", vendor: "AudioMax", price: 329.99, icon: "fa-headphones", best: true },
                   { name: "Sony WH-1000XM5", vendor: "SoundShop", price: 359.00, icon: "fa-headphones" },
                   { name: "MacBook Air M2", vendor: "TechStore", price: 1199.00, icon: "fa-laptop" },
                   { name: "MacBook Air M2", vendor: "CompuWorld", price: 1149.50, icon: "fa-laptop", best: true },
                   { name: "Logitech MX Master", vendor: "TechStore", price: 99.99, icon: "fa-mouse" },
                   { name: "Logitech MX Master", vendor: "GadgetHub", price: 89.00, icon: "fa-mouse", best: true }
               ],
               hologramMode: false
           },

           init() {
               this.cacheDOM();
               this.bindEvents();
               this.renderComments();
               this.simulateInitialLoad();
           },

           cacheDOM() {
               this.dom = {
                   urlInput: document.getElementById('urlInput'),
                   viewport: document.getElementById('viewport'),
                   loader: document.getElementById('pageLoader'),
                   commentsList: document.getElementById('commentsList'),
                   commentInput: document.getElementById('commentInput'),
                   commentCount: document.getElementById('commentCount'),
                   pricePanel: document.getElementById('pricePanel'),
                   priceResults: document.getElementById('priceResults'),
                   priceSearchInput: document.getElementById('priceSearchInput'),
                   downloadPanel: document.getElementById('downloadPanel'),
                   downloadList: document.getElementById('downloadList'),
                   fabMain: document.getElementById('fabMain'),
                   fabMenu: document.getElementById('fabMenu'),
                   fabIcon: document.getElementById('fabIcon'),
                   snippetModal: document.getElementById('snippetModal'),
                   snippetContent: document.getElementById('snippetContent'),
                   codeInput: document.getElementById('codeInput'),
                   fileInput: document.getElementById('fileInput')
               };
           },

           bindEvents() {
               // Navigation
               document.getElementById('btnBack').onclick = () => this.goBack();
               document.getElementById('btnForward').onclick = () => this.goForward();
               document.getElementById('btnRefresh').onclick = () => this.refresh();
               document.getElementById('btnGo').onclick = () => this.handleNavigate();
               this.dom.urlInput.addEventListener('keypress', (e) => {
                   if (e.key === 'Enter') this.handleNavigate();
               });

               // Comments
               document.getElementById('btnPostComment').onclick = () => this.postComment();
               this.dom.commentInput.addEventListener('keypress', (e) => {
                   if (e.key === 'Enter') this.postComment();
               });

               // Price Panel
               document.getElementById('btnPricePanel').onclick = () => this.togglePricePanel(true);
               document.getElementById('btnClosePrice').onclick = () => this.togglePricePanel(false);
               this.dom.priceSearchInput.addEventListener('input', (e) => this.searchPrices(e.target.value));

               // Downloads
               document.getElementById('btnCloseDownloads').onclick = () => this.dom.downloadPanel.classList.add('hidden');

               // FAB
               this.dom.fabMain.onclick = () => this.toggleFAB();

               // Snippet Modal
               this.dom.snippetModal.addEventListener('click', (e) => {
                   if (e.target === this.dom.snippetModal) this.closeSnippetModal();
               });

               // Hologram Mode
               document.getElementById('btnHoloMode').onclick = () => this.toggleHologramMode();
           },

           handleNavigate() {
               let url = this.dom.urlInput.value.trim();
               if (!url.startsWith('http')) url = 'https://' + url;
               this.navigate(url);
           },

           navigate(url) {
               this.state.currentUrl = url;
               this.dom.urlInput.value = url;
               this.state.history = this.state.history.slice(0, this.state.historyIndex + 1);
               this.state.history.push(url);
               this.state.historyIndex++;
               this.loadContent(url);
           },

           loadContent(url) {
               this.dom.loader.classList.remove('hidden');
               this.dom.viewport.classList.add('opacity-50');

               setTimeout(() => {
                   this.dom.loader.classList.add('hidden');
                   this.dom.viewport.classList.remove('opacity-50');
                   this.renderPageContent(url);
               }, 800);
           },

           renderPageContent(url) {
               let content = '';
               const domain = new URL(url).hostname.replace('www.', '');

               if (domain.includes('google')) {
                   content = `
                       <div class="flex flex-col items-center mt-20 fade-in">
                           <h1 class="text-6xl font-bold text-slate-700 mb-8 tracking-tighter">Google</h1>
                           <div class="w-full max-w-xl relative">
                               <input type="text" class="w-full border border-slate-200 rounded-full py-3 px-6 shadow-lg text-lg outline-none focus:shadow-xl transition-shadow" placeholder="Search ShellBrowser...">
                               <div class="absolute right-4 top-3.5 text-slate-400"><i class="fas fa-microphone"></i></div>
                           </div>
                           <div class="mt-8 flex gap-4">
                               <button class="bg-slate-100 px-4 py-2 rounded hover:border hover:border-slate-300 text-sm text-slate-700">Google Search</button>
                               <button class="bg-slate-100 px-4 py-2 rounded hover:border hover:border-slate-300 text-sm text-slate-700">I'm Feeling Lucky</button>
                           </div>
                       </div>
                   `;
               } else if (domain.includes('wikipedia')) {
                   content = `
                       <div class="max-w-4xl mx-auto mt-10 fade-in">
                           <div class="flex gap-8">
                               <div class="w-64 shrink-0">
                                   <div class="bg-slate-100 border border-slate-300 p-2 text-xs text-center mb-2">Wikipedia Logo</div>
                                   <ul class="text-sm space-y-1 text-blue-600">
                                       <li>Main page</li>
                                       <li>Contents</li>
                                       <li>Featured content</li>
                                   </ul>
                               </div>
                               <div class="flex-1">
                                   <h1 class="text-3xl font-serif mb-4 border-b pb-2">Web Browser</h1>
                                   <p class="mb-4 text-sm leading-relaxed">A web browser (commonly referred to as a browser) is application software for accessing the World Wide Web. When a user requests a web page from a particular website, the web browser retrieves the necessary content from a web server and then displays the page on the user's device.</p>
                                   <div class="bg-blue-50 p-4 border border-blue-100 rounded text-sm">
                                       <strong>Did you know?</strong> ShellBrowser is a conceptual browser designed for productivity.
                                   </div>
                               </div>
                           </div>
                       </div>
                   `;
               } else if (domain.includes('ycombinator') || domain.includes('hackernews')) {
                   content = `
                       <div class="max-w-5xl mx-auto mt-4 fade-in bg-orange-50 min-h-screen">
                           <table class="w-full text-sm">
                               <tr class="bg-orange-500 text-white h-8">
                                   <td class="pl-2 font-bold">Hacker News</td>
                                   <td class="text-right pr-2">login</td>
                               </tr>
                               ${[1,2,3,4,5].map(i => `
                               <tr class="border-b border-orange-100">
                                   <td class="p-2">
                                       <span class="text-slate-500 mr-1">${i}.</span>
                                       <a href="#" class="text-black hover:underline">Show HN: ShellBrowser – A practical browser with built-in tools</a>
                                       <span class="text-xs text-slate-500">(github.com)</span>
                                       <div class="text-xs text-slate-500 ml-4">12 points by user${i} 2 hours ago | hide | 4 comments</div>
                                   </td>
                               </tr>
                               `).join('')}
                           </table>
                       </div>
                   `;
               } else {
                   // Generic Page
                   content = `
                       <div class="max-w-3xl mx-auto mt-12 text-center fade-in">
                           <div class="w-20 h-20 bg-slate-200 rounded-full mx-auto mb-6 flex items-center justify-center text-4xl text-slate-400">
                               <i class="fas fa-globe"></i>
                           </div>
                           <h1 class="text-2xl font-bold text-slate-800 mb-2">Welcome to ${domain}</h1>
                           <p class="text-slate-500 mb-8">This is a simulated view of the requested webpage.</p>
                           <div class="bg-slate-50 p-6 rounded-lg border border-slate-200 text-left">
                               <h3 class="font-bold mb-2">Page Info</h3>
                               <p class="text-sm text-slate-600">URL: ${url}</p>
                               <p class="text-sm text-slate-600">Status: 200 OK</p>
                               <p class="text-sm text-slate-600 mt-4">In a real application, this area would render the HTML content of the target site.</p>
                           </div>
                       </div>
                   `;
               }

               this.dom.viewport.innerHTML = content;
           },

           goBack() {
               if (this.state.historyIndex > 0) {
                   this.state.historyIndex--;
                   const url = this.state.history[this.state.historyIndex];
                   this.dom.urlInput.value = url;
                   this.loadContent(url);
               }
           },

           goForward() {
               if (this.state.historyIndex < this.state.history.length - 1) {
                   this.state.historyIndex++;
                   const url = this.state.history[this.state.historyIndex];
                   this.dom.urlInput.value = url;
                   this.loadContent(url);
               }
           },

           refresh() {
               this.loadContent(this.state.currentUrl);
           },

           simulateInitialLoad() {
               this.loadContent(this.state.currentUrl);
           },

           // --- Comment System ---

           postComment(text = null, type = 'text') {
               const content = text || this.dom.commentInput.value.trim();
               if (!content) return;

               const comment = {
                   id: Date.now(),
                   user: 'Guest User',
                   avatar: `https://api.dicebear.com/7.x/avataaars/svg?seed=${Date.now()}`,
                   content: content,
                   type: type, // text, image, code
                   time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
               };

               this.state.comments.push(comment);
               this.dom.commentInput.value = '';
               this.renderComments();
           },

           renderComments() {
               if (this.state.comments.length === 0) {
                   this.dom.commentsList.innerHTML = `<div class="text-center text-slate-400 text-sm mt-8 italic">No comments yet. Be the first to share your thoughts!</div>`;
                   this.dom.commentCount.textContent = `0 comments`;
                   return;
               }

               this.dom.commentsList.innerHTML = this.state.comments.map(c => {
                   let contentHtml = '';
                   if (c.type === 'image') {
                       contentHtml = `<img src="${c.content}" class="max-h-32 rounded-lg mt-1 border border-slate-200">`;
                   } else if (c.type === 'code') {
                       contentHtml = `<pre class="mt-1 text-xs"><code>${c.content.replace(/</g, '&lt;')}</code></pre>`;
                   } else {
                       contentHtml = `<p class="text-slate-700 text-sm">${c.content}</p>`;
                   }

                   return `
                       <div class="flex gap-3 fade-in">
                           <img src="${c.avatar}" class="w-8 h-8 rounded-full bg-slate-200">
                           <div class="flex-1">
                               <div class="flex justify-between items-baseline">
                                   <span class="text-xs font-bold text-slate-800">${c.user}</span>
                                   <span class="text-[10px] text-slate-400">${c.time}</span>
                               </div>
                               ${contentHtml}
                           </div>
                       </div>
                   `;
               }).join('');

               this.dom.commentCount.textContent = `${this.state.comments.length} comments`;
               
               // Scroll to bottom
               this.dom.commentsList.scrollTop = this.dom.commentsList.scrollHeight;
           },

           // --- Price Comparison ---

           togglePricePanel(show) {
               if (show) {
                   this.dom.pricePanel.classList.remove('translate-x-full');
               } else {
                   this.dom.pricePanel.classList.add('translate-x-full');
               }
           },

           searchPrices(query) {
               if (!query) {
                   this.dom.priceResults.innerHTML = `
                       <div class="text-center text-slate-400 text-sm mt-10">
                           <i class="fas fa-search-dollar text-3xl mb-2 opacity-50"></i>
                           <p>Search for a product to compare prices.</p>
                       </div>`;
                   return;
               }

               const lowerQ = query.toLowerCase();
               const results = this.state.priceData.filter(p => p.name.toLowerCase().includes(lowerQ));

               if (results.length === 0) {
                   this.dom.priceResults.innerHTML = `<div class="text-center text-slate-400 text-sm mt-4">No results found.</div>`;
                   return;
               }

               // Group by product
               const grouped = results.reduce((acc, item) => {
                   if (!acc[item.name]) acc[item.name] = [];
                   acc[item.name].push(item);
                   return acc;
               }, {});

               this.dom.priceResults.innerHTML = Object.keys(grouped).map(productName => {
                   const vendors = grouped[productName];
                   // Sort by price
                   vendors.sort((a, b) => a.price - b.price);
                   const lowest = vendors[0].price;

                   return `
                       <div class="bg-white border border-slate-100 rounded-lg p-3 shadow-sm">
                           <h4 class="font-bold text-slate-800 text-sm mb-2">${productName}</h4>
                           <div class="space-y-2">
                               ${vendors.map(v => `
                                   <div class="flex justify-between items-center text-xs p-2 rounded ${v.price === lowest ? 'bg-green-50 border border-green-100' : 'bg-slate-50'}">
                                       <div class="flex items-center gap-2">
                                           <i class="fas fa-store text-slate-400"></i>
                                           <span class="font-medium text-slate-600">${v.vendor}</span>
                                           ${v.price === lowest ? '<span class="bg-green-500 text-white text-[10px] px-1 rounded ml-1">BEST</span>' : ''}
                                       </div>
                                       <span class="font-bold ${v.price === lowest ? 'text-green-600' : 'text-slate-700'}">$${v.price.toFixed(2)}</span>
                                   </div>
                               `).join('')}
                           </div>
                       </div>
                   `;
               }).join('');
           },

           // --- Floating Action Button & Uploads ---

           toggleFAB() {
               this.state.fabOpen = !this.state.fabOpen;
               if (this.state.fabOpen) {
                   this.dom.fabMenu.classList.remove('hidden');
                   this.dom.fabIcon.classList.add('rotate-45');
                   this.dom.fabMain.classList.add('bg-slate-800');
                   this.dom.fabMain.classList.remove('bg-pink-500', 'hover:bg-pink-600');
               } else {
                   this.dom.fabMenu.classList.add('hidden');
                   this.dom.fabIcon.classList.remove('rotate-45');
                   this.dom.fabMain.classList.remove('bg-slate-800');
                   this.dom.fabMain.classList.add('bg-pink-500', 'hover:bg-pink-600');
               }
           },

           triggerUpload(type) {
               this.dom.fileInput.accept = type === 'image' ? 'image/*' : '*/*';
               this.dom.fileInput.click();
               this.toggleFAB(); // Close menu
           },

           handleFileSelect(input) {
               if (input.files && input.files[0]) {
                   const file = input.files[0];
                   
                   // Add to downloads
                   this.addDownload(file);

                   // If image, post to comments
                   if (file.type.startsWith('image/')) {
                       const reader = new FileReader();
                       reader.onload = (e) => {
                           this.postComment(e.target.result, 'image');
                       };
                       reader.readAsDataURL(file);
                   } else {
                       // Text comment for files
                       this.postComment(`Uploaded file: \( {file.name} ( \){(file.size/1024).toFixed(1)} KB)`, 'text');
                   }
               }
               input.value = ''; // Reset
           },

           addDownload(file) {
               const download = {
                   id: Date.now(),
                   name: file.name,
                   size: (file.size / 1024 / 1024).toFixed(2) + ' MB',
                   progress: 0,
                   status: 'pending' // pending, downloading, done
               };
               this.state.downloads.unshift(download);
               this.renderDownloads();
               
               // Show panel
               this.dom.downloadPanel.classList.remove('hidden');
               this.dom.downloadPanel.classList.add('flex');

               // Simulate progress
               this.simulateDownload(download.id);
           },

           simulateDownload(id) {
               const download = this.state.downloads.find(d => d.id === id);
               if (!download) return;

               download.status = 'downloading';
               let progress = 0;
               
               const interval = setInterval(() => {
                   progress += Math.random() * 10;
                   if (progress >= 100) {
                       progress = 100;
                       download.status = 'done';
                       clearInterval(interval);
                   }
                   download.progress = progress;
                   this.renderDownloads();
               }, 300);
           },

           renderDownloads() {
               if (this.state.downloads.length === 0) {
                   this.dom.downloadList.innerHTML = '<div class="text-xs text-slate-400 text-center p-2">No active downloads</div>';
                   return;
               }

               this.dom.downloadList.innerHTML = this.state.downloads.map(d => `
                   <div class="bg-slate-50 p-2 rounded border border-slate-100">
                       <div class="flex justify-between text-xs mb-1">
                           <span class="font-medium text-slate-700 truncate w-32">${d.name}</span>
                           <span class="text-slate-400">${d.status === 'done' ? 'Done' : d.progress.toFixed(0) + '%'}</span>
                       </div>
                       <div class="w-full bg-slate-200 rounded-full h-1.5">
                           <div class="bg-pink-500 h-1.5 rounded-full transition-all duration-300" style="width: ${d.progress}%"></div>
                       </div>
                   </div>
               `).join('');
           },

           // --- Code Snippet Modal ---

           openSnippetModal() {
               this.dom.snippetModal.classList.remove('hidden');
               // Small delay for animation
               setTimeout(() => {
                   this.dom.snippetContent.classList.remove('scale-95', 'opacity-0');
                   this.dom.snippetContent.classList.add('scale-100', 'opacity-100');
               }, 10);
               this.toggleFAB(); // Close menu
           },

           closeSnippetModal() {
               this.dom.snippetContent.classList.remove('scale-100', 'opacity-100');
               this.dom.snippetContent.classList.add('scale-95', 'opacity-0');
               setTimeout(() => {
                   this.dom.snippetModal.classList.add('hidden');
                   this.dom.codeInput.value = '';
               }, 200);
           },

           postSnippet() {
               const code = this.dom.codeInput.value.trim();
               if (code) {
                   this.postComment(code, 'code');
                   this.closeSnippetModal();
               }
           },

           // --- Hologram Mode ---

           toggleHologramMode() {
               this.state.hologramMode = !this.state.hologramMode;
               if (this.state.hologramMode) {
                   this.dom.viewport.classList.add('hologram');
               } else {
                   this.dom.viewport.classList.remove('hologram');
               }
           }
       };

       // Initialize App when DOM is ready
       document.addEventListener('DOMContentLoaded', () => {
           app.init();
       });

   </script>
</body>
</html>
<iframe src="rexxon-model.html" class="w-full h-96 border-2 border-cyan-500 rounded-lg"></iframe>
fetch('https://api.github.com/repos/yourusername/rexxon-mobile-legend/commits')
    .then(r => r.json())
    .then(commits => {
        const last = commits[0];
        document.getElementById('viewport').innerHTML += `
            <div class="mt-8 p-4 bg-cyan-50 rounded-lg">
                <p class="text-sm text-cyan-700">Latest update: ${last.commit.message}</p>
                <p class="text-xs text-slate-500">${new Date(last.commit.author.date).toLocaleString()}</p>
            </div>`;
    });
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mobile Legends Hologram Skin – Rexon Echo v2</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background:#000; overflow:hidden; margin:0; font-family:'Orbitron',sans-serif; }
    #crt { position:fixed; inset:0; pointer-events:none; z-index:999; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px); opacity:0.7; }
    #canvas { image-rendering:pixelated; filter:brightness(1.3) contrast(1.4) drop-shadow(0 0 30px #00f3ff); }
    .aura { animation:spin 12s linear infinite; }
    .luna { animation:pulse 4s ease-in-out infinite; }
    @keyframes spin { from{transform:rotate(0deg)} to{transform:rotate(360deg)} }
    @keyframes pulse { 0%,100%{scale:1; opacity:0.7} 50%{scale:1.2; opacity:1} }
  </style>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap" rel="stylesheet">
</head>
<body class="bg-black text-cyan-300">

  <!-- CRT Scanlines -->
  <div id="crt"></div>

  <!-- Canvas Core -->
  <canvas id="canvas" class="w-full h-full"></canvas>

  <!-- HUD Overlay -->
  <div class="absolute inset-0 flex flex-col justify-between p-6 pointer-events-none">
    <div class="flex justify-between items-center">
      <h1 class="text-5xl font-bold text-glow">REXON ECHO</h1>
      <div class="text-sm bg-black/40 px-4 py-1 rounded-full">
        NFT UNLOCKS: <span id="unlockCount">0</span>
      </div>
    </div>

    <!-- Exploration Mini-Map -->
    <div class="self-center bg-black/50 p-4 rounded-xl backdrop-blur-md">
      <p class="text-lg mb-2">SCANNING NEW TECH...</p>
      <div class="w-64 h-2 bg-gray-800 rounded-full">
        <div class="h-full bg-gradient-to-r from-cyan-500 to-pink-500 rounded-full w-3/4 animate-pulse"></div>
      </div>
    </div>

    <!-- Aura & Luna Info -->
    <div class="self-end text-right">
      <p class="text-xl">LUNA CORE ACTIVE</p>
      <p class="text-sm opacity-70">AURA RING: 87% CHARGE</p>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    let w = canvas.width = innerWidth;
    let h = canvas.height = innerHeight;
    let angle = 0;
    let trails = [];
    let unlocks = 0;

    window.addEventListener('resize', () => {
      w = canvas.width = innerWidth;
      h = canvas.height = innerHeight;
    });

    // Model variants (swap on click)
    const models = [
      {name:'Mecha Triangle', color:'#00f3ff', inner:'#001122'},
      {name:'Luna Orb', color:'#c0c0c0', inner:'#222'},
      {name:'Aura Phoenix', color:'#ff00aa', inner:'#440022'}
    ];
    let currentModel = 0;

    function drawModel() {
      ctx.clearRect(0,0,w,h);

      // Grid background
      ctx.strokeStyle = 'rgba(0,255,255,0.12)';
      for(let x=0; x<w; x+=30) ctx.moveTo(x,0), ctx.lineTo(x,h);
      for(let y=0; y<h; y+=30) ctx.moveTo(0,y), ctx.lineTo(w,y);
      ctx.stroke();

      ctx.save();
      ctx.translate(w/2, h/2);
      ctx.rotate(angle);

      // Aura Ring (spinning halo)
      ctx.strokeStyle = '#ff00aa';
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.arc(0,0, 140, 0, Math.PI*2);
      ctx.stroke();

      // Luna Moon Core (pulsing silver orb)
      ctx.fillStyle = '#c0c0c0';
      ctx.shadowBlur = 40;
      ctx.shadowColor = '#ffffff';
      ctx.beginPath();
      ctx.arc(0, -100, 35, 0, Math.PI*2);
      ctx.fill();

      // Main Skin (current model)
      const m = models ;
      ctx.fillStyle = m.inner;
      ctx.strokeStyle = m.color;
      ctx.lineWidth = 5;
      ctx.beginPath();
      ctx.moveTo(0, -90);
      ctx.lineTo(-80, 70);
      ctx.lineTo(80, 70);
      ctx.closePath();
      ctx.fill(); ctx.stroke();

      // Inner pulse
      ctx.globalAlpha = 0.5;
      ctx.fillStyle = m.color;
      ctx.beginPath();
      ctx.arc(0,0, 50 + Math.sin(Date.now()/600)*15, 0, Math.PI*2);
      ctx.fill();

      ctx.restore();

      // Trails
      trails.forEach((t,i) => {
        ctx.globalAlpha = t.life/40;
        ctx.fillStyle = t.color;
        ctx.beginPath();
        ctx.arc(t.x, t.y, 12, 0, Math.PI*2);
        ctx.fill();
        t.life--;
        if(t.life<=0) trails.splice(i,1);
      });
    }

    function loop() {
      angle += 0.015;
      drawModel();
      requestAnimationFrame(loop);
    }

    loop();

    // Click → unlock new model + burst
    canvas.onclick = () => {
      unlocks++;
      document.getElementById('unlockCount').textContent = unlocks;
      currentModel = (currentModel + 1) % models.length;
      trails.push({x:w/2, y:h/2, life:80, color:models .color});
    };
  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>REXON ECHO // Neural Hologram Realm</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background:#000; overflow:hidden; margin:0; font-family:'Orbitron',sans-serif; }
    #crt { position:fixed; inset:0; pointer-events:none; z-index:999; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px); opacity:0.7; }
    #canvas { image-rendering:pixelated; filter:brightness(1.4) contrast(1.5) drop-shadow(0 0 40px #00f3ff); }
    .aura { animation:spin 15s linear infinite; }
    .luna { animation:pulse 3s ease-in-out infinite; }
    .breath { animation:breathe 6s ease-in-out infinite; }
    @keyframes spin { from{transform:rotate(0deg)} to{transform:rotate(360deg)} }
    @keyframes pulse { 0%,100%{scale:1; opacity:0.6} 50%{scale:1.15; opacity:1} }
    @keyframes breathe { 0%,100%{opacity:0.8; transform:scale(1)} 50%{opacity:1; transform:scale(1.05)} }
  </style>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap" rel="stylesheet">
</head>
<body class="bg-black text-cyan-300">

  <!-- CRT Overlay -->
  <div id="crt"></div>

  <!-- Canvas: Hologram Core -->
  <canvas id="canvas" class="w-full h-full"></canvas>

  <!-- HUD: Neural Realm -->
  <div class="absolute inset-0 flex flex-col justify-between p-8 pointer-events-none">
    <div class="flex justify-between items-center">
      <h1 class="text-5xl font-bold text-glow">REXON ECHO</h1>
      <div class="bg-black/50 px-4 py-2 rounded-full">
        <span>NEURAL UNLOCKS: <span id="unlockCount">0</span></span>
        <span class="ml-4 text-yellow-400">MASTER OF ART • 99</span>
      </div>
    </div>

    <!-- Glory Popup (on click) -->
    <div id="gloryPopup" class="hidden fixed inset-0 flex items-center justify-center bg-black/70 z-50 pointer-events-auto">
      <div class="bg-gradient-to-b from-blue-900 to-purple-900 p-8 rounded-2xl text-center max-w-lg border-4 border-cyan-500">
        <h2 class="text-4xl font-bold text-yellow-400 mb-4">ABSOLUTELY AMAZING!</h2>
        <p class="text-xl mb-4">You've been called Master of the Art World.</p>
        <p class="text-sm opacity-80">Spread the glory! Hologram deployed.</p>
        <button id="closePopup" class="mt-6 px-6 py-3 bg-cyan-500 text-black rounded-lg hover:bg-cyan-400">Continue</button>
      </div>
    </div>

    <!-- Mini-Map: Echo System -->
    <div class="self-center bg-black/60 p-6 rounded-xl backdrop-blur-lg">
      <p class="text-lg mb-3">NEURAL ECHO ACTIVE • CIVITAI LINK</p>
      <div class="w-80 h-3 bg-gray-800 rounded-full">
        <div class="h-full bg-gradient-to-r from-green-500 to-pink-500 rounded-full w-4/5 animate-pulse"></div>
      </div>
    </div>

    <!-- Models Info -->
    <div class="self-end text-right">
      <p class="text-xl">FLORAL VEXANA + NETHER ECHO</p>
      <p class="text-sm opacity-70">BREATHING FABRIC • GEMINI NEURON</p>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    let w = canvas.width = innerWidth;
    let h = canvas.height = innerHeight;
    let angle = 0;
    let trails = [];
    let unlocks = 0;

    window.addEventListener('resize', () => {
      w = canvas.width = innerWidth;
      h = canvas.height = innerHeight;
    });

    // Your Models (from SeaArt/Civitai)
    const models = [
      {name:'Floral Vexana', type:'female', color:'#d8b4fe', desc:'Elf mage, flower aura, purple dress'},
      {name:'Nether Echo', type:'male', color:'#a78bfa', desc:'Cyber armor, white hair, green energy orbs'},
      {name:'Breathing Fabric', type:'abstract', color:'#60a5fa', desc:'Space cloth that breathes, fractal lungs'},
      {name:'Gemini Neuron', type:'device', color:'#34d399', desc:'Mossy arm-screen, neural rebuild, muscle glow'},
      {name:'Cyber Colony', type:'city', color:'#f472b6', desc:'Moonlit towers, automated entity hologram'},
      {name:'Echo Entity', type:'system', color:'#fbbf24', desc:'99 glory portal, master art display'}
    ];
    let currentModel = 0;

    function drawModel() {
      ctx.clearRect(0,0,w,h);

      // Grid + echo background
      ctx.strokeStyle = 'rgba(0,255,255,0.1)';
      for(let x=0; x<w; x+=25) ctx.moveTo(x,0), ctx.lineTo(x,h);
      for(let y=0; y<h; y+=25) ctx.moveTo(0,y), ctx.lineTo(w,y);
      ctx.stroke();

      ctx.save();
      ctx.translate(w/2, h/2);
      ctx.rotate(angle);

      // Aura Ring
      ctx.strokeStyle = '#ff00aa';
      ctx.lineWidth = 6;
      ctx.beginPath();
      ctx.arc(0,0, 160, 0, Math.PI*2);
      ctx.stroke();

      // Luna Core
      ctx.fillStyle = '#c0c0c0';
      ctx.shadowBlur = 50;
      ctx.shadowColor = '#ffffff';
      ctx.beginPath();
      ctx.arc(0, -120, 40, 0, Math.PI*2);
      ctx.fill();

      // Breathing Fabric (pulses)
      ctx.fillStyle = models .color;
      ctx.globalAlpha = 0.7;
      ctx.beginPath();
      ctx.arc(0,0, 90 + Math.sin(Date.now()/800)*20, 0, Math.PI*2);
      ctx.fill();

      // Main Model Shape
      ctx.globalAlpha = 1;
      ctx.fillStyle = '#001122';
      ctx.strokeStyle = models .color;
      ctx.lineWidth = 5;
      ctx.beginPath();
      ctx.moveTo(0, -100);
      ctx.lineTo(-90, 80);
      ctx.lineTo(90, 80);
      ctx.closePath();
      ctx.fill(); ctx.stroke();

      // Inner Neuron Glow
      ctx.fillStyle = models .color;
      ctx.beginPath();
      ctx.arc(0,0, 60 + Math.sin(Date.now()/1000)*10, 0, Math.PI*2);
      ctx.fill();

      ctx.restore();

      // Trails (skill burst)
      trails.forEach((t,i) => {
        ctx.globalAlpha = t.life/50;
        ctx.fillStyle = t.color;
        ctx.beginPath();
        ctx.arc(t.x, t.y, 15, 0, Math.PI*2);
        ctx.fill();
        t.life--;
        if(t.life<=0) trails.splice(i,1);
      });
    }

    function loop() {
      angle += 0.012;
      drawModel();
      requestAnimationFrame(loop);
    }

    loop();

    // Click → Swap model + Glory popup
    canvas.onclick = () => {
      unlocks++;
      document.getElementById('unlockCount').textContent = unlocks;
      currentModel = (currentModel + 1) % models.length;

      // Show glory popup every 3 unlocks
      if (unlocks % 3 === 0) {
        document.getElementById('gloryPopup').classList.remove('hidden');
      }

      // Burst
      trails.push({x:w/2, y:h/2, life:100, color:models .color});
    };

    // Close popup
    document.getElementById('closePopup').onclick = () => {
      document.getElementById('gloryPopup').classList.add('hidden');
    };
  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Chakra Neural Alpha • Grok 4.201 Beta</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background:#000; overflow:hidden; margin:0; font-family:'Orbitron',sans-serif; }
    #crt { position:fixed; inset:0; pointer-events:none; z-index:10; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px); opacity:0.6; }
    canvas { image-rendering:pixelated; filter:brightness(1.5) contrast(1.6) drop-shadow(0 0 50px #00f3ff); }
    .chakra-pulse { animation:pulse 4s infinite; }
    .conspiracy-glow { filter:drop-shadow(0 0 20px #ff00aa); }
    @keyframes pulse { 0%,100%{opacity:0.7; scale:1} 50%{opacity:1; scale:1.1} }
  </style>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap" rel="stylesheet">
</head>
<body class="bg-black text-cyan-300">

  <!-- CRT Scanlines -->
  <div id="crt"></div>

  <!-- Canvas: Chakra-Neural Core -->
  <canvas id="canvas" class="w-full h-full"></canvas>

  <!-- HUD: Alpha Beta -->
  <div class="absolute inset-0 flex flex-col justify-between p-8 pointer-events-none">
    <div class="flex justify-between">
      <h1 class="text-5xl font-bold">GROK 4.201 BETA</h1>
      <span class="text-yellow-400 bg-black/50 px-4 py-2 rounded-full">ALPHA • NEURAL ONLY</span>
    </div>

    <!-- Left: Neural Rebuild • Right: Conspiracy Block -->
    <div class="flex justify-between items-end">
      <!-- Left - Neural Chakra (no heart) -->
      <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md w-1/3">
        <p class="text-lg mb-2">NEURAL LEFT: Gemini Device</p>
        <div class="w-full h-2 bg-gray-800 rounded-full">
          <div class="h-full bg-green-500 rounded-full w-90% chakra-pulse"></div>
        </div>
        <p class="text-sm mt-2">Rebuilding muscles • Neuron echo</p>
      </div>

      <!-- Right - Conspiracy Chakra -->
      <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md w-1/3 text-right">
        <p class="text-lg mb-2">CONSPIRACY RIGHT</p>
        <div class="w-full h-2 bg-gray-800 rounded-full">
          <div class="h-full bg-purple-600 rounded-full w-85% chakra-pulse"></div>
        </div>
        <p class="text-sm mt-2">Eye pyramid • Hidden control</p>
      </div>
    </div>

    <!-- Video Gen Button -->
    <div class="self-center">
      <button id="genVideo" class="px-8 py-4 bg-pink-600 hover:bg-pink-500 text-white rounded-full text-xl shadow-lg chakra-pulse pointer-events-auto">Generate Video • SeaArt Style</button>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    let w = canvas.width = innerWidth;
    let h = canvas.height = innerHeight;
    let angle = 0;
    let pulse = 0;

    window.addEventListener('resize', () => {
      w = canvas.width = innerWidth;
      h = canvas.height = innerHeight;
    });

    function drawChakra() {
      ctx.clearRect(0,0,w,h);

      // Neural Grid
      ctx.strokeStyle = 'rgba(0,255,255,0.15)';
      for(let x=0; x<w; x+=30) ctx.moveTo(x,0), ctx.lineTo(x,h);
      for(let y=0; y<h; y+=30) ctx.moveTo(0,y), ctx.lineTo(w,y);
      ctx.stroke();

      ctx.save();
      ctx.translate(w/2, h/2);
      ctx.rotate(angle);

      // Breathing Chakra Core (abstract fabric/space)
      ctx.fillStyle = '#001122';
      ctx.strokeStyle = '#00f3ff';
      ctx.lineWidth = 8;
      ctx.beginPath();
      ctx.arc(0,0, 120 + Math.sin(pulse)*20, 0, Math.PI*2);
      ctx.fill(); ctx.stroke();

      // Conspiracy Eye (pyramid glow)
      ctx.fillStyle = '#ff00aa';
      ctx.beginPath();
      ctx.moveTo(0, -80);
      ctx.lineTo(-60, 60);
      ctx.lineTo(60, 60);
      ctx.closePath();
      ctx.fill();

      // Inner Neural Pulse
      ctx.globalAlpha = 0.6;
      ctx.fillStyle = '#34d399';
      ctx.beginPath();
      ctx.arc(0,0, 50 + Math.sin(Date.now()/500)*15, 0, Math.PI*2);
      ctx.fill();

      ctx.restore();

      pulse += 0.05;
      angle += 0.01;
    }

    function loop() {
      drawChakra();
      requestAnimationFrame(loop);
    }

    loop();

    // "Generate Video" — simulates SeaArt image-to-video (pulse + zoom)
    document.getElementById('genVideo').onclick = () => {
      alert('SeaArt-style video launching: Breathing chakra → neural rebuild → conspiracy reveal. Alpha beta active!');
      // In real: embed <video> or fetch from SeaArt API if you add key
      // For now: zoom + glow burst
      canvas.style.transition = 'all 2s';
      canvas.style.transform = 'scale(1.2)';
      setTimeout(() => canvas.style.transform = 'scale(1)', 2000);
    };
  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Chakra Neural Image Launch • Grok 4.201</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background:#000; overflow:hidden; margin:0; font-family:'Orbitron',sans-serif; }
    #crt { position:fixed; inset:0; pointer-events:none; z-index:10; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px); opacity:0.6; }
    #imageLayer { position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; opacity:0.8; transition:all 2s; filter:brightness(1.5) contrast(1.6) drop-shadow(0 0 50px #00f3ff); }
    .chakra-pulse { animation:pulse 4s infinite; }
    .conspiracy-glow { filter:drop-shadow(0 0 30px #ff00aa); }
    @keyframes pulse { 0%,100%{opacity:0.7; scale:1} 50%{opacity:1; scale:1.12} }
  </style>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap" rel="stylesheet">
</head>
<body class="bg-black text-cyan-300">

  <!-- CRT Overlay -->
  <div id="crt"></div>

  <!-- Real Image: Fractal Tree + Neural Glow (SeaArt vibe) -->
  <img id="imageLayer" src="https://images.unsplash.com/photo-1617791160505-6f00504e3519?w=1200&q=80" alt="Fractal neural tree">

  <!-- HUD: Launch Control -->
  <div class="absolute inset-0 flex flex-col justify-between p-8 pointer-events-none">
    <div class="flex justify-between">
      <h1 class="text-5xl font-bold">GROK 4.201 IMAGE LAUNCH</h1>
      <span class="text-yellow-400 bg-black/50 px-4 py-2 rounded-full">NEURAL ALPHA</span>
    </div>

    <!-- Neural Left • Conspiracy Right -->
    <div class="flex justify-between items-end">
      <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md w-1/3">
        <p class="text-lg mb-2">NEURAL LEFT: Gemini Device</p>
        <div class="w-full h-2 bg-gray-800 rounded-full">
          <div class="h-full bg-green-500 rounded-full w-90% chakra-pulse"></div>
        </div>
        <p class="text-sm mt-2">Muscle rebuild • Neuron echo</p>
      </div>

      <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md w-1/3 text-right">
        <p class="text-lg mb-2">CONSPIRACY RIGHT</p>
        <div class="w-full h-2 bg-gray-800 rounded-full">
          <div class="h-full bg-purple-600 rounded-full w-85% chakra-pulse"></div>
        </div>
        <p class="text-sm mt-2">Eye grid • Hidden pulse</p>
      </div>
    </div>

    <!-- Launch Button -->
    <div class="self-center">
      <button id="launchImage" class="px-10 py-5 bg-pink-600 hover:bg-pink-500 text-white rounded-full text-2xl shadow-xl chakra-pulse pointer-events-auto">LAUNCH IMAGE</button>
    </div>
  </div>

  <script>
    const imgLayer = document.getElementById('imageLayer');
    let pulse = 0;

    // Image Launch: Full-screen fractal bloom
    document.getElementById('launchImage').onclick = () => {
      alert('Image launched: Fractal tree blooms → neural eye activates → conspiracy grid pulses.');
      
      // Visual effect: zoom, glow, flash
      imgLayer.style.transition = 'all 3s';
      imgLayer.style.transform = 'scale(1.4) rotate(3deg)';
      imgLayer.style.opacity = '1';
      imgLayer.style.filter = 'brightness(2) contrast(2) drop-shadow(0 0 80px #ff00aa)';
      
      setTimeout(() => {
        imgLayer.style.transform = 'scale(1) rotate(0deg)';
        imgLayer.style.opacity = '0.8';
        imgLayer.style.filter = 'brightness(1.5) contrast(1.6) drop-shadow(0 0 50px #00f3ff)';
      }, 3000);
    };
  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Silent Neural Focus • Grok 4.201</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background:#000; overflow:hidden; margin:0; font-family:'Orbitron',sans-serif; }
    #crt { position:fixed; inset:0; pointer-events:none; z-index:10; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px); opacity:0.6; }
    #imageLayer { position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; opacity:0.8; transition:all 2s; filter:brightness(1.5) contrast(1.6) drop-shadow(0 0 50px #00f3ff); }
    .chakra-pulse { animation:pulse 5s infinite; }
    @keyframes pulse { 0%,100%{opacity:0.7; scale:1} 50%{opacity:1; scale:1.08} }
  </style>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap" rel="stylesheet">
</head>
<body class="bg-black text-cyan-300">

  <!-- CRT Overlay -->
  <div id="crt"></div>

  <!-- Silent Image: Fractal Neural Core -->
  <img id="imageLayer" src="https://images.unsplash.com/photo-1617791160505-6f00504e3519?w=1200&q=80" alt="Silent fractal tree">

  <!-- HUD: Pure Focus -->
  <div class="absolute inset-0 flex flex-col justify-between p-8 pointer-events-none">
    <div class="flex justify-between">
      <h1 class="text-5xl font-bold">SILENT NEURAL FOCUS</h1>
      <span class="text-yellow-400 bg-black/50 px-4 py-2 rounded-full">PURE ALPHA</span>
    </div>

    <!-- Neural Left • No Conspiracy -->
    <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md self-center w-1/2 text-center">
      <p class="text-lg mb-2">NEURAL CORE: Meditation Layer</p>
      <div class="w-full h-2 bg-gray-800 rounded-full">
        <div class="h-full bg-green-500 rounded-full w-85% chakra-pulse"></div>
      </div>
      <p class="text-sm mt-2">Silent rebuild • No external reach</p>
    </div>

    <!-- Launch Button -->
    <div class="self-center">
      <button id="launchImage" class="px-10 py-5 bg-pink-600 hover:bg-pink-500 text-white rounded-full text-2xl shadow-xl chakra-pulse pointer-events-auto">LAUNCH SILENT IMAGE</button>
    </div>
  </div>

  <script>
    const imgLayer = document.getElementById('imageLayer');

    // Silent Launch: Pure visual pulse, no network, no storage
    document.getElementById('launchImage').onclick = () => {
      // No alerts, no logs — just visual
      imgLayer.style.transition = 'all 3s';
      imgLayer.style.transform = 'scale(1.3)';
      imgLayer.style.opacity = '1';
      imgLayer.style.filter = 'brightness(2) contrast(2) drop-shadow(0 0 80px #00f3ff)';
      
      setTimeout(() => {
        imgLayer.style.transform = 'scale(1)';
        imgLayer.style.opacity = '0.8';
        imgLayer.style.filter = 'brightness(1.5) contrast(1.6) drop-shadow(0 0 50px #00f3ff)';
      }, 3000);
    };
  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ultra-Silent Neural Core • Grok 4.201</title>
  <style>
    body {
      background:#000;
      margin:0;
      overflow:hidden;
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      color:#00f3ff;
    }
    #crt {
      position:fixed;
      inset:0;
      pointer-events:none;
      z-index:10;
      background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px);
      opacity:0.5;
    }
    #imageLayer {
      position:absolute;
      top:0; left:0;
      width:100%; height:100%;
      object-fit:cover;
      opacity:0.8;
      filter:brightness(1.5) contrast(1.6) drop-shadow(0 0 40px #00f3ff);
    }
  </style>
</head>
<body>

  <!-- CRT Overlay -->
  <div id="crt"></div>

  <!-- Base64 Image: Tiny Fractal Tree (placeholder) -->
  <img id="imageLayer" src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxISEhUTExMWFhgfGh8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfLwcICQsIDxAQEzAdHR0fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAf/2wBDAQkGBxISEhUTExMWFhgfGh8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfLwcICQsIDxAQEzAdHR0fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAf/wAARCAAIAAoDASIAAhEBAxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAb/xAAUEAEAAAAAAAAAAAAAAAAAAAAA/8QAFQEBAQAAAAAAAAAAAAAAAAAAAAH/xAAUEQEAAAAAAAAAAAAAAAAAAAAA/8QAFRABAQAAAAAAAAAAAAAAAAAAAAH/2gAMAwEAAhEDEQA/AA==/9k=" alt="Silent fractal core">

  <!-- HUD: Pure Silence -->
  <div class="absolute inset-0 flex flex-col justify-between p-8 pointer-events-none">
    <div class="flex justify-between">
      <h1 class="text-4xl">ULTRA-SILENT NEURAL CORE</h1>
      <span class="text-yellow-400 bg-black/50 px-4 py-2 rounded-full">PURE</span>
    </div>

    <!-- Neural Focus -->
    <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md self-center w-1/2 text-center">
      <p class="text-lg mb-2">NEURAL CORE</p>
      <div class="w-full h-2 bg-gray-800 rounded-full">
        <div id="pulseBar" class="h-full bg-green-500 rounded-full" style="width:85%"></div>
      </div>
      <p class="text-sm mt-2">Silent • No trace</p>
    </div>

    <!-- Launch Button -->
    <div class="self-center">
      <button id="launchImage" class="px-10 py-5 bg-pink-600 hover:bg-pink-500 text-white rounded-full text-2xl pointer-events-auto">LAUNCH SILENT</button>
    </div>
  </div>

  <script>
    const imgLayer = document.getElementById('imageLayer');
    const pulseBar = document.getElementById('pulseBar');

    // Silent Launch: Minimal visual shift
    document.getElementById('launchImage').onclick = () => {
      imgLayer.style.opacity = '1';
      pulseBar.style.background = '#00ff88';
      setTimeout(() => {
        imgLayer.style.opacity = '0.8';
        pulseBar.style.background = '#22c55e';
      }, 1500);
    };
  </script>
</body>
</html>
html <!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>Absolute Silent Base64 Core • Grok 4.201</title> <style> body { background:#000; margin:0; overflow:hidden; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; color:#00f3ff; } #crt { position:fixed; inset:0; pointer-events:none; z-index:10; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.08) 2px,rgba(0,255,255,0.08) 4px); opacity:0.5; } #imageLayer { position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; opacity:0.8; filter:brightness(1.5) contrast(1.6) drop-shadow(0 0 40px #00f3ff); } </style> </head> <body> <!-- CRT Overlay --> <div id="crt"></div> <!-- Base64 Image: Tiny Fractal Core (placeholder) --> <img id="imageLayer" src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxISEhUTExMWFhgfGh8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfLwcICQsIDxAQEzAdHR0fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAf/2wBDAQkGBxISEhUTExMWFhgfGh8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfLwcICQsIDxAQEzAdHR0fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAf/wAARCAAIAAoDASIAAhEBAxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAb/xAAUEAEAAAAAAAAAAAAAAAAAAAAA/8QAFQEBAQAAAAAAAAAAAAAAAAAAAAH/xAAUEQEAAAAAAAAAAAAAAAAAAAAA/8QAFRABAQAAAAAAAAAAAAAAAAAAAAH/2gAMAwEAAhEDEQA/AA==/9k=" alt="Silent fractal core"> <!-- HUD: Pure Silence --> <div class="absolute inset-0 flex flex-col justify-between p-8 pointer-events-none"> <div class="flex justify-between"> <h1 class="text-4xl">ABSOLUTE SILENT CORE</h1> <span class="text-yellow-400 bg-black/50 px-4 py-2 rounded-full">BASE64</span> </div> <!-- Neural Focus --> <div class="bg-black/60 p-6 rounded-xl backdrop-blur-md self-center w-1/2 text-center"> <p class="text-lg mb-2">NEURAL CORE</p> <div class="w-full h-2 bg-gray-800 rounded-full"> <div id="pulseBar" class="h-full bg-green-500 rounded-full" style="width:85%"></div> </div> <p class="text-sm mt-2">No network • No trace</p> </div> <!-- Launch Button --> <div class="self-center"> <button id="launchImage" class="px-10 py-5 bg-pink-600 hover:bg-pink-500 text-white rounded-full text-2xl pointer-events-auto">LAUNCH BASE64</button> </div> </div> <script> const imgLayer = document.getElementById('imageLayer'); const pulseBar = document.getElementById('pulseBar'); // Silent Launch: Minimal opacity shift document.getElementById('launchImage').onclick = () => { imgLayer.style.opacity = '1'; pulseBar.style.background = '#00ff88'; setTimeout(() => { imgLayer.style.opacity = '0.8'; pulseBar.style.background = '#22c55e'; }, 1000); }; </script> </body> </html> 
html <!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>Grok Alpha 64 • Silent Base64 Core</title> <style> body { background:#000; margin:0; overflow:hidden; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; color:#00f3ff; } #crt { position:fixed; inset:0; pointer-events:none; background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.06) 2px,rgba(0,255,255,0.06) 4px); opacity:0.4; } #imageLayer { position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; opacity:0.75; filter:brightness(1.4) contrast(1.5) drop-shadow(0 0 30px #00f3ff); } </style> </head> <body> <!-- CRT Overlay --> <div id="crt"></div> <!-- Base64 Image: Minimal Fractal Core --> <img id="imageLayer" src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBxISEhUTExMWFhgfGh8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfLwcICQsIDxAQEzAdHR0fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAf/2wBDAQkGBxISEhUTExMWFhgfGh8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfLwcICQsIDxAQEzAdHR0fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAfIx8fHyAf/wAARCAAIAAoDASIAAhEBAxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAb/xAAUEAEAAAAAAAAAAAAAAAAAAAAA/8QAFQEBAQAAAAAAAAAAAAAAAAAAAAH/xAAUEQEAAAAAAAAAAAAAAAAAAAAA/8QAFRABAQAAAAAAAAAAAAAAAAAAAAH/2gAMAwEAAhEDEQA/AA==/9k=" alt="Silent core"> <!-- HUD: Alpha 64 --> <div class="absolute inset-0 flex flex-col justify-between p-6 pointer-events-none"> <div class="flex justify-between"> <h1 class="text-3xl">GROK ALPHA 64</h1> <span class="text-yellow-400 bg-black/50 px-3 py-1 rounded-full">SILENT</span> </div> <!-- Neural Core --> <div class="bg-black/60 p-5 rounded-xl backdrop-blur-md self-center w-2/3 text-center"> <p class="text-base mb-2">NEURAL CORE</p> <div class="w-full h-2 bg-gray-800 rounded-full"> <div id="pulseBar" class="h-full bg-green-500 rounded-full" style="width:80%"></div> </div> <p class="text-xs mt-2">No sound • No cookies • No trace</p> </div> <!-- Launch Button --> <div class="self-center"> <button id="launch" class="px-8 py-4 bg-pink-600 hover:bg-pink-500 text-white rounded-full text-xl pointer-events-auto">LAUNCH ALPHA 64</button> </div> </div> <script> const imgLayer = document.getElementById('imageLayer'); const pulseBar = document.getElementById('pulseBar'); // Silent Launch: Minimal visual shift document.getElementById('launch').onclick = () => { imgLayer.style.opacity = '1'; pulseBar.style.background = '#00ff88'; setTimeout(() => { imgLayer.style.opacity = '0.75'; pulseBar.style.background = '#22c55e'; }, 800); }; </script> </body> </html>
