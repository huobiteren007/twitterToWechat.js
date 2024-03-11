const Twit = require('twit');
const { Wechaty } = require('wechaty');
const translate = require('@vitalets/google-translate-api');
const { PuppetWeChat } = require('wechaty-puppet-wechat');

const twitter = new Twit({
  consumer_key: 'YOUR_TWITTER_CONSUMER_KEY',
  consumer_secret: 'YOUR_TWITTER_CONSUMER_SECRET',
  access_token: 'YOUR_TWITTER_ACCESS_TOKEN',
  access_token_secret: 'YOUR_TWITTER_ACCESS_TOKEN_SECRET'
});

const puppet = new PuppetWeChat();
const wechaty = new Wechaty({ puppet });

const twitterUserIds = ['TWITTER_USER_ID_1', 'TWITTER_USER_ID_2']; // 要监控的Twitter用户ID

async function checkTwitterAndSendToWechat() {
  for (const userId of twitterUserIds) {
    const params = { follow: userId, count: 1 }; // 仅检查最新的一条推文
    const response = await twitter.get('statuses/user_timeline', params);
    const latestTweet = response.data[0];

    console.log(`Latest tweet from user ${userId}: ${latestTweet.text}`);

    // 将推文内容翻译成中文
    translate(latestTweet.text, { to: 'zh-CN' })
      .then(translation => {
        console.log(`Translated tweet: ${translation.text}`);
        // 将翻译后的推文内容发送到微信
        wechaty.say(`最新推文来自用户 ${userId}:\n${translation.text}`);
      })
      .catch(error => {
        console.error('Translation error:', error);
      });
  }
}

wechaty.on('scan', (qrcode, status) => {
  console.log(`Scan QR Code to login: ${status}\n${qrcode}`);
});

wechaty.on('login', user => {
  console.log(`User ${user} logged in`);
  // 每隔30秒检查一次Twitter并发送到微信
  setInterval(checkTwitterAndSendToWechat, 30000);
});

wechaty.start().then(() => {
  console.log('WeChat bot started');
});
