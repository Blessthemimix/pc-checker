<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Проверка характеристик ПК</title>
    <style>
        body { font-family: sans-serif; padding: 20px; }
        #output { 
            margin-top: 20px; 
            padding: 10px; 
            border: 1px solid #ccc; 
            background-color: #f4f4f4;
            white-space: pre-wrap;
            font-family: monospace;
        }
        button { padding: 10px 15px; cursor: pointer; }
    </style>
</head>
<body>
    <h1>📋 Характеристики ПК пользователя</h1>
    <p>Нажмите кнопку, чтобы получить доступные данные о системе. Скопируйте результат и вставьте его в терминал VS Code.</p>
    
    <button onclick="getSystemInfo()">Получить информацию</button>
    
    <h3>Результат для терминала (скопируйте ниже):</h3>
    <div id="output">Нажмите кнопку...</div>

    <script>
        // Функция для получения информации о GPU (рендерере)
        function getGPUInfo(gl) {
            const debugInfo = gl.getExtension('WEBGL_debug_renderer_info');
            return gl.getParameter(debugInfo.UNMASKED_RENDERER_WEBGL);
        }

        async function getSystemInfo() {
            const outputDiv = document.getElementById('output');
            outputDiv.textContent = 'Сбор данных...';

            let cpuInfo = 'CPU: Неизвестно';
            let gpuInfo = 'GPU: Неизвестно';
            let ipAddress = 'IP: Получение...';

            // 1. Получение информации о CPU
            // (доступно только количество ядер)
            if (navigator.hardwareConcurrency) {
                cpuInfo = `CPU: Количество ядер: ${navigator.hardwareConcurrency}`;
            }

            // 2. Получение информации о GPU (WebGL Renderer)
            try {
                const canvas = document.createElement('canvas');
                const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');
                if (gl) {
                    gpuInfo = `GPU: ${getGPUInfo(gl)}`;
                }
            } catch (e) {
                gpuInfo = 'GPU: Не удалось получить (ошибка WebGL)';
            }
            
            // 3. Получение публичного IP-адреса через внешний API
            try {
                const response = await fetch('https://api.ipify.org/?format=json');
                const data = await response.json();
                ipAddress = `IP: ${data.ip}`;
            } catch (error) {
                ipAddress = 'IP: Не удалось получить';
            }

            // Форматирование вывода
            const finalOutput = `
${gpuInfo}
${cpuInfo}
${ipAddress}
            `.trim();
            
            // Вывод в HTML
            outputDiv.textContent = finalOutput;
            console.log("Результат для терминала VS Code:\n" + finalOutput);

            // Опционально: автоматическое копирование
            // navigator.clipboard.writeText(finalOutput).then(() => {
            //     alert('Информация скопирована в буфер обмена!');
            // });
        }
    </script>
</body>
</html>
