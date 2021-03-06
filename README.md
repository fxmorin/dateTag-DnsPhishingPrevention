Current Version: 1.3.0 - Check changelog [here](../master/CHANGELOG.md)

# dateTag DNS Phishing Prevention
It's very important that you read the full README for this project since it requires date/time sync and POST requests

dateTag is a powerful tag that can easily identify a DNS phishing site. This version of dateTag has a 94% DNS Phishing Identification Rate!

dateTag is minimal, to use it properly you should only call the dateTag.php on sites you know usually have dateTag. This means that you still need to code when dateTag.php should get called. What you should do is, make a list of your websites, add dateTag to all of them. Then call dateTag.php whenever you visit one of your sites in your list.

I just want to make it clear that this only protects you against DNS (Domain Name System) Phishing Sites, where they spoof there Domain Name so it looks like yours. These sites are 98% perfect copies of the original one, making it pretty much impossible for you to spot that its a phishing site. Although the reason I built this is because 
> According to the Federal Trade Commission, over 96% of companies operating are vulnerable to domain spoofing attacks in one form or another. According to other research, 91% of phishing attacks are display name spoofs. The bottom line is that domain name spoofing is probably threatening your company.
> - https://www.phishprotection.com/content/domain-name-spoofing/

If you need help setting it up. Contact me!

## Contents

