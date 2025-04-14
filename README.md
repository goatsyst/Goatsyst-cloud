<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Goatsyst cloud</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 20px;
            background-color: #f4f4f9;
        }
        h1 {
            color: #333;
            font-size: 2em;
        }
        p {
            color: #666;
            font-size: 1.1em;
        }
        .input-container {
            margin: 20px 0;
        }
        input[type="text"] {
            padding: 8px;
            width: 200px;
            font-size: 16px;
            border: 2px solid #ccc;
            border-radius: 4px;
        }
        textarea {
            width: 80%;
            max-width: 600px;
            height: 120px;
            padding: 10px;
            margin: 10px 0;
            font-size: 16px;
            border: 2px solid #ccc;
            border-radius: 4px;
        }
        button {
            padding: 10px 20px;
            margin: 5px;
            font-size: 16px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        #result {
            margin-top: 20px;
            font-size: 18px;
            color: #333;
            min-height: 100px;
            text-align: left;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
        }
        .error {
            color: #dc3545;
        }
        .success {
            color: #28a745;
        }
        .ai-response {
            color: #17a2b8;
            font-style: italic;
        }
    </style>
</head>
<body>
    <h1>Goatsyst cloud</h1>
    <p>Save text with a file name, retrieve it, or ask the AI to analyze it!</p>
    <div class="input-container">
        <label for="fileName">File Name: </label>
        <input type="text" id="fileName" placeholder="e.g., myfile"><br>
    </div>
    <textarea id="textInput" placeholder="Enter your text here..."></textarea><br>
    <button onclick="saveText()">Save File</button>
    <div class="input-container">
        <label for="retrieveName">Retrieve/Analyze File Name: </label>
        <input type="text" id="retrieveName" placeholder="Enter file name"><br>
    </div>
    <button onclick="getText()">Retrieve File</button>
    <button onclick="analyzeText()">Analyze with AI</button>
    <div id="result"></div>

    <script>
        // Load files from localStorage or initialize empty array
        let fileStorage = JSON.parse(localStorage.getItem('fileStorage')) || [];

        function saveText() {
            const fileName = document.getElementById('fileName').value.trim();
            const textInput = document.getElementById('textInput').value;
            const resultDiv = document.getElementById('result');

            // Validate inputs
            if (!fileName) {
                resultDiv.innerHTML = '<span class="error">Please enter a file name!</span>';
                return;
            }
            if (!textInput) {
                resultDiv.innerHTML = '<span class="error">Please enter some text!</span>';
                return;
            }

            // Simulate saving
            resultDiv.innerHTML = 'Saving file...';
            setTimeout(() => {
                // Check if file exists
                const existingFileIndex = fileStorage.findIndex(file => file.name === fileName);
                if (existingFileIndex !== -1) {
                    fileStorage[existingFileIndex].content = textInput;
                    resultDiv.innerHTML = `<span class="success">Updated file '${fileName}'!</span>`;
                } else {
                    fileStorage.push({ name: fileName, content: textInput });
                    resultDiv.innerHTML = `<span class="success">Saved file '${fileName}'!</span>`;
                }
                // Save to localStorage
                try {
                    localStorage.setItem('fileStorage', JSON.stringify(fileStorage));
                } catch (e) {
                    resultDiv.innerHTML = '<span class="error">Error saving (storage full?)</span>';
                    return;
                }
                // Clear inputs
                document.getElementById('fileName').value = '';
                document.getElementById('textInput').value = '';
            }, 1000);
        }

        function getText() {
            const retrieveName = document.getElementById('retrieveName').value.trim();
            const resultDiv = document.getElementById('result');

            // Validate input
            if (!retrieveName) {
                resultDiv.innerHTML = '<span class="error">Please enter a file name!</span>';
                return;
            }

            // Simulate fetching
            resultDiv.innerHTML = 'Fetching file...';
            setTimeout(() => {
                const file = fileStorage.find(file => file.name === retrieveName);
                if (file) {
                    resultDiv.innerHTML = `<span class="success">File '${retrieveName}' found!</span><br>Content: ${file.content}`;
                } else {
                    resultDiv.innerHTML = `<span class="error">File '${retrieveName}' not found!</span>`;
                }
                document.getElementById('retrieveName').value = '';
            }, 1000);
        }

        function analyzeText() {
            const retrieveName = document.getElementById('retrieveName').value.trim();
            const resultDiv = document.getElementById('result');

            // Validate input
            if (!retrieveName) {
                resultDiv.innerHTML = '<span class="error">Please enter a file name to analyze!</span>';
                return;
            }

            // Simulate AI processing
            resultDiv.innerHTML = 'AI is analyzing...';
            setTimeout(() => {
                const file = fileStorage.find(file => file.name === retrieveName);
                if (!file) {
                    resultDiv.innerHTML = `<span class="error">File '${retrieveName}' not found!</span>`;
                    document.getElementById('retrieveName').value = '';
                    return;
                }

                // Simple "AI" analysis: keyword detection and response
                const text = file.content.toLowerCase();
                let aiResponse = '';
                if (text.includes('happy') || text.includes('great') || text.includes('awesome')) {
                    aiResponse = 'This text sounds positive! 😊 Want to add more cheerful notes?';
                } else if (text.includes('sad') || text.includes('bad') || text.includes('sorry')) {
                    aiResponse = 'This text seems a bit down. 😔 Maybe write something uplifting next?';
                } else if (text.includes('todo') || text.includes('task') || text.includes('list')) {
                    aiResponse = 'Looks like a task list! 📋 Need help prioritizing?';
                } else {
                    aiResponse = 'Interesting text! 🤔 It’s unique—tell me more about it!';
                }

                // Word count and summary
                const wordCount = text.split(/\s+/).length;
                const summary = file.content.length > 50 ? file.content.substring(0, 47) + '...' : file.content;

                resultDiv.innerHTML = `
                    <span class="success">File '${retrieveName}' analyzed!</span><br>
                    Content: ${file.content}<br>
                    <span class="ai-response">AI Analysis: ${aiResponse}</span><br>
                    Word Count: ${wordCount}<br>
                    Summary: ${summary}
                `;
                document.getElementById('retrieveName').value = '';
            }, 1500); // Longer delay for "AI thinking"
        }
    </script>
</body>
</html>
