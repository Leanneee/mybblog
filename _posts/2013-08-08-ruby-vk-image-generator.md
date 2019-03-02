---
title: Image generator for VKontakte groups
permalink: / ruby-vk-image-generator /
categories: ['Ruby', 'Tutorials', 'Social Networks']
tags: ['ruby', 'vkontakte']

---

Today I will tell you how to write an application for generating images and posting them to the social network vkontakte.
<! - more ->

### Generation of images. To generate images, I will use pre-prepared templates (blanks).

The following code will add the current date and horoscope content:


`` ruby
require 'RMagick'
# date and description we will impose on the workpiece
date = "Thursday, August 8th"
description = "Today, your loved one will have a hard time understanding your true desires and intentions. He will try to bring them out in a logical way, but no one can guarantee him the right result. Unless you have mercy and tell."

# Create a canvas and add our blank to it aquarius.jpg
canvas = Magick :: ImageList.new ("aquarius.jpg")

# Add a date to our preset
horo_date = Magick :: Draw.new
horo_date.font_family = 'helvetica'
horo_date.pointsize = 16
horo_date.interline_spacing = 7
horo_date.annotate (canvas, 0,0,180,57, date) {
  self.fill = '# 929da7'
}

# Add horoscope content
horo_description = Magick :: Draw.new
horo_description.font_family = 'helvetica'
horo_description.pointsize = 22
horo_description.interline_spacing = 7
horo_description.annotate (canvas, 0,0,190,100, description.place (40)) {
  self.fill = '# 929da7'
}

# Write all our changes to the new file.
canvas.write ("test.jpg")
`` `

At the output we get this:

You probably noticed that in the code above there is a place method. It is needed to break down and transfer the lines of the contents of the horoscope.

`` ruby
class String
  def place (count)
    words = self.split ("") # All words from text
    compozition = "" # Composition - a string not exceeding the count_sym characters
    summary = [] # Collection of our compositions

    words.each do | word |
      if (compozition.size + word.size + 1) <= count then
        compozition + = "" + word
      else
        summary << compozition
        compozition = word
      end
    end
    # Add the last piece of text to the summary which was not processed in the cycle each
    summary << compozition
    # Remove the extra space at the beginning
    summary.first [0] = ""

    return summary.join ("\ n")
  end
end
`` `

#### Sending to the wall of Vkontakte group We will send pictures to the wall using gem vkontakte_api

`` ruby
require 'vkontakte_api'
public_id = 'group ID VKontakte'
VkontakteApi.configure do | config |

  config.app_id = VK_APP_ID
  config.app_secret = VK_APP_SECRET
  config.adapter =: net_http
  config.http_verb =: post
  config.faraday_options = {
    ssl: {
      : verify => false
    },
  }
end

# Create customer
app = VkontakteApi :: Client.new (VK_ACCESS_TOKEN)

# Get the url to load on the wall
us = app.photos.get_wall_upload_server (gid: public_id)

# POST request to the received address
up = VkontakteApi.upload (url: us.upload_url, file1: ["Image path", 'image / jpeg'])

# Add a description
up.caption = "http://horo.ru"

# Add gid to our up, which has server, hash and photo parameters
up.gid = public_id

# Save photos
save = app.photos.save_wall_photo (up)

# Send to the wall of the group
app.wall.post (attachments: save.first.id, owner_id: "- # {public_id}", from_group: 1)
`` `

Well, we have image generation, sending pictures too. It remains only to receive data that will fill our pictures.

#### Parsing horoscopes I will take horoscopes from the site ignio.com, especially since they provide receiving horoscopes via XML. Parsing XML will be using Nokogiri.


`` ruby
require 'nokogiri'
require 'open-uri'

url = "http://img.ignio.com/r/export/utf/xml/daily/lov.xml"
feed = Nokogiri :: XML open (url)

feed.xpath ("// aquarius // today")

# => [# <Nokogiri :: XML :: Element: 0x123abf8 name = "today" children = [# <Nokogiri :: XML :: Text: 0x123a928 "\ nDo not expect too much from your loved one. He’s not a magician he is only learning, so he cannot be guessed, let alone fulfill all your desires at the same time (and especially those that you did not express to him personally). Be merciful - and you will be happy. \ n ">]>]
`` `

Well, actually all. We received the texts, we have the image generator, and we also have the upload to the VKontakte groups. It remains only to automate everything. The source code of the finished application can be viewed [here] [1]. And [here] [2] you can see what happened.

