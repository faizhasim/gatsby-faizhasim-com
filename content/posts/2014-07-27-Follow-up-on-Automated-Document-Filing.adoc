# Follow up on Automated Document Filing

:published_at: 2014-07-27
:hp-tags: 


This post is a follow-up on my previous post on the `Automated Document Filing` section.

## http://www.trankynam.com/atext/[aText] - Typing shortcut for Mac

- I managed to find cheaper alternative of TextExpander. USD5 for aText vs USD35 for TextExpander.

- After trialing aText, I have decided to buy it.
- At the moment, I do not really have much text snippets. But, I do want to highlight some of it, which I use a lot:
  - `;td` will gives today's ISO date. For eg: `2014-07-27`
    - `;evn` (means evernote) will give me default evernote title: `2014-07-27 - {{cursor}}`
    - `;userstory` will give me user story format for my planning at work:
            
            h3. Story
            
            *In order to* {{do something}}
            *As a* {{User or Admin}}
            *I want to* {{some more descriptions}}
            
            h3. Acceptance Criteria
            
            - {{cursor}}
            
  - `;cls` will give me default Javadoc format for my Java class:
    
image::https://cloud.githubusercontent.com/assets/898384/10412818/32674998-6fc4-11e5-95f3-29db576a0a94.png[]
  
- I think such tool helps me to be more productive on my personal work and professional work. I would recommend any "typing shortcut" application for your productivity.
- Some other possible use case with this app:
  - File naming convention
    - Write an email with default template
    - Write meeting minute with predefined template
- Need more ideas on this topic? I would recommend http://macsparky.com/tesnippets/[TextExpander Snippets by David Sparks]

## Automated Filing System

- After evaluating Hazel based on its functionality and capability, I have decided to come out with my own solution for automated filing system.

- I wrote a Node.js project. Check it out at:

  > https://github.com/faizhasim/categorize-inbox-to-fs/[categorize-inbox-to-fs on Github]

- The objective of this project is to read the contents of  documents and sort them into directory structure based on regex pattern matching.
- It **does not** perform OCR on PDF. I found that Abyss OCR bundled with ScanSnap is far superior to open-sourced Terracota engine anyway, so I will leave OCR automation to ScanSnap.
- At the moment, my script is able to detect petrol receipts from Shell and Petronas, which probably takes around 30% of my scanned documents.
- The script will also try to guess (based on regex) the receipt date and will rename the file to the following format: `2014-07-27 shell fuel receipt`
- After running the script, I will review the results using quicklook feature in Finder app and possibly make necessary changes if needed.
- Then, I will send those files to the respective Notebook in Evernote.
- Some lessons that I learn while writing this:
  - Removing spaces will help with regex pattern detection because sometimes you will have `S H E L L`, instead of `SHELL`.
    - Confusing characters:
      - `D`, `O` or `0`
        - `i` or `1` or `l`
        - `A` or `4`
        - `s` or `3`
        - Excluding some terms would be useful. For example, to match `UNIT123 SOME-HARD-TO-OCR-WORDS KL SENTRAL`, it would better to match with `/UNIT123.*KLSENTRAL/gi`. (note: I removed all the spaces prior to regex matching)
- I am contributing my work on public domain. Obviously I will not commit any sensitive data. Thus, I am managing a separate branch for my own rules.
- TODOs:
  - More rules to detect other documents: Internet/Phone bills, Groceries etc
    - Improve project structure to extract the rules into a different module. I haven't really put much thought on this.



