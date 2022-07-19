# DJS-Basics

Basics for DJS v13.8.1

This code example will show you how to register multiple slash commands against the Discord API.

If you need to know more about the functions in Discord JS go to the offical Discord JS Website https://discord.js.org/#/docs/discord.js/main/general/welcome

<details><summary>Registering Slash Commands</summary>
<p>

```javascript
const Discord = require('discord.js')
const {Intents, Collection} = require('discord.js')
const fs = require('fs')
const {REST} = require('@discordjs/rest')
const {Routes} = require('discord-api-types/v10')
const client = new Discord.Client({intents: [Intents.FLAGS.GUILD, Intents.FLAGS.GUILD_MESSAGES, Intents.Flags.GUILD_MEMBERS, Intents.FLAGS.DIRECT_MESSAGES] })

// // // // // // // // // // // 
// Commands folder is src/cmds/
// // // // // // // // // // // 

// // // // // // // // // // //
// Getting and Loading commands
// // // // // // // // // // //
folder = `${__dirname}/cmds/`
const cmds = []
const files = fs.readdirSync(folder)
files.filter(f => fs.statSync(folder + f).isDirectory())
    .forEach(nested => fs.readdirSync(folder + nested).forEach(f => files.push(nested + '/' + f)));
client.cmds = new Collection()
const commandsFiles = files.filter(f => f.endsWith('.js'));
if (files.length > 0) {
    console.log(`Found ${commandsFiles.length} files to load!\n`);
    for (const f of commandsFiles) {
        console.log(`Loading '${f}'...`);
        const command = require(folder + f);
        cmds.push(command.data.toJSON())
        client.cmds.set(command.data.name, command)
    }
}
client.once("ready", () => {
    const CLIENT_ID = client.user.id;
    const GUILD_ID = 'YOUR GUILD ID'
    const rest = new REST({version: '10'}).setToken(YOUR BOT TOKEN);
    (async () => {
        try {
          if (process.env.ENV === "production") {
            await rest.put(Routes.applicationCommands(CLIENT_ID), {
                body: cmds
            });
            console.log("Slash commands registered globally!")
          } else {
            await rest.put(Routes.applicationGuildCommands(CLIENT_ID, GUILD_ID), {
                body: cmds
            });
            console.log("Slash commands registered locally!")
          }
        } catch (e) {
            // ignore
        }
    })();
})
client.on('interactionCreate', async interaction => {
    if (!interaction.isCommand()) return;
    
    const command = client.cmds.get(interaction.commandName);
    if (!command) return;
    try {
        await command.execute(interaction);
    } catch (e) {
        // ignore
    }
})
// // // // // // // // // // // // //
// End Of Getting and Loading Commands
// // // // // // // // // // // // //
```
</p>
</details>

<details><summary>Basic Slash Command</summary>
<p>

```javascript
const {SlashCommandBuilder} = require('@discordjs/builders')
const {Interaction} = require('discord.js')

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
