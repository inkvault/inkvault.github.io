+++
title = "Home"
template = "index.html"
+++

{% crt() %}
<pre id="crt-terminal"
    style="height: 7.5rem; line-height: 1.1rem; overflow: hidden; display: flex; flex-direction: column; justify-content: flex-start;"
>
</pre>
<script>
(function() {
    const terminal = document.getElementById('crt-terminal');
    if (!terminal) return;

    let buffer = Array(5).fill("");

    const getTimestamp = () => {
        const now = new Date();
        const parts = new Intl.DateTimeFormat('en-US', {
            timeZone: 'America/New_York',
            year: 'numeric', month: '2-digit', day: '2-digit',
            hour: '2-digit', minute: '2-digit', second: '2-digit',
            hour12: false
        }).formatToParts(now);
        const get = (type) => parts.find(p => p.type === type).value;
        const ms = String(now.getMilliseconds()).padStart(3, '0');
        return `${get('year')}-${get('month')}-${get('day')} ${get('hour')}:${get('minute')}:${get('second')}.${ms} EST`;
    };

    const bootSequence = [
        { msg: "options flow scanner v1.8.0 initializing", delay: 50 },
        { msg: "connecting to OPRA consolidated feed...", delay: 200 },
        { msg: "feed connected, subscribing to unusual activity alerts", delay: 150 },
        { msg: "loading watchlist: 40 underlyings", delay: 120 },
        { msg: "sweep detection threshold set to 3.0x avg volume", delay: 130 },
        { msg: "scanner armed, monitoring live order flow", delay: 100 }
    ];

    const underlyings = ["AAPL", "TSLA", "NVDA", "AMZN", "MSFT", "SPY", "QQQ", "META", "AMD", "NFLX"];
    const types = ["CALL", "PUT"];
    const sentiments = [""];

    // Each underlying keeps a rough persistent price so strikes look plausible
    const anchors = {
        AAPL: 195, TSLA: 249, NVDA: 134, AMZN: 202, MSFT: 422,
        SPY: 545, QQQ: 468, META: 587, AMD: 162, NFLX: 690
    };

    const randomExpiry = () => {
        const months = ["JUL", "AUG", "SEP", "OCT", "DEC", "JAN"];
        const m = months[Math.floor(Math.random() * months.length)];
        const d = Math.floor(Math.random() * 4) * 7 + Math.floor(Math.random() * 5) + 1;
        const yr = m === "JAN" ? "27" : "26";
        return `${m}${String(d).padStart(2, "0")}'${yr}`;
    };

    const nearStrike = (price, type) => {
        const offsetPct = (Math.random() - 0.4) * 0.12; // skew slightly OTM
        const raw = price * (1 + offsetPct);
        return Math.round(raw / 5) * 5; // round to nearest $5 strike
    };

    const updateTerminal = (line) => {
        buffer.shift();
        buffer.push(line);
        terminal.innerText = buffer.join('\n');
    };

    const generateFlow = () => {
        const ts = getTimestamp();
        const symbol = underlyings[Math.floor(Math.random() * underlyings.length)];
        const type = types[Math.floor(Math.random() * types.length)];
        const price = anchors[symbol];
        const strike = nearStrike(price, type);
        const expiry = randomExpiry();
        const size = Math.floor(Math.random() * 4000 + 200);
        const premiumPerContract = (Math.random() * 8 + 0.5).toFixed(2);
        const totalPremium = ((premiumPerContract * size * 100) / 1000).toFixed(0);
        const sentiment = sentiments[Math.floor(Math.random() * sentiments.length)];

        const isSweep = Math.random() < 0.18;
        const tag = isSweep ? "SWEEP" : "FLOW";

        return `[${ts}] ${symbol} ${strike}${type[0]} ${expiry} x${size} @ $${premiumPerContract} ($${totalPremium}k prem) ${sentiment}`;
    };

    const systemEvents = [
        { msg: "feed heartbeat ok, latency {lat}ms" },
        { msg: "watchlist refreshed, {n} underlyings active" },
        { msg: "volume baseline recalculated for session" },
        { msg: "sweep threshold auto-tuned to current volatility" }
    ];

    const generateSystemEvent = () => {
        const ts = getTimestamp();
        const event = systemEvents[Math.floor(Math.random() * systemEvents.length)];
        const lat = (Math.random() * 15 + 1).toFixed(0);
        const n = underlyings.length;
        const msg = event.msg.replace("{lat}", lat).replace("{n}", n);
        return `[${ts}][info][scanner] ${msg}`;
    };

    let bootIndex = 0;
    const runBootstep = () => {
        if (bootIndex < bootSequence.length) {
            const step = bootSequence[bootIndex];
            const ts = getTimestamp();
            updateTerminal(`[${ts}][info][main] ${step.msg}`);
            bootIndex++;
            const nextDelay = bootIndex < bootSequence.length ? bootSequence[bootIndex].delay : 800;
            setTimeout(runBootstep, nextDelay);
        } else {
            loop();
        }
    };

    const loop = () => {
        const isSystem = Math.random() < 0.15;
        const line = isSystem ? generateSystemEvent() : generateFlow();
        updateTerminal(line);
        const isBurst = Math.random() > 0.15;
        const delay = isBurst ? Math.random() * 600 + 200 : Math.random() * 3500 + 1500;
        setTimeout(loop, delay);
    };

    terminal.innerText = buffer.join('\n');
    setTimeout(runBootstep, 300);
})();
</script>
{% end %}

