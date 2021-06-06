# DuckDuckGo Image Scraper
A C# (.NET) implementation of grabbing image URLs from the search engine [DuckDuckGo](https://duckduckgo.com).

Python implementation provided by [Joe Dockrill](https://github.com/joedockrill/jmd_imagescraper)


## Quack! What's the deal?

After finding a need for an image scraper, it became relatively apparent from searching around that no C# / .NET implementation existed for DuckDuckGo, and it also revealed at first how strangely confusing the documentation for DuckDuckGo's image algorithm actually was.

Thankfully, after a few examples, this task was easy enough to accomplish.

The code you see in ImageScraper.cs has been ripped out of one of my existing projects, and it is mostly working. It only needs some minor
changes to work its way into your program.

If you are using another programming language or simply another protocol for Networking,
I have also provided general pseudocode below on how to best accomplish image scraping from DuckDuckGo.  

## Summary

To summarize, your DuckDuckGo Image Scraping program needs to make two requests to the search engine.
The first request tests against the keyterm that you are attempting to scrap images from, such as "dogs", for example.

![image](https://user-images.githubusercontent.com/76540311/120916018-9a27cf80-c66c-11eb-8df7-8af5dcb03225.png)

DuckDuckGo keeps a special key attached to specific search queries, and the query of "dogs" will have a unique VQD present in the HTML.
You can easily find this VQD by performing a CTRL-F search for it when you inspect the HTML of the website.

![image](https://user-images.githubusercontent.com/76540311/120916060-e70ba600-c66c-11eb-8b83-fea7014e0130.png)

This number is neccesary for querying DuckDuckGo's i.js for our required images! It is absolutely unique to each and every search term provided,
and so it needs to be fetched every single time the query changes. In our case, we'll fetch it every time.

The second request takes that unique VQD and sends it along with a couple of other parameters to actually get a result from DuckDuckGo's i.js javascript.
This response will be a lot of garbage specifying thumbnails, heights, and titles of images. If you need that information, feel free to change the code to grab it as well.

This request looks kind of wonky, and it's fine if you don't completely understand the details of it.

https://duckduckgo.com/i.js?l=us-en&o=json&q=dogs&vqd=3-29643716862703163448438133325666064316-164904873373437596410230701441639107791&p=1&v7exp=a

To make it clear, the first part is the the query to DuckDuckGo's Javascript, duckduckgo.com/i.js
Every parameter after is simply a parameter to make the Javascript function as intended, specifying locality, query, JSON, and most importantly, VQD.
Some parameters, such as p=1 for enabling SafeSearch, v7exp=a for some unknown function (expiration, probably) are not neccesary, and can be removed.

![image](https://user-images.githubusercontent.com/76540311/120916204-d7d92800-c66d-11eb-8300-29042caa3c57.png)

The part we will be concentrated is the URLs directly after the "image": qualifiers. There will be one hundred of these, as DuckDuckGo has returned us the information
of the first one hundred images that occur when someone searches up the keyword.

Simply grab those URLs by checking for the positions of each "image": and the single quotes around each URL, and you'll be good.

## Pseudocode!

```

void makeImageList() {

Define a keyword, a URL, and send your first request to fetch your VDQ.
If something isn't working, attach different headers. Try visiting the resulting HTML in your browser to see if it spits the same result.

I suggest you organize your code into two different functions, but you can do it however you want to.

string vdq = GetVDQ(url, keyword);

Now that the special code is grabbed, chunk it into another request to DuckDuckGo. This time, with all parameters attached.

Your URL should look like this: duckduckgo.com/i.js?l=us-en&o=json&q=KEYWORD&vqd=YOURVDQ
where KEYWORD is your search item and YOURVDQ is the vdq you successfully returned from earlier.
Remember, if you need to enable more filters, simply specify more parameters. (SafeSearch, etc. -> https://duckduckgo.com/params)

List<string> stringList = GetImageList(newurl);

Now, iterate through the stringList and look at all the image URLs you scraped! Good job!

}

string GetVDQ(string url, string keyword) {

Call your networking client. This could be different for any and all programming languages, but generally works the same way.
Make it connect to the URL- duckduckgo.com/?q=keyword 
where keyword is your search item.

Attach your headers as need be, and return the HTML body text of the webpage.
Iterate through the string of the HTML and find the VDQ. Grab it.

return vdq;

}

List<string> GetImageList(string newurl) {

Again, call your networking client and attach all proper headers.
Get the same HTML body text of the webpage.

This time, you are looking for each URL that is directly after the words "image":
In my code, I did this with an IndexOf, a while loop, and an iterator to progressively advance the beginning of the startIndex 
past each found URL. For you, there are plenty of ways to accomplish this task, and do whichever way is best for your situation.

Progressively add all the image URLs to your growing List.

return stringList;

}
