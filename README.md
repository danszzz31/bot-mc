# paste di file bot.js
const mineflayer = require('mineflayer');
const dns = require('dns');

const SERVER_HOST = 'mymcserver.aternos.me'; // ganti ini
const SERVER_PORT = 21943;
const USERNAME = 'BotAFK';

const messages = ['p', 'masih aktif', 'hi', 'hai'];
let reconnectDelay = 30000;

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
      viewDistance: 'tiny',
    });

    bot.on('spawn', () => {
      console.log('[SPAWN] Bot masuk server');

      setInterval(() => {
        const randomMessage = messages[Math.floor(Math.random() * messages.length)];
        bot.chat(randomMessage);
        console.log(`[CHAT] Bot mengirim: ${randomMessage}`);
      }, Math.floor(Math.random() * (600000 - 300000)) + 300000);

      // Gerak random
      const actions = ['forward', 'back', 'left', 'right'];
      setInterval(() => {
        const action = actions[Math.floor(Math.random() * actions.length)];
        bot.setControlState(action, true);
        if (Math.random() < 0.5) bot.setControlState('jump', true);
        if (Math.random() < 0.3) bot.setControlState('sneak', true);
        setTimeout(() => {
          bot.setControlState(action, false);
          bot.setControlState('jump', false);
          bot.setControlState('sneak', false);
        }, 1500);
      }, Math.floor(Math.random() * (15000 - 5000)) + 5000);

      // Lari belok-belok
      setInterval(() => {
        bot.setControlState('sprint', true);
        const direction = actions[Math.floor(Math.random() * actions.length)];
        bot.setControlState(direction, true);
        setTimeout(() => {
          bot.setControlState('sprint', false);
          bot.setControlState(direction, false);
        }, 3000);
      }, 20000);
    });

    bot.on('end', () => {
      console.log(`[DISCONNECT] Retry dalam ${reconnectDelay / 1000} detik`);
      reconnectDelay = Math.min(reconnectDelay + 10000, 60000);
      setTimeout(startBot, reconnectDelay);
    });

    bot.on('error', err => console.log('[ERROR]', err.message));
    bot.on('kicked', reason => console.log('[KICKED]', reason));
  });
}

startBot();
