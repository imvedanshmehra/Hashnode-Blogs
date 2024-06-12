---
title: "Telegram bot with Node.js and Telegraf"
datePublished: Wed Jun 12 2024 03:30:43 GMT+0000 (Coordinated Universal Time)
cuid: clxb9wx6500070al7f2l85vmk
slug: telegram-bot-with-nodejs-and-telegraf
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1718115915544/0ed707dd-64f7-4423-902f-4089deb66be5.png
tags: bot, javascript, nodejs, typescript, telegram

---

Telegram is one of the world's most popular instant messaging platform out there, with millions of active users per month, and it's also one of the best platforms when it comes to building your own `bots`.

> Bots are simply Telegram accounts operated by software – not people – and they'll often have AI features.

`Telegraf` is a Node.js library that lets us build Telegram bots easily using JavaScript or TypeScript.

I recently built the *world's first AI-powered nutritionist*, [Nutritionist-AI 🥦](http://nutritionistai.pro/). This Telegram bot is designed to read and understand food labels and answer all your nutrition and diet-related questions. In this blog, I'll share everything I learned while developing Nutritionist-AI, and we'll see how to build our own Telegram bot using `Telegraf` and `Node.js`.

---

### Before we start 🎬

To get started, I am assuming that you have basic knowledge of Node.Js, though it is not mandatory, but it will help you easily navigate through some of the terms and code blocks that I will be sharing in this blog.

Also, I am assuming that you have Node.Js installed in your system (pretty obvious).

If you meet these prerequisites, let's get started.

### Create a new Bot on Telegram 🤖

First things first, we need to create a new bot on Telegram. To do that, we need to open the Telegram app and search for `@BotFather` - it's a special bot on Telegram that allows us to create and manage new bots. Send a message to the BotFather with `/newbot` command, which is a special command to create new bots, it will create a brand new bot for you and will return a *bot token*.

> Your **bot token** is its unique identifier – store it in a **secure place**, and only share it with people who need direct access to the bot. Everyone who has your token will have **full control** over your bot.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1718112540585/256264a0-c31a-4004-8576-307a5526f457.png align="center")

Congratulations 🎉 With this you have successfully registered your new bot on Telegram, you can now access the bot using the bot username.

### Start building your bot 🔨

Although we have created our new bot on Telegram, it won't do anything yet. Now it's time to bring it to life by making it functional.

Let's create a new Node.js application and install `Telegraf` in it.

```bash
npm install telegraf 
```

Let's create an entry point file `index.js` inside our app and add the following code to it.

```javascript
const { Telegraf } = require("telegraf");

// Load your BOT TOKEN from .env file
const bot = new Telegraf(process.env.TELEGRAM_BOT_TOKEN);
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Make sure that you add your <code>.env</code> file to <strong>.gitignore</strong> so that you don't accidentally push it to the version control.</div>
</div>

If you are using Node `v20.6.0` or higher you no longer need to install any third-party libraries for hot module replacement or reading .env variables. Simply add the following `script` to your package.json file, and you should be good to go.

```json
"scripts": {
    "start": "node --watch --env-file=.env index.js"
  },
```

It will automatically read environment variables from your .env file and will perform hot reload whenever there are any changes in your code.

Let's create a .env file now and add your `TELEGRAM_BOT_TOKEN` there.

This will now connect to your Telegram bot, allowing you to start adding it's functionalities.

### Add your first command 🚀

Since we have now connected Node.Js to our Telegram bot, it's time to add our first command.

> Telegram commands are special types of messages that tell the bot what to do. Commands in Telegram start with `/`, for example, `/start`.

Whenever a bot is started for the first time, a default `/start` command is executed. Let's use that and send a welcome message to the user whenever they start a bot.

```javascript
const { Telegraf } = require("telegraf");

const bot = new Telegraf(process.env.TELEGRAM_BOT_TOKEN);

// Sends a welcome message
bot.start((ctx) => ctx.reply("Welcome to my bot."));

// Launch the bot
bot.launch();

// Enable graceful stop
process.once("SIGINT", () => bot.stop("SIGINT"));
process.once("SIGTERM", () => bot.stop("SIGTERM"));
```

The above code will launch the bot and send a welcome message to user whenever the bot is started.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1718113702858/a1157579-27df-4dfa-8ddc-075a1effcdd8.png align="center")

Perfect! Now our bot is sending messages, which is great. Let's do more.

### Add more Commands and reply to user's Messages 💬

The bot now sends a welcome message whenever it starts, but a Telegram bot can do much more. It can respond to your text messages, send stickers, and many other things.

Let's create a new `/help` command, by adding following code to the `index.js` file.

```javascript
bot.help((ctx) =>
  ctx.reply("For any help or assistance, reach out to us <your-email.com>")
);
```

This will show our support email to the user whenever they text `/help`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1718109473462/d591f345-c2d0-48b3-885f-32ac29c9caea.png align="center")

Since our bot is properly replying to the Telegram commands, let's now make our bot handle text messages, so that whenever the user sends any text to our bot, it should reply something in return to the user.

```javascript
bot.on(message("text"), (ctx) => ctx.reply("Your message received 👍"));
```

In order to make this work, we also need to import `messages`.

```javascript
const { message } = require("telegraf/filters");
```

This will make our bot send a reply every time a user sends a message to the bot.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1718114561958/bb1237e3-9fcb-46bd-98ca-e06d2e07e679.png align="center")

If you don't want to send just a plain text response, you can also send an `HTML` response, just replace the above code with this.

```javascript
bot.on(message("text"), (ctx) => ctx.replyWithHTML(`This is a special <b>HTML</b> message 🚀`));
```

This will send a HTML formatted response with the text *"HTML"* in bold.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1718110847492/4e2e748b-e457-4a06-877a-24361aaf6b18.png align="center")

Perfect! Our bot can now respond to text messages as well. You can now adjust the features, change the responses, and make the bot do much more. The possibilities are endless from here.

Complete code can be found here 👉 [https://github.com/imvedanshmehra/telegraf-starter-code](https://github.com/imvedanshmehra/telegraf-starter-code)

### Final Note 📝

We have seen how we can build a basic Telegram bot using Node.Js and Telegraf library that responds to user messages in text and HTML format, you can now explore the Telegram bot API to learn more about what a Telegram bot can do and start building your own Telegram bots that can be used by the millions of users around the world.

I hope you liked this article! If you did, please leave a ❤️ below and share your thoughts in the comments.

Also, if you liked this article, I'm sure you'll enjoy my tweets too. I regularly share posts about building products, JavaScript, and Web Development. Please check them out: [𝕏](https://x.com/imvedanshmehra).