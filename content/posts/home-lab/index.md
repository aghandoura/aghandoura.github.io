<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Project Sentinel: Home Server Build Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* Custom Scrollbar for a cleaner look */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f5f5f4; 
        }
        ::-webkit-scrollbar-thumb {
            background: #d6d3d1; 
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #a8a29e; 
        }

        /* Chart Container Strict Styling */
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 350px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 350px;
            }
        }
        
        /* Utility for the 'Virtual Case' interaction */
        .component-slot {
            transition: all 0.3s ease;
        }
        .component-slot:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
    </style>
    <!-- Chosen Palette: Warm Technical (Stone, Amber, Teal) -->
    <!-- Application Structure Plan: 
         1. Header: Project Identity.
         2. Status Dashboard: High-level donut chart (Ordered vs Needed) + Key Alerts (Dimensions/Power).
         3. Virtual Workbench: Interactive list of components categorized by subsystem. Used to explore specs.
         4. Power Simulation: Bar chart comparing Current vs Future (GPU) power draw to validate PSU choice.
         5. Architect's Notes: Textual analysis of the specific conflicts/checks requested by the user.
         6. Procurement Checklist: Interactive functional checklist for the remaining items.
         This structure moves from "Status" -> "Detail" -> "Analysis" -> "Action", facilitating understanding of the build state.
    -->
    <!-- Visualization & Content Choices:
         - Status Donut: Quick visual check of project completeness. (Chart.js)
         - Power Bar Chart: Comparative analysis to justify the 850W PSU choice for future proofing. (Chart.js)
         - Virtual Workbench: HTML/Tailwind Grid to organize complex specs into digestible cards.
         - Interaction: Javascript filtering and state management to update charts and text dynamically.
    -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
