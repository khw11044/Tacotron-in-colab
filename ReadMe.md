#Tacotron1과 Tacotron2 Colab에서 실행하기

[hcco2님의 코드](https://github.com/hccho2/Tacotron2-Wavenet-Korean-TTS)

위 코드를 코랩에서 실행하기위해 약간만 바꾸어 올려놓았습니다.

파일을 다운받고 구글드라이브에 원하는 위치에 넣어둡니다.

코드가 들어있는 colab은 각각 tacotron1,tacotron2 폴더에 있습니다. (Tacotron1.ipynb, Tacotron2.ipynb)

## 공통 작업

구글 마운트  
> from google.colab import drive
drive.mount('/content/drive')

타코트론 폴더로 이동

> 예 : cd drive/MyDrive/Tacotroncolab/tacotron1

텐서플로 1 다운  
> pip install tensorflow-gpu==1.8  
> %tensorflow_version 1.x  
> import tensorflow as tf  
> print(tf.__version__)  

필요 라이브러리 다운
> !pip install jamo  
> !pip install Unidecode


## Tacotron1 Training

손석희님의 목소리의 경우  
> python preprocess.py --num_workers 8 --name son --in_dir .\datasets\son --out_dir .\data\son

문제인 대통령님의 목소리의 경우  
> python preprocess.py --num_workers 8 --name moon --in_dir .\datasets\moon --out_dir .\data\moon

학습
> !python train_tacotron2.py --data_paths ./data/son --batch_size 32

data_paths와 batch_size는 파라메타로 알아서 수정

중간에 끊겨서 다시 학습하는 경우   
> !python train_tacotron2.py --data_paths ./data/son --batch_size 32 --load_path logdir-tacotron/son_2021-02-06_23-59-17

load_path도 마찬가지로 폴더를 잘확인하고 넣어줄것

colab 연결 끊김 방지

F12를 눌러서 아래 코드 넣기

~~~
function ClickConnect(){
    console.log("코랩 연결 끊김 방지");
    document.querySelector("colab-toolbar-button#connect").click()
}
setInterval(ClickConnect, 60 * 1000)
~~~

마무리 : 원하는 text 이용해서 음성 합성  
> !python synthesizer.py --load_path logdir-tacotron2/son_2021-02-06_23-59-17 --num_speakers 1 --speaker_id 0 --text "김현우 화이팅"

손석희님의 경우 speacker_id가 0
문제인님의 경우 speacker_id가 1

## Tacotron2 Training

tacotron2도 위와 같으며 vocoder인 wavenet학습의 경우  
> !python train_vocoder.py --data_dir ./data/son --logdir ./logdir-wavenet/train/2021-02-08T14-52-11

tacotron2와 wavenet 합치기
> !python generate.py --mel ./logdir-wavenet/mel-moon.npy --gc_cardinality 2 --gc_id 0 ./logdir-wavenet/train/2018-12-21T22-58-10
