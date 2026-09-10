# FriendsAPI

Bit of a large project to made with Skript, but it's my first Skript project so we ball anyways.

FriendsAPI is a reusable Minecraft friend-system API written in Skript.

It provides the backend functionality for managing friends and friend requests while keeping the player-facing messages separate in a frontend layer. This makes it easier to reuse the API from other Skripts and customize what players see.

---

## Features

- Send friend requests
- Accept friend requests
- Deny friend requests
- Remove friends
- Check whether two players are friends
- View pending friend requests
- View your friends list
- Show online friends before offline friends
- Retrieve friend names from stored UUIDs
- Check whether a friend is currently online
- Customizable frontend messages
- Configurable colors and formatting
- Public functions that can be used by other Skripts
- Command permission support

---

## Project Structure

```text
Friend System/
│
├── Commands/
│   └── Commands.sk
│
├── Config/
│   └── Config.sk
│
├── FriendsAPI/
│   └── API.sk
│
├── Frontend/
│   └── Messages.sk
│
├── LICENSE
└── README.md
```

### What each folder does

#### `Commands/`

Contains the player-facing `/friend` command and connects command input to the API and frontend.

#### `Config/`

Contains configurable options such as message formatting, colors, titles, and command help text.

#### `FriendsAPI/`

Contains the core friend-system API and its backend functions.

#### `Frontend/`

Contains the functions responsible for what players see. Keeping these messages separate makes the frontend easier to customize without changing the API logic.

---

# Commands

The included command is:

```text
/friend
```

### Available subcommands

```text
/friend add <player>
/friend accept <player>
/friend deny <player>
/friend remove <player>
/friend requests
/friend list
/friend help
```

---

# Permissions

The `/friend` command requires:

```text
friendsapi.use
```

Players without this permission cannot use the friend command.

The permission is defined directly on the `/friend` command in `Commands.sk`.

### Example

```skript
command /friend <text> [<player>]:
    permission: friendsapi.use
    permission message: &cYou don't have permission to use the friend system.
```

The API functions themselves do not require this permission. Other Skripts can still call the public FriendsAPI functions directly.

---

# Public API

FriendsAPI is designed so that other Skripts can use its functions instead of accessing the underlying friend data directly.

## `addFriend()`

Adds two players as friends.

```skript
addFriend(player, target)
```

---

## `removeFriend()`

Removes two players from each other's friends list.

```skript
removeFriend(player, target)
```

Returns whether the removal was successful.

---

## `areFriends()`

Checks whether two players are friends.

```skript
areFriends(player, target)
```

Returns `true` or `false`.

Example:

```skript
if areFriends(player, target) is true:
    send "&aYou are friends!" to player
```

---

## `sendFriendRequest()`

Sends a friend request from one player to another.

```skript
sendFriendRequest(player, target)
```

Returns whether the request was successfully sent.

---

## `acceptFriendRequest()`

Accepts a pending friend request.

```skript
acceptFriendRequest(player, sender)
```

Returns whether the request was successfully accepted.

---

## `denyFriendRequest()`

Denies a pending friend request.

```skript
denyFriendRequest(player, sender)
```

Returns whether the request was successfully denied.

---

## `getFriends()`

Returns the player's friends.

```skript
getFriends(player)
```

Example:

```skript
set {_friends::*} to getFriends(player)
```

---

## `getFriendRequests()`

Returns the player's pending friend requests.

```skript
getFriendRequests(player)
```

Example:

```skript
set {_requests::*} to getFriendRequests(player)
```

---

## `getFriendName()`

Gets the player name associated with a stored friend UUID.

```skript
getFriendName(uuid)
```

---

## `isFriendOnline()`

Checks whether a stored friend UUID belongs to a currently online player.

```skript
isFriendOnline(uuid)
```

Returns `true` or `false`.

---

# Using FriendsAPI From Other Skripts

One of the main purposes of FriendsAPI is allowing other Skripts to use the friend system without recreating the backend.

For example:

```skript
if areFriends(player, target) is true:
    send "&aYou are friends with %target%!" to player
```

Or:

```skript
set {_friends::*} to getFriends(player)

loop {_friends::*}:
    send "%getFriendName(loop-value)%" to player
```

Other Skripts should use the public API functions rather than directly modifying the internal friend variables.

### Loading Order

FriendsAPI must be loaded before another Skript attempts to use its functions.

If another Skript depends on FriendsAPI, make sure FriendsAPI is loaded first.

---

# Frontend Customization

The frontend is intentionally separated from the backend.

The file:

```text
Frontend/Messages.sk
```

contains functions such as:

```skript
showFriendRequestSent()
showFriendRequestReceived()
showFriendRequestAccept()
showFriendRequestDeny()
showFriendRemove()
showFriends()
showFriendRequests()
showFriendHelp()
```

This allows the messages shown to players to be changed without modifying the underlying friend-system logic.

For example, a server could replace simple text messages with more advanced formatting or clickable components while keeping the API functions unchanged.

---

# Configuration

Configuration is handled through:

```text
Config/Config.sk
```

The configuration contains options for things such as:

- Help menu text
- Colors
- Friend list formatting
- Friend request formatting
- Titles
- Usage messages
- Invalid command messages
- Other frontend formatting

This allows server owners to customize the appearance of the friend system without changing the API logic.

---

# Database / Data Storage

## No External Database

FriendsAPI **does not use SQL or any external database**.

Friend data is stored using **Skript variables**.

This keeps the project simple and easy to set up without requiring MySQL, MariaDB, SQLite, or another database system.

### Important Limitation

FriendsAPI is intended primarily for:

- Small servers
- Medium-sized servers
- Private servers
- Community servers with relatively small player/friend datasets

**FriendsAPI is not recommended for large Minecraft servers or networks with very large player counts and friend datasets.**

A future database-backed implementation could improve scalability for larger environments.

---

# Design

FriendsAPI follows a simple separation-of-concerns structure:

```text
Config
   ↓
Commands ─────→ Frontend
   ↓
FriendsAPI
```

### Backend

`FriendsAPI/API.sk`

Handles the actual friend-system logic and data.

### Frontend

`Frontend/Messages.sk`

Handles what the player sees.

### Commands

`Commands/Commands.sk`

Handles player commands and connects the command system to the API and frontend.

### Configuration

`Config/Config.sk`

Provides configurable values used by the command and frontend layers.

---

# Why Use an API?

Without an API, every Skript that needs a friend system would have to recreate the same logic.

FriendsAPI provides reusable functions instead:

```skript
areFriends(player, target)
sendFriendRequest(player, target)
getFriends(player)
getFriendRequests(player)
```

This keeps other Skripts smaller and allows the underlying friend system to be maintained in one place.

---

# Future Features

Possible future improvements include:

- GUI-based friend menu
- Clickable friend requests
- More advanced notifications
- Friend limits
- Permission-based friend limits
- SQL/database support
- Pagination for large friend lists
- Online/offline status improvements
- Friend-related events
- Additional API functions
- More frontend customization options

---

# License

FriendsAPI is licensed under the [MIT License](LICENSE).

Copyright (c) 2026 Cirvam.
