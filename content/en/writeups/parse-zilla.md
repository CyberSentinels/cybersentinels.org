+++
title = "Parse-Zilla: A BlueTeamLabs Writeup"
subtitle = ""
tags = ["cybersecurity", "BlueTeamLabs", "BTLO", "parse-zilla", "json", "log reading", "incident response", "defensive security"]
date = 2023-02-23
page_type = "post"

# Scenario
DummyCompany has an internal service **dummycompany.localhost**  with which employees can do a variety of things. The Security Team was alerted for excessive requests from an internal IP and potential access to confidential files on this server due to a authentication/authorization misconfiguration. You have been tasked with analyzing the access logs (in JSON format) and answering the questions to prepare an initial report on this incident.

# Based on statistical analysis, which IP address is likely to be the attackers?

- First, use the **`jq`** tool in Ubuntu to parse the log file. This will create a new, clean looking log file from the results.
- Open the newly created log file in a text editor such as Sublime Text.
- Start looking through the file and around line 724, you will find the client host address.
- Look further down and you will see the line "Request_X-Real-Ip". This confirms that the host address found earlier is correct.
- The IP address is likely to be the attacker is: **192.168.50.235**

# What is the name of the reconnaissance tool used by the attacker?

- A few lines above the IP address of the host you found earlier, you will find the "request_User-Agent" line.
- This line provides information of the tool used.
- The reconnaissance tool used by the attacker is: **Nikto**

# After conducting reconnaissance, it is likely that the attacker manually began to investigate the server. What is the browser user-agent used by the attacker?

- The browser user-agent used by the attacker is:

```

"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.3

```

# Based on the UA found in Question 3, what is browser name, version, and operating system used by the attacker?

- To find this information, you can use a website such as **[https://developers.whatismybrowser.com/useragents/parse/#parse-useragent](https://developers.whatismybrowser.com/useragents/parse/#parse-useragent)**
- Based on the UA, the browser name, version, and operating system used by the attacker is: **Chrome 106, Linux**

# Some web attacks can utilize the user-agent field to conduct exploitation. What is the vulnerability identifier related to the attempted exploit?

- To find the CVE easily, you can use the command **`jq '.' ./access.log | grep "CVE"`**.
- This command will parse the log file, and then search for the strings that contain "CVE"
- The vulnerability identifier related to the attempted exploit is: **CVE-2014-6271**

# How many unique user-agent strings were observed from the attacker?

- To find the number of unique user-agent strings, you can use the command **`jq '.' ./access.log | grep "CVE" | uniq -c | awk '{print $1}' | uniq -c`**
- This command will parse the log file, search for the strings that contain "CVE", count for uniques, find the patterns specified, and then count for uniques again.
- The number of unique user-agent strings observed from the attacker is: **3**

# What is the name of the exposed directory containing files that the attacker identified?

To find the exposed directory containing files that the attacker identified, you can use a command similar to the one used in previous questions. In this case, you would need to grep for the "RequestPath" field in the log file.

Here's an example of how you might use the command:

`jq '.' ./access.log | grep "RequestPath"`

This command will search the entire log file for any instances of "RequestPath" and return the results. You will see a bunch of results, but by looking through the results you will find that the attacker is trying to access the '/ftp/' directory.

So, the name of the exposed directory containing files that the attacker identified is:

/ftp/