</head>
<body class="bg-stone-50 text-stone-800 font-sans antialiased selection:bg-amber-200 selection:text-amber-900">

    <!-- Navigation / Header -->
    <header class="bg-white border-b border-stone-200 sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center gap-3">
                <div class="w-8 h-8 bg-amber-500 rounded flex items-center justify-center text-white font-bold">S</div>
                <h1 class="text-xl font-bold tracking-tight text-stone-900">Project Sentinel <span class="text-stone-400 font-normal">| Home Server Build</span></h1>
            </div>
            <div class="text-sm font-medium text-stone-500 hidden sm:block">
                Target: Silent ZFS NAS + Compute
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 space-y-8">

        <!-- Introduction Section -->
        <section>
            <div class="prose prose-stone max-w-none">
                <p class="text-lg text-stone-600 leading-relaxed">
                    This interactive blueprint analyzes your current server component selection. It tracks procurement status, 
                    simulates power consumption scenarios (Idle vs. Load vs. Future GPU), and validates physical compatibility 
                    within the Fractal Design Define 7 XL chassis. Use the sections below to review your hardware architecture.
                </p>
            </div>
        </section>

        <!-- KPI & High Level Status -->
        <section class="grid grid-cols-1 md:grid-cols-12 gap-6">
            <!-- Procurement Status Chart -->
            <div class="md:col-span-4 bg-white rounded-xl border border-stone-200 p-6 shadow-sm flex flex-col">
                <h2 class="text-sm font-semibold text-stone-500 uppercase tracking-wider mb-4">Procurement Status</h2>
                <div class="flex-grow flex items-center justify-center">
                    <div class="chart-container">
                        <canvas id="statusChart"></canvas>
                    </div>
                </div>
                <div class="mt-4 text-center">
                    <span id="completion-text" class="text-2xl font-bold text-stone-900">0%</span>
                    <span class="text-stone-500 text-sm">Components Acquired</span>
                </div>
            </div>

            <!-- Architect's Critical Review (The "Missing/Conflict" Check) -->
            <div class="md:col-span-8 bg-white rounded-xl border border-stone-200 p-6 shadow-sm">
                <div class="flex items-center gap-2 mb-4">
                    <span class="text-xl">🧐</span>
                    <h2 class="text-sm font-semibold text-stone-500 uppercase tracking-wider">Architect's Compatibility Analysis</h2>
                </div>
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <!-- Analysis Card 1 -->
                    <div class="bg-amber-50 rounded-lg p-4 border border-amber-100">
                        <h3 class="font-bold text-amber-800 text-sm mb-1">Fan Hub Redundancy</h3>
                        <p class="text-sm text-amber-900/80">
                            You listed "PWM Fan Hub" as needed. <strong>Good news:</strong> The Fractal Define 7 XL comes with the <em>Nexus+ 2 Fan Hub</em> pre-installed. You likely do not need to buy an external one.
                        </p>
                    </div>
                    <!-- Analysis Card 2 -->
                    <div class="bg-stone-100 rounded-lg p-4 border border-stone-200">
                        <h3 class="font-bold text-stone-700 text-sm mb-1">Dimensions & Fit</h3>
                        <p class="text-sm text-stone-600">
                            <strong>Motherboard:</strong> mATX board in an XL case will fit but leave massive empty space. 
                            <strong>GPU:</strong> 549mm clearance is ample for an RTX 5090 (<400mm).
                            <strong>Cables:</strong> Ensure PSU CPU cables are long enough.
                        </p>
                    </div>
                    <!-- Analysis Card 3 -->
                    <div class="bg-teal-50 rounded-lg p-4 border border-teal-100 sm:col-span-2">
                        <h3 class="font-bold text-teal-800 text-sm mb-1">Power Strategy</h3>
                        <p class="text-sm text-teal-900/80">
                            The <strong>850W Platinum PSU</strong> is perfect. Current build will idle around ~40W. Even with a future 450W GPU, you stay in the efficiency sweet spot (60-70% load).
                        </p>
                    </div>
                </div>
            </div>
        </section>

        <!-- Power Simulation Section -->
        <section class="bg-white rounded-xl border border-stone-200 p-6 shadow-sm">
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 gap-4">
                <div>
                    <h2 class="text-lg font-bold text-stone-900">Power Consumption Simulation</h2>
                    <p class="text-sm text-stone-500">Estimated wattage based on TDP and typical component behavior.</p>
                </div>
                <div class="flex gap-2">
                    <button onclick="toggleGpuMode()" id="gpu-toggle-btn" class="px-4 py-2 bg-stone-100 hover:bg-stone-200 text-stone-700 text-sm font-medium rounded-lg transition-colors border border-stone-300">
                        Add Future RTX 5090
                    </button>
                </div>
            </div>
            
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <div class="lg:col-span-2">
                    <div class="chart-container">
                        <canvas id="powerChart"></canvas>
                    </div>
                </div>
                <div class="flex flex-col justify-center space-y-4">
                    <div class="p-4 bg-stone-50 rounded-lg border border-stone-200">
                        <div class="text-xs text-stone-500 uppercase font-semibold">PSU Capacity</div>
                        <div class="text-2xl font-bold text-stone-800">850W <span class="text-sm font-normal text-stone-500">Titanium/Platinum</span></div>
                        <div class="w-full bg-stone-200 h-2 rounded-full mt-2 overflow-hidden">
                            <div id="psu-load-bar" class="bg-teal-500 h-full transition-all duration-500" style="width: 20%"></div>
                        </div>
                        <div class="text-xs text-right mt-1 text-stone-500" id="psu-load-text">~20% Load</div>
                    </div>
                    <div class="text-sm text-stone-600">
                        <p class="mb-2"><strong>Efficiency Note:</strong> At low loads (Server Idle), the efficiency is lower, but absolute waste is minimal. At high loads (Gaming/Compute), the PSU hits peak efficiency.</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- Component Workbench -->
        <section>
            <div class="flex items-center justify-between mb-6">
                <h2 class="text-lg font-bold text-stone-900">Component Workbench</h2>
                <div class="flex gap-2 text-sm">
                    <span class="flex items-center gap-1"><span class="w-3 h-3 rounded-full bg-teal-500"></span> Ordered</span>
                    <span class="flex items-center gap-1"><span class="w-3 h-3 rounded-full bg-amber-500"></span> Missing</span>
                </div>
            </div>

            <!-- Tab Navigation (JS Controlled) -->
            <div class="flex gap-2 overflow-x-auto pb-2 mb-4 border-b border-stone-200">
                <button onclick="filterComponents('all')" class="tab-btn active px-4 py-2 text-sm font-medium text-stone-900 border-b-2 border-stone-900">All</button>
                <button onclick="filterComponents('core')" class="tab-btn px-4 py-2 text-sm font-medium text-stone-500 hover:text-stone-900 transition-colors">Core (CPU/RAM)</button>
                <button onclick="filterComponents('storage')" class="tab-btn px-4 py-2 text-sm font-medium text-stone-500 hover:text-stone-900 transition-colors">Storage</button>
                <button onclick="filterComponents('cooling')" class="tab-btn px-4 py-2 text-sm font-medium text-stone-500 hover:text-stone-900 transition-colors">Cooling & Case</button>
            </div>

            <!-- Component Grid -->
            <div id="component-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                <!-- JS will populate this -->
            </div>
        </section>

        <!-- Missing Items Checklist -->
        <section class="bg-amber-50 border border-amber-200 rounded-xl p-6">
            <h2 class="text-lg font-bold text-amber-900 mb-4">To-Order Checklist</h2>
            <p class="text-sm text-amber-800 mb-4">Review these items to complete your build.</p>
            <div id="checklist-container" class="space-y-3">
                <!-- JS Populated -->
            </div>
        </section>

    </main>

    <footer class="bg-stone-100 border-t border-stone-200 py-8 mt-12">
        <div class="max-w-7xl mx-auto px-4 text-center text-stone-500 text-sm">
            <p>Generated based on user requirements for Silent Home Server.</p>
        </div>
    </footer>

    <!-- Logic Script -->
    <script>
        // --- Data Store ---
        const buildData = [
            { id: 1, category: 'core', name: 'Intel Xeon E-2436', spec: '6C/12T, 65W TDP', status: 'ordered', icon: '🧠', price: 350 },
            { id: 2, category: 'core', name: 'Supermicro X13SCL-F', spec: 'mATX, LGA1700, IPMI', status: 'ordered', icon: '🔌', price: 280 },
            { id: 3, category: 'core', name: '32GB DDR5 ECC UDIMM', spec: '2x16GB 4800MT/s', status: 'ordered', icon: '💾', price: 180 },
            { id: 4, category: 'cooling', name: 'Noctua NH-U12A', spec: 'Chromax Black, 158mm', status: 'ordered', icon: '❄️', price: 120 },
            { id: 5, category: 'cooling', name: 'Fractal Define 7 XL', spec: 'Full Tower, Sound Dampened', status: 'ordered', icon: '📦', price: 230 },
            { id: 6, category: 'power', name: 'be quiet! Straight Power 12', spec: '850W Platinum', status: 'ordered', icon: '⚡', price: 160 },
            { id: 7, category: 'cooling', name: 'Noctua NF-A14x25 G2', spec: '2x 140mm PWM (Front)', status: 'ordered', icon: '💨', price: 70 },
            { id: 8, category: 'cooling', name: 'Noctua NF-A12 PWM', spec: '1x 120mm PWM (Rear)', status: 'ordered', icon: '💨', price: 30 },
            { id: 9, category: 'storage', name: 'System SSD', spec: '960GB SATA (Estimated)', status: 'missing', icon: '🚀', price: 80, notes: "Rec: Samsung 870 EVO or Crucial MX500" },
            { id: 10, category: 'storage', name: 'NAS HDD x2', spec: '10TB WD Red/Ultrastar', status: 'missing', icon: '💽', price: 400, notes: "Rec: CMR drives only. No SMR." },
            { id: 11, category: 'misc', name: 'SATA Data Cables', spec: '2-Pack', status: 'missing', icon: '🧵', price: 10, notes: "Check mobo box first." },
            { id: 12, category: 'misc', name: 'Anti-Vibration Mounts', spec: 'Silicone Grommets', status: 'missing', icon: '🔇', price: 15, notes: "Optional, case has good trays." }
        ];

        // --- State Management ---
        let gpuIncluded = false;
        let currentFilter = 'all';

        // --- DOM Elements ---
        const grid = document.getElementById('component-grid');
        const checklist = document.getElementById('checklist-container');
        const psuBar = document.getElementById('psu-load-bar');
        const psuText = document.getElementById('psu-load-text');
        const gpuBtn = document.getElementById('gpu-toggle-btn');
        const completionText = document.getElementById('completion-text');

        // --- Chart Instances ---
        let powerChartInst = null;
        let statusChartInst = null;

        // --- Initialization ---
        document.addEventListener('DOMContentLoaded', () => {
            renderComponents();
            renderChecklist();
            initCharts();
            updateStats();
        });

        // --- Core Functions ---

        function updateStats() {
            const total = buildData.length;
            const ordered = buildData.filter(i => i.status === 'ordered').length;
            const percent = Math.round((ordered / total) * 100);
            
            // Animate text
            let current = 0;
            const interval = setInterval(() => {
                if (current >= percent) {
                    clearInterval(interval);
                    completionText.innerText = `${percent}%`;
                } else {
                    current++;
                    completionText.innerText = `${current}%`;
                }
            }, 20);

            // Update Status Chart
            if (statusChartInst) {
                statusChartInst.data.datasets[0].data = [ordered, total - ordered];
                statusChartInst.update();
            }
        }

        function filterComponents(category) {
            currentFilter = category;
            
            // Update Tab Styles
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.classList.remove('text-stone-900', 'border-stone-900', 'border-b-2');
                btn.classList.add('text-stone-500');
                if ((category === 'all' && btn.innerText === 'All') || 
                    btn.innerText.toLowerCase().includes(category)) {
                    btn.classList.remove('text-stone-500');
                    btn.classList.add('text-stone-900', 'border-stone-900', 'border-b-2');
                }
            });

            renderComponents();
        }

        function renderComponents() {
            grid.innerHTML = '';
            
            const filtered = currentFilter === 'all' 
                ? buildData 
                : buildData.filter(item => item.category === currentFilter);

            filtered.forEach(item => {
                const isOrdered = item.status === 'ordered';
                const statusColor = isOrdered ? 'bg-teal-100 text-teal-800 border-teal-200' : 'bg-amber-100 text-amber-800 border-amber-200';
                
                const card = document.createElement('div');
                card.className = `component-slot bg-white rounded-lg border border-stone-200 p-4 flex flex-col gap-2 relative overflow-hidden`;
                
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div class="text-2xl">${item.icon}</div>
                        <span class="text-xs px-2 py-1 rounded-full border ${statusColor} font-medium capitalize">
                            ${item.status}
                        </span>
                    </div>
                    <div>
                        <h3 class="font-bold text-stone-800">${item.name}</h3>
                        <p class="text-sm text-stone-500">${item.spec}</p>
                    </div>
                    ${item.notes ? `<p class="text-xs text-stone-400 mt-auto pt-2 border-t border-stone-100">Note: ${item.notes}</p>` : ''}
                `;
                grid.appendChild(card);
            });
        }

        function renderChecklist() {
            checklist.innerHTML = '';
            const missing = buildData.filter(i => i.status === 'missing');
            
            if (missing.length === 0) {
                checklist.innerHTML = `<div class="p-4 bg-teal-50 text-teal-800 rounded">🎉 All parts ordered! Time to build.</div>`;
                return;
            }

            missing.forEach(item => {
                const row = document.createElement('div');
                row.className = "flex items-center justify-between p-3 bg-white rounded border border-stone-200 shadow-sm";
                row.innerHTML = `
                    <div class="flex items-center gap-3">
                        <input type="checkbox" class="w-5 h-5 text-amber-500 border-stone-300 rounded focus:ring-amber-500" onchange="toggleItemStatus(${item.id})">
                        <div class="flex flex-col">
                            <span class="font-medium text-stone-800">${item.name}</span>
                            <span class="text-xs text-stone-500">${item.spec}</span>
                        </div>
                    </div>
                    <span class="text-sm font-semibold text-stone-400">~$${item.price}</span>
                `;
                checklist.appendChild(row);
            });
        }

        function toggleItemStatus(id) {
            // In a real app, this would persist. Here we just flip it in memory for the session.
            const idx = buildData.findIndex(i => i.id === id);
            if (idx > -1) {
                buildData[idx].status = 'ordered';
                renderComponents();
                renderChecklist();
                updateStats();
            }
        }

        function toggleGpuMode() {
            gpuIncluded = !gpuIncluded;
            
            // Update Button
            if (gpuIncluded) {
                gpuBtn.innerHTML = "Remove GPU";
                gpuBtn.classList.remove('bg-stone-100', 'text-stone-700');
                gpuBtn.classList.add('bg-teal-600', 'text-white', 'hover:bg-teal-700');
            } else {
                gpuBtn.innerHTML = "Add Future RTX 5090";
                gpuBtn.classList.add('bg-stone-100', 'text-stone-700');
                gpuBtn.classList.remove('bg-teal-600', 'text-white', 'hover:bg-teal-700');
            }

            updatePowerChart();
        }

        function updatePowerChart() {
            const baseIdle = 45; // CPU + Drives Idle
            const baseLoad = 130; // CPU Max + Drives Spin up
            const gpuIdle = 20;
            const gpuLoad = 450; // RTX 5090 estimate

            const idle = gpuIncluded ? baseIdle + gpuIdle : baseIdle;
            const load = gpuIncluded ? baseLoad + gpuLoad : baseLoad;
            
            const psuLimit = 850;
            const loadPercentage = Math.round((load / psuLimit) * 100);

            // Update Chart
            powerChartInst.data.datasets[0].data = [idle, load];
            powerChartInst.update();

            // Update Progress Bar
            psuBar.style.width = `${loadPercentage}%`;
            psuText.innerHTML = `Peak Load: ${load}W (~${loadPercentage}%)`;
            
            if (loadPercentage > 80) {
                psuBar.classList.remove('bg-teal-500');
                psuBar.classList.add('bg-red-500');
            } else {
                psuBar.classList.add('bg-teal-500');
                psuBar.classList.remove('bg-red-500');
            }
        }

        function initCharts() {
            // 1. Status Donut Chart
            const ctxStatus = document.getElementById('statusChart').getContext('2d');
            statusChartInst = new Chart(ctxStatus, {
                type: 'doughnut',
                data: {
                    labels: ['Ordered', 'Left to Buy'],
                    datasets: [{
                        data: [1, 1], // Placeholder, updated by updateStats
                        backgroundColor: ['#14b8a6', '#f59e0b'], // Teal-500, Amber-500
                        borderWidth: 0,
                        hoverOffset: 4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    cutout: '70%',
                    plugins: {
                        legend: { position: 'bottom', labels: { usePointStyle: true, font: { family: 'sans-serif' } } },
                        tooltip: { callbacks: { label: (context) => ` ${context.label}: ${context.raw} Items` } }
                    }
                }
            });

            // 2. Power Bar Chart
            const ctxPower = document.getElementById('powerChart').getContext('2d');
            powerChartInst = new Chart(ctxPower, {
                type: 'bar',
                data: {
                    labels: ['System Idle', 'System Max Load'],
                    datasets: [{
                        label: 'Power Draw (Watts)',
                        data: [45, 130],
                        backgroundColor: ['#d6d3d1', '#0f766e'], // Stone-300, Teal-700
                        borderRadius: 6,
                        barThickness: 50
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { 
                            beginAtZero: true, 
                            max: 900,
                            grid: { color: '#f5f5f4' },
                            title: { display: true, text: 'Watts (W)' }
                        },
                        x: { grid: { display: false } }
                    },
                    plugins: {
                        legend: { display: false },
                        annotation: {
                            annotations: {
                                line1: {
                                    type: 'line',
                                    yMin: 850,
                                    yMax: 850,
                                    borderColor: 'rgb(255, 99, 132)',
                                    borderWidth: 2,
                                    borderDash: [5, 5],
                                    label: { content: 'PSU Limit (850W)', enabled: true, position: 'start' }
                                }
                            }
                        }
                    }
                }
            });
            
            // Initial render call for Power Chart to set basic state
            updatePowerChart();
        }

    </script>
</body>
</html>