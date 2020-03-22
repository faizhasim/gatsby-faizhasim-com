# Strategy for Home Backup System - With Extras

:published_at: 2014-07-20
:hp-tags: 


## iMac crash 😩

I have been thinking and planning about this. But never did anything. Main issue was (and still is) money and Internet speed limitation.

Just couple of days ago, thanks to our national power company for having unannounced multiple electricity blackouts, the HDD in our iMac where we store all of our photos and videos crashes. I'm unable to boot up that machine. Downloding OSX image now to try to salvage as much contents as I could. Oh my photos and videos and other stuffs... 📷

### Why not Online backup?

If we have a good cable connection, I would definitely get one of those online backup solutions. I was thinking of CrashPlan or Amazon S3 or Glacier backup. The problem is, with low ADSL upload speed, I think it's gonna take around 50 weeks to upload all of our photos and videos! Not a viable solution.

### Why not typical external HDD?

I need a local backup for these stuffs. The problem with normal HDD is the reliability and manual backup process. I have not seen normal HDD not fail within 3 years.  What manual backup means to me is, I will backup couple of times in that month, then I will probably ignore it. So, I forsee manual backup with a simple external HDD will not work out well as proven in the past.


## Proper Network Attached Storage could be the answer...

I got no one to blame for this accident but myself. Of course, I am little bit pissed with the electric company not announcing for the blackouts. But, still, my data is my responsibility, not theirs.

So, I am thinking to get a good, reliable NAS storage for my home usage.

### The NAS solution **must** be:

- Redundant (obviously). Should one of the HDD fails, data is still redundant on the other HDD.
- Solution-wise, Mac-compatible.
- Easy to use from a non-technical perspective. Ideally it should be configure once and forget. I do not want to keep bashing the keyboard to re-configure stuffs all the time. More importantly, I want to configure it on both my working lappy and my wife's.

### It's **nice (but not necessary)** for the NAS solution to support:

- Able to connect via WiFi. Somewhat important, as I need to hide this far away from my son and place it somewhere in the smaller room. Wired means, I need to drag the cable into the room or place the NAS near the WiFi router, but still need to hide it from the kid.
- Bittorrent
- Able to stream using AirPlay or at least, DLNA-compatible.
- Incremental backup. I might use Time Machine, but ready to explore other solutions if appropriate.
- Crashplan remote backup. So, ideally I can install Crashplan on my lappy. When I'm connected at my local network, the incremental backup will start automatically.
- Being able to run node.js. I have some node.js scripts running on my previous iMac and I would be nice to have similar setup on the NAS.
- OpenVPN. Either as a client or as the server itself.

### The NAS solution **doesn't have to** be:

- Fast. Main usage is for incremental local backup.
- Tied to any RAID setup or propritery setup. So, proprietry RAID-like solution is perfectly ok with me.


> I'm thinking of something like Drobo 5N or Synology Synology 713+

These NAS stuff is damn expensive! I might need to setup payment plan to afford one. Better be a good investment.

What do you guys think?



> Updates:

`[2014-07-20 - 10:22:33]` I am looking at dual-bay Synology DS214 or DS214Play with RAID1 setup. Looks like a better solution for my case.

`[2014-07-21 - 06:57:32]` Yesterday, I have tried to reboot the mac with both DVD and USB. Also resetted the NVRAM. No luck. Perhaps, there is different hardware failure that prevent the mac to be boot up correctly. Please HDD don't fail then. Finger-crossed.






