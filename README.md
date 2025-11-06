<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>温宝宝</title>
    <style>
        /* 全局样式重置 */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            margin: 0;
            padding: 0;
            overflow: hidden; /* 隐藏滚动条 */
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        /* 烟花背景容器 */
        #fireworks-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 0; /* 背景层级 */
        }

        /* 内容容器 - 居中显示 */
        .content {
            position: relative;
            z-index: 1; /* 内容层级高于背景 */
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            padding: 20px;
        }

        /* 花体字样式 */
        .calligraphy-text {
            font-family: 'Brush Script MT', ' cursive', 'Microsoft YaHei', sans-serif;
            font-size: 48px;
            color: #ff4d94;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
            margin-bottom: 60px;
            line-height: 1.5;
        }

        /* 按钮容器 */
        .btn-group {
            display: flex;
            gap: 30px;
        }

        /* 选项按钮样式 */
        .choice-btn {
            padding: 18px 45px;
            font-size: 24px;
            font-weight: bold;
            color: #fff;
            background: linear-gradient(135deg, #ff6b6b, #ff4d94);
            border: none;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(255, 77, 148, 0.4);
            transition: all 0.3s ease;
        }

        .choice-btn:hover {
            transform: scale(1.08);
            box-shadow: 0 6px 20px rgba(255, 77, 148, 0.6);
            background: linear-gradient(135deg, #ff5252, #ff3385);
        }

        /* 响应式适配 - 手机端 */
        @media (max-width: 768px) {
            .calligraphy-text {
                font-size: 36px;
                margin-bottom: 40px;
            }

            .btn-group {
                flex-direction: column;
                gap: 20px;
            }

            .choice-btn {
                padding: 15px 35px;
                font-size: 20px;
            }
        }
    </style>
</head>
<body>
    <!-- 烟花背景容器 -->
    <div id="fireworks-container"></div>

    <!-- 核心内容 -->
    <div class="content">
        <div class="calligraphy-text">
            温葆桢大美女<br>你愿意做我的女朋友吗
        </div>
        <div class="btn-group">
            <button class="choice-btn" onclick="alert('太开心啦！往后余生都是你～')">愿意</button>
            <button class="choice-btn" onclick="alert('收到你的心意，我也是超级愿意呀！')">非常愿意</button>
        </div>
    </div>

    <!-- 烟花效果脚本 -->
    <script>
        // 烟花粒子类
        class Firework {
            constructor() {
                // 随机生成烟花发射位置
                this.x = Math.random() * window.innerWidth;
                this.y = window.innerHeight;
                // 随机生成烟花颜色
                this.color = `hsl(${Math.random() * 360}, 100%, 60%)`;
                // 烟花上升速度
                this.velocityY = -5 - Math.random() * 3;
                this.velocityX = (Math.random() - 0.5) * 3;
                // 烟花大小
                this.size = 3;
                // 是否爆炸
                this.exploded = false;
                // 爆炸后的粒子
                this.particles = [];
            }

            // 更新烟花位置
            update() {
                if (!this.exploded) {
                    this.y += this.velocityY;
                    this.x += this.velocityX;
                    // 上升速度衰减（模拟重力）
                    this.velocityY += 0.1;

                    // 达到一定高度后爆炸
                    if (this.velocityY >= -1) {
                        this.explode();
                    }
                } else {
                    // 更新爆炸粒子
                    this.particles.forEach(particle => particle.update());
                    // 过滤掉消失的粒子
                    this.particles = this.particles.filter(particle => particle.alpha > 0);
                }
            }

            // 烟花爆炸
            explode() {
                this.exploded = true;
                // 生成爆炸粒子（30-50个）
                const particleCount = 30 + Math.floor(Math.random() * 20);
                for (let i = 0; i < particleCount; i++) {
                    this.particles.push(new Particle(this.x, this.y, this.color));
                }
            }

            // 绘制烟花
            draw(ctx) {
                if (!this.exploded) {
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                    ctx.fillStyle = this.color;
                    ctx.fill();
                } else {
                    this.particles.forEach(particle => particle.draw(ctx));
                }
            }
        }

        // 爆炸粒子类
        class Particle {
            constructor(x, y, color) {
                this.x = x;
                this.y = y;
                this.color = color;
                // 随机粒子速度
                this.velocityX = (Math.random() - 0.5) * 6;
                this.velocityY = (Math.random() - 0.5) * 6;
                // 粒子大小
                this.size = 2;
                // 透明度
                this.alpha = 1;
                // 透明度衰减速度
                this.decay = 0.02;
            }

            // 更新粒子位置
            update() {
                this.x += this.velocityX;
                this.y += this.velocityY;
                // 粒子下落（模拟重力）
                this.velocityY += 0.1;
                // 透明度衰减
                this.alpha -= this.decay;
            }

            // 绘制粒子
            draw(ctx) {
                ctx.save();
                ctx.globalAlpha = this.alpha;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.fill();
                ctx.restore();
            }
        }

        // 初始化画布
        const canvas = document.createElement('canvas');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        document.getElementById('fireworks-container').appendChild(canvas);
        const ctx = canvas.getContext('2d');

        // 存储烟花实例
        const fireworks = [];

        // 每隔300-800毫秒生成一个烟花
        setInterval(() => {
            fireworks.push(new Firework());
        }, 300 + Math.random() * 500);

        // 动画循环
        function animate() {
            // 半透明背景叠加（实现烟花轨迹效果）
            ctx.fillStyle = 'rgba(0, 0, 0, 0.1)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 更新并绘制所有烟花
            fireworks.forEach((firework, index) => {
                firework.update();
                firework.draw(ctx);

                // 移除完全消失的烟花
                if (firework.exploded && firework.particles.length === 0) {
                    fireworks.splice(index, 1);
                }
            });

            requestAnimationFrame(animate);
        }

        // 启动动画
        animate();

        // 窗口大小适配
        window.addEventListener('resize', () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        });
    </script>
</body>
</html>
