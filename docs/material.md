# 🌍 级数在现实世界 (Real World Applications)

> "微积分不仅是数学家的游戏，它是现代文明的底层代码。"

这一页将带你走出课本，看看那些 $\sum$ 符号是如何支撑起你的 MP3 音乐、游戏世界和银行密码的。

---

## 🔬 交互式实验室 (Interactive Labs)

请点击下方的标签页切换不同的应用场景。

=== "🎵 信号压缩 (MP3/JPEG)"

    ### 级数逼近：抛弃“尾巴”的艺术
    
    MP3 音乐和 JPEG 图片为什么比原始文件小那么多？因为工程师利用了**傅里叶级数 (Fourier Series)**。
    
    $$f(x) \approx \sum_{n=1}^{N} a_n \sin(nx)$$
    
    * **原理：** 任何复杂的波形（声音/图像）都可以分解成无穷多个正弦波的叠加。
    * **压缩：** 我们不需要无穷项。通常前 $N$ 项就包含了 90% 的信息。我们可以把后面那些人耳听不见、人眼看不清的“高频尾巴”直接扔掉（截断级数）。
    * **代价：** 这种截断会产生**吉布斯现象 (Gibbs Phenomenon)**，也就是波形边缘的“毛刺”。
    
    #### 🎛️ 实验 1：方波的逼近与截断
    拖动滑块，增加级数的项数 $N$，观察正弦波是如何一步步变成方波的，并注意边缘那个顽固的“尖角”。
    
    <div style="border: 1px solid #ddd; padding: 20px; border-radius: 8px; background: #f9f9f9;">
        <label><strong>级数项数 N: <span id="fourier-n-val">1</span></strong></label>
        <input type="range" id="fourier-n" min="1" max="50" step="2" value="1" style="width: 100%;">
        <canvas id="fourier-canvas" width="600" height="300" style="width: 100%; background: white; border: 1px solid #ccc; margin-top: 10px;"></canvas>
        <p style="font-size: 0.9em; color: #666;">注意：即使 N 很大，波形跳变处的“过冲”也不会消失，这就是吉布斯现象。</p>
    </div>

=== "🏔️ 分形噪声 (World Gen)"

    ### 几何级数：上帝的造山运动
    
    现代游戏（如 Minecraft, 塞尔达）里的地形不是美工画的，而是用**几何级数 (Geometric Series)** 算出来的。
    
    $$\text{地形} = \sum_{n=0}^{\infty} A^n \cdot \sin(2^n x)$$
    
    * **收敛 ($|A|<1$)：** 山脉的高度是有限的，形成自然起伏。
    * **发散 ($|A|>1$)：** 细节比主体还大，变成杂乱的噪声。
    
    #### 🎛️ 实验 2：分形地形生成器
    调整“公比 $r$”，看看级数收敛与发散对地形的影响。
    
    <div style="border: 1px solid #ddd; padding: 20px; border-radius: 8px; background: #f9f9f9;">
        <div style="display: flex; gap: 20px;">
            <div style="flex: 1;">
                <label><strong>粗糙度 (公比 r): <span id="frac-r-val">0.5</span></strong></label>
                <input type="range" id="frac-r" min="0.1" max="1.2" step="0.1" value="0.5" style="width: 100%;">
            </div>
            <div style="flex: 1;">
                <label><strong>细节层数 (项数): <span id="frac-n-val">6</span></strong></label>
                <input type="range" id="frac-n" min="1" max="8" step="1" value="6" style="width: 100%;">
            </div>
        </div>
        <div id="frac-status" style="font-weight: bold; color: green; margin: 5px 0;">✅ 级数收敛：自然山脉</div>
        <canvas id="frac-canvas" width="600" height="300" style="width: 100%; background: white; border: 1px solid #ccc; margin-top: 10px;"></canvas>
    </div>

=== "🔐 混沌与随机 (Security)"

    ### 数列行为：级数的前奏
    
    在讨论级数求和之前，我们必须先看**数列 (Sequence)** 本身的性质。
    
    * **收敛数列：** 最终趋向于一个固定值。如果不稳定，它就是**发散**的。
    * **混沌 (Chaos)：** 一种特殊的发散。它在固定范围内乱跳，看起来像随机数，但完全由公式确定。
    
    **应用：** 银行和加密货币利用这种“对初始条件极度敏感”的特性（蝴蝶效应）来生成随机密码。如果数列收敛，你的密码就被黑客预测了！
    
    #### 🎛️ 实验 3：蝴蝶效应模拟器
    对比两个初始值只差 `0.000001` 的序列。在 $r=3.9$ (混沌) 时，它们会在第几步分道扬镳？
    
    <div style="border: 1px solid #ddd; padding: 20px; border-radius: 8px; background: #f9f9f9;">
        <label><strong>生长参数 r: <span id="chaos-r-val">3.90</span></strong></label>
        <input type="range" id="chaos-r" min="2.5" max="4.0" step="0.01" value="3.9" style="width: 100%;">
        <p style="font-size: 0.9em;">🔵 用户 A (初始值 0.5) &nbsp;&nbsp; 🔴 黑客 B (初始值 0.500001)</p>
        <canvas id="chaos-canvas" width="600" height="300" style="width: 100%; background: white; border: 1px solid #ccc;"></canvas>
    </div>

---

