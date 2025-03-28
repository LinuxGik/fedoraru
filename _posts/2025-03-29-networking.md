---
title: Сетевое администрирование
date: 2025-03-29 05:05:01 +0300  # Московское время (UTC+3)
categories: [Неофициальный FAQ по Fedora, Сетевое администрирование]
tags: [Сеть]     # TAG names should always be lowercase
description: Тут будет описание раздела
---


Хочу обезопасить свой Интернет-трафик. Какую реализацию VPN выбрать?
=======================================================================

WireGuard
^^^^^^^^^^^^^

:ref:`WireGuard <using-wireguard>` -- самый современный и актуальный протокол для VPN. Обеспечивает максимальную скорость работы за счёт реализации в виде модуля ядра Linux и надёжную криптографическую защиту от прослушивания.

OpenVPN
^^^^^^^^^^^

:ref:`OpenVPN <using-openvpn>` -- cамая популярная и стабильная в настоящее время реализация VPN. Способен работать как через UDP, так и TCP, имеет плагины маскировки под TLS, обеспечивает высокую защищённость, но имеет низкую производительность из-за постоянных переключений между режимами пользователя и ядра.

L2TP/IPSec
^^^^^^^^^^^^^^

Поддерживается большинством роутеров "из коробки", но является устаревшим. Изначально создавался для Windows, поэтому многие серверы заточены под соответствующие реализации клиентов.

PPTP
^^^^^^^^

Устаревший, `уязвимый by design <https://xakep.ru/2012/07/30/59067/>`__ протокол. Трафик, проходящий через сеть, использующую данный протокол, может быть легко расшифрован за несколько часов. Категорически не рекомендуется к применению даже на устаревшем оборудовании.

.. index:: ssh, keys, error
.. _ssh-keys-error:

При использовании SSH появляется ошибка доступа к ключам. Как исправить?
===========================================================================

См. `здесь <https://www.easycoding.org/2016/07/31/reshaem-problemu-s-ssh-klyuchami-v-fedora-24.html>`__.

.. index:: pptp, connection, error, vpn, gre, firewalld, firewall
.. _pptp-connection-error:

При установке VPN-соединения по протоколу PPTP появляется ошибка. Как исправить?
====================================================================================

Если продключение к VPN по протоколу :ref:`PPTP <vpn-selection>` не проходит из-за ошибки, включим поддержку `GRE <https://ru.wikipedia.org/wiki/GRE_(%D0%BF%D1%80%D0%BE%D1%82%D0%BE%D0%BA%D0%BE%D0%BB)>`__ в настройках :ref:`межсетевого экрана <firewalld-about>`.

Для этого выполним следующее:

.. code-block:: text

    sudo firewall-cmd --permanent --add-protocol=gre
    sudo firewall-cmd --reload

Изменения вступят в силу немедленно.

**Важно:** Некоторые интернет-провайдеры и большая часть операторов сотовой связи ограничивают передачу данных по протоколу GRE. В случае, если вы уверены, что поставщик услуг связи здесь не при чем, обратите внимание на маршрутизатор: некоторые модели бюджетных устройств также могут ограничивать трафик.

.. index:: firewalld, port forwarding, firewall
.. _firewalld-port-forwarding:

Как пробросить локальный порт на удалённый хост?
====================================================

См. `здесь <https://www.easycoding.org/2017/05/23/probrasyvaem-lokalnyj-port-na-udalyonnyj-xost.html>`__.

.. index:: openvpn, vpn, network
.. _using-openvpn:

Как поднять OpenVPN сервер в Fedora?
=======================================

См. `здесь <https://www.easycoding.org/2017/07/24/podnimaem-ovn-server-na-fedora.html>`__. В данной статье вместо **ovn** следует использовать **openvpn** во всех путях и именах юнитов.

.. index:: wireguard, vpn, network
.. _using-wireguard:

Как поднять WireGuard сервер в Fedora?
=========================================

См. `здесь <https://www.easycoding.org/2019/02/28/podnimaem-wireguard-server-na-fedora.html>`__.

.. index:: server, matrix, im
.. _matrix-server:

