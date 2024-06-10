---
title: "Singular"
datePublished: Mon Jun 10 2024 02:00:32 GMT+0000 (Coordinated Universal Time)
cuid: clx8bt8av00000alc7g6h8fe9
slug: singular
canonical: http://chetenright.com/content/dist/blog/singular/
tags: python, anime

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717973903323/870649d0-f2ef-4455-baf8-db7b33fa9e21.png align="center")

### **Disclaimer: Always support content creators, large or small, and use legal sources only. No Company is too large to not need your support.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717974211404/758a6839-caa3-4aed-89b4-90ebc05451f6.gif align="center")

### **Github Link**

[https://github.com/amunchet/singular](https://github.com/amunchet/singular)

### **Summary**

Singular was born when I wanted to aggregate my anime across several channels. Today's media has become so fragmented, it becomes difficult to keep up - that's where Singular aims to help.

One point of note: due to my home's location, I don't have the bandwidth to have a good streaming experience. Singular helps buffer these streams locally for easy viewing. Once a season is completed, all the files are deleted to make room for the next season - no media is retained permanently.

Singular is a two component system: one part is a JSON based, automatic torrent downloader, designed to be run on a schedule. The second part is the frontend to manage the shows that are to be downloaded. There are a few nice features that are specifically designed for blogging (which I am a junior member of the Star Crossed Anime blog team). Singular is meant to compliment, not replace, a media server, such as **Plex**.

I made Singular a docker (actually I use docker-compose mostly), since I wanted it extremely portable. Truth be told, it was originally running on my Windows machine before it was transferred to the proper home-server. In terms of portability, nothing beats docker. Here I'll briefly touch on some of the design decisions I came across throughout the process.

For the backend, I'm doing a lot of Python these days. It's really hard to beat - Python gets you doing what you're actually interested in, not fiddling with the language and compilers/interpreters. There are two python sections: the api for the frontend and the actual worker, which goes out to the RSS feed and downloads the content.

### **Tests**

Why are there no tests? Because the project started out as a collection of scripts and morphed into a real project. One day, I may go back and create tests - this is abnormal for me not to do TDD, and honestly, I felt the pain of it halfway through. TDD (Test Driven Development) really works when you have clear goals in mind - Singular is an organic creation, so I really wasn't sure what I wanted until I made it (not great software design, but pragmatic). When I clean this project up, I'll give it full test coverage (`pytest` and `Jest`).

### **Backend**

The main downloader loop:

```python

def main():
    """
    Main loop
    """
    config = read_json()
    with open(LOG, "w") as f:
        f.write("[Starting]\r\n")

    for feed in read_rss(config["rss_feed"]):
        for item in feed:
            match = is_match(item.title, [x[0] for x in config["shows"]])
            do_thumb = 1
            if match:
                try:
                    do_thumb = download_torrent(
                        item.links[0].href, match, item.title)
                except Exception:
                    log("Download error: " + str(sys.exc_info()[1]))
                    do_thumb = 1

            temp = [x for x in config["shows"] if x[0] == match and len(
                x) > 2 and "thumbnail" in x[2] and str(x[2]["thumbnail"]) == "1"]

            if temp != []:
                log("Generating thumbnail...")
                thumb(match)

    artwork()

```

The backend Python is mostly unremarkable. `SocketIO` and `flask` are really the most interesting bits; also, integration of Docker control from within Python was neat. I've started to see how in Linux everything being a file really lends itself to Docker's shared volumes paradigm. Some additional niceties of the backend include thumbnail generation (`ffmpeg` for the given downloaded files) - that saved me tons of time for when I'm blogging about a show. Since I use **Plex**, I also include a pretty thumbnail for each new show that's generated.

### **Frontend**

Snipped from converting to Google Doc view:

```python

var all_shows = [...this.json.dropped, ...this.json.completed_shows]

all_shows.forEach(val => {
    var created_show = {
        "name": val[0],
        "thumbnail_url": val[1],
        ...val[2]
    }


    if (val[2] != undefined && val[2].season != undefined && val[2].year != undefined) {
        var found = found_seasons.filter(x => x.year != undefined && x.season !=
            undefined && x.year == val[2].year && x.season == val[2].season).length
        if (found < 1) {
            found_seasons.push({
                "season": val[2].season,
                "year": val[2].year,
                "shows": [created_show]
            })
        } else {
            found_seasons.forEach(found_val => {
                if (found_val.season == val[2].season && found_val.year == val[2]
                    .year) {
                    found_val.shows.push(created_show)
                }
            })
        }
    }

```

The frontend is pretty standard Vue, with Bootstrap for the styling. I save my personal notes on all shows in Google docs, so I made a nice formatter that I could copy and paste directly in. Google Docs allows pasting of tables (and they fixed the annoying bug with different font weights pasted in), so that's been very nice. Frontend also bring in RSS feed to take a look at what's currently airing - so new shows aren't missed. The show and RSS feed is a basic CRUD that's saved on disk as a JSON file. Nothing terribly special there.

Other small artifacts include some bootstrapping scripts to get into the development environment, and some simple logos/screenshots (as seen above).

A final debatable implementation point: I ended up bundling some torrent software with Singular - I wanted it to work out of the box.

### **Conclusion**

Overall, Singular was a project I made to scratch my own itch: automatic management of anime content, so I could spend more time watching and blogging. And it's accomplished the goal: since releasing version 1, I've not had to think about missing episodes or what shows I watched but forgot - everything is taken care of, so I have more time to do the important things. That's good software in my book - something that gets the problems out of your way, so you can focus on the real task: relaxing ^\_^

If you happen to watch serialized shows, 2D or 3D, give Singular a try - especially if you like to write notes about what you watch.