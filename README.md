<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Eprocurement Unpad</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Library penunjang waktu untuk Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/date-fns@2.27.0/dist/date-fns.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-date-fns@2.0.0/dist/chartjs-adapter-date-fns.bundle.min.js"></script>
    <!-- Plugin untuk menampilkan label di dalam chart -->
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-datalabels@2.0.0"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        
        body { font-family: 'Inter', sans-serif; background-color: #f0f2f5; overflow-x: hidden; padding-top: 110px; }
        
        /* Layout Header Brand (Layer 1) */
        .brand-header { height: 50px; background: #f0f2f5; position: fixed; top: 0; left: 0; right: 0; z-index: 1100; display: flex; align-items: center; padding: 0 2rem; border-bottom: 1px solid #e2e8f0; }
        .brand { font-size: 1.1rem; font-weight: 800; color: #1e293b; display: flex; align-items: center; text-transform: uppercase; }
        .brand img { height: 42px; width: auto; margin-right: 12px; border-radius: 4px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); }

        /* Layout Menu Bar (Layer 2) */
        .menu-bar { height: 60px; background: #1e293b; position: fixed; top: 50px; left: 0; right: 0; z-index: 1000; display: flex; align-items: center; padding: 0 2rem; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); }
        .nav-container { display: flex; flex: 1; justify-content: flex-start; overflow-x: auto; scrollbar-width: none; }
        .nav-container::-webkit-scrollbar { display: none; }
        
        .nav-link { display: flex; align-items: center; padding: 0 15px; height: 60px; color: #94a3b8; transition: all 0.2s; border-bottom: 4px solid transparent; cursor: pointer; font-size: 0.75rem; font-weight: 600; text-transform: uppercase; white-space: nowrap; }
        .nav-link:hover { color: white; background: rgba(255,255,255,0.05); }
        .nav-link.active { color: white; border-bottom-color: #facc15; background: rgba(255,255,255,0.05); }
        .nav-link i { margin-right: 8px; }

        .main-content { padding: 20px; min-height: calc(100vh - 110px); transition: all 0.3s; }
        .glass-card { background: white; border-radius: 12px; border: 1px solid #e2e8f0; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); }
        
        .page-section { display: none; }
        .page-section.active { display: block; animation: fadeIn 0.4s ease-out; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .badge-card { border-left-width: 4px; padding: 1rem; display: flex; flex-direction: column; justify-content: center; min-height: 95px; }
        th { background-color: #f8fafc; font-size: 0.65rem; text-transform: uppercase; color: #64748b; padding: 12px 8px; border-bottom: 2px solid #edf2f7; font-weight: 700; }
        td { padding: 10px 8px; border-bottom: 1px solid #f1f5f9; font-size: 0.7rem; vertical-align: middle; }
        
        .chart-container { height: 260px; position: relative; }
        .chart-container-tall { height: 400px; position: relative; }
        
        .modal { display: none; position: fixed; z-index: 2000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        .modal.active { display: flex; }

        .sub-nav-btn { padding: 8px 16px; border-radius: 8px; font-size: 0.7rem; font-weight: 700; color: #64748b; transition: all 0.2s; border: 1px solid #e2e8f0; background: white; }
        .sub-nav-btn.active { background-color: #1e293b; color: white; border-color: #1e293b; }

        .star-rating { color: #fbbf24; }
    </style>
</head>
<body>

    <!-- BRAND BAR -->
    <header class="brand-header">
        <div class="brand">
            <img src="https://upload.wikimedia.org/wikipedia/id/6/62/Logo_Unpad.JPG?20160510033059" alt="Logo Unpad">
            Dashboard Eprocurement Unpad
        </div>
    </header>

    <!-- MENU BAR -->
    <nav class="menu-bar">
        <div class="nav-container">
            <div class="nav-link active" data-page="home" onclick="showPage('home', this)"><i class="fas fa-home"></i> Home</div>
            <div class="nav-link" data-page="rup" onclick="showPage('rup', this)"><i class="fas fa-clipboard-list"></i> Rencana Umum Pengadaan</div>
            <div class="nav-link" data-page="pr" onclick="showPage('pr', this)"><i class="fas fa-shopping-cart"></i> Requisitions</div>
            <div class="nav-link" data-page="po" onclick="showPage('po', this)"><i class="fas fa-file-contract"></i> Purchase Order</div>
            <div class="nav-link" data-page="rec" onclick="showPage('rec', this)"><i class="fas fa-box"></i> Receiving</div>
            <div class="nav-link" data-page="pay" onclick="showPage('pay', this)"><i class="fas fa-credit-card"></i> Payment</div>
            <div class="nav-link" data-page="monitoring" onclick="showPage('monitoring', this)"><i class="fas fa-tv"></i> Monitoring</div>
        </div>
    </nav>

    <div class="main-content">
        <!-- GLOBAL FILTERS -->
        <header class="glass-card p-4 mb-6 flex flex-col gap-4">
            <div class="flex flex-wrap gap-3 items-end w-full">
                <div class="flex-1 min-w-[100px]">
                    <label class="block text-[9px] font-bold text-gray-500 mb-1 uppercase">Tahun</label>
                    <select id="filterTahun" onchange="updateDashboard()" class="w-full border rounded p-1.5 text-xs bg-gray-50 focus:outline-blue-500">
                        <option value="Semua">Semua Tahun</option>
                        <option value="2024">2024</option>
                        <option value="2025">2025</option>
                    </select>
                </div>
                <div class="flex-1 min-w-[200px]">
                    <label class="block text-[9px] font-bold text-gray-500 mb-1 uppercase">Unit Kerja</label>
                    <select id="filterUnit" onchange="updateDashboard()" class="w-full border rounded p-1.5 text-xs bg-gray-50 focus:outline-blue-500">
                        <option value="Semua">Semua Unit Kerja</option>
                        <option id="unitListOptions"></option>
                    </select>
                </div>
                <div class="flex-1 min-w-[150px]">
                    <label class="block text-[9px] font-bold text-gray-500 mb-1 uppercase">Metode Pengadaan</label>
                    <select id="filterMetode" onchange="updateDashboard()" class="w-full border rounded p-1.5 text-xs bg-gray-50 focus:outline-blue-500">
                        <option value="Semua">Semua Metode</option>
                        <option value="e-Purchasing">e-Purchasing</option>
                        <option value="Tender">Tender</option>
                        <option value="Pengadaan Langsung">Pengadaan Langsung</option>
                    </select>
                </div>
                <div class="flex-none">
                    <button onclick="resetFilters()" class="bg-gray-100 text-gray-600 px-4 py-1.5 rounded text-xs font-bold hover:bg-gray-200 border">RESET</button>
                </div>
            </div>
            <div class="w-full">
                <label class="block text-[9px] font-bold text-gray-500 mb-1 uppercase">Nama Paket Pekerjaan</label>
                <div class="relative">
                    <input type="text" id="filterSearch" oninput="updateDashboard()" placeholder="Ketik nama paket untuk menyaring data..." class="w-full border rounded p-2 text-xs bg-gray-50 pl-9 focus:ring-1 focus:ring-blue-500 focus:outline-none">
                    <i class="fas fa-search absolute left-3 top-2.5 text-gray-400 text-sm"></i>
                </div>
            </div>
        </header>

        <!-- PAGE: HOME -->
        <div id="page-home" class="page-section active">
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-3 mb-6">
                <div class="glass-card badge-card border-blue-500"><p class="text-[9px] font-bold text-gray-400 uppercase">Pagu RUP</p><h4 id="badgeRupQty" class="text-lg font-bold">0 Pkt</h4><p id="badgeRupVal" class="text-[11px] text-blue-600 font-bold card-value">Rp 0</p></div>
                <div class="glass-card badge-card border-indigo-500"><p class="text-[9px] font-bold text-gray-400 uppercase">Requisitions</p><h4 id="badgePrQty" class="text-lg font-bold">0 Pkt</h4><p id="badgePrVal" class="text-[11px] text-indigo-600 font-bold card-value">Rp 0</p></div>
                <div class="glass-card badge-card border-emerald-500"><p class="text-[9px] font-bold text-gray-400 uppercase">PO</p><h4 id="badgePoQty" class="text-lg font-bold">0 Pkt</h4><p id="badgePoVal" class="text-[11px] text-emerald-600 font-bold card-value">Rp 0</p></div>
                <div class="glass-card badge-card border-amber-500"><p class="text-[9px] font-bold text-gray-400 uppercase">REC</p><h4 id="badgeRecQty" class="text-lg font-bold">0 Pkt</h4><p id="badgeRecVal" class="text-[11px] text-amber-600 font-bold card-value">Rp 0</p></div>
                <div class="glass-card badge-card border-purple-500"><p class="text-[9px] font-bold text-gray-400 uppercase">PAY</p><h4 id="badgePayQty" class="text-lg font-bold">0 Pkt</h4><p id="badgePayVal" class="text-[11px] text-purple-600 font-bold card-value">Rp 0</p></div>
                <div class="glass-card badge-card border-green-600 bg-green-50"><p class="text-[9px] font-bold text-green-700 uppercase">Saving</p><h4 id="badgeSavingsVal" class="text-lg font-bold text-green-800 card-value">Rp 0</h4><p id="badgeSavingsPct" class="text-[10px] text-green-600 font-bold">0% Saving</p></div>
            </div>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <div class="glass-card p-4"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">1. Jenis Pengadaan</h3><div class="chart-container"><canvas id="chartJenis"></canvas></div></div>
                <div class="glass-card p-4"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">2. Metode Pengadaan</h3><div class="chart-container"><canvas id="chartMetode"></canvas></div></div>
                <div class="glass-card p-4"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">3. Usulan Paket per Unit Kerja (Pie)</h3><div class="chart-container"><canvas id="chartUnitUsulan"></canvas></div></div>
                <div class="glass-card p-4"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">4. Progres Requisitions tiap Unit</h3><div class="chart-container"><canvas id="chartUnitProgres"></canvas></div></div>
            </div>
            <div class="glass-card p-5">
                <h3 class="text-xs font-bold text-gray-600 mb-4 uppercase text-center">Rekapitulasi Paket Pekerjaan berdasarkan Unit Kerja</h3>
                <div class="overflow-x-auto">
                    <table class="w-full text-left">
                        <thead>
                            <tr>
                                <th rowspan="2" class="min-w-[180px] border-r">Unit Kerja</th>
                                <th colspan="2" class="text-center border-b border-r bg-indigo-50">Requisition</th>
                                <th colspan="2" class="text-center border-b border-r bg-emerald-50">Purchase Order</th>
                                <th colspan="2" class="text-center border-b border-r bg-amber-50">Receive</th>
                                <th colspan="2" class="text-center border-b border-r bg-purple-50">Payment</th>
                                <th rowspan="2" class="text-center bg-green-50">Efisiensi</th>
                            </tr>
                            <tr>
                                <th class="text-center border-r bg-indigo-50">Jml</th><th class="text-center border-r bg-indigo-50">Nilai</th>
                                <th class="text-center border-r bg-emerald-50">Jml</th><th class="text-center border-r bg-emerald-50">Nilai</th>
                                <th class="text-center border-r bg-amber-50">Jml</th><th class="text-center border-r bg-amber-50">Nilai</th>
                                <th class="text-center border-r bg-purple-50">Jml</th><th class="text-center border-r bg-purple-50">Nilai</th>
                            </tr>
                        </thead>
                        <tbody id="unitTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <div id="page-rup" class="page-section">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6"><div class="glass-card p-4 border-l-4 border-blue-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Jumlah Usulan RUP</p><h2 id="pageRupQty" class="text-xl font-bold text-blue-700">0 Paket</h2></div><div class="glass-card p-4 border-l-4 border-blue-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Total Nilai Pagu RUP</p><h2 id="pageRupVal" class="text-xl font-bold text-blue-700">Rp 0</h2></div></div>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">Rekapitulasi RUP per Unit Kerja</h3><div class="chart-container-tall"><canvas id="chartRupUnit"></canvas></div></div>
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase text-center">Progres Usulan: Sudah diajukan vs Belum diajukan</h3><div class="chart-container flex items-center justify-center" style="height: 350px;"><canvas id="chartRupProgres"></canvas></div></div>
            </div>
            <div class="glass-card overflow-hidden">
                <div class="p-4 bg-slate-50 border-b flex justify-between items-center"><h4 class="text-xs font-bold text-slate-600 uppercase">Tabel Rekapitulasi Rencana Umum Pengadaan</h4></div>
                <div class="overflow-x-auto"><table class="w-full text-left"><thead><tr><th class="text-center">Tahun</th><th>Nama Unit Kerja</th><th class="text-center">Jumlah Paket</th><th class="text-right">Total Nilai Paket</th></tr></thead><tbody id="rupSummaryTableBody"></tbody></table></div>
            </div>
        </div>

        <div id="page-pr" class="page-section">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6"><div class="glass-card p-4 border-l-4 border-indigo-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Jumlah Requisition</p><h2 id="pagePrQty" class="text-xl font-bold text-indigo-700">0 Paket</h2></div><div class="glass-card p-4 border-l-4 border-indigo-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Total Nilai Requisition</p><h2 id="pagePrVal" class="text-xl font-bold text-indigo-700">Rp 0</h2></div></div>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">Rekapitulasi Requisitions per Unit Kerja</h3><div class="chart-container-tall"><canvas id="chartPrUnit"></canvas></div></div>
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase text-center">Progres Requisitions: Sudah diajukan (PO) vs Belum</h3><div class="chart-container flex items-center justify-center" style="height: 350px;"><canvas id="chartPrProgres"></canvas></div></div>
            </div>
            <div class="glass-card overflow-hidden">
                <div class="p-4 bg-slate-50 border-b flex justify-between items-center"><h4 class="text-xs font-bold text-slate-600 uppercase">Tabel Rekapitulasi Requisitions</h4></div>
                <div class="overflow-x-auto"><table class="w-full text-left"><thead><tr><th>Nama Paket Pekerjaan</th><th class="text-center">Nomor Requisitions</th><th class="text-center">Tanggal Requisitions</th><th class="text-right">Nilai Requisitions</th><th>Unit Kerja</th><th>Metode</th><th class="text-center">Status</th></tr></thead><tbody id="prPackageTableBody"></tbody></table></div>
            </div>
        </div>

        <div id="page-po" class="page-section">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6"><div class="glass-card p-4 border-l-4 border-emerald-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Jumlah PO Terbit</p><h2 id="pagePoQty" class="text-xl font-bold text-emerald-700">0 Paket</h2></div><div class="glass-card p-4 border-l-4 border-emerald-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Total Nilai Kontrak (PO)</p><h2 id="pagePoVal" class="text-xl font-bold text-emerald-700">Rp 0</h2></div></div>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">Rekapitulasi Purchase Order per Unit Kerja</h3><div class="chart-container-tall"><canvas id="chartPoUnit"></canvas></div></div>
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase text-center">Progres Kontrak: Lanjut vs Belum</h3><div class="chart-container flex items-center justify-center" style="height: 350px;"><canvas id="chartPoProgres"></canvas></div></div>
            </div>
            <div class="glass-card overflow-hidden">
                <div class="p-4 bg-slate-50 border-b flex justify-between items-center"><h4 class="text-xs font-bold text-slate-600 uppercase">Tabel Rekapitulasi Purchase Order</h4></div>
                <div class="overflow-x-auto"><table class="w-full text-left"><thead><tr><th>Nama Paket Pekerjaan</th><th class="text-center">Nomor PO</th><th class="text-center">Tanggal PO</th><th class="text-right">Nilai Kontrak</th><th>Penyedia</th><th class="text-center">Status</th></tr></thead><tbody id="poPackageTableBody"></tbody></table></div>
            </div>
        </div>

        <div id="page-rec" class="page-section">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
                <div class="glass-card p-4 border-l-4 border-amber-500"><p class="text-[10px] font-bold text-gray-500 uppercase">Jumlah Paket Diterima</p><h2 id="pageRecQty" class="text-xl font-bold text-amber-700">0 Paket</h2></div>
                <div class="glass-card p-4 border-l-4 border-amber-500"><p class="text-[10px] font-bold text-gray-500 uppercase">Total Nilai Barang Diterima</p><h2 id="pageRecVal" class="text-xl font-bold text-amber-700">Rp 0</h2></div>
            </div>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">Rekapitulasi Receiving per Unit Kerja</h3><div class="chart-container-tall"><canvas id="chartRecUnit"></canvas></div></div>
                <div class="glass-card p-5"><h3 class="text-xs font-bold text-gray-600 mb-4 uppercase text-center">Progres Fisik: Selesai vs Dalam Proses</h3><div class="chart-container flex items-center justify-center" style="height: 350px;"><canvas id="chartRecProgres"></canvas></div></div>
            </div>
            <div class="glass-card overflow-hidden">
                <div class="p-4 bg-slate-50 border-b flex justify-between items-center"><h4 class="text-xs font-bold text-slate-600 uppercase">Tabel Rekapitulasi Receiving (Penerimaan)</h4></div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left">
                        <thead>
                            <tr class="bg-gray-50">
                                <th>Nama Paket</th>
                                <th class="text-center">Nomor Receive</th>
                                <th class="text-center">Tanggal Receive</th>
                                <th class="text-right bg-emerald-50">Volume & Nilai (Sudah)</th>
                                <th class="text-right bg-amber-50">Volume & Nilai (Sisa)</th>
                                <th class="text-center">Progres Pekerjaan</th>
                            </tr>
                        </thead>
                        <tbody id="recPackageTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- PAGE: PAYMENT (PERFECTED) -->
        <div id="page-pay" class="page-section">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
                <div class="glass-card p-4 border-l-4 border-purple-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Jumlah Paket Terbayar</p><h2 id="pagePayQty" class="text-xl font-bold text-purple-700">0 Paket</h2></div>
                <div class="glass-card p-4 border-l-4 border-purple-600"><p class="text-[10px] font-bold text-gray-500 uppercase">Total Nilai Pembayaran</p><h2 id="pagePayVal" class="text-xl font-bold text-purple-700">Rp 0</h2></div>
            </div>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                <div class="glass-card p-5">
                    <h3 class="text-xs font-bold text-gray-600 mb-4 uppercase">Rekapitulasi Pembayaran per Unit Kerja</h3>
                    <div class="chart-container-tall"><canvas id="chartPayUnit"></canvas></div>
                </div>
                <div class="glass-card p-5">
                    <h3 class="text-xs font-bold text-gray-600 mb-4 uppercase text-center">Progres Keuangan: Terbayar vs Belum</h3>
                    <div class="chart-container flex items-center justify-center" style="height: 350px;">
                        <canvas id="chartPayProgres"></canvas>
                    </div>
                </div>
            </div>
            <div class="glass-card overflow-hidden">
                <div class="p-4 bg-slate-50 border-b flex justify-between items-center"><h4 class="text-xs font-bold text-slate-600 uppercase">Tabel Rekapitulasi Pembayaran (Payment)</h4></div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left">
                        <thead>
                            <tr>
                                <th>Nama Paket Pekerjaan</th>
                                <th class="text-center">Nomor Payment</th>
                                <th class="text-center">Tanggal Payment</th>
                                <th class="text-right">Nilai Pembayaran</th>
                                <th>Penyedia</th>
                                <th>Metode</th>
                                <th class="text-center">Status</th>
                            </tr>
                        </thead>
                        <tbody id="payPackageTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <div id="page-monitoring" class="page-section">
            <div class="glass-card p-6 mb-6"><h3 class="text-sm font-bold text-gray-600 mb-4 text-center uppercase">Tracking Timeline Capaian Paket</h3><select id="monPaketSelector" onchange="updatePaketTimeline()" class="w-full border rounded p-2 text-sm bg-gray-50 mb-6"></select><div class="chart-container" style="height: 350px;"><canvas id="chartTimelinePaket"></canvas></div></div>
            <div class="glass-card overflow-hidden"><div class="overflow-x-auto"><table class="w-full text-left"><thead><tr><th>Nama Paket</th><th>Kontrak</th><th class="text-center">Eff %</th><th>Stage</th><th class="text-center">Days</th><th class="text-center">SLA</th><th>Aksi</th></tr></thead><tbody id="monPaketTable"></tbody></table></div></div>
        </div>
    </div>

    <!-- MODALS -->
    <div id="detailModal" class="modal">
        <div class="glass-card w-11/12 max-w-5xl overflow-hidden shadow-2xl animate-fadeIn">
            <div class="p-4 border-b flex justify-between items-center bg-gray-50"><h3 class="font-bold text-xs" id="modalTitle">Rincian Paket</h3><button onclick="closeModal('detailModal')"><i class="fas fa-times"></i></button></div>
            <div class="p-4">
                <div class="overflow-x-auto">
                    <table class="w-full text-[11px] text-left border">
                        <thead id="modalThead"></thead>
                        <tbody id="modalTableBody"></tbody>
                    </table>
                </div>
                <div class="flex justify-between items-center border-t pt-4 mt-2">
                    <p class="text-xs font-bold text-gray-500 uppercase">Total Nilai Paket:</p>
                    <h3 id="modalTotal" class="text-lg font-bold text-blue-600">Rp 0</h3>
                </div>
                <div class="flex justify-end mt-4"><button onclick="closeModal('detailModal')" class="bg-gray-200 px-6 py-2 rounded text-xs font-bold uppercase">TUTUP</button></div>
            </div>
        </div>
    </div>

    <div id="rupUnitDetailModal" class="modal">
        <div class="glass-card w-11/12 max-w-[95%] overflow-hidden shadow-2xl"><div class="p-4 border-b flex justify-between items-center bg-blue-700 text-white"><h3 class="font-bold uppercase text-xs" id="rupUnitModalTitle">Rincian Paket RUP</h3><button onclick="closeModal('rupUnitDetailModal')"><i class="fas fa-times"></i></button></div><div class="p-4 bg-slate-50"><div class="overflow-x-auto max-h-[500px]"><table class="w-full text-[9px] text-left border bg-white"><thead><tr class="bg-gray-100"><th class="border p-2">Nama Paket</th><th class="border p-2 text-right">Nilai Paket</th><th class="border p-2">Metode</th><th class="border p-2">Jenis</th><th class="border p-2">Lokasi</th><th class="border p-2 text-center">Negosiasi</th><th class="border p-2 text-center">Status</th></tr></thead><tbody id="rupUnitModalTableBody"></tbody></table></div><div class="flex justify-end mt-4"><button onclick="closeModal('rupUnitDetailModal')" class="bg-gray-200 px-6 py-2 rounded text-xs font-bold uppercase hover:bg-gray-300">TUTUP</button></div></div></div>
    </div>

    <div id="unitDetailModal" class="modal">
        <div class="glass-card w-11/12 max-w-[95%] overflow-hidden shadow-2xl"><div class="p-4 border-b flex justify-between items-center bg-blue-600 text-white"><h3 class="font-bold uppercase text-xs" id="unitModalTitle">Daftar Paket</h3><button onclick="closeModal('unitDetailModal')"><i class="fas fa-times"></i></button></div><div class="p-4 bg-slate-50"><div class="overflow-x-auto max-h-[500px]"><table class="w-full text-[9px] text-left border bg-white"><thead id="unitModalThead"></thead><tbody id="unitModalTableBody"></tbody></table></div><div class="flex justify-end mt-4"><button onclick="closeModal('unitDetailModal')" class="bg-gray-200 px-6 py-2 rounded text-xs font-bold uppercase hover:bg-gray-300">TUTUP</button></div></div></div>
    </div>

    <!-- Modal Detail PR -->
    <div id="prUnitDetailModal" class="modal">
        <div class="glass-card w-11/12 max-w-[95%] overflow-hidden shadow-2xl"><div class="p-4 border-b flex justify-between items-center bg-indigo-700 text-white"><h3 class="font-bold uppercase text-xs" id="prUnitModalTitle">Rincian Paket PR</h3><button onclick="closeModal('prUnitDetailModal')"><i class="fas fa-times"></i></button></div><div class="p-4 bg-slate-50"><div class="overflow-x-auto max-h-[500px]"><table class="w-full text-[9px] text-left border bg-white"><thead><tr class="bg-gray-100"><th class="border p-2">Nama Paket</th><th class="border p-2 text-right">Nilai Paket</th><th class="border p-2">Metode</th><th class="border p-2">Jenis</th><th class="border p-2">Lokasi</th><th class="border p-2 text-center">Tgl PR</th><th class="border p-2 text-center">Status</th></tr></thead><tbody id="prUnitModalTableBody"></tbody></table></div><div class="flex justify-end mt-4"><button onclick="closeModal('prUnitDetailModal')" class="bg-gray-200 px-6 py-2 rounded text-xs font-bold uppercase hover:bg-gray-300">TUTUP</button></div></div></div>
    </div>

    <script>
        const TODAY = new Date("2024-09-15");
        let charts = {};
        let timelineChart = null;

        if (typeof ChartDataLabels !== 'undefined') { Chart.register(ChartDataLabels); }

        // --- GLOBAL HELPERS (DEFINED AT TOP FOR SCOPE) ---
        function fIDR(a) { return a ? "Rp " + new Intl.NumberFormat('id-ID').format(a) : "-"; }
        function fIDRCompact(a) { if(!a) return "Rp 0"; if(a>=1e9) return "Rp "+(a/1e9).toFixed(2)+" M"; if(a>=1e6) return "Rp "+(a/1e6).toFixed(1)+" Jt"; return fIDR(a); }
        function closeModal(id) { document.getElementById(id).classList.remove('active'); }

        // --- DATASET ---
        const unpadUnits = ["Direktorat Perencanaan, Sistem Informasi, dan Transformasi Digital", "Direktorat Keuangan dan Tresuri", "Fakultas Hukum", "Fakultas Keperawatan", "Fakultas Psikologi", "Fakultas Kedokteran", "Program Pascasarjana"];
        const masterData = [
            { id: 1, tahun: "2024", unit: unpadUnits[0], paket: "Server Data Center 2024", nomorPr: "PR/2024/001/DSID", nomorPo: "PO/2024/V-001", nomorRec: "BAST/2024/001", nomorPay: "PAY/2024/SP2D-001", nilaiRup: 1500000000, nilaiPo: 1200000000, jenis: "Barang", metode: "e-Purchasing", lokasi: "Bandung", tahap: "PAYMENT", statusPr: "Approve", statusPo: "Approve", statusPay: "Approve", vendor: "PT. Jaringan Global", timeline: { RUP: "2024-01-10", PR: "2024-01-25", Negotiations: "2024-02-05", PO: "2024-02-15", REC: "2024-03-01", Pay: "2024-03-15" }, items: [{n:"Server Utama", v:2, vRec:2, s:"Unit", h:600000000}] },
            { id: 2, tahun: "2024", unit: unpadUnits[1], paket: "Lisensi Database Oracle", nomorPr: "PR/2024/002/KEU", nomorPo: "PO/2024/V-002", nomorRec: "BAST/2024/002", nomorPay: "PAY/2024/SP2D-002", nilaiRup: 500000000, nilaiPo: 480000000, jenis: "Jasa Konsultansi", metode: "Pengadaan Langsung", lokasi: "Bandung", tahap: "PAYMENT", statusPr: "Approve", statusPo: "Approve", statusPay: "Approve", vendor: "PT. Solusi IT", timeline: { RUP: "2024-01-05", PR: "2024-01-15", Negotiations: "2024-01-25", PO: "2024-02-01", REC: "2024-02-15", Pay: "2024-02-28" }, items: [{n:"Lisensi Database", v:1, vRec:1, s:"Pkg", h:480000000}] },
            { id: 3, tahun: "2024", unit: unpadUnits[2], paket: "Renovasi Ruang Sidang FH", nomorPr: "PR/2024/005/FH", nomorPo: "PO/2024/V-003", nomorRec: null, nomorPay: null, nilaiRup: 800000000, nilaiPo: 780000000, jenis: "Pekerjaan Konstruksi", metode: "Tender", lokasi: "Bandung", tahap: "PO", statusPr: "Approve", statusPo: "Submit", statusPay: "Draft", vendor: "CV. Bangun Jaya", timeline: { RUP: "2024-02-01", PR: "2024-03-10", Negotiations: "2024-04-05", PO: "2024-05-15", REC: null, Pay: null }, items: [{n:"Pekerjaan Sipil", v:1, vRec:0, s:"Lot", h:780000000}] },
            { id: 16, tahun: "2024", unit: unpadUnits[0], paket: "Pemeliharaan Lift Rektorat", nomorPr: "PR/2024/008/DSID", nomorPo: "PO/2024/V-004", nomorRec: "BAST/2024/015", nomorPay: null, nilaiRup: 400000000, nilaiPo: 380000000, jenis: "Jasa Lainnya", metode: "Pengadaan Langsung", lokasi: "Bandung", tahap: "RECEIVING", statusPr: "Approve", statusPo: "Approve", statusPay: "Draft", vendor: "PT. Fiber Link", timeline: { RUP: "2024-03-01", PR: "2024-04-01", Negotiations: "2024-04-15", PO: "2024-05-01", REC: "2024-09-10", Pay: null }, items: [{n:"Service Bulanan", v:12, vRec:8, s:"Bulan", h:31666666}] },
            { id: 4, tahun: "2024", unit: unpadUnits[3], paket: "Pengadaan Mikroskop Lab", nomorPr: "PR/2024/010/FKP", nomorPo: null, nomorRec: null, nomorPay: null, nilaiRup: 500000000, nilaiPo: 0, jenis: "Barang", metode: "e-Purchasing", lokasi: "Jatinangor", tahap: "PR", statusPr: "Submit", statusPo: "Draft", statusPay: "Draft", vendor: "-", timeline: { RUP: "2024-01-10", PR: "2024-02-15", Negotiations: "2024-02-05", PO: null, REC: null, Pay: null }, items: [{n:"Mikroskop Binokuler", v:10, vRec:0, s:"Unit", h:50000000}] }
        ];

        // --- NAVIGATION ---
        function showPage(p, el) { 
            document.querySelectorAll('.page-section').forEach(x => x.classList.remove('active')); 
            const t = document.getElementById('page-' + p); if(t) t.classList.add('active'); 
            document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active')); 
            if(el) el.classList.add('active'); 
            updateDashboard(); 
        }

        function updateDashboard() {
            const year = document.getElementById('filterTahun').value;
            const unit = document.getElementById('filterUnit').value;
            const methods = document.getElementById('filterMetode').value;
            const search = document.getElementById('filterSearch').value.toLowerCase();

            const filtered = masterData.filter(d => 
                (year === "Semua" || d.tahun === year) && (unit === "Semua" || d.unit === unit) && 
                (methods === "Semua" || d.metode === methods) && (d.paket.toLowerCase().includes(search))
            );

            const stats = { RUP:{q:0,v:0}, PR:{q:0,v:0}, PO:{q:0,v:0}, REC:{q:0,v:0}, PAY:{q:0,v:0} };
            filtered.forEach(d => {
                stats.RUP.q++; stats.RUP.v += d.nilaiRup;
                if(['PR','PO','RECEIVING','PAYMENT'].includes(d.tahap)) { stats.PR.q++; stats.PR.v += d.nilaiRup; }
                if(['PO','RECEIVING','PAYMENT'].includes(d.tahap)) { stats.PO.q++; stats.PO.v += d.nilaiPo; }
                if(['RECEIVING','PAYMENT'].includes(d.tahap)) { stats.REC.q++; stats.REC.v += d.items.reduce((a,c)=>a+(c.vRec*c.h), 0); }
                if(d.tahap === 'PAYMENT') { stats.PAY.q++; stats.PAY.v += d.nilaiPo; }
            });

            const updateTxt = (id, val) => { const el = document.getElementById(id); if(el) el.innerText = val; };
            updateTxt('badgeRupQty', stats.RUP.q + " Pkt"); updateTxt('badgeRupVal', fIDRCompact(stats.RUP.v));
            updateTxt('badgePrQty', stats.PR.q + " Pkt"); updateTxt('badgePrVal', fIDRCompact(stats.PR.v));
            updateTxt('badgePoQty', stats.PO.q + " Pkt"); updateTxt('badgePoVal', fIDRCompact(stats.PO.v));
            updateTxt('badgeRecQty', stats.REC.q + " Pkt"); updateTxt('badgeRecVal', fIDRCompact(stats.REC.v));
            updateTxt('badgePayQty', stats.PAY.q + " Pkt"); updateTxt('badgePayVal', fIDRCompact(stats.PAY.v));

            updateTxt('pageRupQty', stats.RUP.q + " Paket"); updateTxt('pageRupVal', fIDR(stats.RUP.v));
            updateTxt('pagePrQty', stats.PR.q + " Paket"); updateTxt('pagePrVal', fIDR(stats.PR.v));
            updateTxt('pagePoQty', stats.PO.q + " Paket"); updateTxt('pagePoVal', fIDR(stats.PO.v));
            updateTxt('pageRecQty', stats.REC.q + " Paket"); updateTxt('pageRecVal', fIDR(stats.REC.v));
            updateTxt('pagePayQty', stats.PAY.q + " Paket"); updateTxt('pagePayVal', fIDR(stats.PAY.v));

            populateUnitTable(filtered);
            populateRupSummaryTable(filtered);
            populatePrPackageTable(filtered);
            populatePoPackageTable(filtered);
            populateRecPackageTable(filtered);
            populatePayPackageTable(filtered); // Added
            
            updateHomeCharts(filtered);
            updateRupPageCharts(filtered);
            updatePrPageCharts(filtered);
            updatePoPageCharts(filtered);
            updateRecPageCharts(filtered);
            updatePayPageCharts(filtered); // Added
            updateMonitoringViews(filtered);
        }

        // --- TABLES ---
        function populateRupSummaryTable(data) {
            const tbody = document.getElementById('rupSummaryTableBody'); if(!tbody) return;
            const curYear = document.getElementById('filterTahun').value === "Semua" ? "2024" : document.getElementById('filterTahun').value;
            tbody.innerHTML = unpadUnits.map(u => {
                const ud = data.filter(x => x.unit === u);
                return `<tr class="hover:bg-gray-50"><td class="text-center text-[10px] font-semibold">${curYear}</td><td class="font-bold text-blue-600 cursor-pointer hover:underline text-[10px]" onclick="openRupUnitDetail('${u}')">${u}</td><td class="text-center font-bold text-[10px]">${ud.length} Paket</td><td class="text-right font-bold text-slate-700 text-[10px]">${fIDR(ud.reduce((a,c)=>a+c.nilaiRup,0))}</td></tr>`;
            }).join("");
        }

        function populatePrSummaryTable(data) {
            const tbody = document.getElementById('prSummaryTableBody'); if(!tbody) return;
            const curYear = document.getElementById('filterTahun').value === "Semua" ? "2024" : document.getElementById('filterTahun').value;
            tbody.innerHTML = unpadUnits.map(u => {
                const ud = data.filter(x => x.unit === u && ['PR','PO','RECEIVING','PAYMENT'].includes(x.tahap));
                return `<tr class="hover:bg-gray-50"><td class="text-center text-[10px] font-semibold">${curYear}</td><td class="font-bold text-indigo-600 cursor-pointer hover:underline text-[10px]" onclick="openUnitDetail('${u}', 'unit')">${u}</td><td class="text-center font-bold text-[10px]">${ud.length} Paket</td><td class="text-right font-bold text-slate-700 text-[10px]">${fIDR(ud.reduce((a,c)=>a+c.nilaiRup,0))}</td></tr>`;
            }).join("");
        }

        function populatePrPackageTable(data) {
            const tbody = document.getElementById('prPackageTableBody'); if(!tbody) return;
            const prData = data.filter(d => ['PR','PO','RECEIVING','PAYMENT'].includes(d.tahap));
            tbody.innerHTML = prData.length > 0 ? prData.map(p => `<tr class="hover:bg-gray-50"><td class="font-bold text-indigo-600 cursor-pointer hover:underline" onclick="openDetail(${p.id})">${p.paket}</td><td class="text-center font-mono text-[10px]">${p.nomorPr || "-"}</td><td class="text-center text-[10px]">${p.timeline.PR || "-"}</td><td class="text-right font-bold text-slate-700 text-[10px]">${fIDR(p.nilaiRup)}</td><td>${p.unit.substring(0,25)}...</td><td>${p.metode}</td><td class="text-center"><span class="px-2 py-0.5 rounded text-[8px] font-bold uppercase ${p.statusPr==='Approve'?'bg-green-100 text-green-700':(p.statusPr==='Cancel'?'bg-red-100 text-red-700':'bg-amber-100 text-amber-700')}">${p.statusPr || "Draft"}</span></td></tr>`).join("") : `<tr><td colspan="7" class="p-4 text-center text-gray-400 italic">Tidak ada data.</td></tr>`;
        }

        function populatePoPackageTable(data) {
            const tbody = document.getElementById('poPackageTableBody'); if(!tbody) return;
            const poData = data.filter(d => ['PO','RECEIVING','PAYMENT'].includes(d.tahap));
            tbody.innerHTML = poData.length > 0 ? poData.map(p => `<tr class="hover:bg-gray-50"><td class="font-bold text-emerald-600 cursor-pointer hover:underline" onclick="openDetail(${p.id})">${p.paket}</td><td class="text-center font-mono text-[10px]">${p.nomorPo || "-"}</td><td class="text-center text-[10px]">${p.timeline.PO || "-"}</td><td class="text-right font-bold text-slate-700 text-[10px]">${fIDR(p.nilaiPo)}</td><td>${p.vendor}</td><td>${p.metode}</td><td>${p.jenis}</td><td class="text-center"><span class="px-2 py-0.5 rounded text-[8px] font-bold uppercase ${p.statusPo==='Approve'?'bg-green-100 text-green-700':(p.statusPo==='Cancel'?'bg-red-100 text-red-700':'bg-amber-100 text-amber-700')}">${p.statusPo||'Draft'}</span></td></tr>`).join("") : `<tr><td colspan="8" class="p-4 text-center text-gray-400 italic">Belum ada PO.</td></tr>`;
        }

        function populateRecPackageTable(data) {
            const tbody = document.getElementById('recPackageTableBody'); if(!tbody) return;
            const recData = data.filter(d => ['RECEIVING','PAYMENT'].includes(d.tahap));
            tbody.innerHTML = recData.length > 0 ? recData.map(p => {
                const volSdh = p.items.reduce((a,c)=>a+c.vRec,0); const volTotal = p.items.reduce((a,c)=>a+c.v,0);
                const valSdh = p.items.reduce((a,c)=>a+(c.vRec*c.h),0); const valTotal = p.items.reduce((a,c)=>a+(c.v*c.h),0);
                const progress = (volTotal>0?(volSdh/volTotal*100):0).toFixed(0);
                return `<tr class="hover:bg-gray-50"><td class="font-bold text-amber-600 cursor-pointer hover:underline" onclick="openRecDetail(${p.id})">${p.paket}</td><td class="text-center font-mono text-[10px]">${p.nomorRec || "-"}</td><td class="text-center text-[10px]">${p.timeline.REC || "-"}</td><td class="text-right bg-emerald-50/30"><div class="font-bold text-emerald-700">${volSdh} Unit</div><div class="text-[9px]">${fIDR(valSdh)}</div></td><td class="text-right bg-amber-50/30"><div class="font-bold text-amber-700">${volTotal-volSdh} Unit</div><div class="text-[9px]">${fIDR(valTotal-valSdh)}</div></td><td class="text-center p-3"><div class="w-full bg-gray-200 rounded-full h-1.5 mb-1"><div class="bg-amber-500 h-1.5 rounded-full" style="width: ${progress}%"></div></div><span class="text-[9px] font-bold text-amber-700">${progress}% Selesai</span></td></tr>`;
            }).join("") : `<tr><td colspan="6" class="p-4 text-center text-gray-400 italic">Belum ada penerimaan.</td></tr>`;
        }

        function populatePayPackageTable(data) {
            const tbody = document.getElementById('payPackageTableBody'); if(!tbody) return;
            const payData = data.filter(d => d.tahap === 'PAYMENT');
            tbody.innerHTML = payData.length > 0 ? payData.map(p => `
                <tr class="hover:bg-gray-50">
                    <td class="font-bold text-purple-600 cursor-pointer hover:underline" onclick="openDetail(${p.id})">${p.paket}</td>
                    <td class="text-center font-mono text-[10px]">${p.nomorPay || "-"}</td>
                    <td class="text-center text-[10px]">${p.timeline.Pay || "-"}</td>
                    <td class="text-right font-bold text-slate-700">${fIDR(p.nilaiPo)}</td>
                    <td>${p.vendor}</td>
                    <td>${p.metode}</td>
                    <td class="text-center"><span class="px-2 py-0.5 rounded text-[8px] font-bold uppercase ${p.statusPay==='Approve'?'bg-green-100 text-green-700':(p.statusPay==='Cancel'?'bg-red-100 text-red-700':'bg-amber-100 text-amber-700')}">${p.statusPay||'Draft'}</span></td>
                </tr>`).join("") : `<tr><td colspan="7" class="p-4 text-center text-gray-400 italic text-[10px]">Belum ada paket terbayar.</td></tr>`;
        }

        function populateUnitTable(data) {
            const tbody = document.getElementById('unitTableBody'); if(!tbody) return;
            tbody.innerHTML = unpadUnits.map(u => {
                const ud = data.filter(x => x.unit === u);
                const reqs = ud.filter(d => ['PR','PO','RECEIVING','PAYMENT'].includes(d.tahap));
                const pos = ud.filter(d => ['PO','RECEIVING','PAYMENT'].includes(d.tahap));
                const recs = ud.filter(d => ['RECEIVING','PAYMENT'].includes(d.tahap));
                const pays = ud.filter(d => d.tahap === 'PAYMENT');
                const valReq = ud.reduce((a,c)=>a+c.nilaiRup, 0);
                const valPo = pos.reduce((a,c)=>a+c.nilaiPo, 0);
                const valRec = recs.reduce((a,c)=>a+(c.items.reduce((b,it)=>b+(it.vRec*it.h),0)),0);
                const valPay = pays.reduce((a,c)=>a+c.nilaiPo, 0);
                return `<tr><td class="font-bold text-blue-600 cursor-pointer border-r hover:underline text-[10px]" onclick="openUnitDetail('${u}', 'unit')">${u}</td><td class="text-center bg-indigo-50/50">${reqs.length}</td><td class="text-right border-r bg-indigo-50/50 text-[10px]">${fIDR(valReq)}</td><td class="text-center bg-emerald-50/50">${pos.length}</td><td class="text-right border-r bg-emerald-50/50 text-[10px]">${fIDR(valPo)}</td><td class="text-center bg-amber-50/50">${recs.length}</td><td class="text-right border-r bg-amber-50/50 text-[10px]">${fIDR(valRec)}</td><td class="text-center bg-purple-50/50">${pays.length}</td><td class="text-right border-r bg-purple-50/50 text-[10px]">${fIDR(valPay)}</td><td class="text-center font-bold text-green-600 text-[10px]">0%</td></tr>`;
            }).join("");
        }

        // --- DRILL-DOWN HELPERS ---
        function openRecDetail(id) {
            const d = masterData.find(x => x.id === id); if(!d) return;
            document.getElementById('modalTitle').innerText = 'Status Penerimaan Barang: ' + d.paket;
            const thead = document.getElementById('modalThead');
            thead.innerHTML = `<tr class="bg-gray-100"><th class="p-2 border">No</th><th class="p-2 border">Nama Barang</th><th class="p-2 border text-center">Pesan</th><th class="p-2 border text-center">Terima</th><th class="p-2 border text-center">Sisa</th><th class="p-2 border text-right">Nilai Terima</th><th class="p-2 border text-right">Nilai Sisa</th></tr>`;
            const tbody = document.getElementById('modalTableBody');
            let totalValRec = 0;
            tbody.innerHTML = d.items.map((it, idx) => {
                const valRec = it.vRec * it.h; const valSisa = (it.v - it.vRec) * it.h; totalValRec += valRec;
                return `<tr><td class="p-2 border text-center text-[10px]">${idx + 1}</td><td class="p-2 border font-bold text-[10px]">${it.n}</td><td class="p-2 border text-center text-[10px]">${it.v}</td><td class="p-2 border text-center font-bold text-emerald-600 text-[10px]">${it.vRec}</td><td class="p-2 border text-center font-bold text-amber-600 text-[10px]">${it.v-it.vRec}</td><td class="p-2 border text-right text-emerald-700 text-[10px]">${fIDR(valRec)}</td><td class="p-2 border text-right text-amber-700 text-[10px]">${fIDR(valSisa)}</td></tr>`;
            }).join("");
            document.getElementById('modalTotal').innerText = fIDR(totalValRec);
            document.getElementById('detailModal').classList.add('active');
        }

        function openDetail(id) {
            const d = masterData.find(x => x.id === id); if(!d) return;
            document.getElementById('modalTitle').innerText = 'Rincian Item: ' + d.paket;
            const thead = document.getElementById('modalThead');
            thead.innerHTML = `<tr class="bg-gray-100"><th class="p-2 border">No</th><th class="p-2 border">Nama Barang</th><th class="p-2 border text-center">Jumlah Barang</th><th class="p-2 border text-center">Satuan</th><th class="p-2 border text-right">Harga Satuan</th><th class="p-2 border text-right">Jumlah Harga</th></tr>`;
            const tbody = document.getElementById('modalTableBody');
            tbody.innerHTML = d.items.map((it, idx) => `<tr class="hover:bg-gray-50"><td class="p-2 border text-center text-[10px]">${idx + 1}</td><td class="p-2 border font-bold text-[10px]">${it.n}</td><td class="p-2 border text-center text-[10px]">${it.v}</td><td class="p-2 border text-center text-[10px]">${it.s || 'Unit'}</td><td class="p-2 border text-right text-[10px]">${fIDR(it.h)}</td><td class="p-2 border text-right font-bold text-[10px]">${fIDR(it.v * it.h)}</td></tr>`).join("");
            document.getElementById('modalTotal').innerText = fIDR(d.nilaiPo > 0 ? d.nilaiPo : d.nilaiRup);
            document.getElementById('detailModal').classList.add('active');
        }

        function openRupUnitDetail(u) {
            const elTitle = document.getElementById('rupUnitModalTitle'); if(elTitle) elTitle.innerText = 'Rincian Paket RUP: ' + u;
            const filtered = masterData.filter(x => x.unit === u);
            const tbody = document.getElementById('rupUnitModalTableBody');
            if(tbody) tbody.innerHTML = filtered.length > 0 ? filtered.map(p => `<tr class="hover:bg-gray-50"><td class="p-2 border font-bold text-slate-700 text-[10px]">${p.paket}</td><td class="p-2 border text-right text-[10px] font-bold">${fIDR(p.nilaiRup)}</td><td class="p-2 border text-[10px]">${p.metode}</td><td class="p-2 border text-[10px]">${p.jenis || 'Barang'}</td><td class="p-2 border text-[10px]">${p.lokasi}</td><td class="p-2 border text-center font-mono text-[10px]">${p.timeline.Negotiations || "-"}</td><td class="p-2 border text-center text-[10px]"><span class="px-2 py-0.5 rounded text-[8px] font-bold uppercase ${p.tahap === 'RUP' ? 'bg-amber-100 text-amber-700' : 'bg-green-100 text-green-700'}">${p.tahap === 'RUP' ? 'Belum diajukan' : 'Sudah diajukan'}</span></td></tr>`).join("") : `<tr><td colspan="7" class="p-4 text-center text-gray-400 italic text-[10px]">Belum ada data.</td></tr>`;
            document.getElementById('rupUnitDetailModal').classList.add('active');
        }

        function openUnitDetail(u, context) {
            const elTitle = document.getElementById('unitModalTitle'); if(elTitle) elTitle.innerText = 'Riwayat Paket: ' + u;
            const filtered = masterData.filter(x => x.unit === u);
            const thead = document.getElementById('unitModalThead');
            if(thead) thead.innerHTML = `<tr class="bg-gray-100 uppercase text-center text-[8px]"><th class="border p-2">Nama Paket</th><th class="border p-2 bg-indigo-50">Tgl Req</th><th class="border p-2 bg-indigo-50">Nilai Req</th><th class="border p-2 bg-emerald-50">Tgl PO</th><th class="border p-2 bg-emerald-50">Nilai PO</th><th class="border p-2 bg-amber-50">Tgl REC</th><th class="border p-2 bg-purple-50">Tgl Pay</th><th class="border p-2 bg-green-50">Eff</th></tr>`;
            const tbody = document.getElementById('unitModalTableBody');
            if(tbody) tbody.innerHTML = filtered.map(p => `<tr class="hover:bg-gray-50 text-[8px]"><td class="p-2 border font-bold">${p.paket}</td><td class="p-2 border text-center">${p.timeline.PR || "-"}</td><td class="p-2 border text-right">${fIDR(p.nilaiRup)}</td><td class="p-2 border text-center">${p.timeline.PO || "-"}</td><td class="p-2 border text-right">${fIDR(p.nilaiPo)}</td><td class="p-2 border text-center">${p.timeline.REC || "-"}</td><td class="p-2 border text-center">${p.timeline.Pay || "-"}</td><td class="p-2 border text-center font-bold">0%</td></tr>`).join("");
            document.getElementById('unitDetailModal').classList.add('active');
        }

        // --- CHARTING ---
        function updatePayPageCharts(data) {
            const resetChart = (id, config) => { if(charts[id]) charts[id].destroy(); const el = document.getElementById(id); if(el) charts[id] = new Chart(el, config); };
            const unitSummary = unpadUnits.map(u => { const ud = data.filter(d => d.unit === u && d.tahap === 'PAYMENT'); return { label: u.substring(0,10)+'..', q: ud.length, v: ud.reduce((a,c)=>a+c.nilaiPo,0), fullLabel: u }; });
            resetChart('chartPayUnit', { type: 'bar', data: { labels: unitSummary.map(s => s.label), datasets: [{ label: 'Jml Pay', data: unitSummary.map(s => s.q), backgroundColor: '#7c3aed', xAxisID: 'x' }, { label: 'Nilai (Miliar)', data: unitSummary.map(s => s.v/1e9), backgroundColor: '#a78bfa', xAxisID: 'x1' }] }, options: { indexAxis: 'y', responsive: true, maintainAspectRatio: false, scales: { x: { type: 'linear', position: 'bottom' }, x1: { type: 'linear', position: 'top', grid: { drawOnChartArea: false } } }, plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false, callbacks: { title: () => '', label: (c) => c.datasetIndex === 0 ? [`${unitSummary[c.dataIndex].fullLabel}`, `Jumlah: ${c.raw} Paket`] : `Total Terbayar: ${fIDR(unitSummary[c.dataIndex].v)}` } } } } });
            
            const totalContracts = data.filter(d => ['PO','RECEIVING','PAYMENT'].includes(d.tahap)).reduce((a,c)=>a+c.nilaiPo,0);
            const paidTotal = data.filter(d => d.tahap === 'PAYMENT').reduce((a,c)=>a+c.nilaiPo,0);
            const payGaugePlugin = { id: 'payGaugeText', afterDraw: (chart) => { if (chart.config.type !== 'doughnut' || chart.canvas.id !== 'chartPayProgres') return; const { ctx, chartArea: { top, height, left, width } } = chart; ctx.save(); const total = chart.data.datasets[0].data.reduce((a, b) => a + b, 0); const val = chart.data.datasets[0].data[0]; const percentage = total > 0 ? ((val / total) * 100).toFixed(0) + "%" : "0%"; ctx.font = 'bold 30px Inter'; ctx.fillStyle = '#1e293b'; ctx.textAlign = 'center'; ctx.fillText(percentage, left + width / 2, top + height / 2 + 10); ctx.restore(); } };
            resetChart('chartPayProgres', { type: 'doughnut', data: { labels: ['Selesai Bayar', 'Belum Bayar'], datasets: [{ data: [paidTotal, totalContracts - paidTotal], backgroundColor: ['#7c3aed', '#f1f5f9'], borderWidth: 0, borderRadius: 10, spacing: 5 }] }, options: { responsive: true, maintainAspectRatio: false, cutout: '80%', plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }, plugins: [payGaugePlugin] });
        }

        // RUP, PR, PO, REC, HOME charts remain similar...
        function updateRecPageCharts(data) {
            const resetChart = (id, config) => { if(charts[id]) charts[id].destroy(); const el = document.getElementById(id); if(el) charts[id] = new Chart(el, config); };
            const unitSummary = unpadUnits.map(u => { const ud = data.filter(d => d.unit === u && ['RECEIVING','PAYMENT'].includes(d.tahap)); const valRec = ud.reduce((a,c)=>a + c.items.reduce((b,it)=>b+(it.vRec*it.h),0), 0); return { label: u.substring(0,10)+'..', q: ud.length, v: valRec, fullLabel: u }; });
            resetChart('chartRecUnit', { type: 'bar', data: { labels: unitSummary.map(s => s.label), datasets: [{ label: 'Jml Paket', data: unitSummary.map(s => s.q), backgroundColor: '#f59e0b', xAxisID: 'x' }, { label: 'Nilai (Miliar)', data: unitSummary.map(s => s.v/1e9), backgroundColor: '#fbbf24', xAxisID: 'x1' }] }, options: { indexAxis: 'y', responsive: true, maintainAspectRatio: false, scales: { x: { type: 'linear', position: 'bottom' }, x1: { type: 'linear', position: 'top', grid: { drawOnChartArea: false } } }, plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false, callbacks: { title: () => '', label: (c) => c.datasetIndex === 0 ? [`${unitSummary[c.dataIndex].fullLabel}`, `Jumlah: ${c.raw} Paket`] : `Nilai Diterima: ${fIDR(unitSummary[c.dataIndex].v)}` } } } } });
            const totalItems = data.filter(d => ['PO','RECEIVING','PAYMENT'].includes(d.tahap)).reduce((a,c)=>a + c.items.reduce((b,it)=>b+it.v, 0), 0);
            const receivedItems = data.filter(d => ['RECEIVING','PAYMENT'].includes(d.tahap)).reduce((a,c)=>a + c.items.reduce((b,it)=>b+it.vRec, 0), 0);
            const recGaugePlugin = { id: 'recGaugeText', afterDraw: (chart) => { if (chart.config.type !== 'doughnut' || chart.canvas.id !== 'chartRecProgres') return; const { ctx, chartArea: { top, height, left, width } } = chart; ctx.save(); const total = chart.data.datasets[0].data.reduce((a, b) => a + b, 0); const val = chart.data.datasets[0].data[0]; const percentage = total > 0 ? ((val / total) * 100).toFixed(0) + "%" : "0%"; ctx.font = 'bold 30px Inter'; ctx.fillStyle = '#1e293b'; ctx.textAlign = 'center'; ctx.fillText(percentage, left + width / 2, top + height / 2 + 10); ctx.restore(); } };
            resetChart('chartRecProgres', { type: 'doughnut', data: { labels: ['Selesai Fisik', 'Sisa Barang'], datasets: [{ data: [receivedItems, totalItems - receivedItems], backgroundColor: ['#f59e0b', '#f1f5f9'], borderWidth: 0, borderRadius: 10, spacing: 5 }] }, options: { responsive: true, maintainAspectRatio: false, cutout: '80%', plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }, plugins: [recGaugePlugin] });
        }

        function updatePoPageCharts(data) {
            const resetChart = (id, config) => { if(charts[id]) charts[id].destroy(); const el = document.getElementById(id); if(el) charts[id] = new Chart(el, config); };
            const unitSummary = unpadUnits.map(u => { const ud = data.filter(d => d.unit === u && ['PO','RECEIVING','PAYMENT'].includes(d.tahap)); return { label: u.substring(0,10)+'..', q: ud.length, v: ud.reduce((a,c)=>a+c.nilaiPo,0), fullLabel: u }; });
            resetChart('chartPoUnit', { type: 'bar', data: { labels: unitSummary.map(s => s.label), datasets: [{ label: 'Jml PO', data: unitSummary.map(s => s.q), backgroundColor: '#10b981', xAxisID: 'x' }, { label: 'Kontrak (Miliar)', data: unitSummary.map(s => s.v/1e9), backgroundColor: '#14b8a6', xAxisID: 'x1' }] }, options: { indexAxis: 'y', responsive: true, maintainAspectRatio: false, scales: { x: { type: 'linear', position: 'bottom' }, x1: { type: 'linear', position: 'top', grid: { drawOnChartArea: false } } }, plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false, callbacks: { title: () => '', label: (c) => c.datasetIndex === 0 ? [`${unitSummary[c.dataIndex].fullLabel}`, `Jumlah: ${c.raw} Kontrak`] : `Total Kontrak: ${fIDR(unitSummary[c.dataIndex].v)}` } } } } });
            const poData = data.filter(d => ['PO','RECEIVING','PAYMENT'].includes(d.tahap));
            const sudahLanjut = poData.filter(d => d.tahap !== 'PO').length; const belumLanjut = poData.filter(d => d.tahap === 'PO').length;
            const poGaugePlugin = { id: 'poGaugeText', afterDraw: (chart) => { if (chart.config.type !== 'doughnut' || chart.canvas.id !== 'chartPoProgres') return; const { ctx, chartArea: { top, height, left, width } } = chart; ctx.save(); const total = chart.data.datasets[0].data.reduce((a, b) => a + b, 0); const val = chart.data.datasets[0].data[0]; const percentage = total > 0 ? ((val / total) * 100).toFixed(0) + "%" : "0%"; ctx.font = 'bold 30px Inter'; ctx.fillStyle = '#1e293b'; ctx.textAlign = 'center'; ctx.fillText(percentage, left + width / 2, top + height / 2 + 10); ctx.restore(); } };
            resetChart('chartPoProgres', { type: 'doughnut', data: { labels: ['Sudah Lanjut', 'Belum Lanjut'], datasets: [{ data: [sudahLanjut, belumLanjut], backgroundColor: ['#059669', '#f1f5f9'], borderWidth: 0, borderRadius: 10, spacing: 5 }] }, options: { responsive: true, maintainAspectRatio: false, cutout: '80%', plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }, plugins: [poGaugePlugin] });
        }

        function updateRupPageCharts(data) {
            const resetChart = (id, config) => { if(charts[id]) charts[id].destroy(); const el = document.getElementById(id); if(el) charts[id] = new Chart(el, config); };
            const unitSummary = unpadUnits.map(u => { const ud = data.filter(d => d.unit === u); return { label: u.substring(0,10)+'..', q: ud.length, v: ud.reduce((a,c)=>a+c.nilaiRup,0), fullLabel: u }; });
            resetChart('chartRupUnit', { type: 'bar', data: { labels: unitSummary.map(s => s.label), datasets: [{ label: 'Jml Paket', data: unitSummary.map(s => s.q), backgroundColor: '#3b82f6', xAxisID: 'x' }, { label: 'Pagu (Miliar)', data: unitSummary.map(s => s.v/1e9), backgroundColor: '#10b981', xAxisID: 'x1' }] }, options: { indexAxis: 'y', responsive: true, maintainAspectRatio: false, scales: { x: { type: 'linear', position: 'bottom' }, x1: { type: 'linear', position: 'top', grid: { drawOnChartArea: false } } }, plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false, callbacks: { title: () => '', label: (c) => c.datasetIndex === 0 ? [`${unitSummary[c.dataIndex].fullLabel}`, `Jumlah: ${c.raw} Paket`] : `Total Pagu: ${fIDR(unitSummary[c.dataIndex].v)}` } } } } });
            const sudahLanjut = data.filter(d => d.tahap !== 'RUP').length; const belumLanjut = data.filter(d => d.tahap === 'RUP').length;
            const gaugePlugin = { id: 'gaugeText', afterDraw: (chart) => { if (chart.config.type !== 'doughnut' || chart.canvas.id !== 'chartRupProgres') return; const { ctx, chartArea: { top, height, left, width } } = chart; ctx.save(); const total = chart.data.datasets[0].data.reduce((a, b) => a + b, 0); const val = chart.data.datasets[0].data[0]; const percentage = total > 0 ? ((val / total) * 100).toFixed(0) + "%" : "0%"; ctx.font = 'bold 30px Inter'; ctx.fillStyle = '#1e293b'; ctx.textAlign = 'center'; ctx.fillText(percentage, left + width / 2, top + height / 2 + 10); ctx.font = '10px Inter'; ctx.fillStyle = '#64748b'; ctx.fillText('PROGRES', left + width / 2, top + height / 2 - 20); ctx.restore(); } };
            resetChart('chartRupProgres', { type: 'doughnut', data: { labels: ['Sudah diajukan', 'Belum diajukan'], datasets: [{ data: [sudahLanjut, belumLanjut], backgroundColor: ['#6366f1', '#e2e8f0'], borderWidth: 0, borderRadius: 10, spacing: 5 }] }, options: { responsive: true, maintainAspectRatio: false, cutout: '80%', plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }, plugins: [gaugePlugin] });
        }

        function updatePrPageCharts(data) {
            const resetChart = (id, config) => { if(charts[id]) charts[id].destroy(); const el = document.getElementById(id); if(el) charts[id] = new Chart(el, config); };
            const unitSummary = unpadUnits.map(u => { const ud = data.filter(d => d.unit === u && ['PR','PO','RECEIVING','PAYMENT'].includes(d.tahap)); return { label: u.substring(0,10)+'..', q: ud.length, v: ud.reduce((a,c)=>a+c.nilaiRup,0), fullLabel: u }; });
            resetChart('chartPrUnit', { type: 'bar', data: { labels: unitSummary.map(s => s.label), datasets: [{ label: 'Jml Paket', data: unitSummary.map(s => s.q), backgroundColor: '#6366f1', xAxisID: 'x' }, { label: 'Nilai (Miliar)', data: unitSummary.map(s => s.v/1e9), backgroundColor: '#f43f5e', xAxisID: 'x1' }] }, options: { indexAxis: 'y', responsive: true, maintainAspectRatio: false, scales: { x: { type: 'linear', position: 'bottom' }, x1: { type: 'linear', position: 'top', grid: { drawOnChartArea: false } } }, plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false, callbacks: { title: () => '', label: (c) => c.datasetIndex === 0 ? [`${unitSummary[c.dataIndex].fullLabel}`, `Jumlah: ${c.raw} Paket`] : `Total Nilai: ${fIDR(unitSummary[c.dataIndex].v)}` } } } } });
            const prData = data.filter(d => ['PR','PO','RECEIVING','PAYMENT'].includes(d.tahap));
            const sudahLanjut = prData.filter(d => d.tahap !== 'PR').length; const belumLanjut = prData.filter(d => d.tahap === 'PR').length;
            const prGaugePlugin = { id: 'prGaugeText', afterDraw: (chart) => { if (chart.config.type !== 'doughnut' || chart.canvas.id !== 'chartPrProgres') return; const { ctx, chartArea: { top, height, left, width } } = chart; ctx.save(); const total = chart.data.datasets[0].data.reduce((a, b) => a + b, 0); const val = chart.data.datasets[0].data[0]; const percentage = total > 0 ? ((val / total) * 100).toFixed(0) + "%" : "0%"; ctx.font = 'bold 30px Inter'; ctx.fillStyle = '#1e293b'; ctx.textAlign = 'center'; ctx.fillText(percentage, left + width / 2, top + height / 2 + 10); ctx.restore(); } };
            resetChart('chartPrProgres', { type: 'doughnut', data: { labels: ['Sudah diajukan', 'Belum diajukan'], datasets: [{ data: [sudahLanjut, belumLanjut], backgroundColor: ['#10b981', '#f1f5f9'], borderWidth: 0, borderRadius: 10, spacing: 5 }] }, options: { responsive: true, maintainAspectRatio: false, cutout: '80%', plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } } }, plugins: [prGaugePlugin] });
        }

        function updateHomeCharts(data) {
            const getMapData = (field) => { const map = data.reduce((a,c)=>{ if(!a[c[field]]) a[c[field]] = {q:0, v:0}; a[c[field]].q++; a[c[field]].v += c.nilaiRup; return a; }, {}); return { l: Object.keys(map), q: Object.values(map).map(x=>x.q), v: Object.values(map).map(x=>x.v) }; };
            const resetChart = (id, config) => { if(charts[id]) charts[id].destroy(); const el = document.getElementById(id); if(el) charts[id] = new Chart(el, config); };
            const uD = getMapData('unit'); resetChart('chartUnitUsulan', { type: 'pie', data: { labels: uD.l.map(l=>l.substring(0,12)+'..'), datasets: [{ data: uD.v, backgroundColor: ['#3b82f6', '#6366f1', '#10b981', '#f59e0b', '#ec4899', '#14b8a6', '#f43f5e'] }] }, options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false }, datalabels: { color: '#fff', font: { weight: 'bold', size: 10 }, formatter: (v, ctx) => { let sum = 0; ctx.chart.data.datasets[0].data.map(d => sum += d); return (v*100/sum).toFixed(1)+"%"; } }, tooltip: { callbacks: { title: () => '', label: (c) => [`${uD.l[c.dataIndex]}`, `Jumlah: ${uD.q[c.dataIndex]} Pkt`, `Nilai: ${fIDR(uD.v[c.dataIndex])}`] } } } } });
            const jD = getMapData('jenis'); resetChart('chartJenis', { type: 'bar', data: { labels: jD.l, datasets: [{ label: 'Paket', data: jD.q, backgroundColor: '#3b82f6' }] }, options: { responsive:true, maintainAspectRatio:false, plugins: { legend: { display: false } } } });
            const mD = getMapData('metode'); resetChart('chartMetode', { type: 'bar', data: { labels: mD.l, datasets: [{ label: 'Paket', data: mD.q, backgroundColor: '#f59e0b' }] }, options: { responsive:true, maintainAspectRatio:false, plugins: { legend: { display: false } } } });
            const progresData = unpadUnits.map(u => { const total = data.filter(d => d.unit === u && ['PR','PO','RECEIVING','PAYMENT'].includes(d.tahap)); const exec = data.filter(d => d.unit === u && ['PO','RECEIVING','PAYMENT'].includes(d.tahap)); return { l: u.substring(0,10)+'..', tQ: total.length, tV: total.reduce((a,c)=>a+c.nilaiRup,0), eQ: exec.length, eV: exec.reduce((a,c)=>a+c.nilaiPo,0) }; });
            resetChart('chartUnitProgres', { type: 'bar', data: { labels: progresData.map(d=>d.l), datasets: [{ label: 'Total PR', data: progresData.map(d=>d.tQ), backgroundColor: '#cbd5e1' }, { label: 'Executed (PO)', data: progresData.map(d=>d.eQ), backgroundColor: '#6366f1' }] }, options: { responsive: true, maintainAspectRatio: false } });
        }

        function updateMonitoringViews(data) {
            const tab = document.getElementById('monPaketTable'); const sel = document.getElementById('monPaketSelector');
            if(sel) sel.innerHTML = data.map(d => `<option value="${d.id}">${d.paket}</option>`).join("");
            if(tab) tab.innerHTML = data.map(d => { const lastKey = Object.keys(d.timeline).filter(k => d.timeline[k] !== null).pop(); const days = Math.ceil(Math.abs(TODAY - new Date(d.timeline[lastKey] || TODAY)) / (1000*60*60*24)); const eff = d.nilaiPo > 0 ? (((d.nilaiRup - d.nilaiPo) / d.nilaiRup) * 100).toFixed(1) : "-"; let slaBg = days > 14 ? 'bg-red-100 text-red-600' : (days > 7 ? 'bg-amber-100 text-amber-600' : 'bg-green-100 text-green-600'); return `<tr><td><div class="font-bold text-slate-700 text-xs">${d.paket}</div></td><td>${fIDR(d.nilaiPo)}</td><td class="text-center font-bold text-green-600">${eff !== "-" ? eff + "%" : "-"}</td><td class="text-[10px] font-bold text-blue-600 uppercase italic">${d.tahap}</td><td class="text-center font-bold">${days} Hari</td><td class="text-center"><span class="${slaBg} px-2 py-0.5 rounded text-[9px] font-bold uppercase">${days>14?'Over':(days>7?'Warn':'Aman')}</span></td><td><button onclick="openDetail(${d.id})" class="text-blue-600 font-bold text-xs uppercase">Detail</button></td></tr>`; }).join("");
        }

        function updatePaketTimeline() {
            const id = document.getElementById('monPaketSelector').value;
            const item = masterData.find(d => d.id == id); if(!item) return;
            const stages = ["RUP", "PR", "Negotiations", "PO", "REC", "Pay"];
            const dates = stages.map(s => item.timeline[s] ? new Date(item.timeline[s]) : null);
            if(timelineChart) timelineChart.destroy();
            timelineChart = new Chart(document.getElementById('chartTimelinePaket').getContext('2d'), { type: 'line', data: { labels: stages, datasets: [{ label: 'Capaian', data: dates, borderColor: '#3b82f6', pointRadius: 8, fill: true, tension: 0.1, spanGaps: false }] }, options: { responsive: true, maintainAspectRatio: false, scales: { y: { type: 'time', time: { unit: 'day' } } } } });
        }

        function resetFilters() { document.getElementById('filterTahun').value = "Semua"; document.getElementById('filterUnit').value = "Semua"; document.getElementById('filterMetode').value = "Semua"; document.getElementById('filterSearch').value = ""; updateDashboard(); }

        window.onload = () => {
            const optCont = document.getElementById('unitListOptions');
            if(optCont) optCont.outerHTML = unpadUnits.map(u => `<option value="${u}">${u}</option>`).join("");
            updateDashboard();
        };
    </script>
</body>
</html>