- [Quick Start](#quick-start)
- [Setup](#setup)
- [Configuration](#configuration)
- [Communication](#communication)
- [Examples](#examples)
- [TODO](#todo)

## Quick Start

1. All you have to do it clone this repository and add it to your project:
   ```bash
   $ git clone https://github.com/fxmorin/dateTag-DnsPhishingPrevention.git
   ```
   You can also just download the [dateTag.php](../master/src/dateTag.php) file & add it to your project
   
2. Make a POST request to the dateTag.php file whenever you want to check an url.
   Check [Communication](#communication) for more help! or Check [Examples](#examples) for examples on how to make the post requests!
   
The system works in three parts:
1. The dateTag on your website.
2. The dateTag.php file
3. The POST sender

It's that easy!

## Setup
dateTag needs to be added to your website for it to work. The whole purpose of dateTag is so that you can recognise your own site depending on how it acts. This means dateTag must be run on the server-side of your website, so that phisherman can't see how to add it to there phishing sites.

Here is the format which you must use, depending on the configuration you have setup:

`<dateTag value=" md5( MM/DD/YYYY/HH + token + uniqueId ) ">`

or if you are using customTag:

`<yourCustomTagName value=" md5( MM/DD/YYYY/HH + token + uniqueId ) ">`



MM = 1-12  **|**  DD = 1-31  **|**  YYYY = 1000-9999  **|**  HH = 0-23

There are many other ways to write the tag, such as removing the md5 if hashing is disabled!
You can get a list of premade dateTag's to add to your site [here](../master/src/examples/dateTag-format.md)
There are multiple examples for many popular back-end web programming languages!
Not all features are pre-made in all languages. If you want to add some, feel free to contact me!
   
## Configuration

dateTag has multiple configurations which can be changed in dateTag.php (more coming soon!)

#### `date_default_timezone_set("America/Toronto");`

Make sure that the default timezone in dateTag is the same as the POST sender. Since if its not theres a chance you both have a diffrent timezone and that the time will never sync up, causing the dateTag.php to always respond 0


#### `$hashing = true;`

This option if set to `true` will tell the program that the dateTag value is hashed using md5: (more hash types coming soon)
**It's highly  recommended to have hashing enabled** , since phishing sites could try to mimic the dateTag's value if they find a pattern.


#### `$tokenType = 0;`

This option tell the program which type of method you will be using to specify the token to add to the dateTag value. The possible methods are:

**`0`:** String
**`1`:** Remote File
**`2`:** Api

Depending on the method you have decided to use, you will have to specify information: (only needed if hashing is enabled)

0. #### String
   `$token = "";`
   Specify a String which will be used as the token

1. #### Remote File
   `$token = "";`
   Specify a URL to a file containing the token you wish to use

2. #### Api
   `$token = "";`
   Specify a URL which should be called to receive the token from
   
   
#### `$uniqueID = true;`

This option if set to `true` will tell the program that the dateTag will be using a uniqueID. Unique ID stops the ability to use a server-side proxy to display the site, preventing proxy based dns phishing where it loads the real site with small modification to phish you. There are 4 types of Unique ID's you can use.


#### `$uniqueType = 0;`

This option tell the program which type of information you will be using as your unique ID to add to the dateTag value. The possible information that can be added are:

**`0`:** UserAgent
**`1`:** Ip-Address
**`2`:** UserAgent & Ip-Address
**`3`:** PHP get_Browser()

Depending on the information you decided to use, you will have to add the information to your dateTag. Currently only PHP based dateTag's (on websites) can support option 3. (unless your willing to make it) Depending on the language you are using, some of the options may not work for you due to there different formating. Although PHP examples are available [here](../master/src/examples/dateTag-format.md)


#### `$testingMode = false;`

This option if set to `true` will run the program in testing mode. Testing mode will make it easier for you to fix situations such as incorrect formating or incorrect posts. Testing mode is basically verbose, make sure to disable testing mode since it may make it possible for phisherman to find a way around dateTag! To find out how to properly understand testing mode. Make sure to read through the entire documentation!

## Communication

In upcoming releases, a library version will be added. Allowing calls straight from PHP.
This program currently requires you to make a POST request to the dateTag.php file. Here is how dateTag reads the POST request, and how it responds:

### Parameters
dateTag currently only checks for one parameter: (more coming soon)

`dateTag` should be the dateTag value found on a website which you want to check

### Response
dateTag currently only responds with either 0, or 1:

0) means that the url is a phishing site, since the dateTag is incorrect. This could mean it was badly setup!

1) means that your url is not a phishing site, and that your dateTag seems to be working well.

**Testing Mode:** 
When using testing mode, dateTag will respond with multiple strings. These strings will help you understand what you are doing wrong if you are having any issues.

## Examples
#### Making a post request

**Javascript:** (This does not need to be server-side. It could be part of a chrome extension)
```js
function checkUrl(dateTag) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      if (this.responseText == 1) { //not a phishing site
        return true;
      }
      return false;
    }
  };
  xhttp.open("POST", "/path/to/file/dateTag.php", true);
  xhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
  xhttp.send("dateTag="+dateTag);
}
```
**PHP:**
```php
function checkUrl($dateTag) {
    $result = HTTPRequester::HTTPPost("/path/to/file/dateTag.php", array("dateTag" => $dateTag));
    if ($result === 1) { //not a phishing site
      	return true;
    }
    return false;
}
```
**Python:**
```python
import requests
def checkUrl(dateTag):
    r = requests.post("/path/to/file/dateTag.php", data = {"dateTag" : dateTag})
    if r.text == 1:
        return true
    return false
}
```
Feel free to give me post requests for other languages. I will be making a seperate file to display all of them while keeping a copy of these ones on the README.

## TODO

- [x] Creating the README (v 1.1)
- [x] Add iFrame prevention (v 1.1)
- [x] Add Proxy prevention (v 1.2)
- [x] Add descriptive testingMode responses (v 1.2.1)
- [x] Add ability to change tag used for dateTag (v 1.2.1)
- [ ] Add other ways of using dateTag other then POST
- [ ] Add error proofing
- [ ] Add ability to change time which dateTag lasts
- [ ] Adding more hash methods
- [ ] Add more parameters to POST request
- [ ] Add POST request security
- [ ] Make a wiki for the project
- [ ] Make file for post requests in other programming languages

## Comment

It's funny how most DNS Protection Companies say that they do crazy SMTP email protection and scanning... since it won't do anything. A good example would be https://www.phishprotection.com/content/domain-name-spoofing/ They based there entire business on it, but they can't guarantee your security. They do what most companies do, try to build a bigger wall so theres less chance the phishing sites can get over. In reality the phishing sites are disguised as citizens and roam in without anyone knowing. My system does things diffrently, every time someone goes in & out of the wall you ask them to wisper the secret code. It's simple and easy.

Secondly, does anyone else find it weird that there offering SMTP protection for something that could easily be done without sending an email. Imagine I want to DNS phish the library (im not creative), all I have to do is make a fake wifi hotspot with the library name, and setup a dns server on it. Now when people join the library wifi they actually join my wifi, I pass the traffic through to the real library wifi so nothing is suspicious. My DNS server is currently making it so that the library login website actually goes to my locally hosted website, it still has the same name and nothing is suspicious because thats what a DNS Phishing site is, a redirected version of the website without the need of any special tools. Using this method I would easily get there password. How is SMTP protection going to stop me? If the library had setup my system so that all there websites had dateTag, then made it so that a chrome extension on the library computers would send the post request to check the dateTag validity. They easily could have made it automatically ban my computer from the network. It's as simple as that.

Is anyone willing to make a chrome extension with me, that would be pretty cool. I have no idea how to make a chrome extension and im way to busy working on other stuff. If you made a chrome extension I would be willing to add it to the project and give you credit for it!
