# 프랜차이즈 카페별 대표 음료 찾기
2021-1 Web Python Term Project 👩‍💻


### 1) 프로젝트 목표 및 내용

- 대표 기능
    - 프렌차이즈 카페별 대표 음료 찾기
- 추가 기능
    - 음료별 대표 카페 찾기
    - 감성분석을 통한 카페 이용자들의 의견 분석

<br>

해당 프로젝트의 가장 큰 목표는 `프랜차이즈 카페별 대표 음료` 를 알아내고, 메뉴 결정을 망설이는 소비자에게 정보를 제공해주는 것이다. 대표 음료는 SNS 언급량이 많은 순으로 결정할 계획이다.
    
또한, 부차적인 기능이 두 가지 있다. 첫 번째는 먹고 싶은 음료가 고정되어 있을 때 카페 별 음료 언급 빈도의 비율을 이용해서 `음료 별 대표 카페` 정보를 제공해주는 것이다. 두 번째는  트위터와 블로그 데이터를 크롤링하여 각 카페별 감성 분석을 진행하고, 많이 사용된 단어를 알아봄으로써 각 카페에 대한 ```카페 이용자들의 의견```을 간단하게 분석해 보는 것이다.

<br>

### 2) 주제 선정 이유


첫 번째는 **선택을 어려워하는 소비자에게 정보를 제공해주기 위함**이다. '선택 장애'라는 말이 있다. 요즘에는 많은 사람이 음식점을 가도 메뉴를 고르는 것을 어려워하고, 카페를 가도 마찬가지다. 선택이 어렵다면 사람들이 가장 많이 마시고 언급이 많은 음료부터 하나씩 마셔보는 건 어떨까? 해당 프로젝트는 프랜차이즈 카페별 대표음료와 음료별 대표 카페를 알아봄으로써 소비자의 결정에 도움을 줄 수 있다. 음료의 출현시기가 모두 다르기 때문에 SNS 언급량이 많다고 무조건 맛있는 '베스트셀러'는 아니겠지만,  꾸준히 잘 팔리는 '스테디셀러'로 간주할 수 있기 때문에, 음료 선택을 실패할 확률은 낮아질 것이다.

두 번째는 **각 프랜차이즈 카페에 정보를 제공해주기 위함**이다. 각 카페와 관련된 트위터와 블로그 콘텐츠를 크롤링하고 감성분석을 통해 긍정의견/부정의견으로 나눠 많이 사용된 단어를 알아봄으로써 카페에 대한 소비자들의 의견과 이미지를 알아볼 수 있다. 이러한 정보를 이용하면 다음 신메뉴를 만들거나 서비스를 개선하는 데 있어 도움이 될 것이다.



### 3) 데이터 획득

각 카페별 음료는 각 카페의 홈페이지를 이용해서 정보를 획득했다. 음료 언급량 획득과 감성분석을 하기 위해서 네이버 블로그와 트위터 콘텐츠를 중점적으로 크롤링했다.

```Python
#트위터 크롤링 (open source)

day = []
tweetList = []

dates = [datetime.datetime.strptime("2020-05-06", "%Y-%m-%d") + 
                  datetime.timedelta(days=x) 
                  for x in range(0, (datetime.datetime.strptime("2020-06-07", "%Y-%m-%d")
                                     -datetime.datetime.strptime("2020-05-06", "%Y-%m-%d")).days)]

for date in tqdm(dates):
    day.append(date.strftime("%Y-%m-%d"))
    
dict3={"스타벅스":"스타벅스 OR 스벅", "이디야": "이디야", "투썸플레이스":"투썸", "파스쿠찌": "파스쿠찌 OR 파스쿠치","앤제리너스":"앤제리너스 OR 엔젤리너스", 
       "할리스": "할리스", "탐앤탐스":"탐앤탐스 OR 탐탐", "커피빈": "커피빈", "빽다방":"빽다방"} 

cafedicnum=str(dropdown1.value)
search = str(dict3[cafedicnum])

runTweet = got.manager.TweetCriteria().setQuerySearch(search)\
                                           .setSince(day[0])\
                                           .setUntil((datetime.datetime.strptime(day[-1], "%Y-%m-%d") 
                                            + datetime.timedelta(days=1)).strftime("%Y-%m-%d"))\
                                           .setMaxTweets(-1)

tweet = got.manager.TweetManager.getTweets(runTweet)
print("크롤링한 트위터 갯수: " , len(tweet))
```

