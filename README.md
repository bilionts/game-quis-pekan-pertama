# game-quis-pekan-pertama
.
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kuis Pengetahuan Umum & Verifikasi</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #e9ecef;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .container {
            background-color: #ffffff;
            padding: 30px 40px;
            border-radius: 12px;
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 600px;
        }

        h1 {
            color: #007bff;
            text-align: center;
            margin-bottom: 25px;
            font-size: 1.8em;
        }

        .question-card {
            margin-bottom: 25px;
            padding: 15px;
            border: 1px solid #ced4da;
            border-radius: 8px;
            background-color: #f8f9fa;
        }

        .question-card p {
            font-weight: bold;
            color: #343a40;
            margin-top: 0;
            margin-bottom: 10px;
        }

        .answer-input {
            width: 95%;
            padding: 10px;
            border: 2px solid #ced4da;
            border-radius: 6px;
            font-size: 1em;
            transition: border-color 0.3s;
        }

        .answer-input:focus:not(:disabled) {
            border-color: #007bff;
            outline: none;
        }
        
        .answer-input:disabled {
            background-color: #e9ecef;
            cursor: not-allowed;
        }

        #submitButton {
            display: block;
            width: 100%;
            padding: 12px;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 1.1em;
            cursor: pointer;
            transition: background-color 0.3s;
            margin-top: 30px;
        }
        
        #submitButton:not(:disabled):hover {
            background-color: #218838;
        }
        
        #submitButton:disabled {
            background-color: #6c757d; 
            cursor: not-allowed;
        }

        #results {
            margin-top: 30px;
            padding-top: 20px;
            border-top: 1px dashed #ced4da;
        }

        .result-item {
            padding: 10px 0;
            border-bottom: 1px dotted #e9ecef;
        }

        .result-item:last-child {
            border-bottom: none;
        }

        .status-correct {
            color: #28a745;
            font-weight: bold;
        }

        .status-incorrect {
            color: #dc3545;
            font-weight: bold;
        }

        #lockMessage {
            text-align: center;
            margin-top: 30px;
            padding: 20px;
            background-color: #fff3cd; 
            border: 1px solid #ffeeba;
            border-radius: 8px;
            color: #856404;
            font-weight: bold;
            line-height: 1.6;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Kuis Pengetahuan Umum 🇮🇩</h1>

        <form id="quizForm">
            <div class="question-card">
                <p>1. Siapa pengetik naskah Proklamasi Kemerdekaan Indonesia?</p>
                <input type="text" id="q1" class="answer-input" placeholder="Tulis jawaban di sini..." required>
            </div>

            <div class="question-card">
                <p>2. Negara apa di benua Amerika yang masih menganut paham komunisme?</p>
                <input type="text" id="q2" class="answer-input" placeholder="Tulis jawaban di sini..." required>
            </div>

            <div class="question-card">
                <p>3. Apa penyebab utama penyakit usus buntu (apendisitis)?</p>
                <input type="text" id="q3" class="answer-input" placeholder="Tulis jawaban di sini..." required>
            </div>

            <div class="question-card">
                <p>4. Benda angkasa yang dulu disebut planet tetapi sekarang sudah dianggap benda asteroid biasa?</p>
                <input type="text" id="q4" class="answer-input" placeholder="Tulis jawaban di sini..." required>
            </div>

            <div class="question-card">
                <p>5. Biasa disebut berapa dimensi patung-patung yang menempel di candi-candi (relief)?</p>
                <input type="text" id="q5" class="answer-input" placeholder="Tulis jawaban di sini..." required>
            </div>

            <button type="submit" id="submitButton" disabled>Isi Semua Jawaban Dulu</button>
        </form>

        <div id="results">
            </div>
        
        <div id="lockMessage" style="display:none;">
            </div>
    </div>

    <script>
        // Data Kunci Jawaban
        const correctAnswers = {
            q1: "sayuti melik",
            q2: "kuba",
            q3: "penyumbatan pada usus buntu",
            q4: "pluto",
            q5: "2,5 dimensi"
        };

        const form = document.getElementById('quizForm');
        const resultsDiv = document.getElementById('results');
        const submitButton = document.getElementById('submitButton');
        const lockMessageDiv = document.getElementById('lockMessage');
        const inputElements = document.querySelectorAll('.answer-input');

        // --- Logika Pengecekan Wajib Sebelum Submit ---

        function checkInputs() {
            let allFilled = true;
            inputElements.forEach(input => {
                if (input.value.trim() === '') {
                    allFilled = false;
                }
            });
            
            // Aktifkan atau nonaktifkan tombol submit
            submitButton.disabled = !allFilled;
            if (allFilled) {
                submitButton.textContent = "Cek Jawaban";
            } else {
                submitButton.textContent = "Isi Semua Jawaban Dulu";
            }
        }

        // Tambahkan event listener ke setiap input untuk memicu pengecekan
        inputElements.forEach(input => {
            input.addEventListener('input', checkInputs);
        });

        // Panggil saat halaman dimuat untuk inisialisasi status tombol
        checkInputs();

        // --- Logika Submit dan Penguncian ---

        form.addEventListener('submit', function(e) {
            e.preventDefault();
            // Pengecekan akhir
            if (submitButton.disabled) return; 
            
            checkQuiz();
            lockQuiz();
        });

        function normalizeAnswer(answer) {
            // Mengubah ke huruf kecil, menghilangkan spasi berlebih, dan menghilangkan tanda baca
            return answer.toLowerCase()
                         .trim()
                         .replace(/[.,\/#!$
                         
