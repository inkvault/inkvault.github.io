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
        { msg: "$ pkg install --recursive ./project", delay: 50 },
        { msg: "resolving dependency graph...", delay: 250 },
        { msg: "found 1,204 packages, 38 direct dependencies", delay: 200 },
        { msg: "checking lockfile integrity... ok", delay: 150 },
        { msg: "starting fetch stage (12 parallel workers)", delay: 180 },
    ];

    // Fake but plausible package names
    const prefixes = ["core", "util", "react", "async", "node", "http", "type", "data", "cli", "dev", "test", "build", "web", "fast", "micro", "meta"];
    const suffixes = ["kit", "js", "lib", "helper", "parser", "loader", "runtime", "cache", "stream", "compat", "tools", "engine", "adapter", "wrapper", "core"];

    const randomPkg = () => {
        const p = prefixes[Math.floor(Math.random() * prefixes.length)];
        const s = suffixes[Math.floor(Math.random() * suffixes.length)];
        const major = Math.floor(Math.random() * 8);
        const minor = Math.floor(Math.random() * 20);
        const patch = Math.floor(Math.random() * 30);
        return `${p}-${s}@${major}.${minor}.${patch}`;
    };

    const actions = [
        { verb: "fetching", weight: 35 },
        { verb: "resolving", weight: 20 },
        { verb: "building", weight: 15 },
        { verb: "linking", weight: 15 },
        { verb: "verifying", weight: 10 },
        { verb: "warning", weight: 5 }
    ];

    const warnings = [
        "deprecated, use v2 instead",
        "peer dependency mismatch, using closest match",
        "no license field found",
        "engine mismatch (expected node >=18)",
        "bundled dependency, skipping resolution"
    ];

    let installed = 0;
    let totalPkgs = 1204;

    const pickAction = () => {
        const total = actions.reduce((sum, a) => sum + a.weight, 0);
        let roll = Math.random() * total;
        for (const a of actions) {
            if (roll < a.weight) return a.verb;
            roll -= a.weight;
        }
        return "fetching";
    };

    const updateTerminal = (line) => {
        buffer.shift();
        buffer.push(line);
        terminal.innerText = buffer.join('\n');
    };

    const generateLine = () => {
        const verb = pickAction();
        const pkg = randomPkg();

        if (verb === "warning") {
            const warn = warnings[Math.floor(Math.random() * warnings.length)];
            return `⚠ warning ${pkg}: ${warn}`;
        }

        if (verb === "fetching") {
            const size = (Math.random() * 900 + 20).toFixed(0);
            const speed = (Math.random() * 8 + 0.5).toFixed(1);
            installed++;
            return `fetching   ${pkg} (${size}kb @ ${speed}mb/s)`;
        }

        if (verb === "building") {
            const time = (Math.random() * 2 + 0.1).toFixed(2);
            return `building   ${pkg} [${time}s]`;
        }

        if (verb === "linking") {
            return `linking    ${pkg} -> node_modules/.pkg`;
        }

        if (verb === "verifying") {
            return `verifying  ${pkg} checksum ok`;
        }

        return `resolving  ${pkg}`;
    };

    const generateProgressLine = () => {
        const pct = Math.min(100, ((installed / totalPkgs) * 100)).toFixed(1);
        const remaining = Math.max(0, totalPkgs - installed);
        return `[${pct}%] ${installed}/${totalPkgs} packages installed, ${remaining} remaining`;
    };

    let bootIndex = 0;
    const runBootstep = () => {
        if (bootIndex < bootSequence.length) {
            const step = bootSequence[bootIndex];
            updateTerminal(step.msg);
            bootIndex++;
            const nextDelay = bootIndex < bootSequence.length ? bootSequence[bootIndex].delay : 400;
            setTimeout(runBootstep, nextDelay);
        } else {
            loop();
        }
    };

    const loop = () => {
        const showProgress = Math.random() < 0.12;
        const line = showProgress ? generateProgressLine() : generateLine();
        updateTerminal(line);

        // reset the "install" once it completes, so it loops forever
        if (installed >= totalPkgs) {
            installed = 0;
            totalPkgs = Math.floor(Math.random() * 600) + 900;
        }

        const delay = Math.random() * 180 + 40;
        setTimeout(loop, delay);
    };

    terminal.innerText = buffer.join('\n');
    setTimeout(runBootstep, 300);
})();
</script>
{% end %}

Welcome to my personal site. This is where I'll be sharing my adventures, writings, and projects. Currently based in West Virginia, USA.

