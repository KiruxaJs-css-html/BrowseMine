const player = document.getElementById('player');
const gameBoard = document.getElementById('game-board');
const menu = document.getElementById('menu');
const playerSpeed = 5;
let playerX = 440;
let playerY = 440;
let moveInterval;
let selectedBlockType = null; // Тип выбранного блока (теперь это цвет)
const clearButton = document.getElementById('clear-button'); // Получаем ссылку на кнопку
const gameMusic = document.getElementById('game-music'); // Получаем ссылку на аудио элемент

function updatePlayerPosition() {
    player.style.left = playerX + 'px';
    player.style.top = playerY + 'px';
}

function startGameLoop(direction) {
    if (moveInterval) return;
    moveInterval = setInterval(() => {
        switch(direction) {
            case 'w':
                playerY -= playerSpeed;
                break;
            case 'a':
                playerX -= playerSpeed;
                break;
            case 's':
                playerY += playerSpeed;
                break;
            case 'd':
                playerX += playerSpeed;
                break;
        }

        // Ограничиваем выход игрока за пределы игрового поля
        if (playerX < 0) playerX = 0;
        if (playerY < 0) playerY = 0;
        if (playerX > gameBoard.offsetWidth - player.offsetWidth) playerX = gameBoard.offsetWidth - player.offsetWidth;
        if (playerY > gameBoard.offsetHeight - player.offsetHeight) playerY = gameBoard.offsetHeight - player.offsetHeight;

        updatePlayerPosition();
    }, 20);
}

function stopGameLoop() {
    clearInterval(moveInterval);
    moveInterval = null;
}

// Обработчик выбора блока из меню
menu.addEventListener('click', function(event) {
    if (event.target.classList.contains('block-option')) {
        selectedBlockType = event.target.dataset.blockType;
        console.log('Выбран цвет:', selectedBlockType); // Для отладки
    }
});

// Обработчик кликов по игровому полю
gameBoard.addEventListener('contextmenu', function(event) {
    event.preventDefault(); // Отменяем стандартное контекстное меню (правая кнопка)
});

gameBoard.addEventListener('mousedown', function(event) {
    if (!selectedBlockType) return; // Если блок не выбран, ничего не делаем

    const x = event.offsetX;
    const y = event.offsetY;

    if (event.button === 2) { // Правая кнопка мыши
        // Добавляем блок
        addBlock(x, y, selectedBlockType);
    } else if (event.button === 0) { // Левая кнопка мыши
        // Удаляем блок
       removeBlock(x, y);
    }
});

function addBlock(x, y, blockType) {
    const block = document.createElement('div');
    block.classList.add('placed-block');
    block.dataset.blockType = blockType;
    block.style.left = Math.floor(x / 20) * 20 + 'px'; // Выравнивание по сетке
    block.style.top = Math.floor(y / 20) * 20 + 'px';
    block.style.backgroundColor = blockType; // Используем blockType (цвет) напрямую
    block.id = "block-" + x + "-" + y; // Добавляем уникальный ID

    gameBoard.appendChild(block);
}

function removeBlock(x, y) {
    const blockId = "block-" + x + "-" + y;
    const block = document.getElementById(blockId);

    if(block){
      block.remove();
    }
}

// Обработчик для кнопки "Удалить все блоки"
clearButton.addEventListener('click', function() {
    const blocks = document.querySelectorAll('.placed-block');
    blocks.forEach(block => {
        block.remove();
    });
});

document.addEventListener('keydown', function(event) {
    switch(event.key) {
        case 'w':
        case 'a':
        case 's':
        case 'd':
            startGameLoop(event.key);
            break;
    }
});

document.addEventListener('keyup', function(event) {
    switch(event.key) {
        case 'w':
        case 'a':
        case 's':
        case 'd':
            stopGameLoop();
            break;
    }
});

// Добавляем логику для управления музыкой:
// Запускаем музыку при загрузке страницы
window.addEventListener('load', function() {
    gameMusic.play();
});
// Останавливаем/запускаем музыку по кнопке (по желанию):
// clearButton.addEventListener('click', function() {  // Используем существующую кнопку, можно создать свою
//    if (gameMusic.paused) {
//        gameMusic.play();
//    } else {
//        gameMusic.pause();
//    }
// });

updatePlayerPosition();