# paste di file bot.js
const mineflayer = require('mineflayer');
const dns = require('dns');

const SERVER_HOST = 'mymcserver.aternos.me';
const SERVER_PORT = 21943;
const USERNAME = 'playerone';

const messages = ['p', 'masih aktif', 'hi', 'hai'];

let reconnectDelay = 30000; // mulai dari 30 detik

function checkDNS(callback) {
  dns.lookup(SERVER_HOST, (err, address) => {
    if (err) {
      console.log(`[WAIT] Nunggu server siap... (DNS gak ketemu)`);
      setTimeout(() => checkDNS(callback), 5000);
    } else {
      console.log(`[DNS OK] Alamat IP: ${address}`);
      callback();
    }
  });
}

function startBot() {
  checkDNS(() => {
    const bot = mineflayer.createBot({
      host: SERVER_HOST,
      port: SERVER_PORT,
      username: USERNAME,
    });

    bot.on('spawn', () => {
      console.log('[SPAWN] Bot masuk server');

      // Chat otomatis tiap 5–10 menit
      setInterval(() => {
        const randomMessage = messages[Math.floor(Math.random() * messages.length)];
        bot.chat(randomMessage);
        console.log(`[CHAT] Bot mengirim pesan: ${randomMessage}`);
      }, Math.floor(Math.random() * (600000 - 300000 + 1)) + 300000);

      // Gerak random tiap 5–15 detik
      const actions = ['forward', 'back', 'left', 'right'];
      setInterval(() => {
        const action = actions[Math.floor(Math.random() * actions.length)];
        bot.setControlState(action, true);
        setTimeout(() => bot.setControlState(action, false), 1000);
      }, Math.floor(Math.random() * (15000 - 5000 + 1)) + 5000);

      // Loncat random tiap 10–20 detik
      setInterval(() => {
        bot.setControlState('jump', true);
        setTimeout(() => bot.setControlState('jump', false), 500);
      }, Math.floor(Math.random() * (20000 - 10000 + 1)) + 10000);

      // Swing/mukul tiap 10–20 detik
      setInterval(() => {
        bot.swingArm();
      }, Math.floor(Math.random() * (20000 - 10000 + 1)) + 10000);

      // Sneak random tiap 15–30 detik
      setInterval(() => {
        bot.setControlState('sneak', true);
        setTimeout(() => bot.setControlState('sneak', false), 2000);
      }, Math.floor(Math.random() * (30000 - 15000 + 1)) + 15000);

      // Liat sekitar random tiap 10–20 detik
      setInterval(() => {
        const yaw = Math.random() * Math.PI * 2;
        const pitch = (Math.random() - 0.5) * 0.5;
        bot.look(yaw, pitch, true);
      }, Math.floor(Math.random() * (20000 - 10000 + 1)) + 10000);

      // Lari sambil belok-belok tiap 7–15 detik, durasi 3 detik
      setInterval(() => {
        bot.setControlState('sprint', true);
        bot.setControlState('forward', true);

        const yaw = Math.random() * Math.PI * 2;
        const pitch = (Math.random() - 0.5) * 0.5;
        bot.look(yaw, pitch, true);

        setTimeout(() => {
          bot.setControlState('forward', false);
          bot.setControlState('sprint', false);
        }, 3000);
      }, Math.floor(Math.random() * (15000 - 7000 + 1)) + 7000);
    });

    bot.on('kicked', (reason) => {
      console.log('[KICKED]', reason);
    });

    bot.on('error', (err) => {
      console.log('[ERROR]', err.message);
    });

    bot.on('end', () => {
      console.log(`[DISCONNECT] Bot disconnect... retry dalam ${reconnectDelay / 1000} detik`);
      reconnectDelay = Math.min(reconnectDelay + 10000, 60000);
      setTimeout(startBot, reconnectDelay);
    });
  });
}

startBot();