Как поднять свой сервер Matrix в Fedora?
===========================================

См. `здесь <https://www.easycoding.org/2018/04/15/podnimaem-sobstvennyj-matrix-server-v-fedora.html>`__.

.. index:: server, web server, http
.. _simple-web-server:

Как запустить простейший веб-сервер в Fedora?
================================================

Для запуска простейшего веб-сервера можно использовать Python и модуль, входящий в состав базового пакета:

.. code-block:: text

    python3 -m http.server 8080

Веб-сервер будет запущен на порту **8080**. В качестве webroot будет использоваться текущий рабочий каталог.

.. index:: network, configuration
.. _network-configuration:

Как лучше настраивать сетевые подключения?
=============================================

В Fedora для настройки сети используется Network Manager. Для работы с ним доступны как графические менеджеры (встроены в каждую DE), так и консольный **nm-cli**.

.. index:: multimedia, dlna, server, streaming
.. _dlna-server:

Как поднять DLNA сервер в локальной сети?
============================================

См. `здесь <https://www.easycoding.org/2018/09/08/podnimaem-dlna-server-v-fedora.html>`__.

.. index:: network speed, iperf, benchmark
.. _fedora-iperf:

Как сделать замеры скорости локальной или беспроводной сети?
================================================================

Для точных замеров производительности сети нам потребуется как минимум два компьютера (либо компьютер и мобильное устройство), а также утилита **iperf3**, присутствующая в репозиториях Fedora. Установим её:

.. code-block:: text

    sudo dnf install iperf3

На первом устройстве запустим сервер:

.. code-block:: text

    iperf3 -s

По умолчанию iperf прослушивает порт **5001/tcp** на всех доступных сетевых соединениях.

Теперь временно разрешим входящие соединения на данный порт посредством :ref:`Firewalld <firewalld-about>` (правило будет действовать до перезагрузки):

.. code-block:: text

    sudo firewall-cmd --add-port=5001/tcp

На втором устройстве запустим клиент и подключимся к серверу:

.. code-block:: text

    iperf3 -c 192.168.1.2

В качестве клиента может выступать и мобильное устройство на базе ОС Android с установленным `Network Tools <https://play.google.com/store/apps/details?id=net.he.networktools>`__. В этом случае в главном меню программы следует выбрать пункт **Iperf3**, а в окне подключения ввести:

.. code-block:: text

    -c 192.168.1.2

Параметр **-c** обязателен. Если он не указан, программа выдаст ошибку.

**192.168.1.2** -- это внутренний IP-адрес устройства в ЛВС, на котором запущен сервер. Номер порта указывать не требуется.

.. index:: ssh, rsync, sync
.. _rsync-remote:

Как передать содержимое каталога на удалённый сервер?
==========================================================

Передача содержимого локального каталога на удалённый сервер посредством rsync:

.. code-block:: text

    rsync -chavzP --delete --stats /path/to/local user@example.org:/path/to/remote

Здесь **user@example.org** -- данные для подключения к серверу, т.е. имя пользователя на удалённом сервере и хост.

.. index:: ssh, rsync, sync
.. _rsync-local:

Как получить содержимое каталога с удалённого сервера?
===========================================================

Получение содержимого каталога с удалённого сервера посредством rsync:

.. code-block:: text

    rsync -chavzP --delete --stats user@example.org:/path/to/remote /path/to/local

Здесь **user@example.org** -- данные для подключения к серверу, т.е. имя пользователя на удалённом сервере и хост.

.. index:: dns, change dns
.. _change-dns:

Как правильно указать DNS серверы в Fedora?
================================================

Для того, чтобы указать другие DNS серверы, необходимо использовать Network Manager (графический или консольный): **свойства соединения** -> страница **IPv4** -> **другие DNS серверы**.

.. index:: dns, resolv.conf, resolver
.. _dns-resolv:

Можно ли править файл /etc/resolv.conf в Fedora?
====================================================

Нет, т.к. этот файл целиком управляется Network Manager и перезаписывается при каждом изменении статуса подключения (активация-деактивация соединений, перезапуск сервиса и т.д.).