<br>


### 4) 구현 내용 설명

분석 가능한 카페는 총 9개로, 스타벅스, 앤제리너스, 파스쿠찌, 이디야, 탐앤탐스, 투썸플레이스, 할리스, 커피빈, 빽다방이 이에 해당한다.

```프렌차이즈 카페별 대표 음료 찾기 ☕️ ```
1. 각 카페별 메뉴(음료)를 미리 txt 파일에 정리한다.
2. 소비자가 분석 하고 싶은 카페명을 선택하면, 카페명에 해당하는 txt파일을 열어서 한 줄 씩 읽어 '카페명+음료명'을 모두 검색한다. 크롤링을 위해 Beautiful Soup를 이용하며, 이 과정에서는 각 '카페명+음료명'별 블로그 콘텐츠 개수를 가져와 csv 파일로 저장한다.
3. csv 파일을 다시 불러와 음료명을 보정해준다. 일반적인 음료지만 이름만 독특한 경우, 축약어 등이 이 과정에 해당한다. (ex. 앗메리카노→아메리카노, 자허블→ 자봉허니블랙티)
4. 각 카페의 음료별 언급 빈도가 많은 순서로 나열하고, Pandas Data Frame과 Pie Plot을 만들어 한 눈에 보기 쉽도록 한다.

<br>


```음료별 대표 카페 찾기 🍵 ```  
1. 앞선 과정에서 저장해둔 카페별 csv 파일을 모두 불러와 하나의 Pandas Data Frame으로 만들어 준다.
2. 소비자가 마시고 싶은 음료명을 선택하면, Data Frame에서 해당 음료명과 일치하는 열만 추출하고, 해당 음료의 언급비율이 많은 카페 순으로 나열해주는 기능이다. (카페별로 총 콘텐츠 개수가 천차만별이므로, 언급 빈도가 아닌, '각 카페에서 얼마나 영향력이 있는가?'를 나타내는 '언급 비율'을 이용하는 것이다.)
3. Pandas Data Frame과 Barh plot을 만들어 한 눈에 보기 쉽도록 한다.

```python
# 음료별 대표 카페 찾기

f=open('./common/sum_df.csv', 'r', encoding='CP949',newline='')
sumDf = pd.read_csv(f)

drink = sumDf['음료명'].unique().tolist()
item = [' ']+sorted(drink)

dropdown= widgets.Dropdown(options= item,description = '음료명 : ' )
outputDf= widgets.Output()
outputPlot=widgets.Output()

def dropdown_control(change):
    outputDf.clear_output()
    outputPlot.clear_output()
    with outputDf:
        display(sumDf.loc[sumDf['음료명']==change.new].sort_values(by=["언급률"],ascending=[False]).reset_index())
    
    with outputPlot:
        display(showbarh(sumDf.loc[sumDf['음료명']==change.new].sort_values(by=["언급률"],ascending=[False]).reset_index()))
    

dropdown.observe(dropdown_control, names='value')
display(dropdown)

widgetchanges=widgets.Tab([outputDf, outputPlot])
widgetchanges.set_title(0, ' 맛있는 카페')
widgetchanges.set_title(1, '그래프')

display(widgetchanges)

f.close()
```

<br>


```감성분석을 통한 카페 이용자들의 의견 분석 🧃 ```

