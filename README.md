<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Besties 4 Ever</title>
    <style>
        /* Base Setup */
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #030712;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: #f3f4f6;
            padding: 20px 0;
        }

        /* Android Phone Ratio Container (approx 9:19.5 aspect ratio) */
        .phone-container {
            width: 100%;
            max-width: 412px; 
            height: 90vh;
            max-height: 892px;
            /* Sleek Dark Blue Gradient Background */
            background: linear-gradient(180deg, #0a192f 0%, #020c1b 100%);
            border: 4px solid #1e293b;
            border-radius: 36px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7);
            overflow-y: auto;
            position: relative;
            display: flex;
            flex-direction: column;
        }

        /* Hide Scrollbars for cleaner mobile look */
        .phone-container::-webkit-scrollbar {
            width: 4px;
        }
        .phone-container::-webkit-scrollbar-thumb {
            background: #1e3a8a;
            border-radius: 10px;
        }

        /* Header Accent */
        .header {
            text-align: center;
            padding: 30px 20px 15px 20px;
        }
        .header h1 {
            font-size: 1.6rem;
            color: #38bdf8;
            text-shadow: 0 0 10px rgba(56, 189, 248, 0.4);
            margin-bottom: 5px;
        }
        .header p {
            font-size: 0.9rem;
            color: #9ca3af;
        }

        /* Form Styling */
        .upload-section {
            background: rgba(255, 255, 255, 0.03);
            margin: 15px;
            padding: 15px;
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }
        .upload-section h3 {
            font-size: 1rem;
            margin-bottom: 10px;
            color: #f3f4f6;
        }
        textarea {
            width: 100%;
            height: 60px;
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid #1e3a8a;
            border-radius: 8px;
            color: white;
            padding: 8px;
            resize: none;
            margin-bottom: 10px;
            font-size: 0.85rem;
            outline: none;
        }
        .form-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
        }
        input[type="file"] {
            font-size: 0.75rem;
            color: #9ca3af;
            max-width: 140px;
        }
        select {
            background: #0f172a;
            color: white;
            border: 1px solid #1e3a8a;
            padding: 5px;
            border-radius: 6px;
            font-size: 0.8rem;
            outline: none;
        }
        button {
            background: #2563eb;
            color: white;
            border: none;
            padding: 6px 14px;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            font-size: 0.85rem;
            transition: background 0.2s;
        }
        button:hover {
            background: #1d4ed8;
        }

        /* Feed / Content Sections */
        .section-title {
            padding-left: 20px;
            margin-top: 15px;
            font-size: 1.1rem;
            color: #60a5fa;
            border-left: 4px solid #2563eb;
        }

        .feed {
            min-height: 50px;
        }

        .card {
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid rgba(255, 255, 255, 0.05);
            margin: 15px;
            padding: 15px;
            border-radius: 16px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
            animation: fadeIn 0.4s ease-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .card p {
            font-size: 0.9rem;
            line-height: 1.4;
            color: #e5e7eb;
            word-wrap: break-word;
        }

        /* Glowing Photo Frame Styling */
        .photo-frame {
            margin-bottom: 12px;
            border-radius: 8px;
            padding: 8px 8px 24px 8px;
            background: #0f172a;
            border: 2px solid #38bdf8;
            box-shadow: 0 0 15px rgba(56, 189, 248, 0.2);
            text-align: center;
        }

        .photo-frame img {
            width: 100%;
            max-height: 250px;
            object-fit: cover;
            border-radius: 4px;
            display: block;
        }
    </style>
</head>
<body>

    <div class="phone-container">
        <div class="header">
            <h1>Besties 4 Ever</h1>
            <p>Our little mobile corner</p>
        </div>

        <div class="upload-section">
            <h3>Add to our Space</h3>
            <div id="postForm">
                <textarea id="postText" placeholder="Write something special..."></textarea>
                <div class="form-row">
                    <input type="file" id="postImage" accept="image/*">
                    <select id="postType">
                        <option value="memories">Memory</option>
                        <option value="letters">Letter</option>
                    </select>
                    <button onclick="addPost()">Post</button>
                </div>
            </div>
        </div>

        <h2 class="section-title">Our Memories</h2>
        <div id="memories-feed" class="feed"></div>

        <h2 class="section-title">Letters to You</h2>
        <div id="letters-feed" class="feed"></div>
    </div>

    <script>
        // Load existing data from local storage or set defaults
        let siteData = JSON.parse(localStorage.getItem('bestieSiteData')) || {
            memories: [{ text: "The day we became absolute besties! 🌟", image: null }],
            letters: [{ text: "Thank you for always being the one person I can text at 2 AM. You are family.", image: null }]
        };

        // Render posts onto the screen
        function displayPosts() {
            const memoriesFeed = document.getElementById('memories-feed');
            const lettersFeed = document.getElementById('letters-feed');
            
            memoriesFeed.innerHTML = '';
            lettersFeed.innerHTML = '';

            // Render Memories
            siteData.memories.forEach(item => {
                memoriesFeed.appendChild(createCard(item, false));
            });

            // Render Letters
            siteData.letters.forEach(item => {
                lettersFeed.appendChild(createCard(item, true));
            });
        }

        // Helper to build a card layout
        function createCard(item, isLetter) {
            const card = document.createElement('div');
            card.className = 'card';
            
            if (item.image) {
                const frame = document.createElement('div');
                frame.className = 'photo-frame';
                const img = document.createElement('img');
                img.src = item.image;
                frame.appendChild(img);
                card.appendChild(frame);
            }
            
            const p = document.createElement('p');
            if (isLetter) p.style.fontStyle = 'italic';
            p.textContent = item.text;
            card.appendChild(p);
            
            return card;
        }

        // Function to grab input details and save a new post
        function addPost() {
            const text = document.getElementById('postText').value.trim();
            const type = document.getElementById('postType').value;
            const imageFile = document.getElementById('postImage').files[0];

            if (!text && !imageFile) {
                alert("Please add some text or an image!");
                return;
            }

            if (imageFile) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const base64Image = e.target.result;
                    saveAndRefresh(type, text, base64Image);
                };
                reader.readAsDataURL(imageFile);
            } else {
                saveAndRefresh(type, text, null);
            }
        }

        function saveAndRefresh(type, text, imageData) {
            siteData[type].unshift({ text: text, image: imageData });
            localStorage.setItem('bestieSiteData', JSON.stringify(siteData));
            
            // Reset form input areas
            document.getElementById('postText').value = '';
            document.getElementById('postImage').value = '';
            
            displayPosts();
        }

        // Initial Load Run
        displayPosts();
    </script>
</body>
</html>
