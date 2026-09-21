<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Para ti 💛</title>

<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    html, body {
        width: 100%;
        height: 100%;
        overflow: hidden;
        background: #000;
    }

    body {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    canvas {
        display: block;
        width: 100vw;
        height: 100vh;
    }

    .message {
        position: absolute;
        bottom: 7%;
        left: 50%;
        transform: translateX(-50%);
        color: rgba(255, 215, 80, 0);
        font-family: Georgia, serif;
        font-size: clamp(16px, 2vw, 25px);
        letter-spacing: 3px;
        transition: 2s ease;
        pointer-events: none;
        text-align: center;
    }

    .message.show {
        color: rgba(255, 215, 80, .9);
        text-shadow:
            0 0 5px #ffd84d,
            0 0 15px #ffbf00,
            0 0 30px #ff9900;
    }
</style>
</head>

<body>

<canvas id="canvas"></canvas>

<div class="message" id="message">
    Para ti 💛
</div>

<script>

const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

let W, H;
let particles = [];
let blinkers = [];

function resize() {
    W = canvas.width = window.innerWidth * devicePixelRatio;
    H = canvas.height = window.innerHeight * devicePixelRatio;

    canvas.style.width = window.innerWidth + "px";
    canvas.style.height = window.innerHeight + "px";

    ctx.setTransform(1, 0, 0, 1, 0, 0);
}

resize();
window.addEventListener("resize", resize);


/* =====================================================
   CONFIGURACIÓN
===================================================== */

const GOLD = "#ffd83d";
const YELLOW = "#ffe76a";

const SCALE = Math.min(window.innerWidth, window.innerHeight) / 700;


/* =====================================================
   CREAR LUZ
===================================================== */

function createLight(x, y, size = 1, brightness = 1) {

    particles.push({
        x,
        y,
        size,
        brightness,
        phase: Math.random() * Math.PI * 2,
        speed: .01 + Math.random() * .025,

        // Algunas luces parpadean
        blink: Math.random() < .22,

        // pequeño movimiento
        drift: Math.random() * Math.PI * 2
    });
}


/* =====================================================
   LÍNEA DE LUCES
===================================================== */

function lineLights(x1, y1, x2, y2, amount, size = 1) {

    for (let i = 0; i <= amount; i++) {

        const t = i / amount;

        const x = x1 + (x2 - x1) * t;
        const y = y1 + (y2 - y1) * t;

        createLight(
            x + (Math.random() - .5) * 2,
            y + (Math.random() - .5) * 2,
            size
        );
    }
}


/* =====================================================
   PÉTALO
===================================================== */

function petal(cx, cy, angle, length, width) {

    const steps = Math.floor(length / 3);

    for (let i = 0; i < steps; i++) {

        const t = i / steps;

        const distance = t * length;

        // forma del pétalo
        const spread =
            Math.sin(t * Math.PI) *
            width;

        const x =
            cx +
            Math.cos(angle) * distance +
            Math.cos(angle + Math.PI / 2) *
            (Math.random() - .5) *
            spread;

        const y =
            cy +
            Math.sin(angle) * distance +
            Math.sin(angle + Math.PI / 2) *
            (Math.random() - .5) *
            spread;

        createLight(
            x,
            y,
            .7 + Math.random() * .6
        );
    }
}


/* =====================================================
   FLOR
===================================================== */

function flower(cx, cy, radius, petals = 12) {

    // pétalos
    for (let i = 0; i < petals; i++) {

        const angle =
            (Math.PI * 2 / petals) * i;

        petal(
            cx,
            cy,
            angle,
            radius,
            radius * .28
        );
    }

    // centro de la flor
    for (let i = 0; i < 35; i++) {

        const angle = Math.random() * Math.PI * 2;
        const r = Math.random() * radius * .25;

        createLight(
            cx + Math.cos(angle) * r,
            cy + Math.sin(angle) * r,
            1.1
        );
    }
}


/* =====================================================
   HOJAS
===================================================== */

function leaf(cx, cy, angle, length) {

    const steps = Math.floor(length / 2.5);

    for (let i = 0; i < steps; i++) {

        const t = i / steps;

        const distance = t * length;

        const width =
            Math.sin(t * Math.PI) *
            length * .22;

        const x =
            cx +
            Math.cos(angle) * distance +
            Math.cos(angle + Math.PI / 2) *
            (Math.random() - .5) *
            width;

        const y =
            cy +
            Math.sin(angle) * distance +
            Math.sin(angle + Math.PI / 2) *
            (Math.random() - .5) *
            width;

        createLight(
            x,
            y,
            .55 + Math.random() * .5
        );
    }
}


/* =====================================================
   RAMO
===================================================== */

function createBouquet() {

    particles = [];

    const cx = W / 2;
    const cy = H / 2;

    const s = Math.min(W, H) / 900;


    /* -------------------------
       TALLOS
    ------------------------- */

    const stems = [
        [-100, 170, -40, -70],
        [-70, 170, -70, -160],
        [-35, 170, -10, -190],
        [0, 170, 0, -230],
        [35, 170, 30, -180],
        [70, 170, 75, -150],
        [105, 170, 130, -80]
    ];

    for (const stem of stems) {

        lineLights(
            cx + stem[0] * s,
            cy + stem[1] * s,

            cx + stem[2] * s,
            cy + stem[3] * s,

            45,
            .8
        );
    }


    /* -------------------------
       FLORES GRANDES
    ------------------------- */

    flower(
        cx - 90 * s,
        cy - 150 * s,
        70 * s,
        14
    );

    flower(
        cx,
        cy - 220 * s,
        82 * s,
        16
    );

    flower(
        cx + 85 * s,
        cy - 145 * s,
        65 * s,
        13
    );


    /* -------------------------
       FLORES PEQUEÑAS
    ------------------------- */

    flower(
        cx - 155 * s,
        cy - 80 * s,
        35 * s,
        9
    );

    flower(
        cx + 150 * s,
        cy - 80 * s,
        38 * s,
        9
    );

    flower(
        cx - 30 * s,
        cy - 80 * s,
        32 * s,
        9
    );


    /* -------------------------
       HOJAS
    ------------------------- */

    const leaves = [

        [-100, 100, -2.7],
        [-125, 45, -2.4],
        [-70, 30, 2.8],

        [90, 100, -.4],
        [125, 45, -.7],
        [70, 25, .3],

        [-45, -20, -2.8],
        [50, -20, -.3]
    ];

    for (const leafData of leaves) {

        leaf(
            cx + leafData[0] * s,
            cy + leafData[1] * s,
            leafData[2],
            70 * s
        );
    }


    /* -------------------------
       ENVOLTURA
    ------------------------- */

    const wrapTop = cy + 120 * s;
    const wrapBottom = cy + 300 * s;

    for (let i = 0; i < 70; i++) {

        const t = i / 70;

        const left =
            cx - (150 - t * 95) * s;

        const right =
            cx + (150 - t * 95) * s;

        lineLights(
            left,
            wrapTop + t * 170 * s,

            right,
            wrapTop + t * 170 * s,

            25,
            .5
        );
    }


    /* -------------------------
       MOÑO
    ------------------------- */

    lineLights(
        cx,
        cy + 285 * s,
        cx - 70 * s,
        cy + 325 * s,
        30,
        .8
    );

    lineLights(
        cx,
        cy + 285 * s,
        cx + 70 * s,
        cy + 325 * s,
        30,
        .8
    );

    lineLights(
        cx - 70 * s,
        cy + 325 * s,
        cx - 100 * s,
        cy + 375 * s,
        25,
        .7
    );

    lineLights(
        cx + 70 * s,
        cy + 325 * s,
        cx + 100 * s,
        cy + 375 * s,
        25,
        .7
    );
}

createBouquet();


/* =====================================================
   DIBUJAR LUCES
===================================================== */

function drawParticle(p) {

    let brightness = p.brightness;

    if (p.blink) {

        brightness =
            .25 +
            Math.pow(
                (Math.sin(
                    performance.now() * p.speed +
                    p.phase
                ) + 1) / 2,
                5
            ) * 1.5;
    }

    const radius =
        p.size *
        (1 + brightness * .25);

    const gradient =
        ctx.createRadialGradient(
            p.x,
            p.y,
            0,
            p.x,
            p.y,
            radius * 7
        );

    gradient.addColorStop(
        0,
        `rgba(255,245,150,${.95 * brightness})`
    );

    gradient.addColorStop(
        .2,
        `rgba(255,215,50,${.7 * brightness})`
    );

    gradient.addColorStop(
        1,
        "rgba(255,180,0,0)"
    );

    ctx.fillStyle = gradient;

    ctx.beginPath();

    ctx.arc(
        p.x,
        p.y,
        radius * 7,
        0,
        Math.PI * 2
    );

    ctx.fill();


    // núcleo brillante
    ctx.fillStyle =
        `rgba(255,245,170,${Math.min(1, brightness)})`;

    ctx.beginPath();

    ctx.arc(
        p.x,
        p.y,
        radius,
        0,
        Math.PI * 2
    );

    ctx.fill();


    // Algunas luces generan una pequeña estrella
    if (p.blink && brightness > 1.3) {

        ctx.strokeStyle =
            `rgba(255,235,120,${brightness * .7})`;

        ctx.lineWidth = .6;

        ctx.beginPath();

        ctx.moveTo(
            p.x - radius * 5,
            p.y
        );

        ctx.lineTo(
            p.x + radius * 5,
            p.y
        );

        ctx.moveTo(
            p.x,
            p.y - radius * 5
        );

        ctx.lineTo(
            p.x,
            p.y + radius * 5
        );

        ctx.stroke();
    }
}


/* =====================================================
   ANIMACIÓN
===================================================== */

function animate() {

    requestAnimationFrame(animate);

    ctx.clearRect(0, 0, W, H);

    // Negro ligeramente iluminado
    ctx.fillStyle = "#000";
    ctx.fillRect(0, 0, W, H);

    for (const particle of particles) {
        drawParticle(particle);
    }
}

animate();


/* =====================================================
   MENSAJE
===================================================== */

// Después de unos segundos aparece discretamente
setTimeout(() => {
    document
        .getElementById("message")
        .classList.add("show");
}, 3500);

</script>

</body>
</html>
