# Sorting Algorithm Shootout

But efficient algorithms only!

![image](https://github.com/user-attachments/assets/e2831367-0c02-47e3-8cc0-10e9628784f9)

Grailsort is disabled in code because the implementation is wrong (memory unsafe) and crashes when run with some specific compiler versions.

As text:

Sort of several million items.
Times are in seconds.
Average of 5 runs each. Measurement noise is around +/-1%.
Textbook/coherent are bottom up.
Textbook, coherent, topdown, and inplace are merge sorts.
All custom sorts have an insertion sort cutoff at 32 elements.
Toolchain: gcc (g++) 14.2.0

- textbook bu = only one merge block size at a time
- coherent bu = merge parent blocks on the fly (cache coherent)
- td = top-down merge sort
- bu = bottom-up merge sort

observations:
- quicksort/std::sort is slower than merge-based sorts on patterned data
- inplace merge sorts are slow for expensive comparators (e.g. for strings)
- merges can be adaptive by skipping presorted affixes w/ binary search
- inplace merges have a high constant complexity factor
- adaptive inplace merges are extremely efficient on nearly-presorted data
- same for reversed data
- timsort excels at highly patterned data but costs elsewhere

| data                        |  textbook bu |  coherent bu |  td      |  std::stable_sort |  std::sort     |  inplace td |  inplace bu |  quicksort |  timsort | average  |
|-----------------------------|--------------|--------------|----------|-------------------|----------------|-------------|-------------|------------|----------|----------|
| presorted                   | 0.004232     | 0.004147     | 0.004138 | 0.110845          | 0.101697       | 0.00399     | 0.004178    | 0.00394    | 0.005682 | 0.026983 |
| strings                     | 0.290365     | 0.26596      | 0.279785 | 0.345382          | 0.268632       | 1.230853    | 1.269105    | 0.259375   | 0.419956 | 0.514379 |
| strings (many duplicates)   | 0.281663     | 0.282507     | 0.283612 | 0.3377            | 0.239405       | 1.130183    | 1.154608    | 0.301361   | 0.419213 | 0.492250 |
| white noise                 | 0.258053     | 0.264052     | 0.273697 | 0.255634          | 0.204307       | 0.409817    | 0.409522    | 0.186906   | 0.300537 | 0.284725 |
| white noise (u16)           | 0.212645     | 0.222899     | 0.227848 | 0.228883          | 0.168553       | 0.355101    | 0.351141    | 0.177723   | 0.270518 | 0.246146 |
| white noise (u8)            | 0.133349     | 0.13193      | 0.13278  | 0.135861          | 0.095899       | 0.216155    | 0.208584    | 0.109454   | 0.175151 | 0.148796 |
| white noise (u64)           | 0.262571     | 0.266393     | 0.279145 | 0.254993          | 0.203694       | 0.424841    | 0.42555     | 0.186183   | 0.327569 | 0.292327 |
| almost presorted            | 0.077246     | 0.044434     | 0.041227 | 0.112643          | 0.067538       | 0.009103    | 0.008883    | 0.076117   | 0.00881  | 0.049556 |
| two values                  | 0.160613     | 0.131273     | 0.123587 | 0.181018          | 0.117374       | 0.115662    | 0.111657    | 0.127614   | 0.16596  | 0.137195 |
| reversed                    | 0.13615      | 0.109148     | 0.133937 | 0.141902          | 0.068197       | 0.09708     | 0.090972    | 0.077007   | 0.004995 | 0.095488 |
| broken reversed             | 0.135864     | 0.106894     | 0.133065 | 0.143648          | 0.616616       | 0.112932    | 0.098089    | 0.078396   | 0.004524 | 0.158892 |
| reversed (many duplicates)  | 0.095764     | 0.068031     | 0.063141 | 0.107638          | 0.081948       | 0.04742     | 0.035309    | 0.085288   | 0.031436 | 0.068442 |
| reversed noisy              | 0.216874     | 0.221599     | 0.22746  | 0.21465           | 0.179412       | 0.343834    | 0.336768    | 0.166963   | 0.255734 | 0.240366 |
| sawtooth A                  | 0.134048     | 0.116191     | 0.136281 | 0.141443          | 0.681638       | 0.170128    | 0.154518    | 0.19873    | 0.11388  | 0.205206 |
| sawtooth B                  | 0.120324     | 0.093978     | 0.096864 | 0.138344          | 0.209613       | 0.303108    | 0.308392    | 0.247999   | 0.057128 | 0.175083 |
| sawtooth C                  | 0.118168     | 0.107766     | 0.109572 | 0.134186          | 0.215604       | 0.335267    | 0.336473    | 0.247344   | 0.081009 | 0.187265 |
| sawtooth D                  | 0.152922     | 0.1342       | 0.163653 | 0.17265           | 0.209484       | 0.349885    | 0.36663     | 0.246424   | 0.084294 | 0.208905 |
| many duplicates A           | 0.129977     | 0.132423     | 0.142616 | 0.131066          | 0.093561       | 0.208673    | 0.203127    | 0.1023     | 0.174206 | 0.146439 |
| many duplicates B           | 0.247483     | 0.255603     | 0.261553 | 0.246241          | 0.192307       | 0.40374     | 0.403339    | 0.188951   | 0.290261 | 0.276609 |
| a bunch of crap             | 0.208863     | 0.221636     | 0.223156 | 0.199277          | 0.258468       | 0.394369    | 0.392741    | 0.251737   | 0.269396 | 0.268849 |
| appended sorted arrays      | 0.127365     | 0.109537     | 0.111389 | 0.133619          | 0.204315       | 0.173555    | 0.178401    | 0.148559   | 0.121412 | 0.145350 |
| interleaved sorted arrays   | 0.110957     | 0.093707     | 0.097817 | 0.126366          | 0.187833       | 0.15524     | 0.163304    | 0.076295   | 0.071017 | 0.120282 |
| average (presorted ignored) | 0.171965     | 0.160960     | 0.168675 | 0.184912          | 0.217352       | 0.332712    | 0.333672    | 0.168606   | 0.173667 |          |
|                             |              |              |          |                   | (avg 0.1719    |             |             |            |          |          |
|                             |              |              |          |                   | without killer |             |             |            |          |          |
|                             |              |              |          |                   | samples)       |             |             |            |          |          |
