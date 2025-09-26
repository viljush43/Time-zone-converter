<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wave 4 time converter</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/feather-icons"></script>
    <script src="https://cdn.jsdelivr.net/npm/feather-icons/dist/feather.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.29.1/moment.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/moment-timezone/0.5.33/moment-timezone-with-data.min.js"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#3b82f6',
                        secondary: '#10b981',
                        dark: '#1e293b',
                        light: '#f8fafc'
                    }
                }
            }
        }
    </script>
    <style>
        .time-card {
            transition: all 0.3s ease;
        }
        .time-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
        }
        .day-night-indicator {
            width: 100%;
            height: 8px;
            border-radius: 4px;
            background: linear-gradient(90deg, #1e3a8a 0%, #3b82f6 50%, #f59e0b 100%);
            position: relative;
        }
        .indicator-marker {
            width: 16px;
            height: 16px;
            border-radius: 50%;
            position: absolute;
            top: -4px;
            transform: translateX(-50%);
        }
        .timeline-bar {
            height: 6px;
            border-radius: 3px;
            background: linear-gradient(90deg, #10b981, #3b82f6);
            position: relative;
        }
        .timeline-marker {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            position: absolute;
            top: -3px;
            transform: translateX(-50%);
        }
    </style>
</head>
<body class="bg-gray-50 text-gray-900 dark:bg-gray-900 dark:text-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <!-- Header -->
        <header class="text-center mb-12">
            <h1 class="text-4xl font-bold text-primary mb-2">Pinoy Time Voyager</h1>
            <p class="text-lg text-gray-600 dark:text-gray-400">Convert Philippine Time to US Time Zones</p>
        </header>

        <!-- Main Content -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
            <!-- Input Section -->
            <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg p-6 time-card">
                <h2 class="text-2xl font-semibold mb-6 text-primary flex items-center">
                    <i data-feather="clock" class="mr-2"></i> Philippine Time
                </h2>
                
                <div class="mb-6">
                    <label class="block text-gray-700 dark:text-gray-300 mb-2">Current PHT</label>
                    <div class="text-4xl font-bold" id="currentPHT">Loading...</div>
                    <div class="text-gray-500 dark:text-gray-400" id="currentPHTDate"></div>
                </div>

                <div class="mb-6">
                    <label class="block text-gray-700 dark:text-gray-300 mb-2">Custom Time</label>
                    <input type="time" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-lg bg-white dark:bg-gray-700" id="customPHT">
                </div>

                <div class="mb-6">
                    <label class="block text-gray-700 dark:text-gray-300 mb-2">Day/Night Indicator</label>
                    <div class="day-night-indicator">
                        <div class="indicator-marker bg-yellow-500 shadow-lg" id="phtIndicator"></div>
                    </div>
                    <div class="flex justify-between text-xs text-gray-500 dark:text-gray-400 mt-1">
                        <span>12 AM</span>
                        <span>6 AM</span>
                        <span>12 PM</span>
                        <span>6 PM</span>
                        <span>12 AM</span>
                    </div>
                </div>
            </div>

            <!-- Output Section -->
            <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg p-6 time-card">
                <h2 class="text-2xl font-semibold mb-6 text-secondary flex items-center">
                    <i data-feather="map-pin" class="mr-2"></i> US Time Zone
                </h2>
                
                <div class="mb-6">
                    <label class="block text-gray-700 dark:text-gray-300 mb-2">Select Time Zone</label>
                    <select class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-lg bg-white dark:bg-gray-700" id="timezoneSelect">
                        <option value="America/New_York">Eastern Time (ET)</option>
                        <option value="America/Chicago">Central Time (CT)</option>
                        <option value="America/Denver">Mountain Time (MT)</option>
                        <option value="America/Los_Angeles">Pacific Time (PT)</option>
                        <option value="America/Anchorage">Alaska Time (AKT)</option>
                        <option value="Pacific/Honolulu">Hawaii-Aleutian Time (HAT)</option>
                    </select>
                    <div class="text-sm text-gray-500 dark:text-gray-400 mt-1" id="dstIndicator"></div>
                </div>

                <div class="mb-6">
                    <label class="block text-gray-700 dark:text-gray-300 mb-2">Converted Time</label>
                    <div class="text-4xl font-bold" id="convertedTime">--:--</div>
                    <div class="text-gray-500 dark:text-gray-400" id="convertedDate"></div>
                    <div class="text-sm mt-1" id="dayDifference"></div>
                </div>

                <div class="mb-6">
                    <label class="block text-gray-700 dark:text-gray-300 mb-2">Time Comparison</label>
                    <div class="timeline-bar">
                        <div class="timeline-marker bg-primary" id="phtTimeline"></div>
                        <div class="timeline-marker bg-secondary" id="usTimeline"></div>
                    </div>
                    <div class="flex justify-between text-xs text-gray-500 dark:text-gray-400 mt-1">
                        <span>PH Time</span>
                        <span>US Time</span>
                    </div>
                </div>

                <div class="flex items-center justify-between">
                    <div>
                        <label class="inline-flex items-center">
                            <input type="checkbox" class="rounded border-gray-300 text-primary focus:ring-primary" id="toggleFormat">
                            <span class="ml-2 text-gray-700 dark:text-gray-300">12-hour format</span>
                        </label>
                    </div>
                    <button id="refreshBtn" class="px-4 py-2 bg-primary text-white rounded-lg hover:bg-primary-600 transition">
                        <i data-feather="refresh-cw" class="w-4 h-4 mr-1"></i> Refresh
                    </button>
                </div>
            </div>
        </div>

        <!-- Visualization Section -->
        <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg p-6 mt-8">
            <h2 class="text-2xl font-semibold mb-6 text-primary flex items-center">
                <i data-feather="globe" class="mr-2"></i> Time Zone Visualization
            </h2>
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="bg-gray-100 dark:bg-gray-700 rounded-lg p-4 h-64 flex items-center justify-center">
                    <div class="text-center">
                        <div class="w-32 h-32 rounded-full border-4 border-primary mx-auto mb-4 relative overflow-hidden">
                            <div class="absolute inset-0 bg-blue-100 dark:bg-blue-900" id="phSunlight"></div>
                            <div class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-16 h-16 rounded-full bg-yellow-400"></div>
                        </div>
                        <div class="font-medium">Philippines</div>
                        <div class="text-sm text-gray-500 dark:text-gray-400" id="phDayNight"></div>
                    </div>
                </div>
                
                <div class="bg-gray-100 dark:bg-gray-700 rounded-lg p-4 h-64 flex items-center justify-center">
                    <div class="text-center">
                        <div class="w-32 h-32 rounded-full border-4 border-secondary mx-auto mb-4 relative overflow-hidden">
                            <div class="absolute inset-0 bg-blue-100 dark:bg-blue-900" id="usSunlight"></div>
                            <div class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-16 h-16 rounded-full bg-yellow-400"></div>
                        </div>
                        <div class="font-medium" id="usLocationName">US Time Zone</div>
                        <div class="text-sm text-gray-500 dark:text-gray-400" id="usDayNight"></div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Footer -->
        <footer class="text-center mt-12 text-gray-500 dark:text-gray-400 text-sm">
            <p>© 2023 Pinoy Time Voyager. All time calculations account for Daylight Saving Time where applicable.</p>
        </footer>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            feather.replace();
            
            // Initialize time and timezone elements
            const currentPHT = document.getElementById('currentPHT');
            const currentPHTDate = document.getElementById('currentPHTDate');
            const customPHT = document.getElementById('customPHT');
            const timezoneSelect = document.getElementById('timezoneSelect');
            const convertedTime = document.getElementById('convertedTime');
            const convertedDate = document.getElementById('convertedDate');
            const dstIndicator = document.getElementById('dstIndicator');
            const dayDifference = document.getElementById('dayDifference');
            const toggleFormat = document.getElementById('toggleFormat');
            const refreshBtn = document.getElementById('refreshBtn');
            const phtIndicator = document.getElementById('phtIndicator');
            const phSunlight = document.getElementById('phSunlight');
            const usSunlight = document.getElementById('usSunlight');
            const phDayNight = document.getElementById('phDayNight');
            const usDayNight = document.getElementById('usDayNight');
            const usLocationName = document.getElementById('usLocationName');
            const phtTimeline = document.getElementById('phtTimeline');
            const usTimeline = document.getElementById('usTimeline');

            let is12HourFormat = false;
            let customTimeSet = false;
            let phTime = moment().tz('Asia/Manila');
            let usTime = moment().tz(timezoneSelect.value);

            // Update time display
            function updateTimeDisplay() {
                const format = is12HourFormat ? 'h:mm A' : 'HH:mm';
                
                if (!customTimeSet) {
                    phTime = moment().tz('Asia/Manila');
                    currentPHT.textContent = phTime.format(format);
                    currentPHTDate.textContent = phTime.format('dddd, MMMM D, YYYY');
                }

                usTime = phTime.clone().tz(timezoneSelect.value);
                convertedTime.textContent = usTime.format(format);
                convertedDate.textContent = usTime.format('dddd, MMMM D, YYYY');

                // Check for day difference
                const phDate = phTime.format('YYYY-MM-DD');
                const usDate = usTime.format('YYYY-MM-DD');
                
                if (phDate !== usDate) {
                    const diff = phTime.isBefore(usTime) ? 'next day' : 'previous day';
                    dayDifference.textContent = `(${diff})`;
                    dayDifference.className = 'text-sm mt-1 text-yellow-600 dark:text-yellow-400';
                } else {
                    dayDifference.textContent = '(same day)';
                    dayDifference.className = 'text-sm mt-1 text-green-600 dark:text-green-400';
                }

                // Update DST indicator
                const isDST = usTime.isDST();
                dstIndicator.textContent = isDST ? 
                    '⏰ Currently observing Daylight Saving Time' : 
                    '⏳ Not currently observing Daylight Saving Time';
                dstIndicator.className = `text-sm mt-1 ${isDST ? 'text-green-600 dark:text-green-400' : 'text-gray-500 dark:text-gray-400'}`;

                // Update location name
                const tzNames = {
                    'America/New_York': 'Eastern Time',
                    'America/Chicago': 'Central Time',
                    'America/Denver': 'Mountain Time',
                    'America/Los_Angeles': 'Pacific Time',
                    'America/Anchorage': 'Alaska Time',
                    'Pacific/Honolulu': 'Hawaii-Aleutian Time'
                };
                usLocationName.textContent = tzNames[timezoneSelect.value] + ' (US)';

                // Update day/night indicators
                updateDayNightIndicators();
            }

            // Update day/night visualization
            function updateDayNightIndicators() {
                // PH Time indicator
                const phHours = phTime.hours();
                const phMinutes = phTime.minutes();
                const phTotalMinutes = phHours * 60 + phMinutes;
                const phPosition = (phTotalMinutes / 1440) * 100;
                phtIndicator.style.left = `${phPosition}%`;
                
                // US Time indicator
                const usHours = usTime.hours();
                const usMinutes = usTime.minutes();
                const usTotalMinutes = usHours * 60 + usMinutes;
                const usPosition = (usTotalMinutes / 1440) * 100;
                
                // Timeline markers
                phtTimeline.style.left = '25%';
                usTimeline.style.left = '75%';

                // Sunlight visualization for PH
                const phSunPosition = ((phHours + phMinutes/60) / 24) * 360;
                const phIsDay = phHours >= 6 && phHours < 18;
                phSunlight.style.background = phIsDay ? 
                    `conic-gradient(from ${phSunPosition - 90}deg, #38bdf8 0%, #7dd3fc 50%, #38bdf8 100%)` :
                    `conic-gradient(from ${phSunPosition - 90}deg, #1e3a8a 0%, #1e40af 50%, #1e3a8a 100%)`;
                phDayNight.textContent = phIsDay ? '☀️ Daytime' : '🌙 Nighttime';

                // Sunlight visualization for US
                const usSunPosition = ((usHours + usMinutes/60) / 24) * 360;
                const usIsDay = usHours >= 6 && usHours < 18;
                usSunlight.style.background = usIsDay ? 
                    `conic-gradient(from ${usSunPosition - 90}deg, #38bdf8 0%, #7dd3fc 50%, #38bdf8 100%)` :
                    `conic-gradient(from ${usSunPosition - 90}deg, #1e3a8a 0%, #1e40af 50%, #1e3a8a 100%)`;
                usDayNight.textContent = usIsDay ? '☀️ Daytime' : '🌙 Nighttime';
            }

            // Event listeners
            customPHT.addEventListener('change', function() {
                if (this.value) {
                    const [hours, minutes] = this.value.split(':');
                    phTime = moment().tz('Asia/Manila').set({
                        hour: parseInt(hours),
                        minute: parseInt(minutes),
                        second: 0,
                        millisecond: 0
                    });
                    customTimeSet = true;
                } else {
                    customTimeSet = false;
                }
                updateTimeDisplay();
            });

            timezoneSelect.addEventListener('change', function() {
                updateTimeDisplay();
            });

            toggleFormat.addEventListener('change', function() {
                is12HourFormat = this.checked;
                updateTimeDisplay();
            });

            refreshBtn.addEventListener('click', function() {
                customPHT.value = '';
                customTimeSet = false;
                updateTimeDisplay();
            });

            // Initialize and update every second
            updateTimeDisplay();
            setInterval(updateTimeDisplay, 1000);
        });
    </script>
</body>
</html>
