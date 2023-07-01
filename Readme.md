# Entity Name Unification
## 유사도 0.75로 설정한 이유
- 유사도 점수는 낮을수록 좋음 (유사한 상호명 잘 찾아낼 확률이 높아지니) 
- 하지만 문제는 점수가 너무 낮으면 동일한 상호명이 아닌데도 유사한 상호명으로 잘못 찾아내는 경우 발생할 때!
- 따라서, 해당 오류가 나기 전까지 최대한 유사도 점수 임계치를 낮추는 것이 목표
- 유사도 0.6일 때 발생하는 오류들이 0.7일때도 동일하게 발생 (예시 1 제외), 0.75부터 오류 피할 수 있음
### 예시 1) 한남멜론인중개사사무소


#### 유사도 0.8로 설정할 경우


##### 1. 글자가 잘린 경우
- 중간에 한 글자가 잘린 경우 O 

![image](https://github.com/jenny99lee/test/assets/108797646/043d2e0f-12db-4e07-969a-a620541ef34d)
- 중간에 두 글자가 잘린 경우 O 

![image](https://github.com/jenny99lee/test/assets/108797646/e524ad9e-d0ed-4842-b65e-f72ec44a3169)
- 뒤에 한 글자가 잘린 경우 O 

![image](https://github.com/jenny99lee/test/assets/108797646/5a795f25-0a4d-492d-b043-b6bb5dd9c003)
- 뒤에 두 글자가 잘린 경우 O 

![image](https://github.com/jenny99lee/test/assets/108797646/2948a4b1-98fb-4bba-8e59-a1080cf85acd)
- 뒤에 세 글자가 잘린 경우 X


![image](https://github.com/jenny99lee/test/assets/108797646/ebe2cd34-0ce0-4974-b25f-4c7c0b441b7a) 

- 글자가 잘린 경우 2글자까지만 잘 찾아내고, 세 글자가 잘린 순간부터 원래 단어를 찾아내지 못함.
- 데이터 중에 3글자 이상 잘린 데이터 상당수 존재 (특히 뒤가 잘린 경우) -> 0.8은너무 strict한 임계치로 판단



##### 2. 오타가 난 경우
- 오타 한글자 O 
![image](https://github.com/jenny99lee/test/assets/108797646/af091d85-b2b2-4fe6-a9ec-ac8ab80f201e)
- 오타 두글자 X 
![image](https://github.com/jenny99lee/test/assets/108797646/eff5205a-efed-487a-bed4-6da287d8dacf)

 - 오타가 난 경우, 글자가 잘린 경우보다 더 성능이 안 좋음
 - 총 11글자짜리 단어에서 두 글자에서만 오타가 나도 원래 단어를 찾아내지 못함
-> 임계치 값이 너무 높아서 성능이 떨어지는 것으로 판단, 임계치를 0.6으로 줄여보자!


#### 유사도 0.6으로 설정할 경우
- 총 11글자 중 뒤에 여섯 글자가 잘린 경우 절반 이상이 잘린 것이기 때문에 섣불리 유추해내는 것보다는 신중히 판단하는 모델이 나음.
- 하지만 뒤가 잘린 경우 (여섯 글자) "한남멜론인중개" -> "한남멜론인중개사사무소"를 찾아줘야 하는데 의도와 맞지 않게 '한남체인'을 유사한 상호명으로 찾아줌! 
- "멜론"은 "한남체인"에 아예 포함되어있지 않는데도 matching한 점에서 0.6은 너무 낮은 점수일 수도 있다는 점! (새로운 store인데 추가 안하는 경우 생길 수 있음)

![image](https://github.com/jenny99lee/test/assets/108797646/9c725266-155f-4ae8-8d36-1e5972018540)


- 0.8은 유사한 상호명을 너무 못 찾아줬고, 0.6은 유사하지 않은데도 새로운 상호명으로 추가하지 않고 1~2글자 겹치는 상호명을 유사한 상호명으로 찾아주는 오류가 생김.
- 0.8과 0.6 사이의 0.75를 유사도 점수로 시도해보면 어떨까?

#### 유사도 0.75로 설정할 경우
##### 1. 글자가 잘린 경우 
 
- 네 글자까지 찾아줌

![image](https://github.com/jenny99lee/test/assets/108797646/9adf63df-2398-451c-8dfd-bdc90b038fb5)


##### 2. 오타가 난 경우
 
- 두 글자까지 찾아줌

![image](https://github.com/jenny99lee/test/assets/108797646/34c26825-efde-47f2-ae15-00ceb79fd0ed)

##### 3. 0.6일 때 오류 났던 예시 (6글자 잘린 경우)
 
- ‘한남체인’고 input_text인 ‘한남멜론인’은 ‘체’가 ‘멜론’으로 바뀐 것 말고는 매우 유사 (길이, 글자)
- 하지만 ‘한남멜론인’을 ‘한남체인’이라고 유사한 상호명으로 찾아버리면 위험 (‘멜론’은 ‘한남체인’의 오타로 발생한 것이 아니라 아예 다른 단어이기 때문에)
- 유사도 0.75로 할 경우, 억지로 비슷한 상호명을 찾아주는 것이 아니라 적절한 상호명이 없다고 판단하고 ‘No exact match or similar store found’라고 결과값이 잘 나옴

![image](https://github.com/jenny99lee/test/assets/108797646/cf642dcd-b997-4494-b794-0e2ad2b2a91e)


### 예시 2) 굿앤굿언어감각발달연구소
#### 유사도 0.8인 경우
##### 1. 글자가 잘린 경우
- 3글자가 잘린 경우는 잘 찾아냄

 ![image](https://github.com/jenny99lee/test/assets/108797646/d13e0ca7-9cf5-43e8-ac07-bd0b4ef23aff)


- 4글자가 잘린 순간부터 ‘No exact match or similar store found’라고 나옴

 ![image](https://github.com/jenny99lee/test/assets/108797646/532f7928-7af1-4920-8a74-54e4c10354cd)

##### 2. 오타가 난 경우
- 두 글자 오타 났을 때 원래 상호명 잘 찾아냄 (굿앤굿언어감각발달연구소  굿앤굿언여감각발달연구쇼)

![image](https://github.com/jenny99lee/test/assets/108797646/80b0f7df-87c4-4fa2-8591-bc0ec0e76a28)


- 세 글자 오타 났을 때 원래 상호명 못 찾아냄 (굿앤굿언어감각발달연구소  굿앤굿언여감칵발달연구쇼)

 ![image](https://github.com/jenny99lee/test/assets/108797646/212f4a1f-3bc1-448a-b76c-add090efe175)


#### 유사도 0.6인 경우
##### 1. 글자가 잘린 경우
- 다섯 글자 잘린 경우도 잘 찾아냄
 
![image](https://github.com/jenny99lee/test/assets/108797646/1626eabf-7913-4e0e-8d11-35dea081b0dc)

##### 2. 오타가 난 경우
- 네 글자 오타 났을 때 원래 상호명 잘 찾아냄 (굿앤굿언어감각발달연구소  굿앤굿언여감칵발댤연구쇼)
  
![image](https://github.com/jenny99lee/test/assets/108797646/12660331-e4ca-4a5f-a37a-bb11b2cb2a3c)


##### 3. 오류 나는 경우
- 6글자 잘린 경우는 원래 의도했던 상호명을 찾아주지 않고 다른 상호명을 찾아주는 오류를 범함
- 잘린 경우는 발생할 수 있지만 기존 상호명에 추가로 글자가 더해지는 경우는 흔하지 않기 때문에 아래 예시와 같은 상황이 일어나면 오류일 확률이 높으므로 임계치를 올려야 함

![image](https://github.com/jenny99lee/test/assets/108797646/b50f7221-942c-4e12-bb08-bb31de8a4059)

#### 유사도 0.75인 경우
##### 1. 글자가 잘린 경우
- 다섯 글자 잘린 경우도 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/9501f8dd-2adc-4480-b377-98e10cde26f0)

##### 2. 오타가 난 경우
- 세 글자 오타 났을 때 원래 상호명 잘 찾아냄 (굿앤굿언어감각발달연구소  굿앤굿언여감칵발댤연구소)
 
![image](https://github.com/jenny99lee/test/assets/108797646/0ae87dd8-fea2-4005-8314-d2b5ab7531c1)
##### 3. 오류 보완
- 뒤가 너무 많이 잘린 경우 다른 비슷한 상호명을 찾기보다 ‘No exact match or similar store found’가 output으로 나오며 오류를 줄임
 
![image](https://github.com/jenny99lee/test/assets/108797646/92beb05c-33ee-4167-9991-574fecf9ee4c)


### 예시 3) 
#### 유사도 0.8인 경우
##### 1. 글자가 잘린 경우
- 다섯 글자가 잘렸을 경우 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/3d0bc901-3e37-4296-a1cb-ae13b5e23952)


- 여섯 글자 잘렸을 경우 찾아내지 못함

![image](https://github.com/jenny99lee/test/assets/108797646/f2e02e55-ce93-4d2d-965b-58638edcb896)

##### 2. 오타가 난 경우
- 세 글자에서 오타가 난 경우: ‘한국바른몸관리연구소정크스튜디오’  ‘한국바른몸관리연구소정크수투띠오’
- 기존 상호명 잘 찾아냄
 
![image](https://github.com/jenny99lee/test/assets/108797646/544b63ef-aaa3-42cb-9c67-0527962d2ac4)

- 네 글자에서 오타가 난 경우: ‘한국바른몸관리연구소정크스튜디오’  ‘한국바른몸관리연구소졍크수투띠오’
- 찾아내지 못함

![image](https://github.com/jenny99lee/test/assets/108797646/a0a5f522-d65f-4384-b57e-9edb43a849fa)



#### 유사도 0.6인 경우
##### 1. 글자가 잘린 경우
- 7 글자 잘려도 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/35f7b1a7-24a7-4827-a818-21e50b55b5a0)

##### 2. 오타가 난 경우
- 오타가 5글자 난 경우 잘 찾아냄 (한국바른몸관리연구소정크스튜디오  한국바른몸관리연구쇼졍크수투띠오)

![image](https://github.com/jenny99lee/test/assets/108797646/a3f9fcda-4e76-4188-8e45-35cecab68c3f)


- 오타가 6글자 나면 더 이상 찾아내지 못함 (한국바른몸관리연구소정크스튜디오  한국바른몸관리연꾸쇼졍크수투띠요)

![image](https://github.com/jenny99lee/test/assets/108797646/b3b2d73a-4aaf-463f-9bb4-986192010fb8)

##### 3. 오류 나는 경우
- 뒤가 많이 잘려서 8글자가 잘리고 앞에 8글자 남은 경우
- 너무 많은 내용이 손실되었기 때문에 ‘No exact match or similar store found’가 나오는 것이 안전함
- 하지만 ‘한국’, ‘연’은 포함하지 않고 ‘샵’을 추가한 ‘바른몸관리샵’이라는 상호명을 유사한 상호명으로 추출하는 오류를 범함

![image](https://github.com/jenny99lee/test/assets/108797646/202d07ae-5012-41da-93fc-c4975f7a8d5d)

#### 유사도 0.75인 경우
##### 1. 글자가 잘린 경우
- 7글자 잘린 경우까지 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/2e5ad790-aab7-4a31-9b4e-e8b7493c28a9)


##### 2. 오타가 난 경우
- 네 글자까지 잘 찾아냄 (한국바른몸관리연구소정크스튜디오  한국바른몸관리연구소졍크수투띠오)

![image](https://github.com/jenny99lee/test/assets/108797646/a9baf325-87f9-4cb3-b126-7b4dd9edb097)


##### 3. 오류 보완
- 8글자가 잘린 경우 0.6 유사도일때와 다르게 ‘No exact match or similar store found’로 오류 피함
 
![image](https://github.com/jenny99lee/test/assets/108797646/ee31072b-cbcc-4939-b98f-a283402130e5)


### 예시 4) 로베르타디까메리노
#### 유사도 0.8인 경우
##### 1. 글자가 잘린 경우
- 두 글자 잘린 경우까지 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/dd53d1a1-c4fb-41e1-b65b-ccea6ffd0710)


- 세 글자 잘린 경우 못 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/78b99f29-7add-4c5a-b6e5-b78e3d60936e)

 
##### 2. 오타가 난 경우
- 한 글자 오타난 경우 잘 찾아냄 (로베르타디까메리노  로베르타디까메리뇨)

![image](https://github.com/jenny99lee/test/assets/108797646/e0c180c2-7ca5-4ff2-b3fd-f4d54670aa9f)


- 두 글자 오타난 경우 못 참아냄 (로베르타디까메리노  로베르타디까메라뇨)

![image](https://github.com/jenny99lee/test/assets/108797646/9d8904bb-f3b8-44b2-998b-a18ef7db7d93)


#### 유사도 0.6인 경우
##### 1. 글자가 잘린 경우
- 3 글자 잘린 경우도 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/6b0696a4-c5af-4935-be6a-00ebb51d2336)



##### 2. 오타가 난 경우
- 3 글자 오타난 경우 잘 찾음

![image](https://github.com/jenny99lee/test/assets/108797646/ded39f3f-0481-4bdc-b800-8fec3fe1634a)


##### 3. 오류 나는 경우
- 오타가 4글자 난 경우는 해당 오타난 부분을 고려하지 않고 앞 4글자만 고려해서 ‘로베르타’라는 다른 짧은 상호명을 가장 유사한 상호명으로 찾아냄
- ‘로베르타디카몌라뇨’는 ‘로베르타디까메리카노’의 오타일 확률이 더 높은데, 오타난 부분들을 아예 고려하지 않고 ‘로베르타’로 매칭하는 오류를 범함 

![image](https://github.com/jenny99lee/test/assets/108797646/e48e0465-36e6-441a-9cbc-568123c68550)

#### 유사도 0.75인 경우
##### 1. 글자가 잘린 경우
- 세 글자 오타난 경우 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/49467408-02e2-4329-b0f2-433be7b9d722)



##### 2. 오타가 난 경우
- 두 글자 오타난 경우까지 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/0454db08-6646-4cec-b8a2-a43925fbe7a3)

##### 3. 오류 보완
- 유사도 0.6일 때 아예 다른 짧은 상호명을 잘못 매칭한 것과 다르게 오타가 많이 난 경우 ‘No exact match or similar store found’로 오류를 피함

![image](https://github.com/jenny99lee/test/assets/108797646/4bc17a20-d84b-4b9e-92a9-37e784f9e7a3)


### 예시 5) 영진타워엔지니어링
#### 유사도 0.8인 경우
##### 1. 글자가 잘린 경우
- 두 글자 잘린 것까지 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/3375bde0-3e6d-4dd2-badd-91f465fa5ea6)


- 세 글자 잘리면 못 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/dcebee64-7222-4bb5-ac63-68face7c6562)

##### 2. 오타가 난 경우
- 한 글자 오타나면 잘 찾아냄 (영진타워엔지니어링  영진타워엔지니어리)

![image](https://github.com/jenny99lee/test/assets/108797646/c1bc326d-9929-4da7-a588-44fcf6e7e648)


- 두 글자 오타나면 찾지 못함 (영진타워엔지니어링  영진타워엔지니여리)

![image](https://github.com/jenny99lee/test/assets/108797646/30a54b2a-4774-4ffb-b2a4-589ddd9bc02b)


#### 유사도 0.6인 경우
##### 1. 글자가 잘린 경우
- 4 글자 잘린 것까지 잘 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/7e6debc4-a4c7-4872-965a-9ac4946fcae8)

##### 2. 오타가 난 경우
- 3 글자가 오타난 것까진 잘 찾아냄 (영진타워엔지니어링  영진타워엔찌니여리)

![image](https://github.com/jenny99lee/test/assets/108797646/027b0e18-e3e2-44aa-9382-bc86188459e3)


- 네 글자가 오타나면 찾지 못함 (영진타워엔지니어링  영진타워앤찌니여리)

![image](https://github.com/jenny99lee/test/assets/108797646/70226010-2132-4626-ad68-2cae055f859f)

##### 3. 오류 나는 경우
- 4글자 잘리고 마지막 글자에 오타가 있는 경우 ‘진타워’만 겹치는 전혀 다른 상호명인 ‘세진타워’를 가장 유사한 상호명으로 찾아내는 오류를 범함
- 잘린 글자수도 많고 오타도 함께 존재하기 때문에 기존 상호명이 불분명할 수 있어 오류를 범하기 위해 ‘No exact match or similar store found’가 나와야 함

![image](https://github.com/jenny99lee/test/assets/108797646/8d2fc308-c152-425f-86c0-64abd1448067)

#### 유사도 0.75인 경우
##### 1. 글자가 잘린 경우
- 3글자 잘린 경우까지 찾아냄

![image](https://github.com/jenny99lee/test/assets/108797646/23f78b38-9cb4-4f71-8250-84e6321c4852)


- 4글자 잘리면 찾아내지 못함

![image](https://github.com/jenny99lee/test/assets/108797646/473aa67f-eb81-4301-8a40-f97060259c62)
 
##### 2. 오타가 난 경우
- 두 글자 오타난 경우까지 찾아냄 (영진타워엔지니어링  영진타워엔지니여리)

![image](https://github.com/jenny99lee/test/assets/108797646/f32f365c-dcdc-45f2-a73f-2219b691d971)



- 세 글자 오타난 경우는 찾아내지 못함 (영진타워엔지니어링  영진타워엔찌니여리)

![image](https://github.com/jenny99lee/test/assets/108797646/aa9febd3-8e23-4379-b8e6-f19ae2d2dc40)


##### 3. 오류 보완
- 유사도 0.6일 때 오류났던 부분 해결됨
- 4글자 잘리고 마지막 글자에 오타가 발생한 경우, 전혀 다른 상호명인 ‘세진타워’이 output으로 나오는 대신 ‘No exact match or similar store found’가 나와서 오류 피함

![image](https://github.com/jenny99lee/test/assets/108797646/dd1d1004-7fd4-4d20-b8c0-88b45399cc31)

