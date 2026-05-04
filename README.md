<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>كاشير برو - الواجهة السحابية</title>
    <link rel="manifest" href="data:application/manifest+json,{'name':'Cashier Pro','short_name':'Cashier','start_url':'.','display':'standalone','background_color':'#f4f6f9','theme_color':'#2c3e50'}">
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://unpkg.com/html5-qrcode"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap');
        
        :root {
            --bg-light: #f4f6f9;
            --text-dark: #2c3e50;
            --white: #ffffff;
            --main-pink: #ff85a2; 
            --g-purchases: linear-gradient(135deg, #e74c3c 0%, #c0392b 100%);
            --g-sales: linear-gradient(135deg, #f39c12 0%, #e67e22 100%);
            --g-reports: linear-gradient(135deg, #27ae60 0%, #1e8449 100%);
            --g-inventory: linear-gradient(135deg, #2c3e50 0%, #1a252f 100%);
            --g-staff: linear-gradient(135deg, #9b59b6 0%, #8e44ad 100%);
            --g-accounts: linear-gradient(135deg, #1abc9c 0%, #16a085 100%);
            --g-store: linear-gradient(135deg, #3498db 0%, #2980b9 100%);
            --g-subs: linear-gradient(135deg, #6c5ce7 0%, #a29bfe 100%); 
        } 

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { font-family: 'Cairo', sans-serif; background: var(--bg-light); color: var(--text-dark); margin: 0; padding-bottom: 20px; overflow-x: hidden; } 

        header { 
            background: rgba(44, 62, 80, 0.95); 
            color: white; padding: 20px; display: flex; justify-content: space-between; align-items: center; 
            position: sticky; top: 0; z-index: 1000; box-shadow: 0 4px 10px rgba(0,0,0,0.1); backdrop-filter: blur(5px);
            display: none; 
        } 

        .header-left { display: flex; gap: 15px; align-items: center; }
        .header-logo { font-size: 1.4rem; font-weight: 700; color: #ffeb3b; cursor: pointer; }
        .icon-btn { color: white; border: none; background: none; font-size: 1.2rem; cursor: pointer; position: relative; }
        .notif-dot { position: absolute; top: -2px; right: -2px; width: 10px; height: 10px; background: #e74c3c; border-radius: 50%; border: 2px solid #2c3e50; display: none; } 

        .sidebar {
            position: fixed; top: 0; right: -280px; width: 280px; height: 100%;
            background: #2c3e50; color: white; z-index: 3000; transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            padding: 20px; box-shadow: -5px 0 15px rgba(0,0,0,0.2);
        }
        .sidebar.active { right: 0; }
        .sidebar-header { border-bottom: 1px solid rgba(255,255,255,0.1); padding-bottom: 15px; margin-bottom: 20px; text-align: center; }
        .sidebar-header h3 { color: #ffeb3b; margin: 0; font-size: 1.2rem; }
        .sidebar-header p { font-size: 0.9rem; opacity: 0.8; margin: 5px 0 0; }
        .sidebar-menu a {
            display: flex; align-items: center; gap: 12px; color: white; text-decoration: none; padding: 15px;
            border-radius: 12px; margin-bottom: 8px; transition: 0.2s; background: rgba(255,255,255,0.05);
        }
        .sidebar-menu a:active { background: rgba(255,255,255,0.2); }
        .overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.5); display: none; z-index: 2500; backdrop-filter: blur(2px);
        } 

        .container { padding: 15px; max-width: 900px; margin: auto; margin-top: 15px; }
        .screen { display: none; animation: fadeIn 0.3s ease; }
        .screen.active { display: block; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; } 

        .card { 
            display: flex; flex-direction: column; justify-content: center; align-items: center; text-align: center; 
            padding: 25px 15px; border-radius: 20px; color: white; cursor: pointer; box-shadow: 0 8px 16px rgba(0,0,0,0.1); 
            transition: 0.3s; position: relative; overflow: hidden; border: 1px solid rgba(255,255,255,0.1);
        } 

        .card:active { transform: scale(0.96); }
        .card.c-purchases { background: var(--g-purchases); }
        .card.c-sales { background: var(--g-sales); }
        .card.c-reports { background: var(--g-reports); }
        .card.c-inventory { background: var(--g-inventory); }
        .card.c-staff { background: var(--g-staff); }
        .card.c-accounts { background: var(--g-accounts); }
        .card.c-store { background: var(--g-store); }
        .card.c-subs { background: var(--g-subs); } 

        .card-icon { font-size: 2.2rem; margin-bottom: 12px; opacity: 0.9; background: rgba(255,255,255,0.15); padding: 15px; border-radius: 50%; }
        .card-title { font-size: 1rem; font-weight: 700; margin: 0; }
        .card-sub { font-size: 0.8rem; opacity: 0.8; margin-top: 4px; font-weight: 400; } 

        .inner-card { background: white; border-radius: 20px; padding: 20px; box-shadow: 0 5px 15px rgba(0,0,0,0.05); margin-bottom: 20px; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 12px; font-family: 'Cairo'; outline: none; }
        
        .btn-row { display: flex; gap: 10px; margin-top: 10px; }
        .btn-action { flex: 1; padding: 15px; border: none; border-radius: 12px; color: white; font-weight: 700; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; text-decoration: none; }
        .bg-save { background: #27ae60; }
        .bg-print { background: #3498db; }
        .bg-add { background: #34495e; }
        .bg-back { background: #eee; color: #333; margin-bottom: 15px; width: fit-content; padding: 10px 20px; }
        
        .btn-camera { background: #2c3e50; margin-bottom: 10px; width: 100%; }
        .total-display { background: #fff5f7; padding: 15px; border-radius: 12px; text-align: center; font-size: 1.3rem; font-weight: 700; color: #e74c3c; margin: 15px 0; border: 1px dashed #e74c3c; }
        
        .report-box { display: flex; flex-direction: column; padding: 15px; background: #f8f9fa; border-radius: 15px; margin-bottom: 12px; border-right: 5px solid #ddd; cursor: pointer; transition: 0.2s; }
        .report-header { display: flex; justify-content: space-between; width: 100%; align-items: center; }
        .staff-item { border-bottom: 1px solid #eee; padding: 12px 0; display: flex; justify-content: space-between; align-items: center; } 

        .notif-item { padding: 15px; border-radius: 12px; margin-bottom: 10px; display: flex; align-items: center; gap: 15px; border-right: 5px solid #ddd; background: #fff; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .notif-warning { border-right-color: #e74c3c; color: #c0392b; }
        .notif-success { border-right-color: #27ae60; color: #1e8449; }
        .notif-info { border-right-color: #3498db; color: #2980b9; } 

        .summary-box { padding: 15px; border-radius: 15px; margin-bottom: 10px; color: white; }
        .summary-title { font-size: 0.9rem; opacity: 0.9; }
        .summary-val { font-size: 1.5rem; font-weight: 700; } 

        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 2000; justify-content: center; align-items: center; padding: 15px; }
        .modal-content { background: white; width: 100%; max-width: 500px; border-radius: 20px; padding: 20px; max-height: 80vh; overflow-y: auto; position: relative; }
        
        .inventory-item { border-bottom: 1px solid #eee; padding: 15px 0; display: flex; justify-content: space-between; align-items: center; }
        .edit-btn { background: #3498db; color: white; border: none; padding: 8px 12px; border-radius: 8px; cursor: pointer; } 

        .quick-items-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-top: 10px; }
        .quick-item { background: #fff; border: 1px solid var(--main-pink); padding: 10px; border-radius: 10px; text-align: center; font-size: 0.8rem; cursor: pointer; transition: 0.2s; }
        .quick-item:active { background: var(--main-pink); color: white; }
        .currency-switch { display: flex; align-items: center; gap: 10px; background: #fff3f6; padding: 10px; border-radius: 12px; margin-bottom: 10px; border: 1px solid #ffcad7; } 

        .cart-table { width: 100%; border-collapse: collapse; margin: 10px 0; font-size: 0.85rem; border-radius: 10px; overflow: hidden; }
        .cart-table th { background: #e67e22; color: white; padding: 10px; text-align: center; }
        .cart-table td { padding: 10px; border-bottom: 1px solid #eee; text-align: center; background: #fff; vertical-align: middle; }
        .cart-del-btn { color: #e74c3c; border: none; background: none; cursor: pointer; font-size: 1.1rem; }
        .qty-btn { border: 1px solid #ddd; background: #f8f9fa; border-radius: 5px; padding: 2px 8px; cursor: pointer; font-weight: bold; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .camera-container { width: 100%; border-radius: 15px; overflow: hidden; margin-bottom: 10px; display: none; border: 2px solid #2c3e50; }
        footer { text-align: center; color: #888; padding: 15px; font-size: 0.8rem; margin-top: 20px; } 

        @media print {
            body * { visibility: hidden; }
            #print-area, #print-area * { visibility: visible; }
            #print-area { 
                position: absolute; 
                left: 0; 
                top: 0; 
                width: 100%; 
                padding: 0; 
                margin: 0;
                background: white;
            }
            @page {
                size: 80mm auto; 
                margin: 0;
            }
            .modal, .btn-row, header, footer { display: none !important; }
            #store-header-print { display: block !important; border-bottom: 1px dashed #000; padding-bottom: 10px; margin-bottom: 10px; }
            #modal-items-list table { font-size: 12px !important; border-top: 1px dashed #000; }
            #modal-total { border-top: 1px dashed #000; padding-top: 10px; font-size: 16px !important; }
        }
    </style>
</head>
<body onload="initializeDashboard(); checkNotifications(); loadLinkSettings(); loadUpdateSettings(); renderStaff(); loadStoreInfo();"> 

<div id="sidebar" class="sidebar">
    <div class="sidebar-header">
        <h3 id="sidebar-store-name">تحميل...</h3>
        <p id="sidebar-user-login"><i class="fas fa-user-circle"></i> المدير العام</p>
    </div>
    <div class="sidebar-menu">
        <a href="#" onclick="showSection('home-screen'); toggleSidebar();"><i class="fas fa-home"></i> الرئيسية</a>
        <a href="#" onclick="showSection('expenses-screen'); toggleSidebar();"><i class="fas fa-money-bill-wave"></i> المصاريف</a>
        <a href="#" id="side-staff-link" onclick="showSection('staff-management-screen'); toggleSidebar();"><i class="fas fa-users-cog"></i> الموظفين</a>
        <a href="#" id="side-store-link" onclick="showSection('store-screen'); toggleSidebar();"><i class="fas fa-cog"></i> الإعدادات</a>
        <a href="#" onclick="logout();" style="color: #e74c3c;"><i class="fas fa-sign-out-alt"></i> تسجيل الخروج</a>
    </div>
</div>
<div id="overlay" class="overlay" onclick="toggleSidebar()"></div> 

<header id="main-header">
    <div class="header-left">
        <button class="icon-btn" onclick="toggleSidebar()"><i class="fas fa-bars"></i></button>
        <button class="icon-btn" onclick="showSection('home-screen')"><i class="fas fa-home"></i></button>
        <button class="icon-btn" onclick="showSection('notif-screen')">
            <i class="fas fa-bell"></i>
            <span class="notif-dot" id="global-notif-dot"></span>
        </button>
    </div>
    <div class="header-logo" onclick="showSection('home-screen')">كاشير برو <i class="fas fa-cash-register"></i></div>
</header> 

<div class="container"> 

    <div id="auth-screen" class="screen active">
        <div class="inner-card" style="margin-top: 50px; border-top: 5px solid #2c3e50;">
            <div style="text-align: center; margin-bottom: 20px;">
                <i class="fas fa-user-shield" style="font-size: 3rem; color: #2c3e50;"></i>
                <h2 id="auth-title">تسجيل الدخول</h2>
            </div>
            
            <div id="login-form">
                <input type="email" id="login-email" placeholder="البريد الإلكتروني">
                <input type="password" id="login-pass" placeholder="كلمة المرور">
                <button class="btn-action bg-save" style="background:#2c3e50; width: 100%;" onclick="handleLogin()">دخول</button>
                <p style="text-align: center; margin-top: 15px;">ليس لديك حساب؟ <a href="#" onclick="toggleAuthMode(true)" style="color:#3498db; text-decoration: none;">إنشاء حساب جديد</a></p>
            </div> 

            <div id="register-form" style="display: none;">
                <input type="text" id="reg-name" placeholder="الاسم الكامل أو اسم المتجر">
                <input type="email" id="reg-email" placeholder="البريد الإلكتروني">
                <input type="password" id="reg-pass" placeholder="كلمة المرور">
                <button class="btn-action bg-save" style="background:#27ae60; width: 100%;" onclick="handleRegister()">إنشاء الحساب</button>
                <p style="text-align: center; margin-top: 15px;">لديك حساب بالفعل؟ <a href="#" onclick="toggleAuthMode(false)" style="color:#3498db; text-decoration: none;">تسجيل دخول</a></p>
            </div>
        </div>
    </div> 

    <div id="home-screen" class="screen">
        <div style="display:flex; gap:10px; margin-bottom:15px;">
            <div style="flex:1; background:#fff; padding:10px; border-radius:15px; text-align:center; box-shadow:0 2px 5px rgba(0,0,0,0.05)">
                <small>مبيعات اليوم</small>
                <div id="quick-day-sales" style="font-weight:bold; color:var(--g-sales)">0</div>
            </div>
            <div style="flex:1; background:#fff; padding:10px; border-radius:15px; text-align:center; box-shadow:0 2px 5px rgba(0,0,0,0.05)">
                <small>حالة المخزن</small>
                <div id="quick-stock-alert" style="font-weight:bold; color:var(--g-purchases)">مستقر</div>
            </div>
        </div> 

        <div class="grid">
            <div class="card c-purchases" id="card-purchases" onclick="showSection('purchases-screen')"><i class="fas fa-truck-loading card-icon"></i><h3 class="card-title">المشتريات</h3><p class="card-sub">إضافة قوائم جديدة</p></div>
            <div class="card c-sales" id="card-sales" onclick="showSection('sales-screen')"><i class="fas fa-shopping-cart card-icon"></i><h3 class="card-title">المبيعات</h3><p class="card-sub">نقطة بيع سريعة (POS)</p></div>
            <div class="card c-reports" id="card-reports" onclick="showSection('reports-screen')"><i class="fas fa-chart-line card-icon"></i><h3 class="card-title">التقارير</h3><p class="card-sub">يومية، شهرية، أرباح</p></div>
            <div class="card c-inventory" id="card-inventory" onclick="showSection('inventory-screen')"><i class="fas fa-warehouse card-icon"></i><h3 class="card-title">المخزن</h3><p class="card-sub">جرد وتعديل الكميات</p></div>
            <div class="card c-accounts" id="card-accounts" onclick="showSection('accounts-screen')"><i class="fas fa-calculator card-icon"></i><h3 class="card-title">الحسابات</h3><p class="card-sub">مطابقة القاصة والأرباح</p></div>
            <div class="card c-subs" id="card-subscriptions" onclick="showSection('subscriptions-screen')"><i class="fas fa-id-card card-icon"></i><h3 class="card-title">الاشتراكات</h3><p class="card-sub">إدارة باقات الزبائن</p></div>
            <div class="card c-store" id="card-store" onclick="showSection('store-screen')"><i class="fas fa-store card-icon"></i><h3 class="card-title">المتجر</h3><p class="card-sub">المنصة والربط الإداري</p></div>
            <div class="card" style="background:#e74c3c" onclick="showSection('expenses-screen')"><i class="fas fa-money-bill-wave card-icon"></i><h3 class="card-title">المصاريف</h3><p class="card-sub">رواتب، إيجار، نثريات</p></div>
        </div>
    </div> 

    <div id="returns-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('sales-screen')">رجوع للمبيعات</button>
        <div class="inner-card" style="border-top: 5px solid #ff69b4;">
            <h3 style="color:#d11d5d"><i class="fas fa-undo"></i> استرجاع مادة (إعادة للمخزن)</h3>
            <p style="font-size:0.8rem; color:#666;">امسح باركود المادة أو أدخل الكود لإعادتها للمخزن.</p>
            
            <button class="btn-action btn-camera" style="background:#d11d5d" onclick="toggleCamera('reader-returns', 'ret-barcode')"><i class="fas fa-camera"></i> مسح باركود المادة للمرتجع</button>
            <div id="cam-box-reader-returns" class="camera-container"><div id="reader-returns"></div></div>
            
            <div style="display:flex; gap:10px">
                <input type="text" id="ret-barcode" placeholder="الباركود" oninput="searchProductForReturn(this.value)">
                <input type="text" id="ret-code" placeholder="كود المادة" oninput="searchProductForReturnByCode(this.value)">
            </div>
            <input type="text" id="ret-item-name" placeholder="اسم المادة" readonly style="background:#f9f9f9">
            <div style="display:flex; gap:10px">
                <input type="number" id="ret-qty" placeholder="الكمية المسترجعة" value="1">
                <input type="number" id="ret-price" placeholder="سعر البيع" readonly style="background:#f9f9f9">
            </div>
            
            <button class="btn-action" style="background:#d11d5d" onclick="processItemReturn()"><i class="fas fa-check-circle"></i> إتمام عملية الاسترجاع</button>
        </div>
        <div class="inner-card">
            <h3>سجل المرتجعات اليوم</h3>
            <div id="returns-log-container"></div>
        </div>
    </div>

    <div id="expenses-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:#e74c3c"><i class="fas fa-hand-holding-usd"></i> تسجيل مصروف جديد</h3>
            <input type="text" id="exp-title" placeholder="عنوان المصروف (مثلاً: إيجار المحل)">
            <input type="number" id="exp-amount" placeholder="المبلغ">
            <input type="date" id="exp-date">
            <button class="btn-action bg-save" style="background:#e74c3c" onclick="saveExpense()">حفظ المصروف</button>
        </div>
        <div class="inner-card">
            <h3>سجل المصاريف</h3>
            <div id="expenses-list-container"></div>
        </div>
    </div> 

    <div id="subscriptions-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:#6c5ce7"><i class="fas fa-user-plus"></i> إضافة مشترك جديد</h3>
            <input type="text" id="sub-customer" placeholder="اسم المشترك">
            <input type="text" id="sub-type" placeholder="نوع الاشتراك (مثلاً: شهري، باقة VIP)">
            <input type="number" id="sub-amount" placeholder="قيمة الاشتراك">
            <input type="date" id="sub-end-date" placeholder="تاريخ انتهاء الاشتراك">
            <button class="btn-action bg-save" style="background:#6c5ce7" onclick="addNewSubscription()">حفظ الاشتراك</button>
        </div>
        <div class="inner-card">
            <h3>قائمة المشتركين الحاليين</h3>
            <div id="sub-list-container"></div>
        </div>
    </div> 

    <div id="staff-management-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:var(--g-staff)"><i class="fas fa-user-plus"></i> إضافة موظف جديد</h3>
            <input type="text" id="staff-name" placeholder="اسم الموظف بالكامل">
            <input type="text" id="staff-user" placeholder="اسم حساب الدخول">
            <input type="password" id="staff-pass" placeholder="كلمة المرور">
            <label>صلاحيات الحساب:</label>
            <select id="staff-role">
                <option value="كامل الصلاحيات">مدير (كامل الصلاحيات)</option>
                <option value="مبيعات فقط">كاشير (مبيعات فقط)</option>
                <option value="مخزن فقط">أمين مخزن (مخزن فقط)</option>
            </select>
            <button class="btn-action bg-save" style="background:var(--g-staff)" onclick="addNewStaff()">إنشاء الحساب</button>
        </div>
        <div class="inner-card">
            <h3>قائمة حسابات الموظفين</h3>
            <div id="staff-list-container"></div>
        </div>
    </div> 

    <div id="notif-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:#2c3e50"><i class="fas fa-bell"></i> تنبيهات المخزن والمبيعات</h3>
            <div id="notif-list-container">
                <p style="text-align:center; color:#999;">لا توجد تنبيهات حالياً</p>
            </div>
        </div>
    </div> 

    <div id="accounts-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:#16a085"><i class="fas fa-chart-pie"></i> الحسابات والتحليل البياني</h3>
            
            <div style="width: 100%; height: 250px; margin-bottom: 20px;">
                <canvas id="dailyChart"></canvas>
            </div>

            <div class="summary-box" style="background:var(--g-sales); margin-bottom:10px;">
                <div class="summary-title">إجمالي مبيعات اليوم</div>
                <div class="summary-val" id="day-sales-total">0 د.ع</div>
            </div>
            <div class="summary-box" style="background:var(--g-reports); margin-bottom:10px;">
                <div class="summary-title">صافي أرباح اليوم (تقديري)</div>
                <div class="summary-val" id="day-profit-total">0 د.ع</div>
            </div>
            <div class="summary-box" style="background:var(--g-purchases);">
                <div class="summary-title">مصاريف اليوم</div>
                <div class="summary-val" id="day-expenses-total">0 د.ع</div>
            </div>
        </div>
    </div> 

    <div id="purchases-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:#c0392b"><i class="fas fa-file-invoice"></i> فاتورة مشتريات</h3>
            <input type="text" id="p-customer" placeholder="اسم المجهز">
            <input type="text" id="p-invoice-num" placeholder="رقم الفاتورة">
            <hr>
            <button class="btn-action btn-camera" onclick="toggleCamera('reader-purchases', 'p-barcode')"><i class="fas fa-camera"></i> فتح الكاميرا لقراءة الباركود</button>
            <div id="cam-box-reader-purchases" class="camera-container"><div id="reader-purchases"></div></div>
            <div style="display:flex; gap:10px">
                <input type="text" id="p-barcode" placeholder="الباركود">
                <input type="text" id="p-code" placeholder="كود المادة">
            </div>
            <input type="text" id="p-item-name" placeholder="اسم المادة">
            <div style="display:flex; gap:10px"><input type="number" id="p-qty" placeholder="العدد"><input type="number" id="p-cost" placeholder="سعر التكلفة"></div>
            <input type="number" id="p-price" placeholder="سعر البيع">
            <button class="btn-action bg-add" onclick="addItemToPurchase()"><i class="fas fa-plus"></i> إضافة مادة أخرى</button>
            <div class="total-display" id="purchase-total-text">المبلغ الكلي: 0 د.ع</div>
            <div class="btn-row"><button class="btn-action bg-save" onclick="savePurchase()">حفظ الفاتورة</button></div>
        </div>
    </div> 

    <div id="sales-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        
        <div class="currency-switch">
            <i class="fas fa-dollar-sign" style="color:#27ae60"></i>
            <small>صرف اليوم:</small>
            <input type="number" id="usd-rate" value="1500" style="width:80px; margin:0; padding:5px; border-radius:5px;" oninput="updateSalesTotal()">
            <small>د.ع</small>
            <button onclick="showSection('returns-screen')" style="margin-right:auto; background:#d11d5d; color:white; border:none; padding:5px 10px; border-radius:8px; font-size:0.7rem; cursor:pointer;"><i class="fas fa-undo"></i> استرجاع بضاعة</button>
        </div> 

        <div class="inner-card">
            <h3 style="color:#e67e22"><i class="fas fa-cash-register"></i> نقطة بيع</h3>
            
            <div id="quick-items-area">
                <small style="color:#888">مواد سريعة:</small>
                <div class="quick-items-grid" id="quick-items-container"></div>
            </div>
            <hr> 

            <input type="text" id="s-customer" placeholder="اسم الزبون (اختياري)">
            <button class="btn-action btn-camera" style="background:#e67e22" onclick="toggleCamera('reader-sales', 's-barcode')"><i class="fas fa-camera"></i> مسح باركود المادة</button>
            <div id="cam-box-reader-sales" class="camera-container"><div id="reader-sales"></div></div>
            <div style="display:flex; gap:10px">
                <input type="text" id="s-barcode" placeholder="الباركود" oninput="searchProductByBarcode(this.value)">
                <input type="text" id="s-code" placeholder="كود المادة" oninput="searchProductByCode(this.value)">
            </div>
            <input type="text" id="s-item-name" placeholder="اسم المادة">
            <div style="display:flex; gap:10px">
                <input type="number" id="s-price" placeholder="السعر">
                <input type="number" id="s-qty" value="1">
            </div>
            
            <div style="display:flex; gap:10px; align-items: center; background: #fff3f6; padding: 10px; border-radius: 12px; border: 1px solid #ffcad7; margin-bottom: 8px;">
                <select id="s-discount-type" style="width: 100px; margin:0; padding:5px; font-size:0.8rem;">
                    <option value="amount">خصم مبلغ</option>
                    <option value="percent">خصم نسبة %</option>
                </select>
                <input type="number" id="s-discount-val" placeholder="0" value="0" style="margin:0; border:none; background:transparent; color:#e74c3c; font-weight:bold;">
            </div>

            <button class="btn-action bg-add" style="background:#e67e22" onclick="addItemToSale()"><i class="fas fa-plus"></i> إضافة للسلة</button>
            
            <div id="current-cart-display" style="margin-top: 15px; display: none;">
                <h4 style="margin: 5px 0;"><i class="fas fa-shopping-basket"></i> السلة:</h4>
                <div class="table-responsive">
                    <table class="cart-table">
                        <thead>
                            <tr>
                                <th>المادة</th>
                                <th style="width: 100px;">الكمية</th>
                                <th>الإجمالي</th>
                                <th>حذف</th>
                            </tr>
                        </thead>
                        <tbody id="cart-items-body"></tbody>
                    </table>
                </div>
            </div>

            <div class="total-display" id="sales-total-text" style="color:#e67e22; border-color:#e67e22">المبلغ الكلي: 0 د.ع</div>
            <div id="usd-total-display" style="text-align:center; font-size:0.9rem; color:#888; margin-bottom:10px">ما يعادل: 0.00 $</div>
            <button class="btn-action bg-save" style="background:#e67e22" onclick="saveSale()">حفظ وإنهاء البيع</button>
        </div>
    </div> 

    <div id="reports-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="btn-row" style="margin-bottom:20px; flex-wrap: wrap;">
            <button class="btn-action" style="background:var(--g-purchases)" onclick="renderReports('purchases')">فواتير المشتريات</button>
            <button class="btn-action" style="background:var(--g-sales)" onclick="renderReports('sales')">فواتير المبيعات</button>
            <button class="btn-action" style="background:var(--g-reports)" onclick="renderMonthlyReport()">تقرير شهري</button>
        </div>
        <div class="inner-card">
            <h3 id="report-title" style="color:var(--text-dark)">اختر نوع التقرير</h3>
            <div id="reports-list-container"></div>
        </div>
    </div> 

    <div id="inventory-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:var(--g-inventory)"><i class="fas fa-boxes"></i> جرد وتعديل المخزن</h3>
            <div id="inventory-container"></div>
        </div>
    </div> 

    <div id="store-screen" class="screen">
        <button class="btn-action bg-back" onclick="showSection('home-screen')">رجوع</button>
        <div class="inner-card">
            <h3 style="color:#2980b9"><i class="fas fa-info-circle"></i> بيانات المحل (للفاتورة)</h3>
            <label>اسم المحل:</label>
            <input type="text" id="store-name-info" placeholder="مثال: كوزمتك السلطانة">
            <label>العنوان:</label>
            <input type="text" id="store-address-info" placeholder="مثال: ميسان - الشارع العام">
            <label>رقم الهاتف:</label>
            <input type="text" id="store-phone-info" placeholder="مثال: 07800000000">
            <hr>
            <h3 style="color:#2980b9"><i class="fas fa-link"></i> إعدادات الربط والتبليغات</h3>
            <label>توكن بوت التليجرام (Telegram Token):</label>
            <input type="text" id="tg-token" placeholder="أدخل التوكن هنا">
            <label>معرف الدردشة (Chat ID):</label>
            <input type="text" id="tg-chatid" placeholder="أدخل معرف المدير">
            <hr>
            <label>رابط قناة الواتساب للموظفين:</label>
            <input type="text" id="wa-channel-link" placeholder="ضع رابط القناة هنا">
            
            <hr>
            <h3 style="color:#27ae60"><i class="fas fa-database"></i> النسخ الاحتياطي والبيانات</h3>
            <p style="font-size: 0.8rem; color: #666;">قم بتحميل نسخة من بياناتك لحمايتها من الضياع.</p>
            <div class="btn-row">
                <button class="btn-action" style="background:#27ae60" onclick="exportBackup()"><i class="fas fa-download"></i> تصدير نسخة احتياطية</button>
            </div>
            <div class="btn-row">
                <input type="file" id="importFile" style="display:none" onchange="importBackup(event)">
                <button class="btn-action" style="background:#f39c12" onclick="document.getElementById('importFile').click()"><i class="fas fa-upload"></i> استيراد نسخة احتياطية</button>
            </div>

            <hr>
            <label>كود تفعيل البرنامج السنوي:</label>
            <input type="text" id="license-key-input" placeholder="ضع كود التفعيل هنا لتجديد الاشتراك">
            <button class="btn-action bg-save" onclick="activateLicense()">تفعيل الاشتراك</button>

            <div class="btn-row" style="margin-top: 15px;">
                <button class="btn-action bg-save" onclick="saveStoreInfo(); saveLinkSettings();">حفظ الإعدادات</button>
            </div>
        </div>
    </div>
</div> 

<div id="detail-modal" class="modal">
    <div class="modal-content">
        <div id="print-area">
            <div id="store-header-print" style="text-align:center; margin-bottom:15px; border-bottom:1px dashed #ccc; padding-bottom:10px; display:none;">
                <h2 id="print-store-name" style="margin:0; color:#2c3e50;"></h2>
                <p id="print-store-address" style="margin:5px 0; font-size:0.9rem;"></p>
                <p id="print-store-phone" style="margin:5px 0; font-size:0.9rem;"></p>
            </div>
            <h2 id="modal-title" style="text-align:center; border-bottom: 2px solid #333; padding-bottom:10px;">تفاصيل الفاتورة</h2>
            <div id="modal-info" style="margin-bottom:15px;"></div>
            <div id="modal-items-list"></div>
            <div id="modal-total" style="font-size:1.4rem; font-weight:bold; text-align:center; margin-top:20px; color:#e74c3c;"></div>
        </div>
        <div class="btn-row" style="margin-top:20px; flex-wrap: wrap;">
            <button class="btn-action bg-print" onclick="window.print()"><i class="fas fa-print"></i> طباعة حرارية</button>
            <button class="btn-action" style="background:#e67e22" onclick="shareToWhatsApp()"><i class="fab fa-whatsapp"></i> واتساب</button>
            <button class="btn-action" style="background:#2c3e50" onclick="exportToPDF()"><i class="fas fa-file-pdf"></i> PDF</button>
            <button class="btn-action bg-back" onclick="closeModal()">إغلاق</button>
        </div>
    </div>
</div> 

<div id="edit-modal" class="modal">
    <div class="modal-content">
        <h3 style="text-align:center;">تعديل مادة: <span id="edit-item-name"></span></h3>
        <label>العدد المتوفر حالياً:</label>
        <input type="number" id="edit-item-qty">
        <label>سعر البيع الحالي:</label>
        <input type="number" id="edit-item-price">
        <div class="btn-row">
            <button class="btn-action bg-save" onclick="saveInventoryEdit()">حفظ التعديل</button>
            <button class="btn-action bg-back" onclick="closeEditModal()">إلغاء</button>
        </div>
    </div>
</div> 

<footer><p>© 2026 كاشير برو - النسخة v1.7</p></footer> 

<script>
    let inventory = JSON.parse(localStorage.getItem('pos_inventory')) || [];
    let purchaseInvoices = JSON.parse(localStorage.getItem('pos_p_invoices')) || [];
    let salesInvoices = JSON.parse(localStorage.getItem('pos_s_invoices')) || [];
    let staffMembers = JSON.parse(localStorage.getItem('pos_staff')) || [];
    let subscriptions = JSON.parse(localStorage.getItem('pos_subscriptions')) || [];
    let expenses = JSON.parse(localStorage.getItem('pos_expenses')) || [];
    let returnsLog = JSON.parse(localStorage.getItem('pos_returns')) || []; 
    
    let tempPurchases = [];
    let tempSales = [];
    let html5QrCode = null; 
    let currentActiveInvoice = null; 
    let currentEditIndex = null;
    let myChart = null; 

    let users = JSON.parse(localStorage.getItem('pos_users')) || [];
    let currentUser = JSON.parse(sessionStorage.getItem('pos_logged_in_user')) || null; 

    // --- وظائف تفعيل الاشتراك (الاشتراك السنوي) ---
    function activateLicense() {
        const key = document.getElementById('license-key-input').value;
        try {
            const expiryTime = parseInt(atob(key));
            if (isNaN(expiryTime)) throw new Error();
            
            if (currentUser) {
                currentUser.expiryDate = expiryTime;
                users = users.map(u => u.email === currentUser.email ? currentUser : u);
                localStorage.setItem('pos_users', JSON.stringify(users));
                sessionStorage.setItem('pos_logged_in_user', JSON.stringify(currentUser));
                alert("تم تفعيل الاشتراك السنوي بنجاح! ✅");
                location.reload();
            }
        } catch(e) {
            alert("كود التفعيل غير صحيح ❌");
        }
    }

    // --- نظام التحديث أونلاين للمدير ---
    const APP_VERSION = "1.7";
    const UPDATE_URL = "https://raw.githubusercontent.com/YOUR_USER/YOUR_REPO/main/version.json"; 

    function checkForUpdates() {
        fetch(UPDATE_URL)
            .then(res => res.json())
            .then(data => {
                if (data.version > APP_VERSION) {
                    showUpdateAlert(data.version, data.url, data.note);
                }
            })
            .catch(e => console.log("Update check offline"));
    }

    function showUpdateAlert(ver, url, note) {
        const div = document.createElement('div');
        div.style = "position:fixed; bottom:20px; left:20px; right:20px; background:#2c3e50; color:white; padding:20px; border-radius:15px; z-index:9999; box-shadow:0 10px 25px rgba(0,0,0,0.3); border-top:4px solid #ff85a2; animation: fadeIn 0.5s ease;";
        div.innerHTML = `<div style="display:flex; justify-content:space-between; align-items:center;"><div><strong style="color:#ffeb3b;">تحديث جديد متوفر (${ver})</strong><p style="margin:5px 0 0; font-size:0.7rem; opacity:0.8;">${note}</p></div><a href="${url}" target="_blank" style="background:#27ae60; color:white; text-decoration:none; padding:8px 15px; border-radius:8px; font-weight:bold; font-size:0.8rem;">تحديث</a></div>`;
        document.body.appendChild(div);
    }

    function searchProductForReturn(barcode) {
        if(!barcode) return;
        const item = inventory.find(i => i.barcode === barcode);
        if (item) {
            document.getElementById('ret-item-name').value = item.name;
            document.getElementById('ret-price').value = item.price;
            document.getElementById('ret-qty').focus();
        }
    }

    function searchProductForReturnByCode(code) {
        if(!code) return;
        const item = inventory.find(i => i.code === code);
        if (item) {
            document.getElementById('ret-item-name').value = item.name;
            document.getElementById('ret-price').value = item.price;
            document.getElementById('ret-qty').focus();
        }
    }

    function processItemReturn() {
        const barcode = document.getElementById('ret-barcode').value;
        const code = document.getElementById('ret-code').value;
        const qty = parseFloat(document.getElementById('ret-qty').value);
        const name = document.getElementById('ret-item-name').value;
        const price = parseFloat(document.getElementById('ret-price').value);

        if(!name || isNaN(qty)) return alert("يرجى اختيار مادة صالحة");

        const stockItem = inventory.find(i => i.name === name);
        if(stockItem) {
            stockItem.qty += qty;
            localStorage.setItem('pos_inventory', JSON.stringify(inventory));
        }

        const returnEntry = { id: Date.now(), name: name, qty: qty, price: price, total: qty * price, date: new Date().toLocaleString('ar-EG') };
        returnsLog.push(returnEntry);
        localStorage.setItem('pos_returns', JSON.stringify(returnsLog));

        const returnInvoice = { id: "RET-" + Date.now(), customer: "مرتجع بضاعة", date: new Date().toLocaleString('ar-EG'), items: [{ name: "مرتجع: " + name, qty: qty, price: price, discount: 0 }], total: -(qty * price) };
        salesInvoices.push(returnInvoice);
        localStorage.setItem('pos_s_invoices', JSON.stringify(salesInvoices));

        alert("تم استرجاع المادة وإعادتها للمخزن بنجاح ✅");
        document.getElementById('ret-barcode').value = ""; document.getElementById('ret-code').value = ""; document.getElementById('ret-item-name').value = ""; document.getElementById('ret-qty').value = "1"; document.getElementById('ret-price').value = "";
        renderReturnsLog(); updateQuickStats();
    }

    function renderReturnsLog() {
        const container = document.getElementById('returns-log-container');
        if(!container) return;
        container.innerHTML = "";
        returnsLog.slice().reverse().forEach(ret => {
            container.innerHTML += `<div class="staff-item"><span>${ret.name} (${ret.qty})</span><strong style="color:#d11d5d">-${ret.total.toLocaleString()} د.ع</strong></div>`;
        });
    }

    function addItemToPurchase() {
        const barcode = document.getElementById('p-barcode').value, code = document.getElementById('p-code').value, name = document.getElementById('p-item-name').value, qty = parseFloat(document.getElementById('p-qty').value), cost = parseFloat(document.getElementById('p-cost').value), price = parseFloat(document.getElementById('p-price').value);
        if (!name || isNaN(qty) || isNaN(cost) || isNaN(price)) return alert("يرجى ملء بيانات المادة كاملة");
        tempPurchases.push({ barcode, code, name, qty, cost, price });
        const total = tempPurchases.reduce((sum, item) => sum + (item.qty * item.cost), 0);
        document.getElementById('purchase-total-text').innerText = `المبلغ الكلي: ${total.toLocaleString()} د.ع`;
        document.getElementById('p-barcode').value = ''; document.getElementById('p-code').value = ''; document.getElementById('p-item-name').value = ''; document.getElementById('p-qty').value = ''; document.getElementById('p-cost').value = ''; document.getElementById('p-price').value = '';
        alert("تمت إضافة المادة");
    }

    function savePurchase() {
        if (tempPurchases.length === 0) return alert("الفاتورة فارغة");
        const invoiceTotal = tempPurchases.reduce((sum, item) => sum + (item.qty * item.cost), 0);
        const invoice = { id: Date.now(), customer: document.getElementById('p-customer').value || "مجهز غير مسمى", invoiceNum: document.getElementById('p-invoice-num').value, date: new Date().toLocaleString('ar-EG'), items: [...tempPurchases], total: invoiceTotal };
        purchaseInvoices.push(invoice);
        localStorage.setItem('pos_p_invoices', JSON.stringify(purchaseInvoices));
        tempPurchases.forEach(newItem => {
            let existingItem = inventory.find(i => (i.barcode && newItem.barcode && i.barcode === newItem.barcode) || (i.code && newItem.code && i.code === newItem.code) || i.name === newItem.name);
            if (existingItem) { existingItem.qty += newItem.qty; existingItem.cost = newItem.cost; existingItem.price = newItem.price; if(newItem.code) existingItem.code = newItem.code; } 
            else { inventory.push({ barcode: newItem.barcode, code: newItem.code, name: newItem.name, qty: newItem.qty, cost: newItem.cost, price: newItem.price }); }
        });
        localStorage.setItem('pos_inventory', JSON.stringify(inventory));
        alert("تم حفظ الفاتورة"); tempPurchases = []; document.getElementById('purchase-total-text').innerText = "المبلغ الكلي: 0 د.ع"; showSection('home-screen'); updateQuickStats();
    }

    function searchProductByBarcode(barcode) { if(!barcode) return; const item = inventory.find(i => i.barcode === barcode); if (item) { document.getElementById('s-item-name').value = item.name; document.getElementById('s-price').value = item.price; document.getElementById('s-qty').focus(); } }
    function searchProductByCode(code) { if(!code) return; const item = inventory.find(i => i.code === code); if (item) { document.getElementById('s-item-name').value = item.name; document.getElementById('s-price').value = item.price; document.getElementById('s-qty').focus(); } }

    function showInvoiceDetails(inv, type) {
        currentActiveInvoice = inv;
        const storeInfo = JSON.parse(localStorage.getItem('pos_store_info')) || {};
        document.getElementById('store-header-print').style.display = 'block'; 
        document.getElementById('print-store-name').innerText = storeInfo.name || "كاشير برو";
        document.getElementById('print-store-address').innerText = "العنوان: " + (storeInfo.address || "غير محدد");
        document.getElementById('print-store-phone').innerText = "هاتف: " + (storeInfo.phone || "غير محدد");
        document.getElementById('modal-title').innerText = (type === 'sales') ? "فاتورة مبيعات" : "فاتورة مشتريات";
        document.getElementById('modal-info').innerHTML = `التاريخ: ${inv.date}<br>الجهة: ${inv.customer || 'غير محدد'}`;
        let itemsHtml = "<table style='width:100%; border-collapse:collapse; margin-top:10px;'><thead><tr style='background:#f4f4f4;'><th>المادة</th><th>الكمية</th><th>السعر</th></tr></thead><tbody>";
        inv.items.forEach(item => { itemsHtml += `<tr><td>${item.name}</td><td style="text-align:center;">${item.qty}</td><td style="text-align:center;">${(item.price || item.cost).toLocaleString()}</td></tr>`; });
        document.getElementById('modal-items-list').innerHTML = itemsHtml + "</tbody></table>";
        document.getElementById('modal-total').innerText = `المجموع: ${inv.total.toLocaleString()} د.ع`;
        document.getElementById('detail-modal').style.display = 'flex';
    }

    function renderInventory() {
        const container = document.getElementById('inventory-container');
        container.innerHTML = "";
        if(inventory.length === 0) { container.innerHTML = "<p style='text-align:center; color:#999;'>المخزن فارغ.</p>"; return; }
        inventory.forEach((item, index) => {
            container.innerHTML += `<div class="inventory-item"><div><strong>${item.name}</strong><br><small>الكمية: ${item.qty} | السعر: ${item.price.toLocaleString()} د.ع</small><br><small style="color:blue">كود: ${item.code || '---'}</small></div><div style="display:flex; gap:5px;"><button class="edit-btn" onclick="openEditItem(${index})"><i class="fas fa-edit"></i></button><button class="edit-btn" style="background:#e74c3c" onclick="deleteItemFromInventory(${index})"><i class="fas fa-trash"></i></button></div></div>`;
        });
    }

    function updateAccountsStats() {
        const todayStr = new Date().toLocaleDateString('ar-EG');
        const todaySalesInvoices = salesInvoices.filter(inv => inv.date.includes(todayStr));
        const totalSales = todaySalesInvoices.reduce((sum, inv) => sum + inv.total, 0);
        let totalProfit = 0;
        todaySalesInvoices.forEach(inv => {
            inv.items.forEach(soldItem => {
                const cleanName = soldItem.name.replace("مرتجع: ", "");
                const stockItem = inventory.find(i => i.name === cleanName);
                const cost = stockItem ? stockItem.cost : 0;
                let profit = (soldItem.price - cost) * soldItem.qty - (soldItem.discount || 0);
                if(inv.total < 0) totalProfit -= Math.abs(profit); else totalProfit += profit;
            });
        });
        const totalExpenses = expenses.filter(exp => new Date(exp.date).toLocaleDateString('ar-EG') === todayStr).reduce((sum, exp) => sum + parseFloat(exp.amount || 0), 0);
        document.getElementById('day-sales-total').innerText = totalSales.toLocaleString() + " د.ع";
        document.getElementById('day-profit-total').innerText = totalProfit.toLocaleString() + " د.ع";
        document.getElementById('day-expenses-total').innerText = totalExpenses.toLocaleString() + " د.ع";
        renderDailyChart(totalSales, totalProfit, totalExpenses);
    }

    function renderDailyChart(sales, profit, expenses) {
        const ctx = document.getElementById('dailyChart').getContext('2d');
        if (myChart) { myChart.destroy(); }
        myChart = new Chart(ctx, {
            type: 'bar',
            data: { labels: ['المبيعات', 'الأرباح', 'المصاريف'], datasets: [{ label: 'إحصاءات اليوم', data: [sales, profit, expenses], backgroundColor: ['#f39c12', '#27ae60', '#e74c3c'], borderRadius: 10 }] },
            options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true, grid: { display: false }, ticks: { font: { family: 'Cairo' } } }, x: { grid: { display: false }, ticks: { font: { family: 'Cairo' } } } } }
        });
    }

    function saveExpense() {
        const title = document.getElementById('exp-title').value, amount = document.getElementById('exp-amount').value, date = document.getElementById('exp-date').value;
        if (!title || !amount || !date) return alert("ملء الحقول");
        expenses.push({ title, amount: parseFloat(amount), date });
        localStorage.setItem('pos_expenses', JSON.stringify(expenses)); renderExpenses();
    }

    function renderExpenses() {
        const container = document.getElementById('expenses-list-container');
        container.innerHTML = "";
        expenses.slice().reverse().forEach(exp => { container.innerHTML += `<div class="staff-item"><span>${exp.title}</span><strong>${parseFloat(exp.amount).toLocaleString()}</strong></div>`; });
    }

    function showSection(id) { 
        stopCamera(); document.querySelectorAll('.screen').forEach(s => s.classList.remove('active')); 
        document.getElementById(id).classList.add('active'); 
        if(id === 'inventory-screen') renderInventory(); 
        if(id === 'sales-screen') { renderQuickItems(); renderCartTable(); }
        if(id === 'accounts-screen') updateAccountsStats();
        if(id === 'expenses-screen') renderExpenses();
        if(id === 'returns-screen') renderReturnsLog();
        window.scrollTo(0,0); 
    }

    function updateSalesTotal() {
        const total = tempSales.reduce((sum, item) => sum + (item.qty * item.price - item.discount), 0);
        document.getElementById('sales-total-text').innerText = `المبلغ الكلي: ${total.toLocaleString()} د.ع`;
        const rate = parseFloat(document.getElementById('usd-rate').value) || 1;
        document.getElementById('usd-total-display').innerText = `ما يعادل: ${(total / rate).toFixed(2)} $`;
    }

    function addItemToSale() {
        const name = document.getElementById('s-item-name').value, qty = parseFloat(document.getElementById('s-qty').value), price = parseFloat(document.getElementById('s-price').value);
        if (!name || isNaN(qty) || isNaN(price)) return alert("اختر مادة");
        const stock = inventory.find(i => i.name === name);
        if(!stock || stock.qty < qty) return alert("الكمية غير كافية");
        let disc = (document.getElementById('s-discount-type').value === 'amount') ? parseFloat(document.getElementById('s-discount-val').value) || 0 : (price * qty) * ((parseFloat(document.getElementById('s-discount-val').value) || 0) / 100);
        tempSales.push({ name, qty, price, discount: disc });
        renderCartTable(); updateSalesTotal();
    }

    function renderCartTable() {
        const body = document.getElementById('cart-items-body');
        document.getElementById('current-cart-display').style.display = tempSales.length ? 'block' : 'none';
        body.innerHTML = "";
        tempSales.forEach((item, i) => { body.innerHTML += `<tr><td>${item.name}</td><td>${item.qty}</td><td>${(item.qty*item.price-item.discount).toLocaleString()}</td><td><button onclick="tempSales.splice(${i},1);renderCartTable();updateSalesTotal();">X</button></td></tr>`; });
    }

    function saveSale() {
        if (!tempSales.length) return alert("السلة فارغة");
        const total = tempSales.reduce((sum, item) => sum + (item.qty * item.price - item.discount), 0);
        const invoice = { id: Date.now(), customer: document.getElementById('s-customer').value || "زبون نقدي", date: new Date().toLocaleString('ar-EG'), items: [...tempSales], total };
        salesInvoices.push(invoice);
        tempSales.forEach(s => { let st = inventory.find(i => i.name === s.name); if(st) st.qty -= s.qty; });
        localStorage.setItem('pos_inventory', JSON.stringify(inventory)); localStorage.setItem('pos_s_invoices', JSON.stringify(salesInvoices));
        tempSales = []; alert("تم البيع"); showSection('home-screen'); updateQuickStats();
    }

    function renderQuickItems() {
        const container = document.getElementById('quick-items-container');
        container.innerHTML = "";
        inventory.slice(0, 6).forEach(item => {
            const div = document.createElement('div'); div.className = 'quick-item'; div.innerText = item.name;
            div.onclick = () => { document.getElementById('s-barcode').value = item.barcode || ""; document.getElementById('s-code').value = item.code || ""; document.getElementById('s-item-name').value = item.name; document.getElementById('s-price').value = item.price; };
            container.appendChild(div);
        });
    }

    function toggleCamera(readerId, inputId) {
        const box = document.getElementById('cam-box-' + readerId);
        if (box.style.display === 'block') { stopCamera(); box.style.display = 'none'; }
        else {
            box.style.display = 'block'; html5QrCode = new Html5Qrcode(readerId);
            html5QrCode.start({ facingMode: "environment" }, { fps: 10, qrbox: 250 }, (text) => {
                document.getElementById(inputId).value = text;
                if(inputId === 's-barcode') searchProductByBarcode(text);
                if(inputId === 'ret-barcode') searchProductForReturn(text);
                stopCamera(); box.style.display = 'none';
            }).catch(() => alert("كاميرا غير متاحة"));
        }
    }

    function stopCamera() { if (html5QrCode) { html5QrCode.stop().then(() => html5QrCode = null).catch(() => {}); } }

    function openEditItem(index) {
        currentEditIndex = index; const item = inventory[index];
        document.getElementById('edit-item-name').innerText = item.name; document.getElementById('edit-item-qty').value = item.qty; document.getElementById('edit-item-price').value = item.price;
        document.getElementById('edit-modal').style.display = 'flex';
    }

    function saveInventoryEdit() {
        inventory[currentEditIndex].qty = parseFloat(document.getElementById('edit-item-qty').value);
        inventory[currentEditIndex].price = parseFloat(document.getElementById('edit-item-price').value);
        localStorage.setItem('pos_inventory', JSON.stringify(inventory)); renderInventory(); closeModal();
    }

    function deleteItemFromInventory(i) { if(confirm("حذف؟")) { inventory.splice(i,1); localStorage.setItem('pos_inventory', JSON.stringify(inventory)); renderInventory(); } }

    // --- تعديل وظيفة الدخول للتحقق من تاريخ التفعيل السنوي ---
    function handleLogin() {
        const email = document.getElementById('login-email').value, pass = document.getElementById('login-pass').value;
        const user = users.find(u => u.email === email && u.pass === pass);
        if (user) {
            // التحقق من الاشتراك السنوي (إذا كان التاريخ موجوداً)
            const now = new Date().getTime();
            if (user.expiryDate && now > user.expiryDate) {
                alert("انتهى اشتراكك السنوي. يرجى التواصل مع الإدارة للتفعيل.");
                return;
            }
            sessionStorage.setItem('pos_logged_in_user', JSON.stringify(user)); 
            location.reload(); 
        } else alert("خطأ في البيانات");
    }

    function handleRegister() {
        const name = document.getElementById('reg-name').value, email = document.getElementById('reg-email').value, pass = document.getElementById('reg-pass').value;
        users.push({ name, email, pass }); localStorage.setItem('pos_users', JSON.stringify(users)); toggleAuthMode(false);
    }

    function logout() { sessionStorage.removeItem('pos_logged_in_user'); location.reload(); }
    function toggleAuthMode(isReg) { document.getElementById('login-form').style.display=isReg?'none':'block'; document.getElementById('register-form').style.display=isReg?'block':'none'; }
    function toggleSidebar() { document.getElementById('sidebar').classList.toggle('active'); const o = document.getElementById('overlay'); o.style.display = o.style.display==='block'?'none':'block'; }
    
    function initializeDashboard() {
        if (currentUser) { 
            document.getElementById('main-header').style.display = 'flex'; 
            showSection('home-screen'); 
            document.getElementById('sidebar-store-name').innerText = currentUser.name;
            updateQuickStats();
            setTimeout(checkForUpdates, 4000); 
        }
    }

    function updateQuickStats() {
        const todayAr = new Date().toLocaleDateString('ar-EG');
        const sales = salesInvoices.filter(inv => inv.date.includes(todayAr)).reduce((s, i) => s + i.total, 0);
        document.getElementById('quick-day-sales').innerText = sales.toLocaleString() + " د.ع";
    }

    function closeModal() { document.getElementById('detail-modal').style.display = 'none'; document.getElementById('edit-modal').style.display = 'none'; }
    function closeEditModal() { closeModal(); }
    
    function loadStoreInfo() {
        const info = JSON.parse(localStorage.getItem('pos_store_info'));
        if(info) { document.getElementById('store-name-info').value = info.name; document.getElementById('store-address-info').value = info.address; document.getElementById('store-phone-info').value = info.phone; }
    }
    
    function saveStoreInfo() {
        localStorage.setItem('pos_store_info', JSON.stringify({ name: document.getElementById('store-name-info').value, address: document.getElementById('store-address-info').value, phone: document.getElementById('store-phone-info').value }));
        alert("تم الحفظ");
    }

    function renderReports(type) {
        const container = document.getElementById('reports-list-container');
        let data = (type === 'sales') ? salesInvoices : purchaseInvoices; container.innerHTML = "";
        data.slice().reverse().forEach(inv => {
            const div = document.createElement('div'); div.className = 'report-box';
            div.innerHTML = `<strong>${inv.date}</strong><br>الجهة: ${inv.customer} | المجموع: ${inv.total.toLocaleString()}`;
            div.onclick = () => showInvoiceDetails(inv, type); container.appendChild(div);
        });
    }

    function renderStaff() {
        const container = document.getElementById('staff-list-container');
        if(!container) return; container.innerHTML = "";
        staffMembers.forEach((staff, index) => { container.innerHTML += `<div class="staff-item"><div><strong>${staff.name}</strong><br><small>${staff.role}</small></div><button class="edit-btn" style="background:#e74c3c" onclick="deleteStaff(${index})">حذف</button></div>`; });
    }

    function addNewStaff() {
        const name = document.getElementById('staff-name').value, user = document.getElementById('staff-user').value, pass = document.getElementById('staff-pass').value, role = document.getElementById('staff-role').value;
        if (!name || !user || !pass) return alert("أكمل البيانات");
        staffMembers.push({ name, user, pass, role }); localStorage.setItem('pos_staff', JSON.stringify(staffMembers)); renderStaff();
    }

    function deleteStaff(index) { staffMembers.splice(index, 1); localStorage.setItem('pos_staff', JSON.stringify(staffMembers)); renderStaff(); }

    function exportBackup() {
        const backupData = { inventory, purchaseInvoices, salesInvoices, staffMembers, subscriptions, expenses, returnsLog, storeInfo: JSON.parse(localStorage.getItem('pos_store_info')) || {}, users: JSON.parse(localStorage.getItem('pos_users')) || [] };
        const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(backupData));
        const dl = document.createElement('a'); dl.setAttribute("href", dataStr); dl.setAttribute("download", "cashier_backup_" + new Date().toLocaleDateString() + ".json"); dl.click();
    }

    function importBackup(event) {
        const file = event.target.files[0]; if (!file) return;
        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const data = JSON.parse(e.target.result);
                if (confirm("استبدال البيانات؟")) {
                    localStorage.setItem('pos_inventory', JSON.stringify(data.inventory || []));
                    localStorage.setItem('pos_s_invoices', JSON.stringify(data.salesInvoices || []));
                    localStorage.setItem('pos_returns', JSON.stringify(data.returnsLog || []));
                    alert("تمت الاستعادة"); location.reload();
                }
            } catch (err) { alert("خطأ في الملف"); }
        };
        reader.readAsText(file);
    }
    function checkNotifications() {} function loadLinkSettings() {} function loadUpdateSettings() {} function saveLinkSettings() {} function addNewSubscription() {} function renderMonthlyReport() {} function shareToWhatsApp() {} function exportToPDF() {}
</script>
</body>
</html>
