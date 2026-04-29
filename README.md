
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Nutricional: Enfoque Escolar & Gym</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #FAFAF9; /* Tailwind stone-50 */
            color: #292524; /* Tailwind stone-800 */
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 300px;
            margin-left: auto;
            margin-right: auto;
            height: 250px;
            max-height: 250px;
        }
        .glass-panel {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03);
        }
        .tab-btn.active {
            background-color: #0D9488; /* teal-600 */
            color: white;
            border-color: #0D9488;
        }
        .water-glass {
            transition: height 0.3s ease-in-out;
        }
    </style>
    <!-- Chosen Palette: Warm Neutrals (Stone) with Teal (Health/Energy) and Sky Blue (Hydration) Accents -->
    <!-- Application Structure Plan: A dynamic, dashboard-style SPA to optimize consumption of the nutrition plan. Instead of a static list, it uses a chronological "Meal Selector" (tabbed interface) to show context-specific meals. A sidebar provides persistent daily goals (Interactive Hydration Tracker, Golden Rules Checklist, and a dynamic Macro Chart reflecting the selected meal). This structure allows the user to focus on their immediate next meal while keeping daily goals in sight, perfect for a busy student/athlete. -->
    <!-- Visualization & Content Choices:
         1. Meal Details (Text/Lists): Goal is to Inform. Presented via dynamic JS updates to a central card based on time-of-day selection.
         2. Macronutrient Balance (Chart.js Donut): Goal is to Compare/Inform. Shows estimated macro ratios for the selected meal to teach nutritional balance visually without overwhelming with exact grams. NO SVG used (Canvas).
         3. Hydration Tracker (Interactive CSS/HTML bar): Goal is to Inform/Change behavior. Allows user to click to add water, gamifying the 2.5-3L goal.
         4. Golden Rules (Interactive Checklist): Goal is to Organize/Remind. A simple to-do list format for daily habits. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
</head>
<body class="min-h-screen p-4 md:p-8">

<header class="max-w-6xl mx-auto mb-8 text-center md:text-left">
    <h1 class="text-3xl md:text-4xl font-bold text-stone-800 tracking-tight flex items-center justify-center md:justify-start gap-3">
        <span class="text-4xl">🥗</span> Plan de Nutrición Pro
    </h1>
    <p class="text-stone-500 mt-2 text-lg max-w-2xl">
        Optimiza tu rendimiento académico y físico. Este dashboard interactivo prioriza la saciedad, la energía constante para la escuela y la recuperación muscular para el gym.
    </p>
</header>

