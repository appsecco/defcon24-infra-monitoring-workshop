# Pentesting the infrastructure
(what does an attacker see?)

<small>[10 minutes]</small>

---

## The Why?
A penetration testing exercise was undertaken to check if there was a possibility of gaining unauthorized access to the setup

---

## Black Box Penetration Testing

----

### Port scanning
![Port scan to discover services](images/pentest/nmapscan.png)

----

### Service enumeration
![Service enumeration scan](images/pentest/serviceenum.png)

----

### HTTP basic Auth on ports 80 and 8080
![HTTP basic Auth](images/pentest/httpAuth.png)

----

### Attempted brute force
Multiple dictionaries were tried against the HTTP Basic Auth
![Hydra HTTP Basic Brute Force](images/pentest/hydrapasscrack.png)

----

### Attempted brute force
Multiple dictionaries were tried against SSH as well
![Hydra SSH Brute Force](images/pentest/hydrasshpasscrack.png)

---

## Grey Box Penetration Testing
App credentials were provided

----

### Verbose Errors
![Verbose Kibana stack traces](images/pentest/kibana_verbose_error.png)

----

### Credential Leakage through MiTM

![Request Response having the Basic Auth header](images/pentest/BasicAuthOverHTTP.png)

----

![MITM decoded password](images/pentest/BasicAuthOverHTTP-2.png)

----

Drop us a note if you want the complete pentest report<br /> <br />
riyaz@appsecco.com

---

### [Exercises](08-exercise.md)