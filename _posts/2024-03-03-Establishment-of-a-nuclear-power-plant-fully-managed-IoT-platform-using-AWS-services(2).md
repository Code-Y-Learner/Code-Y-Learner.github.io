---
layout: post
title: aws 서비스를 활용한 관리형 iot 플랫폼 구축(2)
subtitle: aws iot core 가상 센서 환경 구축 및 웹 관리 콘솔
categories: AWS
description: AWS 서비스를 활용한 원전 관리형 IoT 플랫폼 구축 (Establishment of a nuclear power plant fully managed IoT platform using AWS services)
tags: aws iot miniproject
---

# 센서 환경구축

## 1. 센서 데이터를 만들 가상환경 구축

 발전소의 센서데이터를 aws-iot-core로 보내면서 각 센서들은 독립적이면서도 보내는 데이터는 서로 긴밀히 연결되며 이것들을 한번에 관리할 수 있는 웹 관리 콘솔까지 구축을 해야합니다.
  
>발전요구량 : MWh<br>
>발전량 : MWh<br>
>제어봉 높이 : m<br> / (총 7개 종류 위치가 다름)
>파이프 : MPa(압력), °C(온도) / (총 6개의 종류 위치가 다름)<br>
>터빈 : rpm(회전속도)<br>
>날씨 : °C(온도), %(습도), mm(강수량), m/s(풍속) 

 이러한 총 17개의 센서를 구축하게 됩니다. 이를 각각의 VM을 통해 구성하고 이를 Master VM을 통해 각각의 VM을 관리하게 될 것입니다.
 
 ![Untitled](/assets/images/2024-03-03/vm-arch1.png)

 * 온프레미스 서버를 이용해 VM환경을 구축하자

 ![Untitled](/assets/images/2024-03-03/onpre-1.png)

 온프레미스 서버를 이용하여 VM을 구축하고 이를 활용할 계획입니다. 네트워크 연결을 위해 ip 고정과 같은 네트워크 설정과 호스트네임을 설정하고 호스트네임을 통해 각기 다르게 동작하는 방식으로 구현할 예정이고 복제가 쉽도록 모든 게 준비되어있는 VM을 만들예정입니다.

## 2. AWS-SDK를 이용한 aws-iot-core MQTT 통신연결

 * AWSIoTPythonSDK.MQTTLib 라이브러리를 사용해 MQTT 연결은 다음과 같습니다.

```python
from AWSIoTPythonSDK.MQTTLib import AWSIoTMQTTClient
import json

# AWS IoT Core에 연결하기 위한 설정
host = <Iot core endpoint>
root_ca_path = <.pem 인증서 파일 경로>  # Root CA 파일의 경로
certificate_path = <.pem.crt 인증서 파일 경로>  # 인증서 파일의 경로
private_key_path = <.pem.key 인증서 파일 경로>  # 개인 키 파일의 경로
client_id = <your-iot-ID>  # 클라이언트 ID
topic = "$aws/things/"+client_id+"/sesac3/sensortype/pipe/sensorId/"+client_id  # 토픽

# AWS IoT Core에 연결
client = AWSIoTMQTTClient(client_id)
client.configureEndpoint(host, 8883)
client.configureCredentials(root_ca_path, private_key_path, certificate_path)
client.connect()

# 차례대로 topic, message, qos(품질 ex 1이면 무조건 1번이상 전송)
client.publish(topic, json.dumps(data), 1)
```

