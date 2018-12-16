---
layout: post
title: Wget - The Brilliant Downloader
---

# Wget - The brilliant downloader

I used wget extensively over the week to automate downloading of hundreds of elearning resources so I could use them offline. The time spent learning to use wget is a good investment because it’s extremely good as a downloading tool that’s lightweight and versatile. (Plus, wget supports recursive download compared to curl).

Both sites I wanted content from required logins before any download could happen, So cookie grabbing was important:

## Scenario 1
This was a pretty simple one. The files I wanted was a manual consisting of 200+ separate html pages and were all stored within the /book/ folder. So all I needed to do was to use the –recursive option to grab everything.

```
wget \
–recursive \
–no-clobber \
–random-wait \
–header=”Host: portal.downloadsite1.org” \
–header=”User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36″ \
–header=”Cookie: cookie1=value1; cookie2=value2; cookie3=value3; \
–header=”Connection: keep-alive” \
–no-parent \
“https://downloadsite1.com/membersonly/book/“
```

## Scenario 2
This was more complicated because the 600+ files comprising different modules were hosted on Amazon Cloudfront. Cloudfront assigns Unique signed cookies (3) for each module path, and this requires me to grab the cookies for each module and feeding it into the wget cookie parameters. Furthermore, recursive folder download seemed to be blocked and gave a 503 error. Thankfully the file were numbered sequentially, so REGEX expressions did the trick.
This download ended up pretty hands on as it could not be fully automated. I will revisit it in the future if I get enhancement ideas.
```
wget \
–no-clobber \
–wait=3 –random-wait \
–header=”Host: portal.downloadsite2.org” \
–header=”User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36″
–header=”Cookie: CloudFront-Policy=Value1; CloudFront-Signature=Value2; CloudFront-Key-Pair-Id=Value3
–header=”Connection: keep-alive”
–no-parent \
https://downloadsite2.com/videos/{001..103}.mp3
```
### Notes
* -random-wait requires to be paired with –wait=(seconds,minutes,etc). As it multiplies the wait time by 0.5 – 1.5.
* Easiest way to get the right cookie format is by using a browser extension. I used cookies.txt
* -no-clobber causes existing downloaded files to be ignored from being redownloaded (even if the file on server is of a newer version)
* -no-parent prevents wget from downloading files located above the folder specified. This was useful for me when wget tried to crawl & download files from the parent folder, and it would result in the session cookie being revoked.
* The official documentation is good, I relied on it a ton when I was troubleshooting !

* Next steps:
  * Use wget to obtain grab cookies by logging in through webforms
  * Parse cookies from json files into wget.
