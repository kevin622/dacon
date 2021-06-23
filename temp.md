# Translate 과정 Flow에 대해 설명해주세요

참고 : https://towardsdatascience.com/language-translation-with-rnns-d84d43b40571

- 문맥적 정보(contextual information)를 해석하기 위해 RNN 기반의 모델을 사용해야 함
- 즉 앞의 단어가 다음에 나올 단어와 연관이 있다!

![RNN structure](http://karpathy.github.io/assets/rnn/diags.jpeg)

1. **Preprocessing**: load and examine data, cleaning, tokenization, padding
2. **Modeling**: build, train, and test the model
3. **Prediction**: generate specific translations of English to French, and compare the output translations to the ground truth translations
4. **Iteration**: iterate on the model, experimenting with different architectures



## Preprocessing

1. Cleaning : remove HTML tags, remove stop words, remove punctuation or convert to tag representations, label the parts of speech, or perform entity extraction
2. Tokenization : 각 단어에 index 부여
3. Padding : 가장 긴 문장의 길이가 되도록 모든 문장의 길이를 맞춤(없는 단어 수만큼 0을 추가한 벡터 사용)
4. One Hot Encoding :  Integer를 Vector로 바꾸기 위해 사용하기도 함(단어의 수가 적을 경우)

## Modeling

1. Input : 각 단어가 input 으로 들어옴
2. Embedding Layer : 각 단어를 하나의 벡터로 변환(학습 필요, word2vec에 이미 학습된 벡터가 있기는 함)
3. Encoder(Recurrent Layer) : 현재 단어 벡터에 이전 단어들의 context(Recurrent Layer의 Output)를 적용
4. Decoder(Recurrent Layer) : 이전 예측 단어들을 적용
5. Output : 새로운 단어가(문장이) output

![encoder decoder](https://miro.medium.com/max/2772/0*hZ4r1yyTEf2CqoNZ.png)

## 심화

1. Bidirectional Layer: 양방향층, 문장을 해석할 때 각 단어의 앞에서 나온 단어 뿐 아니라 뒤에 나오는 단어도 이용하기 위해 사용, 즉 전체 문맥을 활용하기 좋음

![bidirectional layer](https://miro.medium.com/max/700/0*IYLXY8Dxvw8rbd8E.png)

2. Hidden Layer(Gated Recurrent Unit) : 문장의 모든 정보를 다 사용하는 것이 아니라 일부 정보만 선택적으로 사용하기 위해, 1. 단어가 Layer로 전달되기 전 2. state(Recurrent Layer의 output)가 다음 Layer로 전달되기 전 그 정도를 계산하는 Gate를 사용

![GRU](https://miro.medium.com/max/700/0*FvPLILqWS2FWD-98.png)

# 요약

1. 데이터 전처리
    1. 중요하지 않은 정보 삭제(stopwords, 기호 등), 가능하면 entity extraction(중요정보 추출)까지
    2. 단어 토큰화, 가능하면 단어 벡터화(One Hot Encoding 등)
    3. 문장 길이를 맞추기 위해 Padding 사용하기도 함
2. 모델링
    1. 문맥적 정보를 활용하기 위해 RNN 기반 모델 사용
    2. 단어가 벡터화되지 않았다면 Embedding Layer 추가
    3. 필요에 따라 Bidirectional Layer, GRU 사용 