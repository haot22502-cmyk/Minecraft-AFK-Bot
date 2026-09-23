mkdir minecraft-bot && cd minecraft-bot && npm init -y && npm install mineflayer && cat > bot.js <<'EOF'
const mineflayer = require("mineflayer");

// ===============================
// 🔧 CẤU HÌNH
// ===============================
const CONFIG = {
  host: "1212-cCnV.aternos.me",   // VD: play.example.com
  port: 23720,         // Port server
  username: "Aquanbellbot",
  auth: "offline",
  version: false
};

let bot;
let reconnectTimer = null;
let afkTimer = null;

// ===============================
// 🤖 TẠO BOT
// ===============================
function createBot() {
  console.log("🔄 Đang kết nối server...");

  bot = mineflayer.createBot(CONFIG);

  // Bot vào server
  bot.once("spawn", () => {
    console.log("================================");
    console.log("✅ BOT ĐÃ VÀO SERVER");
    console.log("🤖 Tên: " + CONFIG.username);
    console.log("🌐 Server: " + CONFIG.host + ":" + CONFIG.port);
    console.log("================================");

    // Chống AFK
    afkTimer = setInterval(() => {
      if (!bot || !bot.entity) return;

      bot.setControlState("jump", true);

      setTimeout(() => {
        if (bot) {
          bot.setControlState("jump", false);
        }
      }, 300);

    }, 60000);
  });

  // ===============================
  // 💬 LỆNH CHAT
  // ===============================
  bot.on("chat", (username, message) => {
    if (username === bot.username) return;

    console.log(`[CHAT] ${username}: ${message}`);

    // !ping
    if (message === "!ping") {
      bot.chat("Pong!");
    }

    // !info
    if (message === "!info") {
      bot.chat("Bot đang online AFK.");
    }

    // !pos
    if (message === "!pos") {
      const p = bot.entity.position;

      bot.chat(
        `XYZ: ${Math.floor(p.x)} ${Math.floor(p.y)} ${Math.floor(p.z)}`
      );
    }

    // !say
    if (message === "!say") {
      bot.chat("Bot vẫn đang hoạt động!");
    }

    // !help
    if (message === "!help") {
      bot.chat("Lenh: !ping !info !pos !say !help");
    }
  });

  // ===============================
  // ❌ BỊ KICK
  // ===============================
  bot.on("kicked", reason => {
    console.log("❌ Bot bị kick:", reason);
  });

  // ===============================
  // ⚠️ LỖI
  // ===============================
  bot.on("error", error => {
    console.log("⚠️ Lỗi:", error.message);
  });

  // ===============================
  // 🔄 TỰ KẾT NỐI LẠI
  // ===============================
  bot.on("end", () => {
    console.log("🔴 Bot mất kết nối!");

    if (afkTimer) {
      clearInterval(afkTimer);
      afkTimer = null;
    }

    if (reconnectTimer) return;

    console.log("⏳ Kết nối lại sau 10 giây...");

    reconnectTimer = setTimeout(() => {
      reconnectTimer = null;
      createBot();
    }, 10000);
  });
}

// ===============================
// 🚀 CHẠY BOT
// ===============================
createBot();
EOF
node bot.js
