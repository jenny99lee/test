# Model
## Stage 1. Prompt-to-image

- Stable Diffusion 1.5 모델 활용
- 글 (Prompt)을 입력하면 Stable Diffusion 1.5을 통해 image가 생성됨
  - ex) 'Black and white photo, Some bicycles have been left unattained'라는 문장 입력하면 자전거가 벽에 기대어 있는 흑백 이미지 생성
![image](https://user-images.githubusercontent.com/108797646/230771341-c4253566-4377-4418-a296-48f35d020ed6.png)

## Stage 2. Image-to-prompt
- BLIP 모델 활용
- 이미지(Image)를 input으로 넣으면 BLIP을 통해 prompt 생성

1. 정답 선지 생성 (1개)
- 이미지 & 정답 선지 paired dataset 활용
- 토익 기출문제/모의고사 데이터에서 이미지와 정답 선지 pair를 BLIP 모델에 학습시킴
- Stage 1에서 생성된 이미지를 input으로 넣으면 정답 선지를 생성하는 BLIP 모델(BLIP-c)이 정답 선지 prompt를 생성
  - ex) Stage 1에서 생성된 이미지의 정답 선지로 'Some bicycles are parked near a wall' prompt 생성

2. 오답 선지 생성 (3개)
- 오답 선지는 총 3개 생성: 매력적인 오답 1개, 일반 오답 2개
- 1) 매력적인 오답 1개: 정답 선지와 특정 키워드는 동일/비슷하되, 오답인 선지
   - 이미지 & 오답 선지 paired dataset 활용: 오답 선지 중 가장 매력적인 오답 선별한 dataset
   - 토익 문제 이미지와 오답 중 가장 매력적인 오답 선지 pair를 BLIP 모델에 학습시킴
   - Stage 1에서 생성된 이미지를 input으로 넣으면 매력적인 오답 선지를 생성하는 BLIP 모델(BLIP-w)이 오답 선지 생성
    - ex) Stage 1에서 생성된 이미지의 매력적인 오답 선지로 'A bike is leaning against a column' prompt 생성
        
- 2) 일반 오답 2개
   - Action-effect 데이터 활용 
    - Caption & Negative_image_list (대상 (사물 or 사람)은 같으나 해당 caption으로 설명이 되지 않는 image) pair data로 구성
   - Action-effect 데이터로 학습시킨 후, Stage 1에서 생성된 이미지를 input으로 넣으면 랜덤한 오답 선지 2개 생성
    - ex) Stage 1에서 생성된 이미지의 오답 선지로 1) 'The door is no longer on the hinges', 2) 'A person sits on a bicycle and makes it move' prompts 생성

![image](https://user-images.githubusercontent.com/108797646/230771911-71e957f3-ac1b-4c26-883e-2b8dd40d5447.png)

## Stage 3. Validation
- CLIP 모델을 활용한 문제 평가
- CLIP 모델이 이미지를 보고 여러 개 보기 중 가장 이미지를 잘 설명하는 text를 골라줌
  - ex) image 2개와 보기 8개를 input으로 넣으면 각 이미지에 가장 적합한 보기를 퍼센트를 부여하여 골라줌
- 비슷한 선지는 50:50 확률을 부여하고, 중복 정답 처리가 될 가능성이 있는 선지들도 10 퍼센트 이상의 확률이 부여됨
- → 90% 이상의 확률이 부여된 선지만 정답 선지로 인정

![image](https://user-images.githubusercontent.com/108797646/230772471-a84ea54d-56e4-4545-a836-b6f06863a43b.png)

# Trial And Error
1) Multiple Datasets
  - 데이터셋의 종류와 조합(비례배분)에 따라 모델의 성능의 차이가 많이 남
  
2) GPT2 모델  
  - 단어 1개 / 단어 뭉치 / 문장 입력하면 이어서 문장 작성 (동일 주제 문장 생성) 가능
  - 하지만 유사한 의미의 문장은 생성해내지 못함
  - → BLIP으로 오답 선지 학습시켜 문장 생성해냄

# 프로젝트의 가치
- CV + NLP를 종합적으로 구현해볼 수 있는 기회
- 우리의 목적에 맞게 fine-tuning 해볼 수 있는 좋은 기회
- Prompt에서 image로, image에서 다시 prompt로 변환하는 과정을 거치는 모델 생성 
- TOEIC LC Part 1 문제를 자동으로 출제하는 모델 생성
