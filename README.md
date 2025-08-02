<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>إدارة ومتابعة أوقاف المحافظات</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@3.7.0/dist/chart.min.js"></script>
    <style>
        /* Chosen Palette: Warm Neutrals (Background), White (Container), Dark Grays (Text), Soft Blue (Primary), Muted Red (Danger), Slate (Secondary), Light Green/Orange (Messages) */
        body {
            font-family: "Inter", sans-serif;
            background-color: #f0f4f8; /* Light gray/off-white */
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        .header {
            background-color: #ffffff;
            width: 100%;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
            padding: 1rem 2rem;
            text-align: center;
        }
        .nav-bar {
            background-color: #ffffff;
            width: 100%;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
            padding: 0.5rem 2rem;
            display: flex;
            justify-content: center;
            gap: 1rem;
            flex-wrap: wrap;
        }
        .nav-item {
            padding: 0.75rem 1.25rem;
            border-radius: 0.5rem;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s, color 0.2s;
            color: #475569; /* slate-600 */
        }
        .nav-item:hover {
            background-color: #e2e8f0; /* slate-200 */
        }
        .nav-item.active {
            background-color: #3b82f6; /* blue-500 */
            color: #ffffff;
        }
        .nav-item.active:hover {
            background-color: #2563eb; /* blue-600 */
        }
        .main-content {
            background-color: #ffffff;
            padding: 2.5rem;
            border-radius: 0.75rem;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            max-width: 1200px; /* Increased max-width for comprehensive app */
            width: 100%;
            margin-top: 2rem;
            margin-bottom: 2rem;
            text-align: right;
        }
        .section {
            display: none; /* Hidden by default, shown by JS */
        }
        .section.active {
            display: block;
        }
        .message-box {
            background-color: #ffe0b2;
            color: #e65100;
            padding: 1rem;
            border-radius: 0.5rem;
            margin-bottom: 1rem;
            border: 1px solid #ff9800;
            display: none;
        }
        .input-group {
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
            margin-bottom: 1rem;
        }
        .input-group label {
            font-weight: 600;
            color: #334155;
        }
        .input-group input, .input-group select, .input-group textarea {
            padding: 0.75rem;
            border: 1px solid #cbd5e1;
            border-radius: 0.5rem;
            width: 100%;
            box-sizing: border-box;
        }
        .btn {
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .btn-primary {
            background-color: #3b82f6; /* blue-500 */
            color: #ffffff;
        }
        .btn-primary:hover {
            background-color: #2563eb; /* blue-600 */
        }
        .btn-danger {
            background-color: #ef4444; /* red-500 */
            color: #ffffff;
        }
        .btn-danger:hover {
            background-color: #dc2626; /* red-600 */
        }
        .btn-secondary {
            background-color: #64748b; /* slate-500 */
            color: #ffffff;
        }
        .btn-secondary:hover {
            background-color: #475569; /* slate-600 */
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 1.5rem;
        }
        th, td {
            border: 1px solid #e2e8f0;
            padding: 0.75rem;
            text-align: right; /* Align text to right for RTL */
            vertical-align: top; /* Align cell content to top */
        }
        th {
            background-color: #e2e8f0;
            font-weight: 600;
            color: #334155;
        }
        tr:nth-child(even) {
            background-color: #f8fafc; /* slate-50 */
        }
        .actions-cell {
            display: flex;
            gap: 0.5rem;
            justify-content: flex-end; /* Align buttons to the right */
            flex-wrap: wrap; /* Allow buttons to wrap */
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px; /* Max width for charts */
            margin-left: auto;
            margin-right: auto;
            height: 350px; /* Base height for charts */
            max-height: 450px; /* Max height for charts */
            background-color: #fdfdfd;
            padding: 1rem;
            border-radius: 0.5rem;
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
        }
        @media (max-width: 768px) {
            .main-content {
                padding: 1.5rem;
            }
            .nav-bar {
                flex-direction: column;
                align-items: center;
            }
            .nav-item {
                width: 100%;
                text-align: center;
            }
            .chart-container {
                height: 300px; /* Smaller height on mobile */
                max-height: 350px;
            }
        }
    </style>
</head>
<body>
    <!-- Chosen Palette: Warm Neutrals (Background: #f0f4f8), White (Container: #ffffff), Dark Grays (Text: #334155, #475569), Soft Blue (Primary: #3b82f6), Muted Red (Danger: #ef4444), Slate (Secondary: #64748b), Light Green (Success Message: #e8f5e9 bg, #2e7d32 text), Light Orange (Info Message: #ffe0b2 bg, #e65100 text) -->
    <!-- Application Structure Plan: The SPA is structured as a dashboard with a top navigation bar allowing users to switch between different management sections: Dashboard (Overview), Correspondence, Circulars, Activities, and Performance Evaluation. This modular approach enhances usability by segmenting complex data into digestible, task-oriented views. The user flow is linear within each section (form -> table -> actions) but non-linear across sections via navigation. This structure was chosen to provide a comprehensive yet intuitive interface for managing diverse types of endowment data and assessing directorate performance. -->
    <!-- Visualization & Content Choices:
    - Dashboard: Goal: Inform. Viz/Presentation: Dynamic Stats (HTML/JS). Interaction: Quick links. Justification: Provides immediate high-level overview.
    - Correspondence/Circulars/Activities Management: Goal: Organize, Track. Viz/Presentation: Forms (HTML/JS), Dynamic Tables (HTML/JS). Interaction: Add, Edit, Delete, Filter by Directorate/Status/Date, Search. Justification: Core CRUD operations for detailed item management.
    - Performance Evaluation: Goal: Compare, Analyze. Viz/Presentation: Chart.js Bar and Doughnut Charts. Interaction: Filters (e.g., by Directorate). Justification: Visualizes aggregated data for performance insights, making complex comparisons clear and actionable.
      - Chart 1 (Correspondence Status): Stacked Bar Chart. Goal: Compare status distribution across directorates.
      - Chart 2 (Activities Completion): Bar Chart. Goal: Compare completed vs. planned activities.
      - Chart 3 (Endowment Value Distribution): Doughnut Chart. Goal: Show proportion of endowment value per province.
    - CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->

    <header class="header">
        <h1 class="text-3xl font-bold text-gray-800">برنامج إدارة ومتابعة أوقاف المحافظات</h1>
        <p class="text-md text-gray-600 mt-2">مُعد البرنامج: رئيس مهندسين: رائد ابراهيم خليل ابراهيم</p>
    </header>

    <nav class="nav-bar">
        <div class="nav-item active" data-section="dashboard">الرئيسية</div>
        <div class="nav-item" data-section="correspondence">المراسلات</div>
        <div class="nav-item" data-section="circulars">الإعمامات</div>
        <div class="nav-item" data-section="activities">النشاطات</div>
        <div class="nav-item" data-section="performance">تقييم الأداء</div>
    </nav>

    <main class="main-content">
        <div id="messageBox" class="message-box"></div>

        <!-- Dashboard Section -->
        <section id="dashboard" class="section active">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">الرئيسية - نظرة عامة</h2>
            <p class="text-gray-600 mb-6">
                يقدم هذا القسم ملخصاً سريعاً لأهم الإحصائيات والمقاييس الرئيسية المتعلقة بالمراسلات والإعمامات والنشاطات عبر مديريات الوقف الشيعي في البصرة وميسان وذي قار. يهدف إلى توفير لمحة عامة عن حالة العمل الجارية والأداء العام.
            </p>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                <div class="bg-blue-50 p-6 rounded-lg shadow-sm text-center">
                    <p class="text-5xl font-bold text-blue-700" id="totalCorrespondence">0</p>
                    <p class="text-lg text-blue-600">إجمالي المراسلات</p>
                </div>
                <div class="bg-green-50 p-6 rounded-lg shadow-sm text-center">
                    <p class="text-5xl font-bold text-green-700" id="completedActivities">0</p>
                    <p class="text-lg text-green-600">نشاطات مكتملة</p>
                </div>
                <div class="bg-yellow-50 p-6 rounded-lg shadow-sm text-center">
                    <p class="text-5xl font-bold text-yellow-700" id="pendingItems">0</p>
                    <p class="text-lg text-yellow-600">عناصر قيد التنفيذ</p>
                </div>
            </div>
            <div class="text-center mt-8">
                <button class="btn btn-primary" onclick="showSection('correspondence'); document.querySelector('.nav-item[data-section=\'correspondence\']').click();">إضافة مراسلة جديدة</button>
                <button class="btn btn-primary mr-2" onclick="showSection('activities'); document.querySelector('.nav-item[data-section=\'activities\']').click();">إضافة نشاط جديد</button>
            </div>
        </section>

        <!-- Correspondence Section -->
        <section id="correspondence" class="section">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">إدارة المراسلات</h2>
            <p class="text-gray-600 mb-6">
                يتيح لك هذا القسم إضافة وتتبع وتعديل وحذف المراسلات الصادرة والواردة لمديريات الوقف الشيعي. يمكنك تسجيل تفاصيل كل مراسلة ومتابعة حالتها لضمان سير العمل بكفاءة.
            </p>
            <form id="correspondenceForm" class="bg-gray-50 p-6 rounded-md mb-8">
                <h3 class="text-xl font-semibold text-gray-700 mb-4">إضافة / تعديل مراسلة</h3>
                <input type="hidden" id="correspondenceId">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div class="input-group">
                        <label for="correspondenceSenderReceiver">الجهة المرسلة / المستلمة:</label>
                        <input type="text" id="correspondenceSenderReceiver" placeholder="أدخل اسم الجهة" required class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="correspondenceBookNumber">رقم الكتاب:</label>
                        <input type="text" id="correspondenceBookNumber" placeholder="أدخل رقم الكتاب" class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="correspondenceDate">تاريخ المراسلة:</label>
                        <input type="date" id="correspondenceDate" required class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="correspondenceSubject">الموضوع:</label>
                        <input type="text" id="correspondenceSubject" placeholder="أدخل موضوع المراسلة" required class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="correspondenceDirectorate">المديرية المعنية:</label>
                        <select id="correspondenceDirectorate" required class="rounded-md">
                            <option value="">اختر المديرية</option>
                            <option value="البصرة">البصرة</option>
                            <option value="ميسان">ميسان</option>
                            <option value="ذي قار">ذي قار</option>
                        </select>
                    </div>
                    <div class="input-group">
                        <label for="correspondenceStatus">الحالة:</label>
                        <select id="correspondenceStatus" required class="rounded-md">
                            <option value="">اختر الحالة</option>
                            <option value="قيد التنفيذ">قيد التنفيذ</option>
                            <option value="تم الإنجاز">تم الإنجاز</option>
                            <option value="مؤجل">مؤجل</option>
                            <option value="ملغى">ملغى</option>
                        </select>
                    </div>
                    <div class="input-group">
                        <label for="correspondenceExpectedDate">تاريخ الإنجاز المتوقع:</label>
                        <input type="date" id="correspondenceExpectedDate" class="rounded-md">
                    </div>
                    <div class="input-group md:col-span-2">
                        <label for="correspondenceNotes">ملاحظات:</label>
                        <textarea id="correspondenceNotes" placeholder="أضف أي ملاحظات إضافية" rows="3" class="rounded-md"></textarea>
                    </div>
                </div>
                <button type="submit" class="btn btn-primary w-full mt-4">إضافة مراسلة</button>
            </form>

            <h3 class="text-xl font-semibold text-gray-700 mb-4">قائمة المراسلات</h3>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
                <div class="input-group">
                    <label for="correspondenceFilterDirectorate">تصفية حسب المديرية:</label>
                    <select id="correspondenceFilterDirectorate" class="rounded-md">
                        <option value="all">جميع المديريات</option>
                        <option value="البصرة">البصرة</option>
                        <option value="ميسان">ميسان</option>
                        <option value="ذي قار">ذي قار</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="correspondenceFilterStatus">تصفية حسب الحالة:</label>
                    <select id="correspondenceFilterStatus" class="rounded-md">
                        <option value="all">جميع الحالات</option>
                        <option value="قيد التنفيذ">قيد التنفيذ</option>
                        <option value="تم الإنجاز">تم الإنجاز</option>
                        <option value="مؤجل">مؤجل</option>
                        <option value="ملغى">ملغى</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="correspondenceSearch">بحث:</label>
                    <input type="text" id="correspondenceSearch" placeholder="بحث بالرقم، التاريخ، الموضوع، الجهة" class="rounded-md">
                </div>
            </div>
            <div class="overflow-x-auto rounded-md border border-gray-200">
                <table id="correspondenceTable" class="min-w-full bg-white">
                    <thead>
                        <tr>
                            <th>الرقم</th>
                            <th>رقم الكتاب</th>
                            <th>الجهة</th>
                            <th>التاريخ</th>
                            <th>الموضوع</th>
                            <th>المديرية</th>
                            <th>الحالة</th>
                            <th>الإجراءات</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Data will be inserted here -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- Circulars Section -->
        <section id="circulars" class="section">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">إدارة الإعمامات</h2>
            <p class="text-gray-600 mb-6">
                يتيح لك هذا القسم تسجيل ومتابعة الإعمامات الصادرة، وتحديد المديريات المستهدفة، وتتبع حالة التنفيذ لضمان وصول التعليمات وتطبيقها بفعالية.
            </p>
            <form id="circularsForm" class="bg-gray-50 p-6 rounded-md mb-8">
                <h3 class="text-xl font-semibold text-gray-700 mb-4">إضافة / تعديل إعمام</h3>
                <input type="hidden" id="circularId">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div class="input-group">
                        <label for="circularIssueDate">تاريخ الإصدار:</label>
                        <input type="date" id="circularIssueDate" required class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="circularBookNumber">رقم الإعمام:</label>
                        <input type="text" id="circularBookNumber" placeholder="أدخل رقم الإعمام" class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="circularSubject">الموضوع:</label>
                        <input type="text" id="circularSubject" placeholder="أدخل موضوع الإعمام" required class="rounded-md">
                    </div>
                    <div class="input-group md:col-span-2">
                        <label for="circularTargetDirectorates">المديريات المستهدفة:</label>
                        <select id="circularTargetDirectorates" multiple required class="rounded-md h-24">
                            <option value="البصرة">البصرة</option>
                            <option value="ميسان">ميسان</option>
                            <option value="ذي قار">ذي قار</option>
                        </select>
                        <p class="text-sm text-gray-500">اختر أكثر من مديرية بالضغط على Ctrl/Cmd والنقر.</p>
                    </div>
                    <div class="input-group">
                        <label for="circularStatus">الحالة:</label>
                        <select id="circularStatus" required class="rounded-md">
                            <option value="">اختر الحالة</option>
                            <option value="صادر">صادر</option>
                            <option value="قيد التنفيذ">قيد التنفيذ</option>
                            <option value="تم التنفيذ">تم التنفيذ</option>
                        </select>
                    </div>
                    <div class="input-group">
                        <label for="circularExpectedDate">تاريخ التنفيذ المتوقع:</label>
                        <input type="date" id="circularExpectedDate" class="rounded-md">
                    </div>
                    <div class="input-group md:col-span-2">
                        <label for="circularNotes">ملاحظات:</label>
                        <textarea id="circularNotes" placeholder="أضف أي ملاحظات إضافية" rows="3" class="rounded-md"></textarea>
                    </div>
                </div>
                <button type="submit" class="btn btn-primary w-full mt-4">إضافة إعمام</button>
            </form>

            <h3 class="text-xl font-semibold text-gray-700 mb-4">قائمة الإعمامات</h3>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
                <div class="input-group">
                    <label for="circularFilterDirectorate">تصفية حسب المديرية المستهدفة:</label>
                    <select id="circularFilterDirectorate" class="rounded-md">
                        <option value="all">جميع المديريات</option>
                        <option value="البصرة">البصرة</option>
                        <option value="ميسان">ميسان</option>
                        <option value="ذي قار">ذي قار</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="circularFilterStatus">تصفية حسب الحالة:</label>
                    <select id="circularFilterStatus" class="rounded-md">
                        <option value="all">جميع الحالات</option>
                        <option value="صادر">صادر</option>
                        <option value="قيد التنفيذ">قيد التنفيذ</option>
                        <option value="تم التنفيذ">تم التنفيذ</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="circularSearch">بحث:</label>
                    <input type="text" id="circularSearch" placeholder="بحث بالرقم، التاريخ، الموضوع، المديرية" class="rounded-md">
                </div>
            </div>
            <div class="overflow-x-auto rounded-md border border-gray-200">
                <table id="circularsTable" class="min-w-full bg-white">
                    <thead>
                        <tr>
                            <th>الرقم</th>
                            <th>رقم الإعمام</th>
                            <th>تاريخ الإصدار</th>
                            <th>الموضوع</th>
                            <th>المديريات المستهدفة</th>
                            <th>الحالة</th>
                            <th>الإجراءات</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Data will be inserted here -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- Activities Section -->
        <section id="activities" class="section">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">إدارة النشاطات</h2>
            <p class="text-gray-600 mb-6">
                يساعدك هذا القسم في تخطيط وتسجيل ومتابعة النشاطات والمشاريع التي تقوم بها مديريات الوقف الشيعي، مع تتبع حالتها والميزانية المرصودة لها.
            </p>
            <form id="activitiesForm" class="bg-gray-50 p-6 rounded-md mb-8">
                <h3 class="text-xl font-semibold text-gray-700 mb-4">إضافة / تعديل نشاط</h3>
                <input type="hidden" id="activityId">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div class="input-group">
                        <label for="activityName">اسم النشاط:</label>
                        <input type="text" id="activityName" placeholder="أدخل اسم النشاط" required class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="activityStartDate">تاريخ البدء:</label>
                        <input type="date" id="activityStartDate" required class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="activityEndDate">تاريخ الانتهاء:</label>
                        <input type="date" id="activityEndDate" class="rounded-md">
                    </div>
                    <div class="input-group">
                        <label for="activityDirectorate">المديرية المسؤولة:</label>
                        <select id="activityDirectorate" required class="rounded-md">
                            <option value="">اختر المديرية</option>
                            <option value="البصرة">البصرة</option>
                            <option value="ميسان">ميسان</option>
                            <option value="ذي قار">ذي قار</option>
                        </select>
                    </div>
                    <div class="input-group">
                        <label for="activityStatus">الحالة:</label>
                        <select id="activityStatus" required class="rounded-md">
                            <option value="">اختر الحالة</option>
                            <option value="مخطط">مخطط</option>
                            <option value="قيد التنفيذ">قيد التنفيذ</option>
                            <option value="مكتمل">مكتمل</option>
                            <option value="ملغى">ملغى</option>
                        </select>
                    </div>
                    <div class="input-group">
                        <label for="activityBudget">الميزانية المرصودة (د.ع):</label>
                        <input type="number" id="activityBudget" placeholder="أدخل الميزانية" min="0" class="rounded-md">
                    </div>
                    <div class="input-group md:col-span-2">
                        <label for="activityNotes">ملاحظات:</label>
                        <textarea id="activityNotes" placeholder="أضف أي ملاحظات إضافية" rows="3" class="rounded-md"></textarea>
                    </div>
                </div>
                <button type="submit" class="btn btn-primary w-full mt-4">إضافة نشاط</button>
            </form>

            <h3 class="text-xl font-semibold text-gray-700 mb-4">قائمة النشاطات</h3>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
                <div class="input-group">
                    <label for="activityFilterDirectorate">تصفية حسب المديرية:</label>
                    <select id="activityFilterDirectorate" class="rounded-md">
                        <option value="all">جميع المديريات</option>
                        <option value="البصرة">البصرة</option>
                        <option value="ميسان">ميسان</option>
                        <option value="ذي قار">ذي قار</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="activityFilterStatus">تصفية حسب الحالة:</label>
                    <select id="activityFilterStatus" class="rounded-md">
                        <option value="all">جميع الحالات</option>
                        <option value="مخطط">مخطط</option>
                        <option value="قيد التنفيذ">قيد التنفيذ</option>
                        <option value="مكتمل">مكتمل</option>
                        <option value="ملغى">ملغى</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="activitySearch">بحث:</label>
                    <input type="text" id="activitySearch" placeholder="بحث بالاسم، التاريخ، المديرية" class="rounded-md">
                </div>
            </div>
            <div class="overflow-x-auto rounded-md border border-gray-200">
                <table id="activitiesTable" class="min-w-full bg-white">
                    <thead>
                        <tr>
                            <th>الرقم</th>
                            <th>اسم النشاط</th>
                            <th>تاريخ البدء</th>
                            <th>المديرية</th>
                            <th>الحالة</th>
                            <th>الميزانية</th>
                            <th>الإجراءات</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Data will be inserted here -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- Performance Evaluation Section -->
        <section id="performance" class="section">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">تقييم الأداء</h2>
            <p class="text-gray-600 mb-6">
                يعرض هذا القسم رسوماً بيانية توضيحية لتقييم أداء مديريات الوقف الشيعي بناءً على بيانات المراسلات والإعمامات والنشاطات. تساعد هذه الرسوم في تحديد نقاط القوة والضعف واتخاذ القرارات المستنيرة.
            </p>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                <div class="chart-container">
                    <canvas id="correspondenceStatusChart"></canvas>
                    <p class="text-center text-sm text-gray-500 mt-2">حالة المراسلات حسب المديرية</p>
                </div>
                <div class="chart-container">
                    <canvas id="activitiesCompletionChart"></canvas>
                    <p class="text-center text-sm text-gray-500 mt-2">النشاطات المكتملة مقابل المخطط لها</p>
                </div>
                <div class="chart-container lg:col-span-2">
                    <canvas id="endowmentValueChart"></canvas>
                    <p class="text-center text-sm text-gray-500 mt-2">توزيع قيمة الأوقاف حسب المحافظة</p>
                </div>
            </div>
        </section>

        <!-- Custom Confirmation Modal (for delete actions) -->
        <div id="customConfirmModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center z-50 hidden">
            <div class="bg-white p-6 rounded-lg shadow-xl text-center max-w-sm mx-auto">
                <p class="text-lg font-semibold mb-4">هل أنت متأكد من هذا الإجراء؟</p>
                <div class="flex justify-center gap-4">
                    <button id="confirmActionBtn" class="btn btn-danger">تأكيد</button>
                    <button id="cancelActionBtn" class="btn btn-secondary">إلغاء</button>
                </div>
            </div>
        </div>

    </main>

    <script>
        // Global data arrays (in-memory storage for this version)
        let correspondenceData = [];
        let circularsData = [];
        let activitiesData = [];
        let endowmentsData = []; // Added for endowment value chart

        // Current edit IDs for forms
        let currentCorrespondenceEditId = null;
        let currentCircularEditId = null;
        let currentActivityEditId = null;

        // Chart.js instances
        let correspondenceStatusChartInstance;
        let activitiesCompletionChartInstance;
        let endowmentValueChartInstance;

        // DOM Elements
        const messageBox = document.getElementById('messageBox');
        const navItems = document.querySelectorAll('.nav-item');
        const sections = document.querySelectorAll('.section');

        // Correspondence DOM Elements
        const correspondenceForm = document.getElementById('correspondenceForm');
        const correspondenceIdInput = document.getElementById('correspondenceId');
        const correspondenceSenderReceiverInput = document.getElementById('correspondenceSenderReceiver');
        const correspondenceBookNumberInput = document.getElementById('correspondenceBookNumber'); // New input
        const correspondenceDateInput = document.getElementById('correspondenceDate');
        const correspondenceSubjectInput = document.getElementById('correspondenceSubject');
        const correspondenceDirectorateSelect = document.getElementById('correspondenceDirectorate');
        const correspondenceStatusSelect = document.getElementById('correspondenceStatus');
        const correspondenceExpectedDateInput = document.getElementById('correspondenceExpectedDate');
        const correspondenceNotesInput = document.getElementById('correspondenceNotes');
        const correspondenceTableBody = document.querySelector('#correspondenceTable tbody');
        const correspondenceFilterDirectorateSelect = document.getElementById('correspondenceFilterDirectorate');
        const correspondenceFilterStatusSelect = document.getElementById('correspondenceFilterStatus');
        const correspondenceSearchInput = document.getElementById('correspondenceSearch');

        // Circulars DOM Elements
        const circularsForm = document.getElementById('circularsForm');
        const circularIdInput = document.getElementById('circularId');
        const circularIssueDateInput = document.getElementById('circularIssueDate');
        const circularBookNumberInput = document.getElementById('circularBookNumber'); // New input
        const circularSubjectInput = document.getElementById('circularSubject');
        const circularTargetDirectoratesSelect = document.getElementById('circularTargetDirectorates');
        const circularStatusSelect = document.getElementById('circularStatus');
        const circularExpectedDateInput = document.getElementById('circularExpectedDate');
        const circularNotesInput = document.getElementById('circularNotes');
        const circularsTableBody = document.querySelector('#circularsTable tbody');
        const circularFilterDirectorateSelect = document.getElementById('circularFilterDirectorate');
        const circularFilterStatusSelect = document.getElementById('circularFilterStatus');
        const circularSearchInput = document.getElementById('circularSearch');

        // Activities DOM Elements
        const activitiesForm = document.getElementById('activitiesForm');
        const activityIdInput = document.getElementById('activityId');
        const activityNameInput = document.getElementById('activityName');
        const activityStartDateInput = document.getElementById('activityStartDate');
        const activityEndDateInput = document.getElementById('activityEndDate');
        const activityDirectorateSelect = document.getElementById('activityDirectorate');
        const activityStatusSelect = document.getElementById('activityStatus');
        const activityBudgetInput = document.getElementById('activityBudget');
        const activityNotesInput = document.getElementById('activityNotes');
        const activitiesTableBody = document.querySelector('#activitiesTable tbody');
        const activityFilterDirectorateSelect = document.getElementById('activityFilterDirectorate');
        const activityFilterStatusSelect = document.getElementById('activityFilterStatus');
        const activitySearchInput = document.getElementById('activitySearch');

        // Dashboard DOM Elements
        const totalCorrespondenceDisplay = document.getElementById('totalCorrespondence');
        const completedActivitiesDisplay = document.getElementById('completedActivities');
        const pendingItemsDisplay = document.getElementById('pendingItems');

        // Utility Functions
        function showMessage(message, isError = false) {
            messageBox.textContent = message;
            messageBox.style.display = 'block';
            if (isError) {
                messageBox.style.backgroundColor = '#ffcdd2';
                messageBox.style.color = '#b71c1c';
                messageBox.style.borderColor = '#ef5350';
            } else {
                messageBox.style.backgroundColor = '#e8f5e9';
                messageBox.style.color = '#2e7d32';
                messageBox.style.borderColor = '#66bb6a';
            }
            setTimeout(() => {
                messageBox.style.display = 'none';
            }, 5000);
        }

        function showCustomConfirm(message, onConfirm) {
            const modal = document.getElementById('customConfirmModal');
            const confirmBtn = document.getElementById('confirmActionBtn');
            const cancelBtn = document.getElementById('cancelActionBtn');
            const modalMessage = modal.querySelector('p');

            modalMessage.textContent = message;
            modal.classList.remove('hidden');

            confirmBtn.onclick = () => {
                onConfirm();
                modal.classList.add('hidden');
            };
            cancelBtn.onclick = () => {
                modal.classList.add('hidden');
            };
        }

        function wrapLabels(label, maxLen = 16) {
            if (label.length <= maxLen) return label;
            const words = label.split(' ');
            let lines = [];
            let currentLine = '';
            words.forEach(word => {
                if ((currentLine + word).length <= maxLen) {
                    currentLine += (currentLine === '' ? '' : ' ') + word;
                } else {
                    lines.push(currentLine);
                    currentLine = word;
                }
            });
            lines.push(currentLine);
            return lines;
        }

        // Navigation Logic
        function showSection(sectionId) {
            sections.forEach(section => {
                section.classList.remove('active');
            });
            document.getElementById(sectionId).classList.add('active');

            navItems.forEach(item => {
                item.classList.remove('active');
                if (item.dataset.section === sectionId) {
                    item.classList.add('active');
                }
            });

            // Update charts when performance section is active
            if (sectionId === 'performance') {
                updatePerformanceCharts();
            }
        }

        navItems.forEach(item => {
            item.addEventListener('click', () => {
                showSection(item.dataset.section);
            });
        });

        // --- Correspondence Management ---
        function renderCorrespondenceTable() {
            correspondenceTableBody.innerHTML = '';
            const filterDirectorate = correspondenceFilterDirectorateSelect.value;
            const filterStatus = correspondenceFilterStatusSelect.value;
            const searchTerm = correspondenceSearchInput.value.toLowerCase();

            const filteredData = correspondenceData.filter(item => {
                const matchesDirectorate = filterDirectorate === 'all' || item.directorate === filterDirectorate;
                const matchesStatus = filterStatus === 'all' || item.status === filterStatus;
                const matchesSearch = 
                    item.subject.toLowerCase().includes(searchTerm) || 
                    item.senderReceiver.toLowerCase().includes(searchTerm) ||
                    (item.bookNumber && item.bookNumber.toLowerCase().includes(searchTerm)) ||
                    item.date.includes(searchTerm); // Date is already a string in 'YYYY-MM-DD' format

                return matchesDirectorate && matchesStatus && matchesSearch;
            });

            if (filteredData.length === 0) {
                correspondenceTableBody.innerHTML = `<tr><td colspan="8" class="text-center text-gray-500">لا توجد مراسلات لعرضها في هذه التصفية.</td></tr>`;
                return;
            }

            filteredData.forEach(item => {
                const row = correspondenceTableBody.insertRow();
                row.innerHTML = `
                    <td>${item.id.substring(0, 8)}...</td>
                    <td>${item.bookNumber || 'غير متوفر'}</td>
                    <td>${item.senderReceiver}</td>
                    <td>${item.date}</td>
                    <td>${item.subject}</td>
                    <td>${item.directorate}</td>
                    <td>${item.status}</td>
                    <td class="actions-cell">
                        <button class="btn btn-secondary text-sm" onclick="editCorrespondence('${item.id}')">تعديل</button>
                        <button class="btn btn-danger text-sm" onclick="deleteCorrespondence('${item.id}')">حذف</button>
                    </td>
                `;
            });
        }

        correspondenceForm.addEventListener('submit', (event) => {
            event.preventDefault();
            const newItem = {
                id: currentCorrespondenceEditId || crypto.randomUUID(),
                senderReceiver: correspondenceSenderReceiverInput.value.trim(),
                bookNumber: correspondenceBookNumberInput.value.trim(), // Capture new field
                date: correspondenceDateInput.value,
                subject: correspondenceSubjectInput.value.trim(),
                directorate: correspondenceDirectorateSelect.value,
                status: correspondenceStatusSelect.value,
                expectedDate: correspondenceExpectedDateInput.value,
                notes: correspondenceNotesInput.value.trim()
            };

            if (currentCorrespondenceEditId) {
                const index = correspondenceData.findIndex(item => item.id === currentCorrespondenceEditId);
                if (index !== -1) {
                    correspondenceData[index] = newItem;
                    showMessage('تم تحديث المراسلة بنجاح.');
                }
                currentCorrespondenceEditId = null;
                correspondenceForm.querySelector('button[type="submit"]').textContent = 'إضافة مراسلة';
            } else {
                correspondenceData.push(newItem);
                showMessage('تم إضافة مراسلة جديدة بنجاح.');
            }
            correspondenceForm.reset();
            renderCorrespondenceTable();
            updateDashboardStats();
        });

        window.editCorrespondence = (id) => {
            console.log('editCorrespondence called for ID:', id); // Debugging log
            const itemToEdit = correspondenceData.find(item => item.id === id);
            if (itemToEdit) {
                showSection('correspondence'); // Ensure the correspondence section is active
                console.log('Correspondence section activated.');

                currentCorrespondenceEditId = id;
                correspondenceIdInput.value = itemToEdit.id;
                correspondenceSenderReceiverInput.value = itemToEdit.senderReceiver;
                correspondenceBookNumberInput.value = itemToEdit.bookNumber || ''; // Populate new field
                correspondenceDateInput.value = itemToEdit.date;
                correspondenceSubjectInput.value = itemToEdit.subject;
                correspondenceDirectorateSelect.value = itemToEdit.directorate;
                correspondenceStatusSelect.value = itemToEdit.status;
                correspondenceExpectedDateInput.value = itemToEdit.expectedDate || '';
                correspondenceNotesInput.value = itemToEdit.notes || '';
                
                const submitBtn = correspondenceForm.querySelector('button[type="submit"]');
                submitBtn.textContent = 'تعديل المراسلة';
                console.log('Correspondence submit button text changed to:', submitBtn.textContent);
                showMessage('يمكنك الآن تعديل بيانات المراسلة.');

                correspondenceForm.scrollIntoView({ behavior: 'smooth', block: 'start' });
                console.log('Scrolled to correspondence form.');
            } else {
                console.log('Correspondence item not found for ID:', id);
            }
        };

        window.deleteCorrespondence = (id) => {
            showCustomConfirm('هل أنت متأكد أنك تريد حذف هذه المراسلة؟', () => {
                correspondenceData = correspondenceData.filter(item => item.id !== id);
                showMessage('تم حذف المراسلة بنجاح.');
                renderCorrespondenceTable();
                updateDashboardStats();
                if (currentCorrespondenceEditId === id) {
                    correspondenceForm.reset();
                    currentCorrespondenceEditId = null;
                    correspondenceForm.querySelector('button[type="submit"]').textContent = 'إضافة مراسلة';
                }
            });
        };

        correspondenceFilterDirectorateSelect.addEventListener('change', renderCorrespondenceTable);
        correspondenceFilterStatusSelect.addEventListener('change', renderCorrespondenceTable);
        correspondenceSearchInput.addEventListener('input', renderCorrespondenceTable);

        // --- Circulars Management ---
        function renderCircularsTable() {
            circularsTableBody.innerHTML = '';
            const filterDirectorate = circularFilterDirectorateSelect.value;
            const filterStatus = circularFilterStatusSelect.value;
            const searchTerm = circularSearchInput.value.toLowerCase();

            const filteredData = circularsData.filter(item => {
                const matchesDirectorate = filterDirectorate === 'all' || item.targetDirectorates.includes(filterDirectorate);
                const matchesStatus = filterStatus === 'all' || item.status === filterStatus;
                const matchesSearch = 
                    item.subject.toLowerCase().includes(searchTerm) ||
                    (item.bookNumber && item.bookNumber.toLowerCase().includes(searchTerm)) ||
                    item.issueDate.includes(searchTerm) ||
                    item.targetDirectorates.some(dir => dir.toLowerCase().includes(searchTerm)); // Search within target directorates

                return matchesDirectorate && matchesStatus && matchesSearch;
            });

            if (filteredData.length === 0) {
                circularsTableBody.innerHTML = `<tr><td colspan="7" class="text-center text-gray-500">لا توجد إعمامات لعرضها في هذه التصفية.</td></tr>`;
                return;
            }

            filteredData.forEach(item => {
                const row = circularsTableBody.insertRow();
                row.innerHTML = `
                    <td>${item.id.substring(0, 8)}...</td>
                    <td>${item.bookNumber || 'غير متوفر'}</td>
                    <td>${item.issueDate}</td>
                    <td>${item.subject}</td>
                    <td>${item.targetDirectorates.join(', ')}</td>
                    <td>${item.status}</td>
                    <td class="actions-cell">
                        <button class="btn btn-secondary text-sm" onclick="editCircular('${item.id}')">تعديل</button>
                        <button class="btn btn-danger text-sm" onclick="deleteCircular('${item.id}')">حذف</button>
                    </td>
                `;
            });
        }

        circularsForm.addEventListener('submit', (event) => {
            event.preventDefault();
            const selectedDirectorates = Array.from(circularTargetDirectoratesSelect.selectedOptions).map(option => option.value);
            const newItem = {
                id: currentCircularEditId || crypto.randomUUID(),
                issueDate: circularIssueDateInput.value,
                bookNumber: circularBookNumberInput.value.trim(), // Capture new field
                subject: circularSubjectInput.value.trim(),
                targetDirectorates: selectedDirectorates,
                status: circularStatusSelect.value,
                expectedDate: circularExpectedDateInput.value,
                notes: circularNotesInput.value.trim()
            };

            if (currentCircularEditId) {
                const index = circularsData.findIndex(item => item.id === currentCircularEditId);
                if (index !== -1) {
                    circularsData[index] = newItem;
                    showMessage('تم تحديث الإعمام بنجاح.');
                }
                currentCircularEditId = null;
                circularsForm.querySelector('button[type="submit"]').textContent = 'إضافة إعمام';
            } else {
                circularsData.push(newItem);
                showMessage('تم إضافة إعمام جديد بنجاح.');
            }
            circularsForm.reset();
            renderCircularsTable();
            updateDashboardStats();
        });

        window.editCircular = (id) => {
            console.log('editCircular called for ID:', id); // Debugging log
            const itemToEdit = circularsData.find(item => item.id === id);
            if (itemToEdit) {
                showSection('circulars'); // Ensure the circulars section is active
                console.log('Circulars section activated.');

                currentCircularEditId = id;
                circularIdInput.value = itemToEdit.id;
                circularIssueDateInput.value = itemToEdit.issueDate;
                circularBookNumberInput.value = itemToEdit.bookNumber || ''; // Populate new field
                circularSubjectInput.value = itemToEdit.subject;
                // Select multiple options
                Array.from(circularTargetDirectoratesSelect.options).forEach(option => {
                    option.selected = itemToEdit.targetDirectorates.includes(option.value);
                });
                circularStatusSelect.value = itemToEdit.status;
                circularExpectedDateInput.value = itemToEdit.expectedDate || '';
                circularNotesInput.value = itemToEdit.notes || '';
                
                const submitBtn = circularsForm.querySelector('button[type="submit"]');
                submitBtn.textContent = 'تعديل الإعمام';
                console.log('Circular submit button text changed to:', submitBtn.textContent);
                showMessage('يمكنك الآن تعديل بيانات الإعمام.');

                circularsForm.scrollIntoView({ behavior: 'smooth', block: 'start' });
                console.log('Scrolled to circulars form.');
            } else {
                console.log('Circular item not found for ID:', id);
            }
        };

        window.deleteCircular = (id) => {
            showCustomConfirm('هل أنت متأكد أنك تريد حذف هذا الإعمام؟', () => {
                circularsData = circularsData.filter(item => item.id !== id);
                showMessage('تم حذف الإعمام بنجاح.');
                renderCircularsTable();
                updateDashboardStats();
                if (currentCircularEditId === id) {
                    circularsForm.reset();
                    currentCircularEditId = null;
                    circularsForm.querySelector('button[type="submit"]').textContent = 'إضافة إعمام';
                }
            });
        };

        circularFilterDirectorateSelect.addEventListener('change', renderCircularsTable);
        circularFilterStatusSelect.addEventListener('change', renderCircularsTable);
        circularSearchInput.addEventListener('input', renderCircularsTable);

        // --- Activities Management ---
        function renderActivitiesTable() {
            activitiesTableBody.innerHTML = '';
            const filterDirectorate = activityFilterDirectorateSelect.value;
            const filterStatus = activityFilterStatusSelect.value;
            const searchTerm = activitySearchInput.value.toLowerCase();

            const filteredData = activitiesData.filter(item => {
                const matchesDirectorate = filterDirectorate === 'all' || item.directorate === filterDirectorate;
                const matchesStatus = filterStatus === 'all' || item.status === filterStatus;
                const matchesSearch = 
                    item.name.toLowerCase().includes(searchTerm) ||
                    item.startDate.includes(searchTerm) ||
                    item.directorate.toLowerCase().includes(searchTerm); // Search by directorate

                return matchesDirectorate && matchesStatus && matchesSearch;
            });

            if (filteredData.length === 0) {
                activitiesTableBody.innerHTML = `<tr><td colspan="7" class="text-center text-gray-500">لا توجد نشاطات لعرضها في هذه التصفية.</td></tr>`;
                return;
            }

            filteredData.forEach(item => {
                const row = activitiesTableBody.insertRow();
                row.innerHTML = `
                    <td>${item.id.substring(0, 8)}...</td>
                    <td>${item.name}</td>
                    <td>${item.startDate}</td>
                    <td>${item.directorate}</td>
                    <td>${item.status}</td>
                    <td>${item.budget ? item.budget.toLocaleString('ar-IQ') + ' د.ع' : 'غير محدد'}</td>
                    <td class="actions-cell">
                        <button class="btn btn-secondary text-sm" onclick="editActivity('${item.id}')">تعديل</button>
                        <button class="btn btn-danger text-sm" onclick="deleteActivity('${item.id}')">حذف</button>
                    </td>
                `;
            });
        }

        activitiesForm.addEventListener('submit', (event) => {
            event.preventDefault();
            const newItem = {
                id: currentActivityEditId || crypto.randomUUID(),
                name: activityNameInput.value.trim(),
                startDate: activityStartDateInput.value,
                endDate: activityEndDateInput.value,
                directorate: activityDirectorateSelect.value,
                status: activityStatusSelect.value,
                budget: parseFloat(activityBudgetInput.value) || 0,
                notes: activityNotesInput.value.trim()
            };

            if (currentActivityEditId) {
                const index = activitiesData.findIndex(item => item.id === currentActivityEditId);
                if (index !== -1) {
                    activitiesData[index] = newItem;
                    showMessage('تم تحديث النشاط بنجاح.');
                }
                currentActivityEditId = null;
                activitiesForm.querySelector('button[type="submit"]').textContent = 'إضافة نشاط';
            } else {
                activitiesData.push(newItem);
                showMessage('تم إضافة نشاط جديد بنجاح.');
            }
            activitiesForm.reset();
            renderActivitiesTable();
            updateDashboardStats();
        });

        window.editActivity = (id) => {
            console.log('editActivity called for ID:', id); // Debugging log
            const itemToEdit = activitiesData.find(item => item.id === id);
            if (itemToEdit) {
                showSection('activities'); // Ensure the activities section is active
                console.log('Activities section activated.');

                currentActivityEditId = id;
                activityIdInput.value = itemToEdit.id;
                activityNameInput.value = itemToEdit.name;
                activityStartDateInput.value = itemToEdit.startDate;
                activityEndDateInput.value = itemToEdit.endDate || '';
                activityDirectorateSelect.value = itemToEdit.directorate;
                activityStatusSelect.value = itemToEdit.status;
                activityBudgetInput.value = itemToEdit.budget !== 0 && !isNaN(itemToEdit.budget) ? itemToEdit.budget : '';
                activityNotesInput.value = itemToEdit.notes || '';
                
                const submitBtn = activitiesForm.querySelector('button[type="submit"]');
                submitBtn.textContent = 'تعديل النشاط';
                console.log('Activity submit button text changed to:', submitBtn.textContent);
                showMessage('يمكنك الآن تعديل بيانات النشاط.');

                activitiesForm.scrollIntoView({ behavior: 'smooth', block: 'start' });
                console.log('Scrolled to activities form.');
            } else {
                console.log('Activity item not found for ID:', id);
            }
        };

        window.deleteActivity = (id) => {
            showCustomConfirm('هل أنت متأكد أنك تريد حذف هذا النشاط؟', () => {
                activitiesData = activitiesData.filter(item => item.id !== id);
                showMessage('تم حذف النشاط بنجاح.');
                renderActivitiesTable();
                updateDashboardStats();
                if (currentActivityEditId === id) {
                    activitiesForm.reset();
                    currentActivityEditId = null;
                    activitiesForm.querySelector('button[type="submit"]').textContent = 'إضافة نشاط';
                }
            });
        };

        activityFilterDirectorateSelect.addEventListener('change', renderActivitiesTable);
        activityFilterStatusSelect.addEventListener('change', renderActivitiesTable);
        activitySearchInput.addEventListener('input', renderActivitiesTable);

        // --- Dashboard & Performance Evaluation ---
        function updateDashboardStats() {
            totalCorrespondenceDisplay.textContent = correspondenceData.length;
            completedActivitiesDisplay.textContent = activitiesData.filter(a => a.status === 'مكتمل').length;
            pendingItemsDisplay.textContent = correspondenceData.filter(c => c.status === 'قيد التنفيذ').length +
                                                circularsData.filter(c => c.status === 'قيد التنفيذ').length +
                                                activitiesData.filter(a => a.status === 'قيد التنفيذ').length;
        }

        function updatePerformanceCharts() {
            // Chart 1: Correspondence Status by Directorate
            const directorates = ['البصرة', 'ميسان', 'ذي قار'];
            const correspondenceStatusCounts = {
                'البصرة': { 'قيد التنفيذ': 0, 'تم الإنجاز': 0, 'مؤجل': 0, 'ملغى': 0 },
                'ميسان': { 'قيد التنفيذ': 0, 'تم الإنجاز': 0, 'مؤجل': 0, 'ملغى': 0 },
                'ذي قار': { 'قيد التنفيذ': 0, 'تم الإنجاز': 0, 'مؤجل': 0, 'ملغى': 0 }
            };

            correspondenceData.forEach(item => {
                if (correspondenceStatusCounts[item.directorate]) {
                    correspondenceStatusCounts[item.directorate][item.status]++;
                }
            });

            const statusLabels = ['قيد التنفيذ', 'تم الإنجاز', 'مؤجل', 'ملغى'];
            const datasets = statusLabels.map((status, index) => ({
                label: status,
                data: directorates.map(dir => correspondenceStatusCounts[dir][status]),
                backgroundColor: [
                    'rgba(255, 159, 64, 0.8)', // Pending - Orange
                    'rgba(75, 192, 192, 0.8)', // Completed - Green
                    'rgba(255, 205, 86, 0.8)', // Deferred - Yellow
                    'rgba(201, 203, 207, 0.8)'  // Cancelled - Grey
                ][index],
                borderColor: [
                    'rgb(255, 159, 64)',
                    'rgb(75, 192, 192)',
                    'rgb(255, 205, 86)',
                    'rgb(201, 203, 207)'
                ][index],
                borderWidth: 1
            }));

            if (correspondenceStatusChartInstance) {
                correspondenceStatusChartInstance.destroy();
            }
            const ctx1 = document.getElementById('correspondenceStatusChart').getContext('2d');
            correspondenceStatusChartInstance = new Chart(ctx1, {
                type: 'bar',
                data: {
                    labels: directorates,
                    datasets: datasets
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: false,
                            text: 'حالة المراسلات حسب المديرية'
                        },
                        tooltip: {
                            callbacks: {
                                title: function(context) {
                                    return wrapLabels(context[0].label);
                                },
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.y !== null) {
                                        label += context.parsed.y;
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            stacked: true,
                            title: {
                                display: true,
                                text: 'المديرية'
                            }
                        },
                        y: {
                            stacked: true,
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'العدد'
                            }
                        }
                    }
                }
            });

            // Chart 2: Activities Completion (Planned vs. Completed)
            const plannedActivities = {};
            const completedActivities = {};
            directorates.forEach(dir => {
                plannedActivities[dir] = activitiesData.filter(a => a.directorate === dir).length;
                completedActivities[dir] = activitiesData.filter(a => a.directorate === dir && a.status === 'مكتمل').length;
            });

            if (activitiesCompletionChartInstance) {
                activitiesCompletionChartInstance.destroy();
            }
            const ctx2 = document.getElementById('activitiesCompletionChart').getContext('2d');
            activitiesCompletionChartInstance = new Chart(ctx2, {
                type: 'bar',
                data: {
                    labels: directorates,
                    datasets: [
                        {
                            label: 'النشاطات المخطط لها',
                            data: directorates.map(dir => plannedActivities[dir]),
                            backgroundColor: 'rgba(54, 162, 235, 0.8)', // Blue
                            borderColor: 'rgb(54, 162, 235)',
                            borderWidth: 1
                        },
                        {
                            label: 'النشاطات المكتملة',
                            data: directorates.map(dir => completedActivities[dir]),
                            backgroundColor: 'rgba(75, 192, 192, 0.8)', // Green
                            borderColor: 'rgb(75, 192, 192)',
                            borderWidth: 1
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: false,
                            text: 'النشاطات المكتملة مقابل المخطط لها'
                        },
                        tooltip: {
                            callbacks: {
                                title: function(context) {
                                    return wrapLabels(context[0].label);
                                },
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.y !== null) {
                                        label += context.parsed.y;
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            title: {
                                display: true,
                                text: 'المديرية'
                            }
                        },
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'العدد'
                            }
                        }
                    }
                }
            });

            // Chart 3: Endowment Value Distribution by Province (using existing endowmentsData)
            const endowmentProvinces = {};
            endowmentsData.forEach(endowment => {
                if (!endowmentProvinces[endowment.province]) {
                    endowmentProvinces[endowment.province] = 0;
                }
                endowmentProvinces[endowment.province] += endowment.value;
            });

            const endowmentLabels = Object.keys(endowmentProvinces);
            const endowmentValues = Object.values(endowmentProvinces);
            const backgroundColors = [
                'rgba(255, 99, 132, 0.8)', // Red
                'rgba(54, 162, 235, 0.8)', // Blue
                'rgba(255, 205, 86, 0.8)', // Yellow
                'rgba(75, 192, 192, 0.8)', // Green
                'rgba(153, 102, 255, 0.8)',// Purple
                'rgba(255, 159, 64, 0.8)'  // Orange
            ];
            const borderColors = [
                'rgb(255, 99, 132)',
                'rgb(54, 162, 235)',
                'rgb(255, 205, 86)',
                'rgb(75, 192, 192)',
                'rgb(153, 102, 255)',
                'rgb(255, 159, 64)'
            ];

            if (endowmentValueChartInstance) {
                endowmentValueChartInstance.destroy();
            }
            const ctx3 = document.getElementById('endowmentValueChart').getContext('2d');
            endowmentValueChartInstance = new Chart(ctx3, {
                type: 'doughnut',
                data: {
                    labels: endowmentLabels,
                    datasets: [{
                        label: 'قيمة الوقف (د.ع)',
                        data: endowmentValues,
                        backgroundColor: backgroundColors.slice(0, endowmentLabels.length),
                        borderColor: borderColors.slice(0, endowmentLabels.length),
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        title: {
                            display: false,
                            text: 'الاستجابة حسب المديرية'
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    let label = context.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed) {
                                        label += context.parsed.toLocaleString('ar-IQ') + ' د.ع';
                                    }
                                    return label;
                                }
                            }
                        }
                    }
                }
            });
        }

        // Initial setup on page load
        document.addEventListener('DOMContentLoaded', () => {
            // Populate with some dummy data for demonstration
            endowmentsData = [ // Dummy data for endowment value chart
                { province: 'البصرة', value: 150000000 },
                { province: 'ميسان', value: 80000000 },
                { province: 'ذي قار', value: 120000000 },
                { province: 'البصرة', value: 70000000 },
                { province: 'ميسان', value: 30000000 }
            ];
            correspondenceData = [
                { id: 'corr1', senderReceiver: 'وزارة المالية', bookNumber: '123/أ', date: '2025-07-01', subject: 'طلب تخصيص ميزانية', directorate: 'البصرة', status: 'قيد التنفيذ', expectedDate: '2025-07-15', notes: 'متابعة عاجلة' },
                { id: 'corr2', senderReceiver: 'مديرية الوقف الشيعي / ميسان', bookNumber: '456/ب', date: '2025-06-25', subject: 'تقرير نشاطات شهر يونيو', directorate: 'ميسان', status: 'تم الإنجاز', expectedDate: '2026-06-30', notes: '' },
                { id: 'corr3', senderReceiver: 'محافظة ذي قار', bookNumber: '789/ج', date: '2025-07-03', subject: 'استفسار عن عقار وقف', directorate: 'ذي قار', status: 'قيد التنفيذ', expectedDate: '2025-07-10', notes: 'يحتاج إلى زيارة ميدانية' }
            ];
            circularsData = [
                { id: 'circ1', issueDate: '2025-06-20', bookNumber: 'ع/2025/001', subject: 'تعميم بخصوص ضوابط الصرف', targetDirectorates: ['البصرة', 'ميسان'], status: 'صادر', expectedDate: '2025-07-01', notes: '' },
                { id: 'circ2', issueDate: '2025-07-01', bookNumber: 'ع/2025/002', subject: 'توجيهات بشأن الأعياد القادمة', targetDirectorates: ['البصرة', 'ميسان', 'ذي قار'], status: 'قيد التنفيذ', expectedDate: '2025-07-07', notes: 'متابعة مع المديريات' }
            ];
            activitiesData = [
                { id: 'act1', name: 'مشروع ترميم مسجد الإمام علي', startDate: '2025-05-01', endDate: '2025-07-30', directorate: 'البصرة', status: 'قيد التنفيذ', budget: 50000000, notes: 'المرحلة الأولى' },
                { id: 'act2', name: 'حملة تنظيف المقابر', startDate: '2025-06-10', endDate: '2025-06-15', directorate: 'ميسان', status: 'مكتمل', budget: 5000000, notes: '' },
                { id: 'act3', name: 'ورشة عمل لتطوير الكوادر', startDate: '2025-07-05', endDate: '2025-07-07', directorate: 'ذي قار', status: 'مخطط', budget: 10000000, notes: 'تحضير القاعة' }
            ];
            


            renderCorrespondenceTable();
            renderCircularsTable();
            renderActivitiesTable();
            updateDashboardStats();
            // Initial chart rendering will happen when performance section is first shown
            // or explicitly called here if needed for initial dashboard view
        });
    </script>
</body>
</html>
# raed
