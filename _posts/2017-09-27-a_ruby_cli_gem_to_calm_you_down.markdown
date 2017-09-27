---
layout: post
title:  "A Ruby CLI Gem to Calm You Down"
date:   2017-09-27 13:51:02 +0000
---


I built my first thing! Well, I've built lots of things, but mostly little things. Little pieces of code to practice a skill or concept. This is a real thing! 

The project took me a little bit longer than I expected because I really wanted to stick with my focus -- playing recent Insight Meditation talks -- rather than hunt around for an easy web page to scrape.

I started out with [Dharmaseed.org](http://dharmaseed.org/talks/), which is the site my workplace (Spirit Rock Meditation Center) uses to store all of its recordings. I discovered that it wasn't planning to play nicely with my current skill. Dharmaseed talks are listed in tables, so working with the CSS selectors was proving to be challenging. The problem with the table of talks was that all of the information I needed was wrapped in a <tr> tag rather than semantic tags. I couldn't distinguish between them in an efficient way.

I moved on to finding some guided meditations from [UCLA's mindful awareness site](http://marc.ucla.edu/mindful-meditations). These were also listed in a table, but the data was less convoluted to extract, so I pulled a few of these into my program. It's not a dynamically updated list, however, so even though I included it in my project (because I wanted the user to have access to these guided meditations), it wasn't exploiting one of the true powers of scraping, which is to access dynamically updated information. So I wasn't yet done with my quest.

```
#code for scraping guided medtiations from the UCLA Mindful Awareness site
def self.scrape_meditations(index_url)
    doc = Nokogiri::HTML(open(index_url))
    rows = doc.css("tr")
    meditations = rows[1..8]

    meditations.collect do |meditation|
      stream_xml = meditation.css("td:nth-child(2)")
      meditation = {
        title: meditation.css("td:nth-child(1)").text,
        url: "http://marc.ucla.edu/#{stream_xml.at("a")["href"]}"
      }
    end
  end
```

Finally, with a reminder from a Flatiron Slack member of Tara Brach's work, I landed upon the [Insight Meditation Community of Washington's site](http://imcw.org/Talks). This one was going to work better-- dynamically updated lists of recent talks wrapped in semantic containers! It gave me the opportunity to share quality content with my users as well as to practice my Nokogiri skills with dynamic content. I used Nokogirl to scrape talks from the main list then used it again to scrape additional details from each talk.

