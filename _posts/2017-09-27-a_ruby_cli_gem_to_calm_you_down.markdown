---
layout: post
title:  "A Ruby CLI Gem to Calm You Down"
date:   2017-09-27 09:51:03 -0400
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

```
#code to scrape talks from main page
def self.scrape_talks(index_url)
    doc = Nokogiri::HTML(open(index_url))
    talks = doc.css(".event-item")

    talks.collect do |talk|
      talk_attributes = {
        date: talk.css(".publish-date").text,
        title: talk.css("h3").text,
        url: talk.at("a")["href"]
      }
    end
  end
```

```
#code to scrape additional details about each talk
def self.scrape_talk_details(index_url)
    doc = Nokogiri::HTML(open(index_url))
    talk_attributes = {
      teacher: doc.css("#dnn_ctr466_ViewTalkDetails_hlTeacher").text,
      description: doc.css(".text-holder").text.gsub("\r", "").gsub("\n", "").gsub("\t","")
    }
  end
```

Once I had the scrapes working, I knew I could get the rest of the pieces to fall into place. Here's how I structured everything: 

![](https://go.gliffy.com/go/share/image/s7f1bvkj6334djvbh8yq.png?utm_medium=live-embed&utm_source=custom)

First, I built out breathe.rb, because that was going to be a simple, fun loop that prints out the letters BREATHE in slow motion. I needed #sleep to slow down how the #print statements executed. I also through in a little color by using the Rainbow gem:

```
breathe.each do |letter|
        print Rainbow(letter).cyan
        sleep(1)
        print "."
        sleep(1)
        print "."
        sleep(1)
        print "."
        sleep(1)
      end
```


Breathe.rb has one method, #start, which is a class-level method. I wouldn't be needing to instantiate any Breathe objects to get the functionality I wanted.

Next, I got to work on the classes for the talks and the guided meditations. I built each class separately, to full functionality to make sure they would work. Once that was done, I noticed a lot of redundant code so I started working on either a module or a superclass of some sort to contain the redundant code.

This became challenging because the duplicate methods were class-level methods and I to do some research to figure out how to inherit class methods properly. I went back and forth between using a module and a superclass. Finally, I got a module working and blissfully removed my redundant code:

```
module Recording

  module InstanceMethods

    def play
      print Rainbow("Click the link to play: #{@url}").red
      puts ""
    end
  end

  module ClassMethods

    def create_from_collection(attributes)
      #takes in array of attributes hashes
      attributes.each do |attributes|
        self.new(attributes)
      end
    end

    def list
      #list the meditations for the user
      all.each_with_index { |m, index| puts "#{index + 1}. #{m.title}"}
    end

    def find(input)
      #find a meditation by user input
      all[input.to_i - 1]
    end

    def reset!
      #reset all to clear the list
      all.clear
    end
  end

end

```
