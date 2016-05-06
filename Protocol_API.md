# Server & Authentification #

This module contains commands related to authentication.

## login ##

Authenticate user using credentials found in the MySQL database.

### in ###
  * username: string
  * password: string
### out ###
  * ret: boolean

## user.create ##

Create a user using the given credential. The user will be considered authenticated.

### in ###
  * username: string
  * password: string
  * firstname: string
  * lastname: string
  * email: string
### out (success) ###
  * id: long
  * ret: boolean
### out (error) ###
  * ret: boolean
  * error: string

## logoff ##

Disconnect self from the server.

### in ###
### out ###
  * ret: boolean

## server.kicked ##

Sent by the server when user is kicked. Never sent from client.

### out ###
  * reason: string


---


# Instant Message #

This module is used for communication between users.

## im.msg ##

Send a message to a target user.

### in ###
  * username: string
  * msg: string
### out ###
  * ret: boolean

## im.recv ##

Sent by server when a message is sent by another user. Never sent from client.

### out ###
  * msg: string


---


# Achievements #

This module includes messages related to achievements.

## ach.get ##

Returns all the achievements earned by a player (or in progress).

### in ###
  * none
### out ###
  * ach : RougeArray of RougeObject
    * key: string
    * point: int
    * progress: double
    * total: double
  * ret: boolean

## ach.update ##

Update the progress on an achievement.

### in ###
  * key: string
  * progress: double
### out ###
  * key: string
  * progress: double


---


# Leaderboards #

Includes commands related to leaderboards.

## leaderboard.get ##

Retrieve a specific a leaderboard.

### in ###
  * key: string
### out ###
  * key: string
  * scores: RougeArray of RougeObject
    * user: string
    * score: long
  * ret: boolean

## leaderboard.gets ##

Retrieve all leaderboards stored on the server.

### in ###
  * None
### out ###
  * leaderboard: RougeArray of RougeObject
    * key: string
    * scores: RougeArray of RougeObject
      * user: string
      * score: long
  * ret: boolean

## leaderboard.submit ##

Submit a score to a specific leaderboard.

### in ###
  * key: string
  * score: long
### out ###
  * key: string
  * ret: boolean


---


# Mail #

This module has commands related to mails.

## mail.getall ##

Get all the mails in the user's mailbox.

### in ###
  * None
### out ###
  * mails: RougeArray of RougeObject
    * id: long
    * to: long
    * from: long
    * content: RougeObject
  * ret: boolean

## mail.getunread ##

Get the unread emails in the user's mailbox.

### in ###
**None
### out ###
  * mails: RougeArray of RougeObject
    * id: long
    * to: long
    * from: long
    * content: RougeObject
  * ret: boolean**

## mail.send ##

Send a mail to target user.

### in ###
  * long: to
  * content: RougeObject
### out ###
  * ret: boolean

## mail.setread ##

Tag mail as read.

### in ###
  * id: long
### out ###
  * ret: boolean

## mail.delete ##

Tag mail as deleted.

### in ###
  * id: long
### out ###
  * ret: boolean


---


# Rooms #

This module contains command related to rooms.

## room.create ##

Create a room using the given name. The room is automatically joined as an admin.

### in ###
  * name: key
### out ###
  * ret: boolean

## room.delete ##

Delete the room of the given name. This can only be done by an admin.

### in ###
  * name: key
### out ###
  * ret: boolean

## room.join ##

Join a room of the given name.

### in ###
  * name: key
### out ###
  * ret: boolean

## room.leave ##

Leave the room of the given name.

### in ###
  * name: key
### out ###
  * ret: boolean

## room.say ##

Send a message to all users in a given room.

### in ###
  * name: key
  * msg: RougeObject
### out ###
  * ret: boolean

## room.recv ##

Received by all the users in a given room when something is said. Never sent from client

### out ###
  * name: key
  * msg: RougeObject
  * ret: boolean

## room.kicked ##

Received when kicked from the room. Never sent from client.

### out ###
  * name: string

## room.destroy ##

Received when the room is destroyed. Never sent from client.

### out ###
  * name: string

## room.whois ##

Returns a like of users in a given room.

### in ###
  * None
### out ###
  * people: RougeArray of String (usernames)
  * ret: boolean


---


# Variables #

This module has commands related to variables, used to store data. Normal variables are temporary, and disappear when the server is restarted. Persistent variable are stored to the database.

## var.set ##

Set the value of a variable.

### in ###
  * key: string
  * value: RougeObject
### out ###
  * key: string
  * ret: boolean

## var.get ##

Get the value of a variable.

### in ###
  * key: string
### out ###
  * key: string
  * value: RougeObject
  * ret: boolean

## var.sub ##

Subscribe to the variable of the given name.

### in ###
  * key: string
### out ###
  * key: string
  * ret: boolean

## var.unsub ##

Unsubscribe from the variable of the given name.

### in ###
  * key: string
### out ###
  * key: string
  * ret: boolean

## var.pset ##

Set the value of a persistent variable. Use version 0 to create a new variable. Otherwise you muse use the correct current version of the variable for the update to work.

### in ###
  * key: string
  * value: RougeObject
  * version: long
### out ###
  * key: string
  * ret: boolean

## var.pget ##

Get the value a persistent variable and its version. The version is needed to set a new value.

### in ###
  * key: string
### out ###
  * key: string
  * value: RougeObject
  * ret: boolean
  * version: long