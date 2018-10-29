# Request the VRChat ranks of all your friends

_This runs completely local on your machine with a direct request to VRChat's API. There is no server from me or whosover in-between. It's basically the same the vrchat.com site and your game client does._

1. Go to https://api.vrchat.cloud
2. Open the **Developer Tools > Console** of your browser:
  * Chrome, Windows: `CTRL` + `SHIFT` + `J`
  * Firefox, Windows: `CTRL` + `SHIFT` + `K`
  * Chrome, macOS: `OPTION` + `COMMAND` + `J`
3. Paste the following code and replace `others_username` (lower case only), `your_username` and `your_password`
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
      
      return fetch(`https://api.vrchat.cloud/api/1/auth/user`, {
        credentials: 'include'
      })
    })
    .then(response => response.json())
    .then(json => {
      console.log(`Total friends: ${json.friends.length}`)
  
      const rankOverview = {
        veteran: 0,
        trusted: 0,
        known: 0,
        user: 0,
        new: 0,
        visitor: 0
      }
  
      const veterans = []
  
      for (let i = 0; i < json.friends.length / 100 + 1; i++) {
        let url = `https://api.vrchat.cloud/api/1/auth/user/friends?offline=true&n=100&offset=${(i - 1) * 100}`
        if (i === 0) url = `https://api.vrchat.cloud/api/1/auth/user/friends?offline=false&n=100&offset=0`
        
        fetch(url, { credentials: 'include' })
          .then(response => response.json())
          .then(json => {
            for (friend of json) {
              if (friend.tags.indexOf('system_trust_legend') >= 0) {
                rankOverview.veteran++
                veterans.push(friend.displayName)
              } else if (friend.tags.indexOf('system_trust_veteran') >= 0) {
                rankOverview.trusted++
              } else if (friend.tags.indexOf('system_trust_trusted') >= 0) {
                rankOverview.known++
              } else if (friend.tags.indexOf('system_trust_known') >= 0) {
                rankOverview.user++
              } else if (friend.tags.indexOf('system_trust_intermediate') >= 0) {
                rankOverview.new++
              } else if (friend.tags.indexOf('system_trust_basic') >= 0) {
                rankOverview.visitor++
              } else {
                // console.log(friend)
              }
            }
          })
      }
  
      console.log(rankOverview)
      console.log(veterans)
    });
```
