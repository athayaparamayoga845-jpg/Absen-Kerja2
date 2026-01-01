<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Absensi Masuk Kerja</title>
    <!-- Bootstrap 5 -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Bootstrap Icons -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css" rel="stylesheet">
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            font-family: 'Segoe UI', sans-serif;
        }
        .card {
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
            background: rgba(255, 255, 255, 0.95);
        }
        .btn-primary {
            background: #667eea;
            border: none;
            border-radius: 50px;
            padding: 10px 30px;
        }
        .btn-primary:hover {
            background: #764ba2;
        }
        .help-btn {
            position: fixed;
            bottom: 20px;
            right: 20px;
            z-index: 1000;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            font-size: 24px;
        }
        img.selfie-preview {
            max-width: 100%;
            border-radius: 10px;
            margin-top: 10px;
        }
        table img {
            width: 60px;
            border-radius: 8px;
        }
        .input-group-text {
            cursor: pointer;
        }

        /* Overlay Feedback Besar */
        #feedbackOverlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.7);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 2000;
            flex-direction: column;
            color: white;
            text-align: center;
            animation: fadeIn 0.5s;
        }
        #feedbackIcon {
            font-size: 120px;
            margin-bottom: 20px;
        }
        #feedbackMessage {
            font-size: 28px;
            font-weight: bold;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        @keyframes fadeOut {
            from { opacity: 1; }
            to { opacity: 0; }
        }
    </style>
