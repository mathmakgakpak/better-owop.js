# better OWOP.js
Better OJS is just fixed ojs.

## TO-DO
destroy\
parseMessage\
ifIsConnectedToWorld

## changelog
getPixel is now queued so you can just use await getPixel\
EventEmitter for browser should be faster and node js uses normal EventEmitter\
Fixed gae memory leak which caused 2 gb of memory while requesting `200x200x2` chunks I did 500x500x2 screenshot using this [link](https://freeimage.host/i/1588530749940.JYf3Sj)

Added parseMessage (not finished)\
Added wolfMove to setPixel which checks if you must move or not\
Added login using Captcha token\
Added bot nick

Queued RequestChunk

Render captcha returns captcha token



Node.js library for [OWOP](https://ourworldofpixels.com "OWOP"). Support Discord server - [discord.gg/k4u7ddk](https://discord.gg/k4u7ddk "discord.gg/k4u7ddk").

Installing: `npm i owop-js`.\
**REQUIRES NODE.JS 12.0+!**

![Nodejs](https://img.shields.io/badge/-Node.js%2012.0%2B-brightgreen?style=for-the-badge&logo=node.js&labelColor=1a1a1a)

This is quite new package, so if you'll find any bugs, don't forget that you can contribute too! I'm often busy, so usually I'm not able to update anything quickly.

*You can also use browser version in [OPM](https://opm.glitch.me "OPM"). In OPM version there is `simpleChunks` option for using OWOP internal chunks and there is no `agent`, `origin`, `controller`, and other node.js only options.*

#### OPM Example
```js
const BOJS = OPM.require("better-owop-js");
const Client = new BOJS.Client({
    reconnect: true
});

Client.on("join", () => {
    Client.chat.send("Hello from browser BOJS!");
});
```

# Node.js Example
```js
const BOJS = require("better-owop-js");
const Client = new BOJS.Client({
    reconnect: true,
    controller: true
});

Client.on("join", () => {
    Client.chat.send("Hello, OWOP from BOJS!");
});
```

# Events
`open` - Opened WebSocket connection.\
`close` - Closed WebSocket connection.\
`join` - Joined to world [world name].\
`id` - Got id [id].\
`rawMessage` - Any message from WebSocket server. (It can be object or string) [data].\
`playerUpdate` - Player in world updates [player object].\
`pixelUpdate` - New pixel in world [pixel object which has id of player which set pixel only then if protocol is new].\
`disconnect` - Someone in world disconnected [player object].\
`playerConnect` - Someone in world connected [player object].\
`teleport` - got 'teleport' opcode. Very rare. [x, y].\
`rank` - Got new rank. [rank].\
`captcha` - Captcha state. [gcaptcha id].\
`chunkProtect` - Chunk (un)protected. [x, y, newState].\
`pquota` - New PQuota. [rate, per].\
`chunk` - New chunk. [x, y, chunk, protected].\
`message` - New message in chat. [msg].

# Options
`ws` - Websocket server address. (default - `wss://ourworldofpixels.com`)\
`origin` - Origin header (default - `https://ourworldofpixels.com`).\
`autoMakeSocket` - should make socket automatically (default - true)\
`autoConnectWorld` - should join world automatically (default - true)\
`protocol` - protocol id so if you set to 0 you will be able to connect bop it owop (default - 1)\
`captchaSiteKey` - buh (default - 6LcgvScUAAAAAARUXtwrM8MP0A0N70z4DHNJh-KI)\
`id` - ID for logging. If not set, OWOP ID will be used.\
`agent` - Proxy Agent.\
`world` -  World name. (default - `main`).\
`log` - default true.\
`reconnect` - Reconnect if disconnected.\
`adminlogin` -  Admin login.\
`modlogin` - Mod login.\
`pass` -  Pass for world.\
`nick` - Nick of bot.\
`captchaPass` -  Captcha pass.\
`captchaToken` - Automatically sends to server captcha token if provided.\
`teleport` -  Teleport on 'teleport' opcode.\
`controller` - Enable controller for this bot. (Use only once!).\
`reconnectTime` - Reconnect time (ms) after disconnect (default - 5000).\
`worldVerification` - buh (default - 25565)
`unsafe` - Use methods that are supposed to be only for admin or moderator or checking bucket.

# Module
When you require lib, you get object with:

`Client` - main OJS Client class (requires `options` object).\
`Bucket` - Bucket class for quota.\
`ChunkSystem` - Class for chunks, pixels management.\
`EventEmitter` - opcionally if you use browser version\
`WeirdDataView` - Normal dataView which automatically adds offset (has other options just look into script)

# API

## Client
Client API is similar to OWOP, and some methods have same 'path'.
### <\static>Client.RANK
Object with all ranks - `ADMIN`, `MODERATOR`, `USER` and `NONE`.
### <\static> Client.options
Object with OWOP options. Check code to see them.
### Client.clientOptions
Options that you passed in `options` argument.
### Client.chat
#### Client.chat.parseMessage
Parses message to [userInfo, messageContent, isTell, rawMessage]
#### Client.chat.send(msg)
Send message in chat.
#### Client.chat.sendModifier
Function for modifying and getting messages that you gonna send.
#### Client.chat.recvModifier
Function for modifying and getting messages that you're getting from server.
#### Client.chat.messages
All messages that you got. Keep in mind that it can only hold maximum of `Client.options.maxChatBuffer` messages in it (default - 256).

### Client.world
#### Client.world.join(name)
Function to join world. Should not be used, only for internal use! For connections to new worlds you should use new `Client` with `world` option in it.
#### Client.world.leave()
Leave world. If there's `reconnect` option enabled, client will try to reconnect after `options.reconnectTime` (default - 5000ms) seconds.
#### Client.world.move(x = 0, y = 0)
Move bot to X, Y.
#### Client.world.setPixel(x = player.x, y = player.y, color = player.color, sneaky)
Move and set pixel. If `sneaky` option is set to true, bot will return to old location.
#### Client.world.setTool(id = 0)
Set tool that bot has eqquiped.
#### Client.world.setColor(color = [0, 0, 0])
Set color of bot.
#### Client.world.protectChunk(x = player.x, y = player.y, newState = 1)
Protect chunk. You need to be admin to use this but you can ignore this if you'll use `unsafe` option. 
#### Client.world.clearChunk(x = player.x, y = player.y, rgb = player.color)
Clear chunk. You need to be admin to use this but you can ignore this if you'll use `unsafe` option. 
#### await Client.world.requestChunk(x = player.x, y = player.y, innacurate)
Request chunk, it'll be loaded to `ChunkSystem`. If `inaccurate` argument is passed, it'll transform `x` and `y` to `chunkX` and `chunkY`, so you can use normal coords to request chunks. Returns raw chunk.
```js
if(inaccurate) {
	x = Math.floor(x/Client.options.chunkSize);
	y = Math.floor(y/Client.options.chunkSize);
};
```
#### await Client.world.getPixel(x = player.x, y = player.y)
Request chunk and get pixel.

### Client.player
- Client.player.x
- Client.player.y
- Client.player.worldX - x\*16
- Client.player.worldY - y\*16
- Client.player.tool
- Client.player.rank
- Client.player.nick
- Client.player.id
- Client.player.color
- Client.player.pixelBucket - instance of `Bucket` for pixelQuota.
- Client.player.chatBucket - instance of `Bucket` for chatQuota.

### Client.players
List of players. Every player is object with properties:
- x
- y
- id
- color
- tool
- rank
- nick

Example: `Client.players[15035]`.

### Client.captcha
- renderCaptcha(uniquename = true) - if used on browser renders captcha otherwise throws error
- login(token) - tries to login using captcha token

### <\static> Client.util
- **Client.util.Player** - class of player
- **Client.util.createChunkFromRGB** - used for older protocol which is on bop it's owop
- **Client.util.decompress** - Chunk decompressor. Vars :(

### Client.chunkSystem
Instance of `ChunkSystem`

## ChunkSystem
This class is created just for **`Client.chunkSystem`**.

## Chunks
All chunks and pixels stuff goes here.
- **Chunks.chunks** - array with chunks. In this array chunks are saved like this: `Chunks.chunks[x][y]`.
- **Chunks.chunkProtected** - same thing as `chunks` but only for protected chunks.

Keep in mind, that you'll usually only need `Chunks.getPixel` from all this stuff here. 

### ChunkSystem.setChunk(x, y, data)
Set chunk data.

### ChunkSystem.getChunk(x, y)
Get chunk data.

### ChunkSystem.removeChunk(x, y)
Remove chunk.

### ChunkSystem.setPixel(x, y, rgb)
Set pixel in chunk.

### **ChunkSystem.getPixel(x, y)**
Get pixel from chunk.

### ChunkSystem.setChunkProtect(x, y, newState)
(un)Protect chunk.

### ChunkSystem.isProtected(x, y)
Is chunk protected.

# Author
License - Mit\
Originally created by [dimden](https://dimden.dev/).\
Edited by mathias377#3326