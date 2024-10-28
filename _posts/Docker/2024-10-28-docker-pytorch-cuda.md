---
title: "[Docker] Docker로 Pytorch CUDA 버전 맞춰진 container 생성하기"
last_modified_at: 2024-07-09
categories:
  - Docker
tags:
  - docker
  - push
  - pull
  - docker image
excerpt: "Docker로 pytorch와 cuda버전을 맞춰서 container를 생성해봅시다."
use_math: true
classes: wide
comments: true
---

> [pytorch/pytorch - Docker Image - Docker Hub](https://hub.docker.com/r/pytorch/pytorch)

Docker에서 container는 새로운 컴퓨터를 하나 판다고 생각하시면 편합니다.

따라서 docker image를 pull할 때, docker hub pytorch에서 pytorch/cuda 버전이 맞는 것을 입력해줍니다.

먼저 [docker hub](https://hub.docker.com/r/pytorch/pytorch)로 들어가서 Tags 탭으로 가줍니다.

![image](https://github.com/user-attachments/assets/7be81276-aed4-4cd3-8647-0bad90ada094)

![image](https://github.com/user-attachments/assets/695e43a7-15f2-4c4e-8359-20c2fca7af15)

현재 project에서 필요한 pytorch version / cuda version을 확인해줍니다. (예시에선 pytorch 1.12.1, cuda 11.3을 사용했습니다.)

![image](https://github.com/user-attachments/assets/4141c5cf-db58-40b3-b70e-a45a56318f1e)

TAG에서 순서는 pytorch version / cuda version / cudnn version 순으로 명시되어있으며, devel이 붙은 것을 사용해야합니다. (runtime이 붙은 것은 제약이 있습니다.)

![image](https://github.com/user-attachments/assets/184d3436-7a3b-47fb-a653-3ba3c980cc3f)

원하는 TAG를 찾았으면 copy를 해줍니다.

![image](https://github.com/user-attachments/assets/ae3abf89-2dab-463e-b963-dbad274e7805)

copy된 다음 command를 docker를 설치한 VScode에서 커맨드를 입력하면 docker image가 다운로드 됩니다.

```python
docker pull pytorch/pytorch:1.12.1-cuda11.3-cudnn8-devel
```

docker images로 docker pull로 내려받은 pytorch version / cuda version을 확인해줍니다.

![image](https://github.com/user-attachments/assets/569c62e2-2b3f-4171-8852-570f09517e14)

여기서 IMAGE ID의 앞 3글자를 사용하여 container를 생성할 것입니다. (여기서 IMAGE ID 앞 3글자는 `fa5`입니다.)

![image](https://github.com/user-attachments/assets/f5016b9a-c4fa-4cf4-acf6-3b16b3c308ee)

container를 생성시 다음과 같은 커맨드를 입력합니다. 

- `--name`으로 생성되는 container 이름을 정해줄 수 있습니다. (예시에선 CoR-GS로 NAMES를 정했습니다.)
- `-v`는 현재 서버의 directory를 생성할 container에 dircectory에 mount할 경로를 정해줍니다.

```python
docker run -it --gpus all --net=host --pid=host --ipc=host -v /mai_nas:/mai_nas --name CoR-GS fa5 /bin/bash
```

docker run으로 container를 실행시 다음과 같이 들어가집니다.

![image](https://github.com/user-attachments/assets/4033a939-8bdf-4a23-92fc-36dc58bbd705)

터미널을 하나 더열고 실제 container가 생성된 것을 확인합니다. NAMES에 CoR-GS 이름을 가진 container가 생성되었음을 확인할 수 있습니다.

![image](https://github.com/user-attachments/assets/671cd559-2879-463a-b868-9f69b1e10d68)

생성된 container는 CONTAINER ID를 가지고 CONTAINER ID의 앞 3글자로 container를 중지시키거나, 제거할 수 있습니다.

- container를 제거할 때는
  - 현재 `container` 내에서 `exit`로 나와 `container`를 종료시키거나
  - 외부 터미널에서 `docker stop [CONTAINER ID 앞 3글자]`로 종료시킨다음
- `docker rm [CONTAINER ID 앞 3글자]`로 제거해줄 수 있습니다.

![image](https://github.com/user-attachments/assets/827e24cd-c32a-4d35-bdd3-b3f7a0444a44)

- `docker stop 97b`
- `docker rm 97b`

container가 처음 생성되었을 시에는 conda를 실행해주어야 conda 사용할 수 있습니다.

아래명령어를 입력해줍니다.

```terminal
conda init bash
source ~/.bashrc
```  

![image](https://github.com/user-attachments/assets/ad79305d-93b3-47c5-ab2b-4c3e3e31bb16)

이제 모든 세팅이 끝났습니다. 현재 container의 cuda version / pytorch version을 확인해봅시다.

- nvcc -V (cuda version이 11.3인지 확인)
- pip list (torch version 1.12.1인지 확인)

![image](https://github.com/user-attachments/assets/410a457b-4a19-4ca4-8c3b-30af4f7cea57)

![image](https://github.com/user-attachments/assets/d3e42c35-9836-496d-a0d6-a0921338bbbd)

### python version까지 맞춰야하는 경우에는 생성한 container에서 conda로 가상환경을 만들어서 사용해줍니다.

예제에서 생성한 container의 python version이 3.7.13 입니다.

![image](https://github.com/user-attachments/assets/396fdb42-17c5-4500-af4d-c3d08390fdbf)

하지만 project에서 사용하는 python version은 3.8.1 입니다.

![image](https://github.com/user-attachments/assets/ba27fab7-e8a1-487f-81f5-d7f4010ae637)

이 경우 python version이 달라서 library가 설치가 제대로 안될 수 있습니다.

이때는 현재 container에서 다음과 같이 python 3.8.1 version의 가상환경을 만들어서 사용합시다.

```python
conda create -n CoR-GS python=3.8.1
conda activate CoR-GS
```

**주의할 점**: **만들어진 가상환경은 torch가 깔려있지 않은 상태**이므로 제일 먼저, cuda version / torch version이 맞는 것을 [pytorch org previous versions](https://pytorch.org/get-started/previous-versions/)에서 찾아서 설치해줍니다.

![image](https://github.com/user-attachments/assets/bb986982-673b-4e52-a732-3f79c2b55d5b)

`environment.yml`의 나머지 모듈들은 requirements.txt에 붙여넣고 설치하는 방식을 사용하면 됩니다.

![image](https://github.com/user-attachments/assets/9d259f1e-93f1-4832-ad42-292d099837c9)

아래와 같이 requirements.txt 파일을 만들고 다음 커맨드를 실행하여 관련 모듈을 설치합니다.

```python
pip install -r requirements.txt
```

![image](https://github.com/user-attachments/assets/f868c17d-79fb-4295-8ef7-ee373a0e4240)

마지막으로 submodules 설치에 대해 알아보겠습니다. 설치해야하는 submodules는 아래와 같이 2개입니다.

![image](https://github.com/user-attachments/assets/877ffff2-cb94-4e54-9cbf-a48ea810bdee)

submodules는 직접 디렉토리로 이동하여 개발모드로 설치해줍니다.

```terminal
cd submodules/diff-gaussian-rasterization-confidence
pip install -e .
cd submodules/simple-knn
pip install -e .
```

![image](https://github.com/user-attachments/assets/283bcbb0-b418-4baa-a9ea-ebab12348152)


긴글 읽어주셔서 감사합니다.

