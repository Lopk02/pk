<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>iPhone Chat</title> 
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans+Thai:wght@400&display=swap" rel="stylesheet">
    <style> 
        body { 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            height: 100vh; 
            background-color: #d1d5db; 
            margin: 0; 
            font-family: 'IBM Plex Sans Thai', sans-serif;
        } 
        .phone { 
            width: 280px; 
            height: 560px; 
            background: black; 
            border-radius: 40px; 
            border: 12px solid #111; 
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3); 
            display: flex; 
            flex-direction: column; 
            position: relative; 
            overflow: hidden; 
            cursor: pointer; 
        } 
        .notch { 
            width: 150px; 
            height: 30px; 
            background: #222; 
            position: absolute; 
            top: 0; 
            left: 50%; 
            transform: translateX(-50%); 
            border-bottom-left-radius: 15px; 
            border-bottom-right-radius: 15px; 
        } 
        .status-bar {
            width: 100%;
            height: 30px;
            position: absolute;
            top: 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 10px;
            color: white;
            font-size: 12px;
            font-family: 'IBM Plex Sans Thai', sans-serif;
        }
        .time {
            text-align: left;
        }
        .battery {
            display: flex;
            align-items: center;
        }
        .battery::before {
            content: '';
            display: inline-block;
            width: 20px;
            height: 10px;
            border: 1px solid white;
            border-radius: 2px;
            margin-right: 2px;
            position: relative;
        }
        .battery::after {
            content: '';
            display: inline-block;
            width: 3px;
            height: 4px;
            background: white;
            position: absolute;
            top: 3px;
            right: 0;
        }
        .chat-container { 
            flex-grow: 1; 
            padding: 10px; 
            display: flex; 
            flex-direction: column; 
            align-items: flex-start; 
            gap: 6px; 
            color: white; 
            overflow-y: auto; 
            margin-top: 40px; 
            margin-bottom: 50px; 
            font-size: 12px;
            scrollbar-width: thin; /* Firefox */
        } 
        .chat-container::-webkit-scrollbar {
            width: 5px; /* Width of the scrollbar */
        }
        .chat-container::-webkit-scrollbar-track {
            background: transparent; /* Background of the scrollbar track */
        }
        .chat-container::-webkit-scrollbar-thumb {
            background: #888; /* Color of the scrollbar thumb */
            border-radius: 10px; /* Rounded corners of the scrollbar thumb */
        }
        .chat-container::-webkit-scrollbar-thumb:hover {
            background: #555; /* Color of the scrollbar thumb when hovered */
        }
        .message { 
            display: flex; 
            align-items: center; 
            padding: 8px; 
            border-radius: 8px; 
            max-width: 75%; 
            opacity: 0; 
            transform: translateY(8px); 
            animation: fadeIn 0.3s forwards; 
        } 
        @keyframes fadeIn { 
            to { 
                opacity: 1; 
                transform: translateY(0); 
            } 
        } 
        .user { 
            background: #3b82f6; 
            align-self: flex-end; 
        } 
        .bot { 
            background: #555; 
            align-self: flex-start; 
        } 
        .bottom-bar { 
            width: 80px; 
            height: 4px; 
            background: #555; 
            border-radius: 5px; 
            position: absolute; 
            bottom: 6px; 
            left: 50%; 
            transform: translateX(-50%); 
        }
        .food-button { 
            display: none; 
            position: absolute; 
            bottom: 60px; 
            left: 50%; 
            transform: translateX(-50%); 
            padding: 10px 20px; 
            background-color: #3b82f6; 
            color: white; 
            border: none; 
            border-radius: 5px; 
            cursor: pointer; 
            font-size: 14px; 
        }
        @media (min-width: 600px) {
            .phone {
                width: 320px;
                height: 640px;
            }
        }
        @media (min-width: 768px) {
            .phone {
                width: 360px;
                height: 720px;
            }
        }
        @media (min-width: 1024px) {
            .phone {
                width: 400px;
                height: 800px;
            }
        }
    </style>
