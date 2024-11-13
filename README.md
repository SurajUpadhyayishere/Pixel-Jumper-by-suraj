html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pixel Jumper</title>
    <link rel="stylesheet" href="style.css">
    <script src="https://cdn.jsdelivr.net/npm/phaser@3.55.2/dist/phaser.min.js"></script>
</head>
<body>
    <div id="game-container"></div>
    <script src="game.js"></script>
</body>
</html>

css

body {
    margin: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #1d1d1d;
}

#game-container {
    width: 800px;
    height: 600px;
    border: 2px solid #fff;
}

Javascript

const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#2d2d2d',
    physics: {
        default: 'arcade',
        arcade: {
            gravity: { y: 500 },
            debug: false
        }
    },
    scene: {
        preload: preload,
        create: create,
        update: update
    }
};

const game = new Phaser.Game(config);
let player, platforms, cursors;
let score = 0;
let scoreText;
let gameSpeed = 200;

function preload() {
    this.load.image('ground', 'https://labs.phaser.io/assets/sprites/platform.png');
    this.load.image('player', 'https://labs.phaser.io/assets/sprites/phaser-dude.png');
    this.load.image('spike', 'https://labs.phaser.io/assets/sprites/spike.png');
}

function create() {
    // Add platforms
    platforms = this.physics.add.staticGroup();
    platforms.create(400, 568, 'ground').setScale(2).refreshBody();
    platforms.create(600, 400, 'ground');
    platforms.create(50, 250, 'ground');
    platforms.create(750, 220, 'ground');

    // Add player
    player = this.physics.add.sprite(100, 450, 'player');
    player.setBounce(0.2);
    player.setCollideWorldBounds(true);

    // Player and platform collision
    this.physics.add.collider(player, platforms);

    // Add spikes as obstacles
    this.spikes = this.physics.add.group();
    this.time.addEvent({
        delay: 2000, // Spawn spikes every 2 seconds
        callback: spawnSpike,
        callbackScope: this,
        loop: true
    });

    // Add score text
    scoreText = this.add.text(16, 16, 'Score: 0', { fontSize: '32px', fill: '#fff' });

    // Add keyboard controls
    cursors = this.input.keyboard.createCursorKeys();
}

function update() {
    // Player movement
    if (cursors.left.isDown) {
        player.setVelocityX(-gameSpeed);
    } else if (cursors.right.isDown) {
        player.setVelocityX(gameSpeed);
    } else {
        player.setVelocityX(0);
    }

    if (cursors.up.isDown && player.body.touching.down) {
        player.setVelocityY(-350);
    }

    // Increase difficulty over time
    increaseDifficulty();
}

function spawnSpike() {
    const spike = this.spikes.create(Phaser.Math.Between(100, 700), 0, 'spike');
    spike.setBounce(1);
    spike.setVelocity(Phaser.Math.Between(-200, 200), 20);
    spike.setCollideWorldBounds(true);
    this.physics.add.collider(spike, platforms);
    this.physics.add.overlap(player, spike, hitSpike, null, this);
}

function hitSpike() {
    this.physics.pause();
    player.setTint(0xff0000);
    player.anims.play('turn');
    scoreText.setText('Game Over! Score: ' + score);
}

function increaseDifficulty() {
    if (gameSpeed < 500) {
        gameSpeed += 0.05; // Increase speed gradually
    }
    score++;
    scoreText.setText('Score: ' + score);
}

function increaseDifficulty() {
    score++;
    scoreText.setText('Score: ' + score);

    if (gameSpeed < 1000) {
        gameSpeed += 0.1; // Increase speed gradually
    }

    if (score % 50 === 0) { // Every 50 points
        this.cameras.main.setAlpha(Phaser.Math.FloatBetween(0.5, 1)); // Flickering effect
    }

    // Impossible mode after 120 seconds (score depends on your increment)
    if (score > 500) {
        gameSpeed += 2; // Drastically increase speed
        player.setGravityY(800); // Increase gravity
        if (Math.random() > 0.9) { // 10% chance to invert controls
            cursors.left.isDown = cursors.right.isDown;
        }
    }
}


Phaser.js

file:///C:/Users/Dell/OneDrive/Documents/Downloads/phaser.js
