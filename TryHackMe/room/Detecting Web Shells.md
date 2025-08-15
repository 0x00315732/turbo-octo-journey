Knowing how to detect web shells is an essential skill for SOC Analysts and Incident Responders. Web shells are a common technique attackers use to gain an initial foothold on target systems. They provide remote access, enabling various actions later in the attack chain. In this room, we will begin by refreshing our understanding of web shells, then dive into detection techniques using a variety of logs and tools.

## Learning Objectives

- Understand what web shells are and how attackers use them
- Detect web shell activity through log, file system, and network analysis
- Understand common tooling in web shell detection

## Room Prerequisites

A basic understanding of the topics below will be helpful during this walkthrough.

- [Web Application Basics](https://tryhackme.com/room/webapplicationbasics): HTTP Request Methods & Responses
- [Intro to Log Analysis](https://tryhackme.com/room/introtologanalysis): Common Log Formats
- [MITRE](https://tryhackme.com/room/mitre): Initial Access & Persistence Tactics


## What is a Web Shell?

To effectively detect web shells, it is important to understand what they are, how attackers deploy them, and the vulnerabilities that they exploit.

A web shell is a malicious program uploaded to a target web server, enabling adversaries to execute commands remotely. Web shells often serve as both an [initial access](https://attack.mitre.org/techniques/T1190/) method (via file upload vulnerabilities) and a [persistence](https://attack.mitre.org/techniques/T1505/003/) mechanism.

Once access has been gained on a compromised server, attackers can use a web shell to move through the kill chain, performing [reconnaissance](https://attack.mitre.org/tactics/TA0043/), [escalating privileges](https://attack.mitre.org/tactics/TA0004/), [moving laterally](https://attack.mitre.org/tactics/TA0008/), and [exfiltrating data](https://attack.mitre.org/tactics/TA0010/).

Below is a simple example of a web shell named `awebshell.php` that can run commands remotely through the web interface. Note that the web shell is located in the `/uploads` directory of the target server, 10.10.10.100.

![An example web shell hosted on 10.10.10.100. A command line to enter and execute commands can be seen. There is a space for entering commands, a run button and the output which shows the contents of the currrent directory.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753149343838.svg)

## Web Shell Deployment

For an attacker to upload and execute a web shell, a file upload vulnerability, misconfiguration, or prior access to the system is required. These vulnerabilities arise when an application fails to validate file type, extension, content, or destination. While web shells are used as an initial access vector, they can also serve as a persistence mechanism if the attacker has already compromised the system and wants to maintain long-term access.

Imagine a simple website that allows you to upload your pet photos. The website is intended for storing images. However, if developed insecurely, an attacker may upload a web shell like `shell.php` or `mydog.aspx` and gain command execution on the server.

## Examples in the Wild

**Hafnium & ProxyLogon**  
[Hafnium](https://attack.mitre.org/groups/G0125), a Chinese-based APT, uploaded `.aspx` web shells to Windows Exchange servers in directories such as `\inetpub\wwwroot\aspnet_client\`. They have also been seen modifying existing `.aspx` files in directories such as `\install_path\FrontEnd\HttpProxy\owa\auth\`.

Once the web shell is successfully deployed, Hafnium continues on to execute commands, perform reconnaissance, dump credentials, further establish persistence through new user accounts, move laterally, exfiltrate data, and cover their tracks to evade detection.

**Conti Ransomware Attackers**  
[Conti](https://attack.mitre.org/software/S0575/) Ransomware threat actors abused a similar vulnerability in Microsoft Exchange, which allowed them to upload an `aspnetclient_log.aspx` file to the same `\aspnet_client\` directory as Hafnium. Within minutes of the initial web shell upload, they uploaded a backup web shell and mapped out the network's computers, domain controllers, and domain administrators.


## Anatomy of a Web Shell

**Legitimate Function Abuse**  
Web shells rely on the abuse of legitimate functions within programs.  
System execution functions in PHP, such as `shell_exec()`, `exec()`, `system()`, and `passthru()`, can be abused to gain command execution.

**Under the Hood**  
Here is a simple web shell written in PHP. Let's take a look at its functionality.

1. Checks if the `cmd` parameter is present in the URL `?cmd=whoami`
2. Stores the user supplied command in the variable `$cmd`
3. Executes the command using `shell_exec()`
4. Displays the output
5. HTML for the user interface
6. Command to execute
7. Output

![On the left is the PHP code for a web shell. This code allows system commands to be executed and their output displayed. On the right is a visual representation of the web shell hosted on a server, as viewed in a browser. The whoami command has been executed, and the output shows www-data, indicating the web server's user context.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753924119088.svg)

Web shells vary in complexity and capabilities. They can range from simple one-liners that execute commands via a URL, to fully-featured interfaces with graphical user interfaces (GUIs), password protection, and built-in file managers.

## A Web Shell in Action

A web shell has been deployed on our target machine at `http://10.201.119.175:8080/files/awebshell.php`.  
It can be accessed directly via browser or by command line, utilizing `curl`.  
Don't forget to URL-encode your commands if accessing the shell via command line (`/awebshell.php?cmd=<encoded_command>`). [Cyberchef](https://gchq.github.io/CyberChef/#recipe=URL_Encode\(false\)) can assist.  
_Example:_ `ls -la` becomes `ls%20la`

## Web Server Logs

Web shells rely on the abuse of web servers, so web server logs are a natural place to start our hunt for evidence. We will explore what to look for in popular web servers like Apache & Nginx. Understanding the difference between normal and suspicious behavior can help uncover malicious activity.

While the format of web server logs varies depending on the service, **access logs** generally follow a similar structure and include the following information.

The remote log name field is typically represented by a hyphen (-), as it is a legacy field that is rarely used today. However, it still appears in access logs for compatibility. Similarly, the authenticated user field is usually shown as a hyphen as well, unless the server required prior authentication, in which case it may contain the actual username.

![A sample access log showing the different fields logged by a web server.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753156279804.svg)

## Web Indicators

**Unusual HTTP Methods** **&** **Request Patterns**

- Repeated GET requests in quick succession could mean an attacker is probing for a valid place to upload a shell
- POST requests to valid upload locations following repeated GET requests
- Repeated GET or POST requests to the same file could indicate web shell interaction

**Request Methods To Be Aware Of.**

|   |   |   |
|---|---|---|
|**Request Method**|**Normal Usage**|**Possible abuse**|
|**GET**|Retrieve a resource|Used for recon or interacting with a web shell|
|**POST**|Submit data to the server|Upload or interact with a web shell|
|**PUT**|Upload or replace a file on the server|Upload a web shell|
|**DELETE**|Remove a resource from the server|Cleanup methods|
|**OPTIONS**|Requests methods that are supported|Reconaissance|
|**HEAD**|Similar to GET but only returns headers|To detect files|

Let’s walk through a potential web shell attack sequence using the indicators we've discussed so far.  
Note that each request shares the same client IP and user agent. Response codes and timestamps should also be noted.  
`200 OK`  
`404 Not Found`

![A sample attack sequence in which an attacker fuzzes a website for an open directory and then uploads a webshell. The attacker finds a valid directory, finds an abusable form, uploads the webshell, and accesses it.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753162986152.svg)

**Suspicious User-Agents & IP Addresses  
**The User-Agent identifies the client making requests to the web server and provides information about the browser, device, and operating system.

- Altered User-Agents: `Mozilla/4.0+(+Windows+NT+5.1)` shortened to `Mozilla/4.0`
- Outdated User-Agents: `Mozilla/4.0 (compatible; MSIE 6.0)` MSIE 6.0 released in 2001
- Blacklisted User-Agents: `curl/1.XX.X` or `wget/1.XX.X` for example
- Suspicious IP Addresses: A network normally only sees internal traffic so an outside IP address would be suspicious

**Query Strings  
**Part of the URL that associates values with a parameter. `example.php?query=somequery`

- Abnormally long or suspicious query strings, especially containing keywords like `cmd=` or `exec=` 
- Encoded query strings. `?query=whoami` becomes `?query=d2hvYW1p` when Base64 encoded. [Cyberchef](https://gchq.github.io/CyberChef/) is an excellent tool for decoding Base64 and many other forms of encoding and obfuscation.

**Missing Referrer  
**The referrer shows the URL the users visited before being linked to the current page.

- A missing referrer can be potentially indicative of web shell activity
- There are valid reasons why a referrer might be missing (e.g., browsers blocking them for privacy, if a URL is directly accessed)

**Sample suspicious web request** including some of the above indicators.

1. Known malicious or untrusted IP address.
2. Abnormal timestamp. Perhaps outside of normal business hours.
3. POST request with a search query string to a malicious file.
4. No referrer. So this page was accessed directly. (not always a valid indicator)
5. A suspicious User-Agent string that is not typically associated with a web browser.

![A sample suspicious web request showing some of the discussed indicators like suspicious IP address, timestamp, request, referrer and user agent.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753163467664.svg)

## Auditd

A native Linux utility that tracks and records events, creating an audit trail. Rules can be created for `auditd`, which determine what is logged in the `audit.log`. Rules can be highly configured to match specific conditions, such as when certain programs are run or files are modified in a particular directory. In the example below, `ausearch` is used to search for any logs matching the `web_shell` rule.

Ausearch Usage for Audit logs

           `user@tryhackme$ ausearch -k web_shell time->Wed Jul 23 06:20:36 2025  // A log matching the web_shell rule "name = /uploads/webshell.php" "OGID = www-data"`
        

## Web & Auditd Correlation

Detecting web shells effectively requires correlating multiple log sources. Combining web access and error logs with auditd provides more insight and can confirm if a file was created, modified, or executed, and by which user or process. A suspicious `POST` request in web logs can be linked to an audit event that includes a `creat` or `execve` syscall, showing a script wrote a file or ran commands. Combining this information helps us build a clearer picture of the attack sequence.

## Leverage SIEM Platforms

Some benefits of Security Information and Event Management (SIEM) platforms include: 

- Centralized log collection and correlation, which is especially useful when dealing with many different log types.
- Targeted queries can be created to uncover signs of malicious activity, including web shells.
- Allows analysts to search and analyze logs more efficiently.

## File System Analysis

An attacker's web shell must be stored somewhere. Analyzing web server files is crucial in identifying uploaded web shells or locating files modified to include a web shell payload.  
It should be noted that some platforms like WordPress and Django store page content in a database rather than a file system, so malicious code may be injected into posts, themes, or settings and won't appear in normal file system searches.

Here are some common web server directories where web shells are typically placed:

- Apache: `/var/www/html/` Default on most Linux distros
- Nginx: `/usr/share/nginx/html/` Default on many Linux setups

Even if a custom root path is configured, attackers can guess or scan for common upload directories, like `/uploads/`, `/images/`, or `/admin/` like we saw in Task 3.  
Temporary directories like `/tmp` can also be abused if file permissions are not configured securely.

**Suspicious or Random File Names**  
Can be used by attackers to evade detection. Be on the lookout for names that deviate from standard application files. 

- Monitor files with executable extensions like `.php` & `.jsp`
- Be on the lookout for double extensions to disguise malicious files `image.jpg.php`

**Helpful Commands**  
You can use `find` to search for recently modified scripts. In the example below, it is used to search the `/var/www` directory for `.php` files modified between two specific dates using the `-newerct` option. Another helpful tool, `grep`, can be used to track down suspicious functions like `eval(` within files. In the example below, we use it to search the WordPress directory `wp-content`.

Find & Grep Command Usage to Locate Specific Files

           `user@tryhackme$ find /var/www -type f -name "*.php" -newerct "2025-07-01" ! -newerct "2025-08-01" /var/www/html/uploads/awebshell.php          // Web shell created between the dates above.  user@tryhackme$ grep -r "eval(" wp-content /wp-content/uploads/awebshell2.php :eval(b64_dd($['cmd']));  // Web shell containing eval(`
        

## Network Traffic Analysis

Network traffic analysis allows analysts to go beyond logs by examining the data exchanged between a client and a server. By inspecting packet payloads, it becomes possible to observe attacker behavior on a more detailed level.

Many of the indicators that analysts need to be on the lookout for in log analysis can be applied to network traffic analysis as well.

- Unusual HTTP Methods & Request Patterns
- Suspicious User-Agents & IP Addresses
- Encoded Payloads
- Malicious Code or Commands in Request Bodies
- Unexpected Protocols or Ports
- Unexpected Resource Usage
- Web Server Processes Spawning Command Line Tools

Packet captures provide important details that can be critical in confirming an attack. For example, in a PCAP showing a `POST /upload.php` request, you might see a PHP web shell (`webshell.php`) being uploaded directly with the shell's source code visible in the payload. This level of detail helps further validate exploitation attempts.

Some useful Wireshark http filters.

- `http.request.method == “METHOD”`  
    Hunting for repeated or unusual requests can be useful
- `http.request.uri contains “.php”`  
    Can be helpful in finding suspicious or modified files
- `http.user_agent`   
    Used to locate unusual or outdated User-Agents

[Complete list](https://www.wireshark.org/docs/dfref/h/http.html) of Wireshark HTTP filters.

Taking a look at the same attack chain as Task 3 in Wireshark.

![A sample attack sequence as seen in Wireshark in which the attacker performs a directory fuzz, uploads a web shell and uses it to run commands. In the sequence, the attacker finds a valid directory, an abusable file, uploads the web shell and interacts with it.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753252373236.svg)

_Attack sequence flow through in Wireshark._

![An open packet in wireshark in which the details of the specific packet are seen. Highlighted are the form that was abused, the user agent of the attacker, the name of the web shell and the web shell php payload.](https://tryhackme-images.s3.amazonaws.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1753156856632.svg)