<script>
// === Lab 1: Fourier Series Script ===
(function() {
    const slider = document.getElementById('fourier-n');
    const label = document.getElementById('fourier-n-val');
    const canvas = document.getElementById('fourier-canvas');
    const ctx = canvas.getContext('2d');

    function draw() {
        const N = parseInt(slider.value);
        label.textContent = N;
        const width = canvas.width;
        const height = canvas.height;
        
        ctx.clearRect(0, 0, width, height);
        
        // Draw Axis
        ctx.beginPath();
        ctx.strokeStyle = '#eee';
        ctx.moveTo(0, height/2);
        ctx.lineTo(width, height/2);
        ctx.stroke();

        // Draw Target Square Wave (Gray)
        ctx.beginPath();
        ctx.strokeStyle = '#eee';
        ctx.lineWidth = 4;
        for(let x=0; x<width; x++) {
            const t = (x / width) * 4 * Math.PI; // 2 periods
            const y = (t % (2*Math.PI) < Math.PI) ? -0.8 : 0.8;
            const py = height/2 + y * (height/3);
            if(x===0) ctx.moveTo(x, py); else ctx.lineTo(x, py);
        }
        ctx.stroke();

        // Draw Fourier Sum (Blue)
        ctx.beginPath();
        ctx.strokeStyle = '#2196F3';
        ctx.lineWidth = 2;
        for (let x = 0; x < width; x++) {
            let t = (x / width) * 4 * Math.PI; // 0 to 4pi
            let sum = 0;
            // Sum of odd harmonics: sin(x) + 1/3sin(3x) + 1/5sin(5x)...
            for (let i = 1; i <= N; i += 2) {
                sum += (1/i) * Math.sin(i * t);
            }
            // Scale and center
            let y = height / 2 + sum * (height / 3) * -1.27; // 1.27 is to normalize 4/pi
            if (x === 0) ctx.moveTo(x, y);
            else ctx.lineTo(x, y);
        }
        ctx.stroke();
    }
    slider.addEventListener('input', draw);
    draw(); // Init
})();

// === Lab 2: Fractal Terrain Script ===
(function() {
    const rSlider = document.getElementById('frac-r');
    const nSlider = document.getElementById('frac-n');
    const rLabel = document.getElementById('frac-r-val');
    const nLabel = document.getElementById('frac-n-val');
    const status = document.getElementById('frac-status');
    const canvas = document.getElementById('frac-canvas');
    const ctx = canvas.getContext('2d');

    function draw() {
        const r = parseFloat(rSlider.value);
        const octaves = parseInt(nSlider.value);
        rLabel.textContent = r;
        nLabel.textContent = octaves;

        if(r >= 1.0) {
            status.textContent = "⚠️ 级数发散：噪点与混沌 (非自然地形)";
            status.style.color = "red";
        } else {
            status.textContent = "✅ 级数收敛：自然山脉";
            status.style.color = "green";
        }

        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.beginPath();
        ctx.moveTo(0, canvas.height/2);
        ctx.lineTo(canvas.width, canvas.height/2);
        ctx.strokeStyle = '#eee';
        ctx.stroke();

        ctx.beginPath();
        ctx.strokeStyle = (r >= 1.0) ? '#E91E63' : '#4CAF50';
        ctx.lineWidth = 2;

        for (let x = 0; x < canvas.width; x++) {
            let y = 0;
            let amp = 60;
            let freq = 0.02;
            for (let i = 0; i < octaves; i++) {
                y += amp * Math.sin(x * freq);
                amp *= r; // Geometric Series Decay
                freq *= 2;
            }
            const py = canvas.height/2 + y;
            if (x === 0) ctx.moveTo(x, py);
            else ctx.lineTo(x, py);
        }
        ctx.stroke();
    }
    rSlider.addEventListener('input', draw);
    nSlider.addEventListener('input', draw);
    draw();
})();

// === Lab 3: Chaos Script ===
(function() {
    const slider = document.getElementById('chaos-r');
    const label = document.getElementById('chaos-r-val');
    const canvas = document.getElementById('chaos-canvas');
    const ctx = canvas.getContext('2d');

    function draw() {
        const r = parseFloat(slider.value);
        label.textContent = r.toFixed(2);
        
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        // Axis
        ctx.beginPath(); ctx.strokeStyle='#eee';
        ctx.moveTo(0, canvas.height); ctx.lineTo(canvas.width, canvas.height); ctx.stroke();

        // Simulate two sequences
        let x1 = 0.5;
        let x2 = 0.500001;
        const steps = 60;
        const stepX = canvas.width / steps;

        // Draw User A (Blue)
        ctx.beginPath(); ctx.strokeStyle = '#2196F3'; ctx.lineWidth=2;
        let currX1 = x1;
        for(let i=0; i<steps; i++) {
            let plotX = i * stepX;
            let plotY = canvas.height - (currX1 * canvas.height * 0.9);
            if(i===0) ctx.moveTo(plotX, plotY); else ctx.lineTo(plotX, plotY);
            currX1 = r * currX1 * (1 - currX1);
        }
        ctx.stroke();

        // Draw Hacker B (Red)
        ctx.beginPath(); ctx.strokeStyle = '#F44336'; ctx.lineWidth=2;
        let currX2 = x2;
        // Use setLineDash to make it distinct
        ctx.setLineDash([5, 5]); 
        for(let i=0; i<steps; i++) {
            let plotX = i * stepX;
            let plotY = canvas.height - (currX2 * canvas.height * 0.9);
            if(i===0) ctx.moveTo(plotX, plotY); else ctx.lineTo(plotX, plotY);
            currX2 = r * currX2 * (1 - currX2);
        }
        ctx.stroke();
        ctx.setLineDash([]); // Reset
    }
    slider.addEventListener('input', draw);
    draw();
})();
</script>