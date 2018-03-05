## Weather Analysis:
- According to the scatter plot of "City Latitude vs. Temperature", you can tell that latitude is one of the main factors affecting the temperature. The closer to the equator (which latitude = 0), the higher of the temperature. The closer to the poles, the lower of the temperature. 
- It doesn't seem like there is correlation between percent of humidity and city latitude. It seems like the closer to the equator, the dots of humidity are closer to 100%.
- It seems like there is no correlation between percent of cloudiness vs. city latitude, or wind speed vs. city latitude. Mostly the range of wind speed is 0-10 (mph).

```python
import requests
import json
from pprint import pprint
from config import api_key
import random
from citipy import citipy
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

```


```python
df = pd.DataFrame()
lat = []
lng = []
for x in range(0,1700): #get 700 random numbers
    lat.append(random.randint(-90,90)) #random number from range -90 to 90
    lng.append(random.randint(-180,180)) #random number from range -180 to 180
df["Lat"] = lat
df["Lng"] = lng
df["City Name"] = ''
df["Country"] = ''
df.head()
df.count()
   
```




    Lat          1700
    Lng          1700
    City Name    1700
    Country      1700
    dtype: int64




```python
for index,row in df.iterrows():
    city = citipy.nearest_city(row["Lat"],row["Lng"])
    if " " in city.city_name:
        city.city_name = city.city_name.replace(" ", "%20") 
    df.set_value(index,"City Name",city.city_name)
    df.set_value(index,"Country",city.country_code)
    df["Temp"]=''
df = df.drop_duplicates(["City Name","Country"])#drop the duplicate values
df["Humidity"]=''
df["Cloudiness"]=''
df["Wind Speed"]=''
df.head()
df.count()
```




    Lat           641
    Lng           641
    City Name     641
    Country       641
    Temp          641
    Humidity      641
    Cloudiness    641
    Wind Speed    641
    dtype: int64




