# Friend yourself

_This runs completely local on your machine with a direct request to VRChat's API. There is no server from me or whosover in-between. It's basically the same the vrchat.com site and your game client does._

1. Go to https://api.vrchat.cloud
2. Open the **Developer Tools > Console** of your browser:
  * Chrome, Windows: `CTRL` + `SHIFT` + `J`
  * Firefox, Windows: `CTRL` + `SHIFT` + `K`
  * Chrome, macOS: `OPTION` + `COMMAND` + `J`
3. Paste the following code and replace `your_username` and `your_password`
4. Press enter
5. Accept the friend request ingame

```javascript
const yourUsername = 'your_username';
const yourPassword = 'your_password';

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
    // Friend yourself
    fetch(`https://api.vrchat.cloud/api/1/user/${json.id}/friendrequest`);
  });
```
