# Running Haproxy:

```
$ haproxy -f awselb.conf
$ haproxy -f lb01.conf
$ haproxy -f lb02.conf
$ haproxy -f appserver.conf
```

AWS-ELB (awselb) listens on port 2000, round-robins between the loadbalancers (lb01 and lb02) and listens on `2*01/healthy` for healthchecks.

Loadbalancer 1 (lb01) listens on 2500 for app-traffic and 2501 for stats and healthchecks, it is configured with `grace 10s`.

Loadbalancer 2 (lb02) listens on 2600 for app-traffic and 2601 for stats and healthchecks, it is **NOT** configured with `grace`.

App-server (appserver) listens on 3000, and responds with static-html.

# Killing the loadbalancer with grace-enabled:

```bash
kill -SIGUSR1 $(cat lb01.pid) && siege -c 30 --delay=0.1 -t20S http://localhost:2000
```

```
{	"transactions":			        5543,
	"availability":			      100.00,
	"elapsed_time":			       19.06,
	"data_transferred":		        0.51,
	"response_time":		        0.01,
	"transaction_rate":		      290.82,
	"throughput":			        0.03,
	"concurrency":			        4.22,
	"successful_transactions":	        5543,
	"failed_transactions":		           0,
	"longest_transaction":		        5.01,
	"shortest_transaction":		        0.00
}
```

# Killing the loadbalancer without grace:

```bash
kill -SIGUSR1 $(cat lb02.pid) && siege -c 30 --delay=0.1 -t20S http://localhost:2000
```

```bash
{	"transactions":			        5449,
	"availability":			       98.91,
	"elapsed_time":			       19.73,
	"data_transferred":		        0.51,
	"response_time":		        0.04,
	"transaction_rate":		      276.18,
	"throughput":			        0.03,
	"concurrency":			        9.88,
	"successful_transactions":	        5449,
	"failed_transactions":		          60,
	"longest_transaction":		        3.02,
	"shortest_transaction":		        0.00
}
```
