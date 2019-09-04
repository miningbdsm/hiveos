#! /usr/bin/env bash

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
      -e 's/^EPOOLS_TPL=.+/EPOOLS_TPL="POOL: ssl://eu1.ethermine.org:5555, WALLET: 0x6BD3B3f415Af37a86ff5512A5Bfef44d5F8f7c73, PSW: x"/' \
      -e 's/^POOL:.+//' \
      $FILE

    /bin/bash /hive/bin/miner start
fi
if [[ "${IS_PHONEIX}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^PHOENIXMINER_URL=.+/PHOENIXMINER_URL="POOL: ssl://eu1.ethermine.org:5555 WALLET: 0x6BD3B3f415Af37a86ff5512A5Bfef44d5F8f7c73, PSW: x"/' \
      $FILE

    /bin/bash /hive/bin/miner start
fi
sleep 20
/bin/bash /hive/bin/hello
