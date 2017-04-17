# Final Exam - Spring 2017 
## Overview:
Yelp API to make 3 analysis about restaurant.  


### Analysis 1
-  Collecting and Store restaurant data and exploring price and rating factor to restaurant.
-  Using matlibplot to show result.

#### Process :
- 1  Apply for yelp api key and collect 7 big city main restaurants data(Chicago,Los angeles,Boston,Hoston,New York,Philadelphia,Seattle) and save them as json files.
```sh
def collectdata(offestlist):
    for num in offestlist:      
        params = {'location': 'Houston',
                   'term':'Restaurants',
          'offset':num,
         'limit':50
           }
        r=requests.get(url=url,params=params,headers=headers)
        writejson(num,r.json())
```        
- 2 Read restaurant data and write them to one csv file
 
```sh
def writecsv(x):
    with open(os.getcwd()+"/restaurant.csv", 'a') as outcsv:      
        writer=csv.writer(outcsv,delimiter=',', quotechar='|',quoting=csv.QUOTE_MINIMAL, lineterminator='\n')   
        ree=x["businesses"]
        for item in ree:
            c1=item["id"].replace(","," ")
            c2=item["name"].replace(","," ")
            c3=item["display_phone"]
            c4=item["rating"]
            c5=item["review_count"]
            if len(item["transactions"])==0:
                c6=None
            else:            
                c6=item["transactions"][0]
            c7=item["coordinates"]["latitude"]
            c8=item["coordinates"]["longitude"]
            c9=item["location"]["city"].replace(","," ")
            if item["location"]["address1"]==None:
                c10=None
            else:
                c10=item["location"]["address1"].replace(","," ")
            if item["location"]["address2"]==None:
                c11=None
            else:
                c11=item["location"]["address2"].replace(","," ")
            if item["location"]["address3"]==None:
                c12=None
            else:
                c12=item["location"]["address3"].replace(","," ")    
            c13=item["location"]["zip_code"].replace(","," ")
            c14=item["distance"]
            if len(item["categories"])>2:
                c15=item["categories"][0]["title"].replace(","," ")
                c16=item["categories"][1]["title"].replace(","," ")
                c17=item["categories"][2]["title"].replace(","," ")
            if len(item["categories"])==2:
                c15=item["categories"][0]["title"].replace(","," ")
                c16=item["categories"][1]["title"].replace(","," ")
                c17=None
            if len(item["categories"])==1:
                c15=item["categories"][0]["title"].replace(","," ")
                c16=None
                c17=None  
            if len(item["categories"])==0:
                c15=None
                c16=None
                c17=None 
            if 'price' not in item:
                c18=None
            else:
                c18=item['price']
            writer.writerow([c1,c2,c3,c4,c5,c6,c7,c8,c9,c10,c11,c12,c13,c14,c15,c16,c17,c18])
```
- 3 User pandas module to read csv file and remove duplicating restaurant information.(restaurant.csv)

```sh
df=pd.read_csv((os.getcwd()+'/restaurant.csv'),low_memory=False)
df=df.drop_duplicates()
df.head()
```

<img src="img/A1-1.png">

- 4 Quantizating price with number and Caculating average price and rating for each category of restaurant.
Replace $ with 1....
```sh
df2['price']=df2['price'].replace('$$',2)
df2['price']=df2['price'].replace('$',1)
df2['price']=df2['price'].replace('$$$',3)
df2['price']=df2['price'].replace('$$$$',4)
df2['price']=df2['price'].replace('$$$$$',5)
df2.head()
```

<img src="img/A1-2.png " width="300" height="200">

Get restaurant quantity for each category(Thai food , French food )
```sh
df3=df2.copy()
df3['Quantity']=1
dfcount=df3[["categories1","Quantity"]]
dfcount.head()
dd=dfcount.groupby(["categories1"],as_index=False).count()

```
<img src="img/A1-3.png" width="300" height="200" > 


```sh
df4=df2.groupby(["categories1"],as_index=False).mean()
df4.head()
```
<img src="img/A1-4.png" width="300" height="200">



merge two dataframe 


```sh
df_rpq=pd.merge(dd,df4,on='categories1')
df_rpq.head()

```
<img src="img/A1-5.png" width="300" height="200">

- 5 Further analyzing and selecting those restaurant categories that are more than 20 and make show with matplotlib.
Using df.query() to select dataframe

```sh
df_ByQu=df_rpq.sort_values(["Quantity"],ascending=[False])

df_result=df_ByQu.query('Quantity>20')
df_result.head()


```

<img src="img/A1-6.png" width="300" height="200">



- Restaurant Category OverView from 7 Big City
<img src="img/A1-7.png">


- Relationship between Price and Rating

<img src="img/A1-8.png">




#### Conclusion
   Most people will prefer to offer higher rating to some cheap and fast food shop.


#### Analysis 2
- 1 Analyze important Email Receiver Content and get high frequency word list and output csv.


