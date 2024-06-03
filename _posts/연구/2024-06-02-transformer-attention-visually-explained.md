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

개념적이해: 문장에서 하나의 word token에 대한 initial embedding vector는 dot product연산으로 이전 word값들을 참조하여 가중치가 반영된 벡터들의 합으로 업데이트 되면서 high dimensional space 상에서 이전 word들과 연관성있게 벡터의 방향과 값의 크기가 업데이트 됩니다.

inital embedding vector는 Attention Pattern에서는 하나의 column vector에 해당합니다.

Column vector(initial embedding vector) 1개를 Tower라 하고 아래를 생각해봅시다.

![tower](https://github.com/sandokim/sandokim.github.io/assets/74639652/db277a9c-ecf4-42ee-b92f-b6d50cfabdb5)

![tower (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/35ab9969-9823-4b39-87aa-38ffc1ddf84d)

"Tower"의 initial embedding vector와 "Eiffel"의 embedding vector와 dot product로 구한 가중치(Q, K 연산결과)만큼 "Tower"의 initial embedding vector를 "Eiffel"의 embedding vector 방향으로 high dimensional space상에서 이동합니다.

즉, 가중치가 반영된 벡터를 1개 더해서 "Tower"의 inital embedding vector를 "Eiffel"의 embedding vector 방향으로 업데이트가 됩니다.

--> 업데이트 되면서 "Tower"의 initial embedding vector가 high dimensional space 상에서 "Eiffel Tower" 벡터에 가까워지게 됩니다.

만약 문장이 "miniature Eiffel Tower"라면, "miniature"라는 context가 "Tower"에 주는 영향으로써 하나 더 추가되었다고 생각하면 됩니다. 즉, "Tower"의 initial embedding vector에 더해주는 context embedding vector가 하나 더 추가로 존재한다고 생각하시면 됩니다.

![tower (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/8a9f216d-658c-434a-95a5-e3cc09ca25db)

![tower (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/2e18fc62-e725-411c-b3af-80f18577d75d)

![tower (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/ef1c6fd7-34d7-4a6e-b979-e2eed7345ece)

이 내용을 Attention Pattern에서 보면, 위와 같은 initial embedding vector에서 더해주는 context에 해당하는 embedding vector들이 여러 개 존재한다고 볼 수 있습니다. (위의 예시에서는 더해주는 벡터가 1개, 2개인 경우만 visualize해서 본 것으로 생각하시면 됩니다.)

이번에는 "miniature Eiffel Tower"보다 긴 문장인 "a fluffy blue creature roamed the verdant forest" 문장에서 Attention을 이해해봅시다.

![creature](https://github.com/sandokim/sandokim.github.io/assets/74639652/f544cd4d-aabd-4fe1-8662-8018b29057b5)

creature의 initial embedding에 더해지는 임베딩 벡터들은, creature 앞에 해당하는 "a", "fluffy", "blue"에 대한 각각의 embedding vector이고, 

이후 모든 임베딩 벡터들은 cheating을 방지하기 위해 0의 가중치를 가지도록 attention pattern을 계산했습니다.

![creature (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/a06e8c4f-dd5c-410f-aac1-92e0419de1c8)

![creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/5489659a-ee02-456e-99ad-a1ff8cf8e022)

즉, creature의 initial embedding vector는 "a", "fluffy", "blue"의 임베딩 벡터와의 유사도를 dot product로 계산하고, 그 값의 크기들만큼 high dimensional space 상에서 "creature"의 initial embedding vector에 더해져 "creature"의 initial embedding vector가 "a fluffy blue creature"의 임베딩 벡터 방향으로 업데이트 되게 됩니다.

![creature (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/13e25c7b-b336-4f21-bb75-126675df8e37)

![creature (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/2261dfee-6e60-4b9f-a174-fc1357df7f2c)

이는 위의 간단한 miniature Eiffel Tower 예시와 동일하고, 단지 initial embedding vector에 더해지는 임베딩 벡터의 개수가 늘어, 더 많은 context를 고려하는 예시일 뿐이라고 이해할 수 있습니다. (문장이 길면 하나의 initial embedding vector에 더해지는 임베딩 벡터의 개수가 늘어난다는 의미로 해석가능합니다. 문장이 길면 전체 context를 고려하기 위해 더 많은 임베딩 벡터가 더해진다고도 해석할 수 있습니다.)

현재까지 모든 설명은 single head attention의 하나의 column vector(initial embedding vector)에 대한 것이었습니다.

![multi-headed attention](https://github.com/sandokim/sandokim.github.io/assets/74639652/8b2bee50-00fe-407d-9619-4dea700dc31a)

**[Single head attention]이 하나의 attention pattern을 가졌다면,**

**[Multi-headed attention]은 96개의 distinct한 attention pattern이 있는 것입니다.**

![multi-headed attention (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/d83d6611-0a47-4310-9127-31e55eb8b93f)

![multi-headed attention (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/86288fe1-43e5-48a6-bb24-e40542de4385)

![multi-headed attention (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/80ee6c3a-1bb2-4973-99fb-735fbed09552)

![multi-headed attention (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/39dd413b-8dc9-428a-b450-43eb9bab0a32)

![multi-headed attention (6)](https://github.com/sandokim/sandokim.github.io/assets/74639652/4985019a-f8a5-4127-a03c-01c700973fcc)

![multi-headed attention (7)](https://github.com/sandokim/sandokim.github.io/assets/74639652/6ee0268f-c8cb-48e7-ba3b-da998ac01394)

### "a fluffy blue creature roamed the verdant forest" 문장에서 Single head attention과 Multi-headed attention의 차이를 알아봅시다.

***"creature"에 대한 1개의 initial embedding vector (Attention Pattern에서 column vector 1개)에 대해서만 고려해봅시다.***

#### [Single head attention]
"creature"의 initial embedding vector와의 "a", "fluffy", "blue"의 embedding vector와의 dot product로 구한 가중치(Q, K 연산결과)만큼 "creature"의 initial embedding vector를 "a', "fluffy", "blue"의 embedding vector ***3개*** 방향으로 high dimensional space상에서 이동합니다.

--> 업데이트 되면서 high dimensional space 상에서 "creature"의 initial embedding vector가 이에 attend하는 "a", "fluffy", "blue"라는 context를 고려하는 "a fluffy blue creature"의 embedding vector에 가까워지게 됩니다.

#### [Multi head attention]
single head attention이 96개인 것입니다. 즉, Distinct한 Attention Pattern이 96개 있는 것입니다.

"creature"의 initial embedding vector와의 "a", "fluffy", "blue"의 embedding vector와의 dot product로 구한 가중치(Q, K 연산결과)만큼 "creature"의 initial embedding vector를 "a", "fluffy", "blue"의 embedding vector ***3개 x 96개*** 방향으로 high dimensional space상에서 이동합니다.

--> 업데이트 되면서 high dimensional space 상에서 "creature"의 initial embedding vector가 이에 attend하는 "a", "fluffy", "blue"라는 context를 고려하는 "a fluffy blue creature"의 embedding vector에 가까워지게 됩니다.

![multi-headed attention (7-2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/6f41b95f-279e-4e05-afd5-f77f5ee94a8a)

![multi-headed attention (8)](https://github.com/sandokim/sandokim.github.io/assets/74639652/05b3a4e0-8a0b-4f41-9dff-c77618fb3b61)

![multi-headed attention (9)](https://github.com/sandokim/sandokim.github.io/assets/74639652/ca7e3116-9bfc-4fc2-9d4b-53689167af8d)

![multi-headed attention (10)](https://github.com/sandokim/sandokim.github.io/assets/74639652/0dec06ba-98ec-4716-87ac-be51c7cc4a7c)

![multi-headed attention (11)](https://github.com/sandokim/sandokim.github.io/assets/74639652/8b4fa4a9-3ddc-4083-9958-d29eed22f373)


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


## 결론적으로 Attention을 통해 각 token의 embedding vector는 더 많은 context를 고려한 벡터로 변화하게 됩니다.

### Attention 예시 1)

![mole](https://github.com/sandokim/sandokim.github.io/assets/74639652/013f9073-d581-4644-a399-1cf8d52549c9)

![mole (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/fe34e50a-d2a4-46c3-9336-cf5a04aef604)

![mole (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/afc5c34d-b1a9-4669-8ab1-d9b26951d14c)

![mole (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/10064241-8c09-4544-a6b4-009264b15124)

![mole (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/d13de4ec-3594-4d80-8cd8-14236cb36967)

![mole (6)](https://github.com/sandokim/sandokim.github.io/assets/74639652/5eabf038-6315-4afe-9356-a21ac3de7fdd)

![mole (7)](https://github.com/sandokim/sandokim.github.io/assets/74639652/c4bef436-1116-443a-9c6a-4f84bba66401)

**또다른 예시로 "they crashed the car" 문장에서 "car"가 "they", "crashed", "the"의 context를 고려해서 바뀌는 것을 관찰할 수 있습니다.** 

![crashed car](https://github.com/sandokim/sandokim.github.io/assets/74639652/dc5e7234-d052-40b1-9d80-c0ee4f4cd948)

![crashed car (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/eebc375b-845e-4a0b-8df6-b437c5a389b6)

![crashed car (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/97e112bf-1726-44e1-a0b5-ff53940650ba)

**좀 더 긴 문장에서 띄엄띄엄 존재하는 context에 대해서도 Attention의 효과를 관찰할 수 있습니다.**

**"wizard", "Hogwarts", "Hermione"의 context를 반영한 "Harry"**

![Harry](https://github.com/sandokim/sandokim.github.io/assets/74639652/0886a024-43dd-4e02-a492-49bffa1ef4cb)

![Harry (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/2ab49654-153b-4995-bdeb-ece0fa2318c6)

**"Queen", "Sussex", "William", "Harry"의 context를 반영한 "Harry"**

![Harry (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/827a730d-c10a-463d-8e4c-755c9b79bb76)

![Harry (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/487445c0-e2fb-4095-996c-43e5ca306281)

### Attention 예시 2)

**"King"의 embedding vector는 context를 표현하는 "lived in Scotland", "murdererd predecessor", "in Shakespearean language"의 embedding vector들의 context를 반영하는 embedding vector로 업데이트 됩니다.**

![King context](https://github.com/sandokim/sandokim.github.io/assets/74639652/7a4eb91b-153e-45e7-9d4f-5e30e7a99aec)

1) "King"의 embedding vector와 "lived in Scotland", "murdered predecssor", "in Shakespearean language"의 embedding vector들 간의 계산은 "King"을 Query vector로, 나머지는 Key vector로하여 dot product로 벡터간의 유사도를 계산한 Attention Pattern을 구하고,

2) Attention Pattern의 가중치만큼 "lived in Scotland", "murdered predecessor", "in Shakespearean language"를 Value_down으로 인코딩한 각각 value embedding vector에 가중치를 주고,

3) "King"의 embedding vector에 더하여, "King"의 embedding vector가 context인 "lived in Scotland", "murdered predecessor", "in Shakespearean langauge"를 반영한 embedding vector로 업데이트 됩니다.

### Attention 예시 3)

Attention 예시 2)와 동일한 과정으로 이전에 들었던 "a fluffy blue creature roamed the verdant forest"에서 "creature"의 embedding vector에 context로 주어지는 "a", "fluffy", "blue"의 embedding vector로 인해 업데이트 되는 과정을 Attention Pattern에서 표현하면 아래와 같습니다.

![creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/5489659a-ee02-456e-99ad-a1ff8cf8e022)

![blue creature](https://github.com/sandokim/sandokim.github.io/assets/74639652/045b791e-809b-4000-a916-57a2aeb085ac)

![blue creature (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/3ef3d26e-2d10-442f-b03e-392ccb846c32)

![blue creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/3df0383e-cace-4de1-a87f-2fdf61e233ca)

이 과정을 high dimensional space 상에서 "creature"의 embedding vector에 "fluffy"의 Value embedding vector와 "blue"의 Value embedding vector가 더해져 "creature"의 embedding vector가 "blue fluffy creature"의 embedding vector로 업데이트되는 과정을 시각화 해보겠습니다.

**1) "fluffy"의 Value가 "creature"의 embedding vector에 더해지는 과정 시각화**

![fluffy creature](https://github.com/sandokim/sandokim.github.io/assets/74639652/4930ebb6-1b5a-444e-8627-97f2a7a0fb03)

![fluffy creature (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/d682e2f4-313f-481c-b960-9ba5c9760a91)

![fluffy creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/bb1f2e71-edf1-4028-a718-dc241a54d642)

![fluffy creature (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/f30f84e6-eaa6-434d-b131-4b1ce195bf11)

![fluffy creature (6)](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1ae7750-8776-4e08-a5a7-3be1c7b620e3)

![fluffy creature (7)](https://github.com/sandokim/sandokim.github.io/assets/74639652/f7bf8bad-8b40-4348-92f2-31d6a8dafa0a)

![fluffy creature (8)](https://github.com/sandokim/sandokim.github.io/assets/74639652/b7ce836e-1c38-4d90-8dd8-ec772c2c1408)

![fluffy creature (9)](https://github.com/sandokim/sandokim.github.io/assets/74639652/59a9f19f-9ef3-4400-a991-0807bf2df2a0)

**2) "blue"의 Value가 "fluffy creature"의 embedding vector에 더해지는 과정 시각화**

![blue fluffy creature](https://github.com/sandokim/sandokim.github.io/assets/74639652/5c43421c-1d1f-4939-aaca-971a4ab020ba)

![blue fluffy creature (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/bbcd644c-85db-46ad-a1fa-3e93af98aaaa)

![blue fluffy creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/cb6b9d03-cd7c-4ffa-a94a-fa0d5a978405)

***시각화를 위해 1), 2)로 나눠 순차적으로 Value가 더해지는 과정을 보았지만, 실제 Attention Pattern에서는 아래와 같이 "creature"의 embedding vector에 "fluffy", "blue"의 Value가 더해지는 과정이 한번에 이루어집니다.***

![creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/5489659a-ee02-456e-99ad-a1ff8cf8e022)

![blue creature](https://github.com/sandokim/sandokim.github.io/assets/74639652/045b791e-809b-4000-a916-57a2aeb085ac)

![blue creature (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/3ef3d26e-2d10-442f-b03e-392ccb846c32)

![blue creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/3df0383e-cace-4de1-a87f-2fdf61e233ca)


### Value Matrix 연산 과정

![Value matrix](https://github.com/sandokim/sandokim.github.io/assets/74639652/8eec9216-1245-4a9a-9784-ce709f6e15e6)

![Value matrix (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/f84080e1-27ce-4b1e-8dab-43084d1d7710)

![Value matrix (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/10558764-fd54-425c-b7ce-26abc31922fe)

Value matrix를 12,288 x 12,288로 사용할 수는 있지만 파라미터 수가 너무 많습니다.

![Value matrix (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/bcbd80db-4a48-4db1-901c-d4a9f65418b8)

우리는 Value matrix의 파라미터 수를 Query와 Key matrix의 파라미터 수의 합만큼으로 나타내고 싶습니다.

![Value matrix (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/8fd2c8ad-9482-4243-868d-319ca1234bcf)

![Value matrix (6)](https://github.com/sandokim/sandokim.github.io/assets/74639652/6f518b47-ae5e-4bd3-8fc2-f9878a800c78)

따라서 Query, Key matrix가 128 x 12,288 matrix의 파라미터를 가지므로, Value matrix를 128 x 12,288 matrix 2개를 연산한 것으로 쪼갭니다.

![Value matrix (7)](https://github.com/sandokim/sandokim.github.io/assets/74639652/af6f046c-96e9-485f-aaf6-654e0d698f24)

이렇게 쪼개진 matrix 2개를 각각 linear map을 한다고 생각하시면 됩니다.

![Value matrix (8)](https://github.com/sandokim/sandokim.github.io/assets/74639652/7b8af54f-6323-4456-828c-71755417003c)

앞서 high dimensional space상에 Value matrix를 통해 Value를 구했던 과정을 linear map으로 생각하면 됩니다.

![fluffy creature (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/faa00fd9-312c-465e-82c8-ce8a1c309585)

![fluffy creature (9)](https://github.com/sandokim/sandokim.github.io/assets/74639652/c29a0f7a-67f5-40e3-a663-a4e7798d80a1)

![Value matrix (9)](https://github.com/sandokim/sandokim.github.io/assets/74639652/f64631a7-08c9-4c94-87ab-d6a5d273817f)

![Value matrix (10)](https://github.com/sandokim/sandokim.github.io/assets/74639652/9380c822-5dcf-4259-986d-f63abffa0ad8)

12,288 dims을 128 dims로 linear map하는 matrix를 Value_down matrix라 해봅시다. (정식 명칭은 아님)

![Value matrix (11)](https://github.com/sandokim/sandokim.github.io/assets/74639652/4a235881-60e6-48f0-8509-6c462b31baa1)

![Value matrix (12)](https://github.com/sandokim/sandokim.github.io/assets/74639652/6fb68fb1-7624-4211-8d3d-1c608d7a7531)

![Value matrix (13)](https://github.com/sandokim/sandokim.github.io/assets/74639652/ba310646-8774-4f64-96d8-44e8f18e6433)

128 dims을 12,288 dims로 linear map하는 matrix를 Value_up matrix라 해봅시다. (정식 명칭은 아님)

![Value matrix (14)](https://github.com/sandokim/sandokim.github.io/assets/74639652/c81d3464-e9b9-4589-aa53-5796b76225cd)

결론적으로 여기서 수행하는 Linear map은 "Low rank" trnasformation과 같습니다.

![Value matrix (15)](https://github.com/sandokim/sandokim.github.io/assets/74639652/9ea3f36f-bea3-49e0-82e4-898843972cdd)

![Value matrix (16)](https://github.com/sandokim/sandokim.github.io/assets/74639652/c25a0e4f-1b44-41d1-b659-c673ec81ab1c)

![Value matrix (17)](https://github.com/sandokim/sandokim.github.io/assets/74639652/38c80133-a7d9-457d-908e-ded75c83522c)


### Value matrix를 linear map(Low rank transformation)으로 취급하여 나눴던 Value_up, Value_down matrix 중에서 Value_up만 따로 행렬로 묶습니다.

우리가 실제 코드 Implementation에서는 Query, Key, Value에서 Value 부분은 ***Value_down matrix***로 코딩이 되어 있고, 이 matrix가 ***128*** x 12,288 의 dimension을 가지게 됩니다.

그리고 ***Value_up matrix들은 Output Matrix로써 따로 분리되어 있습니다.***

![output matrix](https://github.com/sandokim/sandokim.github.io/assets/74639652/3d0c37e9-62bc-476d-a173-ee6a5194fa9b)

![output matrix (2)](https://github.com/sandokim/sandokim.github.io/assets/74639652/76f13997-2434-4061-8b74-cf3eb8e0a78c)

![output matrix (3)](https://github.com/sandokim/sandokim.github.io/assets/74639652/7bb5b87e-52bf-413e-8de5-02727a6bd887)

![output matrix (4)](https://github.com/sandokim/sandokim.github.io/assets/74639652/0117c7d6-15e3-41dc-af96-26234d9b1f93)

![output matrix (5)](https://github.com/sandokim/sandokim.github.io/assets/74639652/b14baf9c-ab40-46a1-836a-ea78ee55ed28)

![output matrix (6)](https://github.com/sandokim/sandokim.github.io/assets/74639652/c284e579-33ed-44ad-9833-22f24084ca59)

![output matrix (7)](https://github.com/sandokim/sandokim.github.io/assets/74639652/fc53e33d-1e52-4f01-8bca-a9821518c422)

![output matrix (8)](https://github.com/sandokim/sandokim.github.io/assets/74639652/b74b4776-6199-49e4-9a6e-41f5e2e97a76)

ML 사람들은 여러 개의 matrix를 하나의 single matrix multiplication으로 compress하는 것을 좋아하기 때문에, 이처럼 Value_up matrix와 Value_down matrix를 분리하여, **Value_up 행렬만 모아서 Output matrix로 만듭니다.**

![output matrix (9)](https://github.com/sandokim/sandokim.github.io/assets/74639652/73c5ef3f-cbec-4118-9210-24bab90a3d39)






