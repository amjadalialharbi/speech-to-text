# speech-to-text
A speech-to-text user interface, allowing users to record audio directly, view the converted text in real time, and save it to a database.
## Step 1: Install XAMPP
- Download XAMPP
- Open the XAMPP Control Panel and start the Apache and MySQL modules.
 ## Step 2: Create the Database
  - Open The web browser and go to` http://localhost/phpmyadmin.`
  - Create a database named `speech_to_text`
    
 ## Step 3: Create the Table
  - Create a table named `texts` the following SQL code:

  
 ```
     CREATE TABLE texts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    text_content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
## Step 4: Set Up the Project Files
- Navigate to the XAMPP htdocs directory ( `C:\xampp\htdocs`).
- Create a new folder named `voice`.
## Step 5: Create the index Page
- Inside the voice folder, create a new file named  `index.php`.
- Add the following code to `index.php`:

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Speech to Text </title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: #f0f0f0;
            margin: 0;
        }
        h1 {
            color: #333;
        }
        button {
            padding: 10px 20px;
            margin: 10px;
            border: none;
            background-color: #007bff;
            color: white;
            cursor: pointer;
            border-radius: 5px;
            font-size: 16px;
        }
        button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }
        #result {
            margin-top: 20px;
            padding: 20px;
            width: 80%;
            max-width: 600px;
            background-color: white;
            border-radius: 5px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
    </style>
</head>
<body>
    <h1>Speech to Text</h1>
    <button id="start-btn">Start Speaking</button>
    <p id="result"></p>
    
    <script>
        const startBtn = document.getElementById('start-btn');
        const result = document.getElementById('result');
        
        let recognition;
        
        if ('webkitSpeechRecognition' in window) {
            recognition = new webkitSpeechRecognition();
            console.log('Using webkitSpeechRecognition');
        } else if ('SpeechRecognition' in window) {
            recognition = new SpeechRecognition();
            console.log('Using SpeechRecognition');
        } else {
            alert("Your browser does not support speech recognition.");
        }
        
        if (recognition) {
            recognition.lang = 'en-US';
            recognition.continuous = false;
            recognition.interimResults = false;
        
            recognition.onstart = () => {
                console.log('Speech recognition started');
                startBtn.disabled = true;
            };
        
            recognition.onresult = (event) => {
                const transcript = event.results[0][0].transcript;
                console.log('Speech recognition result received:', transcript);
                result.textContent = transcript;
                console.log('Result text content set to:', result.textContent);
                saveText(transcript);
            };
        
            recognition.onerror = (event) => {
                console.error('Speech recognition error:', event.error);
            };
        
            recognition.onend = () => {
                console.log('Speech recognition ended');
                startBtn.disabled = false;
            };
        
            startBtn.addEventListener('click', () => {
                console.log('Start button clicked');
                recognition.start();
            });
        }
        
        function saveText(text) {
            console.log('Saving text:', text);
            fetch('save_text.php', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                },
                body: 'text=' + encodeURIComponent(text)
            })
            .then(response => response.text())
            .then(data => {
                console.log('Text saved:', data);
            })
            .catch(error => {
                console.error('Error saving text:', error);
            });
        }
    </script>
</body>
</html>
```
## Step 6: Create the save_text page
- Inside the voice folder, create a new file named `save_text.php`.
- Add the following code to `save_text.php`:
```
<?php
$servername = "localhost";
$username = "root";
$password = "";
$dbname = "speech_to_text";

// Connect to the database
$conn = new mysqli($servername, $username, $password, $dbname);

// Check the connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Check if the text is set
if (isset($_POST['text'])) {
    $text = $_POST['text'];

    // Prepare and execute the query to insert the text
    $stmt = $conn->prepare("INSERT INTO texts (text_content) VALUES (?)");
    $stmt->bind_param("s", $text);

    if ($stmt->execute()) {
        echo "Text saved successfully";
    } else {
        echo "Error: " . $stmt->error;
    }

    $stmt->close();
} else {
    echo "No text received";
}

$conn->close();
?>;
```
 ## speech to text interface:
 ![code 1](https://github.com/user-attachments/assets/08cd2853-79d1-4fc7-81df-f6021f0e967e)
 ![Screenshot 2024-07-13 102624](https://github.com/user-attachments/assets/6546c7f5-5537-45ae-9ef6-c5b2f565408c)
## database:
![data 2](https://github.com/user-attachments/assets/83171c2c-8a04-4d04-aca6-33997ac4af3f)


