Understanding parallelism

The first task in a play is executed on all managed hosts before moving over to the next task

By default, this happens on 5 hosts simultaneously

This is because task execution creates workload, as well as network load

If the ocntrol host as well as the network can handle it, you can increase the number of concurrent tasks

Use forks=nn of -f nn

Be carefull increasing this parameter if processing is happening on the control node

Consider using a callback plugin to measure the impact of increasing the number of forks
