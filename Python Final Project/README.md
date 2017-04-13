# Final Project - Spring 2017 
### Overview : 
-  The project applys Yelp API to make three analysis.
#### Analysis 1
-  Using Yelp API to get restaurants data from New York, San Francisco, Los Angeles, Boston. 
-  Analyze and caculate the proportional relation from different categories of restaurants.(Indian food, Thai food)
-  explore the relation between restaurant category and price.($$ or$$$)


#### Process :
- 1 Parse all Emails from raw content.
```sh
def readmailto(path):                                     #mail to
    with open(path, 'r',encoding='utf-8',errors='ignore') as exa:
        content = exa.read()
        email=Parser().parsestr(content)        
        return(email["to"])
```        
- 2 Write function to get all files in folder.
 - use os.walk(path) method to get all files in root directory(root directory contains some child directory )
```sh
fns=[os.path.join(root,fn) for root,dirs,files in os.walk(path) for fn in files if fn.endswith(".")]
print(len(fns))
```
- 3 Caculate receiver Email quantities and sort it by rank.
- output csv files

- 4 Choose top receiver to make further analysis
```
| Receiver                    |   Quantity | 
| --------------------------- |:----------:| 
| richard.shapiro@enron.com   | 15149      | 
| jeff.dasovich@enron.com     | 14207      |  
| tana.jones@enron.com        | 12829      |   
| steven.kean@enron.com       | 12756      |  
| sara.shackleton@enron.com   | 10341      |  
```

#### Analysis 2
- 1 Analyze important Email Receiver Content and get high frequency word list and output csv.
- 2 Analyze Email distribution per month and output Graphic.

#### Process :
- 1 Read Email content and get all clean words.

```
vipfilenamelist=["shapiro-r","dasovich-j","jones-t","kean-s","shackleton-s"]

def cleanword(rawcontent):
    wordlist=[i for i in word_tokenize(rawcontent.lower()) if i not in stopwords.words('english') and i not in string.punctuation and i.isalpha()]
    return wordlist
```
- 2 List all high frequency word and output CSV
```
[('enron', 13252),
 ('said', 5871),
 ('company', 5641),
 ('power', 4329),
 ('message', 4060),
 ('would', 3980),
 ('energy', 3936),
 ('sent', 3737),
 ('subject', 3715),
 ('please', 3484),
 ('new', 3446),
 ('http', 3342),
 ('gas', 2736),
 ('jones', 2524),
 ('may', 2396),
 ('pm', 2391),
 ('td', 2276),
 ('business', 2257),...]
```
- 2 Caculate Email Distribution per Month.
```sh
def mailquantityofdate(path):                       #mail date
    with open(path, 'r',encoding='utf-8',errors='ignore') as exa:
        content=exa.read()
        email=Parser().parsestr(content)
        t=parsedate_tz(email["date"])
        return (str(t[0])+"-"+str(t[1])) 
emailQuantperMonth=[]
for path in vippath(vipfilenamelist):
    allfiles=getallfile(path)    
    for f in allfiles:
        emailQuantperMonth.append(mailquantityofdate(f))        
```
- it will get Email distribution
```
FreqDist({'2001-10': 1263,
          '2001-11': 937,
          '2001-12': 380,
          '2001-7': 10,
          '2001-8': 6,
          '2001-9': 593,
          '2002-1': 129,
          '2002-2': 101,
          '2002-3': 107})

```
- 3 use matplotlib to make graphic 


#### Analysis 3

- 1 Analyze hot and important words distribution per half month and output Graphic.

#### Process :
- 1 Choose some hot topic words from Analysis 2 high frequency word list.
```
{enron, gas, price, power,company,energy,trading,service,credit}
```
- time range: first means first half of month, second means second half of month
```
["2001-7-first","2001-7-second","2001-8-first","2001-8-second","2001-9-first","2001-9-second","2001-10-first","2001-10-second","2001-11-first","2001-11-second","2001-12-first","2001-12-second","2002-1-first","2002-1-second","2002-2-first","2002-2-second","2002-3-first","2002-3-second"]

```
- 2 Get wordlist per half month

