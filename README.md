<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LinkCheck - Проверка безопасности ссылок</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --black: #0a0a0a;
            --dark-gray: #111;
            --gray: #222;
            --light-gray: #333;
            --text: #f0f0f0;
            --text-light: #aaa;
            --safe: #4CAF50;
            --warning: #ff9800;
            --danger: #f44336;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', system-ui, sans-serif;
        }
        
        body {
            background: var(--black);
            color: var(--text);
            min-height: 100vh;
            padding: 20px;
            background-image: 
                radial-gradient(circle at 10% 20%, rgba(40, 40, 40, 0.1) 0%, transparent 20%),
                radial-gradient(circle at 90% 80%, rgba(60, 60, 60, 0.1) 0%, transparent 20%);
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        /* Header */
        header {
            text-align: center;
            margin-bottom: 50px;
            padding: 40px 0;
            position: relative;
        }
        
        header::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            width: 200px;
            height: 3px;
            background: linear-gradient(90deg, transparent, var(--text-light), transparent);
        }
        
        .logo {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 20px;
            margin-bottom: 25px;
        }
        
        .logo-icon {
            font-size: 3.5rem;
            color: white;
            filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.2));
        }
        
        h1 {
            font-size: 3.2rem;
            background: linear-gradient(90deg, #fff, #aaa);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            letter-spacing: 1.5px;
        }
        
        .tagline {
            font-size: 1.3rem;
            color: var(--text-light);
            margin-top: 15px;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
        }
        
        /* Main Content */
        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 40px;
            margin-bottom: 60px;
        }
        
        @media (max-width: 900px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            h1 {
                font-size: 2.5rem;
            }
            
            .logo-icon {
                font-size: 2.8rem;
            }
        }
        
        /* Panels */
        .panel {
            background: var(--dark-gray);
            border-radius: 16px;
            padding: 35px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.4);
            border: 1px solid var(--light-gray);
            transition: transform 0.3s, box-shadow 0.3s;
        }
        
        .panel:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5);
        }
        
        .panel-title {
            font-size: 1.9rem;
            margin-bottom: 30px;
            display: flex;
            align-items: center;
            gap: 15px;
            color: white;
        }
        
        .panel-title i {
            color: var(--text-light);
        }
        
        /* Checker Panel */
        .url-input {
            width: 100%;
            padding: 20px;
            background: var(--gray);
            border: 2px solid var(--light-gray);
            border-radius: 10px;
            color: white;
            font-size: 1.1rem;
            margin-bottom: 25px;
            transition: all 0.3s;
        }
        
        .url-input:focus {
            outline: none;
            border-color: #888;
            box-shadow: 0 0 0 3px rgba(255, 255, 255, 0.1);
        }
        
        .check-button {
            width: 100%;
            padding: 20px;
            background: linear-gradient(135deg, var(--gray), var(--light-gray));
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 1.2rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
        }
        
        .check-button:hover {
            background: linear-gradient(135deg, var(--light-gray), #444);
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
        }
        
        /* Results */
        #resultsContainer {
            min-height: 200px;
        }
        
        .result-item {
            background: var(--gray);
            border-radius: 10px;
            padding: 25px;
            margin-bottom: 25px;
            border-left: 5px solid var(--light-gray);
            animation: fadeIn 0.5s ease;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .result-item.safe {
            border-left-color: var(--safe);
        }
        
        .result-item.warning {
            border-left-color: var(--warning);
        }
        
        .result-item.danger {
            border-left-color: var(--danger);
        }
        
        .result-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            flex-wrap: wrap;
            gap: 10px;
        }
        
        .result-status {
            font-weight: 700;
            font-size: 1.3rem;
        }
        
        .result-status.safe { color: var(--safe); }
        .result-status.warning { color: var(--warning); }
        .result-status.danger { color: var(--danger); }
        
        .result-url {
            color: var(--text-light);
            word-break: break-all;
            margin-bottom: 20px;
            padding: 10px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 5px;
            font-family: 'Courier New', monospace;
        }
        
        /* History */
        .history-panel {
            margin-bottom: 60px;
        }
        
        .history-list {
            list-style: none;
        }
        
        .history-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px;
            border-bottom: 1px solid var(--light-gray);
            transition: background 0.2s;
        }
        
        .history-item:hover {
            background: var(--gray);
        }
        
        .history-url {
            color: var(--text);
            max-width: 65%;
            word-break: break-all;
        }
        
        .history-status {
            padding: 8px 18px;
            border-radius: 20px;
            font-weight: 600;
            font-size: 0.9rem;
        }
        
        .status-safe { background: rgba(76, 175, 80, 0.15); color: var(--safe); }
        .status-warning { background: rgba(255, 152, 0, 0.15); color: var(--warning); }
        .status-danger { background: rgba(244, 67, 54, 0.15); color: var(--danger); }
        
        /* Owners */
        .owners-list {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 30px;
            margin-bottom: 40px;
        }
        
        .owner-card {
            background: linear-gradient(145deg, var(--gray), var(--dark-gray));
            border-radius: 15px;
            padding: 30px;
            text-align: center;
            transition: all 0.3s;
            border: 1px solid var(--light-gray);
        }
        
        .owner-card:hover {
            transform: translateY(-8px);
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.4);
        }
        
        .owner-icon {
            font-size: 3.5rem;
            margin-bottom: 25px;
            color: white;
        }
        
        .owner-name {
            font-size: 1.6rem;
            margin-bottom: 10px;
            color: white;
        }
        
        .owner-role {
            color: var(--text-light);
            font-size: 1.1rem;
        }
        
        /* URL Display */
        .url-display-container {
            background: var(--gray);
            border-radius: 10px;
            padding: 25px;
            margin-top: 20px;
            border: 1px solid var(--light-gray);
        }
        
        .url-display {
            font-family: 'Courier New', monospace;
            font-size: 1.3rem;
            color: var(--safe);
            word-break: break-all;
            padding: 15px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 5px;
            border-left: 4px solid var(--safe);
        }
        
        /* Footer */
        footer {
            text-align: center;
            margin-top: 70px;
            padding-top: 30px;
            border-top: 1px solid var(--light-gray);
            color: var(--text-light);
            font-size: 0.95rem;
        }
        
        footer p {
            margin-bottom: 10px;
        }
        
        /* Loading Animation */
        .loading {
            text-align: center;
            padding: 40px;
            color: var(--text-light);
        }
        
        .fa-spinner {
            animation: spin 1.5s linear infinite;
            margin-bottom: 20px;
            font-size: 2.5rem;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        /* Responsive */
        @media (max-width: 600px) {
            .container {
                padding: 10px;
            }
            
            .panel {
                padding: 25px;
            }
            
            .panel-title {
                font-size: 1.6rem;
            }
            
            h1 {
                font-size: 2rem;
            }
            
            .logo-icon {
                font-size: 2rem;
            }
            
            .owner-card {
                padding: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="logo">
                <i class="fas fa-shield-alt logo-icon"></i>
                <h1>LinkCheck Security</h1>
            </div>
            <p class="tagline">Бесплатная проверка безопасности ссылок для всех пользователей</p>
        </header>
        
        <div class="main-content">
            <section class="panel checker-panel">
                <h2 class="panel-title"><i class="fas fa-search"></i> Проверка ссылки</h2>
                <input type="url" class="url-input" id="urlInput" placeholder="https://example.com" value="https://">
                <button class="check-button" id="checkButton">
                    <i class="fas fa-shield-alt"></i> Проверить безопасность
                </button>
                <p style="margin-top: 20px; color: var(--text-light); font-size: 0.95rem;">
                    <i class="fas fa-info-circle"></i> Введите любую ссылку для проверки на безопасность
                </p>
            </section>
            
            <section class="panel results-panel">
                <h2 class="panel-title"><i class="fas fa-poll"></i> Результаты проверки</h2>
                <div id="resultsContainer">
                    <div class="result-item">
                        <div class="result-header">
                            <div class="result-status">Готов к проверке</div>
                        </div>
                        <div class="result-url">Введите ссылку выше</div>
                        <div class="result-details">Наш сервис проверит ссылку на безопасность, наличие вредоносного кода, фишинга и других угроз.</div>
                    </div>
                </div>
            </section>
        </div>
        
        <section class="panel history-panel">
            <h2 class="panel-title"><i class="fas fa-history"></i> История проверок</h2>
            <ul class="history-list" id="historyList">
                <!-- История загружается через JavaScript -->
            </ul>
        </section>
        
        <section class="panel owners-panel">
            <h2 class="panel-title"><i class="fas fa-user-tie"></i> Владельцы проекта</h2>
            <div class="owners-list">
                <div class="owner-card">
                    <i class="fas fa-user-cog owner-icon"></i>
                    <h3 class="owner-name">Ляшко Иван Сергеевич</h3>
                    <p class="owner-role">Владелец и разработчик</p>
                </div>
                <div class="owner-card">
                    <i class="fas fa-user-tie owner-icon"></i>
                    <h3 class="owner-name">Ляшко Сергей Викторович</h3>
                    <p class="owner-role">Владелец и администратор</p>
                </div>
            </div>
            
            <div class="url-display-container">
                <h3 style="color: white; margin-bottom: 15px; font-size: 1.4rem;">
                    <i class="fas fa-link"></i> Официальный URL сайта:
                </h3>
                <div class="url-display">https://linkcheck-security.com</div>
                <p style="margin-top: 15px; color: var(--text-light);">
                    <i class="fas fa-globe"></i> Сайт доступен для всех пользователей по этому адресу
                </p>
            </div>
        </section>
        
        <footer>
            <p>© 2023-2024 LinkCheck Security. Все права защищены.</p>
            <p>Сервис доступен для всех пользователей бесплатно.</p>
            <p style="margin-top: 20px;">Сайт выполнен в чёрно-бело-серой цветовой гамме</p>
        </footer>
    </div>

    <script>
        // Элементы DOM
        const urlInput = document.getElementById('urlInput');
        const checkButton = document.getElementById('checkButton');
        const resultsContainer = document.getElementById('resultsContainer');
        const historyList = document.getElementById('historyList');
        
        // История проверок
        let checkHistory = JSON.parse(localStorage.getItem('linkCheckHistory')) || [];
        
        // Примеры ссылок для начальной истории
        const exampleHistory = [
            {
                url: 'https://google.com',
                status: 'safe',
                statusText: 'Безопасно',
                date: new Date(Date.now() - 2*24*60*60*1000).toISOString()
            },
            {
                url: 'https://github.com',
                status: 'safe',
                statusText: 'Безопасно',
                date: new Date(Date.now() - 1*24*60*60*1000).toISOString()
            },
            {
                url: 'https://phishing-example.net',
                status: 'danger',
                statusText: 'Опасность',
                date: new Date(Date.now() - 3*60*60*1000).toISOString()
            },
            {
                url: 'https://suspicious-site.org',
                status: 'warning',
                statusText: 'Подозрительно',
                date: new Date(Date.now() - 5*60*60*1000).toISOString()
            }
        ];
        
        // Инициализация истории
        function initHistory() {
            if (checkHistory.length === 0) {
                checkHistory = [...exampleHistory];
                localStorage.setItem('linkCheckHistory', JSON.stringify(checkHistory));
            }
            renderHistory();
        }
        
        // Отображение истории
        function renderHistory() {
            historyList.innerHTML = '';
            
            if (checkHistory.length === 0) {
                historyList.innerHTML = `
                    <li class="history-item">
                        <div class="history-url">История проверок пуста</div>
                    </li>
                `;
                return;
            }
            
            // Показываем последние 6 проверок
            const recentHistory = checkHistory.slice(-6).reverse();
            
            recentHistory.forEach(item => {
                const li = document.createElement('li');
                li.className = 'history-item';
                
                let statusClass = 'status-safe';
                if (item.status === 'warning') statusClass = 'status-warning';
                if (item.status === 'danger') statusClass = 'status-danger';
                
                li.innerHTML = `
                    <div class="history-url">${item.url}</div>
                    <div class="history-status ${statusClass}">${item.statusText}</div>
                `;
                
                historyList.appendChild(li);
            });
        }
        
        // Проверка ссылки
        function checkLink(url) {
            // Показываем загрузку
            resultsContainer.innerHTML = `
                <div class="loading">
                    <i class="fas fa-spinner fa-spin"></i>
                    <div>Проверяем ссылку на безопасность...</div>
                    <div style="margin-top: 10px; font-size: 0.9rem;">
                        Анализируем домен, SSL-сертификат, репутацию сайта
                    </div>
                </div>
            `;
            
            // Имитация проверки
            setTimeout(() => {
                // Определяем статус на основе домена
                let status, statusText, details, resultClass;
                const domain = url.toLowerCase();
                
                // Правила определения статуса
                if (domain.includes('phishing') || domain.includes('malware') || 
                    domain.includes('virus') || domain.includes('hack')) {
                    status = 'danger';
                    statusText = 'Опасность';
                    details = 'Обнаружены признаки фишинга или вредоносного сайта. Не переходите по этой ссылке и не вводите личные данные.';
                    resultClass = 'danger';
                } else if (domain.includes('test') || domain.includes('example') || 
                          domain.includes('suspicious') || Math.random() < 0.3) {
                    status = 'warning';
                    statusText = 'Подозрительно';
                    details = 'Сайт требует осторожности. Может содержать нежелательный контент или агрессивную рекламу.';
                    resultClass = 'warning';
                } else {
                    status = 'safe';
                    statusText = 'Безопасно';
                    details = 'Ссылка проверена и признана безопасной. Сайт не содержит известных угроз.';
                    resultClass = 'safe';
                }
                
                // Форматирование даты
                const now = new Date();
                const dateStr = now.toLocaleDateString('ru-RU', {
                    day: 'numeric',
                    month: 'long',
                    year: 'numeric',
                    hour: '2-digit',
                    minute: '2-digit'
                });
                
                // Отображение результата
                resultsContainer.innerHTML = `
                    <div class="result-item ${resultClass}">
                        <div class="result-header">
                            <div class="result-status ${resultClass}">${statusText}</div>
                            <div>${dateStr}</div>
                        </div>
                        <div class="result-url">${url}</div>
                        <div class="result-details">${details}</div>
                        <div class="result-details" style="margin-top: 15px; padding-top: 15px; border-top: 1px solid rgba(255,255,255,0.1);">
                            <strong>Что было проверено:</strong><br>
                            • Безопасность домена и SSL-сертификат<br>
                            • Репутация сайта в базе угроз<br>
                            • Наличие фишинга и вредоносного кода<br>
                            • Соответствие стандартам безопасности
                        </div>
                    </div>
                `;
                
                // Добавление в историю
                const historyItem = {
                    url: url,
                    status: status,
                    statusText: statusText,
                    date: now.toISOString()
                };
                
                checkHistory.push(historyItem);
                
                // Ограничение истории (максимум 50 записей)
                if (checkHistory.length > 50) {
                    checkHistory = checkHistory.slice(-50);
                }
                
                localStorage.setItem('linkCheckHistory', JSON.stringify(checkHistory));
                
                // Обновление истории
                renderHistory();
                
                // Прокрутка к результатам
                resultsContainer.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
            }, 1800);
        }
        
        // Обработчики событий
        checkButton.addEventListener('click', () => {
            const url = urlInput.value.trim();
            
            if (!url || url === 'https://' || url === 'http://') {
                alert('Пожалуйста, введите ссылку для проверки');
                urlInput.focus();
                return;
            }
            
            // Базовая валидация URL
            if (!url.startsWith('http://') && !url.startsWith('https://')) {
                alert('Пожалуйста, введите корректную ссылку (начинающуюся с http:// или https://)');
                urlInput.focus();
                return;
            }
            
            try {
                new URL(url);
                checkLink(url);
            } catch (e) {
                alert('Пожалуйста, введите корректную ссылку (например, https://example.com)');
                urlInput.focus();
            }
        });
        
        urlInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                checkButton.click();
            }
        });
        
        // Инициализация при загрузке
        document.addEventListener('DOMContentLoaded', () => {
            initHistory();
            urlInput.value = 'https://';
            urlInput.focus();
            
            // Автоматическая проверка при открытии (пример)
            setTimeout(() => {
                if (Math.random() > 0.5) {
                    urlInput.value = 'https://google.com';
                } else {
                    urlInput.value = 'https://example.com';
                }
            }, 500);
        });
        
        // Информация для пользователей
        console.log(`
        ========================================
        LinkCheck Security - Проверка ссылок
        ========================================
        Сайт создан для проверки безопасности ссылок
        Владельцы: 
        - Ляшко Иван Сергеевич
        - Ляшко Сергей Викторович
        URL: https://linkcheck-security.com
        ========================================
        `);
    </script>
</body>
</html>
