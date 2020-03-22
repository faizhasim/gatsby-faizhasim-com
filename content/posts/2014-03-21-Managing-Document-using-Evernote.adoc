# Managing Document using Evernote

:published_at: 2014-03-21
:hp-tags: mac, productivity, alfred, evernote

If you read my previous posts, I've been trialing some solutions to manage my documents including https://doo.net[Doo], which is unfortunately gain less traction and they were forced to shut down that project. I think it's good product with great ideas, but I suppose, a business cannot sustain without money.

I have decided that I want to stick with Evernote to manage my documents and go paper-less. My strategy is heavily inspired by http://katiefloyd.me/blog/how-i-use-evernote[Katie Floyd's Evernote Tips], http://macsparky.com/paperless/[Paperless by David Sparks] and some of my previous job experiences on Electronic Document and Record Management technical consultations.

From what I see and experience, for most people, it does not matter whether you are managing documents for your enterprise or for your personal filing, the solution has to be easy and fun to use. In order to archive this, the experience of capturing the document, processing them and actually using them must be fun (or at the very least, not annoying).

Let me share my experience on these 3 different stages of processing documents:


# Capture

## Capturing Physical Documents

Previously, I have tried to capture my receipts using my iPhone. But, honestly, after you get a bunch of them, you will feel really lazy to capture them via the camera. Once I have a bunch of receipts to be snapped via camera app, I decided that this is just plain stupidity.

I have a flatbed scanner, but these flatbed scanners are not meant for capturing a stack of documents.

So, I invested in http://www.fujitsu.com/us/services/computing/peripherals/scanners/scansnap/scansnap-iX500.html[Scansnap iX500], bought  from  Amazon. I have my physical receipts in my wallet, my bills and other documents at a physical "Inbox" (could be magnetized on the fridge). Every once a week, I will just collect all of the physical documents into the Scansnap iX500 and scan them all into a directory on my Mac. I use a directory called `Inbox` which sit nicely on my desktop. They will be automatically OCRed. And, I guess with the improvement of today's hardware, the OCR process is damn fast.

I will do a quick preview on the scanned documents using http://en.wikipedia.org/wiki/Quick_Look[Quick Look] from http://en.wikipedia.org/wiki/Finder_(software)[Finder] app, to inspect any anormalies with the scanned documents. Then, I will just export them all into my default Evernote Notebook called `.Inbox`.

## Capturing Electronic Documents

I know Evernote come with the awesome web clipper feature, but I hardly have the need to use it. Maybe I use Evernote primarily to store documents, not notes.

I usually get PDF statments and bills, so most of the time I will just export it into Evernote. But, for statement on the webpage I will convert it as PDF document using http://macsparky.com/blog/2008/3/19/keyboard-shortcut-for-save-as-pdf-in-os-x.html[Twice `CMD` + `P` tricks].

# Process

## Evernote Structure

image::https://cloud.githubusercontent.com/assets/898384/10412684/15d00c56-6fc0-11e5-9aad-07dc1f82d32b.png[]

I have adopted the structure above. The idea is to have an `.Inbox` notebook as to collect unprocessed documents. I do not have a strict workflow to process the document, but, generally, these are my practices:

* Ensure note title starts with relevant date in ISO8601 format, for better sorting. Relevant date could be: bill date, date of purchase or anything.
* For bills, if I cannot pay it here and then, I will reschedule it using Evernote.
* Once I get payment receipt for a bill, I will not create a new receipt, but just attache the receipt to the same note. Evernote does not have the concept of note/document relationship. Plus, practically speaking, it is easier to process and searching for this document is not hard at all.
* I use Evernote checkbox to indicate whether a certain bill has been paid or not.
* I do not use tags extensively. Using tagging extensively without proper planning is an antipattern.

## Automated Document Filing

David Sparks suggested http://www.noodlesoft.com/hazel[Hazel] and possibly https://smilesoftware.com/TextExpander/[TextExpander] to automate document filing. I idea is nice and simple. There will be a folder watcher or scheduler that will inspect your Inbox folder. It will read the PDF content of the documents and depending on the configured rules, it will be automatically filed for you. So, if you are using Evernote, different type of documents will be created in different Evernote notebook.

While both softwares seems really nice, I cannot justify the price for my need. Hazel is USD28 and TextExpander is USD35. Both of them means ~RM200. I guess it's okay if you're earning in USD.

Last month, I kickstarted a personal project to use Evernote thrift API in NodeJS for this document filing automation. But, I was busy with other things, so it was not ready for usage.

## Backup

Honestly, I do not have a reliable backup yet. But, I am looking at CrashPlan for offsite Cloud backup of my documents. Or perhaps, get one of those Drobo storage. Backup need to be redundant, possibly offsite and automated. Incremental backup is a bonus.

# Use

## Querying Evernote using Alfred launcher

I actually bough Alfred powerpack so that I can use custom workflows. So, I can actually retrieve my documents from Alfred, which will query Evernote itself.

So, for example when I need to do my expense claims, I need to retrieve my petrol receipt for last February. So, I will type something like `ens receipt +shell 2014-02*`. 

image::https://cloud.githubusercontent.com/assets/898384/10412683/15cee768-6fc0-11e5-894b-ce66c8b89ba6.png[]

The experience of using electronic document is better than physical papers. Last time, our washing machine shows some funny LED warnings. So, I just open my wife's iPad and open the manual from Evernote app. Then, I just need to search the OCRed manual for `LED` and I got the information I need. Pretty neat!







