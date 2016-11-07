
## Zabbix  with stashboard

      
  The goal of the project to get dashboard similar like Google (https://www.google.com/appsstatus#hl=en&v=status) and AWS status monitoring for the internal staff or your customers view.
  
  Both tools are different purposes and we want to get queried the zabbix trigger and get update to stashboard via REST API or JSON,SOAP . 


###  Zabbix Monitoring Tool

Basic useful feature list:


Zabbix is a monitoring tool which can be useful monitor entire infrastructure, litrelly any services. if not already experienced take a look here https://zabbix.org/zabbix/zabbix.php?action=dashboard.view


### Stashboard 

Stashboard is a statusboard similar like google amazon services status. It's based on Google app enginer. It's good for deep analysis and not good for public view. I decided to use stashboard (http://www.stashboard.org/) UI to update the status on the public view based on zabbix trigger status. However if you want to run locally use whisker board which is forked from stashboard and using Djano frame work based on python.





### Zabbix MySQL Queries

* Get all monitoring host names from the zabbix server :

```javascript
SELECT DISTINCT host, hostid from hosts WHERE host NOT LIKE '%Template%' AND host NOT LIKE '%{%'
```
Result :
```javascript
+--------------------------------------------+--------+
| host                                       | hostid |
+--------------------------------------------+--------+
|  Hostname1.exammple.com                    |  10132 |
|--------------------------------------------+--------+
```
* Query to get all triggered hosts:

```javascript
SELECT DISTINCT host, t.description, f.triggerid, e.acknowledged, t.value
FROM triggers t
INNER JOIN functions f ON ( f.triggerid = t.triggerid )
INNER JOIN items i ON ( i.itemid = f.itemid )
INNER JOIN hosts ON ( i.hostid = hosts.hostid )
INNER JOIN events e ON ( e.objectid = t.triggerid )
WHERE (e.eventid DIV 100000000000000)
IN (0)
AND (e.object-0)=0
AND (t.value=1 OR (t.value =0 AND unix_timestamp(now()) - t.lastchange <60))
AND hosts.status =0
AND i.status =0
AND t.status =0
GROUP BY f.triggerid
ORDER BY t.lastchange DESC
```
* Result :

```javascript
+--------------------------------------------+----------------------------------------------------------+-----------+--------------+-------+
| host                                       | description                                              | triggerid | acknowledged | value |
+--------------------------------------------+----------------------------------------------------------+-----------+--------------+-------+
| Hostname1.exammple.com                     | Zabbix agent on {HOST.NAME} is unreachable for 5 minutes |     14336 |            0 |     1 |
| Hostname2.example.com                      | Zabbix agent on {HOST.NAME} is unreachable for 5 minutes |     14274 |            0 |     1 |
+--------------------------------------------+----------------------------------------------------------+-----------+--------------+-------+
```


* To get a triggers based on the hostname.

```
SELECT DISTINCT host, t.description, f.triggerid, e.acknowledged, t.value
FROM triggers t
INNER JOIN functions f ON ( f.triggerid = t.triggerid )
INNER JOIN items i ON ( i.itemid = f.itemid )
INNER JOIN hosts ON ( i.hostid = hosts.hostid )
INNER JOIN events e ON ( e.objectid = t.triggerid )
WHERE (e.eventid DIV 100000000000000)
IN (0)
AND (e.object-0)=0
AND (t.value=1 OR (t.value =0 AND unix_timestamp(now()) - t.lastchange <60))
AND hosts.status =0
AND i.status =0
AND t.status =0
AND host = 'hostname you want to get'
GROUP BY f.triggerid
ORDER BY t.lastchange DESC
```

* Result :
```javascript
+-------------------------+----------------------------------------------------------+-----------+--------------+-------+
| host                    | description                                              | triggerid | acknowledged | value |
+-------------------------+----------------------------------------------------------+-----------+--------------+-------+
|Hostname1.example.com    | Zabbix agent on {HOST.NAME} is unreachable for 5 minutes |     14274 |            0 |     1 |
|Hostname1.example.com    | HTTP service is down on {HOST.NAME}                      |     14271 |            0 |     1 |
+-------------------------+----------------------------------------------------------+-----------+--------------+-------+
```

Rest I will add later.


