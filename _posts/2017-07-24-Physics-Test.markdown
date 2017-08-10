---
layout: post
title: How to rate a physicist (?)
series: Physics
date: '2017-07-24 17:28:15'
---

My boss has been away for a couple of weeks. I'm in that weird limbo between the completion of a project and the beginning of another one. Uncertain about what to do next and somewhat bored to even start thinking. I figured that all I needed was some good old ego boost. Didn't turn out as i thought.

The community of physicists to which I belong, high energy physics, is somewhat small and peculiar. Our job is to develop new ideas and write papers about them. The new ideas we try to develop are about Nature, trying to understand and explain the way it works at the most fundamental level.

The papers we write are usually published on scientific journal like in any other scientific field. What is peculiar about high energy physics is that nobody really cares about scientific journals and to make our ideas immediately accessible as they are in final form the [arXiv] was created (in the beginning of the nineties). When a paper of mine is ready I would just post it there for anybody to read it. For free.

The [arXiv] is divided in many categories depending on the discipline. All my papers are in two of them [hep-ph] and [hep-th], standing for High Energy Physics - Phenomenology and Theory.

The arXiv did not exist when [Steven Weinberg] wrote his most cited and Nobel worth paper, [A Model of Leptons], so you will not find it there. Luckily there is another online tool which is bread and butter for high energy physicist, which allows you to search for every single paper ever written by the community. This is the [INSPIRE] database.

INSPIRE periodically upload a [screenshot] of the whole high energy physics production (and more) in XML form. Every paper and author is assigned a unique identifier. The database contains information about each paper, like title, abstract, date of appearance in any form, arXiv identifier (if it applies), and so on. An almost complete description of the database can be found [here].

So my plan: download the INSPIRE database and compare various bibliographical indices for the physicist of my generation.

# The database

Two files are relevant for our purposes, they are HEP and HepNames, the first containing records of papers and the second records of authors both with their unique ID. 

The first problem to face is the size of the HEP file, something short of 18 GB. Simply reading it into memory is out of the question as it would annihilate my laptop. One possibility is to be somewhat clever and use some library like `ElementTree` to parse the XML. I ain't got no time to learn all that. One very efficient brute force approach is to parse the XML file like a normal text file and save the relevant information from each record, without overloading your memory.

<!--```js-->
<!--rec = ''-->
<!--with open('HEP-records.xml','rb') as f:-->
<!--    flag = False-->
<!--    for line in f:-->
<!--        if '<record>' in line:-->
<!--            rec = line-->
<!--            flag = True-->
<!--        elif '</record>'in line:-->
<!--            rec += line-->
<!--            flag = False-->
<!--            process_record(rec)-->
<!--            del rec-->
<!--        elif append:-->
<!--            rec += line-->
<!--```-->

The previous loop read the content of the XML between two `<record>` tags and call the function `process_record` on it. This function now has to deal with a very small chunk of the original XML file and extract the relevant info from it. For instance

```js
import xml.etree.ElementTree as ET

def process_record(rec):
    chunk = ET.fromstring(rec)
    item=chunk.findall("./controlfield[@tag='001']")
    dictionary = {'item': item}
    append_record(dictionary)
```

In this case the `001` tag read the unique INSPIRE ID for the paper. Many more field have of course to be recorded for the analysis. The function `append_record` finally append the dictionary to a file (again without having to load the whole thing in memory)

```js
import os

def append_record(dictionary):
    with open('INSPIRE', 'a') as f:
    json.dump(dictionary, f)
    f.write(os.linesep)
```

The resulting file is in JSON style but it is not a JSON yet, it has to be properly wrapped, but that is easy.


{% include image.html file="worldoshit.jpg" description="Test for a caption." %}



[arXiv]: https://arxiv.org/
[hep-ph]: https://arxiv.org/list/hep-ph/new
[hep-th]: https://arxiv.org/list/hep-th/new
[Steven Weinberg]: https://en.wikipedia.org/wiki/Steven_Weinberg
[A Model of Leptons]: https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.19.1264
[INSPIRE]: https://inspirehep.net/
[screenshot]: http://inspirehep.net/dumps/inspire-dump.html
[here]: https://twiki.cern.ch/twiki/bin/view/Inspire/DevelopmentRecordMarkup
