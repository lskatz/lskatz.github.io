---
title:  "Yearbook template"
date:   2026-04-26 19:26:01 -0400
categories: yearbook template jekyll git github
layout: single
collection: personal

---

My wife volunteers on the school yearbook committee.
Her job is to solicit pictures, organize them, and help with the yearbook layout.
She and the other volunteers work in a proprietary web tool.
Watching her, I got curious: what if a yearbook was just a Git repo where you can track your code changes and where you can have all the GitHub Actions goodies?
And what if one of those goodies was a [Jekyll](https://jekyllrb.com/) website?
If so, each item could be a yaml entry and each photo could be linked via markdown.

## The process

So I worked with Claude.ai to give it the broad strokes and I iterated about 20 times over the course of the day.
Some of it was with my pro account and when I ran out of credits for that morning, I uploaded what I had to GitHub.
From there, I saw what the Jekyll rendering looked like and then I used GitHub agent to make even more iterative changes.
The conversational mode in Claude was good for sketching structure and adding in broad unit tests;
the GitHub agent was better once I could see things rendering and just wanted small targeted fixes.

After thinking about the privacy concerns, I created it as a template using fake students.
On GitHub, you can label a repo as a template which lets another person click the "use template" button instead of "fork" button.
Each iteration was something different. For example,

* Sidestep the privacy concerns by modeling everything after Malcolm in the Middle
* Make clubs and sports sections
* Make thumbnails on the main page. To sidestep any copyright issue, estimate the SVG image for each character based on known physical characteristics.
* Make it printable with instructions on how to send it to a book printer
* Add an autographs page and a dedications page and a superlatives page
* The layout doesn't look great. Fix it up by uncropping and by having smaller photos.
* This page didn't render - why? _It turns out that the date of the page was in the future and by Jekyll logic, wasn't supposed to be rendered yet._
* Add documentation on the website on how to use it and link back to the repo and add the template version number to the footer.
* Add documentation on the repo on how to program it
* etc

Each time, the site got better and better.

![front page of the yearbook template](/assets/images/yearbook-template/yearbook-template-front-page.png)

Each item is just like I wanted - described by an entry in yaml.

![profile picture](/assets/images/yearbook-template/yearbook-template-yaml1.png)
![profile picture yaml](/assets/images/yearbook-template/yearbook-template-yaml2.png)

And it has all the Jekyll amazingness!
Jekyll is a way to statically render a website, and it gets rendered once each time the code is uploaded to GitHub.
Which makes it a really fast website because it does all the computation up front!

So just all those little yaml entries turns into something really powerful.
Each kid in the yearbook gets indexed to each place they appear, and each place in the yearbook automatically has link backs.
It also has fast client-side search in the form of a small JSON index gets generated from the YAML at build time, and a JS file filters it in the browser.

**Caveats**: this is a hobby project, not a production service.
The demo uses [Malcolm in the Middle](https://www.imdb.com/title/tt0212671/) characters as stand-in students.
If you'd want to try it for real, the README covers privacy and hosting tradeoffs, but use at your own risk (it is MIT licensed).
_That said_ I would **LOVE** to hear any success stories.
It felt great getting this idea out of my head and into a real project in under a day.

## Links

* <https://lskatz.github.io/yearbook-template/>
* <https://github.com/lskatz/yearbook-template>
* [Original Blue Sky message](https://bsky.app/profile/lskatz.github.io/post/3mkdiu4kn2c2m)
