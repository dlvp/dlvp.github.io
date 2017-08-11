---
layout: post
title: How to rate a physicist (?)
series: Physics
date: '2017-07-24 17:28:15'
---

<!--My boss has been away for a couple of weeks. I'm in that weird limbo between the completion of a project and the beginning of another one. Uncertain about what to do next and somewhat bored to even start thinking. I figured that all I needed was some good old ego boost. Didn't turn out as i thought.-->
<!---->
<!--The community of physicists to which I belong, high energy physics, is somewhat small and peculiar. Our job is to develop new ideas and write papers about them. The new ideas we try to develop are about Nature, trying to understand and explain the way it works at the most fundamental level.-->
<!---->
<!--The papers we write are usually published on scientific journal like in any other scientific field. What is peculiar about high energy physics is that nobody really cares about scientific journals and to make our ideas immediately accessible as they are in final form the [arXiv] was created (in the beginning of the nineties). When a paper of mine is ready I would just post it there for anybody to read it. For free.-->
<!---->
<!--The [arXiv] is divided in many categories depending on the discipline. All my papers are in two of them [hep-ph] and [hep-th], standing for High Energy Physics - Phenomenology and Theory.-->
<!---->
<!--The arXiv did not exist when [Steven Weinberg] wrote his most cited and Nobel worth paper, [A Model of Leptons], so you will not find it there. Luckily there is another online tool which is bread and butter for high energy physicist, which allows you to search for every single paper ever written by the community. This is the [INSPIRE] database.-->
<!---->
<!--INSPIRE periodically upload a [screenshot] of the whole high energy physics production (and more) in XML form. Every paper and author is assigned a unique identifier. The database contains information about each paper, like title, abstract, date of appearance in any form, arXiv identifier (if it applies), and so on. An almost complete description of the database can be found [here].-->
<!---->
<!--So my plan: download the INSPIRE database and compare various bibliographical indices for the high energy physicist of my generation.-->
<!---->
<!--# Reading the database-->
<!---->
<!--Two files are relevant for our purposes, they are HEP and HepNames, the first containing records of papers and the second records of authors both with their unique ID. -->
<!---->
<!--The first problem to face is the size of the HEP file, something short of 18 GB. Simply reading it into memory is out of the question as it would annihilate my laptop. One possibility is to be somewhat clever and use some library like `ElementTree` to parse the XML. I ain't got no time to learn all that. One very efficient brute force approach is to parse the XML file like a normal text file and save the relevant information from each record, without overloading your memory.-->
<!---->
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
<!---->
<!--The previous loop read the content of the XML between two `<record>` tags and call the function `process_record` on it. This function now has to deal with a very small chunk of the original XML file and extract the relevant info from it. For instance-->
<!---->
<!--```js-->
<!--import xml.etree.ElementTree as ET-->
<!---->
<!--def process_record(rec):-->
<!--    chunk = ET.fromstring(rec)-->
<!--    item=chunk.findall("./controlfield[@tag='001']")-->
<!--    dictionary = {'item': item}-->
<!--    append_record(dictionary)-->
<!--```-->
<!---->
<!--In this case the `001` tag read the unique INSPIRE ID for the paper. Many more field have of course to be recorded for the analysis. The function `append_record` finally append the dictionary to a file (again without having to load the whole thing in memory)-->
<!---->
<!--```js-->
<!--import os-->
<!---->
<!--def append_record(dictionary):-->
<!--    with open('INSPIRE', 'a') as f:-->
<!--        json.dump(dictionary, f)-->
<!--        f.write(os.linesep)-->
<!--```-->
<!---->
<!--The resulting file is in JSON style but it is not a JSON yet, it has to be properly wrapped, but that's easy.-->
<!---->
<!--```js-->
<!--with open('INSPIRE') as f:-->
<!--    list = [json.loads(line) for line in f]-->
<!--with open('INSPIRE.json', 'w') as f:-->
<!--    json.dump(list, f)-->
<!--```-->
<!---->
<!--The full codes to read both the HEP and the HepNames database can be found one [GitHub]. I also uploaded a reduced version of the HEP database itself. It is a snapshot of INSPIRE at the time this post was written, so it can get old.-->
<!---->
<!--The field which are relevant for the bibliographic analyisis are the following. From HEP-->
<!---->
<!--```js-->
<!--item=chunk.findall("./controlfield[@tag='001']") # ID-->
<!--cat=chunk.findall("./datafield[@tag='037']/subfield[@code='c']") # arXiv category-->
<!--date=chunk.findall("./datafield[@tag='269']/subfield[@code='c']") # date of appearance-->
<!--aut1=chunk.findall("./datafield[@tag='100']/subfield[@code='x']") # first author ID-->
<!--aut2=chunk.findall("./datafield[@tag='700']/subfield[@code='x']") # other authors ID-->
<!--refs=chunk.findall("./datafield[@tag='999'][@ind1='C'][@ind2='5']/subfield[@code='0']") # list of references-->
<!--```-->
<!---->
<!--and from HepNames-->
<!---->
<!--```js-->
<!--item_aut=chunk.findall("./controlfield[@tag='001']") # ID-->
<!--name=chunk.findall("./datafield[@tag='100']/subfield[@code='a']") #author name-->
<!--```-->
<!---->
<!--The resulting tables can be read in as `pandas` dataframes. -->
<!---->
<!--```js-->
<!--import pandas as pd-->
<!---->
<!--data_HEP=pd.read_json('INSPIRE')-->
<!--data_names=pd.read_json('HEPNAMES.json')-->
<!---->
<!--```-->
<!---->
<!--This is the way one HEP record looks.-->
<!---->
<!--{% include image.html file="heprec" description="Also known as NNaturalness" %}-->
<!---->
<!--I joined the field `aut1` and `aut2` into the new field `authors` and extracted a field `year` from `date`.-->
<!---->
<!--# The analysis-->
<!---->
<!--Now the various simplifications for the analysis. I am interested in comparing the my bibliographical record with those of authors active in my field and belonging to my same generation. I published my first paper in the second half of the noughties. All my papers get sent to the arXiv before they get pubished on a journal and this is pretty common for people in my field. More specifically all my papers appear on the hep-ph and hep-th categories of the arXiv.-->
<!---->
<!--I will thuse rate authors in terms of papers they wrote on hep-ph and hep-th. I will furthermore restrict to those who published their first paper on hep-ph and hep-th no earlier than 2006.-->
<!---->
<!--The arXiv category paper can be selected according to the `cat` field-->
<!---->
<!--```js-->
<!--data_HEP=data[data['cat2_'].isin(['hep-ph','hep-th'])]-->
<!--```-->
<!---->
<!--In order to enforce the time requirement I define a list of unique authors as-->
<!---->
<!--```js-->
<!--temp=data_HEP['authors'].tolist()-->
<!--unique_authors = [item for sublist in temp for item in sublist]-->
<!--unique_authors = set(unique_authors)-->
<!--```-->
<!---->
<!--I then define 'old' authors as those having a paper before 2006, and 'young' authors as the rest-->
<!---->
<!--```js-->
<!--data_HEP_old=data_HEP[data_HEP['year0_']<2006]-->
<!--temp=data_HEP_old['authors_'].tolist()-->
<!--unique_authors_old = [item for sublist in temp for item in sublist]-->
<!--unique_authors_old = set(unique_authors_old)-->
<!--unique_authors_young = [x for x in unique_authors if x not in unique_authors_old]-->
<!--```-->
<!---->
<!--The number of young authors so defined is 10983. I also define `data_HEP_young` as the subset of hep-ph/th papers written by at least one 'young' author. The number of such papers is 42612.-->
<!---->
<!--So some disclaimer. If you are reading and your most cited paper appeared on astro-ph, well, it will not be counted. Similarly if you belong to a big experimental collaboration but you are also a theorist, your 1000+ papers from [CMS] from your experiment will not be counted. Also, give a 10% error margin to all the numbers I will quote. Don't get mad at me.-->
<!---->
<!--Notice however that ALL paper of the INSPIRE database will be considered for citations. So if you paper written on hep-ph had on some other field like hep-ex or astro-ph, this will matter.-->
<!---->
<!---->
<!--# Some results-->
<!---->
<!--The first thing one can do is to associate to every 'young' author the number of paper he/she wrote. This can be done with a simple list comprehension counting how many times each young author appears among the entries of `data_HEP_young['authors']`.-->
<!---->
<!--```js-->
<!--from collections import Counter-->
<!---->
<!--authorship_young=data_HEP_young['authors'].tolist()-->
<!--authorship_young=[item for sublist in authorship_young for item in sublist]-->
<!--authorship_count_young=Counter(authorship_young)-->
<!--data_authors=pd.Series(unique_authors_young).to_frame(name='authorID')-->
<!--num_papers=[authorship_count_young[str(l)] for l in unique_authors_young]-->
<!--data_authors= data_authors.assign(n_papers=pd.Series(num_papers).values)-->
<!--map_names=pd.Series(data_names.name.values,index=data_names.item_aut).to_dict() #Add name-->
<!--data_authors['name']=data_authors['authorID'].map(map_names)-->
<!---->
<!--```-->
<!---->
<!--This is the result ordered py 'n_paper'. The field 'year_0' show the year the first paper was published.-->
<!---->
<!--{% include image.html file="numpap" description="Da fuq" %}-->
<!---->
<!--DANG! Considering that this does not even include all papers, the first two authors wrote roughly 12 papers per year. I have to say that while the first author publishes on hep-ph, his paper are typically dealing with nuclear physics problems. The second author on the other hand is a full fledged phenomenologist. Also you may notice that the first ten or so authors all publish mainly on hep-ph. Just to keep it real. My on this table is 29 papers in 10 years. Kinda sad.-->
<!---->
<!---->
<!---->
<!--<!---->-->
<!--<!--{% include image.html file="rosemary10.jpg" description="Surprise" %}-->-->
<!---->
<!--[arXiv]: https://arxiv.org/-->
<!--[hep-ph]: https://arxiv.org/list/hep-ph/new-->
<!--[hep-th]: https://arxiv.org/list/hep-th/new-->
<!--[Steven Weinberg]: https://en.wikipedia.org/wiki/Steven_Weinberg-->
<!--[A Model of Leptons]: https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.19.1264-->
<!--[INSPIRE]: https://inspirehep.net/-->
<!--[screenshot]: http://inspirehep.net/dumps/inspire-dump.html-->
<!--[here]: https://twiki.cern.ch/twiki/bin/view/Inspire/DevelopmentRecordMarkup-->
<!--[GitHub]: https://github.com/dlvp/-->
<!--[CMS]: http://cms.web.cern.ch/news/what-cms-->
