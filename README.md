# stock-calc
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>台股損益計算機</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #f4f7f6; display: flex; justify-content: center; padding: 20px; }
        .card { background: white; padding: 2rem; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); width: 100%; max-width: 450px; }
        h2 { color: #333; text-align: center; margin-bottom: 1.5rem; }
        .input-group { margin-bottom: 1rem; }
        label { display: block; margin-bottom: 5px; color: #666; font-size: 0.9rem; }
        input { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 1rem; }
        .result-area { margin-top: 1.5rem; padding-top: 1.5rem; border-top: 2px solid #eee; }
        .result-item { display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 1.1rem; }
        .profit { color: #e63946; font-weight: bold; } /* 漲/盈 */
        .loss { color: #2a9d8f; font-weight: bold; }   /* 跌/虧 */
        .hint { font-size: 0.8rem; color: #999; margin-top: 10px; }
    </style>
</head>
<body>

<div class="card">
    <h2>台股損益計算機</h2>
    
    <div class="input-group">
        <label>買入價格 (元)</label>
        <input type="number" id="buyPrice" placeholder="例如: 100" oninput="calculate()">
    </div>
    
    <div class="input-group">
        <label>賣出價格 (元)</label>
        <input type="number" id="sellPrice" placeholder="例如: 110" oninput="calculate()">
    </div>
    
    <div class="input-group">
        <label>股數 (1張 = 1000股)</label>
        <input type="number" id="shares" value="1000" oninput="calculate()">
    </div>

    <div class="input-group">
        <label>手續費折扣 (例如: 6折輸入 0.6)</label>
        <input type="number" id="discount" value="0.6" step="0.1" oninput="calculate()">
    </div>

    <div class="result-area">
        <div class="result-item">
            <span>漲跌幅:</span>
            <span id="rangeText">0%</span>
        </div>
        <div class="result-item">
            <span>總損益 (含稅費):</span>
            <span id="totalProfit">$0</span>
        </div>
        <div class="result-item">
            <span>報酬率 (ROI):</span>
            <span id="roiText">0%</span>
        </div>
    </div>

    <div class="hint">
        * 預設手續費 0.1425%, 證交稅 0.3%<br>
        * 手續費未滿 20 元以 20 元計
    </div>
</div>

<script>
    function calculate() {
        const buyPrice = parseFloat(document.getElementById('buyPrice').value) || 0;
        const sellPrice = parseFloat(document.getElementById('sellPrice').value) || 0;
        const shares = parseInt(document.getElementById('shares').value) || 0;
        const discount = parseFloat(document.getElementById('discount').value) || 1;

        if (buyPrice <= 0 || sellPrice <= 0) return;

        // 1. 漲跌幅計算
        const range = ((sellPrice - buyPrice) / buyPrice) * 100;
        const rangeEl = document.getElementById('rangeText');
        rangeEl.innerText = range.toFixed(2) + "%";
        rangeEl.className = range >= 0 ? "profit" : "loss";

        // 2. 成本計算 (買入手續費)
        let buyFee = Math.floor(buyPrice * shares * 0.001425 * discount);
        if (buyFee < 20) buyFee = 20;
        const totalCost = (buyPrice * shares) + buyFee;

        // 3. 收入計算 (賣出手續費 + 證交稅)
        let sellFee = Math.floor(sellPrice * shares * 0.001425 * discount);
        if (sellFee < 20) sellFee = 20;
        const tax = Math.floor(sellPrice * shares * 0.003);
        const totalRevenue = (sellPrice * shares) - sellFee - tax;

        // 4. 純利潤
        const netProfit = totalRevenue - totalCost;
        const roi = (netProfit / totalCost) * 100;

        const profitEl = document.getElementById('totalProfit');
        const roiEl = document.getElementById('roiText');

        profitEl.innerText = (netProfit >= 0 ? "+" : "") + netProfit.toLocaleString() + " 元";
        profitEl.className = netProfit >= 0 ? "profit" : "loss";
        
        roiEl.innerText = roi.toFixed(2) + "%";
        roiEl.className = netProfit >= 0 ? "profit" : "loss";
    }
</script>

</body>
</html>
