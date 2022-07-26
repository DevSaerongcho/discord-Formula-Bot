<h1 align="center">리아 네트워크 공식 봇 구동 방식</h1>

 - 서버 보안과, 안전을 위해 index 코드만 올리고 나머지는 사진으로 대체 하겠습니다.
 - 사진은, 소스 코드가 아닌, 작동 후 작업 완료한 사진 들 입니다

<br>


- index.js

```c
const { 
	Client, 
	Collection,
	Intents, 
	Message
} = require('discord.js');
const client = new Client({ 
	intents: [Intents.FLAGS.GUILDS,
				Intents.FLAGS.GUILD_MEMBERS,
				Intents.FLAGS.DIRECT_MESSAGE_TYPING,
				Intents.FLAGS.GUILD_MESSAGES,
			],
			partials : ["MESSAGE", "CHANNEL", "REACTION"]});
const { token } = require('./config.json');
const fs = require('fs');
const path = require('path');
const { execute } = require('./commands/button');
const { Channel } = require('diagnostics_channel');
const { MessageEmbed } = require('discord.js');




client.commands = new Collection();
const commandsPath = path.join(__dirname, 'commands');
const commandFiles = fs.readdirSync(commandsPath).filter(file => file.endsWith('.js'));

for (const file of commandFiles) {
	const filePath = path.join(commandsPath, file);
	const command = require(filePath);
	// Set a new item in the Collection
	// With the key as the command name and the value as the exported module
	client.commands.set(command.data.name, command);
}





client.on("ready", () => {
  client.user.setActivity(`리아 네트워크`, { type: "COMPETING" })
  console.log("리아 네트워크 공식 봇 정상적으로 가동 완료")

})

client.on('guildMemberAdd', interaction  => {
	const embed = new MessageEmbed()
    .setTitle(`${interaction.user.tag} 님 서버 가입!`)
	.setDescription(`<@!${interaction.user.id}> 님 환영 합니다 !`)
	.setColor(0xA6A6A6)
	.setTimestamp(new Date())
    interaction.guild.channels.cache.get('986585978556796978').send({ embeds: [embed]});
	let welcomeRole = interaction.guild.roles.cache.find(role => role.name === '《    미인증     》');
    interaction.roles.add(welcomeRole);
})




client.on('interactionCreate', async interaction => {
	


	if (!interaction.isCommand()) return;

	
    const command = client.commands.get(interaction.commandName);

	if (!command) return;

	try {
		await command.execute(interaction);
	} catch (error) {
		console.error(error);
		await interaction.reply({ content: 'There was an error while executing this command!', ephemeral: true });
	}

	
});



client.login(token);
```
<br>
- 인증 시스템
<br>
<br>
<img src="https://cdn.discordapp.com/attachments/998559272155750482/1000359578430619729/91.PNG" />
<img src="https://cdn.discordapp.com/attachments/998559272155750482/1000359578124427355/02.PNG" />
<br>
- 명령어 시스템
<br>
<img src="https://cdn.discordapp.com/attachments/998559272155750482/1000360609302462524/01.PNG" />