관련한 서비스 연결방법은 [aws-공식문서](https://repost.aws/knowledge-center/iot-core-publish-mqtt-messages-python)에서 확인하실수 있습니다.


>[다음포스트](https://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)에서 더 정확한 구현방법과 연결 방법을 설명하겠습니다.

## 온프레미스 Iot 센서 환경 구축(Worker VM)
이제 모든 준비가 끝났습니다. 온프레미스에서 master VM을 만들고 관련한 iot core와 연결해 센서를 송수신할 파일을 미리 만들어둡니다. 예시로 pipe의 연결을 보겠습니다.

>```python
>from AWSIoTPythonSDK.MQTTLib import AWSIoTMQTTClient
>import socketio
>import json
>import time
>import random
>from datetime import datetime
>from pytz import timezone
>
># AWS IoT Core에 연결하기 위한 설정
>host = <Iot core endpoint>
>root_ca_path = <.pem 인증서 파일 경로>  # Root CA 파일의 경로
>certificate_path = <.pem.crt 인증서 파일 경로>  # 인증서 파일의 경로
>private_key_path = <.pem.key 인증서 파일 경로>  # 개인 키 파일의 경로
>client_id = "pipe1"  # 클라이언트 ID
>topic = "$aws/things/"+client_id+"/sesac3/sensortype/pipe/sensorId/"+client_id  # 토픽
>
>
>with open("sequence.txt", "r") as file:
>    global index
>    data_read = file.read()
>    index = int(data_read)
>
># AWS IoT Core에 연결
>client = AWSIoTMQTTClient(client_id)
>client.configureEndpoint(host, 8883)
>client.configureCredentials(root_ca_path, private_key_path, certificate_path)
>client.connect()
>
># 마스터 vm 의 ip 주소
>master_ip = <your masterVM ip>
>sio = socketio.Client()
>pipe = {
>    "pipe1": {
>        "min": {"pre": 15.60405, "tem": 315},
>        "max": {"pre": 15.705375, "tem": 327}
>    },
>    "pipe2": {
>        "min": {"pre": 15.60213, "tem": 275},
>        "max": {"pre": 15.705265, "tem": 288}
>    },
>    "pipe3": {
>        "min": {"pre": 6.2672, "tem": 150},
>        "max": {"pre": 6.2672, "tem": 150}
>    },
>    "pipe4": {
>        "min": {"pre": 6.2795, "tem": 270},
>        "max": {"pre": 6.586125, "tem": 280}
>    },
>    "pipe5": {
>        "min": {"pre": 0.202265, "tem": 7.7},
>        "max": {"pre": 0.202265, "tem": 7.7}
>    },
>    "pipe6": {
>        "min": {"pre": 0.212032, "tem": 53},
>        "max": {"pre": 0.212146, "tem": 55}
>    },
>}
>
>
>pipe_loc = {
>    "pipe1" : "1stin",
>    "pipe2" : "1stout",
>    "pipe3" : "2ndin",
>    "pipe4" : "2ndout",
>    "pipe5" : "3rdin",
>    "pipe6" : "3rdout"
>}
>
>@sio.on('signal')
>def on_signal(data):
>    send_data(data)
>
># 데이터 생성 및 전송
>def send_data(signal):
>    global index
>    pgr = float(signal['master_data'])
>    max_pre = pipe[client_id]["max"]["pre"]
>    min_pre = pipe[client_id]["min"]["pre"]
>    rand1 = 0.001
>    pressure = (pgr-55614.4356164384)/14153.2383561643*(max_pre - min_pre)+min_pre
>    pressure *= random.uniform(1 - rand1, 1 + rand1)
>
>    max_tem = pipe[client_id]["max"]["tem"]
>    min_tem = pipe[client_id]["min"]["tem"]
>    temperature = (pgr-55614.4356164384)/14153.2383561643*(max_tem - min_tem)+min_tem
>    rand2 = 0.002
>    temperature *= random.uniform(1 - rand2, 1 + rand2)
>
>    height = round(rod[client_id], 7)
>
>data = {
>        'location': pipe_loc[client_id],
>        'sensorid' : client_id,
>        'timestamp' : datetime.now().timestamp(),
>        'sequencenumber' : index,
>        'data' : {
>            'pressure' : 15.1293 if (pressure < 15.1293) else round(pressure,7),
>            'temperature' : round(temperature,7)
>            }
>    }
>    index += 1
>    client.publish(topic, json.dumps(data), 1)
>
>    with open("sequence.txt", "w") as file:
>        file.write(str(index))
>
>sio.connect(f'http://{master_ip}:5000')
>sio.wait()
>```

이 코드는 앞서 말한 mqtt 통신에서 pipe1~pipe7까지의 모든 경우의 수를 넣어놓고 이를 마스터 VM에서 소켓IO 단방향 통신을 이용해 마스터 VM에서 보내주는 신호에 따라 민감하게 반응하도록 파이프의 값을 실제 상항에 맞게끔 계산 후 약간의 노이즈를 넣어 Iot core에 전송하는 코드입니다. 이런식으로 17개의 센서는 모두 각자 민감하게 수식으로 연결되는 센서 값을 전송합니다. 이런식으로 크게 모든 VM에서 각자의 역할을 수행할 수 있는 코드를 만들고 이를 템플릿으로 복제하여 17개를 독립적 Worker VM을 생성하였습니다.

## 온프레미스 Iot 센서 환경 구축(Master VM)
이제는 이 모든 Worker VM에 웹관리콘솔로 소켓 단방향 통신을 구현하고 모든 Worker VM에 필요한 명령을 수행하는 환경을 구성해야합니다. ansible을 통해 관리형 네트워크를 구축할 수도 있지만 저는 필요한 서비스만을 구축하기엔 단순한 bash 명령어도 가능할 거라 생각하여 이런 식으로 bash 명령어 파일을 생성하였습니다.

>```python
># Slave VM의 IP 주소와 실행할 Python 스크립트의 목록입니다.
>declare -A SLAVE_IPS_SCRIPTS=(["192.168.239.61"]="rod.py" ["192.168.239.62"]="rod.py" ["192.168.239.63"]="rod.py" ["192.168.239.64"]="rod.py" ["192.168.239.65"]="rod.py" ["192.168.239.66"]="rod.py" ["192.168.239.67"]="rod.py" ["192.168.239.71"]="pipe.py" ["192.168.239.72"]="pipe.py" ["192.168.239.73"]="pipe.py" ["192.168.239.74"]="pipe.py" ["192.168.239.75"]="pipe.py" ["192.168.239.76"]="pipe.py" ["192.168.239.81"]="turbine.py" ["192.168.239.82"]="weather.py" ["192.168.239.83"]="generation.py" ["192.168.239.84"]="pgr.py")
>
># 각 slave VM에 원격으로 Python 스크립트를 실행합니다.
>for ip in "${!SLAVE_IPS_SCRIPTS[@]}"; do
>    sshpass -p <yourPW> ssh -o StrictHostKeyChecking=no <Your ID>@"$ip" "python3 ${SLAVE_IPS_SCRIPTS[$ip]}" &
>done
>```
이런식으로 sshpass를 사용해 센서의 시작과 중지를 관리하는 bash 파일을 만들고 마지막으로 Master VM에서 웹관리콘솔에서 단방향 소켓 통신과 Worker VM을 관리하는 코드를 보겠습니다.

## 웹관리 콘솔
웹관리 콘솔은 빠른 구현을 위해 flask를 통해 구현하였고 다음과 같습니다

![웹관리콘솔_이미지](https://github.com/Code-Y-Learner/aws_iotproject/blob/main/images/4.gif?raw=true)


```python
#master.py
from datetime import datetime
import random
from flask import Flask, render_template, request
from flask_socketio import SocketIO, emit
import time
import threading
from pytz import timezone

app = Flask(__name__)
socketio = SocketIO(app, cors_allowed_origins="*")

#초기값 설정
power_gen = {0: 62972.3698630137, 0.5: 61223.58356164385, 1: 59474.797260274, 1.5: 58402.29452054795, 2: 57329.7917808219, 2.5: 56732.8520547945, 3: 56135.9123287671, 3.5: 55875.17397260275, 4: 55614.4356164384, 4.5: 55747.5671232877, 5: 55880.698630137, 5.5: 56439.0780821918, 6: 56997.4575342466, 6.5: 58142.9904109589, 7: 59288.5232876712, 7.5: 60834.005479452055, 8: 62379.4876712329, 8.5: 64288.26301369865, 9: 66197.0383561644, 9.5: 66492.4095890411, 10: 66787.7808219178, 10.5: 66738.3383561644, 11: 66688.895890411, 11.5: 66203.85479452055, 12: 65718.8136986301, 12.5: 64639.378082191746, 13: 63559.9424657534, 13.5: 64535.7397260274, 14: 65511.5369863014, 14.5: 66072.55342465756, 15: 66633.5698630137, 15.5: 67160.77260273974, 16: 67687.9753424658, 16.5: 68496.37123287676, 17: 69304.7671232877, 17.5: 69498.70547945205, 18: 69692.6438356164, 18.5: 69730.15890410956, 19: 69767.6739726027, 19.5: 69491.36712328765, 20: 69215.0602739726, 20.5: 68471.54520547946, 21: 67728.0301369863, 21.5: 66645.74657534245, 22: 65563.4630136986, 22.5: 64563.527397260244, 23: 63563.5917808219, 23.5: 63267.9808219178, 24: 62972.3698630137, 24.5: 61223.58356164385, 25 :59474.797260274}
prev = 58000
index = datetime.now(timezone('Asia/Seoul')).hour
master_data = power_gen[index] + (power_gen[(index + 1) % 24] - power_gen[index]) * (datetime.now().minute / 60)
emergency = False
recover = False
control = False

def send_value():
    global master_data
    global index
    global control
    global prev
    global emergency
    while True:
        if (emergency==False):
            if (recover == False):
                if (control == False):
                    index = datetime.now(timezone('Asia/Seoul')).hour
                    master_data = power_gen[index] + (power_gen[(index + 1) % 24] - power_gen[index]) \
                            * ((datetime.now().minute / 60))
                    prev = master_data
                socketio.emit('signal', {'master_data' : master_data })
            else:
                index = datetime.now(timezone('Asia/Seoul')).hour
                master_data = power_gen[index] + (power_gen[(index + 1) % 24] - power_gen[index]) \
                        * (datetime.now().minute / 60)
                prev *= 1.0015
                socketio.emit('signal', {'master_data' : prev if (prev < master_data) else master_data })
        else:
            prev *= 0.999
            print(prev)
            socketio.emit('signal', {'master_data' : prev })
        time.sleep(0.1)

#인덱스
@app.route('/')
def index():
    return render_template('index.html')

@socketio.on('power_off')
def handle_shutdown():
    print('power_off')
    socketio.emit('power_off')

@socketio.on('power_on')
def handle_shuton():
    print('power_on')
    socketio.emit('power_on')

# 업데이트시
@socketio.on('update_signal')
def update_signal(message):
    global master_data
    master_data = float(message['data'])

@socketio.on('control_off')
def handle_control():
    global control
    control = False

@socketio.on('control_on')
def handel_control():
    global control
    control = True

@socketio.on('emergency_on')
def handle_emergency():
    global emergency
    emergency = True
    print("emergency")
    socketio.emit("rod_down")

@socketio.on('emergency_off')
def handle_emergency():
    global emergency
    global recover
    emergency = False
    recover = True
    print("emergency down")
    socketio.emit("rod_up")

# 변수 초기화
@socketio.on('get_init_data')
def handle_get_init_data():
    global master_data
    # 'init' 이벤트를 클라이언트로 전송하고, 이 이벤트의 데이터에 초기 데이터를 포함합니다.
    emit('init', {'master_data': master_data})

if __name__ == '__main__':
    threading.Thread(target=send_value).start()
    socketio.run(app, host='0.0.0.0', port=5000)
```

Master VM은 일반적으론 전기요구량의 하루치 변화량을 통해 값을 구성하여 각각의 Worker VM에 0.1초마다 신호를 줍니다. 파이프3의 신호가 끊기는 상황, 직접적으로 웹관리콘솔에서 토대가 되는 전기요구량을 컨트롤하는 패널 그리고 마지막으로 비상상황이 되면 원전의 비상상황을 모델링하여 비상신호를 Worker VM에 보내 시뮬레이션 상황을 만들도록 구축하였습니다.

[실제 구축 과정](https://code-y-learner.github.io/aws_iotproject/) 웹에서 발표 ppt를 확인할 수 있습니다.

[다음포스트](https://code-y-learner.github.io/aws/2024/03/03/Establishment-of-a-nuclear-power-plant-fully-managed-IoT-platform-using-AWS-services(2).html)에서 이어집니다.