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
    > 설치 후 확인=> localhost:9200

  - Elastic Kibana 설치 (버전:7.11.2)
    > https://www.elastic.co/kr/downloads/kibana  
    > 설치 후 확인=> localhost:5601
    
  - Elastic Logstach (Optional) 설치
    > https://www.elastic.co/kr/downloads/logstash

* Windows 7 32bit (Endpoint 환경) - VM 구성
  - Python 2.7 32bit
  - Elastic Winlogbeat (버전:7.11.2)
  - sysmon
    > https://docs.microsoft.com/ko-kr/sysinternals/
    
  - SwiftOnSecurity의 sysmon-config (보안로그 발생을 위한 sysmon 환경 파일)
    > https://github.com/SwiftOnSecurity/sysmon-config
    
  - Red Team Automation (Red Team용 MITRE ATT@CK 기반 malicious attack 발생)
    > https://github.com/endgameinc/RTA
    
* Ubuntu 18.04 64bit 환경 - VM 구성
  - Yelp의 elastalert
    > https://github.com/Yelp/elastalert

  - elastalert 설치
    > https://elastalert.readthedocs.io/en/latest/running_elastalert.html

##  실행 방법
* Elasticsearch 실행(관리자 계정)
  > bin/elasticsearch.bat

* Elastic Kibana 실행(관리자 계정)
  > bin/kibana.bat

* Windows7 sysmon vm 환경 실행
  > sysmon config파일을 다운받아서 sysmon파일안에 넣어준다  
  > cmd(관리자계정)에서 sysmon.exe -i %configfile% 입력

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
 * sysmon 10.x 실행 오류
   > kb3033929 설치
   > kb2533623 설치 (wevtapi.dll 문제)

* sysmon-config.xml  
  **변경전** 
     
    > \<PipeEvent onmatch="exclude"\>
	
    > \<EVENTID condition="is"\>1\</EVENTID\> 
     
    > \<\/PipeEvent\>
          
  **변경후**   
   
    > \<PipeEvent onmatch="include"\>
			
    >**삭제**
	
    > \</PipeEvent\>
          
  **변경전**
   
    > \<WmiEvent onmatch="include"\>
		
    >    \<Operation condition="is">Created</Operation\> 
            
    > \</WmiEvent\>
           
  **변경후**     
   
    > \<WmiEvent onmatch="include"\>
	
    > **삭제** 
	
    > \</WmiEvent\>
        
[[ Elasticsearch ]] 
* network.host 설정 bootstrap checks failed
  > https://soye0n.tistory.com/178

[[ Elastalert ]]
* pip install 오류
  > python version 3.6 다운
  
## Contributors
* maxup37
* idk3669
* air83
