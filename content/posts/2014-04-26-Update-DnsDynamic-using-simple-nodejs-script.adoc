# Update DnsDynamic using simple nodejs script

:published_at: 2014-04-26
:hp-tags: nodejs, dynamicdns

I'm in the middle of configuring dynamic dns on my home network because I'm planning on exposing a few services for my own use. VPN will, ultimately, be one of them.

So, I've configured www.dnsdynamic.org and wrote a simple nodejs script to update my IP address every hour. The script is as following (`request` and `cron` module were used, look for them in the NPM registry):

```
var url="https://«username»:«password»@www.dnsdynamic.org/api/?hostname=«my.host.name»&myip="
var request = require('request');

var CronJob = require('cron').CronJob;
var job = new CronJob('0 0 */1 * * *', function() {
  request('http://fugal.net/ip.cgi', function (error, response, ipAddress) {
    request(url + ipAddress, function(errror2, response2, body) {  
      if (!error && response.statusCode == 200) {
        console.log(body);
      }
    });
  });
}, function() {
  console.log('exitting');
}, /* start now */ true);

job.start();
```

I've also intalled https://github.com/nodejitsu/forever[forever] to ensure that script will always be run on my home iMac.