```python
cities = df["City Name"].tolist()
settings = {"units":"imperial","appid":api_key}
responses=[]
query = ["main.temp"]
urls = []
print("Beginning Data Retrieval:")
print("-----------------------------")
record = 1
for index,row in df.iterrows():
    url = f"http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID={api_key}&q={row['City Name']},{row['Country']}"
    print(f"Processing record {record}: {row['City Name']}, {row['Country']}")
    print(url)
    responses = requests.get(url).json()
    record += 1
    try:
        df.set_value(index,"Lat",responses["coord"]["lat"])
        df.set_value(index,"Lng",responses["coord"]["lon"])
        df.set_value(index,"Temp",responses["main"]["temp"])
        df.set_value(index,"Humidity",responses["main"]["humidity"])
        df.set_value(index,"Cloudiness",responses["clouds"]["all"])
        df.set_value(index,"Wind Speed",responses["wind"]["speed"])
        df.set_value(index, "City Name",responses["name"])
    except KeyError:
        df.set_value(index,"Temp",np.nan)


df.head()

        

    
  
```

    Beginning Data Retrieval:
    -----------------------------
    Processing record 1: East London, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=East London,za
    Processing record 2: Hermanus, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=Hermanus,za
    Processing record 3: Luderitz, na
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=Luderitz,na
    Processing record 4: Carlsbad, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=Carlsbad,us
    Processing record 5: Le Port, re
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=Le Port,re
    Processing record 6: barentsburg, sj
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=barentsburg,sj
    Processing record 7: Kavieng, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=Kavieng,pg
    Processing record 8: promakhoi, gr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=promakhoi,gr
    Processing record 9: rikitea, pf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rikitea,pf
    Processing record 10: te%20anau, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=te%20anau,nz
    Processing record 11: carnarvon, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=carnarvon,au
    Processing record 12: yellowknife, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yellowknife,ca
    Processing record 13: kapaa, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kapaa,us
    Processing record 14: poso, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=poso,id
    Processing record 15: mosquera, co
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mosquera,co
    Processing record 16: khatanga, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=khatanga,ru
    Processing record 17: kununurra, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kununurra,au
    Processing record 18: khandbari, np
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=khandbari,np
    Processing record 19: shimoda, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=shimoda,jp
    Processing record 20: san%20luis, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20luis,ar
    Processing record 21: albany, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=albany,au
    Processing record 22: ushuaia, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ushuaia,ar
    Processing record 23: jamestown, sh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jamestown,sh
    Processing record 24: katsuura, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=katsuura,jp
    Processing record 25: skibbereen, ie
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=skibbereen,ie
    Processing record 26: alofi, nu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=alofi,nu
    Processing record 27: georgetown, sh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=georgetown,sh
    Processing record 28: puerto%20ayora, ec
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=puerto%20ayora,ec
    Processing record 29: taolanaro, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=taolanaro,mg
    Processing record 30: sentyabrskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sentyabrskiy,ru
    Processing record 31: vaitupu, wf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vaitupu,wf
    Processing record 32: faanui, pf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=faanui,pf
    Processing record 33: sitka, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sitka,us
    Processing record 34: barrow, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=barrow,us
    Processing record 35: karratha, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=karratha,au
    Processing record 36: mataura, pf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mataura,pf
    Processing record 37: hualmay, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hualmay,pe
    Processing record 38: auki, sb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=auki,sb
    Processing record 39: vila%20franca%20do%20campo, pt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vila%20franca%20do%20campo,pt
    Processing record 40: lagoa, pt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lagoa,pt
    Processing record 41: belushya%20guba, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=belushya%20guba,ru
    Processing record 42: kismayo, so
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kismayo,so
    Processing record 43: butaritari, ki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=butaritari,ki
    Processing record 44: utiroa, ki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=utiroa,ki
    Processing record 45: hobart, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hobart,au
    Processing record 46: dunedin, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dunedin,nz
    Processing record 47: malakal, sd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=malakal,sd
    Processing record 48: bredasdorp, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bredasdorp,za
    Processing record 49: oktyabrskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=oktyabrskiy,ru
    Processing record 50: grindavik, is
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=grindavik,is
    Processing record 51: busselton, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=busselton,au
    Processing record 52: palabuhanratu, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=palabuhanratu,id
    Processing record 53: iqaluit, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=iqaluit,ca
    Processing record 54: gainesville, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gainesville,us
    Processing record 55: bluff, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bluff,nz
    Processing record 56: tasiilaq, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tasiilaq,gl
    Processing record 57: dongobesh, tz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dongobesh,tz
    Processing record 58: wyndham, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=wyndham,nz
    Processing record 59: thompson, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=thompson,ca
    Processing record 60: opuwo, na
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=opuwo,na
    Processing record 61: pangnirtung, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pangnirtung,ca
    Processing record 62: alta%20floresta, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=alta%20floresta,br
    Processing record 63: punta%20arenas, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=punta%20arenas,cl
    Processing record 64: charlottetown, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=charlottetown,ca
    Processing record 65: moron, mn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=moron,mn
    Processing record 66: pacific%20grove, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pacific%20grove,us
    Processing record 67: nanortalik, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nanortalik,gl
    Processing record 68: san%20cristobal, ec
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20cristobal,ec
    Processing record 69: ponta%20do%20sol, cv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ponta%20do%20sol,cv
    Processing record 70: victoria, sc
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=victoria,sc
    Processing record 71: kem, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kem,ru
    Processing record 72: hithadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hithadhoo,mv
    Processing record 73: kawana%20waters, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kawana%20waters,au
    Processing record 74: coquimbo, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=coquimbo,cl
    Processing record 75: petropavlovsk-kamchatskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=petropavlovsk-kamchatskiy,ru
    Processing record 76: khanu%20woralaksaburi, th
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=khanu%20woralaksaburi,th
    Processing record 77: tumannyy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tumannyy,ru
    Processing record 78: tiznit, ma
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tiznit,ma
    Processing record 79: lewisporte, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lewisporte,ca
    Processing record 80: tunduru, tz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tunduru,tz
    Processing record 81: port%20alfred, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20alfred,za
    Processing record 82: vaini, to
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vaini,to
    Processing record 83: necochea, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=necochea,ar
    Processing record 84: bambanglipuro, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bambanglipuro,id
    Processing record 85: cape%20town, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cape%20town,za
    Processing record 86: amderma, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=amderma,ru
    Processing record 87: chokurdakh, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=chokurdakh,ru
    Processing record 88: babanusah, sd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=babanusah,sd
    Processing record 89: kaya, bf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kaya,bf
    Processing record 90: half%20moon%20bay, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=half%20moon%20bay,us
    Processing record 91: mau, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mau,in
    Processing record 92: mar%20del%20plata, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mar%20del%20plata,ar
    Processing record 93: atbasar, kz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=atbasar,kz
    Processing record 94: ostrovnoy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ostrovnoy,ru
    Processing record 95: pallikonda, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pallikonda,in
    Processing record 96: port%20lincoln, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20lincoln,au
    Processing record 97: new%20norfolk, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=new%20norfolk,au
    Processing record 98: castro, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=castro,cl
    Processing record 99: ribeira%20grande, pt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ribeira%20grande,pt
    Processing record 100: dikson, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dikson,ru
    Processing record 101: illoqqortoormiut, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=illoqqortoormiut,gl
    Processing record 102: attawapiskat, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=attawapiskat,ca
    Processing record 103: souillac, mu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=souillac,mu
    Processing record 104: marcona, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=marcona,pe
    Processing record 105: saint%20george, bm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint%20george,bm
    Processing record 106: saint-philippe, re
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint-philippe,re
    Processing record 107: bengkulu, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bengkulu,id
    Processing record 108: mahebourg, mu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mahebourg,mu
    Processing record 109: carambei, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=carambei,br
    Processing record 110: ostersund, se
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ostersund,se
    Processing record 111: murmansk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=murmansk,ru
    Processing record 112: upernavik, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=upernavik,gl
    Processing record 113: hamilton, bm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hamilton,bm
    Processing record 114: zhigansk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=zhigansk,ru
    Processing record 115: hovd, mn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hovd,mn
    Processing record 116: mocuba, mz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mocuba,mz
    Processing record 117: amudat, ug
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=amudat,ug
    Processing record 118: springdale, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=springdale,ca
    Processing record 119: qaqortoq, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=qaqortoq,gl
    Processing record 120: portland, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=portland,au
    Processing record 121: mys%20shmidta, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mys%20shmidta,ru
    Processing record 122: avarua, ck
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=avarua,ck
    Processing record 123: chuy, uy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=chuy,uy
    Processing record 124: tecoanapa, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tecoanapa,mx
    Processing record 125: henties%20bay, na
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=henties%20bay,na
    Processing record 126: talnakh, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=talnakh,ru
    Processing record 127: fortuna, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=fortuna,us
    Processing record 128: lebyazhye, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lebyazhye,ru
    Processing record 129: atuona, pf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=atuona,pf
    Processing record 130: gerardmer, fr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gerardmer,fr
    Processing record 131: tougan, bf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tougan,bf
    Processing record 132: berlevag, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=berlevag,no
    Processing record 133: gualaco, hn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gualaco,hn
    Processing record 134: bealanana, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bealanana,mg
    Processing record 135: lolua, tv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lolua,tv
    Processing record 136: hilo, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hilo,us
    Processing record 137: pevek, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pevek,ru
    Processing record 138: lazaro%20cardenas, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lazaro%20cardenas,mx
    Processing record 139: nipawin, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nipawin,ca
    Processing record 140: karpathos, gr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=karpathos,gr
    Processing record 141: lebu, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lebu,cl
    Processing record 142: turgoyak, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=turgoyak,ru
    Processing record 143: tazovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tazovskiy,ru
    Processing record 144: uvat, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=uvat,ru
    Processing record 145: los%20llanos%20de%20aridane, es
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=los%20llanos%20de%20aridane,es
    Processing record 146: esperance, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=esperance,au
    Processing record 147: sao%20filipe, cv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sao%20filipe,cv
    Processing record 148: dicabisagan, ph
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dicabisagan,ph
    Processing record 149: maceio, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=maceio,br
    Processing record 150: aktash, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=aktash,ru
    Processing record 151: vardo, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vardo,no
    Processing record 152: mtwara, tz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mtwara,tz
    Processing record 153: puerto%20suarez, bo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=puerto%20suarez,bo
    Processing record 154: sobolevo, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sobolevo,ru
    Processing record 155: jaramana, sy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jaramana,sy
    Processing record 156: arraial%20do%20cabo, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=arraial%20do%20cabo,br
    Processing record 157: homer, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=homer,us
    Processing record 158: sharjah, ae
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sharjah,ae
    Processing record 159: satitoa, ws
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=satitoa,ws
    Processing record 160: aklavik, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=aklavik,ca
    Processing record 161: rocha, uy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rocha,uy
    Processing record 162: yar-sale, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yar-sale,ru
    Processing record 163: namibe, ao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=namibe,ao
    Processing record 164: pangai, to
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pangai,to
    Processing record 165: tambacounda, sn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tambacounda,sn
    Processing record 166: abu%20jubayhah, sd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=abu%20jubayhah,sd
    Processing record 167: severo-kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=severo-kurilsk,ru
    Processing record 168: tuktoyaktuk, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tuktoyaktuk,ca
    Processing record 169: hambantota, lk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hambantota,lk
    Processing record 170: sa%20kaeo, th
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sa%20kaeo,th
    Processing record 171: taveta, ke
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=taveta,ke
    Processing record 172: vanavara, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vanavara,ru
    Processing record 173: eyl, so
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=eyl,so
    Processing record 174: toliary, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=toliary,mg
    Processing record 175: berga, es
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=berga,es
    Processing record 176: ahipara, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ahipara,nz
    Processing record 177: port%20hedland, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20hedland,au
    Processing record 178: muros, es
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=muros,es
    Processing record 179: klaksvik, fo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=klaksvik,fo
    Processing record 180: talcahuano, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=talcahuano,cl
    Processing record 181: broome, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=broome,au
    Processing record 182: meulaboh, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=meulaboh,id
    Processing record 183: paita, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=paita,pe
    Processing record 184: longyearbyen, sj
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=longyearbyen,sj
    Processing record 185: laguna, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=laguna,br
    Processing record 186: qaanaaq, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=qaanaaq,gl
    Processing record 187: port%20augusta, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20augusta,au
    Processing record 188: constitucion, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=constitucion,mx
    Processing record 189: bambous%20virieux, mu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bambous%20virieux,mu
    Processing record 190: yerbogachen, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yerbogachen,ru
    Processing record 191: lagunas, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lagunas,pe
    Processing record 192: lavrentiya, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lavrentiya,ru
    Processing record 193: san%20benito, ph
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20benito,ph
    Processing record 194: patacamaya, bo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=patacamaya,bo
    Processing record 195: yumen, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yumen,cn
    Processing record 196: cayenne, gf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cayenne,gf
    Processing record 197: lorengau, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lorengau,pg
    Processing record 198: svetlyy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=svetlyy,ru
    Processing record 199: coihaique, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=coihaique,cl
    Processing record 200: gusau, ng
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gusau,ng
    Processing record 201: airai, pw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=airai,pw
    Processing record 202: kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kurilsk,ru
    Processing record 203: tsihombe, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tsihombe,mg
    Processing record 204: quang%20ngai, vn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=quang%20ngai,vn
    Processing record 205: toamasina, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=toamasina,mg
    Processing record 206: novoleushkovskaya, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=novoleushkovskaya,ru
    Processing record 207: hofn, is
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hofn,is
    Processing record 208: tahta, eg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tahta,eg
    Processing record 209: comodoro%20rivadavia, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=comodoro%20rivadavia,ar
    Processing record 210: arman, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=arman,ru
    Processing record 211: port%20elizabeth, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20elizabeth,za
    Processing record 212: kaitangata, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kaitangata,nz
    Processing record 213: tutoia, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tutoia,br
    Processing record 214: filingue, ne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=filingue,ne
    Processing record 215: namtsy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=namtsy,ru
    Processing record 216: kodiak, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kodiak,us
    Processing record 217: port-gentil, ga
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port-gentil,ga
    Processing record 218: druskininkai, lt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=druskininkai,lt
    Processing record 219: emerald, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=emerald,au
    Processing record 220: mastic%20beach, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mastic%20beach,us
    Processing record 221: perelyub, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=perelyub,ru
    Processing record 222: bathsheba, bb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bathsheba,bb
    Processing record 223: hesla, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hesla,in
    Processing record 224: jadu, ly
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jadu,ly
    Processing record 225: doha, kw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=doha,kw
    Processing record 226: anuchino, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=anuchino,ru
    Processing record 227: umm%20kaddadah, sd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=umm%20kaddadah,sd
    Processing record 228: haines%20junction, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=haines%20junction,ca
    Processing record 229: thanh%20hoa, vn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=thanh%20hoa,vn
    Processing record 230: azanka, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=azanka,ru
    Processing record 231: mgandu, tz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mgandu,tz
    Processing record 232: narrabri, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=narrabri,au
    Processing record 233: shakiso, et
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=shakiso,et
    Processing record 234: bodden%20town, ky
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bodden%20town,ky
    Processing record 235: puerto%20colombia, co
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=puerto%20colombia,co
    Processing record 236: batagay, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=batagay,ru
    Processing record 237: turbat, pk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=turbat,pk
    Processing record 238: port%20blair, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20blair,in
    Processing record 239: dwarka, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dwarka,in
    Processing record 240: vanimo, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vanimo,pg
    Processing record 241: palmer, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=palmer,us
    Processing record 242: vyartsilya, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vyartsilya,ru
    Processing record 243: najran, sa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=najran,sa
    Processing record 244: bethel, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bethel,us
    Processing record 245: seymchan, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=seymchan,ru
    Processing record 246: goderich, sl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=goderich,sl
    Processing record 247: hlotse, ls
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hlotse,ls
    Processing record 248: bilibino, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bilibino,ru
    Processing record 249: saint-georges, gf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint-georges,gf
    Processing record 250: mareeba, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mareeba,au
    Processing record 251: biak, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=biak,id
    Processing record 252: nikolskoye, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nikolskoye,ru
    Processing record 253: fare, pf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=fare,pf
    Processing record 254: cherskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cherskiy,ru
    Processing record 255: tiksi, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tiksi,ru
    Processing record 256: sucua, ec
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sucua,ec
    Processing record 257: tieli, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tieli,cn
    Processing record 258: vostok, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vostok,ru
    Processing record 259: chiredzi, zw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=chiredzi,zw
    Processing record 260: saint%20anthony, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint%20anthony,ca
    Processing record 261: elk%20city, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=elk%20city,us
    Processing record 262: matay, eg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=matay,eg
    Processing record 263: nuuk, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nuuk,gl
    Processing record 264: raga, sd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=raga,sd
    Processing record 265: bijie, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bijie,cn
    Processing record 266: zonguldak, tr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=zonguldak,tr
    Processing record 267: yian, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yian,cn
    Processing record 268: cidreira, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cidreira,br
    Processing record 269: raudeberg, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=raudeberg,no
    Processing record 270: mokhsogollokh, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mokhsogollokh,ru
    Processing record 271: monrovia, lr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=monrovia,lr
    Processing record 272: uspenka, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=uspenka,ru
    Processing record 273: crateus, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=crateus,br
    Processing record 274: erzin, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=erzin,ru
    Processing record 275: teya, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=teya,ru
    Processing record 276: nemuro, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nemuro,jp
    Processing record 277: tshela, cd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tshela,cd
    Processing record 278: nishihara, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nishihara,jp
    Processing record 279: hihifo, to
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hihifo,to
    Processing record 280: saskylakh, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saskylakh,ru
    Processing record 281: oyama, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=oyama,jp
    Processing record 282: kamaishi, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kamaishi,jp
    Processing record 283: ilulissat, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ilulissat,gl
    Processing record 284: pathein, mm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pathein,mm
    Processing record 285: kayes, ml
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kayes,ml
    Processing record 286: vestmanna, fo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vestmanna,fo
    Processing record 287: thinadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=thinadhoo,mv
    Processing record 288: san%20patricio, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20patricio,mx
    Processing record 289: kushmurun, kz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kushmurun,kz
    Processing record 290: tual, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tual,id
    Processing record 291: tuatapere, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tuatapere,nz
    Processing record 292: sur, om
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sur,om
    Processing record 293: hami, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hami,cn
    Processing record 294: buala, sb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=buala,sb
    Processing record 295: geraldton, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=geraldton,ca
    Processing record 296: pisco, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pisco,pe
    Processing record 297: banyo, cm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=banyo,cm
    Processing record 298: doctor%20pedro%20p.%20pena, py
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=doctor%20pedro%20p.%20pena,py
    Processing record 299: provideniya, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=provideniya,ru
    Processing record 300: kalakamati, bw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kalakamati,bw
    Processing record 301: carutapera, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=carutapera,br
    Processing record 302: linden, gy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=linden,gy
    Processing record 303: sabang, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sabang,id
    Processing record 304: kavaratti, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kavaratti,in
    Processing record 305: thai%20binh, vn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=thai%20binh,vn
    Processing record 306: saint-joseph, re
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint-joseph,re
    Processing record 307: isangel, vu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=isangel,vu
    Processing record 308: norman%20wells, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=norman%20wells,ca
    Processing record 309: san%20carlos%20de%20bariloche, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20carlos%20de%20bariloche,ar
    Processing record 310: bilma, ne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bilma,ne
    Processing record 311: moose%20factory, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=moose%20factory,ca
    Processing record 312: shimanovsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=shimanovsk,ru
    Processing record 313: giaveno, it
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=giaveno,it
    Processing record 314: iguape, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=iguape,br
    Processing record 315: codrington, ag
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=codrington,ag
    Processing record 316: newport, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=newport,us
    Processing record 317: togur, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=togur,ru
    Processing record 318: saldanha, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saldanha,za
    Processing record 319: charters%20towers, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=charters%20towers,au
    Processing record 320: tafresh, ir
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tafresh,ir
    Processing record 321: naze, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=naze,jp
    Processing record 322: saleaula, ws
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saleaula,ws
    Processing record 323: byron%20bay, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=byron%20bay,au
    Processing record 324: itarema, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=itarema,br
    Processing record 325: cabedelo, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cabedelo,br
    Processing record 326: boueni, yt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=boueni,yt
    Processing record 327: mount%20gambier, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mount%20gambier,au
    Processing record 328: akdepe, tm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=akdepe,tm
    Processing record 329: ancud, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ancud,cl
    Processing record 330: robertsport, lr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=robertsport,lr
    Processing record 331: nizhneyansk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nizhneyansk,ru
    Processing record 332: ciudad%20guayana, ve
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ciudad%20guayana,ve
    Processing record 333: riyadh, sa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=riyadh,sa
    Processing record 334: novobirilyussy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=novobirilyussy,ru
    Processing record 335: puerto%20ayacucho, ve
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=puerto%20ayacucho,ve
    Processing record 336: kathu, th
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kathu,th
    Processing record 337: igarape-miri, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=igarape-miri,br
    Processing record 338: jizan, sa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jizan,sa
    Processing record 339: sola, vu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sola,vu
    Processing record 340: kruisfontein, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kruisfontein,za
    Processing record 341: nam%20tha, la
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nam%20tha,la
    Processing record 342: mudon, mm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mudon,mm
    Processing record 343: paka, my
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=paka,my
    Processing record 344: lufilufi, ws
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lufilufi,ws
    Processing record 345: agadez, ne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=agadez,ne
    Processing record 346: athens, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=athens,us
    Processing record 347: nalvo, ph
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nalvo,ph
    Processing record 348: shangzhi, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=shangzhi,cn
    Processing record 349: severo-yeniseyskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=severo-yeniseyskiy,ru
    Processing record 350: yulara, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yulara,au
    Processing record 351: geraldton, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=geraldton,au
    Processing record 352: ossora, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ossora,ru
    Processing record 353: deer%20lake, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=deer%20lake,ca
    Processing record 354: vestmannaeyjar, is
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vestmannaeyjar,is
    Processing record 355: quatre%20cocos, mu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=quatre%20cocos,mu
    Processing record 356: yima, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yima,cn
    Processing record 357: bowen, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bowen,au
    Processing record 358: matara, lk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=matara,lk
    Processing record 359: thaba-tseka, ls
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=thaba-tseka,ls
    Processing record 360: haibowan, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=haibowan,cn
    Processing record 361: rawson, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rawson,ar
    Processing record 362: nikki, bj
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nikki,bj
    Processing record 363: taoudenni, ml
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=taoudenni,ml
    Processing record 364: praia%20da%20vitoria, pt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=praia%20da%20vitoria,pt
    Processing record 365: poltavka, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=poltavka,ru
    Processing record 366: aksu, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=aksu,cn
    Processing record 367: roma, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=roma,au
    Processing record 368: faya, td
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=faya,td
    Processing record 369: skalistyy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=skalistyy,ru
    Processing record 370: chitrakonda, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=chitrakonda,in
    Processing record 371: margate, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=margate,za
    Processing record 372: lata, sb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lata,sb
    Processing record 373: louisbourg, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=louisbourg,ca
    Processing record 374: teguise, es
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=teguise,es
    Processing record 375: mathathane, bw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mathathane,bw
    Processing record 376: craig, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=craig,us
    Processing record 377: colinas, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=colinas,br
    Processing record 378: komsomolskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=komsomolskiy,ru
    Processing record 379: walvis%20bay, na
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=walvis%20bay,na
    Processing record 380: narsaq, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=narsaq,gl
    Processing record 381: quelimane, mz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=quelimane,mz
    Processing record 382: amahai, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=amahai,id
    Processing record 383: takoradi, gh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=takoradi,gh
    Processing record 384: zipaquira, co
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=zipaquira,co
    Processing record 385: cockburn%20town, bs
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cockburn%20town,bs
    Processing record 386: veraval, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=veraval,in
    Processing record 387: jimenez, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jimenez,mx
    Processing record 388: magadan, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=magadan,ru
    Processing record 389: lixourion, gr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lixourion,gr
    Processing record 390: karaul, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=karaul,ru
    Processing record 391: cabo%20san%20lucas, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cabo%20san%20lucas,mx
    Processing record 392: miandrivazo, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=miandrivazo,mg
    Processing record 393: rabaul, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rabaul,pg
    Processing record 394: calvinia, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=calvinia,za
    Processing record 395: boca%20do%20acre, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=boca%20do%20acre,br
    Processing record 396: karaton, kz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=karaton,kz
    Processing record 397: tadine, nc
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tadine,nc
    Processing record 398: morondava, mg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=morondava,mg
    Processing record 399: panguna, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=panguna,pg
    Processing record 400: longjiang, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=longjiang,cn
    Processing record 401: lipari, it
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lipari,it
    Processing record 402: ust-nera, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ust-nera,ru
    Processing record 403: tambopata, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tambopata,pe
    Processing record 404: baykit, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=baykit,ru
    Processing record 405: cabadiangan, ph
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cabadiangan,ph
    Processing record 406: yirol, sd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yirol,sd
    Processing record 407: vila%20velha, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vila%20velha,br
    Processing record 408: gulfport, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gulfport,us
    Processing record 409: birao, cf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=birao,cf
    Processing record 410: la%20tuque, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=la%20tuque,ca
    Processing record 411: petlawad, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=petlawad,in
    Processing record 412: porto%20novo, cv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=porto%20novo,cv
    Processing record 413: kahului, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kahului,us
    Processing record 414: kloulklubed, pw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kloulklubed,pw
    Processing record 415: saint%20pete%20beach, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint%20pete%20beach,us
    Processing record 416: beyneu, kz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=beyneu,kz
    Processing record 417: samarai, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=samarai,pg
    Processing record 418: olavarria, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=olavarria,ar
    Processing record 419: san%20giorgio%20del%20sannio, it
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20giorgio%20del%20sannio,it
    Processing record 420: hasaki, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hasaki,jp
    Processing record 421: svetlogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=svetlogorsk,ru
    Processing record 422: nicoya, cr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nicoya,cr
    Processing record 423: tombouctou, ml
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tombouctou,ml
    Processing record 424: paamiut, gl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=paamiut,gl
    Processing record 425: zile, tr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=zile,tr
    Processing record 426: dekar, bw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dekar,bw
    Processing record 427: manzanillo, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=manzanillo,mx
    Processing record 428: mopipi, bw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mopipi,bw
    Processing record 429: high%20level, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=high%20level,ca
    Processing record 430: muzquiz, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=muzquiz,mx
    Processing record 431: grand-lahou, ci
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=grand-lahou,ci
    Processing record 432: beisfjord, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=beisfjord,no
    Processing record 433: peterhead, gb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=peterhead,gb
    Processing record 434: crab%20hill, bb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=crab%20hill,bb
    Processing record 435: tubmanburg, lr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tubmanburg,lr
    Processing record 436: krasnoselkup, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=krasnoselkup,ru
    Processing record 437: warwick, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=warwick,au
    Processing record 438: orlovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=orlovskiy,ru
    Processing record 439: alcamo, it
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=alcamo,it
    Processing record 440: kailua, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kailua,us
    Processing record 441: caravelas, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=caravelas,br
    Processing record 442: jabinyanah, tn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jabinyanah,tn
    Processing record 443: egvekinot, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=egvekinot,ru
    Processing record 444: qibili, tn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=qibili,tn
    Processing record 445: great%20yarmouth, gb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=great%20yarmouth,gb
    Processing record 446: rio%20gallegos, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rio%20gallegos,ar
    Processing record 447: la%20ronge, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=la%20ronge,ca
    Processing record 448: guerrero%20negro, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=guerrero%20negro,mx
    Processing record 449: kieta, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kieta,pg
    Processing record 450: olafsvik, is
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=olafsvik,is
    Processing record 451: melo, uy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=melo,uy
    Processing record 452: korla, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=korla,cn
    Processing record 453: pauini, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pauini,br
    Processing record 454: kaeo, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kaeo,nz
    Processing record 455: mabaruma, gy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mabaruma,gy
    Processing record 456: mahanje, tz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mahanje,tz
    Processing record 457: constantine, dz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=constantine,dz
    Processing record 458: pingxiang, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pingxiang,cn
    Processing record 459: berbera, so
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=berbera,so
    Processing record 460: timmins, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=timmins,ca
    Processing record 461: murgab, tm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=murgab,tm
    Processing record 462: fayaoue, nc
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=fayaoue,nc
    Processing record 463: mazara%20del%20vallo, it
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mazara%20del%20vallo,it
    Processing record 464: seoul, kr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=seoul,kr
    Processing record 465: lima, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lima,pe
    Processing record 466: alekseyevsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=alekseyevsk,ru
    Processing record 467: sarakhs, ir
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sarakhs,ir
    Processing record 468: rafai, cf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rafai,cf
    Processing record 469: kalmunai, lk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kalmunai,lk
    Processing record 470: maarianhamina, fi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=maarianhamina,fi
    Processing record 471: kristiansand, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kristiansand,no
    Processing record 472: deputatskiy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=deputatskiy,ru
    Processing record 473: taman, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=taman,id
    Processing record 474: qui%20nhon, vn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=qui%20nhon,vn
    Processing record 475: bolshegrivskoye, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bolshegrivskoye,ru
    Processing record 476: orotukan, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=orotukan,ru
    Processing record 477: kourou, gf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kourou,gf
    Processing record 478: bama, ng
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bama,ng
    Processing record 479: rafaela, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rafaela,ar
    Processing record 480: rakiv%20lis, ua
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rakiv%20lis,ua
    Processing record 481: ninh%20binh, vn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ninh%20binh,vn
    Processing record 482: havre-saint-pierre, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=havre-saint-pierre,ca
    Processing record 483: flinders, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=flinders,au
    Processing record 484: kazalinsk, kz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kazalinsk,kz
    Processing record 485: tabiauea, ki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tabiauea,ki
    Processing record 486: sorland, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sorland,no
    Processing record 487: touros, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=touros,br
    Processing record 488: brigantine, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=brigantine,us
    Processing record 489: cravo%20norte, co
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cravo%20norte,co
    Processing record 490: vadso, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vadso,no
    Processing record 491: poum, nc
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=poum,nc
    Processing record 492: lubango, ao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=lubango,ao
    Processing record 493: atar, mr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=atar,mr
    Processing record 494: oktyabrskoye, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=oktyabrskoye,ru
    Processing record 495: belyy%20yar, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=belyy%20yar,ru
    Processing record 496: progreso, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=progreso,mx
    Processing record 497: batemans%20bay, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=batemans%20bay,au
    Processing record 498: chapais, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=chapais,ca
    Processing record 499: nome, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nome,us
    Processing record 500: mambolo, sl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mambolo,sl
    Processing record 501: black%20river, jm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=black%20river,jm
    Processing record 502: griffith, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=griffith,au
    Processing record 503: jiupu, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jiupu,cn
    Processing record 504: taburi, ph
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=taburi,ph
    Processing record 505: santa%20marta, co
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=santa%20marta,co
    Processing record 506: motygino, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=motygino,ru
    Processing record 507: nanakuli, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nanakuli,us
    Processing record 508: mandali, iq
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mandali,iq
    Processing record 509: tocopilla, cl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tocopilla,cl
    Processing record 510: kisangani, cd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kisangani,cd
    Processing record 511: coruripe, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=coruripe,br
    Processing record 512: jiazi, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jiazi,cn
    Processing record 513: sonoita, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sonoita,mx
    Processing record 514: mutoko, zw
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mutoko,zw
    Processing record 515: jalu, ly
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jalu,ly
    Processing record 516: general%20pico, ar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=general%20pico,ar
    Processing record 517: kantang, th
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kantang,th
    Processing record 518: moundsville, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=moundsville,us
    Processing record 519: rungata, ki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rungata,ki
    Processing record 520: hanmer%20springs, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hanmer%20springs,nz
    Processing record 521: damghan, ir
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=damghan,ir
    Processing record 522: bima, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bima,id
    Processing record 523: kawalu, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kawalu,id
    Processing record 524: kemijarvi, fi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kemijarvi,fi
    Processing record 525: mount%20isa, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mount%20isa,au
    Processing record 526: basco, ph
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=basco,ph
    Processing record 527: rosetta, eg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rosetta,eg
    Processing record 528: bonthe, sl
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bonthe,sl
    Processing record 529: san%20luis, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20luis,mx
    Processing record 530: atambua, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=atambua,id
    Processing record 531: sao%20joao%20da%20barra, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sao%20joao%20da%20barra,br
    Processing record 532: praia, cv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=praia,cv
    Processing record 533: jambi, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jambi,id
    Processing record 534: mayo, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mayo,ca
    Processing record 535: boa%20vista, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=boa%20vista,br
    Processing record 536: kapuskasing, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kapuskasing,ca
    Processing record 537: arandis, na
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=arandis,na
    Processing record 538: coos%20bay, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=coos%20bay,us
    Processing record 539: cerro%20largo, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cerro%20largo,br
    Processing record 540: kuche, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kuche,cn
    Processing record 541: soyo, ao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=soyo,ao
    Processing record 542: christchurch, nz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=christchurch,nz
    Processing record 543: peace%20river, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=peace%20river,ca
    Processing record 544: santa%20fe, cu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=santa%20fe,cu
    Processing record 545: torbay, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=torbay,ca
    Processing record 546: hertford, gb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=hertford,gb
    Processing record 547: grand%20gaube, mu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=grand%20gaube,mu
    Processing record 548: nykobing, dk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nykobing,dk
    Processing record 549: ust-kamchatsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ust-kamchatsk,ru
    Processing record 550: sioux%20lookout, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sioux%20lookout,ca
    Processing record 551: dingle, ie
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dingle,ie
    Processing record 552: veliki%20preslav, bg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=veliki%20preslav,bg
    Processing record 553: debre%20tabor, et
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=debre%20tabor,et
    Processing record 554: senno, by
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=senno,by
    Processing record 555: garowe, so
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=garowe,so
    Processing record 556: sibolga, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sibolga,id
    Processing record 557: levelek, hu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=levelek,hu
    Processing record 558: khuchni, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=khuchni,ru
    Processing record 559: sorong, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sorong,id
    Processing record 560: tessaoua, ne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=tessaoua,ne
    Processing record 561: nouadhibou, mr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nouadhibou,mr
    Processing record 562: mehamn, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mehamn,no
    Processing record 563: kukmor, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kukmor,ru
    Processing record 564: romitan, uz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=romitan,uz
    Processing record 565: inderborskiy, kz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=inderborskiy,kz
    Processing record 566: san%20quintin, mx
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=san%20quintin,mx
    Processing record 567: maloy, no
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=maloy,no
    Processing record 568: corn%20island, ni
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=corn%20island,ni
    Processing record 569: nazca, pe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nazca,pe
    Processing record 570: port%20shepstone, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=port%20shepstone,za
    Processing record 571: dandong, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=dandong,cn
    Processing record 572: mutsamudu, km
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mutsamudu,km
    Processing record 573: santa%20rosa, bo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=santa%20rosa,bo
    Processing record 574: jinchang, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=jinchang,cn
    Processing record 575: ust-omchug, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ust-omchug,ru
    Processing record 576: muroran, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=muroran,jp
    Processing record 577: westerland, de
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=westerland,de
    Processing record 578: marsabit, ke
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=marsabit,ke
    Processing record 579: yasnyy, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=yasnyy,ru
    Processing record 580: qianan, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=qianan,cn
    Processing record 581: nikolayevka, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nikolayevka,ru
    Processing record 582: oistins, bb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=oistins,bb
    Processing record 583: pierre, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pierre,us
    Processing record 584: taua, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=taua,br
    Processing record 585: peterborough, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=peterborough,us
    Processing record 586: sumbawa, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sumbawa,id
    Processing record 587: abeche, td
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=abeche,td
    Processing record 588: marzuq, ly
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=marzuq,ly
    Processing record 589: glendive, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=glendive,us
    Processing record 590: mount%20pleasant, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mount%20pleasant,us
    Processing record 591: nara, ml
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nara,ml
    Processing record 592: umzimvubu, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=umzimvubu,za
    Processing record 593: caconda, ao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=caconda,ao
    Processing record 594: porto%20velho, br
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=porto%20velho,br
    Processing record 595: leh, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=leh,in
    Processing record 596: camacha, pt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=camacha,pt
    Processing record 597: nykoping, se
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nykoping,se
    Processing record 598: sai%20buri, th
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sai%20buri,th
    Processing record 599: fort%20nelson, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=fort%20nelson,ca
    Processing record 600: malwan, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=malwan,in
    Processing record 601: ajdabiya, ly
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=ajdabiya,ly
    Processing record 602: brae, gb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=brae,gb
    Processing record 603: asau, tv
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=asau,tv
    Processing record 604: bismarck, us
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=bismarck,us
    Processing record 605: gazanjyk, tm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gazanjyk,tm
    Processing record 606: pingliang, cn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=pingliang,cn
    Processing record 607: marsh%20harbour, bs
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=marsh%20harbour,bs
    Processing record 608: cockburn%20town, tc
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=cockburn%20town,tc
    Processing record 609: mweka, cd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mweka,cd
    Processing record 610: niono, ml
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=niono,ml
    Processing record 611: rosita, ni
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=rosita,ni
    Processing record 612: kushima, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kushima,jp
    Processing record 613: alotau, pg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=alotau,pg
    Processing record 614: novoagansk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=novoagansk,ru
    Processing record 615: burnie, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=burnie,au
    Processing record 616: nhulunbuy, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=nhulunbuy,au
    Processing record 617: manta, ec
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=manta,ec
    Processing record 618: knysna, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=knysna,za
    Processing record 619: kamenka, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kamenka,ru
    Processing record 620: peniche, pt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=peniche,pt
    Processing record 621: honiara, sb
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=honiara,sb
    Processing record 622: usinsk, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=usinsk,ru
    Processing record 623: gerash, ir
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gerash,ir
    Processing record 624: kefamenanu, id
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=kefamenanu,id
    Processing record 625: arlit, ne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=arlit,ne
    Processing record 626: clyde%20river, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=clyde%20river,ca
    Processing record 627: darhan, mn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=darhan,mn
    Processing record 628: saint-augustin, ca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=saint-augustin,ca
    Processing record 629: vagur, fo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=vagur,fo
    Processing record 630: launceston, au
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=launceston,au
    Processing record 631: grand%20river%20south%20east, mu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=grand%20river%20south%20east,mu
    Processing record 632: salym, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=salym,ru
    Processing record 633: sibu, my
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=sibu,my
    Processing record 634: krutikha, ru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=krutikha,ru
    Processing record 635: mocambique, mz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mocambique,mz
    Processing record 636: labutta, mm
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=labutta,mm
    Processing record 637: mangai, cd
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=mangai,cd
    Processing record 638: shingu, jp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=shingu,jp
    Processing record 639: gat, ly
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=gat,ly
    Processing record 640: springbok, za
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=springbok,za
    Processing record 641: koyilandi, in
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=5addd7633b888ba2559a6ca22e961000&q=koyilandi,in





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City Name</th>
      <th>Country</th>
      <th>Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-33</td>
      <td>27</td>
      <td>East London</td>
      <td>za</td>
      <td>72.59</td>
      <td>100</td>
      <td>80</td>
      <td>2.84</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-34</td>
      <td>19</td>
      <td>Hermanus</td>
      <td>za</td>
      <td>63.18</td>
      <td>91</td>
      <td>76</td>
      <td>5.64</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-26</td>
      <td>15</td>
      <td>Luderitz</td>
      <td>na</td>
      <td>62.6</td>
      <td>88</td>
      <td>76</td>
      <td>18.34</td>
    </tr>
    <tr>
      <th>3</th>
      <td>33</td>
      <td>-117</td>
      <td>Carlsbad</td>
      <td>us</td>
      <td>69.33</td>
      <td>23</td>
      <td>1</td>
      <td>9.17</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-20</td>
      <td>55</td>
      <td>Le Port</td>
      <td>re</td>
      <td>79.59</td>
      <td>83</td>
      <td>92</td>
      <td>29.97</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_pd = df.dropna(how='any')
