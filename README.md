# Azure Functions (PowerShell) Discord Bot

An example bot for Discord written in PowerShell for Azure Functions.

This is based on a presentation give at the PowerShell Summit 2023 ([PPTX + Code here](https://github.com/ThePoShWolf/Sessions/tree/master/2023-04-26%20-%20Building%20a%20serverless%20Discord%20bot)).

If you have suggestions to improve this, please submit a PR!

If you have questions or trouble getting this to work, feel free to open an issue and I will do my best to help.

## Setup

### Discord

1. Create a Discord Application in the [Discord Developer portal](https://discord.com/developers)
2. Add a bot to your Discord Application
3. Create a URL with at least the following permissions:
    1. bot
    2. application.commands
    3. bot\Send Messages
4. Navigate to that URL using your browser and add the bot to your server.
5. Copy the Public Key

### Azure

1. Create a Function App (Linux w/PowerShell 7.2+)
2. Add the public key of your Discord bot to the app configuration as `DISCORD_PUBLIC_KEY`.
3. Add one of the function's keys into the app configuration as `FUNCTIONKEY`
4. Deploy this repository to the application using VS Code (needs Azure and Azure Functions extensions)
5. Copy the HTTPStart function URL
6. Ensure the Function App is warm (wait for the monitor to run or send a few POST requests)

### Back to Discord

1. Paste the HTTPStart function URL (including key) into the Interactions Endpoint URL field.

You will not be able to save unless you have the function app configured properly!

### Add commands

Using the [Discordant](https://github.com/theposhwolf/discordant) module:

```powershell
# Authenticate with bot token (from Discord dev portal):
Connect-Discord -RestClient -TokenType Bot -Token '<token>'

# Get your guild (server):
$guild = Get-DiscordGuild | Where-Object { $_.Name -eq 'Bot Testing' }

# No parameters
# this command is already supported out of the box by the bot
New-DiscordGuildCommand -Name 'hello' -Description "Say hello to the bot" -Guild $guild -CommandBuilder (
    New-DiscordSlashCommand -Name 'hello' -Description 'Say Hello to the bot'
)

# Single string parameter
New-DiscordGuildCommand -Name 'start-server' -Description 'Start a server' -Guild $guild -CommandBuilder (
    New-DiscordSlashCommand -Name 'start-server' -Description 'Start a server' -Options @(
        (New-DiscordSlashCommandOption -Name 'server' -Description 'The selected server' -Type String)
    )
)
```

Both of the above example commands already exist in the [Orchestrator](orchestrator/run.ps1). To add more, simply extend the `switch` statement.