Если необходимо указать другие DNS серверы, это следует производить через :ref:`свойства <change-dns>` соответствующего соединения.

.. index:: firewall, icmp, firewalld
.. _disable-icmp:

Как можно средствами Firewalld запретить ICMP?
===================================================

По умолчанию ICMP трафик разрешён для большей части зон, поэтому запретить его можно вручную:

.. code-block:: text

    sudo firewall-cmd --zone=public --add-icmp-block={echo-request,echo-reply,timestamp-reply,timestamp-request} --permanent

Применим новые правила:

.. code-block:: text

    sudo firewall-cmd --reload

В данном примере для зоны **public** блокируются как входящие, так и исходящие ICMP ECHO и ICMP TIMESTAMP.

.. index:: firewall, firewalld, openvpn
.. _openvpn-allowed-ips:

Как средствами Firewalld разрешить подключение к OpenVPN серверу только с разрешённых IP адресов?
=====================================================================================================

Сначала отключим правило по умолчанию для :ref:`OpenVPN <using-openvpn>`, разрешающее доступ к серверу с любых IP адресов:

.. code-block:: text

    sudo firewall-cmd --zone=public --remove-service openvpn --permanent

Теперь создадим rich rule, разрешающее доступ с указанных IP-адресов (или подсетей):

.. code-block:: text

    sudo firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 source address="1.2.3.4" service name="openvpn" accept' --permanent
    sudo firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 source address="5.6.7.0/24" service name="openvpn" accept' --permanent

Применим новые правила:

.. code-block:: text

    sudo firewall-cmd --reload

Здесь **public** -- имя зоны для публичного интерфейса, **1.2.3.4** -- IP-адрес, а **5.6.7.0/24** -- подсеть, доступ для адресов из которой следует разрешить.

.. index:: firewall, firewalld, wireguard
.. _wireguard-allowed-ips:

Как средствами Firewalld разрешить подключение к WireGuard серверу только с разрешённых IP адресов?
======================================================================================================

Сначала отключим правило по умолчанию для :ref:`WireGuard <using-wireguard>`, разрешающее доступ к серверу с любых IP адресов:

.. code-block:: text

    sudo firewall-cmd --zone=public --remove-port=27015/udp --permanent

Теперь создадим rich rule, разрешающее доступ с указанных IP-адресов (или подсетей):

.. code-block:: text

    sudo firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 source address="1.2.3.4" port port=27015 protocol=udp accept' --permanent
    sudo firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 source address="5.6.7.0/24" port port=27015 protocol=udp accept' --permanent

Применим новые правила:

.. code-block:: text

    sudo firewall-cmd --reload

Здесь **27015** -- порт сервера WireGuard, **public** -- имя зоны для публичного интерфейса, **1.2.3.4** -- IP-адрес, а **5.6.7.0/24** -- подсеть, доступ для адресов из которой следует разрешить.

.. index:: ip address, external ip, curl
.. _get-external-ip:

Как узнать внешний IP адрес за NAT провайдера?
===================================================

Для этой цели можно использовать внешний сервис, возвращающий только внешний IP и утилиту **curl**:

.. code-block:: text

    curl https://ifconfig.me

.. index:: firewall, firewalld, web server, http, https, cloudflare
.. _firewalld-cloudflare:

Как средствами Firewalld разрешить подключение к веб-серверу только с IP адресов CloudFlare?
================================================================================================

При использовании CloudFlare в качестве системы защиты от DDoS атак, а также WAF, возникает необходимость разрешать входящие подключения исключительно с IP адресов данного сервиса.

Сначала отключим правило по умолчанию для веб-сервера, разрешающее доступ с любых IP адресов:

.. code-block:: text

    sudo firewall-cmd --zone=public --remove-service http --permanent
    sudo firewall-cmd --zone=public --remove-service https --permanent

Напишем небольшой скрипт ``foo-bar.sh``, который получит актуальные пулы IP-адресов и создаст rich rule, разрешающие доступ лишь с подсетей CloudFlare (`IPv4 <https://www.cloudflare.com/ips-v4>`__, `IPv6 <https://www.cloudflare.com/ips-v6>`__):

