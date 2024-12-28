
const { Telegraf } = require('telegraf');
const express = require('express');

const bot = new Telegraf(process.env.BOT_TOKEN || '7606916075:AAGvXjPrMDhc6HzYPV1IZ58zJVMjevMdZrc');
const app = express();

// Serve ads page
app.get('/ads', (req, res) => {
  res.send(`
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Watch Ads</title>
  </head>
  <body style="text-align: center; font-family: Arial;">
    <h1>Watch Ads to Earn Rewards</h1>
    <div id="ad-container" style="margin: 20px;">
      <script src='//niphaumeenses.net/vignette.min.js' data-zone='8713874' data-sdk='show_8713874'></script>
    </div>
    <script>
      show_8713874().then(() => {
        // Simulate a reward being sent
        fetch('/reward').then(() => {
          alert('You have earned your reward!');
        });
      });
    </script>
  </body>
  </html>
  `);
});

// Track rewards
let userPoints = {};

app.get('/reward', (req, res) => {
  const userId = req.query.userId || 'unknown';
  userPoints[userId] = (userPoints[userId] || 0) + 1;
  res.status(200).send('Reward granted.');
});

// Bot commands
bot.start((ctx) => {
  ctx.reply(`Welcome to the Ad Bot!\n
You can:
1️⃣ Watch ads manually.\n
2️⃣ Use autoplay for ads and earn points.\n\n
Use /watchads to watch ads or /points to check your points.`);
});

bot.command('watchads', (ctx) => {
  ctx.reply(`Click the link to watch ads and earn rewards:\n
🔗 http://localhost:3000/ads`);
});

bot.command('points', (ctx) => {
  const userId = ctx.from.id;
  const points = userPoints[userId] || 0;
  ctx.reply(`You have earned ${points} points so far.`);
});

// Launch the bot
bot.launch();

// Start the server for the ads
app.listen(3000, () => {
  console.log('Ad server running on http://localhost:3000');
});

process.once('SIGINT', () => bot.stop('SIGINT'));
process.once('SIGTERM', () => bot.stop('SIGTERM'));
