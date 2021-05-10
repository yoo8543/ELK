# Intelligent Incident Response Platform

##  목표 구성도 참고 
* Open Source Endpoint monitoring 
  - https://github.com/DearBytes/Opensource-Endpoint-Monitoring
  
##  시스템 구성도 

![제목 없음3](https://user-images.githubusercontent.com/74276139/116993319-485cf400-ad12-11eb-9823-eca9bafe4f15.jpg)

##  환경 구성    
* Elastic Stack 64bit (Server 환경) - Host
  - Elastic Elasticsearch 설치 (버전:7.11.2)
    > https://www.elastic.co/kr/downloads/elasticsearch

  - Elastic Kibana 설치 (버전:7.11.2)
    > https://www.elastic.co/kr/downloads/kibana

* Windows 7 32bit (Endpoint 환경) - VM 구성
  - Python 2.7 32bit 설치
  - Elastic Winlogbeat (버전:7.11.2) 설치
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

## 설정파일 바꾸는 법(ip자리는 다 host ip삽입)
* Elasticsearch.yml
  > network.host: 192.168.0.12  
  > discovery.seed_hosts: ["127.0.0.1", "[::1]"]
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
  > $python3 -m elastalert.elastalert --config config.yaml --verbose --rule example_rules/example_frequency.yaml
 
## 메뉴얼 

* sysmon
  > https://github.com/trustedsec/SysmonCommunityGuide/blob/master/Sysmon.md

* elastic
  > https://www.elastic.co/guide/en/elastic-stack-get-started/7.6/get-started-elastic-stack.html#install-elasticsearch

* elastalert
  > https://elastalert.readthedocs.io/en/latest/running_elastalert.html
  
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
 
## Contributors
* maxup37
* idk3669
* air83
