# DJS-Basics

Basics for DJS latest version

This code example will show you how to register multiple slash commands against the Discord API.

For more about the functions in Discord JS go to the offical Discord JS Website https://discord.js.org/#/docs/discord.js/main/general/welcome

DJS also has a discord check it out https://discord.gg/djs

To install all necessary dependencies run these commands in your terminal: 
```
npm install discord.js
npm install fs
```

<details><summary>Registering Slash Commands</summary>
<p>

```javascript

// // // // // //
// src/index.js
// // // // // //

const {GatewayIntentBits, Collection, REST, Routes, Client} = require('discord.js')
const fs = require('fs')
const client = new Discord.Client({intents: [GatewayIntentBits.Guilds] })

// // // // // // // // // // // 
// Commands folder is src/cmds/
// // // // // // // // // // // 

// // // // // // // // // // //
// Getting and Loading commands
// // // // // // // // // // //
folder = `${__dirname}/cmds/`
console.log("Loading commands...")
const cmds = []
const commandFiles = fs.readdirSync(folder)
commandFiles.filter(f => fs.statSync(folder + f).isDirectory())
    .forEach(nested => fs.readdirSync(folder + nested).forEach(f => commandFiles.push(nested + '/' + f)));

client.cmds = new Collection()

const cmdFiles = commandFiles.filter(f => f.endsWith('.js'));
if (commandFiles.length > 0) {
    console.log(`Found ${cmdFiles.length} files to load!\n`);
    
    for (const f of cmdFiles) {
        console.log(`Loading '${f}'...`);
        const command = require(folder + f);
        cmds.push(command.data.toJSON())
        client.cmds.set(command.data.name, command)

    }
}

client.once("ready", () => {
    const CLIENT_ID = client.user.id;
    const GUILD_ID = '332997002826874882'
    
    const rest = new REST({version: '10'}).setToken(config.token);

    (async () => {
        try {
              await rest.put(Routes.applicationGuildCommands(CLIENT_ID, GUILD_ID), { body: cmds })
              console.log("Slash commands registered locally!")
          } catch (e) {
              console.error(e)
          }
    })();
})
// // // // // // // // // // // // //
// End Of Getting and Loading Commands
// // // // // // // // // // // // //

client.login('YOUR BOT TOKEN')

```
</p>
</details>

<details><summary>Basic Slash Command</summary>
<p>

```javascript

// // // // // // // // // // // // // // // // // //
// This command's directory would be src/cmds/ping.js
// // // // // // // // // // // // // // // // // //

const {SlashCommandBuilder, Interaction} = require('discord.js')

module.exports = {
  data: new SlashCommandBuilder()
      .setName('ping')
      .setDescription('Replies with pong'),
      /**
      * @param {Interaction} interaction
      */
    async execute(interaction) {
      interaction.reply({content: 'Pong!', ephermal: true}) // Ephermal only shows the reply to the user that ran the command
  }
}
```
</p>
</details>
