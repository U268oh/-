<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>狼兽人跑酷游戏</title>
    <script src="https://cdn.jsdelivr.net/npm/phaser@3.55.2/dist/phaser.min.js"></script>
</head>
<body>
    <script>
        const config = {
            type: Phaser.AUTO,
            width: 800,
            height: 600,
            physics: {
                default: 'arcade',
                arcade: {
                    gravity: { y: 300 },
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

        let player;
        let cursors;
        let obstacles;
        let score = 0;
        let scoreText;

        function preload() {
            // 加载角色和背景素材
            this.load.image('background', 'https://i.imgur.com/9ZQ9ZQz.png'); // 替换为你的背景图
            this.load.image('ground', 'https://i.imgur.com/9ZQ9ZQz.png'); // 替换为你的地面图
            this.load.spritesheet('wolf', 'https://i.imgur.com/9ZQ9ZQz.png', { frameWidth: 32, frameHeight: 48 }); // 替换为你的狼兽人角色图
            this.load.image('obstacle', 'https://i.imgur.com/9ZQ9ZQz.png'); // 替换为你的障碍物图
        }

        function create() {
            // 添加背景
            this.add.image(400, 300, 'background');

            // 添加地面
            const ground = this.physics.add.staticGroup();
            ground.create(400, 568, 'ground').setScale(2).refreshBody();

            // 添加玩家
            player = this.physics.add.sprite(100, 450, 'wolf');
            player.setBounce(0.2);
            player.setCollideWorldBounds(true);

            // 添加动画
            this.anims.create({
                key: 'run',
                frames: this.anims.generateFrameNumbers('wolf', { start: 0, end: 3 }),
                frameRate: 10,
                repeat: -1
            });

            // 添加障碍物
            obstacles = this.physics.add.group();

            // 碰撞检测
            this.physics.add.collider(player, ground);
            this.physics.add.collider(player, obstacles, hitObstacle, null, this);

            // 键盘控制
            cursors = this.input.keyboard.createCursorKeys();

            // 显示分数
            scoreText = this.add.text(16, 16, 'Score: 0', { fontSize: '32px', fill: '#fff' });

            // 定时生成障碍物
            this.time.addEvent({
                delay: 2000,
                callback: spawnObstacle,
                callbackScope: this,
                loop: true
            });
        }

        function update() {
            // 玩家移动控制
            if (cursors.left.isDown) {
                player.setVelocityX(-160);
                player.anims.play('run', true);
                player.flipX = true;
            } else if (cursors.right.isDown) {
                player.setVelocityX(160);
                player.anims.play('run', true);
                player.flipX = false;
            } else {
                player.setVelocityX(0);
                player.anims.stop();
            }

            if (cursors.up.isDown && player.body.touching.down) {
                player.setVelocityY(-330);
            }
        }

        function spawnObstacle() {
            const obstacle = obstacles.create(800, 500, 'obstacle');
            obstacle.setVelocityX(-200);
            obstacle.setCollideWorldBounds(true);
            obstacle.setBounce(1);
            obstacle.setScale(0.5);

            // 增加分数
            score += 10;
            scoreText.setText('Score: ' + score);
        }

        function hitObstacle(player, obstacle) {
            this.physics.pause();
            player.setTint(0xff0000);
            player.anims.play('turn');
            scoreText.setText('Game Over! Final Score: ' + score);
        }
    </script>
</body>
</html>
