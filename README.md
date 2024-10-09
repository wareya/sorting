# Sorting Algorithm Shootout

But efficient algorithms only!

![explorer_2024-10-09_15-10-02](https://github.com/user-attachments/assets/07a77167-99e9-49aa-bf0c-713aff1d7fd6)

Grailsort is disabled in code because the implementation is wrong (memory unsafe) and crashes when run with some specific compiler versions.

As text:

| td = top-down merge sort | bu = bottom-up merge sort
----

Sort of several million items.
Times are in seconds.
Average of 5 runs each. Measurement noise is around +/-1%.
Textbook/coherent are bottom up.
Textbook, coherent, topdown, and inplace are merge sorts.
All custom sorts have an insertion sort cutoff at 16 elements.
Toolchain: gcc (g++) 14.2.0

textbook bu = only one merge block size at a time

coherent bu = merge parent blocks on the fly (cache coherent)

observations:
- quicksort/std::sort is slower than merge-based sorts on patterned data
- inplace merge sorts are slow for expensive comparators (e.g. for strings)
- merges can be adaptive by skipping presorted affixes w/ binary search
- inplace merges have a high constant complexity factor
- adaptive inplace merges are extremely efficient on nearly-presorted data
- same for reversed data

| data                        |  textbook bu |  coherent bu |  td      |  std::stable_sort |  std::sort     |  inplace td |  inplace bu |  quicksort |  grailsort | average  |
|-----------------------------|--------------|--------------|----------|-------------------|----------------|-------------|-------------|------------|------------|----------|
| presorted                   | 0.002955     | 0.002945     | 0.002949 | 0.110635          | 0.102862       | 0.002931    | 0.002963    | 0.003043   | 0.21412    | 0.049489 |
| strings                     | 0.29669      | 0.286109     | 0.287658 | 0.367133          | 0.272514       | 1.207397    | 1.248559    | 0.311226   | 0.711344   | 0.554292 |
| strings (many duplicates)   | 0.294615     | 0.287306     | 0.284571 | 0.350828          | 0.243335       | 1.133873    | 1.149729    | 0.315052   | 0.666405   | 0.525079 |
| white noise                 | 0.255115     | 0.263579     | 0.273999 | 0.250333          | 0.202988       | 0.408597    | 0.407649    | 0.228734   | 0.2939     | 0.287210 |
| white noise (u16)           | 0.219737     | 0.221889     | 0.229251 | 0.216402          | 0.167913       | 0.3565      | 0.349957    | 0.179864   | 0.264146   | 0.245073 |
| white noise (u8)            | 0.133837     | 0.12971      | 0.135755 | 0.135475          | 0.096118       | 0.21274     | 0.208939    | 0.106642   | 0.299767   | 0.162109 |
| white noise (u64)           | 0.277878     | 0.265884     | 0.274902 | 0.260782          | 0.205194       | 0.423288    | 0.425991    | 0.230398   | 0.32334    | 0.298629 |
| almost presorted            | 0.078051     | 0.042946     | 0.040693 | 0.115562          | 0.06694        | 0.009165    | 0.009288    | 0.075569   | 0.23101    | 0.074358 |
| two values                  | 0.160749     | 0.130911     | 0.123288 | 0.179878          | 0.11714        | 0.114592    | 0.111244    | 0.155793   | 0.271815   | 0.151712 |
| reversed                    | 0.129577     | 0.108662     | 0.133365 | 0.137837          | 0.068153       | 0.098938    | 0.090997    | 0.091271   | 0.275047   | 0.125983 |
| broken reversed             | 0.129254     | 0.107002     | 0.132896 | 0.140735          | 0.618776       | 0.09582     | 0.089424    | 0.090274   | 0.27377    | 0.186439 |
| reversed (many duplicates)  | 0.091525     | 0.064574     | 0.061497 | 0.108187          | 0.082326       | 0.047139    | 0.035264    | 0.100574   | 0.324933   | 0.101780 |
| reversed noisy              | 0.214779     | 0.220673     | 0.227238 | 0.20939           | 0.177896       | 0.341925    | 0.336253    | 0.202518   | 0.255063   | 0.242859 |
| sawtooth A                  | 0.131965     | 0.113584     | 0.134957 | 0.137359          | 0.686495       | 0.166494    | 0.152977    | 0.261746   | 0.306349   | 0.232436 |
| sawtooth B                  | 0.118164     | 0.094231     | 0.093133 | 0.138322          | 0.209413       | 0.29967     | 0.308996    | 0.240123   | 0.274268   | 0.197369 |
| sawtooth C                  | 0.11633      | 0.108002     | 0.10675  | 0.127919          | 0.213991       | 0.331651    | 0.335838    | 0.242858   | 0.322945   | 0.211809 |
| sawtooth D                  | 0.14986      | 0.132221     | 0.159132 | 0.173447          | 0.208113       | 0.347424    | 0.371694    | 0.237042   | 0.279567   | 0.228722 |
| many duplicates A           | 0.128982     | 0.132177     | 0.138166 | 0.130127          | 0.091785       | 0.209444    | 0.202014    | 0.104838   | 0.287983   | 0.158391 |
| many duplicates B           | 0.248606     | 0.262591     | 0.263204 | 0.240746          | 0.190023       | 0.401572    | 0.402       | 0.218674   | 0.28892    | 0.279593 |
| a bunch of crap             | 0.21051      | 0.223556     | 0.22296  | 0.197058          | 0.2558         | 0.38944     | 0.392233    | 0.291048   | 0.282704   | 0.273923 |
| appended sorted arrays      | 0.130735     | 0.110432     | 0.114015 | 0.135464          | 0.203557       | 0.17343     | 0.179057    | 0.233658   | 0.231876   | 0.168025 |
| interleaved sorted arrays   | 0.112762     | 0.093852     | 0.099044 | 0.125023          | 0.187535       | 0.154746    | 0.162789    | 0.196487   | 0.214752   | 0.149666 |
| average (presorted ignored) | 0.172844     | 0.161900     | 0.168404 | 0.184667          | 0.217429       | 0.329707    | 0.331947    | 0.195923   | 0.318091   |          |
|                             |              |              |          |                   | (avg 0.171618  |             |             |            |            |          |
|                             |              |              |          |                   | without killer |             |             |            |            |          |
|                             |              |              |          |                   | samples)       |             |             |            |            |          |