df_pd.head()

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City Name</th>
      <th>Country</th>
      <th>Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-33</td>
      <td>27</td>
      <td>East London</td>
      <td>za</td>
      <td>72.59</td>
      <td>100</td>
      <td>80</td>
      <td>2.84</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-34</td>
      <td>19</td>
      <td>Hermanus</td>
      <td>za</td>
      <td>63.18</td>
      <td>91</td>
      <td>76</td>
      <td>5.64</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-26</td>
      <td>15</td>
      <td>Luderitz</td>
      <td>na</td>
      <td>62.6</td>
      <td>88</td>
      <td>76</td>
      <td>18.34</td>
    </tr>
    <tr>
      <th>3</th>
      <td>33</td>
      <td>-117</td>
      <td>Carlsbad</td>
      <td>us</td>
      <td>69.33</td>
      <td>23</td>
      <td>1</td>
      <td>9.17</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-20</td>
      <td>55</td>
      <td>Le Port</td>
      <td>re</td>
      <td>79.59</td>
      <td>83</td>
      <td>92</td>
      <td>29.97</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(df_pd.count())
df_pd.to_csv("Output.csv",index = False)
```

    Lat           553
    Lng           553
    City Name     553
    Country       553
    Temp          553
    Humidity      553
    Cloudiness    553
    Wind Speed    553
    dtype: int64



```python
plt.scatter(df_pd["Lat"],df_pd["Temp"],color="blue",alpha = "0.75")
plt.title("City Latitude vs. Temperature")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.grid(True,color = "white")
plt.rcParams['axes.facecolor'] = 'lightgrey'
plt.show()
```


![png](Weatherpy_files/Weatherpy_6_0.png)



```python
plt.scatter(df_pd["Lat"],df_pd["Humidity"],color="blue",alpha = "0.75")
plt.title("City Latitude vs. Humidity (%)")
plt.xlabel("Latitude")
plt.ylabel("Humimdity (%)")
plt.grid(True,color = "white")
plt.rcParams['axes.facecolor'] = 'lightgrey'
plt.show()
```


![png](Weatherpy_files/Weatherpy_7_0.png)



```python
plt.scatter(df_pd["Lat"],df_pd["Cloudiness"],color="blue",alpha = "0.75")
plt.title("City Latitude vs. Cloudiness (%)")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.grid(True,color = "white")
plt.rcParams['axes.facecolor'] = 'lightgrey'
plt.show()
```


![png](Weatherpy_files/Weatherpy_8_0.png)



```python
plt.scatter(df_pd["Lat"],df_pd["Wind Speed"],color="blue",alpha = "0.75")
plt.title("City Latitude vs. Wind Speed (mph)")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")
plt.grid(True,color = "white")
plt.rcParams['axes.facecolor'] = 'lightgrey'
plt.show()
```


![png](Weatherpy_files/Weatherpy_9_0.png)

