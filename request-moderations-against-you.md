# Request all moderations which are targeted against you

_This runs completely local on your machine with a direct request to VRChat's API. There is no server from me or whosover in-between. It's basically the same the vrchat.com site and your game client does._

1. Go to https://api.vrchat.cloud
2. Open the **Developer Tools > Console** of your browser:
  * Chrome, Windows: `CTRL` + `SHIFT` + `J`
  * Firefox, Windows: `CTRL` + `SHIFT` + `K`
  * Chrome, macOS: `OPTION` + `COMMAND` + `J`
3. Paste the following code and replace `your_username` and `your_password`
4. Press enter

```javascript
const yourUsername = 'your_username';
const yourPassword = 'your_password';

const data = new URLSearchParams();
data.append('username_email', yourUsername);
data.append('password', yourPassword);

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
      
      fetch(`https://api.vrchat.cloud/api/1/auth/user/playermoderated`, {
        method: 'GET',
        credentials: 'include'
      })
        .then(response => response.json())
        .then(json => {
          const moderations = {};

          json.forEach(moderation => {
            if (!moderations[moderation.type]) moderations[moderation.type] = [];
            moderations[moderation.type].push(moderation.sourceDisplayName);
          });

          console.log(moderations);
          console.log(json.filter(moderation => moderation.type !== 'unmute'));
        });
    });
```
