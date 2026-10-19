
## xboxlive messaging
See [xblmessaging](./xblmessaging.md).

## Party chat
Party chat is sent over peer to peer connections using WebRTC.

## Websocket
The chat websocket provides notifications about new messages, invites, and achievements.
```
curl "https://chat.xboxlive.com/users/xuid($xuid)/chat/auth" \
-H "authorization: XBL3.0 x=${uhs};${xsts}"
```
```
wscat -p 13 -s chat --connect "wss://chat.xboxlive.com/users/xuid($xuid)/chat/connect?AuthKey=${authKey}"
```
The first message sent by the client is always:
```
{"clientSeqNum":0,"channel":{"type":"System"},"messageType":"WhoAmI"}
```
The the response provides the chat nonce and chat connection id to provide
to `notificationinbox.xboxlive.com`.

The following is sent after recieving a response from WhoAmI:
```{"clientSeqNum":1,"channel":{"type":"XboxMessage","id":"<xuid>"},"messageType":"JoinChannel"}```

The following is sent before opening the party menu:
```{"clientSeqNum":2,"channel":{"type":"Party","id":"<xuid>"},"messageType":"JoinChannel"}```


If a message from the server was sent in response to a message from the client,
`flagServerOriginated` is missing and `clientSeqNum` matches that from the client.
Otherwise flagServerOriginated is `true` and `clientSeqNum` is zero.

## Notification inbox
To subscribe to notifications a request is sent to `notificationinbox.xboxlive.com`:
```
curl 'https://notificationinbox.xboxlive.com/users/2535412854559864/inbox/push?subscriptionCategory=Microsoft.Xbox.Multiplayer,Microsoft.Xbox.Achievements,Microsoft.Xbox.People&subscriptionType=GameInvites,GamePartyInvites,MultiplayerActivityGameInvites,PartyInvites,AchievementUnlock,Followers,IncomingFriendRequests,AcceptedFriendRequests' \
  -H 'Accept: */*' \
  -H 'Accept-Language: en-US,en;q=0.9' \
  -H 'Content-Type: application/json' \
  -H 'x-xbl-contract-version: 1' \
  -H "authorization: $auth" \
  -d "{\"ChatNonce\":\"$chatNonce\",\"ChatConnectionId\":\"$chatConnection\"}"
```

### Needs testing
What is the difference between
- Microsoft.Xbox.People&subscriptionType=GameInvites,
- MultiplayerActivityGameInvites,
- GamePartyInvites,
- PartyInvites

What is the difference between `Microsoft.Xbox.Achievements` and
`AchievementUnlock`? Is one our achievements and the other our friend's?

What is `Microsoft.Xbox.Multiplayer`?
