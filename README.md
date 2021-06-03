# Intelligent Incident Response Platform

##  목표 구성도 참고 
* Open Source Endpoint monitoring 
  - https://github.com/DearBytes/Opensource-Endpoint-Monitoring

## 메뉴얼 

* sysmon
  > https://github.com/trustedsec/SysmonCommunityGuide/blob/master/Sysmon.md

* elastic
  > https://www.elastic.co/guide/en/elastic-stack-get-started/7.6/get-started-elastic-stack.html#install-elasticsearch

* elastalert
  > https://elastalert.readthedocs.io/en/latest/running_elastalert.html

##  시스템 구성도 

![116993319-485cf400-ad12-11eb-9823-eca9bafe4f15](https://user-images.githubusercontent.com/74276139/117798793-70a59f00-b28c-11eb-8826-a911ae27680b.jpg)

##  환경 구성    
* Elastic Stack 64bit (Server 환경) - Host
  - Elastic Elasticsearch 설치 (버전:7.11.2)
    > https://www.elastic.co/kr/downloads/past-releases/elasticsearch-7-11-2

  - Elastic Kibana 설치 (버전:7.11.2)
    > https://www.elastic.co/kr/downloads/past-releases/kibana-7-11-2

* Windows 7 32bit (Endpoint 환경) - VM 구성
  - Python 2.7 32bit 설치
  - Elastic Winlogbeat (버전:7.11.2) 설치
    > https://www.elastic.co/kr/downloads/past-releases/winlogbeat-7-11-2
    > 1. C:program files에 Winlogbeat 파일이름 설정해서 넣어줌  
    > 2. powershell(관리자) 실행 후 .\install-service-winlogbeat.ps1 명령 실행  
    > 3. .\winlogbeat.exe setup -e'  
    > -E output.elasticsearch.hosts=['192.168.0.12:9200']'  
    > -E output.kibana.host=192.168.0.12:5601  
    > 4. Start-Service winlogbeat  
    > 5. .\winlogbeat.exe setup -dashboards
  - sysmon
    > https://docs.microsoft.com/ko-kr/sysinternals/  
    > 1. sysmonconfig-export.xml파일을 sysmon파일 안에 넣어줌  
    > 2. cmd(관리자)실행 후 sysmon파일로 경로 이동해주고 명령문 입력  
    > Sysmon.exe -accepteula -i C:\Sysmon\sysmonconfig-export.xml -l -n
    
* Ubuntu 18.04 64bit 환경 - VM 구성
  - elastalert 설치
    > https://elastalert.readthedocs.io/en/latest/running_elastalert.html  
    > https://sg-choi.tistory.com/309

## 네트워크 구성도
![캡처](https://user-images.githubusercontent.com/74276139/118220434-a7f79400-b4b6-11eb-8d04-cf56464f615b.JPG)  

## 설정파일 바꾸는 법
* Elasticsearch.yml
  > network.host: 192.168.0.12  
  > discovery.seed_hosts: ["127.0.0.1", "[::1]"](이건 이대로 적어야함)
* Kibana.yml
  > server.host: "192.168.0.12"  
  > elasticsearch.hosts: ["http://192.168.0.12:9200"]  
* Winlogbeat.yml
  > kibana부분에서 host: "192.168.0.12:5601"  
  > elasticsearch부분에서 hosts: ["192.168.0.12:9200"]

##  실행 방법
* Elasticsearch 실행(관리자 계정)
  > bin/elasticsearch.bat

* Elastic Kibana 실행(관리자 계정)
  > bin/kibana.bat

* Ubuntu 18.04 64bit 환경에서 Elasticalert 실행(elastalert폴더안에서 실행)
  > $python3 -m elastalert.elastalert --config config.yaml --verbose --rule example_rules\example_frequency.yaml

## sigma_rule (ubuntu_18.04)
* sigmatools 설치
  > $sudo pip3 install sigmatools  
* sigma git 가져오기
  > $git clone https://github.com/Neo23x0/sigma.git  
  > $cd sigma  
* home 디렉토리에 복사
  > $cp -r rules/windows ~  
* 실행
  > $sudo sigmac -t elastalert -r -c winlogbeat ~/windows -o ~/test  
  > $cd ~  
* 실행 후 생성된 파일 원하는 폴더로 옮기기
  > $csplit --prefix sigma_ --suffix-format "%04d.yml" test "/^alert:/" "{\*}"  
  > $mv ./sigma_\*.yml elastalert/example_rules  

## slack (ubuntu_18.04)  
* 참고사이트
  > https://miiingo.tistory.com/223  
* slack 다운로드
  > https://slack.com/intl/ko-kr/downloads/windows  
  > 로그인 후 워크스페이스 생성 (이하 elk)  
* Incoming WebHook 설정
  > a. slack 프로그램 내 만든 워크스페이스 접속  
  > b. slack 찾아보기 -> 앱 -> incoming webhook 검색 후 추가  
  > c. 홈페이지 자동 접속 -> slack에 추가 버튼 클릭 -> 메세지를 띄울 채널 선택  
  > d. 수신 웹후크 통합 앱 추가 버튼 클릭  
  > e. 웹후크 URL 복사해놓기
  > f. 창 닫아도 접속 가능 : https://XXXXX(만든 워크스페이스의 이름).slack.com/services/new/incoming-webhook  
* ubuntu 실행
  > $cd elastalert/example_rules/  
  > example_frequency.yaml 파일 수정 (계속 써왔던 rule 파일)  
* rule 파일 수정
  > 아래 내용을 rule 파일 마지막에 입력  
  > alert:  
  > \- slack:  
  >      slack_webhook_url: "(webhook에서 복사한 url)"  
  >      slack_username_override: "ZEUS"  
  >      slack_channel_override: "#(webhook에서 설정한 메세지 받을 채널이름)"  
  >      slack_emoji_override: ":\zap:"  
  >      slack_msg_color: "danger"  

 ## 오류 수정 
 [[ windows 7 ]]
 * winlogbeat 실행정책 오류
   > Set-ExecutionPolicy bypass  
 * sysmon 10.x 실행 오류
   > kb3033929 설치  

[[ Elastalert ]]
 * git 오류
   > $sudo apt install git  
 * Elastalert 실행 오류
   > 1. "python setup.py egg_info" failed with error code 1  
   > $sudo pip3 install --upgrade pip  
   > 2. AttributeError: module 'yaml' has no attribute 'FullLoader'  
   > $sudo python3 -m pip install -U pip numpy  
   > $pip3 install -U PyYAML  
   > 3. urllib3(1.26.4) or chardet(3.0.4) doesn't match a supported version!  
   > $sudo pip3 install --upgrade requests  
 * sigma rule 오류
   > An unsupported feature is required for this sigma rule ([파일이름]) : None : The 'near' ~~~~  
   > $rm 파일이름
 
## Contributors
* maxup37
* idk3669
* air83
* yoo8543
