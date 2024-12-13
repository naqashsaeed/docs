[![HOMER](https://camo.githubusercontent.com/c287bf83f8d5969635b5bed047a3e70854bc1840/687474703a2f2f736970636170747572652e6f72672f646174612f696d616765732f736970636170747572655f6865616465722e706e67)](http://sipcapture.org)

<p align="center">

<br>

<!--

<b>FREQUENTLY ASKED QUESTIONS</b>

<br><br>

Having issues setting up or using Homer SIP Capture? No Problem! 
<br>
Most likely someone raised and solved the same issue before - Enter <b>HOMER'S F.A.Q.</b>

</p>


----

###  GENERAL FAQ

----

* **I receive a fatal error while trying to clone GIT repository**

Please update your GIT client to the latest version (ie: 1.7.2.5) and try again

----
* **Geo Chart: Incompatible data table: Error: Table contains more columns than expected (Expecting 2 columns)**

If you enabled ```#!define WITH_HOMER_GEO``` this message indicates no data/entries in geo table

----
* **I see search results, but when i click on a message it shows an empty window**

Please upgrade Kamailio to 4.3+ and edit ```kamailio.cfg``` as follows :

```
#For old models. Not accurate insert time. System vs capture time.
#$var(a) = $var(table) + "" + $timef(%Y%m%d);
#for Kamailio >4.3 please uncomment this parameters:
$var(a) = $var(table) + "%Y%m%d";
```

----
* **My proxy has both IPv4 and IPv6 addresses and it displays as two separate hosts in Call Flows**

Go to Admin > Aliases define both the IPv4 and IPv6 address and for both define the exact same hostname. The V4/V6 flow will now correlate.

----
* **I'm running more SIP services on the same host and columns in Call Flows are not correctly disaplyed**

Please change the following parameter in your preferences: 
```
define('CFLOW_HPORT', 2);
```

----
* **I have a B2BUA and I would like to achieve leg correlation in Call-Flow**

If your B2B leg are extending/appending the original Call-ID with a suffix (A-Leg: 1234 B-Leg: 1234+suffix) you can enable the following parameters: 
```
define('BLEGCID', "b2b");
define('BLEGTAIL', "-0"); /* session-ID correlation suffix, required for b2b mode */
```

If your two Call-IDs are completely different, your B2BUA should insert an X-CID: header in the B-Leg, carrying over the A-LEG Call-ID: 
```
define('BLEGCID', "x-cid");
```
----
* **If accessing Homer-UI via a locally defined hostname or [IPv6] URL and I see PHP errors related to API calls**

Please define the same host/ip aliases on the server running Homer-UI in order for the server to refer itself using the same alias.

----
* **In Call Flow, the RTP Statistic tab is always empty**

RTP Stats in Homer OSS are currently based only on X-RTP/P-RTP stats in BYE messages generated by User-Agents (Fritzbox, Linksys, Sipura, Cisco, Aastra ...). For the P-RTP-Stats string format reference see [https://supportforums.cisco.com/servlet/JiveServlet/downloadBody/18784-102-3-46597/spaPhoneP-RTP-Stat_09292011.pdf here]

----
* **I use Kamailio/RTPProxy and I would like to generate P-RTP-Stats**

Although RTP-Stats are supposed to be generated by the UA/client to be any meaningful, if you are using Kamailio/RTPProxy you can still write *pseudo-statistics* (minimalistic, as seen from the server-side, *NOT* client-side) in your BYE messages using the data provided back to Kamailio core by RTPProxy using something as the following basic example in your script:

```
                        ## P-RTP-Stats snippet for Kamailio/RTPProxy
                        if (is_method("BYE")) {
                                setflag(FLT_ACC); # do accounting ...
                                setflag(FLT_ACCFAILED); # ... even if the transaction fails

                                $var(xrtpstat) = $(rtpstat{s.striptail,1});

                                 # Work the stats
                                 $var(rtp0) = $(var(xrtpstat){s.select,1, });
                                 $var(rtp1) = $(var(xrtpstat){s.select,2, });
                                 $var(rtp2) = $(var(xrtpstat){s.select,3, });
                                 $var(rtp3) = $(var(xrtpstat){s.select,4, });
                                 $var(rtp4) = $(var(xrtpstat){s.select,5, });
                                 if ($var(rtp0) != "" || $var(rtp1) != "")
                                 {
                                 append_hf("P-RTP-Stat:  EX=RTPProxy,PS=$var(rtp0),PR=$var(rtp1),PL=$var(rtp3)\r\n");
                                 }
                        }
```

----

### HOMER/SIPCAPTURE FAQ

----
* **I see the following error in syslog:**
```
ERROR: <core> [db.c:79]: module db_mysql does not export db_use_table function 
ERROR: sipcapture [sipcapture.c:323]: unable to bind database module 
```

This means _db_mysql.so_ was not loaded, likely because the module does not exist on the module's path (mpath) of _kamailio.cfg_

Please  go to kamalio's source directory, modules/db_mysql and check if you have db_mysql.so there. If not, execute in db_mysql:
```
make
make install
```

in other scenarios you might have to check the correct mpath in _kamailio.cfg_ and _db_mysql.so_ is on this path.

```
find /usr/local -type f -name db_mysql.so
```


----
* **I use HEP and see kamailio replying with 100 Trying, 200 OK. Why is that?**

The messages have been replied to by onreply_route; To disable replies, adjust your kamailio.cfg as follows: 

```
onreply_route {
     sip_capture();
     drop;
}
```




----
* **I have results in webhomer, but some messages are still missing. What did I do wrong ?**

Check your route block in the kamailio.cfg. If you want to capture all messages your route block must looks as follows:

```
route {
   sip_capture();
   drop;
}

```

if you wish to bypass specific methods, i.e. OPTIONS or NOTIFY,  use the following approach:

```

route {
     
    if(!(is_method("OPTIONS|NOTIFY")) {
            sip_capture();
    }
    drop;
}

onreply_route {

    if(!($rm =~"*OPTIONS|NOTIFY)$")) {
            sip_capture();
    }
    drop;
}


```

----
* **I have duplicate traffic/packets in my results**

If you enabled capturing on your all SIP Proxies, each will duplicate incoming and outgoing SIP messages to your Homer node. 

How do I fix it ?

1. if you are using captagent, update to the latest git code and create an extra pcap file filter (using the -f param): 

```
and not src host 10.0.0.1
```
where 10.0.0.1 is your sip proxy - all packets from this host will be not duplicated to Homer.

2. if you are using built-in agent, activate sip_trace() only for outgoing or incoming messages.



----
* **Partition Rotation doesn't work anymore and partoration_unixtimestamp.pl scripts reports: "DBD::mysql::db do failed: Out of resources when opening file './homer_db/sip_capture#P#p2011082512.MYD' (Errcode: 24) at partrotate_unixtimestamp.pl line 137."**

Increase open_file_limit for mySQL in your my.cnf (/etc/my.cnf)

```
open_files_limit = 24000
```

and restart mysql

----
* **No new traffic is inserted to DB and i see the following in my syslog: 'driver error on query: Table has no partition ...'**

Your partition rotation script is not running. Please verify its correctly scheduled in CRON __(to verify this is the case, execute the partrotate_unixtimestamp.pl script manually)__

----
* **No new traffic is inserted to DB and I see the following in my syslog: db_mysql [km_dbase.c:122]: driver error on query: Unknown column 'auth' in 'field list'**

You're probably using the new DB schema with older Kamailio - Add the following line to your Kamailio configuration and restart:
```
modparam("sipcapture", "authorization_column", "authorization")
```

----
* **I have a lot of SIP traffic and some SIP messages are missing...**

some tuning in my.cnf. i.e. increase innodb pool size
```
innodb_buffer_pool_size = 8G
```

and also your kernel kernel in /etc/sysctl.conf

```
net.ipv4.udp_rmem_min = 131072
net.ipv4.udp_wmem_min = 131072
net.core.netdev_max_backlog=1000
net.core.rmem_max=67108864
net.ipv4.udp_mem = 19257652
```

and increase the udp receiver's buffer in your kamailio.conf

----
* **Can we see a sample my.cnf?**

This is config for the system with 12 GB RAM:

```
max_connections = 8000
tmp_table_size  = 32M
thread_cache_size = 50
query_cache_type = 0
query_cache_size = 0
max_heap_table_size = 4G
open_files_limit = 65535
table_definition_cache = 1024
table_definition_cache = 1024
table_open_cache = 2048

#INNODB
innodb_flush_method = O_DIRECT
innodb_log_files_in_group = 2
innodb_log_file_size = 500M
innodb_flush_log_at_trx_commit = 2
innodb_file_per_table = 1
innodb_buffer_pool_size = 4G
innodb_stats_on_metadata = 0
innodb_file_format = barracuda

```

----
* **The following error appears in syslog: "ERROR: sipcapture [sipcapture.c:658]: sipcapture:hep_msg_received:  unsupported family"**

Regular SIP messages are reaching your HEP socket. Consider using a non-standard port for your HEP capture.


----
* **Where is the captagent source? It used to be bundled with Homer?**

Captagent has moved to its own [repository](https://github.com/sipcapture/captagent) starting with the new release.


----
* **I need to import a PCAP into Homer DB. Is it possible?**

Yes it sure is

*CAPTAGENT 4.x*
Download the [latest version](https://github.com/sipcapture/captagent) of captagent and use the flag -D to import a pcap. 
Please complete the [setup and configuration](https://github.com/sipcapture/captagent/wiki) before running:

```
captagent -v -D test.pcap
```


*CAPTAGENT 0.x*
If you are using the "original" captagent originally included with webHomer, use the following syntax instead:

To import a pcap to Homer with current timestamp traffic:
```
captagent -p 9060 -D test.pcap -s homerserver -n -r 5060-5080 
```
To preserve the pcap original timestamps, just use HEPv2:
```
captagent -p 9060 -D test.pcap -s homerserver -n -r 5060-5080 -H 2 -i 101

```

You can also specify the captagent 0.8 path to automate the process in webHomer by using the following preferences.php - an IMPORT button will appear in PCAP toolbox:
```
define('PCAP_AGENT', 'captagent'); /* System path captagent 0.8 bin */
define('PCAP_HEP_IP', "192.168.1.100"); /* HEP server IP */
define('PCAP_HEP_PORT', "5060"); /* HEP server PORT */
```

----
* **I use OpenSIPS 1.7.x and siptrace module does not support HEP encapsulation**

To enable HEP support in siptrace/opensips 1.7.1 do the following:

1. Download OpenSIPS SVN head 

2. Copy modules/siptrace and modules/sipcapture to the 1.7.1 source tree

3. Modify modules/siptrace/siptrace.c as follows:

```		
	-     if (dlgb.create_dlg(msg,0)<1) {
	+     if (dlgb.create_dlg(msg)<1) {
```		
4.  Recompile the siptrace module:

```		
	# make modules=modules/siptrace modules
```		

### Q: I see errors related to API calls (404 Not Found)

please be sure that you have activated mod_rewrite and added AllowOverride  All to your Directory section:

https://github.com/sipcapture/homer/wiki/webHomer-settings#apache-mod_rewrite

-----

### Q: I see no QoS Media Statistics in the UI

This might depend on your instance setting and Capture server of choice _(OpenSIPS, Kamailio)_

If your `rtcp_capture` tables have a `_ yyyymmdd"`_ partition please set the following in `preferences.php`:
```
define('RTCP_TABLE_PARTITION', 1);
```

If your `rtcp_capture` table use a non-rotating table please set the following in `preferences.php`:
```
define('RTCP_TABLE_PARTITION', 0);
```

-->