.. code-block:: bash

    #!/bin/bash
    set -ef

    API=https://www.cloudflare.com/ips-v
    ZONE=public

    function fw_add {
        local IFS=$'\n'
        local lines=($(curl -sS $API$1))
        for i in "${lines[@]}"
        do
            firewall-cmd --zone=$ZONE --add-rich-rule="rule family=ipv$1 source address=\"$i\" service name=\"http\" accept" --permanent
            firewall-cmd --zone=$ZONE --add-rich-rule="rule family=ipv$1 source address=\"$i\" service name=\"https\" accept" --permanent
        done
    }

    fw_add 4
    fw_add 6

Запустим наш скрипт:

.. code-block:: text

    sudo ./foo-bar.sh

Применим новые правила файрвола:

.. code-block:: text

    sudo firewall-cmd --reload

Здесь **public** -- имя зоны для публичного сетевого интерфейса.

.. index:: web server, http, https, cloudflare, ip
.. _cloudflare-forwarding:

Как пробросить IP адреса клиентов за CloudFlare?
====================================================

См. `здесь <https://www.easycoding.org/2013/08/12/nastraivaem-probros-ip-klientov-za-cloudflare.html>`__.

.. index:: network, icmp, mtr, traceroute
.. _using-mtr:

Как проверить наличие или отсутствие потерь пакетов до узла?
===============================================================

Для проверки работоспособности сети и наличия, либо отсутствия потерь пакетов между узлами маршрута, широко используется утилита **mtr**:

.. code-block:: text

    sudo dnf install mtr

Запустим проверку маршрута до узла **example.org**:

.. code-block:: text

    mtr example.org

Приостановить работу можно нажатием клавиши **P**, для возобновить -- **пробел**, а для выхода -- **Q**.

.. index:: network, connection, netstat, ss, socket
.. _ss-established:

Как получить список установленных сетевых соединений?
========================================================

Воспользуемся утилитой **ss** для вывода списка установленных сетевых соединений:

.. code-block:: text

    ss -tupn

.. index:: network, connection, netstat, ss, socket, unconn, listen
.. _ss-listening:

Как получить список открытых портов?
=======================================

Воспользуемся утилитой **ss** для вывода открытых портов, ожидающих входящих соединений:

.. code-block:: text

    ss -tulpn

Статус **LISTEN** означает, что TCP-порт открыт и ожидает входящих соединений. В то же время для UDP-портов будет отображаться статус **UNCONN**, т.к. этот протокол не подразумевает предварительное открытие подключений.

.. index:: hostname, network, dhcp
.. _transient-hostname:

Почему при подключении к сети имя хоста машины изменяется?
=============================================================

DHCP сервер провайдера способен выдавать помимо IP-адресов и DNS-серверов ещё и нестандартное имя хоста. Полученное таким способом значение называется *transient hostname*. Оно будет применимо с компьютеру с момента установки соединения и до отключения от соответствующей сети.

Если на компьютере имеется несколько сетевых подключений, каждое из которых предоставляет свой hostname, основным будет считаться то, чьё соединение было установлено последним.

.. index:: hostname, network, dhcp
.. _transient-disable:

Как запретить использование полученного от провайдера имени хоста?
=====================================================================

Для того, чтобы запретить использование полученного от DHCP сервера :ref:`transient hostname <transient-hostname>`, установим :ref:`статическое имя хоста <change-hostname>`.

.. index:: network, dns, resolv.conf, resolver, systemd, resolved
.. _resolved-nm:

Как переключить Network Manager на использование systemd-resolved?
=====================================================================

Начиная с Fedora 30, в комплект базовой системы входит systemd-resolved, который занимается преобразованием имён DNS в IP-адреса, имеет встроенный DNS-кэш и активирован по умолчанию.

В то же время, Network Manager с настройками по умолчанию использует собственный виртуальный файл конфигурации :ref:`resolv.conf <dns-resolv>`, игнорирующий присутствие systemd-resolved.

