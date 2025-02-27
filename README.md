# Investigating Failed SSH Logins with Splunk

## 📌 Project Overview
For the company Buttercup Games, I was tasked with identifying potential security issues related to failed SSH login attempts on the mail server. Using **Splunk Cloud**, I conducted a search to detect unusual login activity and potential brute-force attacks targeting the root account.

![image](https://github.com/user-attachments/assets/191fa307-4ecd-4ac0-91a4-bd1e09e36f74)


---

## **1️⃣ Data Ingestion and Indexing in Splunk**

To begin the investigation, I uploaded the provided log data (`tutorialdata.zip`) into Splunk to make it searchable. Once indexed, Splunk allowed me to query and analyze security events efficiently.

![image](https://github.com/user-attachments/assets/37267616-a349-46bc-83be-4797cdf8e97b)


---

## **2️⃣ Performing a Basic Query**

To retrieve all indexed data, I executed the following Splunk query:

```splunk
index="main"
```

![image](https://github.com/user-attachments/assets/2a9535af-f349-4009-9707-1fa3f5d77172)


- **`index="main"`**: Retrieves all events from the primary index.
- Selected **All Time** as the time range to view all recorded events.

This search retrieved **thousands of events**, which were then filtered to find relevant security incidents.

---

## **3️⃣ Evaluating the Fields**

Each log entry in Splunk contains several key fields that provide critical information:

- **host** → The network host from which the event originated.
- **source** → The specific log file generating the event.
- **sourcetype** → The format or type of data being indexed.

![image](https://github.com/user-attachments/assets/da03b8a3-7008-4952-8e11-dceb4f8825d5)


### **Key Hosts Identified in the Logs:**
| Hostname        | Description |
|----------------|-------------|
| **mailsv**       | Buttercup Games' mail server |
| **www1**        | Web application server 1 |
| **www2**        | Web application server 2 |
| **www3**        | Web application server 3 |
| **vendor_sales** | Retail sales system |

### **Key Log Source Identified:**
- `/mailsv/secure.log` → Contains authentication and authorization attempts for the mail server.

---

## **4️⃣ Narrowing the Search to the Mail Server**

Since the focus was on SSH login failures on the mail server, I refined the search:

```splunk
index=main host=mailsv
```

![image](https://github.com/user-attachments/assets/f9d6f928-8edd-4939-a58e-d4601ade18c7)


- **`host=mailsv`** → Filters results to only show logs from the mail server.
- This reduced the dataset to **over 9,000 events** originating from `mailsv`.

---

## **5️⃣ Searching for Failed Root Logins**

To locate failed SSH logins for the **root** account, I refined the query further:

```splunk
index=main host=mailsv fail* root
```
![image](https://github.com/user-attachments/assets/bf69a321-69bd-4773-bf8b-1c3dbdabd746)


### **Explanation:**
- **`fail*`** → Uses a wildcard to search for terms like `failure`, `failed`, etc.
- **`root`** → Searches for events related to the root account.
- This query retrieved **over 300 security events**, highlighting repeated failed login attempts.

---

## **6️⃣ Identifying a Distributed Brute-Force Attack**

After reviewing the search results, I observed **multiple failed password attempts** targeting the **root account via SSH**, originating from different IP addresses. This pattern indicates a **distributed brute-force attack**, where attackers use automated scripts to test different password combinations from various sources.

### **🔍 Indicators of Attack:**

✅ Multiple failed SSH login attempts in a short time frame.  

![image](https://github.com/user-attachments/assets/adb6d2cb-0062-4a2b-8cd9-79f1110e2a7e)


✅ Attempts originating from various IP addresses.  

![image](https://github.com/user-attachments/assets/53ac4a3e-29d9-4b9a-9948-0ebc33f11062)


✅ Repeated login attempts using the root account.  

![image](https://github.com/user-attachments/assets/aa955850-2366-4dfc-9226-ce4217f12d38)

---

## **7️⃣ Key Takeaways and Mitigation Strategies**

By leveraging **Splunk Cloud**, I successfully:

✅ Uploaded and indexed security log data.  
✅ Queried and filtered logs for relevant security events.  
✅ Identified suspicious SSH login attempts.  
✅ Determined signs of a potential brute-force attack.

### **🚀 Recommended Security Measures:**

- **Disable root SSH login:** Prevent direct SSH access for the root user.
- **Implement fail2ban:** Automatically block IPs after multiple failed login attempts.
- **Use SSH key authentication:** Replace password authentication with key-based authentication.
- **Monitor and alert:** Set up Splunk alerts for abnormal login activity.

---

### **Conclusion**
This project highlights the importance of log analysis in identifying security threats. By using Splunk, organizations can detect and mitigate brute-force attacks before they escalate into serious breaches.
