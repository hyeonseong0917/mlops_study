# Data and Model Management

- DVC: git과 연동하여 소스코드와 데이터의 버전을 함께 관리하기 위한 툴
    - 대부분의 스토리지(AWS S3, Google Drive)등과 호환
    - Gitlab, Bitbucket과 같은 git 호스팅 서버와 연동 가능
    - Data Pipeline을 DAG로 관리
    - Git과 유사한 인터페이스
- DVC 저장 방식
    - model.pkl의 메타데이터를 .dvc파일에 저장
    - .dvc파일에 실제 데이터 스토리지 위치, 메타데이터 정보가 담겨있음
    - 이러한 .dvc파일만 git에 저장
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bf4ff75-bd87-4567-ba52-b1faed3fe3ab/4753fb4f-a774-4b34-a51c-c68e56762305/Untitled.png)
    

### DVC 실습

- DVC 설치: git 설치 후 pip install dvc[all]=2.6.4

### DVC 저장소 세팅

- 원하는 디렉토리에 이동해서 git 저장소로 초기화 git init후 dvc init
- Google Drive의 id값을 통해 default remote storage로 지정
    
    ```bash
    dvc remote add -d storage s3://<bucket_name>/<folder_name>/
    ```
    

### DVC config를 git commit

```bash
git add .dvc/config
git commit -m "add remote storage"
dvc push
```

### DVC Cache 삭제

```bash
rm -rf .dvc/cache/
```

### DVC Pull: 데이터를 remote storage로부터 다운로드

```bash
cd dvc-tutorial

# 기존 dvc 캐시를 삭제
rm -rf .dvc/cache
# 이전에 dvc push했던 데이터를 삭제
rm -rf data/demo.txt

# dvc pull을 통해 s3에 올렸던 데이터 가져오기
dvc pull
```

### DVC Checkout

- data의 버전을 변경

```bash
# demo.txt.dvc 파일을 이전 commit 버전으로 되돌림
git checkout <COMMIT_HASH> data/demo.txt.dvc
dvc checkout
```

### Model Management

### mlflow

- tracking: 모델의 버전 정보, 메타데이터 관리
- projects: pytorch 버전, 도커 버전 등을 패키징
- models: 통일된 형태로 배포될 수 있도록 포매팅
- model registry: mlflow로 시연했던 모델을 쉽게 서빙할 수 있는 저장소

### mlfow 구조

- server-client 구조
    - server: 로컬 파일 시스템 등을 이용해 클라이언트가 저장해 달라고 요청한 모델 관련 메타정보를 백엔드 스토리지에 저장
    

### Python 가상 환경 생성

```bash
python -m venv venv
source venv/bin/activate
```

### mlflow 설치

```python
pip install mlflow
```

### mlflow ui

```python
mlflow ui --host 0.0.0.0 --port 5000 // 외부 ip로 접속 시 host와 port 설정 필요
```

### scikit-learn에서 제공하는 당뇨병 예측

- 데이터를 이용해 모델을 학습시키고, predict 한 뒤 evaluation metric을 MLflow에 저장

```bash

wget https://raw.githubusercontent.com/mlflow/mlflow/master/examples/sklearn_elasticnet_diabetes/linux/train_diabetes.py
```

- train_diabetes.py 파일에서 mlflow.set_tracking_uri("[http://](http://44.200.169.24:5000/)mlflow서버 주소") 추가
- 터미널에서 export MLFLOW_TRACKING_URI=”http://mlflow서버 주소”

### 데이터 저장 방식

- mlruns의 0디렉토리 하위에서 진행했던 실험에 대한 파라미터, 메트릭에 대한 정보 확인 가능

### 메모리 최적화

```bash
ps aux --sort=-%mem

killall -9 mysql

sudo apt-get remove --purge mysql*

sudo rm -rf /etc/mysql /var/lib/mysql
sudo rm -rf /var/log/mysql
sudo rm -rf /var/log/mysql.*
sudo rm /var/lib/dpkg/info/*
sudo apt-get autoremove
sudo apt-get autoclean
sudo apt-get clean
sync
```

fgfddgdfsfdx

메모리 비우기

pyenv,bz2,virtualenv 설치

### pyenv

```bash
curl https://pyenv.run | bash

echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc

echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc

echo 'eval "$(pyenv init -)"' >> ~/.bashrc

echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc

source ~/.bashrc
```

### bz2

```bash
sudo apt-get update
sudo apt-get install libbz2-dev
export PYTHONPATH=$PYTHONPATH:/home/ubuntu/.pyenv/versions/3.10.12/lib/python3.10
```

### virtualenv

```bash
pip3 install virtualenv

export PYTHONPATH=$PYTHONPATH:/home/ubuntu/environment/venv/bin/python
```

### MLflow를 사용한 서빙

```bash
// pyenv가 설치되어 있지 않다면 pyenv설치 사전 진행
mlflow models serve -m {$pwd}/mlartifacts/0/진행한 실험 번호/artifacts/model -p 1234
mlflow models serve -m /home/ubuntu/environment/mlflow-test/mlartifacts/0/70513fb242774e8995c18c348e0a1657/artifacts/model --host=0.0.0.0 --port=1234
```

### Predict API 요청

request body에 포함될 데이터 형식을 알아야 함

```python
data = load_diabetes()
print(data.feature_names) 

df = pd.DataFrame(data.data)
print(df.head())

print(data.target[0])
```

```python
['age', 'sex', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6'] // df에서 열 이름 반환
          0         1         2         3         4         5         6  \
0  0.038076  0.050680  0.061696  0.021872 -0.044223 -0.034821 -0.043401   
1 -0.001882 -0.044642 -0.051474 -0.026328 -0.008449 -0.019163  0.074412   
2  0.085299  0.050680  0.044451 -0.005671 -0.045599 -0.034194 -0.032356   
3 -0.089063 -0.044642 -0.011595 -0.036656  0.012191  0.024991 -0.036038   
4  0.005383 -0.044642 -0.036385  0.021872  0.003935  0.015596  0.008142   

          7         8         9  
0 -0.002592  0.019908 -0.017646  
1 -0.039493 -0.068330 -0.092204  
2 -0.002592  0.002864 -0.025930  
3  0.034309  0.022692 -0.009362  
4 -0.002592 -0.031991 -0.046641  
 
151.0 // data.target[0] 결과
```

```bash
curl -X POST -H  "Content-Type:application/json" http://3.236.50.113:1234/invocations --data '{
    "dataframe_split": {
        "columns":["age", "sex", "bmi", "bp", "s1", "s2", "s3", "s4", "s5", "s6"],
        "data":[[0.038076, 0.050680,  0.061696,  0.021872, -0.044223, -0.034821, -0.043401, -0.002592,  0.019908, -0.017646]]
    }
} '
```

```bash
{"predictions": [153.20093644395587]}
```

http://3.236.50.113:1234/invocations에 API를 보내서 predict() 함수를 실행