# Защита сети - Искрянов


## Задание 1
*Проведите разведку системы и определите, какие сетевые службы запущены на защищаемой системе:*
*sudo nmap -sA < ip-адрес >*
*sudo nmap -sT < ip-адрес >*
*sudo nmap -sS < ip-адрес >*
*sudo nmap -sV < ip-адрес >*

**sudo nmap -sS 10.130.0.22**

![pic1](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/nmap-sS.png)

>08/24/2026-19:08:29.372823  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:42468 -> 10.130.0.22:3306
>08/24/2026-19:08:29.374199  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:42468 -> 10.130.0.22:1521
>08/24/2026-19:08:29.379714  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:42468 -> 10.130.0.22:5432
>08/24/2026-19:08:29.382425  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:42468 -> 10.130.0.22:1433
>08/24/2026-19:08:29.384194  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:42468 -> 10.130.0.22:5811

Обнаружены
+ MySQL порт 3306
+ Oracle SQL порт 1521
+ PostgreSQL порт 5432
+ MSSQL порт 1433
+ VNC сканирование портов 5800-5820 - обнаружен порт 5811

**sudo nmap -sV 10.130.0.22**

![pic2](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/nmap-sS.png)

>08/24/2026-19:15:53.381437  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:41080 -> 10.130.0.22:3306
>08/24/2026-19:15:53.384659  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:41080 -> 10.130.0.22:5432
>08/24/2026-19:15:53.406829  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:41080 -> 10.130.0.22:1433
>08/24/2026-19:15:53.409479  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:41080 -> 10.130.0.22:1521

+ MySQL - 3306
+ PostgreSQL - 5432
+ MSSQL - 1433
+ Oracle - 1521

**sudo nmap -sT 10.130.0.22**
![pic3](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/nmap-sS.png)

>08/24/2026-19:18:19.975697  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:42766 -> 10.130.0.22:3306
>08/24/2026-19:18:19.980565  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:34932 -> 10.130.0.22:1433
>08/24/2026-19:18:19.992665  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:44878 -> 10.130.0.22:1521
>08/24/2026-19:18:19.999410  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.130.0.5:44444 -> 10.130.0.22:5432

те же порты, что и в прошлом сканировании.

**sudo nmap -sA 10.130.0.22**

![pic4](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/nmap-sS.png)

не выдал ни каких данных в логах suricata так как -sA отправляет пакеты без флага SYN, что, видимо, говорит о том, что suricata отрабатывается в основном правила SYN-сканирования.

Логи Fail2Ban остались пусты во время сканирования, так как данный сервис не предназначен для обнаружения сканирования, но для блокировки, после нескольких неудачных авторизаций.

## Задание 2
*Проведите атаку на подбор пароля для службы SSH:*

Изначально провёл атаку без включённого fail2ban
![pic5](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/hydra1.png)

Пароль найден
![pic6](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/accept1.png)


Затем, включил fail2ban
![pic7](https://github.com/DefAKAAlex/Zashita-seti/blob/main/IMG/fail2ban-on.png)


Статус блокировки

>def1@test-1:~$ sudo fail2ban-client status sshd
>Status for the jail: sshd
>|- Filter
>|  |- Currently failed: 1
>|  |- Total failed:     77
>|  `- File list:        /var/log/auth.log
>`- Actions
>   |- Currently banned: 1
>   |- Total banned:     1
>   `- Banned IP list:   10.130.0.5

Логи fail2ban

>def1@test-1:~$ sudo tail -50 /var/log/fail2ban.log
>2026-08-24 21:25:34,629 fail2ban.filter         [2182]: INFO    [sshd] Found 10.130.0.5 - 2026-08-24 21:25:34
>2026-08-24 21:25:34,636 fail2ban.filter         [2182]: INFO    [sshd] Found 10.130.0.5 - 2026-08-24 21:25:34
>2026-08-24 21:25:34,651 fail2ban.filter         [2182]: INFO    [sshd] Found 10.130.0.5 - 2026-08-24 21:25:34
>2026-08-24 21:25:34,684 fail2ban.filter         [2182]: INFO    [sshd] Found 10.130.0.5 - 2026-08-24 21:25:34
>2026-08-24 21:25:35,171 fail2ban.filter         [2182]: INFO    [sshd] Found 10.130.0.5 - 2026-08-24 21:25:35
>2026-08-24 21:25:35,171 fail2ban.filter         [2182]: INFO    [sshd] Found 10.130.0.5 - 2026-08-24 21:25:35
>2026-08-24 21:25:35,297 fail2ban.actions        [2182]: NOTICE  [sshd] 10.130.0.5 already banned
>2026-08-24 21:25:35,297 fail2ban.actions        [2182]: NOTICE  [sshd] 10.130.0.5 already banned
>2026-08-24 21:25:35,297 fail2ban.actions        [2182]: NOTICE  [sshd] 10.130.0.5 already banned
>2026-08-24 21:25:35,297 fail2ban.actions        [2182]: NOTICE  [sshd] 10.130.0.5 already banned
>2026-08-24 21:25:35,298 fail2ban.actions        [2182]: NOTICE  [sshd] 10.130.0.5 already banned

События в suricata

>08/24/2026-21:10:21.536375  [**] [1:2001219:20] ET SCAN Potential SSH Scan [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:46836 -> 10.130.0.22:22
>08/24/2026-21:10:21.536375  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:46836 -> 10.130.0.22:22
>08/24/2026-21:10:21.537480  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:46952 -> 10.130.0.22:22
>08/24/2026-21:10:21.536883  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:46910 -> 10.130.0.22:22
>08/24/2026-21:10:21.558328  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:46944
>08/24/2026-21:10:21.558634  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:46952
>08/24/2026-21:10:23.786539  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:46958
>08/24/2026-21:10:23.798887  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:46978 -> 10.130.0.22:22
>08/24/2026-21:10:23.800466  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:46978
>08/24/2026-21:10:23.811255  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:46994
>08/24/2026-21:10:23.811398  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:47014 -> 10.130.0.22:22
>08/24/2026-21:10:23.811635  [**] [1:2006546:9] ET SCAN LibSSH Based Frequent SSH Connections Likely BruteForce Attack [**] [Classification: Attempted Administrator Privilege Gain] [Priority: 1] {TCP} 10.130.0.5:46994 -> 10.130.0.22:22
>08/24/2026-21:10:23.811861  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:47012
>08/24/2026-21:10:23.811890  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:47014
>08/24/2026-21:10:24.433529  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:47070 -> 10.130.0.22:22
>08/24/2026-21:10:24.492942  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:47086
>08/24/2026-21:10:24.492967  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:47088
>08/24/2026-21:10:24.715251  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:47110 -> 10.130.0.22:22
>08/24/2026-21:10:26.671250  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.130.0.5:47154 -> 10.130.0.22:22
>08/24/2026-21:10:26.811162  [**] [1:2260002:1] SURICATA Applayer Detect protocol only one direction [**] [Classification: Generic Protocol Command Decode] [Priority: 3] {TCP} 10.130.0.22:22 -> 10.130.0.5:47168


После запуска Fail2Ban и проведения атаки Hydra, система успешно обнаружила попытки брутфорса.
Suricata зафиксировала подозрительную активность на сетевом уровне, а Fail2Ban проанализировал логи SSH и после 3 неудачных попыток заблокировал IP-адрес злоумышленника (10.130.0.5) с помощью iptables.

Это говорит о том, что комплексная защита работает: Suricata обнаруживает атаку, а Fail2Ban предотвращает ее продолжение, блокируя источник.