Для исправления этой ситуации, убедимся, что systemd-neworkd запущен и функционирует:

.. code-block:: text

    sudo systemctl enable --now systemd-resolved.service

Создадим в каталоге ``/etc/NetworkManager/conf.d`` файл ``99-resolved.conf`` следующего содержания:

.. code-block:: ini

    [main]
    dns=systemd-resolved

Убедимся, что файл ``/etc/resolv.conf`` является символической ссылкой на ``/run/NetworkManager/resolv.conf``:

.. code-block:: text

    file /etc/resolv.conf

Если по какой-то причине это не так, то внесём соответствующие правки:

.. code-block:: text

    sudo rm -f /etc/resolv.conf
    sudo ln -sf /run/NetworkManager/resolv.conf /etc/resolv.conf

Перезапустим затронутые сервисы:

.. code-block:: text

    sudo systemctl restart NetworkManager.service
    sudo systemctl restart systemd-resolved.service

Проверим, что в качестве основного сервера DNS применяется виртуальная заглушка:

.. code-block:: text

    cat /etc/resolv.conf

Если в выводе присутствует строка ``nameserver 127.0.0.53``, значит всё настроено верно.

.. index:: network, dns, resolver, resolved
.. _resolved-status:

Как проверить статус работы systemd-resolved?
================================================

Выведем статус systemd-resolved, включающий список используемых DNS серверов и общие параметры конфигурации:

.. code-block:: text

    resolvectl status

Выведем статистические данные об использовании systemd-resolved (состояние кэша, количество запросов и т.д.):

.. code-block:: text

    resolvectl statistics

.. index:: network, dns, resolv.conf, resolver, systemd, resolved
.. _resolved-default:

Как сделать systemd-resolved основным резолвером?
=====================================================

**Важно:** Начиная с Fedora 33, systemd-resolved уже используется в качестве основного системного DNS-резолвера.

Удалим существующую символическую ссылку, указывающую на Network Manager:

.. code-block:: text

    sudo rm -f /etc/resolv.conf

Установим systemd-resolved основным резолвером:

.. code-block:: text

    sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

Изменения вступят в силу немедленно.

.. index:: network, dns, resolv.conf, resolver, systemd, resolved
.. _resolved-disable:

Как отключить systemd-resolved и вернуться к прежней реализации?
====================================================================

Удалим существующую символическую ссылку, указывающую на :ref:`systemd-resolved <resolved-default>`:

.. code-block:: text

    sudo rm -f /etc/resolv.conf

Установим Network Manager основным :ref:`генератором <dns-resolv>` файла ``/etc/resolv.conf``:

.. code-block:: text

    sudo ln -sf /run/NetworkManager/resolv.conf /etc/resolv.conf

Остановим и заблокируем сервис:

.. code-block:: text

    sudo systemctl disable --now systemd-resolved.service
    sudo systemctl mask systemd-resolved.service

Изменения вступят в силу немедленно.

.. index:: network, dns, resolv.conf, resolver, resolved
.. _dns-crypt:

Можно ли зашифровать DNS при помощи TLS?
===========================================

Да, systemd-resolved, входящий в поставку системы начиная с Fedora 30, полностью поддерживает технологию `DNS-over-TLS <https://ru.wikipedia.org/wiki/DNS_%D0%BF%D0%BE%D0%B2%D0%B5%D1%80%D1%85_TLS>`__, позволяющую зашифровать весь DNS трафик устройства.

Настроим систему на использование systemd-resolved либо :ref:`совместно с Network Manager <resolved-nm>`, либо в :ref:`монопольном режиме <resolved-default>`, затем откроем файл конфигурации ``/etc/systemd/resolved.conf``:

.. code-block:: text

    sudoedit /etc/systemd/resolved.conf

Внесём следующие правки:

.. code-block:: ini

    [Resolve]
    DNS=1.1.1.1 1.0.0.1 2606:4700:4700::1111 2606:4700:4700::1001
    FallbackDNS=8.8.8.8 8.8.4.4 2001:4860:4860::8888 2001:4860:4860::8844
    #Domains=
    #LLMNR=yes
    MulticastDNS=yes
    DNSSEC=allow-downgrade
    DNSOverTLS=opportunistic
    Cache=yes
    DNSStubListener=yes
    ReadEtcHosts=yes

