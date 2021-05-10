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
  - sysmon
    > https://docs.microsoft.com/ko-kr/sysinternals/  
    > 1. sysmonconfig-export.xml파일을 sysmon파일 안에 넣어줌  
    > 2. cmd(관리자)실행 후 sysmon파일로 경로 이동해주고 명령문 입력  
    > Sysmon.exe -accepteula -i C:\Sysmon\sysmonconfig-export.xml -l -n
    
  - SwiftOnSecurity의 sysmon-config (보안로그 발생을 위한 sysmon 환경 파일)
    > https://github.com/SwiftOnSecurity/sysmon-config
    
  - Red Team Automation (Red Team용 MITRE ATT@CK 기반 malicious attack 발생)
    > https://github.com/endgameinc/RTA
    
* Ubuntu 18.04 64bit 환경 - VM 구성
  - Yelp의 elastalert
    > https://github.com/Yelp/elastalert

  - elastalert 설치
    > https://elastalert.readthedocs.io/en/latest/running_elastalert.html

## 설정파일 바꾸는 법(ip자리는 다 host ip삽입)
* Elasticsearch.yml
  > network.host: 192.168.0.12  
  > discovery.seed_hosts: ["127.0.0.1", "[::1]"]
* Kibana.yml
  > server.host: "192.168.0.12"  
  > elasticsearch.hosts: ["http://192.168.0.12:9200"]  
* Winlogbeat.yml
  > kibana부분에서 host: "192.168.0.12:5601"
  > elasticsearch output부분에서 hosts: ["192.168.0.12:9200"]

##  실행 방법
* Elasticsearch 실행(관리자 계정)
  > bin/elasticsearch.bat

* Elastic Kibana 실행(관리자 계정)
  > bin/kibana.bat

* Windows7 sysmon vm 환경에서 winlogbeat 실행(관리자 계정)
  > winlogbeat.exe -c winlogbeat.yml

* Ubuntu 18.04 64bit 환경에서 Elasticalert 실행
  >/elastalert  
  >elastalert --verbose --start  --config <config.yaml> --rule <error.yaml>
 
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
        
[[ Elasticsearch ]] 

[[ Elastalert ]]
  
## Contributors
* maxup37
* idk3669
* air83
