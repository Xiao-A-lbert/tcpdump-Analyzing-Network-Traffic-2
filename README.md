# tcpdump Analyzing Network Traffic 2

<h2>Description</h2>
In this task, I used tcpdump to example a pcap for suspicious malware that showed a GET request for a audiodg.exe from vietnam. 


<h2>Languages and Utilities Used</h2>

- <b>tcpdump</b>
- <b>linux CLI</b>


<h2>Environments Used </h2>

- <b>Unbuntu</b> 

<br />
<br />
I used tcpdump to count how many paackets were in the pcap before reading the 2021 pcap with -tt to instructs tcpdump to print an unformatted timestamp on each dump line displayed as the number of seconds and microseconds since January 1, 1970, 00: 00 :00 GMT.

![1) counting 9902 packets in this pcap ](https://github.com/user-attachments/assets/e15515f5-59a5-4db5-a221-342410d756c8)

<br />
<br />
I noticed a lot of traffic from host 10.0.0.168 to destination 103.232.55.148. 

![2) reading tcp packets -n and -tt](https://github.com/user-attachments/assets/34078e86-b1c4-4a1e-9b48-aa4fbe06f354)

<br />
<br />  
Narrowing the search, I counted the packets filtering only for port 80 traffic which reduced the output by ~1000 packets. I filtered further by including host 10.0.0.168 and piping to grep -E "GET|POST". I used -E to allow grep to interpret "GET|POST" as a pattern that matches lines containing either "GET" or "POST". Without -E, I would need to escape the | character, like this: "GET\|POST".

![3) cutting -d spaces and grabbing -f 3 which is the src ip](https://github.com/user-attachments/assets/356d9b1a-8eb7-4b05-8f97-b537ba5bd816)

<br />
<br />
The output shows a GET for an "audiodg.exe" from 103.232.55.148.

![4) piping to cut again with -d dot grabbing fields 1-4](https://github.com/user-attachments/assets/1d9c73f7-4bb5-4b1b-81ca-51f419a0de6e)

<br />
<br />
Doning some OSINT, the audiodg.exe would normally be a Windows Audio Device file, however, the ip is from Vietnam and the ASN is not associated with Microsoft. 

![5) sorting unique count shows top 3 src ips](https://github.com/user-attachments/assets/9b77c772-4d56-410e-8a08-e7c057c7fcc1)

<br />
<br />
Running tcpdump to grep specifically for "audiodg.exe" outputs two packets with the first showing a GET request to a suspicious url. 

![6) changing first -f to 5 shows dst ips](https://github.com/user-attachments/assets/9be5f428-dfed-4d2a-9af2-6b5aae62d6d1)

<br />
<br />
Using the following tcpdump command to read the file in ASCII with -A then piping to grep for "audiodg.exe" along with -A 500 after grep to display the matching line along with the next 500 lines of output after each match.

![7) count of packets with ips with most communication](https://github.com/user-attachments/assets/b7b051be-da26-4e6f-bee4-0584fb759e6f)

<br />
<br />  
The results show the destination ip opf 13.107.5.80 with the GET request for the suspicious http url and a host from api.bing.com.

![8) cutting and sorting src and dst shows top ports](https://github.com/user-attachments/assets/3c30c34a-57b9-458a-81c3-1f79bf18c196)

<br />
<br />
Looking at whoisdomain for the ip 13.107.5.80 shows it is microsoft bing that is being used. This leads me to believe that the request uses bing to forward the request to the suspicious url. 

![9) filtering for get post requests on src and dst ips](https://github.com/user-attachments/assets/dea944f4-5e7d-4e70-b84f-d35453191733)

<br />
<br />
Decoding the suspicious url and running it through virustotal shows 7 vendor flags for malicious malware. 

![10) opening first 5 packets in ASCII format andnotice LA and ssload](https://github.com/user-attachments/assets/529d9428-1295-4994-8532-96f26a8db731)

<br />
<br />
Running tcpdump to grep specifically for "audiodg.exe" outputs two packets with the first showing a GET request to a suspicious url. 

![11) osint ssload malware](https://github.com/user-attachments/assets/e3272f1b-4a96-44cf-998a-1c6d4bc51912)

<br />
<br />
Using the following tcpdump command to read the file in ASCII with -A then piping to grep for "audiodg.exe" along with -A 500 after grep to display the matching line along with the next 500 lines of output after each match.

![12) ssload ioc leads to github ](https://github.com/user-attachments/assets/227403f6-ec2d-468b-9258-3ac6a53e1334)

<br />
<br />  
The results show the destination ip opf 13.107.5.80 with the GET request for the suspicious http url and a host from api.bing.com.

![13) filtering for host shows a unique domain](https://github.com/user-attachments/assets/540f10c0-a8a3-4b9a-b769-367cb2200ca5)

<br />
<br />
Looking at whoisdomain for the ip 13.107.5.80 shows it is microsoft bing that is being used. This leads me to believe that the request uses bing to forward the request to the suspicious url. 

![14) grep -v exclude output to show values related to affected endpoint ](https://github.com/user-attachments/assets/0cb6e87c-338d-4301-9707-a9ce6236c39f)

<br />
<br />
Decoding the suspicious url and running it through virustotal shows 7 vendor flags for malicious malware. 

![15) grep filename shows ddl bin](https://github.com/user-attachments/assets/e6596fee-734c-4230-be08-890a978092d1)

<br />
<br />
Decoding the suspicious url and running it through virustotal shows 7 vendor flags for malicious malware. 

![16) grep tme and dll](https://github.com/user-attachments/assets/dbc6de33-6bcb-4346-85fd-9c0808299224)

<br />
<br />
