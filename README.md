# CryptoMiners
Python Language


Python implementation of a CryptoCurrency stratum CPU mining client.


Command Line Interface
----------------------


 python BitcoinPhilippines.py [-h] [-o URL] [-u USERNAME] [-p PASSWORD]
                         [-O USERNAME:PASSWORD] [-a {scrypt,sha256d}] [-B] [-q]
                         [-P] [-d] [-v]

    -o URL, --url=              stratum mining server url
    -u USERNAME, --user=        username for mining server
    -p PASSWORD, --pass=        password for mining server
    -O USER:PASS, --userpass=   username:password pair for mining server

    -a, --algo                  hashing algorithm to use for proof of work (scrypt, sha256d)

    -B, --background            run in the background as a daemon

    -q, --quiet                 suppress non-errors
    -P, --dump-protocol         show all JSON-RPC chatter
    -d, --debug                 show extra debug information

    -h, --help                  show the help message and exit
    -v, --version               show program's version number and exit


    Example:
        python BitcoinPhilippines.py-o stratum+tcp://<pool>:<port> -u pinoyunknown -p bitcoinminers
        
        
        
        API
---
The API can be used by anyone wishing to create their own modified miner to learn more about the protocol, test their own pool or experiment with new algorithms.

```python
import BitcoinPhilippines
```


### Selecting a scrypt implementation (optional)

By default, the fastest detected library will be used; but if you wish to force a specific implementation:

```python
BitcoinPhilippines.set_scrypt_library(library = BitcoinPhilippines.SCRYPT_LIBRARY_AUTO)
print BitcoinPhilippines.SCRYPT_LIBRARY
```


### Experimenting with a new algorithm...

For this example, we will create a CryptoCoin based on MD5.

```python
import hashlib

# Create the Subscription object (proof-of-work should be 32 bytes long)
class SubscriptionMd5(nightminer.Subscription):
  def ProofOfWork(self, header):
    return hashlib.md5(header).digest() + ('0' * 16)
```

If you wish to manually find a few valid shares:
```python
# Create a subscription (and fill it in a bit with what a proper server would give us)
subs = SubscriptionMd5()
subs.set_subscription('my_subs_id', '00000000', 4)
subs.set_difficulty(1.0 / (2 ** 16))
subs.set_worker_name('my_fake_worker')

# Create a job
job = subs.create_job('my_job', ('0' * 64), ('0' * 118), ('0' * 110), [ ], '00000002', 'deadbeef', '01234567')

# Search for 5 shares
share_count = 0
for valid_share in job.mine():
  print "Found a valid share:", valid_share
  share_count += 1
  if share_count == 5: break

print "Hashrate:", job.hashrate
```

Or if you already have a server ready to go with your algorithm:
```python
# Register the Subscription
SubscriptionByAlgorithm['my_algo'] = SubscriptionMd5

# Start a miner
miner = Miner('stratum+tcp://localhost:3333', 'username', 'password', 'my_algo')
miner.server_forever()
```








