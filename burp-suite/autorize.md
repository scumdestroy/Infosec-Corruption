---
description: Finding authorization issues with autorize
---

# Autorize

### Basic workflow

1. Login to the target application as a low or non-privileged user
2. Visit the  Autorize tab in burp, keep it off during configuration 
3. Click Configuration 
4. Click Fetch cookies from last request 
5. Open an incognito window in your browser
6. Login as a higher privileged user 
7. Click "Autorize is off" button to turn it on 
8. Visit various areas of the site, but focus on sensitive areas that require privileged accounts to access or actions a lower privileged user should not be able to accomplish
9. Frown at the results until you find something interesting.



