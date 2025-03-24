# -‏<!DOCTYPE html>
‏<html lang="ar">
‏<head>
‏    <meta charset="UTF-8">
‏    <meta name="viewport" content="width=device-width, initial-scale=1.0">
‏    <title>لعبة الحروف الخلية</title>
‏    <style>
‏        body { font-family: Arial, sans-serif; text-align: center; direction: rtl; }
‏        .grid { display: grid; grid-template-columns: repeat(5, 60px); gap: 5px; margin: 20px auto; width: fit-content; }
‏        .cell { width: 60px; height: 60px; background: lightgray; text-align: center; line-height: 60px; font-size: 24px; cursor: pointer; border-radius: 10px; }
‏        .controls { margin: 20px; }
‏        button { padding: 10px; margin: 5px; font-size: 16px; cursor: pointer; }
‏        .team-box { display: inline-block; margin: 10px; padding: 10px; border: 1px solid #ccc; border-radius: 10px; }
‏        .buzzer-btn { font-size: 18px; padding: 15px; width: 150px; }
‏    </style>
‏</head>
‏<body>
‏    <h2>لعبة الحروف الخلية</h2>
    
    <!-- إدخال بيانات الفرق -->
‏    <div class="controls">
‏        <div class="team-box">
‏            <h3>الفريق الأول</h3>
‏            <input type="text" id="team1-name" placeholder="اسم الفريق الأول">
‏            <input type="color" id="team1-color" value="#ff0000">
‏        </div>
‏        <div class="team-box">
‏            <h3>الفريق الثاني</h3>
‏            <input type="text" id="team2-name" placeholder="اسم الفريق الثاني">
‏            <input type="color" id="team2-color" value="#0000ff">
‏        </div>
‏        <button onclick="startGame()">ابدأ اللعبة</button>
‏        <button onclick="resetGame()">🔄 إعادة بدء اللعبة</button>
‏    </div>

‏    <div class="controls">
‏        <p id="turn">يرجى اختيار حرف...</p>
‏    </div>

‏    <div class="grid" id="grid">
        <!-- سيتم إنشاء الحروف تلقائيًا هنا -->
‏    </div>

    <!-- أزرار الجرس لكل فريق -->
‏    <div class="controls">
‏        <button class="buzzer-btn" id="buzzer-team1" onclick="buzz('team1')" disabled>🔔 جرس <span id="team1-label">الفريق الأول</span></button>
‏        <button class="buzzer-btn" id="buzzer-team2" onclick="buzz('team2')" disabled>🔔 جرس <span id="team2-label">الفريق الثاني</span></button>
‏    </div>

‏    <script>
‏        let team1 = { name: "", color: "" };
‏        let team2 = { name: "", color: "" };
‏        let selectedCell = null; // الحرف المختار
‏        let currentTeam = ""; // الفريق الذي ضغط على الجرس
‏        let buzzerPressed = false;
‏        let letters = ["أ", "ب", "ت", "ث", "ج", "ح", "خ", "د", "ذ", "ر", "ز", "س", "ش", "ص", "ض", "ط", "ظ", "ع", "غ", "ف", "ق", "ك", "ل", "م", "ن"];

‏        function startGame() {
‏            team1.name = document.getElementById("team1-name").value || "فريق 1";
‏            team1.color = document.getElementById("team1-color").value;
            
‏            team2.name = document.getElementById("team2-name").value || "فريق 2";
‏            team2.color = document.getElementById("team2-color").value;

‏            document.getElementById("team1-label").innerText = team1.name;
‏            document.getElementById("team2-label").innerText = team2.name;
‏            document.getElementById("turn").innerText = "يرجى اختيار حرف...";
            
‏            createGrid();
        }

‏        function createGrid() {
‏            let grid = document.getElementById("grid");
‏            grid.innerHTML = ""; // مسح الشبكة السابقة

‏            for (let letter of letters) {
‏                let cell = document.createElement("div");
‏                cell.classList.add("cell");
‏                cell.innerText = letter;
‏                cell.onclick = function () { selectLetter(cell, letter); };
‏                grid.appendChild(cell);
            }

            // تعطيل الجرس عند بداية اللعبة
‏            document.getElementById("buzzer-team1").disabled = true;
‏            document.getElementById("buzzer-team2").disabled = true;
        }

‏        function selectLetter(cell, letter) {
‏            if (selectedCell) {
‏                selectedCell.style.border = "none"; // إزالة التحديد عن الحرف السابق
            }

‏            selectedCell = cell;
‏            cell.style.border = "3px solid black"; // تمييز الحرف المختار

‏            document.getElementById("turn").innerText = `تم اختيار الحرف "${letter}". اضغطوا على الجرس للإجابة!`;

            // تفعيل أزرار الجرس
‏            document.getElementById("buzzer-team1").disabled = false;
‏            document.getElementById("buzzer-team2").disabled = false;
‏            buzzerPressed = false;
        }

‏        function buzz(team) {
‏            if (buzzerPressed || !selectedCell) return; // لا يسمح بالضغط قبل اختيار الحرف
‏            buzzerPressed = true;
            
‏            currentTeam = team;
‏            let teamName = team === "team1" ? team1.name : team2.name;

‏            document.getElementById("turn").innerText = `الدور الآن لفريق: ${teamName}. أجب عن السؤال!`;

            // تعطيل أزرار الجرس لمنع الفريق الآخر من الضغط
‏            document.getElementById("buzzer-team1").disabled = true;
‏            document.getElementById("buzzer-team2").disabled = true;

‏            askQuestion(selectedCell);
        }

‏        function askQuestion(cell) {
‏            let letter = cell.innerText;
‏            let answer = prompt(`اذكر كلمة تبدأ بحرف "${letter}"`);

‏            if (answer && answer.startsWith(letter)) {
‏                let teamColor = currentTeam === "team1" ? team1.color : team2.color;
‏                cell.style.background = teamColor;
‏                cell.onclick = null; // منع إعادة اختيار الخلية
‏                cell.style.border = "none";
‏            } else {
‏                alert("إجابة خاطئة! يمكن للفريق الآخر المحاولة.");
            }

            // إعادة تهيئة الجولة
‏            document.getElementById("turn").innerText = "يرجى اختيار حرف جديد...";
‏            selectedCell = null;
‏            buzzerPressed = false;
            
            // إعادة تفعيل أزرار الجرس للجولة القادمة
‏            document.getElementById("buzzer-team1").disabled = true;
‏            document.getElementById("buzzer-team2").disabled = true;
        }

‏        function resetGame() {
‏            createGrid(); // إعادة إنشاء الحروف
‏            document.getElementById("turn").innerText = "تمت إعادة اللعبة! يرجى اختيار حرف...";
            
            // تعطيل الجرس بعد إعادة التشغيل
‏            document.getElementById("buzzer-team1").disabled = true;
‏            document.getElementById("buzzer-team2").disabled = true;
        }

        // ربط البلوتوث باستخدام Web Bluetooth API
‏        async function connectBluetooth() {
‏            try {
‏                const device = await navigator.bluetooth.requestDevice({
‏                    acceptAllDevices: true,
‏                    optionalServices: ['battery_service'] // خدمة يمكن استخدامها عند الاتصال بالجهاز
                });

‏                const server = await device.gatt.connect();
‏                const service = await server.getPrimaryService('battery_service');
‏                const characteristic = await service.getCharacteristic('battery_level');
‏                const value = await characteristic.readValue();

‏                console.log('Battery Level: ' + value.getUint8(0));
                
                // في هذه الخطوة، يمكنك إرسال الإشارة عند تلقي ضغط الجرس
‏                alert("تم الاتصال بالجهاز عبر البلوتوث!");

‏            } catch (error) {
‏                console.error('Error: ' + error);
            }
        }

        // عند الضغط على الجرس من الهاتف باستخدام البلوتوث
‏        function buzzWithBluetooth() {
‏            connectBluetooth(); // الاتصال بالجهاز عبر البلوتوث
            // بعد الاتصال بالجهاز يمكنك إضافة منطقك لتحليل ضغط الجرس
‏            alert("تم الضغط على الجرس عبر البلوتوث!");
        }

        // إضافة زر للاتصال عبر البلوتوث
‏        document.getElementById("buzzer-team1").onclick = buzzWithBluetooth;
‏        document.getElementById("buzzer-team2").onclick = buzzWithBluetooth;
‏    </script>
‏</body>
‏</html>
