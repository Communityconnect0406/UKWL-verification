


<html lang="en">
<head>
<meta charset="UTF-8">
<title>UKWL • Verification</title>

<style>
    body {
        margin: 0;
        background: #0d0d0d;
        font-family: Arial, sans-serif;
        color: white;
    }

    .page {
        width: 100%;
        max-width: 500px;
        margin: 0 auto;
        padding: 25px;
        display: none;
    }

    .active {
        display: block;
    }

    h2 {
        text-align: center;
        margin-bottom: 10px;
    }

    p {
        text-align: center;
        opacity: 0.9;
    }

    input, textarea {
        width: 100%;
        padding: 12px;
        margin: 10px 0;
        border-radius: 6px;
        border: none;
        outline: none;
        font-size: 16px;
        box-sizing: border-box;
    }

    textarea {
        min-height: 80px;
        resize: vertical;
    }

    button {
        width: 100%;
        padding: 14px;
        background: #00c853;
        border: none;
        border-radius: 6px;
        font-size: 18px;
        cursor: pointer;
        color: white;
        margin-top: 15px;
        opacity: 0.4;
        transition: 0.3s;
    }

    button.enabled {
        opacity: 1;
    }
</style>
</head>
<body>

<!-- PAGE 1 -->
<div id="page1" class="page active">
    <h2>👋 Welcome to UKWL • UK West London Roleplay</h2>
    <p>Before you can participate, you must verify yourself by answering a few questions.</p>
    <button id="btn1" class="enabled">Continue</button>
</div>

<!-- PAGE 2 -->
<div id="page2" class="page">
    <h2>Basic Information</h2>
    <input id="discordUser" placeholder="Discord Username">
    <input id="discordID" placeholder="Discord ID">
    <input id="robloxUser" placeholder="Roblox Username">
    <input id="robloxID" placeholder="Roblox ID">
    <button id="btn2">✔ Continue</button>
</div>

<!-- PAGE 3 -->
<div id="page3" class="page">
    <h2>Security Questions</h2>
    <textarea id="globalBan" placeholder="Are you globally banned anywhere? Why?"></textarea>
    <textarea id="alts" placeholder="Do you have any alternate accounts?"></textarea>
    <button id="btn3">✔ Continue</button>
</div>

<!-- PAGE 4 -->
<div id="page4" class="page">
    <h2>Final Questions</h2>
    <textarea id="wish" placeholder="What do you wish to do in our community?"></textarea>
    <textarea id="guidelines" placeholder="Do you agree to follow all guidelines?"></textarea>
    <textarea id="tos" placeholder="Do you agree to follow Discord TOS?"></textarea>
    <input id="age" placeholder="What is your age? (9+)">
    <button id="btn4">✔ Submit</button>
</div>

<!-- PAGE 5 -->
<div id="page5" class="page">
    <h2>Thank you!</h2>
    <p>Your form has been submitted and will be reviewed automatically.</p>
</div>

<script>
// PAGE SWITCHING
function showPage(id) {
    document.querySelectorAll(".page").forEach(p => p.classList.remove("active"));
    document.getElementById(id).classList.add("active");
    window.scrollTo(0, 0);
}

// ENABLE BUTTON WHEN FIELDS ARE FILLED
function enableOnFill(fields, button) {
    function check() {
        const filled = fields.every(f => document.getElementById(f).value.trim() !== "");
        if (filled) button.classList.add("enabled");
        else button.classList.remove("enabled");
    }
    fields.forEach(f => document.getElementById(f).addEventListener("input", check));
    check();
}

// PAGE 1
document.getElementById("btn1").onclick = () => showPage("page2");

// PAGE 2
enableOnFill(["discordUser","discordID","robloxUser","robloxID"], document.getElementById("btn2"));
document.getElementById("btn2").onclick = () => {
    if (!btn2.classList.contains("enabled")) return;
    showPage("page3");
};

// PAGE 3
enableOnFill(["globalBan","alts"], document.getElementById("btn3"));
document.getElementById("btn3").onclick = () => {
    if (!btn3.classList.contains("enabled")) return;
    showPage("page4");
};

// PAGE 4
enableOnFill(["wish","guidelines","tos","age"], document.getElementById("btn4"));
document.getElementById("btn4").onclick = submitForm;

// GLOBAL BAN LIST
const globalBanned = {
    "foxysbestfriend": "1475778804709331048",
    "galaxy011756": "1473356679734104177",
    "lostrbxofficial": "1460360628924317888"
};

// AUTO-MODERATION
function evaluate(data) {
    let status = "Accepted";
    let reason = "No issues detected.";

    if (parseInt(data.age) < 9) {
        status = "Declined";
        reason = "User does not meet age requirement.";
    }

    for (let name in globalBanned) {
        if (
            data.robloxUser.toLowerCase() === name ||
            data.discordUser.toLowerCase() === name ||
            data.discordID === globalBanned[name] ||
            data.robloxID === globalBanned[name]
        ) {
            status = "Declined";
            reason = "User matches global banned identity.";
        }
    }

    if (data.globalBan.toLowerCase().includes("yes")) {
        status = "Pending";
        reason = "User claims to be globally banned — requires manual review.";
    }

    return { status, reason };
}

// SUBMIT FORM
function submitForm() {
    if (!btn4.classList.contains("enabled")) return;

    const data = {
        discordUser: discordUser.value,
        discordID: discordID.value,
        robloxUser: robloxUser.value,
        robloxID: robloxID.value,
        globalBan: globalBan.value,
        alts: alts.value,
        wish: wish.value,
        guidelines: guidelines.value,
        tos: tos.value,
        age: age.value
    };

    const result = evaluate(data);

    const webhook = "https://discord.com/api/webhooks/1489801541400658030/pGJcMTEqkHFtjVQW17P7NDRAwNnKP6r18kEEGuDU_7GIRIhovYSynX4IrSBa2Di3YbPo";

    const payload = {
        content: "<@&1489795617185071286> <@&1489796282703937656> <@&1489796448231887020>",
        embeds: [
            {
                title: "UKWL • Verification Submission",
                color:
                    result.status === "Accepted"
                        ? 65280
                        : result.status === "Declined"
                        ? 16711680
                        : 16776960,
                fields: [
                    { name: "Discord Username", value: data.discordUser },
                    { name: "Discord ID", value: data.discordID },
                    { name: "Roblox Username", value: data.robloxUser },
                    { name: "Roblox ID", value: data.robloxID },
                    { name: "Global Ban?", value: data.globalBan },
                    { name: "Alternate Accounts", value: data.alts },
                    { name: "Wish in Community", value: data.wish },
                    { name: "Agrees to Guidelines", value: data.guidelines },
                    { name: "Agrees to Discord TOS", value: data.tos },
                    { name: "Age", value: data.age },
                    { name: "Status", value: result.status },
                    { name: "Auto‑Moderation Reason", value: result.reason }
                ]
            }
        ]
    };

    fetch(webhook, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload)
    });

    showPage("page5");
}
</script>

</body>
</html>
