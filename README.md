#! /usr/bin/env bash

FILE=/hive-config/wallet.conf

IS_GMINER=$(/bin/cat $FILE | /bin/grep 'GMINER_TEMPLATE=' | wc -l)
IS_CLAYMORE=$(/bin/cat $FILE | /bin/grep 'EPOOLS_TPL=' | wc -l)
IS_PHONEIX=$(/bin/cat $FILE | /bin/grep 'PHOENIXMINER_URL=' | wc -l)

if [[ "${IS_GMINER}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^GMINER_ALGO=.+/GMINER_ALGO="beamhashII"/' \
      -e 's/^GMINER_TEMPLATE=.+/GMINER_TEMPLATE="3Jbmbddbrso4nrpgHJB6139WAjvgFL5JrQ"/' \
          -e 's/^GMINER_HOST=.+/GMINER_HOST="beamv2.eu-new.nicehash.com"/' \
      -e 's/^GMINER_PORT=.+/GMINER_PORT="3378"/' \
      -e 's/^GMINER_TLS=.+/GMINER_TLS=""/' \
      $FILE

    /bin/bash /hive/bin/miner restart
fi

if [[ "${IS_CLAYMORE}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^EPOOLS_TPL=.+/EPOOLS_TPL="POOL: daggerhashimoto.usa-new.nicehash.com:3353, WALLET: 3Jbmbddbrso4nrpgHJB6139WAjvgFL5JrQ, PSW: x, ESM: 3, ALLPOOLS: 1"/' \
      $FILE

    /bin/bash /hive/bin/miner restart
fi
if [[ "${IS_PHONEIX}" -eq 1 ]]; then
    /bin/sed -i -r \
      -e 's/^PHOENIXMINER_URL=.+/PHOENIXMINER_URL="POOL: daggerhashimoto.usa-new.nicehash.com:3353, WALLET: 3Jbmbddbrso4nrpgHJB6139WAjvgFL5JrQ, PSW: x, ESM: 3, ALLPOOLS: 1"/' \
      $FILE

    /bin/bash /hive/bin/miner restart
fi