1. 분석하고 싶은 카페명을 입력하면, 트위터 콘텐츠를 크롤링한다. 트위터 자체에서 제공하는 API는 최근 7일 간의 콘텐츠만 크롤링할 수 있다. 과거 데이터도 함께 크롤링할 수 있는 GetOldTweet 모듈을 사용해서 각 카페별 최근 한 달 간의 트위터 데이터를 크롤링 한다.
2. 네이버 고객 의견을 모으기 위해, Beautiful Soup를 이용해 카페별 블로그 제목을 500개씩 크롤링 한다.
3.  감성분석을 진행하기 위해 한글 본문을 영어로 번역해 준다. (한글 감성 분석 오픈소스도 있지만 비교적 전처리가 쉬운 영어로 번역해서 프로젝트를 진행했다.) Papago API는 일일 번역량에 한계가 있기 때문에 pypapago 오픈소스를 사용했다.
4. AFINN 어휘사전을 통해 영어로 번역된 각 콘텐츠를 긍정/부정/중립 의견으로 분류한다. AFINN score 가 0보다 크면 긍정, 작으면 부정, 0이면 중립으로 분류한다.
5. 카페별 긍정/부정/중립 의견의 비율이 어떤지 Pie graph 를 통해 알아보고, 긍정 및 부정 콘텐츠에 많이 사용된 단어가 무엇인지 알아 본다.
6. 9개 카페의 긍부정 단어 출현 비율 평균값과 자사의 긍부정 단어 출현 비율을 비교함으로써 자사의 강점과 부족한 점을 파악할 수 있도록 한다.
7. 결과를 Pandas DataFrame과 Bar plot을 만들어 한 눈에 보기 쉽도록 한다.

<br>

### 5) 분석결과
선택한 카페 / 음료 종류에 따라 다른 결과가 도출 됩니다.

#### ```할리스에서 잘나가는 음료는?```  - 카페를 선택하면 네이버 블로그 언급량을 통해 카페별 대표 음료를 알 수 있다!
<img src="https://user-images.githubusercontent.com/64299475/126641505-4c6aaf6e-3805-48cd-8a6c-65cc30b5d761.JPG" width="600">

<br>

#### ```에스프레소가 가장 맛있는 카페는?```  - 음료를 선택하면 카페별 네이버 블로그 언급비율을 통해 음료별 대표 카페를 알 수 있다!
<img src="https://user-images.githubusercontent.com/64299475/126642091-af168a3d-10b6-446f-90d3-797c902f028d.JPG" width="600">
<br>                            

#### ```감성분석: 카페 후기 한 눈에 보기```  - 네이버와 트위터에서 생각하는 우리 카페 이미지는? 긍/부정 감성분석! 
<img src="https://user-images.githubusercontent.com/64299475/126642097-02ab5f15-187f-4028-96c7-e7075e1c2753.JPG" width="300">
             
<br>

#### ```부정적 의견에서 많이 사용한 단어는?```  - 우리 카페의 단점 알아보기!
<img src="https://user-images.githubusercontent.com/64299475/126642098-c1a7866b-fd04-406c-8ede-3fbcc2689afd.JPG" width="600">
    
<br>

#### ```긍정적 의견에서 많이 사용한 단어는?```  - 우리 카페의 장점 알아보기!
<img src="https://user-images.githubusercontent.com/64299475/126642102-6ff3eabe-ee7b-4855-b57a-24a453b7e7e0.JPG" width="600">
    
<br>


### 6) 결론


```목표 달성 체크 🎯 ```  

해당 프로젝트에서는 프랜차이즈 카페별 대표 음료 찾기를 중심으로, 음료별 대표 카페 찾기, 감성분석을 통한 카페 이용자들의 의견 분석까지 진행했고, 몇가지 결론이 도출되었다.

