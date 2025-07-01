# hissing

## Scoping

#### Questions to cover with client in scoping

	
 • ﻿﻿Would you like us to create a pretext or do you have anything specific in mind?
	
 • ﻿﻿Are we sending a single phish to all targets or targeting specific individuals with specific pretexts 
    o I.e. send aws admins aws password reset, azure admins azure resets

#### Releasing emails
In most scenarios, phishing emails will have to be released from quarantine to reach user inboxes. This is typical for new domains with no domain authority. It is good to mention this to the client so they have resources prepared to troubleshoot this proces, as it can be lengthy. Make sure to mention this in scoping.

#### Picking a domain
I like to use sso-client.tld, others like to change the tid to net or something similar. It's also possible to use tricks with numbers and letters, for instance coalfire.com, or if something has a d in it using cl, so clefcon.org. Different fonts render better, and if the url is big enough it's pretty easy to miss, but if it's seen I think it's less convincing than using something like sso-client.

Check if the domain is available according to whois and has no blacklisting on mxtoolbox.com.

Once you get the phishing server setup, don't send a test email to your CF email. Our controls may end up blacklisting the domain. Use mail-tester.com for a test email account. The free account allows 3 emails/day but there is an account in lastpass that will let you do unlimited emails.



##### Subdomains
During info gathering you can also find the various services they use, so that you can request specific subdomains for your landing pages.

• microsoft.sso-client.tld

• slagk.sso-client.tid

##### Email Format for Domain Setup
Once you've gathered this information, send an email to support with the title ClientName GoPhish Instance Request

```
Hello,

Can I please get a gophish instance going with DNS name sso-<client>.<tld> and the following s
	• ﻿﻿aws
	• ﻿﻿azure
	• ﻿﻿microsoft
Thanks!
Best,
‹name>
```
## GoPhish Setup

#### Logging in to GoPhish for the first time

An email will be sent to you with instructions to login to the GoPhish instance for the first time. Connect to the labs vpn and ssh to the device, then follow the instructions to find temporary credentials and a link to the web interface.

#### Users and groups


Users and Groups are used as target lists. It's fairly simple if the phish simply requires you to send the same phish to all targets, but this allows you to delineate which emails go where. For example, you may want to send an aws-related phish to aws admins.
For mass importing, you'll need to convert the provided target list to the following cs format if you want to mass import them.
```
First Name, Last Name, Email, Position
```
##### Sending Profile

Create a sending profile for each pretext used. Use the below image as a guide to setup, modifying the From: field to be in line with your pretext. Username and Password fields should not be filled out.

Make sure to note the X-Mailer header below that should be changed. By default the X-Mailer header is setup to be "GoPhish."

name: main
interface type: smtp
From: Microsoft <microsoft-noreply@sso-acumerdkc.com>
Host: localhost:25
check ignore certificate errors

##### Email Templates
I've provided a library of templates I've used in the past. Some may be more specific to a particular client, so be sure to review all templates before using. Just paste the contents of the template into a new template in GoPhish

```
<form action=""
method="post" name="form">< label>Username:</label> <input name="username" type L
< label>Password:</label> <input name="password" type="password"/><br/> <input id="login" name="login" type="submit" value="Login"/> </form>
```

##### Campaigns
Campaigns are where you combine everything together to perform final tests then send out to client. Use the below image as a guide to filling out your Campaign, and be sure to add a subdomain to the URL field if you're using one for the landing page.
Campaigns

##### Other Things of Note
Chrome Flagging Page as Malicious
It is possible upon visiting the page in chrome during development that chrome will flag the page and domain as malicious. I have found that if you use browsers like Safari this is less likely to happen. My suggestion is simply to not use chrome while testing your phish.

##### DKIM Service Not Functioning
Often times you will see in mail-tester that DKIM does not appear to be functioning. You can usually fix this with the following command:

```
sudo systemctl restart opendkim.service
```

#### Tools of the Trade


#### Singlefile

Chrome extension to extract a page's html, js, assets, etc. into a single file for a landing page.
https://chromewebstore.google.com/detail/singlefile/mpiodijhokgodhhofbcjdecpffjipkle?pli=1

#### Quoted Printable Converter
Saving emails from outlook cause them to be encoded in Quoted Printable. Use the following to convert to human readable:
https://www.webatic.com/guoted-printable-convertor
Sample of quoted printable encoding:
```
<html><head>
‹meta http-equiv=3D"Content-Type" content=3D"text/html; charset=3Dutf-8"></=
head><body><div class=3D"ydp6e010f87yahoo-style-wrap" style=3D"font-family:=
Helvetica Neue, Helvetica, Arial, sans-serif;font-size: 13px;"><div›</div> ‹div><br></div><div><br></div>
=20
</div><div id=3D"ydpdc9ccb21yahoo_quoted_6591437351" class=3D"ydpdc=
9ccb21yahoo_quoted">
‹div style=3D"font-family: 'Helvetica Neue', Helvetica, Arial, s= ans-serif;font-size: 13px; color:#26282a; "> ‹div>---- Forwarded Message -</div> ‹div><b>From:</b> Microsoft account team &lt;account-securi= ty-noreply@accountprotection.microsoft.comsgt;</div>div><b>To:</b> aquot;
&quot; &lt; &gt;</div<div><b>Sent: </=
b> Tuesday, January 23, 2024 at 10:59:24 AM CST</div›<div><b>Subject:</b> M= icrosoft account password reset</div><div><br›</div>
‹div><div id=3D"ydpdc9ccb21yiv5954967424">=20
=20
<div>
```

#### Mail Tester
Allows you to see rendered email and also provides a score based on how likely your email is to get past spam. it grades based on several factors such as DKIM, whether it's a new domain, proper authentication, and other factors.

SpamAssassin thinks you can improve
The famous spam filter SpamAssassin. Score: -Q.8.
A score below -5 is considered spam.



-0,8


-0.1
DKIM_SIGNED

Message has a DKIM or DK signature, not necessarily valid
This rule is automatically applied it your email contains a DKIM signature but other positive rules will also be added if your DKIM signature is valid. See immediately below.


0.1
DKIM_VALID
Message has at least one valid DKIM or DK signature |
Great! Your signature is valid


0.1
DKIM_VALID_AU
Message has a valid DKIM or DK signature from author's domain
Great! Your signature is valid and it's coming from your domain name


0.1
DKIM_VALID_EF
Message has a valid DKIM or DK signature from envelope-from domain


-0.999
FROM_FMBLA_NEWDOM14
From domain was registered in last 7-14 days


-0.001|
HIML_MESSAGE
HTML included in message
No worry, that's expected if you send HTML emails


0.001
SPF_HELO_PASS
SPF: HELO matches SPF record


0.001
SPF_PASS
SPF: sender matches SPF record


Great! Your SPF is valid


#### Phishing Campaign Approval

```
I wanted to reach out to you to get us whitelisted and communicate the information needed to start the phish <when>.

We have the phishing server set up and the campaign created.

The whitelisting needs to take place anywhere necessary that might interfere with user interacting with the phishing email(ex. Email server, etc).

The IP address that the phish will originate from is: <IP>.
The domain that the email will originate from is: <DOMAIN>. 
To ensure the phishing email is appropriately whitelisted there will need to be a rule set for your internal controls that looks for the custom header and value pair referenced below.

<put screenshot of header here>

<screenshot of phish email>

A test email can be sent if requested. Please provide a test target if you would like a test.
```