Здесь используются серверы `CloudFlare <https://cloudflare-dns.com/dns/>`__ с поддержкой DNS-over-TLS.

Сохраним изменения в файле и перезапустим systemd-resolved:

.. code-block:: text

    sudo systemctl restart systemd-resolved.service

Теперь в :ref:`информации об используемых DNS <resolved-status>` должна отображаться информация об использовании этой технологии.

.. index:: network, dns, resolv.conf, resolver, resolved, cache, flush
.. _resolved-flush:

Как очистить кэши systemd-resolved?
======================================

Очистим кэш systemd-resolved:

.. code-block:: text

    resolvectl flush-caches

.. index:: firewall, firewalld, service
.. _firewalld-services:

Где расположены файлы конфигурации доступных сервисов Firewalld?
===================================================================

Предустановленные файлы конфигурации служб Firewalld находятся в каталоге ``/usr/lib/firewalld/services``.

Настоятельно не рекомендуется что-либо изменять в нём ибо при следующем обновлении пакета все изменения будут потеряны. Вместо этого следует создать :ref:`пользовательское переопределение <firewalld-override>`.

.. index:: firewall, firewalld, service, override
.. _firewalld-override:

Как переопределить предустановленный сервис в Firewalld?
===========================================================

Пользовательские переопределения должны храниться в каталоге ``/etc/firewalld/services``.

В качестве примера создадим оверрайд для сервиса SSH на базе настроек по умолчанию:

.. code-block:: text

    sudo cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/ssh.xml

Откроем скопированный файл в текстовом редакторе:

.. code-block:: text

    sudoedit /etc/firewalld/services/ssh.xml

Внесём правки, добавив возможность использования порта **2222/tcp**:

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <service>
        <short>SSH</short>
        <description>Secure Shell (SSH) is a protocol.</description>
        <port protocol="tcp" port="22"/>
        <port protocol="tcp" port="2222"/>
    </service>

Перезагрузим настройки Firewalld для вступления изменений в силу:

.. code-block:: text

    sudo firewall-cmd --reload

.. index:: vpn, openvpn, ovpn, import, nmcli
.. _ovpn-import:

Как правильно импортировать подключение из OVPN файла?
=========================================================

Воспользуемся консольной утилитой **nmcli** для быстрого импортирования подключения из OVPN файла:

.. code-block:: text

    nmcli connection import file /path/to/foo-bar.ovpn type openvpn

Здесь **/path/to/foo-bar.ovpn** -- путь к OVPN файлу на диске.

Встроенные сертификаты и ключи будут автоматически импортированы и сохранены в каталоге ``~/.cert/nm-openvpn``, что не вызовет :ref:`проблем с SELinux <openvpn-selinux>`.

.. index:: port, nc, check
.. _check-port:

Как проверить открыт ли порт на удалённом сервере?
======================================================

Воспользуемся утилитой **nc** для непосредственного осуществления проверки без полного сканирования портов.

Проверим доступность сервиса на IP **1.2.3.4** с номером порта **443/tcp**:

.. code-block:: text

    nc -z -v 1.2.3.4 443

Проверим доступность сервиса на IP **1.2.3.4** с номером порта **27015/udp**:

.. code-block:: text

    nc -z -v -u 1.2.3.4 27015

.. index:: network, vpn, wireguard, lan
.. _wireguard-lan:

Как открыть доступ к локальной сети через WireGuard?
=======================================================

Для того, чтобы через VPN была также доступна локальная сеть, внесём ряд изменений в файлы конфигурации сервера и клиента :ref:`WireGuard <using-wireguard>`.

Отредактируем файл ``/etc/wireguard/wg0.conf`` сервера:

.. code-block:: text

    sudoedit /etc/wireguard/wg0.conf

В директиве ``AllowedIPs`` через запятую добавим адрес локальной подсети клиента, например **192.168.1.0/24**:

