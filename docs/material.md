# 🌍 级数在现实世界 (Real World Applications)

> "微积分不仅是数学家的游戏，它是现代文明的底层代码。"

这一页将带你走出课本，看看 $\sum$ 符号是如何支撑起现代技术的。

---

## 🔬 交互式实验室 (Interactive Labs)

请点击下方的标签页切换不同的应用场景。

=== "🎵 信号压缩 (MP3/JPEG)"

    ### 级数逼近：抛弃“尾巴”的艺术
    
    MP3 音乐和 JPEG 图片为什么小？因为工程师利用了**傅里叶级数**，扔掉了人耳听不见的高频分量。
    
    #### 🎛️ 实验 1：方波的逼近与截断
    拖动滑块，观察正弦波如何变成方波。注意边缘的“尖角”（吉布斯现象）。
    
    <div style="border: 1px solid #ddd; padding: 20px; border-radius: 8px; background: #f9f9f9;">
        <label><strong>级数项数 N: <span id="fourier-n-val">1</span></strong></label>
        <input type="range" id="fourier-n" min="1" max="50" step="2" value="1" style="width: 100%;">
        <canvas id="fourier-canvas" width="600" height="300" style="width: 100%; background: white; border: 1px solid #ccc; margin-top: 10px;"></canvas>
    </div>

=== "🏔️ 分形噪声 (World Gen)"

    ### 几何级数：上帝的造山运动
    
    游戏里的地形是用**几何级数**算出来的。
    
    #### 🎛️ 实验 2：分形地形生成器
    调整“公比 $r$”，看看级数收敛（山脉）与发散（噪声）的区别。
    
    <div style="border: 1px solid #ddd; padding: 20px; border-radius: 8px; background: #f9f9f9;">
        <div style="display: flex; gap: 20px;">
            <div style="flex: 1;">
                <label><strong>粗糙度 (公比 r): <span id="frac-r-val">0.5</span></strong></label>
                <input type="range" id="frac-r" min="0.1" max="1.2" step="0.1" value="0.5" style="width: 100%;">
            </div>
            <div style="flex: 1;">
                <label><strong>细节层数: <span id="frac-n-val">6</span></strong></label>
                <input type="range" id="frac-n" min="1" max="8" step="1" value="6" style="width: 100%;">
            </div>
        </div>
        <div id="frac-status" style="font-weight: bold; color: green; margin: 5px 0;">✅ 级数收敛：自然山脉</div>
        <canvas id="frac-canvas" width="600" height="300" style="width: 100%; background: white; border: 1px solid #ccc; margin-top: 10px;"></canvas>
    </div>

=== "🔐 混沌与随机 (Security)"

    ### 数列行为：级数的前奏
    
    如果数列本身就**发散**且**混沌**，它就成了最好的加密工具。
    
    #### 🎛️ 实验 3：蝴蝶效应模拟器
    对比两个初始值只差 `0.000001` 的序列，在 $r=3.9$ 时会发生什么？
    
    <div style="border: 1px solid #ddd; padding: 20px; border-radius: 8px; background: #f9f9f9;">
        <label><strong>生长参数 r: <span id="chaos-r-val">3.90</span></strong></label>
        <input type="range" id="chaos-r" min="2.5" max="4.0" step="0.01" value="3.9" style="width: 100%;">
        <p style="font-size: 0.9em;">🔵 用户 A (初始值 0.5) &nbsp;&nbsp; 🔴 黑客 B (初始值 0.500001)</p>
        <canvas id="chaos-canvas" width="600" height="300" style="width: 100%; background: white; border: 1px solid #ccc;"></canvas>
    </div>

---

