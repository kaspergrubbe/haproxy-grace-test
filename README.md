# Running Haproxy:

```bash
$ docker compose up
```

AWS-ELB (awselb) listens on port 2000, round-robins between the loadbalancers (lb01 and lb02) and listens on `/healthy` for healthchecks.

Loadbalancer 1 (lb01) listens on 2500 for app-traffic and 2501 for stats and healthchecks, it is configured with `grace 10s`.

Loadbalancer 2 (lb02) listens on 2600 for app-traffic and 2601 for stats and healthchecks, it is **NOT** configured with `grace`.

App-server (appserver) listens on 3000, and responds with static html.

# Killing the loadbalancer with grace-enabled:

```bash
$ docker kill --signal SIGUSR1 haproxytest_lb01_1 && siege -c 30 --delay=0.1 -t20S http://localhost:2000
```

```json
{
	"transactions":			        8625,
	"availability":			      100.00,
	"elapsed_time":			       19.54,
	"data_transferred":		        0.80,
	"response_time":		        0.02,
	"transaction_rate":		      441.40,
	"throughput":			        0.04,
	"concurrency":			        7.13,
	"successful_transactions":	        8625,
	"failed_transactions":		           0,
	"longest_transaction":		        0.18,
	"shortest_transaction":		        0.00
}
```

# Killing the loadbalancer without grace:

```bash
$ docker kill --signal SIGUSR1 haproxytest_lb02_1 && siege -c 30 --delay=0.1 -t20S http://localhost:2000
```

```json
{
	"transactions":			        1508,
	"availability":			       98.37,
	"elapsed_time":			       19.61,
	"data_transferred":		        0.14,
	"response_time":		        0.28,
	"transaction_rate":		       76.90,
	"throughput":			        0.01,
	"concurrency":			       21.18,
	"successful_transactions":	        1508,
	"failed_transactions":		          25,
	"longest_transaction":		       16.04,
	"shortest_transaction":		        0.00
}
```
