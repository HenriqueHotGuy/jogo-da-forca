# jogo-da-forca
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogo da Forca</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a202c;
            color: #e2e8f0;
        }
        .key.disabled {
            background-color: #4a5568 !important;
            cursor: not-allowed;
            color: #a0aec0 !important;
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4">
    <div id="game-container" class="bg-gray-800 p-8 rounded-xl shadow-2xl w-full max-w-2xl text-center">
        <h1 class="text-4xl md:text-5xl font-bold mb-6 text-green-400">Jogo da Forca</h1>

        <!-- Display da Forca -->
        <pre id="hangman-display" class="text-lg md:text-xl font-mono text-white bg-gray-900 p-4 rounded-lg mb-6 shadow-inner overflow-x-auto">
  +---+
  |   |
      |
      |
      |
      |
=========
        </pre>

        <!-- Display da Palavra -->
        <div id="word-display" class="text-4xl md:text-6xl font-bold tracking-widest my-8 text-yellow-300"></div>

        <!-- Mensagens do Jogo -->
        <div id="message-display" class="text-xl md:text-2xl font-semibold text-white mb-6">Adivinhe a palavra!</div>

        <!-- Teclado Virtual -->
        <div id="keyboard" class="grid grid-cols-6 sm:grid-cols-7 md:grid-cols-9 gap-3 md:gap-4 mb-6"></div>

        <!-- Botão de Reset -->
        <button id="reset-button" class="mt-4 px-6 py-3 bg-green-500 hover:bg-green-600 text-white font-bold rounded-full shadow-lg transition duration-300 transform hover:scale-105 focus:outline-none" style="display: none;">
            Novo Jogo
        </button>
    </div>

    <script>
        // Dicionário de palavras para o jogo
        const words = ['PROGRAMACAO', 'DESENVOLVIMENTO', 'COMPUTADOR', 'JAVASCRIPT', 'HTML', 'CSS', 'INTERNET', 'ALGORITMO', 'INTERFACE'];

        // Variáveis de estado do jogo
        let chosenWord = '';
        let guessedWord = [];
        let wrongGuesses = 0;
        const maxWrongGuesses = 6;
        const guessedLetters = new Set();

        // Estados do boneco da forca
        const hangmanParts = [
            `
  +---+
  |   |
      |
      |
      |
      |
=========
            `,
            `
  +---+
  |   |
  O   |
      |
      |
      |
=========
            `,
            `
  +---+
  |   |
  O   |
  |   |
      |
      |
=========
            `,
            `
  +---+
  |   |
  O   |
 /|   |
      |
      |
=========
            `,
            `
  +---+
  |   |
  O   |
 /|\\  |
      |
      |
=========
            `,
            `
  +---+
  |   |
  O   |
 /|\\  |
 /    |
      |
=========
            `,
            `
  +---+
  |   |
  O   |
 /|\\  |
 / \\  |
      |
=========
            `
        ];

        // Elementos do DOM
        const wordDisplay = document.getElementById('word-display');
        const hangmanDisplay = document.getElementById('hangman-display');
        const messageDisplay = document.getElementById('message-display');
        const keyboard = document.getElementById('keyboard');
        const resetButton = document.getElementById('reset-button');

        // Função para inicializar o jogo
        function initializeGame() {
            chosenWord = words[Math.floor(Math.random() * words.length)];
            guessedWord = Array(chosenWord.length).fill('_');
            wrongGuesses = 0;
            guessedLetters.clear();
            
            hangmanDisplay.textContent = hangmanParts[0];
            wordDisplay.textContent = guessedWord.join(' ');
            messageDisplay.textContent = 'Adivinhe a palavra!';
            resetButton.style.display = 'none';

            createKeyboard();
        }

        // Função para criar o teclado virtual
        function createKeyboard() {
            keyboard.innerHTML = '';
            for (let i = 65; i <= 90; i++) {
                const letter = String.fromCharCode(i);
                const key = document.createElement('div');
                key.classList.add('key', 'bg-gray-700', 'text-white', 'p-2', 'rounded-md', 'font-bold', 'uppercase', 'cursor-pointer', 'select-none', 'transition-colors', 'hover:bg-gray-600');
                key.textContent = letter;
                key.addEventListener('click', () => handleGuess(letter));
                keyboard.appendChild(key);
            }
        }

        // Função para lidar com a tentativa do usuário
        function handleGuess(letter) {
            if (guessedLetters.has(letter) || wrongGuesses >= maxWrongGuesses) {
                return;
            }

            guessedLetters.add(letter);
            const keyElement = [...keyboard.children].find(el => el.textContent === letter);
            if (keyElement) {
                keyElement.classList.add('disabled');
            }

            let found = false;
            for (let i = 0; i < chosenWord.length; i++) {
                if (chosenWord[i] === letter) {
                    guessedWord[i] = letter;
                    found = true;
                }
            }

            wordDisplay.textContent = guessedWord.join(' ');

            if (!found) {
                wrongGuesses++;
                hangmanDisplay.textContent = hangmanParts[wrongGuesses];
            } else {
                if (keyElement) {
                    keyElement.classList.add('bg-green-500', 'hover:bg-green-500');
                }
            }

            checkGameState();
        }

        // Função para verificar o estado do jogo
        function checkGameState() {
            if (guessedWord.join('') === chosenWord) {
                messageDisplay.textContent = 'Parabéns, você venceu!';
                endGame();
            } else if (wrongGuesses >= maxWrongGuesses) {
                messageDisplay.textContent = `Você perdeu! A palavra era: ${chosenWord}`;
                endGame();
            }
        }

        // Função para terminar o jogo
        function endGame() {
            const keys = keyboard.querySelectorAll('.key');
            keys.forEach(key => key.classList.add('disabled'));
            resetButton.style.display = 'block';
        }

        // Evento para reiniciar o jogo
        resetButton.addEventListener('click', initializeGame);

        // Inicializar o jogo ao carregar a página
        initializeGame();
    </script>
</body>
</html>
