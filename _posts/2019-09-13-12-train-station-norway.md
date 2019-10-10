---
layout: post
title:  "Norway, Redux"
date:   2019-09-20 09:26:38 +0000
---

To begin with, let me tell a story of my idea. I'd like to take all the photos I've favorited in the
last month and write a short post about them. Here we begin with Norway. I've written a script
to try and as best as possible deduce the date the photo was taken using EXIF data or the file's modify
date if the EXIF data is missing. Here's the (pretty ugly) script if anyone's interested. Note that it
just guesses +2h from UTC if it can't find the time zone, which I'm justifying by guessing most of these
photos were taken in mainland Europe. It's possible that the ones lacking a time offset were stored in
UTC, but my Mac isn't making that assumption (it's guessing the local timezone) and since the pictures
without a time offset were taken by an iPhone I'm not sure what to think.

```python
from glob import glob
import os
import time
from PIL import Image

def get_date_taken(path):
    try:
        exif = Image.open(path)._getexif()

        date_str_time = exif[0x9003]

        try:
            offset = exif[0x9010]
        except KeyError:
            offset = "+02:00"

    except:
        taken = time.gmtime(os.path.getmtime(image_name))
        date_str_time = time.strftime("%Y:%m:%d %H:%M:%S", taken)
        offset = "+00:00"

    date_s, time_s = date_str_time.split(" ")

    return [date_s.replace(":", "-"), time_s, offset.replace(":", "")]

for image_name in glob("source-images/*"):
    title = image_name.split("/")[1].rsplit(".", 2)[0]
    print(image_name)
    date_s, time_s, offset = get_date_taken(image_name)

    fname_time = f"{date_s}-{time_s.split(':')[0]}"

    with open(time.strftime(f"markdown/{fname_time}-{title}.md", taken), "w") as file:
        file.write(f"""---
layout: post
title:  "{title}"
date:   {date_str_time}
---

![{title}]({{{{site.baseurl}}}}/assets/{image_name.split("/")[1]})
""")
```

Without further ado, here's a picture of the train station in Norway.
This is where we hopped on the train to head to Oslo, having flown into a
much cheaper town just outside.

![train-station-norway]({{site.baseurl}}/assets/train-station-norway.jpg)