</head>
<body>

    <!-- Overlay Feedback Besar -->
    <div id="feedbackOverlay">
        <div id="feedbackIcon"></div>
        <div id="feedbackMessage"></div>
    </div>

    <!-- Halaman Absensi Karyawan -->
    <div class="container py-5" id="absensiPage">
        <div class="row justify-content-center">
            <div class="col-lg-6 col-md-8">
                <div class="card p-5">
                    <h2 class="text-center mb-4 text-primary">Absensi Masuk Kerja</h2>
                    <form id="absensiForm">
                        <div class="mb-3">
                            <label class="form-label fw-bold">Nama Karyawan</label>
                            <input type="text" class="form-control form-control-lg" id="nama" required>
                        </div>

                        <div class="mb-3">
                            <label class="form-label fw-bold">Unggah Selfie</label>
                            <input type="file" class="form-control" id="foto" accept="image/*" capture="user" required>
                            <img id="preview" class="selfie-preview" style="display:none;">
                        </div>

                        <div class="mb-3">
                            <label class="form-label fw-bold">Shift Kerja</label>
                            <select class="form-select form-select-lg" id="shift" required>
                                <option value="">Pilih Shift</option>
                                <option value="Pagi">Pagi</option>
                                <option value="Siang">Siang</option>
                                <option value="Malam">Malam</option>
                            </select>
                        </div>

                        <div class="text-center">
                            <button type="submit" class="btn btn-primary btn-lg">Submit Absensi</button>
                        </div>
                    </form>

                    <div id="status" class="mt-4 alert" style="display:none;"></div>

                    <div class="text-center mt-4">
                        <button class="btn btn-secondary me-2" data-bs-toggle="modal" data-bs-target="#jadwalModal">Lihat Jadwal</button>
                        <a href="#" id="toAdmin" class="text-muted small">Login Admin</a>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal Jadwal -->
    <div class="modal fade" id="jadwalModal">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-primary text-white">
                    <h5 class="modal-title">Jadwal Kerja</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body text-center">
                    <img id="jadwalImage" src="" alt="Jadwal" style="max-width: 100%; display: none;">
                    <p id="noJadwal" class="fs-4 text-muted">Belum ada jadwal yang diupload oleh admin.</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Halaman Admin -->
    <div class="container py-5" id="adminPage" style="display:none;">
        <div class="row justify-content-center">
            <div class="col-lg-10">
                <div class="card p-5">
                    <h2 class="text-center mb-4 text-primary">Dashboard Admin</h2>

                    <!-- Login Admin -->
                    <div id="loginSection">
                        <div class="mb-3">
                            <label class="form-label fw-bold">Password Admin</label>
                            <div class="input-group">
                                <input type="password" class="form-control form-control-lg" id="adminPass" placeholder="Masukkan password admin">
                                <span class="input-group-text" id="togglePassword"><i class="bi bi-eye-slash"></i></span>
                            </div>
                        </div>
                        <div class="text-center">
                            <button id="loginAdmin" class="btn btn-primary btn-lg">Login</button>
                            <button class="btn btn-outline-secondary ms-2" id="backFromLogin">Kembali</button>
                        </div>
                    </div>

                    <!-- Dashboard setelah login -->
                    <div id="dashboard" style="display:none;">
                        <div class="mb-4">
                            <button class="btn btn-outline-secondary" id="logout">Logout</button>
                            <button class="btn btn-warning ms-3" id="changePassBtn">Ubah Password</button>
                            <button class="btn btn-outline-secondary ms-3" id="backFromDashboard">Kembali</button>
                        </div>

                        <!-- Upload Jadwal -->
                        <h4 class="mt-4">Upload Jadwal</h4>
                        <form id="jadwalForm" class="mb-5">
                            <div class="mb-3">
                                <label class="form-label fw-bold">Unggah Gambar Jadwal</label>
                                <input type="file" class="form-control" id="jadwalFoto" accept="image/*" required>
                                <img id="jadwalPreview" class="selfie-preview" style="display:none;">
                            </div>
                            <button type="submit" class="btn btn-primary">Upload Jadwal</button>
                        </form>

                        <h4>Rekap Absensi</h4>
                        <div class="table-responsive">
                            <table class="table table-hover align-middle">
                                <thead class="table-primary">
                                    <tr>
                                        <th>No</th>
                                        <th>Nama</th>
                                        <th>Selfie</th>
                                        <th>Lokasi</th>
                                        <th>Waktu Absen</th>
                                        <th>Shift</th>
                                    </tr>
                                </thead>
                                <tbody id="dataTable"></tbody>
                            </table>
                        </div>

                        <h4 class="mt-5">Statistik Absensi</h4>
                        <div class="row">
                            <div class="col-md-6">
                                <canvas id="shiftChart"></canvas>
                            </div>
                            <div class="col-md-6">
                                <div class="mt-4">
                                    <p id="avgTime" class="fs-5 fw-bold"></p>
                                    <p id="totalPresence" class="fs-5"></p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Tombol Help -->
    <button class="btn btn-info btn-lg help-btn shadow-lg" data-bs-toggle="modal" data-bs-target="#helpModal">?</button>

    <!-- Modal Help -->
    <div class="modal fade" id="helpModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header bg-primary text-white">
                    <h5 class="modal-title">Panduan Absensi</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <h6>Langkah-langkah absensi:</h6>
                    <ol>
                        <li>Isi nama lengkap Anda</li>
                        <li>Ambil selfie (klik ikon kamera atau pilih dari galeri)</li>
                        <li>Pilih shift kerja hari ini</li>
                        <li>Tekan tombol "Submit Absensi"</li>
                    </ol>
                    <hr>
                    <h6>Jika ada kendala:</h6>
                    <ul>
                        <li>Pastikan izinkan akses <strong>kamera</strong> dan <strong>lokasi (GPS)</strong></li>
                        <li>Refresh halaman jika error</li>
                        <li>Hubungi admin jika masalah berlanjut</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        // IndexedDB Setup
        let db;
        const request = indexedDB.open('AbsensiDB', 1);

        request.onerror = () => console.error("Database error");
        request.onsuccess = (event) => {
            db = event.target.result;
            if (window.location.hash === '#admin') showAdmin();
            loadJadwalImage();
        };

        request.onupgradeneeded = (event) => {
            db = event.target.result;
            db.createObjectStore('attendances', { keyPath: 'id', autoIncrement: true });
            db.createObjectStore('settings', { keyPath: 'key' });
        };

        // Fungsi Feedback Visual Besar
        function showFeedback(success, message) {
            const overlay = document.getElementById('feedbackOverlay');
            const icon = document.getElementById('feedbackIcon');
            const msg = document.getElementById('feedbackMessage');

            if (success) {
                icon.innerHTML = '<i class="bi bi-check-circle"></i>';
                icon.style.color = '#28a745';
                msg.textContent = message || 'Absensi Berhasil!';
            } else {
                icon.innerHTML = '<i class="bi bi-x-circle"></i>';
                icon.style.color = '#dc3545';
                msg.textContent = message || 'Absensi Gagal!';
            }

            overlay.style.display = 'flex';

            // Hilang otomatis setelah 3 detik
            setTimeout(() => {
                overlay.style.animation = 'fadeOut 0.5s';
                setTimeout(() => {
                    overlay.style.display = 'none';
                    overlay.style.animation = 'fadeIn 0.5s';
                }, 500);
            }, 3000);
        }

        function showStatus(msg, type = 'success') {
            const status = document.getElementById('status');
            if (status) {
                status.className = `alert alert-${type}`;
                status.textContent = msg;
                status.style.display = 'block';
                setTimeout(() => status.style.display = 'none', 5000);
            }
        }

        function saveSetting(key, value) {
            const tx = db.transaction('settings', 'readwrite');
            tx.objectStore('settings').put({ key, value });
        }

        function getSetting(key, callback) {
            const tx = db.transaction('settings');
            const req = tx.objectStore('settings').get(key);
            req.onsuccess = () => callback(req.result ? req.result.value : null);
        }

        // Preview foto absensi
        document.getElementById('foto').addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file) {
                const preview = document.getElementById('preview');
                preview.src = URL.createObjectURL(file);
                preview.style.display = 'block';
            }
        });

        // Submit Absensi dengan Feedback Visual
        document.getElementById('absensiForm').addEventListener('submit', (e) => {
            e.preventDefault();

            const nama = document.getElementById('nama').value.trim();
            const fotoFile = document.getElementById('foto').files[0];
            const shift = document.getElementById('shift').value;

            if (!nama || !fotoFile || !shift) {
                showFeedback(false, 'Lengkapi semua data!');
                return;
            }

            // Cek Lokasi (GPS)
            navigator.geolocation.getCurrentPosition(
                (position) => {
                    const location = {
                        lat: position.coords.latitude,
                        lng: position.coords.longitude
                    };
                    const waktu = new Date().toLocaleString('id-ID');

                    const reader = new FileReader();
                    reader.onload = () => {
                        const photoData = reader.result;

                        const data = { nama, photoData, location, waktu, shift };

                        const tx = db.transaction('attendances', 'readwrite');
                        tx.objectStore('attendances').add(data);

                        tx.oncomplete = () => {
                            showFeedback(true, 'Absensi Berhasil Disimpan!');
                            document.getElementById('absensiForm').reset();
                            document.getElementById('preview').style.display = 'none';
                        };

                        tx.onerror = () => {
                            showFeedback(false, 'Gagal menyimpan data!');
                        };
                    };
                    reader.onerror = () => {
                        showFeedback(false, 'Gagal membaca foto!');
                    };
                    reader.readAsDataURL(fotoFile);
                },
                (error) => {
                    let errorMsg = 'Gagal mendeteksi lokasi!';
                    if (error.code === 1) errorMsg = 'Akses lokasi ditolak. Izinkan GPS!';
                    else if (error.code === 2) errorMsg = 'Lokasi tidak tersedia.';
                    else if (error.code === 3) errorMsg = 'Timeout deteksi lokasi.';

                    showFeedback(false, errorMsg);
                },
                { enableHighAccuracy: true, timeout: 10000, maximumAge: 60000 }
            );
        });

        // Navigasi Admin & Back
        document.getElementById('toAdmin').addEventListener('click', (e) => {
            e.preventDefault();
            document.getElementById('absensiPage').style.display = 'none';
            document.getElementById('adminPage').style.display = 'block';
            window.location.hash = 'admin';
        });

        document.getElementById('backFromLogin').addEventListener('click', backToAbsensi);
        document.getElementById('backFromDashboard').addEventListener('click', backToAbsensi);

        function backToAbsensi() {
            document.getElementById('adminPage').style.display = 'none';
            document.getElementById('absensiPage').style.display = 'block';
            window.location.hash = '';
        }

        function showAdmin() {
            document.getElementById('absensiPage').style.display = 'none';
            document.getElementById('adminPage').style.display = 'block';
        }

        // Toggle Password
        document.getElementById('togglePassword').addEventListener('click', () => {
            const passField = document.getElementById('adminPass');
            const icon = document.querySelector('#togglePassword i');
            if (passField.type === 'password') {
                passField.type = 'text';
                icon.classList.replace('bi-eye-slash', 'bi-eye');
            } else {
                passField.type = 'password';
                icon.classList.replace('bi-eye', 'bi-eye-slash');
            }
        });

        // Login Admin
        document.getElementById('loginAdmin').addEventListener('click', () => {
            const pass = document.getElementById('adminPass').value;
            getSetting('adminPassword', (savedPass) => {
                const correctPass = savedPass || 'Admin1209';
                if (pass === correctPass) {
                    document.getElementById('loginSection').style.display = 'none';
                    document.getElementById('dashboard').style.display = 'block';
                    loadAdminData();
                } else {
                    alert('Password salah!');
                }
            });
        });

        // Ubah Password & Logout (tetap sama seperti sebelumnya)
        document.getElementById('changePassBtn').addEventListener('click', () => {
            const newPass = prompt('Masukkan password baru:');
            if (newPass && newPass.length >= 4) {
                saveSetting('adminPassword', newPass);
                alert('Password admin berhasil diubah!');
            } else if (newPass) {
                alert('Password minimal 4 karakter');
            }
        });

        document.getElementById('logout').addEventListener('click', () => {
            document.getElementById('dashboard').style.display = 'none';
            document.getElementById('loginSection').style.display = 'block';
            document.getElementById('adminPass').value = '';
        });

        // Jadwal Upload & Tampilkan
        document.getElementById('jadwalFoto').addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file) {
                document.getElementById('jadwalPreview').src = URL.createObjectURL(file);
                document.getElementById('jadwalPreview').style.display = 'block';
            }
        });

        document.getElementById('jadwalForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const file = document.getElementById('jadwalFoto').files[0];
            if (!file) return alert('Pilih gambar terlebih dahulu!');

            const reader = new FileReader();
            reader.onload = () => {
                saveSetting('jadwalImage', reader.result);
                alert('Jadwal berhasil diupload!');
                document.getElementById('jadwalForm').reset();
                document.getElementById('jadwalPreview').style.display = 'none';
                loadJadwalImage();
            };
            reader.readAsDataURL(file);
        });

        function loadJadwalImage() {
            getSetting('jadwalImage', (data) => {
                const img = document.getElementById('jadwalImage');
                const no = document.getElementById('noJadwal');
                if (data) {
                    img.src = data;
                    img.style.display = 'block';
                    no.style.display = 'none';
                } else {
                    img.style.display = 'none';
                    no.style.display = 'block';
                }
            });
        }

        // Load data admin (tetap sama)
        function loadAdminData() {
            const tx = db.transaction('attendances');
            const req = tx.objectStore('attendances').getAll();

            req.onsuccess = () => {
                const data = req.result;
                const tbody = document.getElementById('dataTable');
                tbody.innerHTML = data.length === 0 ?
                    '<tr><td colspan="6" class="text-center">Belum ada data absensi</td></tr>' : '';

                const shifts = { Pagi: 0, Siang: 0, Malam: 0 };
                const presence = {};
                let totalHours = 0;

                data.forEach((item, i) => {
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td>${i + 1}</td>
                        <td>${item.nama}</td>
                        <td><img src="${item.photoData}" alt="selfie"></td>
                        <td>${item.location.lat.toFixed(4)}, ${item.location.lng.toFixed(4)}</td>
                        <td>${item.waktu}</td>
                        <td><span class="badge bg-primary">${item.shift}</span></td>
                    `;
                    tbody.appendChild(row);

                    shifts[item.shift]++;
                    presence[item.nama] = (presence[item.nama] || 0) + 1;
                    totalHours += new Date(item.waktu).getHours();
                });

                document.getElementById('avgTime').textContent = 
                    `Jam masuk rata-rata: ${data.length ? (totalHours / data.length).toFixed(1) : 0} pagi`;

                let presenceText = 'Total kehadiran: ';
                for (const [name, count] of Object.entries(presence)) {
                    presenceText += `${name}: ${count}x, `;
                }
                document.getElementById('totalPresence').textContent = presenceText.slice(0, -2);

                const ctx = document.getElementById('shiftChart').getContext('2d');
                if (window.myChart) window.myChart.destroy();
                window.myChart = new Chart(ctx, {
                    type: 'doughnut',
                    data: {
                        labels: ['Pagi', 'Siang', 'Malam'],
                        datasets: [{
                            data: [shifts.Pagi, shifts.Siang, shifts.Malam],
                            backgroundColor: ['#36A2EB', '#FFCE56', '#FF6384']
                        }]
                    },
                    options: { responsive: true, plugins: { legend: { position: 'bottom' } } }
                });
            };
        }
    </script>
</body>
</html>