<main class="max-w-6xl mx-auto grid grid-cols-1 lg:grid-cols-3 gap-8">

    <!-- Left Column: Timeline & Meal Details -->
    <div class="lg:col-span-2 space-y-6">

        <section aria-labelledby="timeline-heading">
            <h2 id="timeline-heading" class="sr-only">Línea de Tiempo de Comidas</h2>
            <p class="text-sm text-stone-500 mb-3 px-1">Selecciona un momento del día para ver tu plan específico:</p>

            <!-- Meal Navigation -->
            <div class="flex flex-wrap gap-2 mb-6" id="meal-nav">
                <button data-meal="desayuno" class="tab-btn active px-4 py-2 rounded-full border border-stone-300 font-medium text-sm transition-colors hover:bg-stone-100 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-offset-2">🌅 Desayuno</button>
                <button data-meal="snack1" class="tab-btn px-4 py-2 rounded-full border border-stone-300 font-medium text-sm transition-colors hover:bg-stone-100 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-offset-2">🥪 Snack 1</button>
                <button data-meal="snack2" class="tab-btn px-4 py-2 rounded-full border border-stone-300 font-medium text-sm transition-colors hover:bg-stone-100 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-offset-2">🍎 Snack 2</button>
                <button data-meal="comida" class="tab-btn px-4 py-2 rounded-full border border-stone-300 font-medium text-sm transition-colors hover:bg-stone-100 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-offset-2">🍽️ Comida</button>
                <button data-meal="cena" class="tab-btn px-4 py-2 rounded-full border border-stone-300 font-medium text-sm transition-colors hover:bg-stone-100 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-offset-2">🌙 Cena</button>
            </div>

            <!-- Meal Display Card -->
            <div class="glass-panel rounded-2xl p-6 md:p-8 min-h-[400px] flex flex-col justify-between transition-opacity duration-300" id="meal-display">
                <div>
                    <div class="flex items-center justify-between border-b border-stone-200 pb-4 mb-5">
                        <div>
                            <span class="text-xs font-bold uppercase tracking-wider text-teal-600 mb-1 block" id="meal-time-badge">En Casa</span>
                            <h3 class="text-2xl font-bold text-stone-800" id="meal-title">🌅 Desayuno: Energía Base</h3>
                        </div>
                    </div>

                    <p class="text-stone-600 mb-6 italic" id="meal-desc">El motor para empezar el día.</p>

                    <ul class="space-y-4 mb-8" id="meal-items">
                        <!-- Injected via JS -->
                    </ul>
                </div>

                <div class="bg-teal-50 rounded-xl p-4 border border-teal-100" id="meal-tip-container">
                    <div class="flex items-start gap-3">
                        <span class="text-xl">💡</span>
                        <div>
                            <h4 class="font-bold text-teal-800 text-sm mb-1">Tip de Preparación</h4>
                            <p class="text-teal-700 text-sm" id="meal-tip">Prepara la avena la noche anterior (Overnight Oats) si tienes prisa por la mañana.</p>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div>

    <!-- Right Column: Stats & Rules -->
    <div class="space-y-6">

        <!-- Macro Breakdown Chart -->
        <section class="glass-panel rounded-2xl p-6">
            <h3 class="text-lg font-bold text-stone-800 mb-2">Balance de Macros</h3>
            <p class="text-xs text-stone-500 mb-4">Proporción estimada para la comida seleccionada. Ayuda a entender la función de esta comida.</p>
            <div class="chart-container">
                <canvas id="macroChart"></canvas>
            </div>
        </section>

        <!-- Hydration Tracker -->
        <section class="glass-panel rounded-2xl p-6">
            <div class="flex justify-between items-end mb-4">
                <div>
                    <h3 class="text-lg font-bold text-stone-800">Hidratación</h3>
                    <p class="text-xs text-stone-500">Mínimo 2.5 - 3 L diarios.</p>
                </div>
                <div class="text-xl font-bold text-sky-500" id="water-count">0 / 12 vasos</div>
            </div>

            <div class="flex gap-4 items-center">
                <!-- Visual Glass -->
                <div class="w-16 h-24 border-4 border-sky-200 rounded-b-lg relative overflow-hidden bg-stone-50 cursor-pointer" id="water-glass-container" onclick="addWater()">
                    <div class="water-glass absolute bottom-0 left-0 w-full bg-sky-400 opacity-80" id="water-level" style="height: 0%;"></div>
                </div>
                <div>
                    <button onclick="addWater()" class="bg-sky-100 hover:bg-sky-200 text-sky-700 px-4 py-2 rounded-lg text-sm font-semibold transition-colors flex items-center gap-2">
                        <span>💧</span> Agregar Vaso (250ml)
                    </button>
                    <p class="text-xs text-stone-400 mt-2 max-w-[150px]">El cerebro deshidratado no programa bien.</p>
                </div>
            </div>
        </section>

        <!-- Golden Rules -->
        <section class="glass-panel rounded-2xl p-6">
            <h3 class="text-lg font-bold text-stone-800 mb-4 flex items-center gap-2"><span>🛠️</span> Reglas de Oro</h3>
            <p class="text-xs text-stone-500 mb-4">Hábitos diarios del ingeniero fitness.</p>

            <div class="space-y-3">
                <label class="flex items-start gap-3 cursor-pointer group">
                    <input type="checkbox" class="mt-1 w-5 h-5 rounded border-stone-300 text-teal-600 focus:ring-teal-500">
                    <span class="text-sm text-stone-700 group-hover:text-stone-900"><strong>Batch Cooking:</strong> Cocina proteínas el domingo para armar snacks en la semana.</span>
                </label>
                <label class="flex items-start gap-3 cursor-pointer group">
                    <input type="checkbox" class="mt-1 w-5 h-5 rounded border-stone-300 text-teal-600 focus:ring-teal-500">
                    <span class="text-sm text-stone-700 group-hover:text-stone-900"><strong>Cero Sal Extra:</strong> Evita cacahuates "japoneses" y exceso de sodio.</span>
                </label>
                <label class="flex items-start gap-3 cursor-pointer group">
                    <input type="checkbox" class="mt-1 w-5 h-5 rounded border-stone-300 text-teal-600 focus:ring-teal-500">
                    <span class="text-sm text-stone-700 group-hover:text-stone-900"><strong>Disciplina:</strong> "El 70% del progreso se hace en la cocina."</span>
                </label>
            </div>
        </section>

    </div>
