# DisBull Library 

# ¿Qué es esto?

**__DisBull__** es un módulo útil para facilitar la creación de bots de discord mediante **discords.js v12**.

**Nota**: este módulo utiliza características recientes de discordjs y requiere discord.js versión 12.

## Antes de empezar

**__DisBull__** todavía está en beta, se realizarán muchos cambios, muchas cosas añadidas como muchas otras eliminadas.


##  Enlaces de utilidad

* [Servidor de soporte](https://discord.gg/q99CQEP)
* Repositorio](https://github.com/DisBull/DisBull)
* [Danos una estrella](https://github.com/DisBull/DisBull/stargazers)

## Instalación

```sh
npm install --save disbull.js
```

## Estructura básica

```js
const { Client, SQLiteProvider } = require("disbull.js");

const sqlite = require("sqlite");
const path = require("path");
const oneLine = require("common-tags").oneLine;

const client = new Client({
  owner: ["<your-id>", "<another-id>"],
  commandPrefix: "<your-prefix>",
  token: "<secret-token>"
});

// base de datos necesaria (Configuración del servidor)
sqlite.open(path.join(__dirname, "/Data/settings.sqlite3")).then(db => {
  client.setProvider(new SQLiteProvider(db));
});
// Nota: No usa mucho espacio, esta super optimizada la base
client.registry.registerCommandsIn(path.join(__dirname, "commands"));
client.registry.registerEventsIn(path.join(__dirname, "events"));
client.registry.registerLocalesIn(path.join(__dirname, "locales"));

client
  .on("commandPrefixChange", (guild, prefix) => {
    console.log(oneLine`
    Prefix ${prefix === "" ? "removed" : `cambiado a ${prefijo || "the default"}`}
    ${guild ? `in guild ${guild.name} (${guild.id})` : "globally"}.
  `);
  })
  .on("localeChange", (guild, locale) => {
    console.log(oneLine`
			Locale ${locale ? `changed to ${locale}` : `changed to the default.`}
			${guild ? `in guild ${client.guilds.cache.get(guild).name} (${guild})` : "globally"}
		`);
  });

client.run();
```

## Estructura básica del Comando

```js
const { Command } = require("disbull.js");

class Ping extends Command {

    constructor (client) {
        super(client, {
            name: "ping",
            description: (language) => language.get("PING_DESCRIPTION"),
            usage: (language) => language.get("PING_USAGE"),
            examples: (language) => language.get("PING_EXAMPLES"),
            dirname: __dirname,
            enabled: true,
            guildOnly: false,
            aliases: [ "pong", "latency" ],
            memberPermissions: ["ADMINISTRATOR"],
            botPermissions: [ "SEND_MESSAGES" ],
            nsfw: false,
            ownerOnly: false,
            cooldown: 1000
        });
    }

    async run (msg, args) {
      let ping = Math.floor(msg.client.ws.ping);
      
      msg.say(msg.language.get("PING", 0.0000)).then((m) => {
        m.edit(msg.language.get("PING", ping));
      });
    }

}

module.exports = Ping;
```

## Evento básico 

```js
const Discord = require("discord.js");
const { version } = require("disbull.js");

module.exports = class {

    constructor(client) {
        this.client = client;
    }

    async run() {
      
      this.client.logger.info(`Cargar un total de comandos de ${this.client.commands.size}`, { tag: "BOT" });
      this.client.logger.log('${this.client.user.tag}, listo para servir a un total de ${this.client.users.cache.size} usuarios en el total de ${this.client.guilds.cache.size} servidores `, { tag: "BOT" } );

      let toDisplay = "{USER} en {serversCount} servidores".replace("{serversCount}", this.client.guilds.cache.size).replace("{USER}", this.client.user.tag) + " | v" + version;
      
      this.client.user.setPresence({
          status: "dnd",
          activity: {
            name: toDisplay,
            type: 3
          }
        })
    }
}
```

## Nuevo idioma estructura   

```js
const {
    Locale
} = require('disbull.js')

const e = {
    error: "❌",
    success: "✅"
}