<script>
// Lab 1: Fourier
(function() {
    setTimeout(function() { // 延迟加载以确保 Canvas 已渲染
        const slider = document.getElementById('fourier-n');
        const label = document.getElementById('fourier-n-val');
        const canvas = document.getElementById('fourier-canvas');
        if(!canvas) return; 
        const ctx = canvas.getContext('2d');

        function draw() {
            const N = parseInt(slider.value);
            label.textContent = N;
            const w = canvas.width; const h = canvas.height;
            ctx.clearRect(0,0,w,h);
            
            ctx.beginPath(); ctx.strokeStyle='#eee'; ctx.moveTo(0,h/2); ctx.lineTo(w,h/2); ctx.stroke();
            
            // Gray Square Wave
            ctx.beginPath(); ctx.strokeStyle='#eee'; ctx.lineWidth=4;
            for(let x=0; x<w; x++) {
                const t = (x/w)*4*Math.PI;
                const y = (t%(2*Math.PI)<Math.PI) ? -0.8 : 0.8;
                const py = h/2 + y*(h/3);
                if(x===0) ctx.moveTo(x,py); else ctx.lineTo(x,py);
            }
            ctx.stroke();

            // Blue Fourier
            ctx.beginPath(); ctx.strokeStyle='#2196F3'; ctx.lineWidth=2;
            for(let x=0; x<w; x++) {
                let t = (x/w)*4*Math.PI;
                let sum = 0;
                for(let i=1; i<=N; i+=2) sum += (1/i)*Math.sin(i*t);
                let y = h/2 + sum*(h/3)*-1.27;
                if(x===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
            }
            ctx.stroke();
        }
        slider.addEventListener('input', draw);
        draw();
    }, 500);
})();

// Lab 2: Fractal
(function() {
    setTimeout(function() {
        const rSlider = document.getElementById('frac-r');
        const nSlider = document.getElementById('frac-n');
        const rLabel = document.getElementById('frac-r-val');
        const nLabel = document.getElementById('frac-n-val');
        const status = document.getElementById('frac-status');
        const canvas = document.getElementById('frac-canvas');
        if(!canvas) return;
        const ctx = canvas.getContext('2d');

        function draw() {
            const r = parseFloat(rSlider.value);
            const oct = parseInt(nSlider.value);
            rLabel.textContent = r; nLabel.textContent = oct;
            
            if(r>=1.0) { status.textContent="⚠️ 级数发散：噪声"; status.style.color="red"; }
            else { status.textContent="✅ 级数收敛：自然山脉"; status.style.color="green"; }

            const w=canvas.width; const h=canvas.height;
            ctx.clearRect(0,0,w,h);
            ctx.beginPath(); ctx.strokeStyle='#eee'; ctx.moveTo(0,h/2); ctx.lineTo(w,h/2); ctx.stroke();

            ctx.beginPath(); ctx.strokeStyle=(r>=1.0)?'#E91E63':'#4CAF50'; ctx.lineWidth=2;
            for(let x=0; x<w; x++) {
                let y=0; let amp=60; let freq=0.02;
                for(let i=0; i<oct; i++) {
                    y += amp*Math.sin(x*freq);
                    amp *= r; freq *= 2;
                }
                const py = h/2 + y;
                if(x===0) ctx.moveTo(x,py); else ctx.lineTo(x,py);
            }
            ctx.stroke();
        }
        rSlider.addEventListener('input', draw);
        nSlider.addEventListener('input', draw);
        draw();
    }, 500);
})();

// Lab 3: Chaos
(function() {
    setTimeout(function() {
        const slider = document.getElementById('chaos-r');
        const label = document.getElementById('chaos-r-val');
        const canvas = document.getElementById('chaos-canvas');
        if(!canvas) return;
        const ctx = canvas.getContext('2d');

        function draw() {
            const r = parseFloat(slider.value);
            label.textContent = r.toFixed(2);
            const w=canvas.width; const h=canvas.height;
            ctx.clearRect(0,0,w,h);
            ctx.beginPath(); ctx.strokeStyle='#eee'; ctx.moveTo(0,h); ctx.lineTo(w,h); ctx.stroke();

            let x1=0.5; let x2=0.500001;
            const steps=60; const stepX=w/steps;

            // User A
            ctx.beginPath(); ctx.strokeStyle='#2196F3'; ctx.lineWidth=2;
            let cx1=x1;
            for(let i=0; i<steps; i++) {
                let px=i*stepX; let py=h-(cx1*h*0.9);
                if(i===0) ctx.moveTo(px,py); else ctx.lineTo(px,py);
                cx1=r*cx1*(1-cx1);
            }
            ctx.stroke();

            // Hacker B
            ctx.beginPath(); ctx.strokeStyle='#F44336'; ctx.lineWidth=2; ctx.setLineDash([5,5]);
            let cx2=x2;
            for(let i=0; i<steps; i++) {
                let px=i*stepX; let py=h-(cx2*h*0.9);
                if(i===0) ctx.moveTo(px,py); else ctx.lineTo(px,py);
                cx2=r*cx2*(1-cx2);
            }
            ctx.stroke(); ctx.setLineDash([]);
        }
        slider.addEventListener('input', draw);
        draw();
    }, 500);
})();
</script>