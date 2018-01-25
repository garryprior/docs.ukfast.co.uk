# WAF on DDoSX settings

Within the WAF tab in the DDoSX section of [MyUKFast](https://my.ukfast.co.uk) you will need to configure the various settings required to activate your WAF and control the level of security provided, as shown in the screenshot below.

![settingupwaf](files/settingupwaf.png)

## WAF modes

The different WAF modes are:

- **Off** – WAF not in use
- **Detection Only** – WAF is in test mode, will report on what traffic will be blocked based on the current rulesets and paranoia level selected, but it not actively blocking traffic.  This should be used to test your WAF settings on your domain before going live, which can help you to avoid false positives and customer experience issues
- **On** – The WAF is on and is now blocking requests as per the rulesets and paranoia level selected

```eval_rst
.. warning::

   Always run your WAF in Detection Only mode for a period of time before switching it on, otherwise you could cause issues that prevent your application from being accessible to you or your customers.

```

## Paranoia Levels

The Paranoia Level (PL) setting determines the recommended rulesets to be implemented for the protection of your application.  You can further manage the granular level of protection provided by toggling rulesets on or off, see section below on this.

With each paranoia level increase, the Core Rule Set (CRS) enables additional rules to give a higher level of security. However, higher paranoia levels increase the possibility of blocking some legitimate traffic due to false alarms (also named false positives or FPs).

- **Paranoia level 1 (default)** In this level, most core rules are enabled. PL1 is advised for beginners, installations covering many different sites and applications, and for setups with standard security requirements. With PL1 you should rarely face FPs, however if you do please contact UKFast support by raising a ticket in [MyUKFast](https://my.ukfast.co.uk).

- **Paranoia level 2** includes many extra rules, for instance enabling many regexp-based SQL and XSS injection protections, and adding extra keywords checked for code injections. PL2 is advised for moderate to experienced users needing more complete coverage, and for installations with elevated security requirements. PL2 comes with some FPs which you will need to handle.

- **Paranoia level 3** enables more rules and keyword lists, and tweaks limits on special characters used. PL3 is aimed at users experienced with handling FPs, and at installations with a high security requirement.

- **Paranoia level 4** further restricts special characters. The highest level is advised for experienced users protecting applications with very high security requirements. Running PL4 will likely produce a very high number of FPs, which have to be treated before the application can go into production.

## Rule Sets

Once you have set your paranoia level you can now decide which rules you would like to have turned on or off. The table below outlines each of the rulesets and what these mean.

- **IP REPUTATION:** A check is performed to see if the IP has already been blacklisted from previous requests, then a GeoIP lookup is performed, to determine if the IP resolves to a database of high risk countries. Following this it is checked again on IP Reputation.  The scoring becomes gradually more severe the higher the paranoia level.

- **METHOD ENFORCEMENT:** Enforces a hard list of methods, the allowed method types are as follows: GET, HEAD, POST, OPTIONS.

- **SCANNER DETECTION:** An analysis is performed of the headers of the incoming request against a list of known malicious user-agents and a threat score is assigned against the request based on what is found.

- **PROTOCOL ENFORCEMENT:** Validation of the users request line against the format specified found in the HTTP RFC, where the format is '"http:" "//" host [ ":" port ] [ abs_path [ "?" query ]]'. As well as for CONNECT, OPTIONS and GET requests.  Please see these articles on [w3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.1) and [Capec](http://capec.mitre.org/data/definitions/272.html) for references.

- **PROTOCOL ATTACK:** Looks to detect the "," character within the Content-Length / Transfer-Encoding header values.  As well as the CR/LF character combinations in the HTTP method name. Please see [this article](http://projects.webappsec.org/HTTP-Request-Smuggling) for references.

- **APPLICATION ATTACK LFI (Local File Inclusion):** A number of rules which analyse the payload for path traversal and other indicators of local file inclusion, decoders to detect binary within the payload as well as attempts to reach restricted files.

- **APPLICATION ATTACK RFI (Remote File Inclusion):** Inspecting the arguments for possible attack methods for remote file inclusion, this includes looking for PHP such as "include()", URLs containing an IP address, data ends with question marks (?) and RFI host that does not match its local host value.

- **APPLICATION ATTACK RCE (Remote Code Execution):** This ruleset detects either Unix based, or Windows based shell command injections such as foo.jpg;uname -a for example, this ruleset is also case-sensitive to prevent false positives. Efforts have been made to detect common evasion techniques such as 'l'"s".  

- **APPLICATION ATTACK PHP:** Looks to identify the injection, or upload of PHP code by inspecting cookies and arguments for "<?" "<?php" opening tags, and file paths or names ending in .php. In addition it looks for the inspection of or detection of PHP configuration files in common locations, and the detection of use of PHP variables/functions from a database of common / known items.

- **APPLICATION ATTACK XSS (Cross Site Scripting):** This ruleset contains rules which search for indications of cross-site scripting, using libinjection libraries as well as a database of common attack filters.  This includes regular expression analysis for particular phrases or character combinations which are indicators of XSS.

- **APPLICATION ATTACK SQLI (SQL Injection):**
Rules which attempt to detect the use of SQL keywords to inject commands into the application / database, using RegEx to detect the use of database names, and the libinjection module to detect SQL keyword usage, such as SELECT, DROP, INSERT etc. whilst being mindful of false positives.  See [this article](http://websec.ca/kb/sql_injection) for references.

- **APPLICATION ATTACK SESSION FIXATION:** Attempts to detect the introduction of session fixation, which is the attempt to create a false session for another user to enter.  This is done by inspecting the cookies and arguments for the setting of cookie values in HTML or SessionID parameters with off-domain referrers.  See [this article](http://projects.webappsec.org/w/page/13246960/Session%20Fixation) for a full breakdown of session fixation.

- **DATA LEAKAGES:** Analysis of the response from the server to look for potential data leakage, such as directory listings and server status codes.

- **DATA LEAKAGE SQL:**
Analyses the response body from the server looking for potential leakage of SQL information, such as error warnings compiled from a list of common errors found sending a false or prohibited SQL query to a database.  

- **DATA LEAKAGE JAVA:** Analyses the response body from the server looking at potential leakage of Java error warnings / messages when an invalid or prohibited Java query has been sent to the server.  This is to prevent giving the attacker information about applications that you are running on the backend that is not wanted.

- **DATA LEAKAGE PHP:**
Analyses the response body from the server looking at potential leakage of PHP error warnings / messages when an invalid or prohibited PHP query has been sent to the server.  This is to prevent giving the attacker information about application that you're running on the backend that is not wanted.

- **DATA LEAKAGE IIS:** Analyses the response body from the server looking at potential leakage of IIS error warnings / messages when an invalid or prohibited SQL query has been sent to the server.  This is to prevent giving the attacker information about application that you're running on the backend that is not wanted.

You can choose which rulesets to switch on or off for each of your domains, using the toggle switches as shown in the screenshot below.  Remember the paranoia level you have selected will also influence how aggressively each ruleset switched on will block traffic.

![togglerulesets](files/togglerulesets.png)