- split a month to two parts
```sh
def returnmaildate(path):                       #  mail date        
    with open(path, 'r',encoding='utf-8',errors='ignore') as exa:
        content=exa.read()
        email=Parser().parsestr(content)
        t=parsedate_tz(email["date"])
        if t[2]<16:
            q="first"
        else:
            q="second"
        return (str(t[0])+"-"+str(t[1])+"-"+q) 

def getallemailwordslist(path,date):
    wordlist=[]
    filespath=getallfile(path)
    for eachfilepath in filespath:
        if returnmaildate(eachfilepath)==date:         
            words=cleanword(getemailcontent(eachfilepath))
            wordlist.extend(words)
    return wordlist 

def getwordpermonth(date):
    wordbase=[]
    for path in vippath(vipfilenamelist):
        wordbase.extend(getallemailwordslist(path,date))
    return wordbase  
        
```
- 3 output word frequency about important words
```
{'2001-10-first': 5450,
 '2001-10-second': 10764,
 '2001-11-first': 8047,
 '2001-11-second': 2431,
 '2001-12-first': 311,
 '2001-12-second': 581,
 '2001-7-first': 0,
 '2001-7-second': 589,
 '2001-8-first': 5,
 '2001-8-second': 218,
 '2001-9-first': 3248,
 '2001-9-second': 3911,
 '2002-1-first': 89,
 '2002-1-second': 279,
 '2002-2-first': 168,
 '2002-2-second': 139,
 '2002-3-first': 179,
 '2002-3-second': 109}
```
---
### Question 2 : 

#### Analysis 1  with BOOK API
- Analyze best seller book list in New York Times(Combined Print and E-Book Fiction list)


#### Process
- 1 write function to collect book information data according date
```
def getbooklist(datelist,booktype):
    for pubdate in datelist:
        r = requests.get("https://api.nytimes.com/svc/books/v3/lists/"+pubdate+"/"+booktype+".json?&api-key="+os.getenv('auth_key'))
        writejson(pubdate,booktype,r.json()) 
```

- 2 Analyze book author and title to caculate their times on best seller list.
```
('GONE GIRL', 'Gillian Flynn', 131),
 ('THE GIRL ON THE TRAIN', 'Paula Hawkins', 107),
 ('ALL THE LIGHT WE CANNOT SEE', 'Anthony Doerr', 96),
 ('FIFTY SHADES OF GREY', 'E L James', 93),
 ('THE NIGHTINGALE', 'Kristin Hannah', 76),
 ('FIFTY SHADES DARKER', 'E L James', 74),
 ('A GAME OF THRONES', 'George R R Martin', 68),
 ('FIFTY SHADES FREED', 'E L James', 65),
 ('THE HELP', 'Kathryn Stockett', 60),
 ("THE HUSBAND'S SECRET", 'Liane Moriarty', 60),
 ('ORPHAN TRAIN', 'Christina Baker Kline', 54),
 ('THE GOLDFINCH', 'Donna Tartt', 52),
 ('THE MARTIAN', 'Andy Weir', 50),
 ('THE GIRL WHO KICKED THE HORNET’S NEST', 'Stieg Larsson', 45),
 ('A MAN CALLED OVE', 'Fredrik Backman', 44),
 ('WATER FOR ELEPHANTS', 'Sara Gruen', 41),
```
- 3 output csv


#### Analysis 2  with BOOK API
- Analyze the most popular publishers seller book list (Combined Print and E-Book Unfiction list)


#### Process
- 1 Get unfiction book information (collected in Analysis 1)

- 2 Get all titles and author information from top 250 book in unfiction book list from 2011 to 2017
```
[('A HIGHER CALL', 'Adam Makos with Larry Alexander'),
 ('ORIGINALS', 'Adam Grant'),
 ('THE GENE', 'Siddhartha Mukherjee'),
 ('THE UNWINDING', 'George Packer'),
 ('TRIBE', 'Sebastian Junger'),
 ('KEEP IT PITHY', "Bill O'Reilly"),
 ('THOSE GUYS HAVE ALL THE FUN', 'James Andrew Miller and Tom Shales'),
 ('RED', 'Sammy Hagar with Joel Selvin'),
 ('CLINTON CASH', 'Peter Schweizer'),
 ('BACK TO WORK', 'Bill Clinton'),
 ("STILL FOOLIN' 'EM", 'Billy Crystal'),
 ('IN THE HEART OF THE SEA', 'Nathaniel Philbrick'),
 ('OUR REVOLUTION', 'Bernie Sanders'),
 ('THE LOST CITY OF THE MONKEY GOD', 'Douglas Preston'),
 ('THE DAILY SHOW (THE BOOK)', 'Chris Smith'),
 ('THE IMMORTAL IRISHMAN', 'Timothy Egan'),
 ('TALKING AS FAST AS I CAN', 'Lauren Graham'),
 ('DREAMERS AND DECEIVERS', 'Glenn Beck with Kevin Balfe'),
 ("OBAMA'S AMERICA", "Dinesh D'Souza"),
 ('DOUBLE CROSS', 'Ben Macintyre'),
 ('BUTLER', 'Wil Haygood'),
 ('SULLY', 'Chesley B Sullenberger III with Jeffrey Zaslow'),
 ('LIGHTS OUT', 'Ted Koppel'),
 ('VALIANT AMBITION', 'Nathaniel Philbrick'),
 ('A SPY AMONG FRIENDS', 'Ben Macintyre'),
 ('HOW TO BE A WOMAN', 'Caitlin Moran'),
 ('MRS. KENNEDY AND ME', 'Clint Hill'),
 ('COWARDS', 'Glenn Beck and Kevin Balfe'),
 ('IT IS ABOUT ISLAM', 'Glenn Beck'),
 ('THE PRICE OF POLITICS', 'Bob Woodward'),
 ('THE BILLION DOLLAR SPY', 'David E Hoffman'),
 ('A MORE PERFECT UNION', 'Ben Carson with Candy Carson'),
 ('BINGE', 'Tyler Oakley'),
 ('PHYSICS OF THE FUTURE', 'Michio Kaku'),
 ('SPQR', 'Mary Beard'),.......]


```
- 3 Use GET /lists/best-sellers/history.json to collect publishers information of top 250 best seller books
```sh
def getbookinfo(title,author):
    params={'api-key':os.getenv('auth_key'),'author':author,'title':title}
    r = requests.get("https://api.nytimes.com/svc/books/v3/lists/best-sellers/"+title+"-"+author+".json",params=params)
    writejson(title,author,r.json())
```
- 4 Clean data and analyze frequency of publishers to ensure the most popular publishers.

