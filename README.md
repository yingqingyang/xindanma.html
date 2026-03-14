# xindanma.html
谷歌做的新网格代码
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>均衡策略计算器 - yingqingyang</title>
    <style>
        body { font-family: -apple-system, sans-serif; background: #121212; color: #e0e0e0; padding: 15px; }
        .card { background: #1e1e1e; padding: 20px; border-radius: 12px; border: 1px solid #333; }
        h2 { color: #f0b90b; text-align: center; margin-bottom: 20px; }
        .input-group { margin-bottom: 15px; }
        label { display: block; font-size: 14px; margin-bottom: 5px; color: #aaa; }
        input { width: 100%; padding: 10px; border-radius: 6px; border: 1px solid #444; background: #2a2a2a; color: white; box-sizing: border-box; }
        button { width: 100%; padding: 12px; background: #f0b90b; border: none; border-radius: 6px; font-weight: bold; cursor: pointer; margin-top: 10px; }
        .result { margin-top: 20px; padding: 15px; border-radius: 8px; background: #252525; }
        .highlight { color: #f0b90b; font-weight: bold; }
        .risk-warn { color: #ff4d4d; font-weight: bold; }
        .nav-link { text-align: center; margin-top: 20px; display: block; color: #888; text-decoration: none; font-size: 12px; }
    </style>
</head>
<body>

<div class="card">
    <h2>⚖️ ETH 均衡策略推演</h2>
    
    <div class="input-group">
        <label>当前 ETH 价格 (U)</label>
        <input type="number" id="price" value="2200">
    </div>

    <div style="display: flex; gap: 10px;">
        <div class="input-group" style="flex:1;">
            <label>网格下限</label>
            <input type="number" id="low" value="1900">
        </div>
        <div class="input-group" style="flex:1;">
            <label>网格上限</label>
            <input type="number" id="high" value="2500">
        </div>
    </div>

    <div class="input-group">
        <label>投入本金 (U)</label>
        <input type="number" id="capital" value="1000">
    </div>

    <div class="input-group">
        <label>对冲空单量 (ETH)</label>
        <input type="number" id="short" value="0.7">
    </div>

    <div class="input-group">
        <label>现货底仓 (ETH)</label>
        <input type="number" id="spot" value="0.3">
    </div>

    <button onclick="calculate()">开始推演</button>

    <div id="resultArea" class="result" style="display:none;">
        <p>📈 网格内多单持仓: <span id="gridEth" class="highlight">0</span> ETH</p>
        <p>🛡️ 账户净敞口: <span id="netExposure" class="highlight">0</span> ETH</p>
        <p>📊 风险状态: <span id="riskStatus">安全</span></p>
        <hr style="border: 0.5px solid #333;">
        <p style="font-size: 13px; color: #888;">注：当净敞口为正时，你在做多；为负时，你在做空。</p>
    </div>
</div>

<a href="https://yingqingyang.github.io/btc.html/btc.html" class="nav-link">📈 切换到 BTC 定投计算器</a>

<script>
function calculate() {
    const price = parseFloat(document.getElementById('price').value);
    const low = parseFloat(document.getElementById('low').value);
    const high = parseFloat(document.getElementById('high').value);
    const capital = parseFloat(document.getElementById('capital').value);
    const short = parseFloat(document.getElementById('short').value);
    const spot = parseFloat(document.getElementById('spot').value);
    
    // 计算网格内的多单持仓 (简化线性模型)
    let gridEth = 0;
    const maxEth = (capital * 5) / ((low + high) / 2); // 粗略估算最大持仓
    
    if (price <= low) {
        gridEth = maxEth;
    } else if (price >= high) {
        gridEth = 0;
    } else {
        // 价格越低，持仓越多
        gridEth = maxEth * (1 - (price - low) / (high - low));
    }

    // 净敞口 = 网格多单 + 现货底仓 - 空单对冲
    const net = (gridEth + spot - short).toFixed(3);
    
    document.getElementById('gridEth').innerText = gridEth.toFixed(3);
    document.getElementById('netExposure').innerText = net;
    
    const statusEl = document.getElementById('riskStatus');
    if (price >= high) {
        statusEl.innerText = "⚠️ 已突破上限，当前为纯空头！";
        statusEl.className = "risk-warn";
    } else if (price <= low) {
        statusEl.innerText = "⚠️ 已跌破下限，当前为重仓多头！";
        statusEl.className = "risk-warn";
    } else {
        statusEl.innerText = net > 0 ? "✅ 轻度看涨震荡中" : "✅ 轻度看空震荡中";
        statusEl.className = "highlight";
    }

    document.getElementById('resultArea').style.display = 'block';
}
</script>
</body>
</html>
