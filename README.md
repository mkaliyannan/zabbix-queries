# zabbix-queries
Zabbix DB SQL queries to bring the trigger status.

Zabbix is a monitoring tool which can be useful monitor entire infrastructure, litrelly any services. if not already experienced take a 
look here https://zabbix.org/zabbix/zabbix.php?action=dashboard.view

Zabbix UI is not realtivly simple and userfriendly. It's good for deep analysis and not good for public view. I decided to use stashboard 
(http://www.stashboard.org/) UI to update the status on the public view based on zabbix trigger status.

## Zabbix queries get the current trigger status ###


MySQL Querires :


//to get displayed all host names.

SELECT DISTINCT host, hostid from hosts WHERE host NOT LIKE '%Template%' AND host NOT LIKE '%{%'


// To get all triggered hosts.

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



// To get a triggers based on the hostname.

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