class fr extends Locale {
    constructor(client) {
        super(client, 'fr') // <-- id locale here ej: en-us, es-mx, fr

        this.language = {
            PREFIX_INFO: (prefix) => `le préfixe de ce serveur est \'${prefix}\``,
            /* DEFAULT MESSAGES */
            NO_DESCRIPTION_PROVIDED: "Aucune description donnée",
            NO_USAGE_PROVIDED: "Aucune utilisation donnée",
            NO_EXAMPLE_PROVIDED: "Aucun exemple donné",

            // ERROR MESSAGES

            ERR_COMMAND_DISABLED: `${e.error} | Cette commande est actuellement désactivée !`,
            ERR_OWNER_ONLY: `${e.error} | Seul ${c.owner.name} peut effectuer ces commandes !`,
            ERR_INVALID_CHANNEL: `${e.error} | Veuillez mentionner un salon valide !`,
            ERR_INVALID_ROLE: `${e.error} | Veuillez mentionner un rôle valide !`,
            ERR_INVALID_MEMBER: `${e.error} | Veuillez mentionner un membre valide !`,
            ERR_INVALID_NUMBER: (nan) => `${e.error} | \`${nan}\` n'est pas un nombre valide !`,
            ERR_INVALID_NUMBER_MM: (min, max) => `${e.error} Veuillez indiquer un nombre valide entre ${min} et ${max} !`,
            ERR_INVALID_TIME: `${e.error} | Vous devez entrer un temps valide ! Unités valides : \`s\`, \`m\`, \`h\`, \`d\`, \`w\`, \`y\``,
            ERR_INVALID_ID: `${e.error} | Veuillez entrer une ID valide !`,

            ERR_MISSING_BOT_PERMS: (perms) => `${e.error} | J'ai besoin des permissions suivantes pour effectuer cette commande : \`${perms}\``,
            ERR_MISSING_MEMBER_PERMS: (perm) => `${e.error} | Vous n'avez pas les permissions nécessaires pour effectuer cette commande (\`${perm}\`)`,
            ERR_NOT_NSFW: `${e.error} | Vous devez vous rendre dans un salon qui autorise le NSFW pour taper cette commande !`,
            ERR_GUILDONLY: `${e.error} | Cette commande est uniquement disponible sur un serveur !`,
            ERR_UNAUTHORIZED_CHANNEL: (channel) => `${e.error} | Les commandes sont interdites dans ${channel} !`,
            ERR_BAD_PARAMETERS: (cmd, prefix) => `${e.error} | Veuillez vérifier les paramètres de la commande. Regardez les exemples en tapant \`${prefix}help ${cmd}\` !`,
            ERR_ROLE_NOT_FOUND: (role) => `${e.error} | Aucun rôle trouvé avec \`${role}\` !`,
            ERR_CHANNEL_NOT_FOUND: (channel) => `${e.error} | Aucun salon trouvé avec \`${channel}\``,
            ERR_YES_NO: `${e.error} | Vous devez répondre par "oui" ou par "non" !`,
            ERR_EVERYONE: `${e.error} | Vous n'avez pas l'autorisation de mentionner everyone ou here dans les commandes.`,
            ERR_BOT_USER: `${e.error} | Cet utilisateur est un bot !`,
            ERR_GAME_ALREADY_LAUNCHED: `${e.error} | Une partie est déjà en cours sur ce serveur !`,
            ERR_A_GAME_ALREADY_LAUNCHED: `${e.error} | A cause des lags et bugs dus au findwords et au number, il est impossible de lancer deux parties en même temps, même si elles sont sur deux serveurs différents.\nIl y a une partie actuellement en cours sur un autre serveur, veuillez donc patientez quelques minutes puis réessayer.\nNous sommes désolés, mais des personnes abusaient de cette commande en la spammant sur pleins de serveurs.`,
            ERR_OCCURENCED: `${e.error} | Une erreur est survenue, veuillez réessayez dans quelques minutes.`,
            ERR_CMD_COOLDOWN: (seconds) => `${e.error} | Vous devez attendre **${seconds}** seconde(s) pour pouvoir de nouveau effectuer cette commande !`,
            ERR_SANCTION_YOURSELF: `${e.error} | Vous ne pouvez pas vous sanctionner vous-même !`,

            // Utils
            PING_DESCRIPTION: "Affiche la latence du bot",
            PING_USAGE: "ping",
            PING_EXAMPLES: "ping",
            // Content
            PING: (ms) => `${e.success} | Pong ! Ma latence est de \`${ms}\` ms !`,

        }
    }
}

module.exports = fr
```
