# Invite all your online friends with a custom message

_This runs completely local on your machine with a direct request to VRChat's API. There is no server from me or whosover in-between. It's basically the same the vrchat.com site and your game client does._

1. Friend yourself by using this script: [friend_yourself.md](./friend_yourself.md)
2. Go to https://api.vrchat.cloud
3. Open the **Developer Tools > Console** of your browser:
  * Chrome, Windows: `CTRL` + `SHIFT` + `J`
  * Firefox, Windows: `CTRL` + `SHIFT` + `K`
  * Chrome, macOS: `OPTION` + `COMMAND` + `J`
4. Paste the following code and replace `your_username`, `your_password` and `your_message`
5. Press enter
6. It will prompt you for each online friend if you want to invite that one

_You can join my Discord if you have any questions: [discord.gg/TTRgRpJ](https://discord.gg/TTRgRpJ)_

```javascript
const yourUsername = 'your_username';
const yourPassword = 'your_password';
const yourMessage = 'your_message';

const data = new URLSearchParams();
data.append('username_email', yourUsername);
data.append('password', yourPassword);

let yourUserId = null;
let yourLocation = null;

fetch('https://api.vrchat.cloud/login', {
  method: 'POST',
  body: data,
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  credentials: 'include'
})
  .then(response => {
    const rawCookies = document.cookie.split(';');
    const cookies = {};
    rawCookies.forEach(cookie => {
      cookie.split('=');
      cookies[cookie[0]] = cookie[1];
    });

    // Get your user id
    return fetch('https://api.vrchat.cloud/api/1/auth/user', {
      credentials: 'include'
    });
  })
  .then(response => response.json())
  .then(json => {
    yourUserId = json.id
    
    // Get your world and instance id
    return fetch(`https://api.vrchat.cloud/api/1/users/${yourUserId}`, {
      credentials: 'include'
    });
  })
  .then(response => response.json())
  .then(json => {
    yourLocation = json.location;
    
    // Get your online friends
    return fetch('https://api.vrchat.cloud/api/1/auth/user/friends?offline=false&n=100&offset=0', {
      credentials: 'include'
    });
  })
  .then(response => response.json())
  .then(json => {
    const friendsToInvite = json.filter(friend => {
      return window.confirm(`Invite ${friend.displayName}?`);
    });

    friendsToInvite.forEach(friend => {
      setTimeout(() => {
        fetch(`https://api.vrchat.cloud/api/1/user/${friend.id}/notification`, {
          method: 'POST',
          body: JSON.stringify({
            type: 'invite',
            message: yourMessage,
            details: { worldId: yourLocation }
          }),
          headers: { 'Content-Type': 'application/json' },
          credentials: 'include'
        })
          .then(response => response.json())
          .then(json => {
            console.log(`Invited ${friend.displayName}`);
          });
      }, 1000);
    });
  });
```
