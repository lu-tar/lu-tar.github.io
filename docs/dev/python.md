# Python

## Show Python PATH

```python
python3 -c "import sys;print('\n'.join(sys.path))"
```
## ModuleNotFoundError in virtualenv
Si risolve puntanto al percorso python nel virtualenv
```bash
sudo venv/bin/python3 main.py
```
## Datetime
```python
now = datetime.now().strftime(CLOCK_STR))
```

## icmplib
```python
from icmplib import ping
import time
from datetime import datetime
import threading

#CLOCK_STR = "%m/%d/%Y, %H:%M:%S"
CLOCK_STR = "%H:%M:%S.%f"
host_list = ["192.168.1.1", "dns.google.com"]
#host_list = ["repubblica.it", "dns.google.com"]
SOGLIA_RTT = 10
SOGLIA_LOSS = 0

def do_ping(host):
    #print(host)
    for i in range(1,6):    
        start_polling = datetime.now().strftime(CLOCK_STR)
        
        gateway = ping(host, count=5, interval=0.1, timeout=0.5)
        rtt = gateway.avg_rtt
        loss = gateway.packet_loss
        
        end_polling = datetime.now().strftime(CLOCK_STR)

        if rtt > SOGLIA_RTT or loss > SOGLIA_LOSS:
            print(f'{host}, {start_polling}||{end_polling}, {rtt}, {loss}')

        time.sleep(0.5)

threads = []

# Creazione e avvio dei thread
for host in host_list:
    thread = threading.Thread(target=do_ping, args=(host,))

    threads.append(thread)

    thread.start()


# Attesa che tutti i thread terminino
for thread in threads:
    thread.join()

print("Tutti i thread sono terminati.")
```
