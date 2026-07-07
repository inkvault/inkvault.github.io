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
        { cat: "main", file: "engine.cc:112", msg: "trading engine v4.2.0 starting", delay: 50 },
        { cat: "main", file: "engine.cc:140", msg: "loading risk limits and instrument config", delay: 80 },
        { cat: "feed", file: "md_feed.cc:88", msg: "connecting to market data feed: nasdaq-itch.internal:9001", delay: 200 },
        { cat: "feed", file: "md_feed.cc:104", msg: "market data feed connected, subscribing to symbols", delay: 150 },
        { cat: "feed", file: "md_feed.cc:130", msg: "subscribed: AAPL, TSLA, NVDA, AMZN, MSFT, GOOG, META", delay: 120 },
        { cat: "book", file: "order_book.cc:210", msg: "order book initialized for 7 instruments", delay: 130 },
        { cat: "risk", file: "risk_engine.cc:75", msg: "risk engine online, max position size loaded", delay: 110 },
        { cat: "strategy", file: "strategy_mgr.cc:40", msg: "loading strategy: mean_reversion_v2", delay: 140 },
        { cat: "strategy", file: "strategy_mgr.cc:58", msg: "strategy warmed up, awaiting signal", delay: 160 },
        { cat: "oms", file: "oms.cc:301", msg: "order management system connected to exchange gateway", delay: 180 },
        { cat: "main", file: "engine.cc:220", msg: "all systems nominal, entering live trading loop", delay: 100 }
    ];

    const symbols = ["AAPL", "TSLA", "NVDA", "AMZN", "MSFT", "GOOG", "META"];
    const sides = ["BUY", "SELL"];
    const orderTypes = ["LIMIT", "MARKET", "IOC"];
    const statuses = ["FILLED", "FILLED", "FILLED", "PARTIAL", "REJECTED", "CANCELLED"];

    const systemEvents = [
        { cat: "risk", file: "risk_engine.cc:120", msg: "position check passed, exposure within limits" },
        { cat: "feed", file: "md_feed.cc:150", msg: "market data heartbeat ok, latency {lat}ms" },
        { cat: "book", file: "order_book.cc:260", msg: "order book snapshot synced, seq {ver}" },
        { cat: "strategy", file: "strategy_mgr.cc:80", msg: "signal recalculated, confidence {n}%" }
    ];

    const updateTerminal = (line) => {
        buffer.shift();
        buffer.push(line);
        terminal.innerText = buffer.join('\n');
    };

    const makeBootLog = (entry) => {
        const ts = getTimestamp();
        return `[${ts}][1][info][${entry.cat}] [${entry.file}] ${entry.msg}`;
    };

    const generateLog = () => {
        const ts = getTimestamp();
        const symbol = symbols[Math.floor(Math.random() * symbols.length)];
        const side = sides[Math.floor(Math.random() * sides.length)];
        const type = orderTypes[Math.floor(Math.random() * orderTypes.length)];
        const status = statuses[Math.floor(Math.random() * statuses.length)];
        const price = (Math.random() * 500 + 50).toFixed(2);
        const size = Math.floor(Math.random() * 900 + 100);
        const latency = (Math.random() * 8 + 0.2).toFixed(2);
        return `[${ts}] ${side} ${type} ${symbol} x${size} @ ${price} - ${status} ${latency}ms`;
    };

    const generateSystemEvent = () => {
        const event = systemEvents[Math.floor(Math.random() * systemEvents.length)];
        const ver = Math.random().toString(16).substring(2, 8);
        const lat = (Math.random() * 3 + 0.1).toFixed(1);
        const n = Math.floor(Math.random() * 30) + 65;
        const msg = event.msg.replace("{ver}", ver).replace("{lat}", lat).replace("{n}", n);
        const ts = getTimestamp();
        return `[${ts}][1][info][${event.cat}] [${event.file}] ${msg}`;
    };

    // Initialize
    terminal.innerText = buffer.join('\n');

    let bootIndex = 0;
    const runBootstep = () => {
        if (bootIndex < bootSequence.length) {
            const step = bootSequence[bootIndex];
            updateTerminal(makeBootLog(step));
            bootIndex++;
            const nextDelay = bootIndex < bootSequence.length ? bootSequence[bootIndex].delay : 3000;
            setTimeout(runBootstep, nextDelay);
        } else {
            loop();
        }
    };

    const loop = () => {
        const isSystem = Math.random() < 0.20;
        const logLine = isSystem ? generateSystemEvent() : generateLog();
        updateTerminal(logLine);
        const isBurst = Math.random() > 0.05;
        const delay = isBurst ? Math.random() * 200 : Math.random() * 4000 + 1500;
        setTimeout(loop, delay);
    };

    setTimeout(runBootstep, 400);
})();
</script>
{% end %}

Welcome to my personal site. This is where I'll be sharing my adventures, writings, and projects. Currently based in West Virginia, USA.

