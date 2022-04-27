# AMI / AJAM

## Originate

{% tabs %}
{% tab title="Telnet (AMI)" %}
Connect to the PBX via ssh. Run the command to connect to the AMI:

```
busybox telnet 127.0.0.1 5038 
```

Perform authorization:

```
Action: Login
Username: phpagi
Secret: phpagi
Events: off

```

Perform Originate to call from 201 to 203:

```
Action: Originate
Channel: Local/201@internal-originate
Context: all_peers
Exten: 203
Priority: 1
Callerid: 201
Variable: SIPADDHEADER="Call-Info:\;answer-after=0",pt1c_cid=203,ALLOW_MULTY_ANSWER=1

```

* **SIPADDHEADER** - The header will be added to the INVITE for the internal number 201
* **pt1c\_cid** - destination number
* **ALLOW\_MULTY\_ANSWER** - If there are multiple registrations on account 201, SIPADDHEADER will be sent to all contacts
{% endtab %}

{% tab title="Curl AJAM" %}
Connect to the PBX via ssh. Run the command to connect to the AMI:

```
h='127.0.0.1';
u='phpagi';
p='phpagi';

from='201';
to='203';

# Login
tcookie="$(curl -I "http://${h}:8088/asterisk/rawman?action=login&username=${u}&secret=${p}" | grep Cookie | awk -F': ' '{ print$2 }')";

# Send Originate command to call from ${from} to ${to}
curl --cookie "${tcookie}" "http://${h}:8088/asterisk/rawman?action=originate&channel=Local/${from}@internal-originate&exten=${to}&context=all_peers&priority=1&callerid=${from}&Variable=pt1c_cid=${to},SIPADDHEADER="Call-Info:\;answer-after=0"&ALLOW_MULTY_ANSWER=1";
```
{% endtab %}
{% endtabs %}