첫번째는 일일이 카페명+음료명을 검색하지 않아도, 네이버 블로그 콘텐츠 양을 수집함으로써 음료 선택을 망설이는 소비자에게 정보를 제공해줄 수 있게 된 것이다. 

두번째는 비슷한 맥락으로 음료명만 입력을 해도, 9개의 카페 중 어디에서 해당 음료를 판매하며, 어디가 가장 해당 음료의 언급률이 높은지까지 파악할 수 있다.

세 번째는 감성분석을 통해 각 카페의 긍/부정 평가 비율과 긍정적인 의견에서 많이 사용된 단어, 부정적인 의견에서 많이 사용된 단어를 파악할 수 있다. 이 정보는 각 프랜차이즈 카페의 차기 신메뉴 개발, 서비스 개선에 도움이 될 수 있다. 예를 들어, 긍정적인 단어 중 'delicious'의 언급 비율이 9개 카페의 평균보다 낮다면, 맛에 좀 더 중점을 둬서 신메뉴를 개발할 수 있는 것이다.


<br>

```한계 📌 ```  

하지만 몇 가지 한계점도 있었다. 

첫번째는 언급 빈도와 대표음료와의 관계가 명확하지 않다는 것이다. 대부분의 카페에서 언급 빈도가 가장 높은 음료는 '아메리카노'였는데, 이러한 결과가 '어떤 카페든 아메리카노가 가장 맛있다'로 해석될 수는 없기 때문이다. 언급량이 높은 음료는 스테디셀러와 베스트셀러가 섞여있기 때문에 '개개인의 취향을 고려하지 않은 단순 추천'정도가 될 수 있을 것 같다.

두번째는 음료별 대표 카페 찾기에서, 음료를 구분짓는 것이 쉽지 않다는 점이다. 같은 종류의 음료라도 이름이 제각기 다른 경우와 '망고스노우, 망고스무디, 망고주스' 처럼 경계가 모호한 음료를 구분하는 것에 한계가 있었다.

세번째는 단어만으로 우리 카페의 장/단점을 알아내는 것이 쉽지 않다는 것이다. 예를 들어, 부정적 평가 요소에서 'bitter'가 평균보다 훨씬 높다면, '우리 카페의 음료가 타 카페의 음료보다 쓰구나'라고 해석할 수 있지만, 'bad', 'sad'와 같은 단어는 무엇이 나쁘고, 무엇이 슬프다는 건지 사실상 해석하기가 어렵다.

네번째는 번역 및 감성분석 작업에서의 오류 발생이다. 예를 들어, 우리나라에서는 '미쳤어'가 긍정적으로 해석될 수 있지만, afinn 단어 사전에서 'crazy'는 부정적인 단어로 해석이 된다. 비슷한 경우로, '너무 맛있어서 죽음'이라는 문장은 긍정적인 문장이지만, 마찬가지로 영어로 번역시 부정적인 문장으로 해석된다. 이를 해결하기 위해서는 한글용 감성분석기를 사용하거나 보정작업이 필요할 것 같다.



### 7) 오픈소스


트위터 본문 크롤링 하는 부분에서 ```GetOldTweets3```open source library 사용하였습니다.  
https://pypi.org/project/GetOldTweets3/  
<br>
트위터 / 블로그 데이터 영어로 번역하는 부분에서 ```Pypapago``` open source library 사용하였습니다.  
https://pypi.org/project/pypapago/  
<br>
감성분석 하는 부분에서 ```afinn``` open source library 사용하였습니다.  
https://pypi.org/project/afinn/  
<br>
sentiment dataframe 과 neagtive / positive dataframe 만드는 과정에서 tokenizer 작업을 하기 위해 ```sentifish``` open source library 사용하였습니다.  
https://pypi.org/project/sentifish/  
<br>

기타 크롤링을 위한 Beautiful Soup, urlopen, 단어 개수 카운팅을 위한 Counter, 시각화를 위한 ipywidgets, matplotlib 패키지 등을 사용하였습니다.