Welcome to my personal site. This is where I'll be sharing my adventures, writings, and projects. Currently based in West Virginia, USA.

## Reading list

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); gap: 24px; margin-top: 16px;">

  <a href="https://www.amazon.com/dp/0812979907" target="_blank" rel="noopener" aria-label="A Man for All Markets by Edward O. Thorp" style="text-decoration: none; color: inherit; display: block; transition: transform 0.15s ease;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
    <div style="width: 100%; aspect-ratio: 2/3; background-image: url('/images/books/thorp.jpg'); background-size: cover; background-position: center; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.25);"></div>
    <p style="margin: 10px 0 0; font-size: 14px; font-weight: bold;">A Man for All Markets</p>
    <p style="margin: 2px 0 0; font-size: 12px; opacity: 0.7;">Edward O. Thorp</p>
  </a>

  <a href="https://www.amazon.com/dp/1476766681" target="_blank" rel="noopener" aria-label="A Mind at Play by Jimmy Soni and Rob Goodman" style="text-decoration: none; color: inherit; display: block; transition: transform 0.15s ease;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
    <div style="width: 100%; aspect-ratio: 2/3; background-image: url('/images/books/soni-goodman.jpg'); background-size: cover; background-position: center; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.25);"></div>
    <p style="margin: 10px 0 0; font-size: 14px; font-weight: bold;">A Mind at Play</p>
    <p style="margin: 2px 0 0; font-size: 12px; opacity: 0.7;">Jimmy Soni &amp; Rob Goodman</p>
  </a>

  <a href="https://www.amazon.com/dp/1476728747" target="_blank" rel="noopener" aria-label="The Wright Brothers by David McCullough" style="text-decoration: none; color: inherit; display: block; transition: transform 0.15s ease;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
    <div style="width: 100%; aspect-ratio: 2/3; background-image: url('/images/books/wright-brothers.jpg'); background-size: cover; background-position: center; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.25);"></div>
    <p style="margin: 10px 0 0; font-size: 14px; font-weight: bold;">The Wright Brothers</p>
    <p style="margin: 2px 0 0; font-size: 12px; opacity: 0.7;">David McCullough</p>
  </a>

  <a href="https://www.amazon.com/Small-Difference-Raymond-Plank/dp/0533165873" target="_blank" rel="noopener" aria-label="A Small Difference by Raymond Plank" style="text-decoration: none; color: inherit; display: block; transition: transform 0.15s ease;" onmouseover="this.style.transform='translateY(-4px)'" onmouseout="this.style.transform='translateY(0)'">
    <div style="width: 100%; aspect-ratio: 2/3; background-image: url('/images/books/plank.jpg'); background-size: cover; background-position: center; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.25);"></div>
    <p style="margin: 10px 0 0; font-size: 14px; font-weight: bold;">A Small Difference</p>
    <p style="margin: 2px 0 0; font-size: 12px; opacity: 0.7;">Raymond Plank</p>
  </a>

</div>