</head>
<body>
    <div class="phone" onclick="nextMessage()">
        <div class="notch"></div>
        <div class="status-bar">
            <span class="time" id="time">17:48</span>
            <span class="battery">78%</span>
        </div>
        <div id="chat" class="chat-container"></div>
        <button class="food-button" id="foodButton" onclick="suggestFood()">วันนี้กินอะไร</button>
        <div class="bottom-bar"></div>
    </div>
    <script>
        document.addEventListener("DOMContentLoaded", function() {
            const messages = [
                { sender: "user", text: "พี่ๆ" },
                { sender: "user", text: "หนูว่าหนูลืมอะไรไปอย่างนึง 🤔" },
                { sender: "bot", text: "ลืมอะไร" },
                { sender: "user", text: "ลืมตา 👀" },
                { sender: "bot", text: "แฮ่!!" },
                { sender: "user", text: "พี่ๆ" },
                { sender: "bot", text: "อะไรอีก" },
                { sender: "user", text: "หนูว่าหนูลืมอีกอย่าง" },
                { sender: "bot", text: "ลืมไร" },
                { sender: "user", text: "ก็บอกว่าลืมไง" },
                { sender: "user", text: "ปั๊ดโถ่วววว" },
                { sender: "bot", text: "😤" },
                { sender: "user", text: "เธอๆ 👋" },
                { sender: "user", text: "ปกติกินหวานน้อยหรือหวานมากอะ" },
                { sender: "bot", text: "หวานปกตินะ" },
                { sender: "user", text: "หรอ แล้วมีหวานใจยัง ❤️" },
                { sender: "bot", text: "..." },
                { sender: "user", text: "เธอๆ" },
                { sender: "bot", text: "ว่าไง" },
                { sender: "user", text: "หิวข้าวไหม 🍚" },
                { sender: "bot", text: "ไม่นะ" },
                { sender: "user", text: "งั้นถ้าขอ 'ข้าว' ไปในใจได้ไหม ❤️" },
                { sender: "user", text: "ฮิ้ววววว 🤩" },
            ];
            let index = 0;

            window.nextMessage = function() {
                if (index >= messages.length) return;

                const chatContainer = document.getElementById("chat");
                const messageDiv = document.createElement("div");
                messageDiv.classList.add("message", messages[index].sender);
                
                const textNode = document.createElement("span");
                textNode.textContent = messages[index].text;

                messageDiv.appendChild(textNode);
                chatContainer.appendChild(messageDiv);
                chatContainer.scrollTop = chatContainer.scrollHeight;
                index++;

                if (messages[index - 1].text.includes("หิวข้าวไหม")) {
                    document.getElementById('foodButton').style.display = 'block';
                }
            };

            function updateTime() {
                const now = new Date();
                const hours = now.getHours().toString().padStart(2, '0');
                const minutes = now.getMinutes().toString().padStart(2, '0');
                document.getElementById('time').textContent = `${hours}:${minutes}`;
            }

            setInterval(updateTime, 1000);
            updateTime();
        });

        function suggestFood() {
            const foods = [
                "ต้มยำกุ้ง",
                "แกงเขียวหวานไก่",
                "ผัดไทย",
                "ผัดกะเพรา",
                "แกงมัสมั่นไก่",
                "ข้าวซอย",
                "ข้าวเหนียวมะม่วง"
            ];
            const randomFood = foods[Math.floor(Math.random() * foods.length)];
            const chatContainer = document.getElementById("chat");
            const messageDiv = document.createElement("div");
            messageDiv.classList.add("message", "bot");

            const textNode = document.createElement("span");
            textNode.textContent = `กิน ${randomFood} ดีไหม`;

            messageDiv.appendChild(textNode);
            chatContainer.appendChild(messageDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;

            document.getElementById('foodButton').style.display = 'none';
        }
    </script>
</body>
</html>
