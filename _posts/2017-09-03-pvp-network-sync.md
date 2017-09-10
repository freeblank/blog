---
layout: "post"
title: "PVP Network Sync"
date: "2017-09-03 11:21"
---
# Mobile Network Overview
As the evolvement of mobile network, more and more Real Time PVP Game are widely acclaimed by players. If you start a PVP, a weak network make the fighting becoming to strike keyboard and mouth and then make you lose all the game, or when you click move button, but you will stay there until one seconds later, or A and B have the different show, how do you think about the game? You must have heard or played the games, DOTA2, Overwatch, Clash Royale, Strike Of Kings, you can enjoy fighting with other players. How do they deal with the network sync? How can we make our games fast response, sync, stable when start a PVP?<!--more-->

# Sync Models
there are several typical sync models for network sync, each of them have obvious strengths and weaknesses, with the players from all of the world, the stable and speed with network is different, and with special requirement of game, we need to choose a sync model or combos for the game

### Server Driver Model

It's popular with PC Network Games, especially in MMORPG, all the logic run on server and dispatch the states to clients, the clients just send operational commands to server and render result from server to show, server initial all the state and send to clients, and just send the change state later, the faster command server receive will run first.

> Obviously the advantage is safe because all the logic run on server and just legal commands can be accepted by server, cheater can only affect the render but it's impossible to change the result on client. There is another important advantage that the logic update will very simple, you need just update logic code on server, all the clients don't need change anything, you will feel more deeply on mobile game.

> The defect is also obviously, the server have high load with all the logic running, you need more servers with load balancing or support less users play on a server, with peak load, the server is very busy, even crash down which will make all clients disconnect, and waste when most players don't launch a PVP. Another one is high network data will cost with send the states each frame.

If the game you develop need high secure, quick update, less players with PVP, you can adopt the model.

### Host Model

One client create as a host, other clients send operational commands to server and the server resend to the host, even directly send to host in a LAN, all the logic run on the host, then response result to the server which dispatch the result to other clients.

> The player as the host have well network will enjoy the PVP, the operation execute and render as immediate as possible, other clients with worse network just make their action sluggish. logic code will be more simple on client, the server will have low load with just resend command and result. they are the advantages also cost less network data.

> The weakness is cheater as the host, make himself as a gold, other players will kneel down as disciples, it's hard to avoid that. The host need be more effective and well network, all the clients will be affected by the host.

If the player just need care about himself, even each player have different result, like do some task with friends help, this model will be perfect! make each player as a host himself, each operational command send to all host.

### Frame Sync Model

With the model, all the clients have the same frame server generate, when the war begin, the clients will be waiting commands from server, each frame server will send commands to all clients, including empty commands, clients need send operational commands to server, and server dispatch to all at the current frame. the earlier commands server received will be dispatch first, so that the server will not the clients, this means when a client has some problem with network, other clients won't be affected.

> All the clients will have the same frame and commands make the result be the same certainly, make the more fairness which is very important in Real Time PVP Game. Dispatch mere commands cost less network data which will be more response and requirement with mobile game.

> It also have the weakness, well network is essential to make quick response and enjoyable, cheater is also an enemy, each key command can't be lost which will make the client with different result never allowed in Real Time PVP Game.

In Real Time PVP Game, we always adopt Frame Sync Model, now we'll have a deeply discuss with it.

# Communications Protocol
we can't lose key command, but we also need the network data transmit as quickly as possible. As we know, TCP is stable which make the data received and sequential, unfortunately to make sure for that cost the procedure complicated and slow, extraordinarily when the network is not stable which is an ordinary state in mobile network. In contrast, UDP is more light and quick, it's also very smart to change server, but the network data is not stable.

you can earn money, but you should kneel down, how can we stand up to earn money? we can't reduce procedure with TCP, but we can improve with UDP

> Data with frame sequence identify can make sure the data sequential.

> How to make sure the data you have received? first we discuss the server send frame commands to the client, the client will tell the server which latest frame have received each server seconds or pack with operational commands, the server will send all the commands from the latest frame to the current frame, it will send some repetitive frame commands but it can make sure the client receive all the commands. how about the client send operational commands to server? resend the command when receive the server commands which isn't contain the command, and mark the command resend, otherwise, don't resend with a long time, for example, when a client start a command to attack you, it is quite natural that you will send a command to escape, but with the worse network you receive the server commands several seconds later, the other clients have hit you, a escape command is useless.

