---
id: keydbdiagnostictool
title: KeyDB Diagnostic Tool - Diagnose performance problems
sidebar_label: keydb-diagnostic-tool
---

`keydb-diagnostic-tool` is a tool designed to help diagnose problems with your database.

Currently the tool can solely detect throughput bottlenecks in your system. It accomplishes this by overloading the server with SET commands and reading CPU usage. As such, we strongly recommend **not to run this tool against a production database.** The exact procedure is this:

1. Start a new thread with a set amount of clients. All of these clients are set to run the SET command on sequential keys repeatedly. The first client runs `SET 0`, the next runs `SET 1`, etc. (Clients on subsequent threads will not repeat keys.) All of the clients are set to run the command repeatedly in an event loop.
2. After a set period of time, read the CPU usage from the server. This is displayed on the client and used to determine if a bottleneck has been reached. 
3. Repeat.

If after step 2 the CPU usage is determined to have stopped growing, the program displays the end result to the user and terminates. Its logic is as follows: 

* If the server's CPU usage is at full load (>=96%), the server is handling peak throughput. Any further increase to the performance necessarily requires a change on the serverside. Later iterations of the diagnostic tool will help determine other problems in the case that the server is still not reaching nominal performance.
* If the server's CPU usage is not at full load (<96%) but has stopped increasing, it is not receiving the commands from the new clients. The tool in this case recommends checking network configuration. Later iterations will do more work to detect specific network problems that may exist.

Note that KeyDB servers enforce a hard limit of 2000 concurrent clients, and the tool will forcibly stop at this point. It is very unlikely that a functioning server will hit this limit before saturating its throughput, so if this happens it is very likely a misconfiguration of some kind. The total number of clients that can be started by the tool is `clients * threads`, which are both configurable (see below). However, it may stop before hitting that number.

To determine CPU usage, the tool detects how many threads the server is configured to run. This is because the kernel gives total CPU time for each thread the process is using. This information is shown to the user.

## Sample usage

    $ keydb-diagnostic-tool -h keydb.example.org
    $ Server has 2 threads.
    $ Starting...
    $ 3 threads, 150 total clients. CPU Usage Self: 284.4% (94.8% per thread), Server: 195.4% (97.7% per thread)
    $ Server is at full CPU load. If higher performance is expected, check server configuration.
    $ Done.

## Options

### -h, --host

Hostname of the KeyDB server to connect to. Default is `127.0.0.1`.

### -p, --port

Port of the KeyDB server to connect to. Default is `6379`.

### -c, --clients

Number of clients to start per thread. This many clients will be started each iteration of the loop. Default is `50`.

### -t, --threads

Maximum number of threads to start on the client. Default and maximum value is `500`.

### --time

Time to wait between starting new client threads, in milliseconds. Default is `5000` (5 seconds).

### --dbnum

Select the database number to select from. Default is `0`.

### --user <username> --password <password>

Provide credentials for KeyDB auth. Default blank.