<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8" />
    <title>Bingo Multiplayer Lokal</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>
    <h1>Bingo Multiplayer Lokal</h1>
    <button id="mulaiBtn">Mulai Game</button>
    <div id="gameContainer"></div>
    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    text-align: center;
    margin: 20px;
}

button {
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
}

#gameContainer {
    margin-top: 20px;
}

.player {
    display: inline-block;
    vertical-align: top;
    margin: 10px;
    border: 2px solid #333;
    padding: 10px;
    border-radius: 8px;
}

.player h2 {
    margin-top: 0;
}

.kartu {
    display: grid;
    grid-template-columns: repeat(5, 40px);
    grid-gap: 5px;
}

.kartu div {
    width: 40px;
    height: 40px;
    line-height: 40px;
    background-color: #eee;
    border: 1px solid #999;
    border-radius: 4px;
    cursor: pointer;
    user-select: none;
    font-weight: bold;
}

.kartu div.marked {
    background-color: #4CAF50;
    color: white;
}
const mulaiBtn = document.getElementById('mulaiBtn');
const gameContainer = document.getElementById('gameContainer');

let angkaDipanggil = [];
let pemain = [];
let jumlahPemain = 2; // bisa diubah sesuai kebutuhan

mulaiBtn.addEventListener('click', mulaiGame);

function mulaiGame() {
    // Reset area
    gameContainer.innerHTML = '';

    // Reset variabel
    angkaDipanggil = [];
    pemain = [];
    // Buat pemain dan kartu
    for (let i = 1; i <= jumlahPemain; i++) {
        const playerDiv = document.createElement('div');
        playerDiv.className = 'player';

        const playerTitle = document.createElement('h2');
        playerTitle.innerText = 'Pemain ' + i;
        playerDiv.appendChild(playerTitle);

        const kartuDiv = document.createElement('div');
        kartuDiv.className = 'kartu';

        const kartu = generateKartuBingo();
        kartu.forEach((num) => {
            const cell = document.createElement('div');
            cell.innerText = num;
            cell.addEventListener('click', () => {
                // Penandaan jika angka dipanggil
                if (angkaDipanggil.includes(num)) {
                    cell.classList.toggle('marked');
                }
            });
            kartuDiv.appendChild(cell);
        });

        playerDiv.appendChild(kartuDiv);
        gameContainer.appendChild(playerDiv);

        pemain.push({
            id: i,
            kartu: kartu,
            kartuDiv: kartuDiv,
            // status pemenang bisa dicek nanti
        });
    }

    // Tampilkan tombol panggil angka
    if (!document.getElementById('panggilBtn')) {
        const panggilBtn = document.createElement('button');
        panggilBtn.id = 'panggilBtn';
        panggilBtn.innerText = 'Panggil Angka';
        panggilBtn.addEventListener('click', panggilAngka);
        gameContainer.insertBefore(panggilBtn, gameContainer.firstChild);
    }
}

function generateKartuBingo() {
    // Generate 25 angka unik dari 1-75
    const angka = Array.from({length: 75}, (_, i) => i + 1);
    shuffleArray(angka);
    const kartu = angka.slice(0, 25);
    return kartu;
}

function shuffleArray(array) {
    for (let i = array.length -1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
}

function panggilAngka() {
    if (angkaDipanggil.length >= 75) {
        alert('Semua angka sudah dipanggil!');
        return;
    }

    let angkaBaru;
    do {
        angkaBaru = Math.floor(Math.random() * 75) + 1;
    } while (angkaDipanggil.includes(angkaBaru));

    angkaDipanggil.push(angkaBaru);

    // Tampilkan angka yang dipanggil
    alert('Angka dipanggil: ' + angkaBaru);

    // Tandai angka di kartu pemain
    pemain.forEach(p => {
        Array.from(p.kartuDiv.children).forEach(cell => {
            if (parseInt(cell.innerText) === angkaBaru) {
                cell.classList.add('marked');
            }
        });
        // Cek pemenang
        if (cekBingo(p.kartuDiv)) {
            alert('Pemain ' + p.id + ' MENANG!');
        }
    });
}

function cekBingo(kartuDiv) {
    const cells = Array.from(kartuDiv.children);
    // Cek baris
    for (let i=0; i<5; i++) {
        const row = cells.slice(i*5, i*5+5);
        if (row.every(cell => cell.classList.contains('marked'))) {
            return true;
        }
    }
    // Cek kolom
    for (let i=0; i<5; i++) {
        const col = [];
        for (let j=0; j<5; j++) {
            col.push(cells[j*5 + i]);
        }
        if (col.every(cell => cell.classList.contains('marked'))) {
            return true;
        }
    }
    // Cek diagonal
    const diag1 = [cells[0], cells[6], cells[12], cells[18], cells[24]];
    const diag2 = [cells[4], cells[8], cells[12], cells[16], cells[20]];
    if (diag1.every(cell => cell.classList.contains('marked')) || diag2.every(cell => cell.classList.contains('marked'))) {
        return true;
    }
    return false;
}
