
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>كاشير برو - الواجهة السحابية</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap');
        
        :root {
            --bg-light: #f4f6f9;
            --text-dark: #2c3e50;
            --white: #ffffff;
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

        /* شاشة تسجيل الدخول */
        #login-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #2c3e50; z-index: 5000; display: flex; justify-content: center; align-items: center;
        }
        .login-card {
            background: white; padding: 30px; border-radius: 20px; width: 90%; max-width: 400px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.3); text-align: center;
        }
        .login-card h2 { color: #2c3e50; margin-bottom: 20px; }
        .login-card i { font-size: 3.5rem; color: #e67e22; margin-bottom: 15px; } 

        header { 
            background: rgba(44, 62, 80, 0.95); 
            color: white; padding: 20px; display: flex; justify-content: space-between; align-items: center; 
            position: sticky; top: 0; z-index: 1000; box-shadow: 0 4px 10px rgba(0,0,0,0.1); backdrop-filter: blur(5px);
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

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .camera-container { width: 100%; border-radius: 15px; overflow: hidden; margin-bottom: 10px; display: none; border: 2px solid #2c3e50; }
        footer { text-align: center; color: #888; padding: 15px; font-size: 0.8rem; margin-top: 20px; } 

        @media print {
            body * { visibility: hidden; }
            #print-area, #print-area * { visibility: visible; }
            #print-area { position: absolute; left: 0; top: 0; width: 100%; }
        }
    </style>
</head>
<body> 

<div id="login-screen">
    <div class="login-card">
        <i class="fas fa-user-shield"></i>
        <h2>دخول النظام</h2>
        <input type="text" id="login-user" placeholder="اسم المستخدم">
        <input type="password" id="login-pass" placeholder="كلمة المرور">
        <button class="btn-action bg-save" style="width:100%; margin-top:15px;" onclick="handleLogin()">تسجيل الدخول</button>
        <p id="login-error" style="color:#e74c3c; font-size:0.9rem; margin-top:10px; display:none; font-weight:bold;">خطأ في البيانات، يرجى التأكد</p>
    </div>
</div> 

<div id="sidebar" class="sidebar">
    <div class="sidebar-header">
        <h3 id="sidebar-store-name">متجر السلطانة</h3>
        <p id="sidebar-user-login"><i class="fas fa-user-circle"></i> المدير العام</p>
    </div>
    <div class="sidebar-menu">
        <a href="#" onclick="showSection('home-screen'); toggleSidebar();"><i class="fas fa-home"></i> الرئيسية</a>
        <a href="#" onclick="showSection('expenses-screen'); toggleSidebar();"><i class="fas fa-money-bill-wave"></i> المصاريف</a>
        <a href="#" id="side-staff-link" onclick="showSection('staff-management-screen'); toggleSidebar();"><i class="fas fa-users-cog"></i> الموظفين</a>
        <a href="#" id="side-store-link" onclick="showSection('store-screen'); toggleSidebar();"><i class="fas fa-cog"></i> الإعدادات</a>
        <a href="#" onclick="logout()" style="background:rgba(231, 76, 60, 0.1); color:#e74c3c;"><i class="fas fa-sign-out-alt"></i> تسجيل الخروج</a>
    </div>
</div>
<div id="overlay" class="overlay" onclick="toggleSidebar()"></div> 

<header>
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
    <div id="home-screen" class="screen active">
        <div class="grid">
            <div class="card c-purchases" id="card-purchases" onclick="showSection('purchases-screen')"><i class="fas fa-truck-loading card-icon"></i><h3 class="card-title">المشتريات</h3><p class="card-sub">إضافة قوائم جديدة</p></div>
            <div class="card c-sales" id="card-sales" onclick="showSection('sales-screen')"><i class="fas fa-shopping-cart card-icon"></i><h3 class="card-title">المبيعات</h3><p class="card-sub">نقطة بيع سريعة (POS)</p></div>
            <div class="card c-reports" id="card-reports" onclick="showSection('reports-screen')"><i class="fas fa-chart-line card-icon"></i><h3 class="card-title">التقارير</h3><p class="card-sub">يومية، شهرية، أرباح</p></div>
            <div class="card c-inventory" id="card-inventory" onclick="showSection('inventory-screen')"><i class="fas fa-warehouse card-icon"></i><h3 class="card-title">المخزن</h3><p class="card-sub">جرد وتعديل الكميات</p></div>
            <div class="card c-accounts" id="card-accounts" onclick="showSection('accounts-screen')"><i class="fas fa-calculator card-icon"></i><h3 class="card-title">الحسابات</h3><p class="card-sub">مطابقة القاصة والأرباح</p></div>
            <div class="card c-subs" id="card-subscriptions" onclick="showSection('subscriptions-screen')"><i class="fas fa-id-card card-icon"></i><h3 class="card-title">الاشتراكات</h3><p class="card-sub">إدارة باقات الزبائن</p></div>
            <div class="card c-store" id="card-store" onclick="showSection('store-screen')"><i class="fas fa-store card-icon"></i><h3 class="card-title">المتجر</h3><p class="card-sub">المنصة والربط الإداري</p></div>
            <div class="card" id="card-expenses" style="background:#e74c3c" onclick="showSection('expenses-screen')"><i class="fas fa-money-bill-wave card-icon"></i><h3 class="card-title">المصاريف</h3><p class="card-sub">رواتب، إيجار، نثريات</p></div>
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
            <h3 style="color:#16a085"><i class="fas fa-coins"></i> الحسابات اليومية</h3>
            <div class="summary-box" style="background:var(--g-sales); margin-bottom:10px;">
                <div class="summary-title">إجمالي مبيعات اليوم</div>
                <div class="summary-val" id="day-sales-total">0 د.ع</div>
            </div>
            <div class="summary-box" style="background:var(--g-reports); margin-bottom:10px;">
                <div class="summary-title">صافي أرباح اليوم (بعد التكلفة)</div>
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
            <input type="text" id="p-barcode" placeholder="الباركود">
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
        <div class="inner-card">
            <h3 style="color:#e67e22"><i class="fas fa-cash-register"></i> نقطة بيع</h3>
            <input type="text" id="s-customer" placeholder="اسم الزبون (اختياري)">
            <button class="btn-action btn-camera" style="background:#e67e22" onclick="toggleCamera('reader-sales', 's-barcode')"><i class="fas fa-camera"></i> مسح باركود المادة</button>
            <div id="cam-box-reader-sales" class="camera-container"><div id="reader-sales"></div></div>
            <input type="text" id="s-barcode" placeholder="الباركود" oninput="searchProductByBarcode(this.value)">
            <input type="text" id="s-item-name" placeholder="اسم المادة">
            <div style="display:flex; gap:10px">
                <input type="number" id="s-price" placeholder="سعر البيع">
                <input type="number" id="s-qty" placeholder="العدد" value="1">
            </div>
            <button class="btn-action bg-add" style="background:#e67e22" onclick="addItemToSale()"><i class="fas fa-plus"></i> إضافة للسلة</button>
            <div class="total-display" id="sales-total-text" style="color:#e67e22; border-color:#e67e22">المبلغ الكلي: 0 د.ع</div>
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
            <h3 style="color:#2980b9"><i class="fas fa-link"></i> إعدادات الربط والتبليغات</h3>
            <label>توكن بوت التليجرام (Telegram Token):</label>
            <input type="text" id="tg-token" placeholder="أدخل التوكن هنا">
            <label>معرف الدردشة (Chat ID):</label>
            <input type="text" id="tg-chatid" placeholder="أدخل معرف المدير">
            <hr>
            <label>رابط قناة الواتساب للموظفين:</label>
            <input type="text" id="wa-channel-link" placeholder="ضع رابط القناة هنا">
            <div class="btn-row">
                <button class="btn-action bg-save" onclick="saveLinkSettings()">حفظ الإعدادات</button>
            </div>
        </div>
    </div>
</div> 

<div id="detail-modal" class="modal">
    <div class="modal-content" id="print-area">
        <h2 id="modal-title" style="text-align:center; border-bottom: 2px solid #333; padding-bottom:10px;">تفاصيل الفاتورة</h2>
        <div id="modal-info" style="margin-bottom:15px;"></div>
        <div id="modal-items-list"></div>
        <div id="modal-total" style="font-size:1.4rem; font-weight:bold; text-align:center; margin-top:20px; color:#e74c3c;"></div>
        <div class="btn-row" style="margin-top:20px;">
            <button class="btn-action bg-print" onclick="window.print()"><i class="fas fa-print"></i> طباعة</button>
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

<footer><p>© 2026 كاشير برو - النسخة v1.6</p></footer> 

<script>
    // --- البيانات والمتغيرات العامة ---
    let inventory = JSON.parse(localStorage.getItem('pos_inventory')) || [];
    let purchaseInvoices = JSON.parse(localStorage.getItem('pos_p_invoices')) || [];
    let salesInvoices = JSON.parse(localStorage.getItem('pos_s_invoices')) || [];
    let staffMembers = JSON.parse(localStorage.getItem('pos_staff')) || [];
    let subscriptions = JSON.parse(localStorage.getItem('pos_subscriptions')) || [];
    let expenses = JSON.parse(localStorage.getItem('pos_expenses')) || [];
    
    let tempPurchases = [];
    let tempSales = [];
    let currentEditIdx = null;
    let html5QrCode = null; 

    const ADMIN_USER = "admin";
    const ADMIN_PASS = "1234";
    let currentUser = null; 

    // --- نظام تسجيل الدخول ---
    function handleLogin() {
        const user = document.getElementById('login-user').value;
        const pass = document.getElementById('login-pass').value;
        const errorMsg = document.getElementById('login-error'); 

        if (user === ADMIN_USER && pass === ADMIN_PASS) {
            loginSuccess({ name: "المدير العام", role: "كامل الصلاحيات" });
        } else {
            const staff = staffMembers.find(s => s.user === user && s.pass === pass);
            if (staff) {
                loginSuccess(staff);
            } else {
                errorMsg.style.display = 'block';
            }
        }
    } 

    function loginSuccess(userData) {
        currentUser = userData;
        document.getElementById('login-screen').style.display = 'none';
        document.getElementById('sidebar-user-login').innerHTML = `<i class="fas fa-user-circle"></i> ${userData.name}`;
        applyPermissions(userData.role);
        initializeDashboard();
    } 

    function applyPermissions(role) {
        const ids = ['card-purchases', 'card-sales', 'card-reports', 'card-inventory', 'card-accounts', 'card-subscriptions', 'card-store', 'card-expenses'];
        ids.forEach(id => document.getElementById(id).style.display = "flex"); 

        if (role === "مبيعات فقط") {
            ['card-purchases', 'card-inventory', 'card-accounts', 'card-store', 'card-reports'].forEach(id => document.getElementById(id).style.display = "none");
            document.getElementById('side-staff-link').style.display = "none";
        } else if (role === "مخزن فقط") {
            ['card-sales', 'card-accounts', 'card-store', 'card-expenses'].forEach(id => document.getElementById(id).style.display = "none");
            document.getElementById('side-staff-link').style.display = "none";
        }
    } 

    function logout() { location.reload(); } 

    function initializeDashboard() {
        if (!currentUser) return;
        showSection('home-screen');
        if(document.getElementById('exp-date')) document.getElementById('exp-date').valueAsDate = new Date();
        renderStaff();
    } 

    function toggleSidebar() {
        document.getElementById('sidebar').classList.toggle('active');
        const overlay = document.getElementById('overlay');
        overlay.style.display = overlay.style.display === 'block' ? 'none' : 'block';
    } 

    function showSection(id) { 
        stopCamera(); 
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active')); 
        document.getElementById(id).classList.add('active'); 
        if(id === 'inventory-screen') renderInventory(); 
        if(id === 'accounts-screen') calculateAccounts(); 
        if(id === 'staff-management-screen') renderStaff(); 
        if(id === 'subscriptions-screen') renderSubscriptions(); 
        if(id === 'expenses-screen') renderExpenses();
        window.scrollTo(0,0); 
    } 

    // --- المصاريف ---
    function saveExpense() {
        const title = document.getElementById('exp-title').value;
        const amount = parseFloat(document.getElementById('exp-amount').value) || 0;
        const date = document.getElementById('exp-date').value;
        if(!title || amount <= 0 || !date) return alert("أكمل بيانات المصروف");
        expenses.push({ id: Date.now(), title, amount, date });
        localStorage.setItem('pos_expenses', JSON.stringify(expenses));
        renderExpenses();
        alert("تم حفظ المصروف");
        clearInputs(['exp-title', 'exp-amount']);
    } 

    function renderExpenses() {
        const container = document.getElementById('expenses-list-container');
        container.innerHTML = expenses.length === 0 ? "<p style='text-align:center;'>لا توجد مصاريف</p>" : "";
        [...expenses].reverse().forEach((ex) => {
            container.innerHTML += `<div class="staff-item">
                <div><strong>${ex.title}</strong><br><small>${ex.date} | ${ex.amount.toLocaleString()} د.ع</small></div>
                <button class="edit-btn" style="background:#e74c3c" onclick="deleteExpense(${ex.id})"><i class="fas fa-trash"></i></button>
            </div>`;
        });
    }

    function deleteExpense(id) {
        if(confirm("حذف المصروف؟")) {
            expenses = expenses.filter(ex => ex.id !== id);
            localStorage.setItem('pos_expenses', JSON.stringify(expenses));
            renderExpenses();
        }
    }

    // --- الحسابات ---
    function calculateAccounts() {
        const todayStr = new Date().toLocaleDateString('en-CA');
        const todayAr = new Date().toLocaleDateString('ar-EG');
        let daySalesInvoices = salesInvoices.filter(inv => inv.date.includes(todayAr));
        let totalSales = daySalesInvoices.reduce((sum, inv) => sum + inv.total, 0);
        let totalProfit = 0;
        daySalesInvoices.forEach(inv => {
            inv.items.forEach(item => {
                let product = inventory.find(i => i.name === item.name);
                let cost = product ? product.cost : 0;
                totalProfit += (item.price - cost) * item.qty;
            });
        });
        let totalExp = expenses.filter(ex => ex.date === todayStr).reduce((sum, ex) => sum + ex.amount, 0);
        document.getElementById('day-sales-total').innerText = totalSales.toLocaleString() + " د.ع";
        document.getElementById('day-profit-total').innerText = totalProfit.toLocaleString() + " د.ع";
        document.getElementById('day-expenses-total').innerText = totalExp.toLocaleString() + " د.ع";
    } 

    // --- المشتريات ---
    function addItemToPurchase() {
        const name = document.getElementById('p-item-name').value;
        const qty = parseFloat(document.getElementById('p-qty').value) || 0;
        const cost = parseFloat(document.getElementById('p-cost').value) || 0;
        if(!name || qty <= 0) return alert("أكمل البيانات");
        tempPurchases.push({ barcode: document.getElementById('p-barcode').value, name: name, qty: qty, price: cost, sellPrice: document.getElementById('p-price').value });
        updateTempTotal('purchase-total-text', tempPurchases);
        clearInputs(['p-barcode', 'p-item-name', 'p-qty', 'p-cost', 'p-price']);
    } 

    function savePurchase() {
        if(tempPurchases.length === 0) return alert("الفاتورة فارغة");
        const invoice = { id: document.getElementById('p-invoice-num').value || "INV-" + Date.now(), customer: document.getElementById('p-customer').value || "مجهز عام", date: new Date().toLocaleString('ar-EG'), items: [...tempPurchases], total: tempPurchases.reduce((sum, i) => sum + (i.qty * i.price), 0), type: 'مشتريات' };
        purchaseInvoices.push(invoice);
        localStorage.setItem('pos_p_invoices', JSON.stringify(purchaseInvoices));
        tempPurchases.forEach(item => {
            let idx = inventory.findIndex(i => i.barcode === item.barcode && item.barcode !== "");
            if(idx > -1) { inventory[idx].qty += item.qty; inventory[idx].cost = item.price; inventory[idx].price = item.sellPrice; } 
            else { inventory.push({barcode: item.barcode, name: item.name, qty: item.qty, cost: item.price, price: item.sellPrice}); }
        });
        localStorage.setItem('pos_inventory', JSON.stringify(inventory));
        tempPurchases = []; alert("تم الحفظ"); showSection('home-screen');
    }

    // --- المبيعات ---
    function searchProductByBarcode(code) { 
        const product = inventory.find(i => i.barcode === code); 
        if (product) { 
            document.getElementById('s-item-name').value = product.name; 
            document.getElementById('s-price').value = product.price; 
        } 
    } 

    function addItemToSale() {
        const name = document.getElementById('s-item-name').value;
        const price = parseFloat(document.getElementById('s-price').value) || 0;
        const qty = parseFloat(document.getElementById('s-qty').value) || 0;
        if(!name || qty <= 0) return alert("أكمل البيانات");
        tempSales.push({ name, price, qty });
        updateTempTotal('sales-total-text', tempSales);
        clearInputs(['s-barcode', 's-item-name', 's-price', 's-qty']);
        document.getElementById('s-qty').value = "1";
    }

    function saveSale() {
        if (tempSales.length === 0) return alert("السلة فارغة");
        const invoice = { 
            id: "SAL-" + Date.now(), 
            customer: document.getElementById('s-customer').value || "زبون نقدي", 
            date: new Date().toLocaleString('ar-EG'), 
            items: [...tempSales], 
            total: tempSales.reduce((sum, i) => sum + (i.qty * i.price), 0), 
            type: 'مبيعات' 
        };
        salesInvoices.push(invoice);
        localStorage.setItem('pos_s_invoices', JSON.stringify(salesInvoices));
        tempSales.forEach(saleItem => { 
            let idx = inventory.findIndex(i => i.name === saleItem.name); 
            if (idx > -1) inventory[idx].qty -= saleItem.qty; 
        });
        localStorage.setItem('pos_inventory', JSON.stringify(inventory));
        tempSales = []; 
        document.getElementById('sales-total-text').innerText = "المبلغ الكلي: 0 د.ع";
        alert("تم حفظ عملية البيع بنجاح"); 
        showSection('home-screen');
    }

    // --- الكاميرا والباركود ---
    function toggleCamera(readerId, inputId) {
        const camBox = document.getElementById('cam-box-' + readerId);
        if (camBox.style.display === 'block') {
            stopCamera();
            camBox.style.display = 'none';
        } else {
            camBox.style.display = 'block';
            startScanner(readerId, inputId);
        }
    }

    function startScanner(readerId, inputId) {
        html5QrCode = new Html5Qrcode(readerId);
        html5QrCode.start(
            { facingMode: "environment" }, 
            { fps: 10, qrbox: { width: 250, height: 150 } },
            (decodedText) => {
                document.getElementById(inputId).value = decodedText;
                if (inputId === 's-barcode') searchProductByBarcode(decodedText);
                stopCamera();
                document.getElementById('cam-box-' + readerId).style.display = 'none';
            },
            (err) => {}
        ).catch(err => alert("خطأ في تشغيل الكاميرا"));
    }

    function stopCamera() {
        if (html5QrCode && html5QrCode.isScanning) {
            html5QrCode.stop();
        }
    }

    // --- دوال مساعدة ---
    function clearInputs(ids) { ids.forEach(id => { if(document.getElementById(id)) document.getElementById(id).value = ""; }); }
    
    function updateTempTotal(elementId, array) {
        const total = array.reduce((sum, item) => sum + (parseFloat(item.qty) * parseFloat(item.price)), 0);
        document.getElementById(elementId).innerText = `المبلغ الكلي: ${total.toLocaleString()} د.ع`;
    }

    function renderInventory() {
        const container = document.getElementById('inventory-container');
        container.innerHTML = inventory.length === 0 ? "<p>المخزن فارغ</p>" : "";
        inventory.forEach((item, idx) => {
            container.innerHTML += `<div class="inventory-item">
                <div><strong>${item.name}</strong><br><small>الكمية: ${item.qty} | السعر: ${item.price}</small></div>
                <button class="edit-btn" onclick="openEditModal(${idx})">تعديل</button>
            </div>`;
        });
    }

    function renderStaff() {
        const container = document.getElementById('staff-list-container');
        if(!container) return;
        container.innerHTML = staffMembers.length === 0 ? "<p>لا يوجد موظفين</p>" : "";
        staffMembers.forEach((s, idx) => {
            container.innerHTML += `<div class="staff-item"><div><strong>${s.name}</strong><br><small>${s.role}</small></div><button class="edit-btn" style="background:#e74c3c" onclick="deleteStaff(${idx})">حذف</button></div>`;
        });
    }

    function addNewStaff() {
        const name = document.getElementById('staff-name').value;
        const user = document.getElementById('staff-user').value;
        const pass = document.getElementById('staff-pass').value;
        const role = document.getElementById('staff-role').value;
        if(!name || !user || !pass) return alert("يرجى ملء كافة البيانات");
        staffMembers.push({ name, user, pass, role });
        localStorage.setItem('pos_staff', JSON.stringify(staffMembers));
        renderStaff();
        clearInputs(['staff-name', 'staff-user', 'staff-pass']);
    }

    function deleteStaff(idx) { if(confirm("حذف الحساب؟")) { staffMembers.splice(idx, 1); localStorage.setItem('pos_staff', JSON.stringify(staffMembers)); renderStaff(); } }
</script>
</body>
</html>