```sh
cleandata=[]
for pub in allpublisher:
    pub=pub.replace("Random House Publishing","Random House")
    pub=pub.replace("Publishers","")
    pub=pub.replace("House ","House")
    pub=pub.replace("St. Martin’s","St. Martin's")
    pub=pub.replace(","," ")
    pub=pub.replace("Editions","")
    pub=pub.replace("Threshold ","Threshold")
    pub=pub.replace("Little, Brown","Little Brown")
    pub=pub.replace("HarperCollins","HarperCollins ")
    pub=pub.replace("HarperCollins","HarperCollins ")
    #Threshold Editions/Mercury Radio Arts------>Threshold Editions and Mercury Radio Arts
    if "/" in pub:
        for i in pub.split("/"):
            cleandata.append(i)
    else:
        cleandata.append(pub)
```
```
('Random House', 20),
 ('Simon & Schuster', 15),
 ('Little  Brown', 14),
 ('HarperCollins ', 12),
 ('Holt', 11),
 ('Threshold', 11),
 ('Morrow', 9),
 ('Back Bay', 8),
 ('Penguin', 7),
 ('Broadway', 7),
 ('Grand Central', 6),
 ('Scribner', 6),
 ('Mercury Radio Arts', 6),
 ('Anchor', 6),
 ('Regnery', 6),
 ('Vintage', 6),
 ("St. Martin's", 5),
 ('Norton', 5),
 ('Houghton Mifflin Harcourt', 4),
 ('Farrar  Straus & Giroux', 4),
 ('Knopf', 4),
 ('Doubleday', 4),
 ('Gallery Books', 4),
 ('Spiegel & Grau', 4),
 ('Twelve', 3),
 ('Sentinel', 3),
 ('Viking', 3),
 ('Harper', 3),
 ('Crown Archetype', 3),
 ('Dey Street', 2),
 ('Flatiron', 2),.....]



```

- 5 Output CSV

#### Analysis 3  with Archive API
- Analyze article type distribution from 2014 to 2017

#### Process
- 1 Write requests to collect archive data
```sh
def getjson(startmonth,startyear,endmonth,endyear):
    for date in months(startmonth,startyear,endmonth,endyear):
        r = requests.get("https://api.nytimes.com/svc/archive/v1/"+str(date[1])+"/"+str(date[0])+".json?&api-key="+os.getenv('auth_key'))
        writejson(date[1],date[0],r.json())  


```

- 2 Analyze article type proportion
```sh
typedistrbution=[]
for eachtype in articletype:
    typedistrbution.append((eachtype[0],eachtype[1]/len(typelist)))
    
    
typedistrbution  
```

```
('News', 0.4880685511671427),
 ('Blog', 0.17659804983748645),
 ('Review', 0.055333398995370826),
 ('Brief', 0.04230473751600512),
 ('Op-Ed', 0.03611149413966316),
 ('Video', 0.03540628385698808),
 ('Slideshow', 0.03221116911257756),
 ('Interactive Feature', 0.024816310450113267),
 ('Letter', 0.021061755146262188),
 ('Paid Death Notice', 0.01882005318625037),
 ('Editorial', 0.014967004826159756),
 ('Schedule', 0.012441642864178076),
 ('Obituary', 0.010849995075347188),
 ('Question', 0.007430316162710529),
 ('List', 0.004574017531764011),
 ('Quote', 0.003912144193834335),
 ('briefing', 0.003723037525854427),


```
- 3 output csv
