<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบประเมินถังขยะ - โครงงาน</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Kanit', sans-serif; }
        .star { transition: all 0.2s ease; }
        .star:hover { transform: scale(1.1); }
        .star.active { color: #fbbf24; }
        .fade-in { animation: fadeIn 0.5s ease-in; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-gradient-to-br from-green-100 via-yellow-50 to-green-200 min-h-screen">
    <!-- Main Container -->
    <div class="container mx-auto px-4 py-8 max-w-4xl">
        <!-- Header -->
        <div class="text-center mb-8 fade-in">
            <div class="bg-white rounded-2xl shadow-lg p-8 mb-6">
                <div class="text-6xl mb-4">🗑️</div>
                <h1 class="text-4xl font-bold text-gray-800 mb-2">ระบบประเมินถังขยะ</h1>
                <p class="text-gray-600 text-lg">ให้คะแนนและแสดงความคิดเห็นเกี่ยวกับถังขยะของเรา</p>
            </div>
        </div>

        <!-- Public Voting Section -->
        <div id="votingSection" class="fade-in">
            <!-- Rating Card -->
            <div class="bg-white rounded-2xl shadow-lg p-8 mb-6">
                <h2 class="text-2xl font-semibold text-gray-800 mb-6 text-center">ให้คะแนนถังขยะ</h2>
                
                <!-- Star Rating -->
                <div class="text-center mb-6">
                    <div class="flex justify-center space-x-2 mb-4">
                        <span class="star text-4xl cursor-pointer text-gray-300" data-rating="1">⭐</span>
                        <span class="star text-4xl cursor-pointer text-gray-300" data-rating="2">⭐</span>
                        <span class="star text-4xl cursor-pointer text-gray-300" data-rating="3">⭐</span>
                        <span class="star text-4xl cursor-pointer text-gray-300" data-rating="4">⭐</span>
                        <span class="star text-4xl cursor-pointer text-gray-300" data-rating="5">⭐</span>
                    </div>
                    <p class="text-gray-600 mb-4">คลิกดาวเพื่อให้คะแนน (1-5 ดาว)</p>
                    <button id="submitRating" class="bg-green-600 hover:bg-green-700 text-white px-8 py-3 rounded-full font-semibold transition-all duration-200 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed shadow-lg" disabled>
                        ส่งคะแนน
                    </button>
                </div>
            </div>

            <!-- Results Display -->
            <div class="bg-white rounded-2xl shadow-lg p-8 mb-6">
                <h3 class="text-2xl font-semibold text-gray-800 mb-6 text-center">ผลการประเมิน</h3>
                
                <!-- Summary Stats -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
                    <div class="bg-gradient-to-r from-green-500 to-yellow-400 rounded-xl p-6 text-white text-center shadow-lg">
                        <div class="text-3xl font-bold" id="averageRating">0.0</div>
                        <div class="text-lg">คะแนนเฉลี่ย</div>
                        <div class="flex justify-center mt-2" id="averageStars"></div>
                    </div>
                    <div class="bg-gradient-to-r from-green-600 to-green-400 rounded-xl p-6 text-white text-center shadow-lg">
                        <div class="text-3xl font-bold" id="totalVotes">0</div>
                        <div class="text-lg">จำนวนผู้โหวต</div>
                    </div>
                </div>

                <!-- Chart -->
                <div class="bg-gray-50 rounded-xl p-6">
                    <canvas id="ratingsChart" width="400" height="200"></canvas>
                </div>
            </div>
        </div>

        <!-- Admin Section -->
        <div class="text-center mb-6">
            <button id="adminBtn" class="bg-green-700 hover:bg-green-800 text-white px-6 py-3 rounded-full font-semibold transition-all duration-200 shadow-lg">
                🔐 เข้าสู่เมนูผู้ดูแลระบบ
            </button>
        </div>

        <!-- Admin Login Modal -->
        <div id="adminModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50">
            <div class="bg-white rounded-2xl p-8 max-w-md w-full mx-4">
                <h3 class="text-2xl font-semibold text-gray-800 mb-6 text-center">เข้าสู่ระบบผู้ดูแล</h3>
                <div class="mb-6">
                    <label class="block text-gray-700 text-sm font-medium mb-2">รหัสผ่าน:</label>
                    <input type="password" id="adminPassword" class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="ใส่รหัสผ่าน">
                </div>
                <div id="loginError" class="text-red-500 text-sm mb-4 hidden">รหัสผ่านไม่ถูกต้อง</div>
                <div class="flex space-x-4">
                    <button id="loginBtn" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-lg font-semibold transition-colors shadow-lg">
                        เข้าสู่ระบบ
                    </button>
                    <button id="cancelBtn" class="flex-1 bg-gray-300 hover:bg-gray-400 text-gray-700 py-3 rounded-lg font-semibold transition-colors">
                        ยกเลิก
                    </button>
                </div>
            </div>
        </div>

        <!-- Admin Panel -->
        <div id="adminPanel" class="hidden fade-in">
            <div class="bg-white rounded-2xl shadow-lg p-8 mb-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-semibold text-gray-800">🛠️ แผงควบคุมผู้ดูแลระบบ</h2>
                    <div class="flex space-x-3">
                        <button id="resetBtn" class="bg-yellow-500 hover:bg-yellow-600 text-white px-4 py-2 rounded-lg font-semibold transition-colors shadow-lg">
                            🔄 รีเซ็ตข้อมูล
                        </button>
                        <button id="logoutBtn" class="bg-red-500 hover:bg-red-600 text-white px-4 py-2 rounded-lg font-semibold transition-colors shadow-lg">
                            ออกจากระบบ
                        </button>
                    </div>
                </div>

                <!-- Admin Stats -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                    <div class="bg-gradient-to-r from-green-500 to-yellow-400 rounded-xl p-6 text-white text-center shadow-lg">
                        <div class="text-3xl font-bold" id="adminAverage">0.0</div>
                        <div class="text-lg">คะแนนเฉลี่ย</div>
                    </div>
                    <div class="bg-gradient-to-r from-green-600 to-green-400 rounded-xl p-6 text-white text-center shadow-lg">
                        <div class="text-3xl font-bold" id="adminTotal">0</div>
                        <div class="text-lg">จำนวนผู้โหวตทั้งหมด</div>
                    </div>
                    <div class="bg-gradient-to-r from-yellow-500 to-green-500 rounded-xl p-6 text-white text-center shadow-lg">
                        <div class="text-3xl font-bold" id="adminHighest">0</div>
                        <div class="text-lg">คะแนนสูงสุด</div>
                    </div>
                </div>

                <!-- Detailed Chart -->
                <div class="bg-gray-50 rounded-xl p-6 mb-6">
                    <h3 class="text-xl font-semibold text-gray-800 mb-4">กราฟรายละเอียดคะแนน</h3>
                    <canvas id="adminChart" width="400" height="200"></canvas>
                </div>

                <!-- Rating Breakdown -->
                <div class="bg-gray-50 rounded-xl p-6">
                    <h3 class="text-xl font-semibold text-gray-800 mb-4">รายละเอียดคะแนน</h3>
                    <div id="ratingBreakdown" class="space-y-3"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Data storage
        let ratings = JSON.parse(localStorage.getItem('trashBinRatings')) || [];
        let selectedRating = 0;
        let chart = null;
        let adminChart = null;

        // DOM elements
        const stars = document.querySelectorAll('.star');
        const submitBtn = document.getElementById('submitRating');
        const adminBtn = document.getElementById('adminBtn');
        const adminModal = document.getElementById('adminModal');
        const adminPanel = document.getElementById('adminPanel');
        const votingSection = document.getElementById('votingSection');

        // Initialize
        document.addEventListener('DOMContentLoaded', function() {
            updateDisplay();
            initializeChart();
        });

        // Star rating functionality
        stars.forEach(star => {
            star.addEventListener('click', function() {
                selectedRating = parseInt(this.dataset.rating);
                updateStars();
                submitBtn.disabled = false;
            });

            star.addEventListener('mouseenter', function() {
                const rating = parseInt(this.dataset.rating);
                highlightStars(rating);
            });
        });

        document.querySelector('.flex.justify-center.space-x-2').addEventListener('mouseleave', function() {
            updateStars();
        });

        function highlightStars(rating) {
            stars.forEach((star, index) => {
                if (index < rating) {
                    star.classList.add('active');
                } else {
                    star.classList.remove('active');
                }
            });
        }

        function updateStars() {
            highlightStars(selectedRating);
        }

        // Submit rating
        submitBtn.addEventListener('click', function() {
            if (selectedRating > 0) {
                ratings.push({
                    rating: selectedRating,
                    timestamp: new Date().toISOString()
                });
                localStorage.setItem('trashBinRatings', JSON.stringify(ratings));
                
                // Reset selection
                selectedRating = 0;
                stars.forEach(star => star.classList.remove('active'));
                submitBtn.disabled = true;
                
                // Update display
                updateDisplay();
                
                // Show success message
                showMessage('ขอบคุณสำหรับการให้คะแนน!', 'success');
            }
        });

        // Admin functionality
        adminBtn.addEventListener('click', function() {
            adminModal.classList.remove('hidden');
            adminModal.classList.add('flex');
            document.getElementById('adminPassword').focus();
        });

        document.getElementById('cancelBtn').addEventListener('click', function() {
            adminModal.classList.add('hidden');
            adminModal.classList.remove('flex');
            document.getElementById('adminPassword').value = '';
            document.getElementById('loginError').classList.add('hidden');
        });

        document.getElementById('loginBtn').addEventListener('click', function() {
            const password = document.getElementById('adminPassword').value;
            if (password === 'Win2549') {
                adminModal.classList.add('hidden');
                adminModal.classList.remove('flex');
                votingSection.classList.add('hidden');
                adminPanel.classList.remove('hidden');
                updateAdminPanel();
                document.getElementById('adminPassword').value = '';
                document.getElementById('loginError').classList.add('hidden');
            } else {
                document.getElementById('loginError').classList.remove('hidden');
            }
        });

        document.getElementById('logoutBtn').addEventListener('click', function() {
            adminPanel.classList.add('hidden');
            votingSection.classList.remove('hidden');
        });

        document.getElementById('resetBtn').addEventListener('click', function() {
            if (confirm('⚠️ คุณแน่ใจหรือไม่ที่จะรีเซ็ตข้อมูลทั้งหมด?\n\nการกระทำนี้ไม่สามารถย้อนกลับได้!')) {
                ratings = [];
                localStorage.removeItem('trashBinRatings');
                updateDisplay();
                updateAdminPanel();
                showMessage('🔄 รีเซ็ตข้อมูลเรียบร้อยแล้ว!', 'success');
            }
        });

        // Enter key for password
        document.getElementById('adminPassword').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                document.getElementById('loginBtn').click();
            }
        });

        // Update display functions
        function updateDisplay() {
            const stats = calculateStats();
            
            document.getElementById('averageRating').textContent = stats.average.toFixed(1);
            document.getElementById('totalVotes').textContent = stats.total;
            
            // Update average stars display
            const averageStars = document.getElementById('averageStars');
            averageStars.innerHTML = '';
            for (let i = 1; i <= 5; i++) {
                const star = document.createElement('span');
                star.textContent = '⭐';
                star.className = i <= Math.round(stats.average) ? 'text-yellow-400' : 'text-gray-300';
                averageStars.appendChild(star);
            }
            
            updateChart();
        }

        function updateAdminPanel() {
            const stats = calculateStats();
            
            document.getElementById('adminAverage').textContent = stats.average.toFixed(1);
            document.getElementById('adminTotal').textContent = stats.total;
            document.getElementById('adminHighest').textContent = stats.highest;
            
            updateAdminChart();
            updateRatingBreakdown();
        }

        function calculateStats() {
            if (ratings.length === 0) {
                return { average: 0, total: 0, highest: 0, distribution: [0, 0, 0, 0, 0] };
            }
            
            const total = ratings.length;
            const sum = ratings.reduce((acc, r) => acc + r.rating, 0);
            const average = sum / total;
            const highest = Math.max(...ratings.map(r => r.rating));
            
            const distribution = [0, 0, 0, 0, 0];
            ratings.forEach(r => {
                distribution[r.rating - 1]++;
            });
            
            return { average, total, highest, distribution };
        }

        function initializeChart() {
            const ctx = document.getElementById('ratingsChart').getContext('2d');
            chart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['1 ดาว', '2 ดาว', '3 ดาว', '4 ดาว', '5 ดาว'],
                    datasets: [{
                        label: 'จำนวนคะแนน',
                        data: [0, 0, 0, 0, 0],
                        backgroundColor: [
                            '#fbbf24',
                            '#f59e0b',
                            '#eab308',
                            '#65a30d',
                            '#16a34a'
                        ],
                        borderRadius: 8,
                        borderSkipped: false,
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            display: false
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                stepSize: 1
                            }
                        }
                    }
                }
            });
        }

        function updateChart() {
            const stats = calculateStats();
            chart.data.datasets[0].data = stats.distribution;
            chart.update();
        }

        function updateAdminChart() {
            if (adminChart) {
                adminChart.destroy();
            }
            
            const ctx = document.getElementById('adminChart').getContext('2d');
            const stats = calculateStats();
            
            adminChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['1 ดาว', '2 ดาว', '3 ดาว', '4 ดาว', '5 ดาว'],
                    datasets: [{
                        data: stats.distribution,
                        backgroundColor: [
                            '#fbbf24',
                            '#f59e0b',
                            '#eab308',
                            '#65a30d',
                            '#16a34a'
                        ],
                        borderWidth: 2,
                        borderColor: '#ffffff'
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        }
                    }
                }
            });
        }

        function updateRatingBreakdown() {
            const stats = calculateStats();
            const breakdown = document.getElementById('ratingBreakdown');
            breakdown.innerHTML = '';
            
            for (let i = 0; i < 5; i++) {
                const count = stats.distribution[i];
                const percentage = stats.total > 0 ? (count / stats.total * 100).toFixed(1) : 0;
                
                const item = document.createElement('div');
                item.className = 'flex items-center justify-between p-3 bg-white rounded-lg';
                item.innerHTML = `
                    <div class="flex items-center">
                        <span class="text-lg mr-2">${'⭐'.repeat(i + 1)}</span>
                        <span class="font-medium">${i + 1} ดาว</span>
                    </div>
                    <div class="flex items-center">
                        <span class="text-gray-600 mr-4">${count} คน (${percentage}%)</span>
                        <div class="w-20 h-2 bg-gray-200 rounded-full">
                            <div class="h-2 bg-green-500 rounded-full" style="width: ${percentage}%"></div>
                        </div>
                    </div>
                `;
                breakdown.appendChild(item);
            }
        }

        function showMessage(message, type) {
            const messageDiv = document.createElement('div');
            messageDiv.className = `fixed top-4 right-4 px-6 py-3 rounded-lg text-white font-semibold z-50 ${
                type === 'success' ? 'bg-green-500' : 'bg-red-500'
            }`;
            messageDiv.textContent = message;
            document.body.appendChild(messageDiv);
            
            setTimeout(() => {
                messageDiv.remove();
            }, 3000);
        }
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'964d4d62a417a1ba',t:'MTc1MzQ2Mzc1Ny4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getEleme
