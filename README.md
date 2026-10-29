# Switch Core SNMP

Zabbix 7.4 template for HP/Aruba core switches monitored over SNMP v2c. Built
for stacked switches, so every physical component (CPU, sensor, fan, power
supply, interface) is discovered automatically with low level discovery rather
than hardcoded, which keeps a single template working across models with a
different number of stack members.

## What it monitors

| Area | Items |
|------|-------|
| Availability | ICMP ping, response time, reachability trigger |
| System | description, uptime, restart detection |
| Memory | total, used, used %, usage threshold |
| CPU (per stack member) | load, warning and critical thresholds |
| Temperature (per sensor) | value, warning and critical thresholds |
| Fans (per fan) | RPM, minimum RPM threshold |
| Power supplies (per PSU) | operational status, failure trigger |
| Interfaces (per port) | operational status, traffic in/out, link down trigger |

## Low level discovery

CPU, temperature sensors, fans, power supplies and network interfaces are all
discovered dynamically from the relevant SNMP tables (IF-MIB and the
ENTITY-MIB based sensor tables), so adding or removing a stack member needs no
template change.

## Macros

| Macro | Default | Purpose |
|-------|---------|---------|
| `{$SNMP_COMMUNITY}` | `public` | SNMP v2c community, override per host |
| `{$CPU_WARN}` / `{$CPU_CRIT}` | 80 / 90 | CPU load thresholds (%) |
| `{$MEM_WARN}` | 85 | Memory usage threshold (%) |
| `{$TEMP_WARN}` / `{$TEMP_CRIT}` | 60 / 75 | Temperature thresholds (deg C) |
| `{$FAN_MIN_RPM}` | 500 | Minimum healthy fan speed (RPM) |

## Usage

1. Import `switch_core_snmp.xml` under Data collection -> Templates.
2. Create a host with an SNMP v2c interface pointing at the switch.
3. Set `{$SNMP_COMMUNITY}` on the host and link the template.

Thresholds can be overridden per host through the macros above.



# Switch Access SNMP

Zabbix 7.4 template for HP/Aruba access switches over SNMP v2c. Same discovery
based approach as the core switch template but tuned for the access layer: it
drops per interface traffic monitoring (too many edge ports to be useful) and
adds a loop protection trap, which is the failure that matters most at the
access edge.

## What it monitors

| Area | Items |
|------|-------|
| Availability | ICMP ping, response time, reachability trigger |
| System | description, uptime, restart detection |
| Memory | total, used, used %, usage threshold |
| CPU (per stack member) | load, warning and critical thresholds |
| Temperature (per sensor) | value, warning and critical thresholds |
| Fans (per fan) | RPM, minimum RPM threshold |
| Power supplies (per PSU) | operational status, failure trigger |
| Loop protection | SNMP trap, alert on received trap |

## Low level discovery

CPU, temperature sensors, fans and power supplies are discovered from the SNMP
sensor tables, so the template adapts to the stack size on its own.

## Macros

| Macro | Default | Purpose |
|-------|---------|---------|
| `{$SNMP_COMMUNITY}` | `public` | SNMP v2c community, override per host |
| `{$CPU_WARN}` / `{$CPU_CRIT}` | 80 / 90 | CPU load thresholds (%) |
| `{$MEM_WARN}` | 85 | Memory usage threshold (%) |
| `{$TEMP_WARN}` / `{$TEMP_CRIT}` | 60 / 75 | Temperature thresholds (deg C) |
| `{$FAN_MIN_RPM}` | 500 | Minimum healthy fan speed (RPM) |

## Usage

1. Import `switch_access_snmp.xml` under Data collection -> Templates.
2. Create a host with an SNMP v2c interface and set `{$SNMP_COMMUNITY}`.
3. Link the template. For loop protection, point the switch SNMP traps at your
   Zabbix trap receiver.