.. code-block:: text

    [Peer]
    PublicKey = CLIENT_PUBLIC_KEY
    AllowedIPs = 10.9.0.2/32, 192.168.1.0/24

Перезапустим сервис WireGuard на сервере:

.. code-block:: text

    sudo systemctl restart wg-quick@wg0.service

На клиенте, к локальной сети которого требуется получить доступ, разрешим перенаправление трафика и включим маскарадинг в :ref:`Firewalld <firewalld-about>`:

.. code-block:: text

    sudo sysctl -w net.ipv4.conf.all.forwarding=1
    sudo sysctl -w net.ipv6.conf.all.forwarding=1
    sudo firewall-cmd --zone=public --add-masquerade
    sudo firewall-cmd --zone public --add-forward

Отредактируем файл конфигурации данного клиента, дописав в ``AllowedIPs`` подсеть через запятую (однако если там уже указано **0.0.0.0/0**, то ничего более делать не требуется).

Проверим доступность компьютеров из локальной сети со стороны других клиентов WireGuard:

.. code-block:: text

    ping 192.168.1.2

Здесь вместо **192.168.1.2** укажем реально существующий адрес в локальной сети.

Если всё работает корректно, сделаем изменения на клиенте с LAN постоянными:

.. code-block:: text

    sudo bash -c "echo -e \"net.ipv4.conf.all.forwarding=1\nnet.ipv6.conf.all.forwarding=1\" > /etc/sysctl.d/99-wireguard.conf"
    sudo firewall-cmd --zone=public --add-masquerade --permanent
    sudo firewall-cmd --permanent --zone public --add-forward

Перезагрузим настройки Firewalld:

.. code-block:: text

    sudo firewall-cmd --reload

.. index:: network, chroot, dns, resolv.conf, resolver, systemd, resolved
.. _chroot-dns:

В chroot окружении не работает DNS. Как исправить?
======================================================

Из-за использования :ref:`systemd-resolved <resolved-default>` в :ref:`chroot-окружениях <chroot>` Fedora не работает преобразование имён DNS, т.е. фактически отсутствует доступ к Интернету.

Для решения этой проблемы настроим классическую временную конфигурацию:

.. code-block:: text

    mv /etc/resolv.conf /etc/resolv.conf.orig
    echo "nameserver 8.8.8.8" > /etc/resolv.conf
    chown root:root /etc/resolv.conf
    chmod 0644 /etc/resolv.conf

С этого момента DNS начнёт работать корректно.

По окончании работы обязательно восстановим предыдущие настройки:

.. code-block:: text

    rm -f /etc/resolv.conf
    mv /etc/resolv.conf.orig /etc/resolv.conf

.. index:: network, dns, server, dnsmasq
.. _dns-server:

Как установить и запустить собственный DNS-сервер?
=====================================================

См. `здесь <https://www.easycoding.org/2021/05/03/podnimaem-dns-server-dnsmasq.html>`__.

.. index:: network, ping, connectivity, privacy
.. _nm-connectivity-check:

Почему система обращается к fedoraproject.org каждые 5 минут?
===============================================================

С настройками по умолчанию, предустановленными пакетом **NetworkManager-config-connectivity-fedora**, NetworkManager проверяет доступность Интернета по таймеру каждые 5 минут посредством обращения к файлу **hotspot.txt** по протоколу HTTP и анализируя ответ сервера.

Подключение считается полнофункциональным после получения строки **OK**. При любом другом ответе -- ограниченным.

С целью сохранения приватности возможно :ref:`отключить это поведение <nm-connectivity-disable>`.

.. index:: network, ping, connectivity, privacy
.. _nm-connectivity-disable:

Как отключить автоматические запросы с целью проверки соединения?
=====================================================================

Для отключения :ref:`автоматических запросов <nm-connectivity-check>` к домену **fedoraproject.org**, удалим пакет **NetworkManager-config-connectivity-fedora**:

.. code-block:: text

    sudo dnf remove NetworkManager-config-connectivity-fedora

Перезапустим NetworkManager:

.. code-block:: text

    sudo systemctl restart NetworkManager.service
