# tcpdump Analyzing Network Traffic 2

<h2>Description</h2>
In this task, I used tcpdump to examine a pcap with advanced filtering using grep, cut, -d " ", -f, etc to find the most common src and dst ips, most common src and dst ports, find a suscpisious malware within the packet, perform OSINT to narrow my search even deeper into a host ip for the malware, and find the filename and host domain for the malware. 


<h2>Languages and Utilities Used</h2>

- <b>tcpdump</b>
- <b>linux CLI</b>


<h2>Environments Used </h2>

- <b>Unbuntu</b> 

<br />
<br />
Reading the pcap shows 9902 results. Need to narrow down search. 

![1) counting 9902 packets in this pcap ](https://github.com/user-attachments/assets/e15515f5-59a5-4db5-a221-342410d756c8)

<br />
<br />
Adding -tt to switch to UTC time -n to not resolve DNS names while filtering for only tcp traffic. Output shown above command for screenshot reference. 

![2) reading tcp packets -n and -tt](https://github.com/user-attachments/assets/34078e86-b1c4-4a1e-9b48-aa4fbe06f354)

<br />
<br />  
Grepped tcp output to cut -d " " which sets delimiter to a space and -f 3 to select third field to output which in the above text outputs ips along with their pots. 

![3) cutting -d spaces and grabbing -f 3 which is the src ip](https://github.com/user-attachments/assets/356d9b1a-8eb7-4b05-8f97-b537ba5bd816)

<br />
<br />
Piping again to cut -d "." sets delimiter to . and -f 1-4 outputs the first four fields between the .'s effectively removing the port numbers associated with the ips. 

![4) piping to cut again with -d dot grabbing fields 1-4](https://github.com/user-attachments/assets/1d9c73f7-4bb5-4b1b-81ca-51f419a0de6e)

<br />
<br />
| sort to short initial output, then | uniq -c to tally the total count per unique output, then | sort -nr to sort again numerically in reverse showing the top most unique ips to the least most unique ips within the pcap. 

![5) sorting unique count shows top 3 src ips](https://github.com/user-attachments/assets/9b77c772-4d56-410e-8a08-e7c057c7fcc1)

<br />
<br />
Changing the first -f field from 3 to 5 will output a sorted uniquely counted destingation ip from the original tcp pcap. 

![6) changing first -f to 5 shows dst ips](https://github.com/user-attachments/assets/9be5f428-dfed-4d2a-9af2-6b5aae62d6d1)

<br />
<br />
Now that we know the most common src and dst ports, we can assume they are communicating with each other. I filtered for this and counted 1169 packets.

![7) count of packets with ips with most communication](https://github.com/user-attachments/assets/b7b051be-da26-4e6f-bee4-0584fb759e6f)

<br />
<br />  
Now I applied the cut -d " " -f  to extract the src ip from the src and dst filters, and cut -d "." -f 5 to cut out the port to be sorted in resverse order. I repeated the process but switched the src and dst ips. This screenshot shows the most common ports used between each direction of traffic. 

![8) cutting and sorting src and dst shows top ports](https://github.com/user-attachments/assets/3c30c34a-57b9-458a-81c3-1f79bf18c196)

<br />
<br />
I pipped the src and dst ips to find GET and POST requests within their traffic. 

![9) filtering for get post requests on src and dst ips](https://github.com/user-attachments/assets/dea944f4-5e7d-4e70-b84f-d35453191733)

<br />
<br />
Looking into the first 5 packets -c 5 in ASCII format with -A shows a content-type: Los Angeles and a user-agent: SSLoad.

![10) opening first 5 packets in ASCII format andnotice LA and ssload](https://github.com/user-attachments/assets/529d9428-1295-4994-8532-96f26a8db731)

<br />
<br />
Looking up SSLoad we can see it is a rust-based downlaoder used to deliver a secondary payload. 

![11) osint ssload malware](https://github.com/user-attachments/assets/e3272f1b-4a96-44cf-998a-1c6d4bc51912)

<br />
<br />
Looking up SSLoad IOCs leads me to a github page about the post-infection traffic enumerating a defanged url with a dst ip. 

![12) ssload ioc leads to github ](https://github.com/user-attachments/assets/227403f6-ec2d-468b-9258-3ac6a53e1334)

<br />
<br />  
Using tcpdump to filter for a host of the suspicious 85.239.53.219 outputs the results above showing a firstopportunity.online http. 

![13) filtering for host shows a unique domain](https://github.com/user-attachments/assets/540f10c0-a8a3-4b9a-b769-367cb2200ca5)

<br />
<br />
Looking up the domain in virustotal outputs 1 vendor flag for phishing. 


![13) virustotal malicious](https://github.com/user-attachments/assets/2d570586-a452-4f03-8439-afa0b4f895c7)

<br />
<br />
Filtering for user\ or pass\ or login stirngs shows User-Agent outputs. Another grep to excude User-Agent shows a interesting object that may relate to the affected endpoint sent over to the attacker.

![14) grep -v exclude output to show values related to affected endpoint ](https://github.com/user-attachments/assets/0cb6e87c-338d-4301-9707-a9ce6236c39f)

<br />
<br />
Grepping the suspicious host to search for filename outputs a crypted dll.bin.

![15) grep filename shows ddl bin](https://github.com/user-attachments/assets/e6596fee-734c-4230-be08-890a978092d1)

<br />
<br />
From the github IOC on SSLoad, grepping for t.me domain outlputs a list of queries for A records. Grepping for dll shows a GET request over 8080. This concludes the IOCs gather by examining this sample pcap. Furher analysis tools like Wireshark will help augment this investigationg. 

![16) grep tme and dll](https://github.com/user-attachments/assets/dbc6de33-6bcb-4346-85fd-9c0808299224)

<br />
<br />