> An alternative idea, you can have a try with QUIC which is created and updated by google and is also stable with quickly, if you want to know more, glance over [ QUIC](https://github.com/google/proto-quic)

# Consistency

To make all clients with consistency, all the clients should receive the same commands, how to make the clients which have different hardware even operating system have the same result with the same commands? Logic Frame is a key.

### Logic Frame
```cpp
float accumulatedTime = 0.0f;
float frameLength = 0.05f;
float lastTime = -1.0f;
//called once per unity frame
void Update() {
    float curTime = now();
    if (lastTime < 0) lastTime = curTime;
    accumulatedTime = accumulatedTime + curTime - lastTime;

    while(accumulatedTime > frameLength) {
        GameFrameUpdate(frameLength);
        accumulatedTime = accumulatedTime - frameLength;
    }
}

int gameFrame = 0;
void GameFrameUpdate(delta) {
    Command *i_cmd = CommandManager::getCommand(gameFrame);
    if (i_cmd == NULL) return;

    i_cmd->run(delta);
    gameFrame++;
}
```

Another key is logic update, we need to focus on several points.

### Random

before the game frame update, all the clients need set the same random seed, and we need also create the custom random for logic update, because we can't make sure all the clients have the same call with random defined by system, one accidental call will make the result have a completed different result.

### Float

Float is beautiful but in this case is evil, when you run in different platform it's hard to make sure get same result all the time. using [fp:strict](https://msdn.microsoft.com/en-us/library/aa289157(VS.71).aspx#floapoint_topic4) can reduce the probability, or replace with fixed-point, some functions, such as sin, cos, and so on, are also not stable, you need avoid using them or override them.

### Compatibility

Make sure the libs and engine called by game frame logic consistent with all the devices your game need to run on.

# Log And Replay

With key logic frame commands, we can replay the whole fighting very easy, so we can store each replay which will be very small on the server, the replay will consist merely of key logic frame commands and all states of the result to verify the replay is valid.

If each step and logic getting accident difference the result will be in a tremendous difference, it's hard to find out the reason, so we need a log to analyze and solve the problem.

> Each logic frame, we record the key states, like position and special states with all units which be affected by game logic, so that run the replay again and again, you can find which step cause different result, and then fix them one by one.

# Reconnect

when your client was disconnected unexpectedly, such as a phone call, you should go back as soon as possible to reconnect and continue, or you will lost the fighting, at the moment your client will receive several commands.

> With the commands, you need to tell the cpu just run them as quickly as possible, don't wast any time, i don't want to lose the fighting, otherwise server will send more commands, till all the commands have been deal with, the cpu can restore, enjoy the fighting.

but if you reconnect for a long time, you will receive a mass of commands, even the app have been crash, you lost all the states, you need tell the server to resend all commands, and you should deal with the mass commands, if the logic update with complicated orders, it will cost a long time to restore, at that time your client also receive a lot of commands, restore them with continuous, at last you can play ordinary, but you may lost the fighting because wasting lots of time to restore the fighting

> One solution is tell the server i don't want to restore step by step, please tell another client give me all the states, with a few new commands i can restore the fighting very soon, this will depends on the game logic can restore with all states, such as a frame of an animation, or ruggedly waiting the fighting finished, you may win the game without any operation if you are so powerful.

# Secure

As we know, the server just dispatch operational commands, the clients deal with the commands frame by frame and send result to the server, so cheater can change the commands to win the fighting, how can we against the cheaters?

All the clients will send result and all states to the server, compare the result and all states, if they are all the same, the server will believe the result certainly; if there are some differences and the number of clients is more than 2, we can also find the cheaters except all the results are different; if it's a fighting between two players and with different results, we need to verify the results.

> build a verify server to just run the frame logic with commands, the game server send all commands to the verify server return a reliable result to find out the cheaters, you need to separate all frame logic to build the verify server.

> If you think it's hard to separate the logics, you can send the commands to a third disengaged client return the result which also reliable.