</main>

<script>
    // --- Data Storage ---
    const nutritionData = {
        desayuno: {
            title: "🌅 Desayuno: Energía Base",
            time: "En Casa",
            desc: "El motor para empezar el día. Aporta energía sostenida.",
            items: [
                { name: "Proteína", detail: "3 huevos (revueltos o estrellados) con jamón de pavo." },
                { name: "Carbohidrato Complejo", detail: "1 taza de avena (con leche o agua)." },
                { name: "Extra", detail: "Añade canela para mejorar la sensibilidad a la insulina." }
            ],
            tip: "Prepara la avena la noche anterior (Overnight Oats) si tienes prisa por la mañana.",
            macros: [35, 45, 20] // Protein, Carbs, Fats (Conceptual percentages)
        },
        snack1: {
            title: "🥪 Snack 1: El Saciante",
            time: "Escuela - Entre clases",
            desc: "El 'Pack' de Supervivencia para mantener el cerebro activo y evitar hambre.",
            items: [
                { name: "Ingredientes", detail: "Sándwich doble de atún (en agua) o pollo deshebrado." },
                { name: "Grasas Saludables", detail: "Mucho aguacate (crucial para la saciedad prolongada)." },
                { name: "Vegetales", detail: "Espinacas frescas." }
            ],
            tip: "Lleva el sándwich en un recipiente hermético para que no se humedezca en la mochila.",
            macros: [30, 40, 30]
        },
        snack2: {
            title: "🍎 Snack 2: Control de Ansiedad",
            time: "Escuela - Media tarde",
            desc: "Para calmar la ansiedad con alimentos de baja carga glucémica.",
            items: [
                { name: "Fruta", detail: "1 manzana verde." },
                { name: "Frutos Secos", detail: "Un puño de almendras o cacahuates naturales (sin sal ni aceites añadidos)." }
            ],
            tip: "Mastica despacio las almendras; esto envía señales de saciedad al cerebro más rápido.",
            macros: [15, 35, 50]
        },
        comida: {
            title: "🍽️ Comida: Recarga Post-Escuela",
            time: "Casa / Fonda",
            desc: "Comida completa antes o después de tus actividades fuertes.",
            items: [
                { name: "Plato Principal", detail: "Pechuga de pollo, carne de res magra o pescado." },
                { name: "Acompañamiento", detail: "Porción pequeña de arroz al vapor o 1 tortilla de maíz." },
                { name: "Volumen", detail: "Gran cantidad de verduras cocidas o ensalada verde." }
            ],
            tip: "La verdura aporta fibra que retrasa la digestión y te mantiene lleno por horas.",
            macros: [45, 35, 20]
        },
        cena: {
            title: "🌙 Cena: Recuperación (Post-Gym)",
            time: "En Casa - Noche",
            desc: "Reparación de fibras musculares durante el sueño.",
            items: [
                { name: "Opción A", detail: "Licuado de proteína (Whey) con agua." },
                { name: "Opción B", detail: "Tazón de yogurt griego (sin azúcar) con un poco de fruta (berries o fresas)." }
            ],
            tip: "La proteína líquida o semi-líquida de noche facilita la digestión antes de dormir.",
            macros: [70, 20, 10]
        }
    };

    // --- State Management ---
    let currentMeal = 'desayuno';
    let glassesDrank = 0;
    const totalGlasses = 12; // approx 3 Liters (250ml per glass)
    let macroChartInstance = null;

    // --- DOM Elements ---
    const mealTitle = document.getElementById('meal-title');
    const mealTimeBadge = document.getElementById('meal-time-badge');
    const mealDesc = document.getElementById('meal-desc');
    const mealItems = document.getElementById('meal-items');
    const mealTip = document.getElementById('meal-tip');
    const tabButtons = document.querySelectorAll('.tab-btn');
    const waterLevel = document.getElementById('water-level');
    const waterCount = document.getElementById('water-count');

    // --- Initialization ---
    function init() {
        initChart();
        updateMealDisplay(currentMeal);
        setupEventListeners();
    }

    // --- Event Listeners ---
    function setupEventListeners() {
        tabButtons.forEach(btn => {
            btn.addEventListener('click', (e) => {
                // Update active button styling
                tabButtons.forEach(b => b.classList.remove('active'));
                e.target.classList.add('active');

                // Update content
                const mealKey = e.target.getAttribute('data-meal');
                updateMealDisplay(mealKey);
            });
        });
    }

    // --- Core Interaction Handling ---
    function updateMealDisplay(mealKey) {
        const data = nutritionData[mealKey];

        // Fade effect
        const displayCard = document.getElementById('meal-display');
        displayCard.style.opacity = 0;

        setTimeout(() => {
            mealTitle.textContent = data.title;
            mealTimeBadge.textContent = data.time;
            mealDesc.textContent = data.desc;
            mealTip.textContent = data.tip;

            // Build list items
            mealItems.innerHTML = '';
            data.items.forEach(item => {
                const li = document.createElement('li');
                li.className = "flex items-start gap-3 bg-white p-4 rounded-xl border border-stone-100 shadow-sm";
                li.innerHTML = `
                        <div class="mt-1 text-teal-600 font-bold">✓</div>
                        <div>
                            <strong class="text-stone-800 block text-sm">${item.name}</strong>
                            <span class="text-stone-600 text-sm">${item.detail}</span>
                        </div>
                    `;
                mealItems.appendChild(li);
            });

            updateChart(data.macros);
            displayCard.style.opacity = 1;
        }, 150); // Small delay for transition
    }

    // --- Chart.js Logic ---
    function initChart() {
        const ctx = document.getElementById('macroChart').getContext('2d');
        macroChartInstance = new Chart(ctx, {
            type: 'doughnut',
            data: {
                labels: ['Proteína', 'Carbohidratos', 'Grasas'],
                datasets: [{
                    data: nutritionData.desayuno.macros,
                    backgroundColor: [
                        '#0D9488', // Teal-600 (Protein)
                        '#EAB308', // Yellow-500 (Carbs)
                        '#F97316'  // Orange-500 (Fats)
                    ],
                    borderWidth: 0,
                    hoverOffset: 4
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                cutout: '70%',
                plugins: {
                    legend: {
                        position: 'bottom',
                        labels: {
                            font: { family: "'Inter', sans-serif", size: 12 },
                            color: '#57534E',
                            padding: 15,
                            usePointStyle: true
                        }
                    },
                    tooltip: {
                        callbacks: {
                            label: function(context) {
                                return ' ' + context.label + ': ' + context.parsed + '%';
                            }
                        },
                        backgroundColor: 'rgba(28, 25, 23, 0.9)',
                        padding: 12,
                        cornerRadius: 8
                    }
                },
                animation: {
                    animateScale: true,
                    animateRotate: true
                }
            }
        });
    }

    function updateChart(newMacros) {
        if (macroChartInstance) {
            macroChartInstance.data.datasets[0].data = newMacros;
            macroChartInstance.update();
        }
    }

    // --- Hydration Logic ---
    window.addWater = function() {
        if (glassesDrank < totalGlasses) {
            glassesDrank++;
            const percentage = (glassesDrank / totalGlasses) * 100;
            waterLevel.style.height = `${percentage}%`;
            waterCount.textContent = `${glassesDrank} / ${totalGlasses} vasos`;

            // Add a little pop animation to the glass container
            const glassContainer = document.getElementById('water-glass-container');
            glassContainer.style.transform = 'scale(1.05)';
            setTimeout(() => {
                glassContainer.style.transform = 'scale(1)';
            }, 150);

            if (glassesDrank === totalGlasses) {
                waterCount.innerHTML = `¡Meta Cumplida! 🎉`;
                waterCount.classList.remove('text-sky-500');
                waterCount.classList.add('text-teal-600');
            }
        }
    }

    // Run setup
    document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
