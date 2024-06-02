---
title: "[CV 연구] Transformer Attention Visually Explained"
last_modified_at: 2024-06-02
categories:
  - 연구
tags:
  - 연구
  - Attention
  - Attention Pattern
  - Attention Mechanism
  - Transformer
  - GPT3
  - Language Model
  - 3Blue1Brown
  - visually explaned
excerpt: "Transformer의 Attention을 시각적으로 이해해봅시다."
use_math: true
classes: wide
---

[[But what is a GPT? Visual intro to transformers Chapter 5, Deep Learning](https://www.youtube.com/watch?v=wjZofJX0v4M)]

[[Attention in transformers, visually explained Chapter 6, Deep Learning](https://youtu.be/eMlx5fFNoYc?si=fJCtjEPUpt20zht_)]

본 포스트는 3Blue1Brown 영상을 참고하여 개인적으로 정리한 글임을 밝힙니다.

### Attention Pattern: Q, K dot product한 matrix

[Single head attention]이 하나의 attention pattern을 가졌다면,
[Multi-headed attention]은 96개의 distinct한 attention pattern이 있는 것입니다.

개념적이해: 문장에서 하나의 word token에 대한 initial embedding vector는 dot product연산으로 이전 word값들을 참조하여 가중치가 반영된 벡터들의 합으로 업데이트 되면서 high dimensional space 상에서 이전 word들과 연관성있게 벡터의 방향과 값의 크기가 업데이트 됩니다.

inital embedding vector는 Attention Pattern에서는 하나의 column vector에 해당합니다.

Column vector 1개를 Tower라 하고 아래를 생각해봅시다.

### [Single head attention]
Tower의 initial embedding vector와 Eiffel의 embedding vector와 dot product로 구한 가중치(Q, K 연산결과)만큼 Tower의 initial embedding vector를 Eiffel의 embedding vector 방향(Value 벡터)으로 high dimensional space상에서 이동합니다.

즉, 가중치가 반영된 벡터를 1개 더해서 Tower의 inital embedding vector를 Eiffel embedding vector 방향으로 업데이트가 됩니다.

--> 업데이트 되면서 high dimensional space 상에서 Eiffel Tower 벡터에 가까워지게 됩니다.

### [Multi head attention]
single head attention이 96개인 것이고, 즉 Distinct한 Attention Pattern이 96개 있는 것입니다.
다시말해, 가중치가 반영된 벡터를 96개 더해서 Tower의 inital embedding vector를 Eiffel embedding vector 방향으로 업데이트가 됩니다.

--> 업데이트 되면서 high dimensional space 상에서 Eiffel Tower 벡터에 가까워지게 됩니다.

### Embedding Space, Attention Pattern, Softmax, Masking

Query, Key는 먼저 사전의 정의된 Embedding Matrix를 거쳐 initial Embedding Space 상으로 보냅니다. 그리고, 임베딩 벡터들은 initial Embedding Space 상에서 Attention Pattern에서 구하는 임베딩 벡터만큼 더해져 더 많은 context를 아우르는 임베딩 벡터로 이동하게 됩니다.

![embedding space](https://github.com/sandokim/sandokim.github.io/assets/74639652/ca890719-1d1e-48d3-9b85-4ca7ca171d35)

![QK](https://github.com/sandokim/sandokim.github.io/assets/74639652/6e08198f-098a-41b6-83c7-c3e914200247)

![QK2](https://github.com/sandokim/sandokim.github.io/assets/74639652/32a0ace1-58da-4e68-8233-29a79dfc2dd4)

![QK3](https://github.com/sandokim/sandokim.github.io/assets/74639652/78a41131-be87-4774-a14b-a60f8a3e5847)

Query, Key에 대해 dot product를 취하여, dot product 결과의 크기를 회색원의 크기로 표현하면 아래와 같습니다.

![Attention Pattern](https://github.com/sandokim/sandokim.github.io/assets/74639652/44f8af48-6081-4199-9e0e-2e275c0076bc)

Query, Key의 연산 결과를 Attention Pattern이라고 하며, 연산 자체의 결과는 Logits으로 표현됩니다.

![logit1](https://github.com/sandokim/sandokim.github.io/assets/74639652/2b56836b-33d6-4a37-bcb7-81f4f5ca6dbc)

![logit2](https://github.com/sandokim/sandokim.github.io/assets/74639652/05bc8dd2-39c2-42df-afba-8ec1a92dbbac)

우리는 Probability에 대해 다루고 싶으므로 Logits을 softmax 함수를 거쳐 확률값으로 변환해줍니다.

Softmax를 거친 Logits들의 합은 1로 변화합니다. 

![Softmax](https://github.com/sandokim/sandokim.github.io/assets/74639652/79b73c71-5a44-44e4-b3f0-3451d6b08b65)

![Softmax(2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/d6b173c1-4f2c-4341-aa7e-95337092cde9)

(이때, softmax의 temperature 값으로 가장 큰 probability의 값의 contribution 정도를 조절할 수 있습니다. t=0이면 제일 큰 softmax 값이 100%가 되고 나머지는 0%가 되도록 scaling이 조절되고, t가 커질수록 제일 큰 softmax 값의 기여도가 줄어들도록 확률값을 조절할 수 있습니다.)

Attention Pattern은 Attention is All you need 페이퍼의 수식과 대응하여 보면 아래와 같습니다.

![attention softmax](https://github.com/sandokim/sandokim.github.io/assets/74639652/dfc171e5-639c-4feb-8725-f4644a461a6e)

이때 numerical stability를 위해 d_k를 나누는 작업이 추가되었습니다.

![attention softmax2](https://github.com/sandokim/sandokim.github.io/assets/74639652/dd218214-8563-493c-80aa-a2747296a0ab)

Attention Pattern을 계산할 때, 현재값 이후의 token을 Attend하는 것은 cheating에 해당하므로 이를 방지하기 위해, 해당 token들에 대해서는 masking을 하는 과정이 추가되어야 합니다. 이는 그 부분에 해당하는 값을 -∞로 바꾸고, softmax를 통과시켜 확률값을 0%로 바꾸는 것으로 해당 token부분에 대해서 masking을 수행할 수 있습니다.

![masking](https://github.com/sandokim/sandokim.github.io/assets/74639652/e01ebd32-0dc3-4b46-b0bd-9d5f02e1a259)

![masking (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/753a94be-7693-4378-82c3-10443e5404fe)

![masking (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/6ace54a2-0ec1-45de-a483-9f434623c3a9)

![masking (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/24070f84-bbdf-4839-b46a-43e283210e1d)

![masking (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/91ad4dbd-471c-47af-9fbf-529829319e50)

![masking (6)](https://github.com/sandokim/sandokim.github.io/assets/74639652/91090752-cfb2-44fe-81dc-985ed80ebb38)

![masking (7)](https://github.com/sandokim/sandokim.github.io/assets/74639652/60784887-712c-431e-8db1-7fe8a3e61732)









