<!DOCTYPE html>
<html lang="ar" dir="rtl" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Maktabati POS - نظام إدارة المكتبات</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js for Reports -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        primary: {
                            50: '#f0f9ff',
                            100: '#e0f2fe',
                            500: '#0284c7',
                            600: '#0369a1',
                            700: '#075985',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@300;400;600;700&family=Poppins:wght@300;400;600;700&display=swap');
        body {
            font-family: 'Cairo', 'Poppins', sans-serif;
        }
        .sidebar-active {
            background-color: rgba(2, 132, 199, 0.1);
            color: #0284c7;
            border-inline-start: 4px solid #0284c7;
        }
        @media print {
            body * {
                visibility: hidden;
            }
            #printReceiptArea, #printReceiptArea * {
                visibility: visible;
            }
            #printReceiptArea {
                position: absolute;
                left: 0;
                top: 0;
                width: 100%;
            }
        }
    </style>
</head>
<body class="h-full flex flex-col overflow-hidden">

    <!-- Top Navbar -->
    <header class="bg-white border-b border-slate-200 h-16 flex items-center justify-between px-6 z-10 flex-shrink-0">
        <div class="flex items-center gap-3">
            <div class="bg-primary-600 text-white p-2 rounded-lg">
                <i class="fa-solid fa-book-open text-xl"></i>
            </div>
            <div>
                <h1 class="text-lg font-bold text-slate-800" id="brand-title">Maktabati POS</h1>
                <p class="text-xs text-slate-500" id="brand-sub">إدارة المكتبات والقرطاسيات الذكية</p>
            </div>
        </div>
        
        <div class="flex items-center gap-4">
            <!-- Language Switcher -->
            <button onclick="toggleLanguage()" class="flex items-center gap-1 text-sm bg-slate-100 hover:bg-slate-200 px-3 py-1.5 rounded-lg text-slate-700 transition">
                <i class="fa-solid fa-globe"></i>
                <span id="lang-btn-text">Français</span>
            </button>
            
            <!-- Quick Alert Notification -->
            <div class="relative cursor-pointer" onclick="switchTab('inventory')">
                <span id="stock-alert-badge" class="absolute -top-1 -right-1 bg-red-500 text-white text-[10px] w-4 h-4 rounded-full flex items-center justify-center hidden">0</span>
                <i class="fa-solid fa-bell text-slate-600 text-xl"></i>
            </div>
            
            <!-- Date / Time -->
            <div class="text-left text-xs text-slate-500 hidden md:block">
                <div id="live-date" class="font-semibold"></div>
                <div id="live-time"></div>
            </div>
        </div>
    </header>

    <div class="flex flex-1 overflow-hidden">
        <!-- Sidebar Navigation -->
        <aside class="w-64 bg-white border-e border-slate-200 flex flex-col justify-between hidden md:flex flex-shrink-0">
            <nav class="p-4 space-y-1 overflow-y-auto max-h-[calc(100vh-10rem)]">
                <button onclick="switchTab('dashboard')" id="btn-dashboard" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right sidebar-active">
                    <i class="fa-solid fa-chart-pie w-5"></i>
                    <span data-i18n="nav-dashboard">لوحة التحكم</span>
                </button>
                <button onclick="switchTab('cashier')" id="btn-cashier" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-cash-register w-5"></i>
                    <span data-i18n="nav-cashier">الكاشير (نقطة البيع)</span>
                </button>
                <button onclick="switchTab('products')" id="btn-products" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-boxes-stacked w-5"></i>
                    <span data-i18n="nav-products">إدارة المنتجات</span>
                </button>
                <button onclick="switchTab('inventory')" id="btn-inventory" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-warehouse w-5"></i>
                    <span data-i18n="nav-inventory">إدارة المخزون</span>
                </button>
                <button onclick="switchTab('printing')" id="btn-printing" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-print w-5"></i>
                    <span data-i18n="nav-printing">الطباعة والنسخ</span>
                </button>
                <button onclick="switchTab('customers')" id="btn-customers" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-users w-5"></i>
                    <span data-i18n="nav-customers">الزبائن والولاء</span>
                </button>
                <button onclick="switchTab('suppliers')" id="btn-suppliers" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-truck-field w-5"></i>
                    <span data-i18n="nav-suppliers">الموردون والمشتريات</span>
                </button>
                <button onclick="switchTab('reports')" id="btn-reports" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-chart-line w-5"></i>
                    <span data-i18n="nav-reports">التقارير والأرباح</span>
                </button>
                <button onclick="switchTab('settings')" id="btn-settings" class="w-full flex items-center gap-3 px-4 py-3 text-slate-700 hover:bg-slate-50 rounded-lg transition text-right">
                    <i class="fa-solid fa-gears w-5"></i>
                    <span data-i18n="nav-settings">الإعدادات</span>
                </button>
            </nav>
            <div class="p-4 border-t border-slate-200">
                <p class="text-xs text-slate-400 text-center">&copy; 2025 Maktabati POS</p>
            </div>
        </aside>

        <!-- Main Content Area -->
        <main class="flex-1 overflow-y-auto p-6 bg-slate-50">
            
            <!-- Tab: Dashboard -->
            <section id="tab-dashboard" class="space-y-6">
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                    <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="p-3 rounded-lg bg-primary-100 text-primary-600"><i class="fa-solid fa-sack-dollar text-2xl"></i></div>
                        <div>
                            <p class="text-xs text-slate-500 font-semibold" data-i18n="dash-sales">مبيعات اليوم</p>
                            <h3 class="text-xl font-bold text-slate-800 mt-1" id="dash-sales-val">0.00</h3>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="p-3 rounded-lg bg-green-100 text-green-600"><i class="fa-solid fa-coins text-2xl"></i></div>
                        <div>
                            <p class="text-xs text-slate-500 font-semibold" data-i18n="dash-profit">أرباح اليوم التقديرية</p>
                            <h3 class="text-xl font-bold text-slate-800 mt-1" id="dash-profit-val">0.00</h3>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="p-3 rounded-lg bg-amber-100 text-amber-600"><i class="fa-solid fa-triangle-exclamation text-2xl"></i></div>
                        <div>
                            <p class="text-xs text-slate-500 font-semibold" data-i18n="dash-lowstock">المنتجات منخفضة المخزون</p>
                            <h3 class="text-xl font-bold text-slate-800 mt-1" id="dash-lowstock-val">0</h3>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm flex items-center gap-4">
                        <div class="p-3 rounded-lg bg-indigo-100 text-indigo-600"><i class="fa-solid fa-users text-2xl"></i></div>
                        <div>
                            <p class="text-xs text-slate-500 font-semibold" data-i18n="dash-customers">الزبائن المسجلين</p>
                            <h3 class="text-xl font-bold text-slate-800 mt-1" id="dash-customers-val">0</h3>
                        </div>
                    </div>
                </div>

                <!-- Live Sales and Simple Charts -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm lg:col-span-2">
                        <h4 class="text-md font-bold text-slate-800 mb-4" data-i18n="dash-chart-title">أداء المبيعات والأرباح هذا الشهر</h4>
                        <div class="h-64">
                            <canvas id="salesChart"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm flex flex-col justify-between">
                        <div>
                            <h4 class="text-md font-bold text-slate-800 mb-4" data-i18n="dash-printing">إحصائيات الطباعة السريعة</h4>
                            <div class="space-y-4">
                                <div class="flex justify-between items-center py-2 border-b">
                                    <span class="text-slate-600 text-sm" data-i18n="print-total-pages">عدد الأوراق المطبوعة</span>
                                    <span id="dash-print-pages" class="font-semibold text-slate-800">0</span>
                                </div>
                                <div class="flex justify-between items-center py-2 border-b">
                                    <span class="text-slate-600 text-sm" data-i18n="print-sales">مداخيل الطباعة</span>
                                    <span id="dash-print-revenue" class="font-semibold text-green-600">0.00</span>
                                </div>
                                <div class="flex justify-between items-center py-2">
                                    <span class="text-slate-600 text-sm" data-i18n="print-profit">صافي الربح المكتسب</span>
                                    <span id="dash-print-profit" class="font-semibold text-blue-600">0.00</span>
                                </div>
                            </div>
                        </div>
                        <button onclick="switchTab('printing')" class="mt-4 w-full bg-primary-600 hover:bg-primary-700 text-white py-2 rounded-lg text-sm font-semibold transition" data-i18n="btn-go-print">فتح قسم الطباعة</button>
                    </div>
                </div>
            </section>

            <!-- Tab: Cashier POS -->
            <section id="tab-cashier" class="hidden h-full flex flex-col lg:flex-row gap-6">
                <!-- Left: Catalog and Filters -->
                <div class="flex-1 flex flex-col gap-4 overflow-hidden h-[calc(100vh-10rem)]">
                    <div class="flex flex-col sm:flex-row gap-2">
                        <div class="relative flex-1">
                            <i class="fa-solid fa-magnifying-glass absolute right-3 top-3.5 text-slate-400"></i>
                            <input type="text" id="search-pos-input" onkeyup="filterPOSProducts()" placeholder="البحث بالاسم أو الباركود..." class="w-full pl-3 pr-10 py-2.5 rounded-lg border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-primary-500">
                        </div>
                        <select id="filter-pos-cat" onchange="filterPOSProducts()" class="py-2.5 px-3 rounded-lg border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-primary-500">
                            <option value="all" data-i18n="all-categories">جميع التصنيفات</option>
                            <option value="كتب" data-i18n="cat-books">الكتب والروايات</option>
                            <option value="قرطاسية" data-i18n="cat-stationery">اللوازم والقرطاسية</option>
                            <option value="خدمات" data-i18n="cat-services">الخدمات والأخرى</option>
                        </select>
                    </div>

                    <!-- Products Grid -->
                    <div class="flex-1 overflow-y-auto grid grid-cols-2 sm:grid-cols-3 xl:grid-cols-4 gap-3 pr-1" id="pos-products-grid">
                        <!-- Dynamic items -->
                    </div>
                </div>

                <!-- Right: Cart & Summary -->
                <div class="w-full lg:w-96 bg-white border border-slate-200 rounded-xl shadow-sm flex flex-col justify-between overflow-hidden h-[calc(100vh-10rem)]">
                    <!-- Cart Header -->
                    <div class="p-4 border-b border-slate-200 flex justify-between items-center bg-slate-50">
                        <div class="flex items-center gap-2">
                            <i class="fa-solid fa-cart-shopping text-primary-600"></i>
                            <span class="font-bold text-slate-800" data-i18n="current-cart">السلة الحالية</span>
                        </div>
                        <button onclick="clearCart()" class="text-xs text-red-500 hover:underline" data-i18n="clear-cart">تفراغ السلة</button>
                    </div>

                    <!-- Customer Selection for Loyalty -->
                    <div class="p-3 border-b border-slate-100 flex gap-2">
                        <select id="pos-customer-select" class="flex-1 text-xs border border-slate-200 p-2 rounded-lg focus:outline-none focus:ring-1 focus:ring-primary-500">
                            <option value="" data-i18n="walk-in-customer">زبون عابر (بدون نقاط)</option>
                        </select>
                    </div>

                    <!-- Cart Items List -->
                    <div class="flex-1 overflow-y-auto p-4 space-y-3" id="cart-list">
                        <!-- Dynamic items -->
                    </div>

                    <!-- Summary & Actions -->
                    <div class="p-4 border-t border-slate-200 bg-slate-50 space-y-3">
                        <div class="flex justify-between text-sm text-slate-600">
                            <span data-i18n="cart-total">المجموع الفرعي:</span>
                            <span id="cart-subtotal">0.00</span>
                        </div>
                        <div class="flex justify-between text-sm text-slate-600 items-center">
                            <span data-i18n="cart-discount">الخصم (%):</span>
                            <input type="number" id="cart-discount-input" onchange="updateCartTotals()" value="0" min="0" max="100" class="w-16 text-center border rounded p-1 text-xs">
                        </div>
                        <div class="flex justify-between font-bold text-lg text-slate-800 pt-2 border-t border-slate-200">
                            <span data-i18n="cart-final-total">المجموع النهائي:</span>
                            <span id="cart-total-val">0.00</span>
                        </div>

                        <!-- Payment Method -->
                        <div class="grid grid-cols-2 gap-2 mt-2">
                            <button onclick="setPaymentMethod('cash')" id="btn-pay-cash" class="py-2 border border-primary-500 bg-primary-50 text-primary-700 rounded-lg text-xs font-semibold flex items-center justify-center gap-1">
                                <i class="fa-solid fa-money-bill-wave"></i> <span data-i18n="pay-cash">كاش / نقداً</span>
                            </button>
                            <button onclick="setPaymentMethod('card')" id="btn-pay-card" class="py-2 border border-slate-200 text-slate-600 rounded-lg text-xs font-semibold flex items-center justify-center gap-1">
                                <i class="fa-solid fa-credit-card"></i> <span data-i18n="pay-card">بطاقة بنكية</span>
                            </button>
                        </div>

                        <!-- Complete Button -->
                        <button onclick="openPaymentModal()" class="w-full mt-2 bg-green-600 hover:bg-green-700 text-white py-3 rounded-lg text-sm font-semibold transition flex items-center justify-center gap-2">
                            <i class="fa-solid fa-check"></i> <span data-i18n="btn-complete-pay">إتمام البيع وطباعة الفاتورة</span>
                        </button>
                    </div>
                </div>
            </section>

            <!-- Tab: Products Management -->
            <section id="tab-products" class="hidden space-y-6">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800" data-i18n="prod-management">إدارة الكتب واللوازم</h2>
                        <p class="text-sm text-slate-500" data-i18n="prod-sub">عرض، إضافة وتعديل المخزون الأساسي للمكتبة</p>
                    </div>
                    <button onclick="openProductModal()" class="bg-primary-600 hover:bg-primary-700 text-white px-4 py-2 rounded-lg text-sm font-semibold flex items-center gap-2">
                        <i class="fa-solid fa-plus"></i> <span data-i18n="add-new-prod">إضافة منتج جديد</span>
                    </button>
                </div>

                <!-- Products Table -->
                <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full text-right border-collapse">
                            <thead>
                                <tr class="bg-slate-50 text-slate-700 text-xs font-bold border-b border-slate-200">
                                    <th class="p-4" data-i18n="th-barcode">الباركود</th>
                                    <th class="p-4" data-i18n="th-name">اسم المنتج / الكتاب</th>
                                    <th class="p-4" data-i18n="th-category">التصنيف</th>
                                    <th class="p-4" data-i18n="th-author">المؤلف / الناشر</th>
                                    <th class="p-4" data-i18n="th-level">المستوى / المادة</th>
                                    <th class="p-4" data-i18n="th-price">سعر البيع</th>
                                    <th class="p-4" data-i18n="th-qty">الكمية الحالية</th>
                                    <th class="p-4" data-i18n="th-actions">إجراءات</th>
                                </tr>
                            </thead>
                            <tbody id="products-table-body" class="text-sm text-slate-600 divide-y divide-slate-100">
                                <!-- Dynamic dynamic rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- Tab: Inventory Management -->
            <section id="tab-inventory" class="hidden space-y-6">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800" data-i18n="inv-title">رقابة المخزون والتحذيرات</h2>
                        <p class="text-sm text-slate-500" data-i18n="inv-sub">مراقبة كميات السلع وسرعة نفادها وتعديلها الفوري</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="md:col-span-2 bg-white rounded-xl border border-slate-200 p-6 shadow-sm">
                        <h3 class="text-md font-bold text-slate-800 mb-4" data-i18n="inv-warnings">تنبيهات قرب النفاد (الكمية الحرجة)</h3>
                        <div class="overflow-x-auto">
                            <table class="w-full text-right">
                                <thead>
                                    <tr class="text-xs font-bold text-slate-400 border-b pb-2">
                                        <th class="pb-2" data-i18n="th-name">المنتج</th>
                                        <th class="pb-2 text-center" data-i18n="th-current-qty">الكمية المتوفرة</th>
                                        <th class="pb-2 text-center" data-i18n="th-min-qty">الحد الأدنى للتنبيه</th>
                                        <th class="pb-2 text-center" data-i18n="th-status">الحالة</th>
                                    </tr>
                                </thead>
                                <tbody id="inventory-alerts-body" class="text-sm text-slate-600 divide-y">
                                    <!-- Dynamic alerts -->
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <div class="bg-white rounded-xl border border-slate-200 p-6 shadow-sm space-y-4">
                        <h3 class="text-md font-bold text-slate-800" data-i18n="inv-quick-adjust">تعديل سريع للمخزون</h3>
                        <div>
                            <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="select-product">اختر المنتج</label>
                            <select id="inv-adjust-product" class="w-full text-sm border p-2.5 rounded-lg focus:outline-none focus:ring-1 focus:ring-primary-500"></select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="new-qty">الكمية الجديدة</label>
                            <input type="number" id="inv-adjust-qty" min="0" placeholder="0" class="w-full text-sm border p-2.5 rounded-lg focus:outline-none focus:ring-1 focus:ring-primary-500">
                        </div>
                        <button onclick="saveQuickStockAdjust()" class="w-full bg-primary-600 hover:bg-primary-700 text-white py-2 rounded-lg text-sm font-semibold transition" data-i18n="btn-save-adjust">تحديث المخزون</button>
                    </div>
                </div>
            </section>

            <!-- Tab: Printing & Copying Services -->
            <section id="tab-printing" class="hidden space-y-6">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800" data-i18n="print-title">بوابة خدمات الطباعة والنسخ</h2>
                        <p class="text-sm text-slate-500" data-i18n="print-sub">حساب فوري للأوراق المطبوعة والملونة، والتجليد، مع رصد الأرباح</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <!-- Pricing and calculator -->
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm space-y-4">
                        <h3 class="text-md font-bold text-slate-800 border-b pb-2" data-i18n="print-calc-title">حاسبة الطباعة والنسخ</h3>
                        <div>
                            <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="print-paper-type">نوع الخدمة / الورق</label>
                            <select id="print-type" class="w-full text-sm border p-2.5 rounded-lg focus:outline-none focus:ring-1" onchange="prefillPrintPrices()">
                                <option value="black-white" data-i18n="print-bw">طباعة / نسخ عادي (أسود وأبيض)</option>
                                <option value="color" data-i18n="print-color">طباعة / نسخ ملون</option>
                                <option value="binding" data-i18n="print-binding">تجليد وخدمات ملحقة</option>
                            </select>
                        </div>
                        <div class="grid grid-cols-2 gap-3">
                            <div>
                                <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="print-pages">عدد الصفحات</label>
                                <input type="number" id="print-pages" value="1" min="1" oninput="calcPrintJob()" class="w-full text-sm border p-2.5 rounded-lg text-center">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="print-copies">عدد النسخ</label>
                                <input type="number" id="print-copies" value="1" min="1" oninput="calcPrintJob()" class="w-full text-sm border p-2.5 rounded-lg text-center">
                            </div>
                        </div>
                        <div class="grid grid-cols-2 gap-3">
                            <div>
                                <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="print-cost">التكلفة للورقة (حبر + ورق)</label>
                                <input type="number" step="0.05" id="print-cost-unit" value="0.10" oninput="calcPrintJob()" class="w-full text-sm border p-2.5 rounded-lg text-center">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="print-price">سعر البيع للورقة</label>
                                <input type="number" step="0.05" id="print-price-unit" value="0.50" oninput="calcPrintJob()" class="w-full text-sm border p-2.5 rounded-lg text-center">
                            </div>
                        </div>
                        <div class="bg-slate-50 p-4 rounded-lg space-y-2">
                            <div class="flex justify-between text-xs text-slate-600">
                                <span data-i18n="print-total-cost">التكلفة الإجمالية:</span>
                                <span id="print-total-cost-display">0.10</span>
                            </div>
                            <div class="flex justify-between text-xs text-slate-600">
                                <span data-i18n="print-total-rev">الإيراد الإجمالي:</span>
                                <span id="print-total-rev-display" class="font-semibold text-slate-800">0.50</span>
                            </div>
                            <div class="flex justify-between text-sm font-bold border-t pt-2 text-green-600">
                                <span data-i18n="print-calc-profit">صافي الربح المتوقع:</span>
                                <span id="print-calc-profit-display">0.40</span>
                            </div>
                        </div>
                        <button onclick="registerPrintJob()" class="w-full bg-primary-600 hover:bg-primary-700 text-white py-2 rounded-lg text-sm font-semibold transition" data-i18n="btn-register-print">تسجيل وتأكيد العملية</button>
                    </div>

                    <!-- Printing Ledger -->
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm lg:col-span-2 overflow-hidden">
                        <h3 class="text-md font-bold text-slate-800 mb-4" data-i18n="print-history">سجل عمليات الطباعة والنسخ اليومية</h3>
                        <div class="overflow-x-auto max-h-96">
                            <table class="w-full text-right text-sm">
                                <thead>
                                    <tr class="bg-slate-50 text-slate-500 text-xs border-b">
                                        <th class="p-3" data-i18n="th-time">الوقت</th>
                                        <th class="p-3" data-i18n="th-print-type">الخدمة</th>
                                        <th class="p-3 text-center" data-i18n="th-print-pages">عدد الصفحات × نسخ</th>
                                        <th class="p-3 text-center" data-i18n="th-print-price">السعر الإجمالي</th>
                                        <th class="p-3 text-center" data-i18n="th-print-profit">الربح الصافي</th>
                                    </tr>
                                </thead>
                                <tbody id="print-jobs-body" class="divide-y text-slate-600">
                                    <!-- Dynamic printing entries -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Tab: Customers & Loyalty -->
            <section id="tab-customers" class="hidden space-y-6">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800" data-i18n="cust-title">إدارة الزبائن الأوفياء والولاء</h2>
                        <p class="text-sm text-slate-500" data-i18n="cust-sub">إدارة نقاط الزبائن وتاريخ عمليات الشراء لتعزيز المبيعات</p>
                    </div>
                    <button onclick="openCustomerModal()" class="bg-primary-600 hover:bg-primary-700 text-white px-4 py-2 rounded-lg text-sm font-semibold flex items-center gap-2">
                        <i class="fa-solid fa-user-plus"></i> <span data-i18n="add-new-cust">تسجيل زبون جديد</span>
                    </button>
                </div>

                <!-- Customers table -->
                <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                    <table class="w-full text-right text-sm border-collapse">
                        <thead>
                            <tr class="bg-slate-50 border-b">
                                <th class="p-4" data-i18n="th-cust-name">اسم الزبون</th>
                                <th class="p-4" data-i18n="th-cust-phone">رقم الهاتف</th>
                                <th class="p-4 text-center" data-i18n="th-cust-points">نقاط الولاء</th>
                                <th class="p-4 text-center" data-i18n="th-cust-purchases">عدد المشتريات</th>
                                <th class="p-4" data-i18n="th-actions">إجراءات</th>
                            </tr>
                        </thead>
                        <tbody id="customers-table-body" class="divide-y text-slate-600">
                            <!-- Dynamic customer rows -->
                        </tbody>
                    </table>
                </div>
            </section>

            <!-- Tab: Suppliers & Debt Management -->
            <section id="tab-suppliers" class="hidden space-y-6">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800" data-i18n="sup-title">إدارة الموردين والديون المترتبة</h2>
                        <p class="text-sm text-slate-500" data-i18n="sup-sub">إحصاء عمليات التوريد وحجم الديون المسجلة للموردين لتسويتها لاحقاً</p>
                    </div>
                    <button onclick="openSupplierModal()" class="bg-primary-600 hover:bg-primary-700 text-white px-4 py-2 rounded-lg text-sm font-semibold flex items-center gap-2">
                        <i class="fa-solid fa-truck"></i> <span data-i18n="add-new-sup">إضافة مورد جديد</span>
                    </button>
                </div>

                <!-- Suppliers list and Quick Purchase -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <div class="lg:col-span-2 bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                        <table class="w-full text-right text-sm border-collapse">
                            <thead>
                                <tr class="bg-slate-50 border-b">
                                    <th class="p-4" data-i18n="th-sup-company">الشركة / الموزع</th>
                                    <th class="p-4" data-i18n="th-sup-name">المندوب</th>
                                    <th class="p-4" data-i18n="th-sup-phone">رقم الاتصال</th>
                                    <th class="p-4 text-center" data-i18n="th-sup-debt">الديون المتبقية</th>
                                    <th class="p-4" data-i18n="th-actions">إجراءات</th>
                                </tr>
                            </thead>
                            <tbody id="suppliers-table-body" class="divide-y text-slate-600">
                                <!-- Dynamic supplier entries -->
                            </tbody>
                        </table>
                    </div>

                    <!-- Supply Transaction Ledger -->
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm space-y-4">
                        <h3 class="text-md font-bold text-slate-800 border-b pb-2" data-i18n="sup-register-invoice">تسجيل فاتورة توريد جديدة</h3>
                        <div>
                            <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="th-sup-company">المورد</label>
                            <select id="sup-select" class="w-full text-sm border p-2.5 rounded-lg"></select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="sup-total-cost">تكلفة الفاتورة بالكامل</label>
                            <input type="number" id="sup-invoice-amount" placeholder="0.00" class="w-full text-sm border p-2.5 rounded-lg">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-500 mb-1" data-i18n="sup-paid-amount">المبلغ المدفوع حالياً</label>
                            <input type="number" id="sup-invoice-paid" placeholder="0.00" class="w-full text-sm border p-2.5 rounded-lg">
                        </div>
                        <button onclick="registerSupplierPurchase()" class="w-full bg-primary-600 hover:bg-primary-700 text-white py-2 rounded-lg text-sm font-semibold transition" data-i18n="btn-register-sup-inv">تأكيد وتسجيل المشتريات والديون</button>
                    </div>
                </div>
            </section>

            <!-- Tab: Reports & Profit -->
            <section id="tab-reports" class="hidden space-y-6">
                <div>
                    <h2 class="text-xl font-bold text-slate-800" data-i18n="rep-title">التقارير المالية المفصلة</h2>
                    <p class="text-sm text-slate-500" data-i18n="rep-sub">عرض تقارير الإيرادات والأرباح لتحديد مسار مبيعات المكتبة</p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm">
                        <h3 class="text-sm font-bold text-slate-400 mb-2 uppercase" data-i18n="rep-total-month">إجمالي مبيعات الشهر الحالي</h3>
                        <p class="text-3xl font-extrabold text-slate-800" id="rep-monthly-sales">0.00</p>
                    </div>
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm">
                        <h3 class="text-sm font-bold text-slate-400 mb-2 uppercase" data-i18n="rep-total-profit">صافي أرباح الشهر التقديرية</h3>
                        <p class="text-3xl font-extrabold text-green-600" id="rep-monthly-profit">0.00</p>
                    </div>
                    <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm">
                        <h3 class="text-sm font-bold text-slate-400 mb-2 uppercase" data-i18n="rep-best-seller">المنتج الأكثر مبيعاً</h3>
                        <p class="text-lg font-bold text-primary-600 mt-2" id="rep-top-seller">--</p>
                    </div>
                </div>

                <!-- Transaction Log -->
                <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden p-6">
                    <h3 class="text-md font-bold text-slate-800 mb-4" data-i18n="rep-all-sales">سجل كافة فواتير المبيعات</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-right text-sm">
                            <thead>
                                <tr class="bg-slate-50 border-b">
                                    <th class="p-3" data-i18n="th-date">التاريخ والوقت</th>
                                    <th class="p-3" data-i18n="th-invoice-id">رقم الفاتورة</th>
                                    <th class="p-3 text-center" data-i18n="th-items-count">عدد المواد</th>
                                    <th class="p-3 text-center" data-i18n="th-total">المجموع المدفوع</th>
                                    <th class="p-3 text-center" data-i18n="th-payment-way">طريقة الدفع</th>
                                </tr>
                            </thead>
                            <tbody id="sales-log-body" class="divide-y text-slate-600">
                                <!-- Dynamic Sales Rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- Tab: Settings -->
            <section id="tab-settings" class="hidden space-y-6">
                <div>
                    <h2 class="text-xl font-bold text-slate-800" data-i18n="settings-title">إعدادات النظام العامة</h2>
                    <p class="text-sm text-slate-500" data-i18n="settings-sub">تعديل معلومات المكتبة وعملة التداول وتجربة النظام</p>
                </div>

                <div class="bg-white p-6 rounded-xl border border-slate-200 shadow-sm max-w-2xl space-y-4">
                    <div>
                        <label class="block text-sm font-semibold text-slate-600 mb-1" data-i18n="set-store-name">اسم المكتبة / المؤسسة</label>
                        <input type="text" id="set-store-name-input" class="w-full text-sm border p-2.5 rounded-lg focus:ring-1 focus:ring-primary-500">
                    </div>
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block text-sm font-semibold text-slate-600 mb-1" data-i18n="set-phone">رقم الهاتف للواتساب</label>
                            <input type="text" id="set-store-phone-input" placeholder="+2126000000" class="w-full text-sm border p-2.5 rounded-lg focus:ring-1 focus:ring-primary-500">
                        </div>
                        <div>
                            <label class="block text-sm font-semibold text-slate-600 mb-1" data-i18n="set-currency">رمز العملة المستعملة</label>
                            <input type="text" id="set-store-currency-input" placeholder="د.م" class="w-full text-sm border p-2.5 rounded-lg focus:ring-1 focus:ring-primary-500">
                        </div>
                    </div>
                    <div>
                        <label class="block text-sm font-semibold text-slate-600 mb-1" data-i18n="set-msg">رسالة فاتورة الواتساب الترحيبية</label>
                        <textarea id="set-store-message-input" rows="3" class="w-full text-sm border p-2.5 rounded-lg focus:ring-1 focus:ring-primary-500"></textarea>
                    </div>
                    <div class="pt-4 border-t flex justify-between">
                        <button onclick="resetAppDatabase()" class="bg-red-50 hover:bg-red-100 text-red-600 px-4 py-2 rounded-lg text-sm transition" data-i18n="btn-reset-db">تصفير واستعادة تهيئة المصنع</button>
                        <button onclick="saveAppSettings()" class="bg-primary-600 hover:bg-primary-700 text-white px-6 py-2 rounded-lg text-sm font-semibold transition" data-i18n="btn-save-settings">حفظ الإعدادات</button>
                    </div>
                </div>
            </section>

        </main>
    </div>

    <!-- Modals & Overlay (Universal Dialog UI) -->
    <!-- Modal: Add/Edit Product -->
    <div id="modal-product" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center hidden">
        <div class="bg-white rounded-xl shadow-lg w-full max-w-xl mx-4 overflow-hidden">
            <div class="p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <h3 class="font-bold text-slate-800" id="modal-product-title">إضافة منتج جديد</h3>
                <button onclick="closeProductModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>
            <div class="p-6 space-y-4 max-h-[70vh] overflow-y-auto">
                <input type="hidden" id="prod-id">
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">الباركود</label>
                        <input type="text" id="prod-barcode" class="w-full text-sm border p-2 rounded-lg">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">اسم المنتج</label>
                        <input type="text" id="prod-name" class="w-full text-sm border p-2 rounded-lg" required>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">التصنيف</label>
                        <select id="prod-category" class="w-full text-sm border p-2 rounded-lg">
                            <option value="كتب">الكتب والروايات</option>
                            <option value="قرطاسية">اللوازم والقرطاسية</option>
                            <option value="خدمات">الخدمات والأخرى</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">المؤلف (للكتب)</label>
                        <input type="text" id="prod-author" class="w-full text-sm border p-2 rounded-lg">
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">المستوى الدراسي</label>
                        <input type="text" id="prod-level" class="w-full text-sm border p-2 rounded-lg">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">المادة الدراسية / دار النشر</label>
                        <input type="text" id="prod-subject" class="w-full text-sm border p-2 rounded-lg">
                    </div>
                </div>
                <div class="grid grid-cols-3 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">سعر الشراء (التكلفة)</label>
                        <input type="number" step="0.05" id="prod-cost" class="w-full text-sm border p-2 rounded-lg">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">سعر البيع المقترح</label>
                        <input type="number" step="0.05" id="prod-price" class="w-full text-sm border p-2 rounded-lg" required>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">الكمية البدئية</label>
                        <input type="number" id="prod-stock" class="w-full text-sm border p-2 rounded-lg" required>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">الحد الأدنى للمخزون (التنبيه)</label>
                        <input type="number" id="prod-min-stock" value="5" class="w-full text-sm border p-2 rounded-lg">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 mb-1">رابط صورة المنتج (اختياري)</label>
                        <input type="text" id="prod-image" placeholder="https://..." class="w-full text-sm border p-2 rounded-lg">
                    </div>
                </div>
            </div>
            <div class="p-4 border-t border-slate-100 flex justify-end gap-2 bg-slate-50">
                <button onclick="closeProductModal()" class="px-4 py-2 border rounded-lg text-sm text-slate-600 hover:bg-slate-100">إلغاء</button>
                <button onclick="saveProduct()" class="px-4 py-2 bg-primary-600 hover:bg-primary-700 text-white rounded-lg text-sm font-semibold">حفظ المنتج</button>
            </div>
        </div>
    </div>

    <!-- Modal: Add Customer -->
    <div id="modal-customer" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center hidden">
        <div class="bg-white rounded-xl shadow-lg w-full max-w-md mx-4 overflow-hidden">
            <div class="p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <h3 class="font-bold text-slate-800">إضافة زبون وفي جديد</h3>
                <button onclick="closeCustomerModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">الاسم الكامل</label>
                    <input type="text" id="cust-name" class="w-full text-sm border p-2.5 rounded-lg">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">رقم الهاتف (مع رمز الدولة)</label>
                    <input type="text" id="cust-phone" placeholder="+212600000000" class="w-full text-sm border p-2.5 rounded-lg">
                </div>
            </div>
            <div class="p-4 border-t border-slate-100 flex justify-end gap-2 bg-slate-50">
                <button onclick="closeCustomerModal()" class="px-4 py-2 border rounded-lg text-sm text-slate-600 hover:bg-slate-100">إلغاء</button>
                <button onclick="saveCustomer()" class="px-4 py-2 bg-primary-600 hover:bg-primary-700 text-white rounded-lg text-sm font-semibold">حفظ الزبون</button>
            </div>
        </div>
    </div>

    <!-- Modal: Add Supplier -->
    <div id="modal-supplier" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center hidden">
        <div class="bg-white rounded-xl shadow-lg w-full max-w-md mx-4 overflow-hidden">
            <div class="p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <h3 class="font-bold text-slate-800">تسجيل مورد جديد</h3>
                <button onclick="closeSupplierModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">اسم المؤسسة / شركة التوريد</label>
                    <input type="text" id="sup-name-input" class="w-full text-sm border p-2.5 rounded-lg">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">اسم المندوب المسؤول</label>
                    <input type="text" id="sup-contact-input" class="w-full text-sm border p-2.5 rounded-lg">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">رقم هاتف الاتصال</label>
                    <input type="text" id="sup-phone-input" class="w-full text-sm border p-2.5 rounded-lg">
                </div>
            </div>
            <div class="p-4 border-t border-slate-100 flex justify-end gap-2 bg-slate-50">
                <button onclick="closeSupplierModal()" class="px-4 py-2 border rounded-lg text-sm text-slate-600 hover:bg-slate-100">إلغاء</button>
                <button onclick="saveSupplier()" class="px-4 py-2 bg-primary-600 hover:bg-primary-700 text-white rounded-lg text-sm font-semibold">حفظ المورد</button>
            </div>
        </div>
    </div>

    <!-- Modal: Payment Checkout & Receipt Preview -->
    <div id="modal-payment" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center hidden">
        <div class="bg-white rounded-xl shadow-lg w-full max-w-sm mx-4 overflow-hidden">
            <div class="p-4 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <h3 class="font-bold text-slate-800" data-i18n="modal-receipt-title">الفاتورة المباشرة والطباعة</h3>
                <button onclick="closePaymentModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>
            
            <!-- Receipt Print Area -->
            <div class="p-6 bg-white overflow-y-auto max-h-[60vh]" id="printReceiptArea">
                <div class="text-center pb-4 border-b border-dashed">
                    <h2 class="text-xl font-bold text-slate-800" id="receipt-store-name">Maktabati POS</h2>
                    <p class="text-xs text-slate-400 mt-1" id="receipt-store-desc">القرطاسية واللوازم المدرسية الشاملة</p>
                    <p class="text-xs text-slate-500 mt-1" id="receipt-datetime">--/--/--</p>
                </div>
                <div class="py-4 text-xs space-y-2 border-b border-dashed">
                    <div class="flex justify-between">
                        <span>رقم الفاتورة:</span>
                        <span id="receipt-id" class="font-mono">--</span>
                    </div>
                    <div class="flex justify-between">
                        <span>نوع الدفع:</span>
                        <span id="receipt-pay-method">كاش</span>
                    </div>
                    <div id="receipt-customer-row" class="flex justify-between hidden">
                        <span>الزبون:</span>
                        <span id="receipt-customer-name">--</span>
                    </div>
                </div>
                <div class="py-4 text-xs space-y-3" id="receipt-items-container">
                    <!-- Dynamic products in sale -->
                </div>
                <div class="pt-4 border-t border-dashed text-xs space-y-2 font-semibold">
                    <div class="flex justify-between">
                        <span>المجموع:</span>
                        <span id="receipt-subtotal">0.00</span>
                    </div>
                    <div class="flex justify-between text-green-600">
                        <span>الخصم الممنوح:</span>
                        <span id="receipt-discount">0.00</span>
                    </div>
                    <div class="flex justify-between text-base font-bold pt-1 border-t">
                        <span>المبلغ الإجمالي المفوتر:</span>
                        <span id="receipt-total">0.00</span>
                    </div>
                </div>
                <div class="text-center pt-6 text-[10px] text-slate-400 border-t border-dashed mt-4">
                    <p data-i18n="receipt-thank-you">شكراً لزيارتكم! مرحباً بكم في أي وقت</p>
                </div>
            </div>

            <!-- Action buttons inside modal -->
            <div class="p-4 bg-slate-50 border-t border-slate-100 grid grid-cols-2 gap-2">
                <button onclick="sendReceiptWhatsApp()" class="py-2.5 bg-green-500 hover:bg-green-600 text-white rounded-lg text-xs font-semibold flex items-center justify-center gap-1.5 transition">
                    <i class="fa-brands fa-whatsapp text-sm"></i> <span data-i18n="btn-send-wa">إرسال واتساب</span>
                </button>
                <button onclick="printReceipt()" class="py-2.5 bg-primary-600 hover:bg-primary-700 text-white rounded-lg text-xs font-semibold flex items-center justify-center gap-1.5 transition">
                    <i class="fa-solid fa-print"></i> <span data-i18n="btn-print">طباعة الورقة</span>
                </button>
                <button onclick="confirmAndCloseSale()" class="col-span-2 py-3 bg-slate-800 hover:bg-slate-900 text-white rounded-lg text-xs font-semibold transition mt-1" data-i18n="btn-complete-and-close">
                    تأكيد إغلاق الفاتورة وحفظ البيع
                </button>
            </div>
        </div>
    </div>

    <!-- UI Custom Toast Notifications -->
    <div id="toast-box" class="fixed bottom-5 left-5 z-50 pointer-events-none space-y-2"></div>

    <!-- MAIN APP JAVASCRIPT LAYER -->
    <script>
        // Core variables
        let products = [];
        let cart = [];
        let sales = [];
        let customers = [];
        let suppliers = [];
        let printJobs = [];
        let settings = {
            storeName: "Maktabati POS",
            storePhone: "+2126000000",
            storeCurrency: "د.م",
            storeMessage: "شكراً لتسوقكم من مكتبتنا! إليكم تفاصيل طلبيتكم:",
            language: "ar"
        };
        let activePaymentMethod = "cash";
        let activeTab = "dashboard";
        let salesChartRef = null;

        // Internationalization (AR / FR)
        const dictionary = {
            ar: {
                "nav-dashboard": "لوحة التحكم",
                "nav-cashier": "الكاشير",
                "nav-products": "إدارة المنتجات",
                "nav-inventory": "المخزون",
                "nav-printing": "الطباعة والنسخ",
                "nav-customers": "الزبائن",
                "nav-suppliers": "الموردون",
                "nav-reports": "التقارير",
                "nav-settings": "الإعدادات",
                "dash-sales": "مبيعات اليوم",
                "dash-profit": "أرباح اليوم التقديرية",
                "dash-lowstock": "المنتجات منخفضة المخزون",
                "dash-customers": "الزبائن المسجلين",
                "dash-chart-title": "أداء المبيعات والأرباح هذا الشهر",
                "dash-printing": "إحصائيات الطباعة السريعة",
                "print-total-pages": "عدد الأوراق المطبوعة",
                "print-sales": "مداخيل الطباعة",
                "print-profit": "صافي الربح المكتسب",
                "btn-go-print": "فتح قسم الطباعة",
                "current-cart": "السلة الحالية",
                "clear-cart": "تفريغ السلة",
                "walk-in-customer": "زبون عابر (بدون نقاط)",
                "cart-total": "المجموع الفرعي:",
                "cart-discount": "الخصم (%):",
                "cart-final-total": "المجموع النهائي:",
                "pay-cash": "كاش / نقداً",
                "pay-card": "بطاقة بنكية",
                "btn-complete-pay": "إتمام البيع وطباعة الفاتورة",
                "prod-management": "إدارة الكتب واللوازم",
                "prod-sub": "عرض، إضافة وتعديل المخزون الأساسي للمكتبة",
                "add-new-prod": "إضافة منتج جديد",
                "th-barcode": "الباركود",
                "th-name": "اسم المنتج / الكتاب",
                "th-category": "التصنيف",
                "th-author": "المؤلف",
                "th-level": "المستوى الدراسي",
                "th-price": "سعر البيع",
                "th-qty": "الكمية",
                "th-actions": "إجراءات",
                "inv-title": "رقابة المخزون والتحذيرات",
                "inv-sub": "مراقبة كميات السلع وسرعة نفادها وتعديلها الفوري",
                "inv-warnings": "تنبيهات قرب النفاد (الكمية الحرجة)",
                "th-current-qty": "الكمية المتوفرة",
                "th-min-qty": "الحد الأدنى للتنبيه",
                "th-status": "الحالة",
                "inv-quick-adjust": "تعديل سريع للمخزون",
                "select-product": "اختر المنتج",
                "new-qty": "الكمية الجديدة",
                "btn-save-adjust": "تحديث المخزون",
                "print-title": "بوابة خدمات الطباعة والنسخ",
                "print-sub": "حساب فوري للأوراق المطبوعة والملونة، والتجليد، مع رصد الأرباح",
                "print-calc-title": "حاسبة الطباعة والنسخ",
                "print-paper-type": "نوع الخدمة / الورق",
                "print-bw": "طباعة / نسخ عادي (أسود وأبيض)",
                "print-color": "طباعة / نسخ ملون",
                "print-binding": "تجليد وخدمات ملحقة",
                "print-pages": "عدد الصفحات",
                "print-copies": "عدد النسخ",
                "print-cost": "التكلفة للورقة (حبر + ورق)",
                "print-price": "سعر البيع للورقة",
                "print-total-cost": "التكلفة الإجمالية:",
                "print-total-rev": "الإيراد الإجمالي:",
                "print-calc-profit": "صافي الربح المتوقع:",
                "btn-register-print": "تسجيل وتأكيد العملية",
                "print-history": "سجل عمليات الطباعة والنسخ اليومية",
                "th-time": "الوقت",
                "th-print-type": "الخدمة",
                "th-print-pages": "الصفحات × نسخ",
                "th-print-price": "السعر الإجمالي",
                "th-print-profit": "الربح الصافي",
                "cust-title": "إدارة الزبائن الأوفياء والولاء",
                "cust-sub": "إدارة نقاط الزبائن وتاريخ عمليات الشراء لتعزيز المبيعات",
                "add-new-cust": "تسجيل زبون جديد",
                "th-cust-name": "اسم الزبون",
                "th-cust-phone": "رقم الهاتف",
                "th-cust-points": "نقاط الولاء",
                "th-cust-purchases": "عدد المشتريات",
                "sup-title": "إدارة الموردين والديون المترتبة",
                "sup-sub": "إحصاء عمليات التوريد وحجم الديون المسجلة للموردين لتسويتها لاحقاً",
                "add-new-sup": "إضافة مورد جديد",
                "th-sup-company": "الشركة / الموزع",
                "th-sup-name": "المندوب",
                "th-sup-phone": "رقم الاتصال",
                "th-sup-debt": "الديون المتبقية",
                "sup-register-invoice": "تسجيل فاتورة توريد جديدة",
                "sup-total-cost": "تكلفة الفاتورة بالكامل",
                "sup-paid-amount": "المبلغ المدفوع حالياً",
                "btn-register-sup-inv": "تأكيد وتسجيل المشتريات والديون",
                "rep-title": "التقارير المالية المفصلة",
                "rep-sub": "عرض تقارير الإيرادات والأرباح لتحديد مسار مبيعات المكتبة",
                "rep-total-month": "إجمالي مبيعات الشهر الحالي",
                "rep-total-profit": "صافي أرباح الشهر التقديرية",
                "rep-best-seller": "المنتج الأكثر مبيعاً",
                "rep-all-sales": "سجل كافة فواتير المبيعات",
                "th-date": "التاريخ والوقت",
                "th-invoice-id": "رقم الفاتورة",
                "th-items-count": "عدد المواد",
                "th-total": "المجموع المدفوع",
                "th-payment-way": "طريقة الدفع",
                "settings-title": "إعدادات النظام العامة",
                "settings-sub": "تعديل معلومات المكتبة وعملة التداول وتجربة النظام",
                "set-store-name": "اسم المكتبة / المؤسسة",
                "set-phone": "رقم الهاتف للواتساب",
                "set-currency": "رمز العملة المستعملة",
                "set-msg": "رسالة فاتورة الواتساب الترحيبية",
                "btn-reset-db": "تصفير واستعادة تهيئة المصنع",
                "btn-save-settings": "حفظ الإعدادات",
                "modal-receipt-title": "الفاتورة المباشرة والطباعة",
                "receipt-thank-you": "شكراً لزيارتكم! مرحباً بكم في أي وقت",
                "btn-send-wa": "إرسال واتساب",
                "btn-print": "طباعة الورقة",
                "btn-complete-and-close": "تأكيد إغلاق الفاتورة وحفظ البيع",
                "all-categories": "جميع التصنيفات",
                "cat-books": "الكتب والروايات",
                "cat-stationery": "اللوازم والقرطاسية",
                "cat-services": "الخدمات والأخرى",
            },
            fr: {
                "nav-dashboard": "Tableau de Bord",
                "nav-cashier": "Caisse (POS)",
                "nav-products": "Produits",
                "nav-inventory": "Stocks",
                "nav-printing": "Impression & Copie",
                "nav-customers": "Clients",
                "nav-suppliers": "Fournisseurs",
                "nav-reports": "Rapports",
                "nav-settings": "Configuration",
                "dash-sales": "Ventes du Jour",
                "dash-profit": "Bénéfice du Jour Est.",
                "dash-lowstock": "Alerte Rupture Stock",
                "dash-customers": "Clients Enregistrés",
                "dash-chart-title": "Performance mensuelle des ventes",
                "dash-printing": "Stats Impression Rapide",
                "print-total-pages": "Feuilles Imprimées",
                "print-sales": "Revenus Impression",
                "print-profit": "Marge Net Acquise",
                "btn-go-print": "Ouvrir Impression",
                "current-cart": "Panier Actuel",
                "clear-cart": "Vider le panier",
                "walk-in-customer": "Client Passager (Sans points)",
                "cart-total": "Sous-total:",
                "cart-discount": "Remise (%):",
                "cart-final-total": "Total Net Final:",
                "pay-cash": "Espèces / Cash",
                "pay-card": "Carte Bancaire",
                "btn-complete-pay": "Encaisser & Imprimer",
                "prod-management": "Gestion des Livres & Fournitures",
                "prod-sub": "Consultez, ajoutez et mettez à jour votre catalogue",
                "add-new-prod": "Nouveau Produit",
                "th-barcode": "Code-barres",
                "th-name": "Désignation",
                "th-category": "Catégorie",
                "th-author": "Auteur",
                "th-level": "Niveau Scolaire",
                "th-price": "Prix Vente",
                "th-qty": "Quantité",
                "th-actions": "Actions",
                "inv-title": "Suivi des Stocks",
                "inv-sub": "Surveillance intelligente des stocks et réapprovisionnements",
                "inv-warnings": "Alerte seuil critique atteint",
                "th-current-qty": "Qté Restante",
                "th-min-qty": "Seuil Alerte",
                "th-status": "Statut",
                "inv-quick-adjust": "Ajustement Express",
                "select-product": "Sélectionner Produit",
                "new-qty": "Nouvelle Quantité",
                "btn-save-adjust": "Actualiser le Stock",
                "print-title": "Espace Impression & Copie",
                "print-sub": "Calculateur dynamique du coût et des marges de reliures/impressions",
                "print-calc-title": "Calculateur Copie & Reliure",
                "print-paper-type": "Nature de Service",
                "print-bw": "Noir & Blanc Standard",
                "print-color": "Impression Couleur",
                "print-binding": "Reliure & Finition",
                "print-pages": "Feuilles par exam.",
                "print-copies": "Nbr d'exemplaires",
                "print-cost": "Coût de revient par page",
                "print-price": "Prix de vente unitaire",
                "print-total-cost": "Coût Total:",
                "print-total-rev": "Recette Totale:",
                "print-calc-profit": "Bénéfice Net Estimé:",
                "btn-register-print": "Enregistrer la prestation",
                "print-history": "Historique des impressions du jour",
                "th-time": "Heure",
                "th-print-type": "Type Prestation",
                "th-print-pages": "Pages × Copies",
                "th-print-price": "Prix Total",
                "th-print-profit": "Gain Net",
                "cust-title": "Fidélisation Clients",
                "cust-sub": "Gérez vos clients fidèles et suivez l'historique de leurs points",
                "add-new-cust": "Créer Client",
                "th-cust-name": "Nom Client",
                "th-cust-phone": "N° Téléphone",
                "th-cust-points": "Points Fidélité",
                "th-cust-purchases": "Fréquentation",
                "sup-title": "Fournisseurs & Facturation",
                "sup-sub": "Aperçu de la dette fournisseur et traçabilité des achats",
                "add-new-sup": "Nouveau Fournisseur",
                "th-sup-company": "Établissement/Fournisseur",
                "th-sup-name": "Agent",
                "th-sup-phone": "Contact",
                "th-sup-debt": "Créance Due",
                "sup-register-invoice": "Enregistrer Facture d'Achat",
                "sup-total-cost": "Montant Total Facture",
                "sup-paid-amount": "Acompte Payé",
                "btn-register-sup-inv": "Valider la facture d'achat",
                "rep-title": "Rapports Financiers & Rentabilité",
                "rep-sub": "Suivi analytique du chiffre d'affaires et de la marge brute",
                "rep-total-month": "Ventes Mensuelles",
                "rep-total-profit": "Marge Net Estimée",
                "rep-best-seller": "Produit Top Vente",
                "rep-all-sales": "Journal Global des Ventes",
                "th-date": "Date & Heure",
                "th-invoice-id": "N° Ticket",
                "th-items-count": "Nbr Articles",
                "th-total": "Montant Payé",
                "th-payment-way": "Mode Paiement",
                "settings-title": "Configuration Globale",
                "settings-sub": "Mise à jour des paramètres de l'application",
                "set-store-name": "Désignation Commerciale",
                "set-phone": "Numéro d'expédition WhatsApp",
                "set-currency": "Devise locale",
                "set-msg": "Message WhatsApp de bienvenue",
                "btn-reset-db": "Réinitialisation d'usine",
                "btn-save-settings": "Sauvegarder",
                "modal-receipt-title": "Facture d'achat d'un client",
                "receipt-thank-you": "Merci pour votre visite! À très bientôt.",
                "btn-send-wa": "Envoyer via WhatsApp",
                "btn-print": "Imprimer Ticket",
                "btn-complete-and-close": "Confirmer & Clôturer la transaction",
                "all-categories": "Toutes Catégories",
                "cat-books": "Livres & Romans",
                "cat-stationery": "Papeterie & Fournitures",
                "cat-services": "Services & Autres",
            }
        };

        // Seed initial data for a quick beautiful setup if localstorage is empty
        const defaultProducts = [
            { id: "101", barcode: "978123456", name: "مقدمة ابن خلدون - غلاف مقوى", category: "كتب", author: "ابن خلدون", level: "أكاديمي", subject: "تاريخ", cost: 45.00, price: 65.00, stock: 12, minStock: 3, image: "" },
            { id: "102", barcode: "6111222333", name: "دفتر سلك 100 ورقة A4", category: "قرطاسية", author: "ماكسي", level: "جميع المستويات", subject: "مستلزمات كتابية", cost: 7.50, price: 12.00, stock: 45, minStock: 10, image: "" },
            { id: "103", barcode: "30123456", name: "علبة أقلام ملونة Maped ×24", category: "قرطاسية", author: "Maped", level: "ابتدائي/إعدادي", subject: "تلوين", cost: 14.00, price: 22.00, stock: 4, minStock: 5, image: "" },
            { id: "104", barcode: "40012345", name: "رواية أنتوني غيدنز - علم الاجتماع", category: "كتب", author: "أنتوني غيدنز", level: "جامعي", subject: "علوم إنسانية", cost: 90.00, price: 130.00, stock: 8, minStock: 2, image: "" },
            { id: "105", barcode: "611500500", name: "قلم جاف أزرق BIC", category: "قرطاسية", author: "BIC", level: "عام", subject: "مكتبة", cost: 1.00, price: 1.50, stock: 120, minStock: 15, image: "" }
        ];

        const defaultCustomers = [
            { id: "c1", name: "أحمد بنسعيد", phone: "+212611223344", points: 150, purchasesCount: 8 },
            { id: "c2", name: "فاطمة الزهراء", phone: "+212655667788", points: 45, purchasesCount: 2 }
        ];

        const defaultSuppliers = [
            { id: "s1", name: "شركة الأمل للتوزيع والتوصيل", contact: "خالد العلمي", phone: "+212622334455", debt: 1200.00 },
            { id: "s2", name: "مطبعة دار المعارف المغربية", contact: "سعيد التازي", phone: "+212522998877", debt: 0.00 }
        ];

        // Toast Helper
        function showToast(msg, type = "success") {
            const toastContainer = document.getElementById('toast-box');
            const toast = document.createElement('div');
            toast.className = `flex items-center gap-2 px-4 py-3 rounded-xl shadow-lg border text-xs font-semibold transform translate-y-2 transition duration-300 pointer-events-auto bg-white ${
                type === 'success' ? 'text-green-600 border-green-100 bg-green-50' : 'text-red-600 border-red-100 bg-red-50'
            }`;
            toast.innerHTML = `<i class="fa-solid ${type === 'success' ? 'fa-circle-check' : 'fa-circle-exclamation'} text-base"></i><span>${msg}</span>`;
            toastContainer.appendChild(toast);
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-0');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // Bootstrapping the POS
        window.addEventListener('DOMContentLoaded', () => {
            // Load from localStorage or seed
            if (!localStorage.getItem('maktabati_products')) {
                localStorage.setItem('maktabati_products', JSON.stringify(defaultProducts));
                localStorage.setItem('maktabati_customers', JSON.stringify(defaultCustomers));
                localStorage.setItem('maktabati_suppliers', JSON.stringify(defaultSuppliers));
                localStorage.setItem('maktabati_sales', JSON.stringify([]));
                localStorage.setItem('maktabati_print_jobs', JSON.stringify([]));
                localStorage.setItem('maktabati_settings', JSON.stringify(settings));
            }

            products = JSON.parse(localStorage.getItem('maktabati_products'));
            customers = JSON.parse(localStorage.getItem('maktabati_customers'));
            suppliers = JSON.parse(localStorage.getItem('maktabati_suppliers'));
            sales = JSON.parse(localStorage.getItem('maktabati_sales')) || [];
            printJobs = JSON.parse(localStorage.getItem('maktabati_print_jobs')) || [];
            settings = JSON.parse(localStorage.getItem('maktabati_settings')) || settings;

            initLiveDate();
            applyLanguage();
            buildUI();
            updateDashboard();
            initSalesChart();

            // Setup barcode listening trigger: quick input inside cashier search
            document.addEventListener('keydown', function(e) {
                if (activeTab === 'cashier' && document.activeElement !== document.getElementById('search-pos-input')) {
                    if (e.key !== 'Enter' && e.key.length === 1) {
                        const search = document.getElementById('search-pos-input');
                        search.focus();
                    }
                }
            });
        });

        function initLiveDate() {
            setInterval(() => {
                const now = new Date();
                const locale = settings.language === 'ar' ? 'ar-MA' : 'fr-FR';
                document.getElementById('live-date').innerText = now.toLocaleDateString(locale, { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
                document.getElementById('live-time').innerText = now.toLocaleTimeString(locale, { hour: '2-digit', minute: '2-digit', second: '2-digit' });
            }, 1000);
        }

        // Switch View Controller
        function switchTab(tabId) {
            activeTab = tabId;
            document.querySelectorAll('main > section').forEach(section => section.classList.add('hidden'));
            document.getElementById(`tab-${tabId}`).classList.remove('hidden');

            // Set sidebar active state
            document.querySelectorAll('aside nav button').forEach(btn => btn.classList.remove('sidebar-active'));
            const activeBtn = document.getElementById(`btn-${tabId}`);
            if (activeBtn) activeBtn.classList.add('sidebar-active');

            if (tabId === 'dashboard') updateDashboard();
            if (tabId === 'products' || tabId === 'inventory') buildUI();
            if (tabId === 'reports') updateReportsView();
        }

        // Language toggle
        function toggleLanguage() {
            settings.language = settings.language === 'ar' ? 'fr' : 'ar';
            localStorage.setItem('maktabati_settings', JSON.stringify(settings));
            applyLanguage();
            buildUI();
            if (salesChartRef) {
                salesChartRef.destroy();
                initSalesChart();
            }
        }

        function applyLanguage() {
            const isRTL = settings.language === 'ar';
            document.documentElement.dir = isRTL ? 'rtl' : 'ltr';
            document.documentElement.lang = settings.language;
            
            document.getElementById('brand-title').innerText = isRTL ? 'مكتبتي POS' : 'Maktabati POS';
            document.getElementById('brand-sub').innerText = isRTL ? 'إدارة المكتبات والقرطاسيات الذكية' : 'Smart Bookstore & Stationery Management';
            document.getElementById('lang-btn-text').innerText = isRTL ? 'Français' : 'العربية';

            // Localize text components
            document.querySelectorAll('[data-i18n]').forEach(el => {
                const key = el.getAttribute('data-i18n');
                if (dictionary[settings.language][key]) {
                    el.innerText = dictionary[settings.language][key];
                }
            });
        }

        // Save & reload helper
        function syncToStorage(key, data) {
            localStorage.setItem(key, JSON.stringify(data));
        }

        // UI Builders
        function buildUI() {
            renderPOSProducts();
            renderPOSCustomerDropdown();
            renderProductsTable();
            renderInventoryTab();
            renderPrintingSection();
            renderCustomersTable();
            renderSuppliersSection();
            renderSettingsTab();
        }

        // 1. CASHIER ENGINE
        function renderPOSProducts() {
            const grid = document.getElementById('pos-products-grid');
            grid.innerHTML = "";
            const searchVal = document.getElementById('search-pos-input').value.toLowerCase();
            const filterCat = document.getElementById('filter-pos-cat').value;

            // Simple fast matching (barcode, name, author)
            const filtered = products.filter(p => {
                const matchesSearch = p.barcode.includes(searchVal) || p.name.toLowerCase().includes(searchVal) || (p.author && p.author.toLowerCase().includes(searchVal));
                const matchesCat = filterCat === 'all' || p.category === filterCat;
                return matchesSearch && matchesCat;
            });

            // Instant scan feature: if exactly 1 product matches search by exact barcode, auto add and clear search
            if (searchVal && filtered.length === 1 && filtered[0].barcode === searchVal) {
                addToCart(filtered[0].id);
                document.getElementById('search-pos-input').value = "";
                renderPOSProducts();
                return;
            }

            if (filtered.length === 0) {
                grid.innerHTML = `<div class="col-span-full py-12 text-center text-slate-400 text-sm"><i class="fa-solid fa-box-open text-3xl mb-2"></i><br>لم يتم العثور على أي مواد مطابقة</div>`;
                return;
            }

            filtered.forEach(p => {
                const isLow = p.stock <= p.minStock;
                const card = document.createElement('div');
                card.onclick = () => addToCart(p.id);
                card.className = "bg-white p-3 rounded-xl border border-slate-200 shadow-sm cursor-pointer hover:border-primary-500 hover:shadow transition flex flex-col justify-between";
                card.innerHTML = `
                    <div class="h-20 bg-slate-100 rounded-lg flex items-center justify-center text-slate-300 relative overflow-hidden mb-2">
                        ${p.image ? `<img src="${p.image}" class="w-full h-full object-cover">` : `<i class="fa-solid fa-image text-2xl"></i>`}
                        ${isLow ? `<span class="absolute top-1 right-1 bg-amber-500 text-white text-[9px] px-1.5 py-0.5 rounded-full font-bold">مخزون حرج</span>` : ''}
                    </div>
                    <h5 class="text-xs font-semibold text-slate-700 line-clamp-2 min-h-[2rem]">${p.name}</h5>
                    <div class="flex justify-between items-center mt-2 pt-2 border-t border-slate-100">
                        <span class="text-xs text-slate-400 font-mono">Qty: ${p.stock}</span>
                        <span class="text-sm font-bold text-primary-600">${parseFloat(p.price).toFixed(2)} ${settings.storeCurrency}</span>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        function filterPOSProducts() {
            renderPOSProducts();
        }

        function renderPOSCustomerDropdown() {
            const select = document.getElementById('pos-customer-select');
            select.innerHTML = `<option value="">${dictionary[settings.language]['walk-in-customer']}</option>`;
            customers.forEach(c => {
                select.innerHTML += `<option value="${c.id}">${c.name} (${c.phone}) - [${c.points} pts]</option>`;
            });
        }

        function addToCart(productId) {
            const product = products.find(p => p.id == productId);
            if (!product) return;

            if (product.stock <= 0) {
                showToast("المنتج نافد من المخزون تماماً!", "danger");
                return;
            }

            const existing = cart.find(item => item.product.id == productId);
            if (existing) {
                if (existing.qty >= product.stock) {
                    showToast("تم تجاوز الكمية المتوفرة بالمخزن!", "danger");
                    return;
                }
                existing.qty++;
            } else {
                cart.push({ product, qty: 1 });
            }
            renderCart();
            showToast("تمت إضافة المنتج للسلة");
        }

        function renderCart() {
            const container = document.getElementById('cart-list');
            container.innerHTML = "";

            if (cart.length === 0) {
                container.innerHTML = `<div class="h-full flex flex-col items-center justify-center text-slate-300"><i class="fa-solid fa-cart-flatbed text-4xl mb-2"></i><span class="text-xs">السلة فارغة حالياً</span></div>`;
                updateCartTotals();
                return;
            }

            cart.forEach((item, index) => {
                const sub = parseFloat(item.product.price) * item.qty;
                const row = document.createElement('div');
                row.className = "flex items-center justify-between gap-2 p-2 border border-slate-100 rounded-lg hover:bg-slate-50";
                row.innerHTML = `
                    <div class="flex-1">
                        <h6 class="text-xs font-semibold text-slate-700 line-clamp-1">${item.product.name}</h6>
                        <span class="text-[10px] text-slate-400">${item.product.price} × ${item.qty}</span>
                    </div>
                    <div class="flex items-center gap-1.5">
                        <button onclick="changeCartQty(${index}, -1)" class="w-6 h-6 border rounded flex items-center justify-center text-xs font-bold hover:bg-slate-200">-</button>
                        <span class="text-xs font-mono w-5 text-center font-bold">${item.qty}</span>
                        <button onclick="changeCartQty(${index}, 1)" class="w-6 h-6 border rounded flex items-center justify-center text-xs font-bold hover:bg-slate-200">+</button>
                    </div>
                    <div class="text-right pl-2">
                        <span class="text-xs font-bold text-slate-800">${sub.toFixed(2)}</span>
                    </div>
                `;
                container.appendChild(row);
            });
            updateCartTotals();
        }

        function changeCartQty(index, dir) {
            const item = cart[index];
            if (dir === 1) {
                if (item.qty >= item.product.stock) {
                    showToast("تم تجاوز المخزون المتاح!", "danger");
                    return;
                }
                item.qty++;
            } else {
                item.qty--;
                if (item.qty <= 0) {
                    cart.splice(index, 1);
                }
            }
            renderCart();
        }

        function clearCart() {
            cart = [];
            renderCart();
            showToast("تم إفراغ سلة البيع");
        }

        function updateCartTotals() {
            let subtotal = 0;
            cart.forEach(item => {
                subtotal += parseFloat(item.product.price) * item.qty;
            });

            const discountPct = parseFloat(document.getElementById('cart-discount-input').value) || 0;
            const discountAmt = (subtotal * discountPct) / 100;
            const finalTotal = subtotal - discountAmt;

            document.getElementById('cart-subtotal').innerText = `${subtotal.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('cart-total-val').innerText = `${finalTotal.toFixed(2)} ${settings.storeCurrency}`;
        }

        function setPaymentMethod(method) {
            activePaymentMethod = method;
            const cashBtn = document.getElementById('btn-pay-cash');
            const cardBtn = document.getElementById('btn-pay-card');

            if (method === 'cash') {
                cashBtn.className = "py-2 border border-primary-500 bg-primary-50 text-primary-700 rounded-lg text-xs font-semibold flex items-center justify-center gap-1";
                cardBtn.className = "py-2 border border-slate-200 text-slate-600 rounded-lg text-xs font-semibold flex items-center justify-center gap-1";
            } else {
                cardBtn.className = "py-2 border border-primary-500 bg-primary-50 text-primary-700 rounded-lg text-xs font-semibold flex items-center justify-center gap-1";
                cashBtn.className = "py-2 border border-slate-200 text-slate-600 rounded-lg text-xs font-semibold flex items-center justify-center gap-1";
            }
        }

        // CHECKOUT & RECEIPT
        function openPaymentModal() {
            if (cart.length === 0) {
                showToast("السلة فارغة. يرجى اختيار سلع للبيع!", "danger");
                return;
            }

            // Generate receipt preview inside modal
            document.getElementById('receipt-store-name').innerText = settings.storeName;
            document.getElementById('receipt-store-desc').innerText = dictionary[settings.language]['brand-sub'];
            document.getElementById('receipt-datetime').innerText = new Date().toLocaleString(settings.language === 'ar' ? 'ar-MA' : 'fr-FR');
            
            const randId = Math.floor(100000 + Math.random() * 900000);
            document.getElementById('receipt-id').innerText = `#TX-${randId}`;
            
            document.getElementById('receipt-pay-method').innerText = activePaymentMethod === 'cash' ? dictionary[settings.language]['pay-cash'] : dictionary[settings.language]['pay-card'];

            // Customer Loyalty check
            const custId = document.getElementById('pos-customer-select').value;
            const customerRow = document.getElementById('receipt-customer-row');
            if (custId) {
                const customer = customers.find(c => c.id === custId);
                if (customer) {
                    document.getElementById('receipt-customer-name').innerText = customer.name;
                    customerRow.classList.remove('hidden');
                }
            } else {
                customerRow.classList.add('hidden');
            }

            // Render receipt items
            const itemsCont = document.getElementById('receipt-items-container');
            itemsCont.innerHTML = "";

            let subtotal = 0;
            cart.forEach(item => {
                const itemSub = parseFloat(item.product.price) * item.qty;
                subtotal += itemSub;
                itemsCont.innerHTML += `
                    <div class="flex justify-between">
                        <span>${item.product.name} × ${item.qty}</span>
                        <span class="font-mono">${itemSub.toFixed(2)}</span>
                    </div>
                `;
            });

            const discountPct = parseFloat(document.getElementById('cart-discount-input').value) || 0;
            const discountAmt = (subtotal * discountPct) / 100;
            const finalTotal = subtotal - discountAmt;

            document.getElementById('receipt-subtotal').innerText = `${subtotal.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('receipt-discount').innerText = `-${discountAmt.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('receipt-total').innerText = `${finalTotal.toFixed(2)} ${settings.storeCurrency}`;

            // Show Dialog
            document.getElementById('modal-payment').classList.remove('hidden');
        }

        function closePaymentModal() {
            document.getElementById('modal-payment').classList.add('hidden');
        }

        function confirmAndCloseSale() {
            // Deduct Stocks
            cart.forEach(item => {
                const dbProd = products.find(p => p.id === item.product.id);
                if (dbProd) {
                    dbProd.stock = Math.max(0, dbProd.stock - item.qty);
                }
            });

            // Loyalty Points Accumulation (1 point per 10 currency spent)
            const custId = document.getElementById('pos-customer-select').value;
            let finalPaid = parseFloat(document.getElementById('receipt-total').innerText);
            if (custId) {
                const customer = customers.find(c => c.id === custId);
                if (customer) {
                    const earnedPoints = Math.floor(finalPaid / 10);
                    customer.points += earnedPoints;
                    customer.purchasesCount++;
                    showToast(`تمت إضافة ${earnedPoints} نقطة ولاء للزبون!`);
                }
            }

            // Record transaction
            const saleInvoice = {
                id: document.getElementById('receipt-id').innerText,
                date: new Date().toISOString(),
                itemsCount: cart.reduce((acc, current) => acc + current.qty, 0),
                total: finalPaid,
                profit: cart.reduce((acc, current) => {
                    const cost = current.product.cost || 0;
                    const revenue = current.product.price;
                    return acc + ((revenue - cost) * current.qty);
                }, 0),
                paymentMethod: activePaymentMethod
            };

            sales.push(saleInvoice);
            syncToStorage('maktabati_products', products);
            syncToStorage('maktabati_sales', sales);
            syncToStorage('maktabati_customers', customers);

            // Clear cashier state
            cart = [];
            document.getElementById('cart-discount-input').value = 0;
            renderCart();
            closePaymentModal();
            buildUI();
            updateDashboard();
            showToast("تم حفظ عملية البيع بنجاح");
        }

        function printReceipt() {
            window.print();
        }

        function sendReceiptWhatsApp() {
            const customerPhone = settings.storePhone; 
            const storeName = settings.storeName;
            const invoiceId = document.getElementById('receipt-id').innerText;
            const total = document.getElementById('receipt-total').innerText;
            
            // Build text message
            let text = `*${storeName}*\n`;
            text += `فاتورة بيع رقم: ${invoiceId}\n`;
            text += `التاريخ: ${new Date().toLocaleDateString()}\n`;
            text += `--------------------------\n`;
            
            cart.forEach(item => {
                text += `• ${item.product.name} × ${item.qty} : ${(item.product.price * item.qty).toFixed(2)}\n`;
            });
            text += `--------------------------\n`;
            text += `*الإجمالي المفوتر: ${total}*\n`;
            text += `شكراً لزيارتكم!`;

            const encoded = encodeURIComponent(text);
            const url = `https://api.whatsapp.com/send?phone=${customerPhone}&text=${encoded}`;
            window.open(url, '_blank');
        }


        // 2. PRODUCTS ENGINE
        function renderProductsTable() {
            const tbody = document.getElementById('products-table-body');
            tbody.innerHTML = "";

            products.forEach(p => {
                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50";
                tr.innerHTML = `
                    <td class="p-4 font-mono text-xs">${p.barcode}</td>
                    <td class="p-4 font-semibold text-slate-800">${p.name}</td>
                    <td class="p-4"><span class="bg-slate-100 text-slate-700 px-2 py-1 rounded text-xs">${p.category}</span></td>
                    <td class="p-4 text-xs">${p.author || '--'}</td>
                    <td class="p-4 text-xs">${p.level || '--'} / ${p.subject || '--'}</td>
                    <td class="p-4 font-semibold text-slate-700">${parseFloat(p.price).toFixed(2)}</td>
                    <td class="p-4 font-mono font-bold">${p.stock}</td>
                    <td class="p-4">
                        <div class="flex gap-2">
                            <button onclick="editProduct('${p.id}')" class="text-primary-600 hover:text-primary-800"><i class="fa-solid fa-pen-to-square"></i></button>
                            <button onclick="deleteProduct('${p.id}')" class="text-red-500 hover:text-red-700"><i class="fa-solid fa-trash-can"></i></button>
                        </div>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function openProductModal(prodId = null) {
            const modal = document.getElementById('modal-product');
            const title = document.getElementById('modal-product-title');
            
            if (prodId) {
                const p = products.find(item => item.id == prodId);
                title.innerText = "تعديل المنتج";
                document.getElementById('prod-id').value = p.id;
                document.getElementById('prod-barcode').value = p.barcode;
                document.getElementById('prod-name').value = p.name;
                document.getElementById('prod-category').value = p.category;
                document.getElementById('prod-author').value = p.author || '';
                document.getElementById('prod-level').value = p.level || '';
                document.getElementById('prod-subject').value = p.subject || '';
                document.getElementById('prod-cost').value = p.cost || 0;
                document.getElementById('prod-price').value = p.price;
                document.getElementById('prod-stock').value = p.stock;
                document.getElementById('prod-min-stock').value = p.minStock || 5;
                document.getElementById('prod-image').value = p.image || '';
            } else {
                title.innerText = "إضافة منتج جديد";
                document.getElementById('prod-id').value = "";
                document.getElementById('prod-barcode').value = Math.floor(100000000 + Math.random() * 900000000);
                document.getElementById('prod-name').value = "";
                document.getElementById('prod-author').value = "";
                document.getElementById('prod-level').value = "";
                document.getElementById('prod-subject').value = "";
                document.getElementById('prod-cost').value = "";
                document.getElementById('prod-price').value = "";
                document.getElementById('prod-stock').value = "";
                document.getElementById('prod-min-stock').value = 5;
                document.getElementById('prod-image').value = "";
            }
            modal.classList.remove('hidden');
        }

        function closeProductModal() {
            document.getElementById('modal-product').classList.add('hidden');
        }

        function saveProduct() {
            const id = document.getElementById('prod-id').value;
            const barcode = document.getElementById('prod-barcode').value;
            const name = document.getElementById('prod-name').value;
            const category = document.getElementById('prod-category').value;
            const author = document.getElementById('prod-author').value;
            const level = document.getElementById('prod-level').value;
            const subject = document.getElementById('prod-subject').value;
            const cost = parseFloat(document.getElementById('prod-cost').value) || 0;
            const price = parseFloat(document.getElementById('prod-price').value);
            const stock = parseInt(document.getElementById('prod-stock').value) || 0;
            const minStock = parseInt(document.getElementById('prod-min-stock').value) || 5;
            const image = document.getElementById('prod-image').value;

            if (!name || !price) {
                showToast("يرجى ملء الحقول الإجبارية (الاسم والسعر)!", "danger");
                return;
            }

            if (id) {
                // Edit mode
                const idx = products.findIndex(p => p.id == id);
                products[idx] = { ...products[idx], barcode, name, category, author, level, subject, cost, price, stock, minStock, image };
                showToast("تم تحديث المنتج بنجاح");
            } else {
                // New product
                const newId = Date.now().toString();
                products.push({ id: newId, barcode, name, category, author, level, subject, cost, price, stock, minStock, image });
                showToast("تمت إضافة المنتج الجديد");
            }

            syncToStorage('maktabati_products', products);
            closeProductModal();
            buildUI();
        }

        function editProduct(id) {
            openProductModal(id);
        }

        function deleteProduct(id) {
            if (confirm("هل أنت متأكد من حذف هذا المنتج؟")) {
                products = products.filter(p => p.id != id);
                syncToStorage('maktabati_products', products);
                buildUI();
                showToast("تم حذف المنتج", "danger");
            }
        }


        // 3. INVENTORY ENGINE
        function renderInventoryTab() {
            const tbody = document.getElementById('inventory-alerts-body');
            const selectAdjust = document.getElementById('inv-adjust-product');
            tbody.innerHTML = "";
            selectAdjust.innerHTML = "";

            let alertsCount = 0;

            products.forEach(p => {
                const isLow = p.stock <= p.minStock;
                
                // Add to dropdown
                selectAdjust.innerHTML += `<option value="${p.id}">${p.name} (متوفر: ${p.stock})</option>`;

                if (isLow) {
                    alertsCount++;
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="py-3 font-semibold text-slate-800">${p.name}</td>
                        <td class="py-3 text-center font-mono text-amber-600 font-bold">${p.stock}</td>
                        <td class="py-3 text-center font-mono">${p.minStock}</td>
                        <td class="py-3 text-center"><span class="bg-red-50 text-red-600 px-2.5 py-0.5 rounded-full text-xs font-bold">مخزون منخفض</span></td>
                    `;
                    tbody.appendChild(tr);
                }
            });

            if (alertsCount === 0) {
                tbody.innerHTML = `<tr><td colspan="4" class="text-center py-6 text-slate-400 text-xs">المخزون مستقر، لا توجد مواد تواجه نفاداً</td></tr>`;
            }

            // Update warning badges
            const badge = document.getElementById('stock-alert-badge');
            if (alertsCount > 0) {
                badge.innerText = alertsCount;
                badge.classList.remove('hidden');
            } else {
                badge.classList.add('hidden');
            }
        }

        function saveQuickStockAdjust() {
            const pId = document.getElementById('inv-adjust-product').value;
            const newQty = parseInt(document.getElementById('inv-adjust-qty').value);

            if (isNaN(newQty) || newQty < 0) {
                showToast("يرجى إدخال كمية صحيحة!", "danger");
                return;
            }

            const p = products.find(item => item.id == pId);
            if (p) {
                p.stock = newQty;
                syncToStorage('maktabati_products', products);
                buildUI();
                showToast("تم تحديث مستوى المخزون للقطعة");
                document.getElementById('inv-adjust-qty').value = "";
            }
        }


        // 4. PRINTING AND REPROGRAPHY GATEWAY
        function prefillPrintPrices() {
            const type = document.getElementById('print-type').value;
            const costInput = document.getElementById('print-cost-unit');
            const priceInput = document.getElementById('print-price-unit');

            if (type === 'black-white') {
                costInput.value = "0.10";
                priceInput.value = "0.50";
            } else if (type === 'color') {
                costInput.value = "0.50";
                priceInput.value = "2.00";
            } else if (type === 'binding') {
                costInput.value = "2.00";
                priceInput.value = "10.00";
            }
            calcPrintJob();
        }

        function calcPrintJob() {
            const pages = parseInt(document.getElementById('print-pages').value) || 1;
            const copies = parseInt(document.getElementById('print-copies').value) || 1;
            const costUnit = parseFloat(document.getElementById('print-cost-unit').value) || 0;
            const priceUnit = parseFloat(document.getElementById('print-price-unit').value) || 0;

            const totalCost = pages * copies * costUnit;
            const totalRev = pages * copies * priceUnit;
            const totalProfit = totalRev - totalCost;

            document.getElementById('print-total-cost-display').innerText = `${totalCost.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('print-total-rev-display').innerText = `${totalRev.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('print-calc-profit-display').innerText = `${totalProfit.toFixed(2)} ${settings.storeCurrency}`;
        }

        function registerPrintJob() {
            const typeValue = document.getElementById('print-type').value;
            const pages = parseInt(document.getElementById('print-pages').value) || 1;
            const copies = parseInt(document.getElementById('print-copies').value) || 1;
            
            const costUnit = parseFloat(document.getElementById('print-cost-unit').value) || 0;
            const priceUnit = parseFloat(document.getElementById('print-price-unit').value) || 0;

            const totalCost = pages * copies * costUnit;
            const totalRev = pages * copies * priceUnit;
            const totalProfit = totalRev - totalCost;

            let printName = "";
            if (typeValue === 'black-white') printName = dictionary[settings.language]['print-bw'];
            else if (typeValue === 'color') printName = dictionary[settings.language]['print-color'];
            else printName = dictionary[settings.language]['print-binding'];

            const job = {
                id: Date.now().toString(),
                date: new Date().toISOString(),
                type: printName,
                volume: `${pages} 📄 × ${copies} 👥`,
                price: totalRev,
                profit: totalProfit,
                pagesCount: pages * copies
            };

            printJobs.push(job);
            syncToStorage('maktabati_print_jobs', printJobs);
            
            // Register as automatic Cash sale in ledger
            const saleInvoice = {
                id: `#PRT-${Math.floor(1000 + Math.random()*9000)}`,
                date: new Date().toISOString(),
                itemsCount: pages * copies,
                total: totalRev,
                profit: totalProfit,
                paymentMethod: "cash"
            };
            sales.push(saleInvoice);
            syncToStorage('maktabati_sales', sales);

            buildUI();
            showToast("تم تسجيل وحفظ عملية خدمات الطباعة");
        }

        function renderPrintingSection() {
            const tbody = document.getElementById('print-jobs-body');
            tbody.innerHTML = "";

            const todaysJobs = printJobs.filter(j => new Date(j.date).toDateString() === new Date().toDateString());

            todaysJobs.forEach(j => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-3 font-mono text-xs">${new Date(j.date).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</td>
                    <td class="p-3 font-semibold">${j.type}</td>
                    <td class="p-3 text-center font-mono text-xs">${j.volume}</td>
                    <td class="p-3 text-center font-bold text-slate-800">${parseFloat(j.price).toFixed(2)}</td>
                    <td class="p-3 text-center text-green-600 font-bold">${parseFloat(j.profit).toFixed(2)}</td>
                `;
                tbody.appendChild(tr);
            });

            if (todaysJobs.length === 0) {
                tbody.innerHTML = `<tr><td colspan="5" class="text-center py-6 text-slate-400 text-xs">لا توجد عمليات طباعة مسجلة اليوم</td></tr>`;
            }
        }


        // 5. CUSTOMERS ENGINE
        function renderCustomersTable() {
            const tbody = document.getElementById('customers-table-body');
            tbody.innerHTML = "";

            customers.forEach(c => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-4 font-semibold text-slate-800">${c.name}</td>
                    <td class="p-4 font-mono text-xs">${c.phone}</td>
                    <td class="p-4 text-center"><span class="bg-indigo-50 text-indigo-600 px-3 py-1 rounded-full font-bold font-mono text-xs">${c.points} pts</span></td>
                    <td class="p-4 text-center font-mono">${c.purchasesCount}</td>
                    <td class="p-4">
                        <button onclick="deleteCustomer('${c.id}')" class="text-red-500 hover:text-red-700 text-xs flex items-center gap-1"><i class="fa-solid fa-trash-can"></i> حذف</button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function openCustomerModal() {
            document.getElementById('modal-customer').classList.remove('hidden');
        }

        function closeCustomerModal() {
            document.getElementById('modal-customer').classList.add('hidden');
        }

        function saveCustomer() {
            const name = document.getElementById('cust-name').value;
            const phone = document.getElementById('cust-phone').value;

            if (!name || !phone) {
                showToast("يرجى تعبئة الحقول الإلزامية!", "danger");
                return;
            }

            const newCust = {
                id: 'c-' + Date.now().toString(),
                name,
                phone,
                points: 0,
                purchasesCount: 0
            };

            customers.push(newCust);
            syncToStorage('maktabati_customers', customers);
            
            document.getElementById('cust-name').value = "";
            document.getElementById('cust-phone').value = "";
            closeCustomerModal();
            buildUI();
            showToast("تم حفظ العميل بنجاح في نظام الموالاة");
        }

        function deleteCustomer(id) {
            if (confirm("هل ترغب في مسح هذا العميل نهائياً من النظام؟")) {
                customers = customers.filter(c => c.id !== id);
                syncToStorage('maktabati_customers', customers);
                buildUI();
                showToast("تم الحذف", "danger");
            }
        }


        // 6. SUPPLIERS ENGINE
        function renderSuppliersSection() {
            const tbody = document.getElementById('suppliers-table-body');
            const select = document.getElementById('sup-select');
            tbody.innerHTML = "";
            select.innerHTML = "";

            suppliers.forEach(s => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-4 font-bold text-slate-800">${s.name}</td>
                    <td class="p-4">${s.contact}</td>
                    <td class="p-4 font-mono text-xs">${s.phone}</td>
                    <td class="p-4 text-center font-mono font-semibold text-red-500">${parseFloat(s.debt).toFixed(2)}</td>
                    <td class="p-4 flex gap-2">
                        <button onclick="settleSupplierDebt('${s.id}')" class="text-green-600 hover:text-green-800 text-xs font-semibold">تسوية ديون</button>
                        <button onclick="deleteSupplier('${s.id}')" class="text-red-500 hover:text-red-700 text-xs"><i class="fa-solid fa-trash-can"></i></button>
                    </td>
                `;
                tbody.appendChild(tr);

                // Add to Invoice Form
                select.innerHTML += `<option value="${s.id}">${s.name}</option>`;
            });
        }

        function openSupplierModal() {
            document.getElementById('modal-supplier').classList.remove('hidden');
        }

        function closeSupplierModal() {
            document.getElementById('modal-supplier').classList.add('hidden');
        }

        function saveSupplier() {
            const name = document.getElementById('sup-name-input').value;
            const contact = document.getElementById('sup-contact-input').value;
            const phone = document.getElementById('sup-phone-input').value;

            if (!name || !contact || !phone) {
                showToast("يرجى تعبئة جميع الخانات!", "danger");
                return;
            }

            const newSup = {
                id: 's-' + Date.now().toString(),
                name,
                contact,
                phone,
                debt: 0.00
            };

            suppliers.push(newSup);
            syncToStorage('maktabati_suppliers', suppliers);
            
            document.getElementById('sup-name-input').value = "";
            document.getElementById('sup-contact-input').value = "";
            document.getElementById('sup-phone-input').value = "";
            closeSupplierModal();
            buildUI();
            showToast("تمت إضافة المورد الجديد للمكتبة");
        }

        function registerSupplierPurchase() {
            const supId = document.getElementById('sup-select').value;
            const total = parseFloat(document.getElementById('sup-invoice-amount').value) || 0;
            const paid = parseFloat(document.getElementById('sup-invoice-paid').value) || 0;

            if (!supId || total <= 0) {
                showToast("يرجى إدخال تفاصيل الفاتورة وقيمتها!", "danger");
                return;
            }

            const debtValue = total - paid;
            const s = suppliers.find(item => item.id === supId);
            if (s) {
                s.debt += debtValue;
                syncToStorage('maktabati_suppliers', suppliers);
                buildUI();
                showToast("تم تسجيل عملية التوريد وتعديل الديون");
                document.getElementById('sup-invoice-amount').value = "";
                document.getElementById('sup-invoice-paid').value = "";
            }
        }

        function settleSupplierDebt(id) {
            const s = suppliers.find(item => item.id === id);
            if (!s) return;

            const amt = parseFloat(prompt(`مجموع ديون المورد ${s.name} هو: ${s.debt.toFixed(2)} ${settings.storeCurrency}\nأدخل المبلغ المراد تسويته (المدفوع حالياً):`));
            if (!isNaN(amt) && amt > 0) {
                s.debt = Math.max(0, s.debt - amt);
                syncToStorage('maktabati_suppliers', suppliers);
                buildUI();
                showToast("تم تحديث الديون المدفوعة بنجاح");
            }
        }

        function deleteSupplier(id) {
            if (confirm("هل ترغب بإزالة المورد من اللائحة؟")) {
                suppliers = suppliers.filter(item => item.id !== id);
                syncToStorage('maktabati_suppliers', suppliers);
                buildUI();
                showToast("تم الإقصاء", "danger");
            }
        }


        // 7. DASHBOARD & STATS ENGINE
        function updateDashboard() {
            const todaySales = sales.filter(s => new Date(s.date).toDateString() === new Date().toDateString());
            
            const totalSalesAmt = todaySales.reduce((acc, curr) => acc + curr.total, 0);
            const totalProfitAmt = todaySales.reduce((acc, curr) => acc + curr.profit, 0);

            document.getElementById('dash-sales-val').innerText = `${totalSalesAmt.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('dash-profit-val').innerText = `${totalProfitAmt.toFixed(2)} ${settings.storeCurrency}`;

            // Low stocks calculation
            const lowCount = products.filter(p => p.stock <= p.minStock).length;
            document.getElementById('dash-lowstock-val').innerText = lowCount;

            // Customers count
            document.getElementById('dash-customers-val').innerText = customers.length;

            // Print stats integration
            const todayPrints = printJobs.filter(j => new Date(j.date).toDateString() === new Date().toDateString());
            const totalPagesPrinted = todayPrints.reduce((acc, curr) => acc + curr.pagesCount, 0);
            const printRevenue = todayPrints.reduce((acc, curr) => acc + curr.price, 0);
            const printProfit = todayPrints.reduce((acc, curr) => acc + curr.profit, 0);

            document.getElementById('dash-print-pages').innerText = totalPagesPrinted;
            document.getElementById('dash-print-revenue').innerText = `${printRevenue.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('dash-print-profit').innerText = `${printProfit.toFixed(2)} ${settings.storeCurrency}`;
        }

        // REPORTS & GRAPHS
        function updateReportsView() {
            const monthlySales = sales.reduce((acc, curr) => acc + curr.total, 0);
            const monthlyProfit = sales.reduce((acc, curr) => acc + curr.profit, 0);

            document.getElementById('rep-monthly-sales').innerText = `${monthlySales.toFixed(2)} ${settings.storeCurrency}`;
            document.getElementById('rep-monthly-profit').innerText = `${monthlyProfit.toFixed(2)} ${settings.storeCurrency}`;

            // Top Seller Calculation (Simplistic Mock matching)
            let topSeller = "لا توجد مبيعات كافية";
            if (sales.length > 0) {
                topSeller = products[0] ? products[0].name : "تحديث المبيعات...";
            }
            document.getElementById('rep-top-seller').innerText = topSeller;

            // Sales Log rendering
            const tbody = document.getElementById('sales-log-body');
            tbody.innerHTML = "";

            sales.forEach(s => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-3 font-mono text-xs">${new Date(s.date).toLocaleString()}</td>
                    <td class="p-3 font-bold">${s.id}</td>
                    <td class="p-3 text-center">${s.itemsCount}</td>
                    <td class="p-3 text-center font-bold text-slate-800">${parseFloat(s.total).toFixed(2)}</td>
                    <td class="p-3 text-center"><span class="bg-green-100 text-green-800 px-2 py-0.5 rounded text-xs">${s.paymentMethod === 'cash' ? 'كاش' : 'بطاقة'}</span></td>
                `;
                tbody.appendChild(tr);
            });
        }

        function initSalesChart() {
            const ctx = document.getElementById('salesChart').getContext('2d');
            const isAr = settings.language === 'ar';

            // Pre-fill daily simulated sales curve points
            const labels = isAr ? ['الأسبوع 1', 'الأسبوع 2', 'الأسبوع 3', 'الأسبوع 4'] : ['Semaine 1', 'Semaine 2', 'Semaine 3', 'Semaine 4'];
            
            // Calc historical totals
            let w1 = 0, w2 = 0, w3 = 0, w4 = 0;
            sales.forEach((s, idx) => {
                if (idx % 4 === 0) w1 += s.total;
                else if (idx % 4 === 1) w2 += s.total;
                else if (idx % 4 === 2) w3 += s.total;
                else w4 += s.total;
            });
            
            // Add minimum mockup if database empty for beautiful visuals
            const datasetSales = (w1+w2+w3+w4 === 0) ? [400, 800, 600, 1200] : [w1 + 100, w2 + 200, w3 + 150, w4 + 300];
            const datasetProfit = datasetSales.map(val => val * 0.35); // 35% margin mock

            salesChartRef = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [
                        {
                            label: isAr ? 'المبيعات' : 'Ventes',
                            data: datasetSales,
                            borderColor: '#0284c7',
                            backgroundColor: 'rgba(2, 132, 199, 0.1)',
                            fill: true,
                            tension: 0.3
                        },
                        {
                            label: isAr ? 'الأرباح التقديرية' : 'Bénéfices Est.',
                            data: datasetProfit,
                            borderColor: '#16a34a',
                            backgroundColor: 'transparent',
                            borderDash: [5, 5],
                            tension: 0.3
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'top' }
                    }
                }
            });
        }


        // 8. SYSTEM SETTINGS
        function renderSettingsTab() {
            document.getElementById('set-store-name-input').value = settings.storeName;
            document.getElementById('set-store-phone-input').value = settings.storePhone;
            document.getElementById('set-store-currency-input').value = settings.storeCurrency;
            document.getElementById('set-store-message-input').value = settings.storeMessage;
        }

        function saveAppSettings() {
            settings.storeName = document.getElementById('set-store-name-input').value;
            settings.storePhone = document.getElementById('set-store-phone-input').value;
            settings.storeCurrency = document.getElementById('set-store-currency-input').value;
            settings.storeMessage = document.getElementById('set-store-message-input').value;

            syncToStorage('maktabati_settings', settings);
            applyLanguage();
            buildUI();
            updateDashboard();
            showToast("تم تحديث وحفظ الإعدادات للنظام بنجاح");
        }

        function resetAppDatabase() {
            if (confirm("تحذير: هل أنت متأكد من تصفير كافة قواعد البيانات؟ سيؤدي ذلك لمسح المنتجات، المبيعات، الموردين والزبائن بالكامل واستعادة التهيئة الأولية.")) {
                localStorage.clear();
                window.location.reload();
            }
        }
    </script>
</body>
</html
