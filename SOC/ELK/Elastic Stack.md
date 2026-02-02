![[Elastic Title.png]]

**What is an Elastic Stack?**  
 Elastic Stack - Elastic Search, Logstash, Beats, Kibana 
- A collection of different open-source tools that collect, store, search, and visualize data in real-time. 
	- Elastic Search – full text search and analytics for JSON-formatted documents. 
	- Logstash – a data processing engine that takes data from different sources, filters or normalizes it and then sends it to a destination like Kibana or any other destination for deeper analysis. 
	- Beats – host-based agent that ships/transfer data from the endpoint to Elastic Search. 
    - Kibana – a web–based data tool that works with Elastic Search to analyze, investigate and visualize data streams in real-time. 
    

**1. Select the index vpn_connection and filter from 31st December to 2nd February 2022. How many hits are returned?**  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/dab46127139b2662cad5cb3b86b3390972526722/SOC/ELK/Elastic%20Stack%20Images/Returned%20Hits.png)

- Before we can identify how many hits were returned, we must set the dates to determine the number of hits returned.  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Returned%20Hits1.png)


Answer: 2,861 
- Once we updated the calendar, the results were returned as shown above. 
    
**2.Which IP address has the maximum number of connections?**  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Max%20Connections.png)

Answer: 238.163.231.224  
- Since Elastic automatically fine filters events, we can simply go to Source_ip icon and click to view the IP addresses.  
- We can see that IP address 238.163.231.224 has more events occurred within the network.  

**3. Which user is responsible for the overall maximum traffic?**  

 ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/ELK%20User.png)

Answer: James 

- By reviewing the information in the “UserName”, we can see that James had 4.0% of traffic compared to the rest of the users.  
    

**4. Apply Filter on UserName Emanda: Which SourceIP has max hits?** 
- First, let's apply the filter to focus on Emanda to easily identify which IP had max hits under that specific “UserName” 
- Click Save when Done. 
     ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Emanda.png)

- After the filter is applied, we can then go to the Source_ip to see which IP had the most traffic.  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Emanda1.png)] 

Answer: 107.14.1.247
- We can see that IP 107.14.1.247 had 53.6% of traffic compared to Ip 107.14.4.82.  
    
**5. On the 11th of Jan. Which IP caused the spike observed in the time chart?**  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Jan%2011th.png)
- Here we can see a spike that occurred. Let's click on the spike bar and see which IP caused the spike.  
	![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Jan%2011th.1.png)

Answer: 172.201.60.191 
- We can see there are repeated hits of the same IP address that caused the spike, which was 172.201.60.191 
    

**6. How many connections were observed from IP 238.163.231.224, excluding New York State?**  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Not%20New%20York.png)

Answer: 48 

- Here we can do a search query to identify the number of connections that are not associated with New York. 
- The search input would be Source_ip: 238.163.231.224 and not New York 
- The results are shown above 
    

KQL (Kibana Query Language) 

**1. Create a search query to filter the logs where Source_Counrty is the United States and shows logs from UserNames: James or Albert. How many records were returned?**  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/United%20States.png)

Answer: 161 
- In the search bar, we can input the following to filter out what the question is asking. 
- The Search Input would be Source_Country:”United States” and UserName:”James” or UserName:”Albert” 
    

**2. A user Johny Brown was terminated on the 1st of January 2022. Create a search query to determine how many times a VPN connection was observed after his termination.**  
- There's two ways to conduct this search.  
- The 1st option is searching for the query in the search bar.  
- The search would be UserName:”Johny Brown”, this will return the number of connections the user attempted to make connection.  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Johnny%20Brown.png)

Answer: 1  
- The 2nd option is filtering the query to identify the user.  
![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Filter%20Johnny%20Brown.png)

![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Filter%20Johnny%20Brown1.png)


**Creating a Visualization**  

**1. Which User was observed with the greatest number of failed attempts?** 
    
**2. How many wrong VPN connection attempts were observed in January?**  
- To create a visualization to Identify the failed attempts and the number of wrong connections, we must do as followed below:  
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Visualization.png)

- We first want to create a Visualization by clicking on create visualization 
	![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/New%20Visual.png)
- It will ask us what type of visualization we want to create.  
- Click on Lens
- We are filtering the results and want to display what we are looking for.  

	![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Lens%20Filter.png)

- Before we add the information, we are looking for, we want to filter the failed attempts (based on what the question is asking us). 
- Once we create the filter, click Save. 
- Once we complete the filtering, we can then drag the Source_ip and UserName to the table. 
    ![alt text](https://github.com/DJackson-BlueTeam/Portfolio/blob/6093d5c5d235a70a67aa35deada67cb17b68725e/SOC/ELK/Elastic%20Stack%20Images/Simon.png)
































































Question 1 Answer: Simons 

Question 2 Answer: 274
