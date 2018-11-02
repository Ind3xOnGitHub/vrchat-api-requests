# Request a user's VRChat rank

_This runs completely local on your machine with a direct request to VRChat's API. There is no server from me or whosover in-between. It's basically the same the vrchat.com site and your game client does._

1. Go to https://api.vrchat.cloud
2. Open the **Developer Tools > Console** of your browser:
  * Chrome, Windows: `CTRL` + `SHIFT` + `J`
  * Firefox, Windows: `CTRL` + `SHIFT` + `K`
  * Chrome, macOS: `OPTION` + `COMMAND` + `J`
3. Paste the following code and replace `others_username` (lower case only), `your_username` and `your_password`
4. Press enter
5. Under `tags` you will find some of these: `trust_system_`. The highest one subtracted by one will be your ingame rank. Example: If you have the tag `trust_system_veteran` your ingame rank will be _Trusted User_ (not Veteran User)

_You can join my Discord if you have any questions: [discord.gg/TTRgRpJ](https://discord.gg/TTRgRpJ)_

```javascript
const yourUsername = 'your_username';
const yourPassword = 'your_password';
const othersUsername = 'others_username'

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
      
      fetch(`https://api.vrchat.cloud/api/1/users/${othersUsername}/name`, {
        method: 'GET',
        credentials: 'include'
      })
        .then(response => response.json())
        .then(json => console.log(json));
    });
```
