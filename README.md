# DPDK-MALLOCFREE-SCAN

## Purpose
debug tool for accumlating information on rte_|calloc|zalloc|mallloc and rte_free in DPDK

## Motivation
DPDK allocas like rte_malloc, rte_calloc and rte_zalloc does not map alloc region name to address. The variables are unsed. This makes it difficult to track the uasage on dynamically allocates instance. 

## Solution
- Create a container **to hold malloc|calloc}zalloc name, pointer and size**.
- on every successfull allocation fetch an element holder from fb_array and update the details.

# Steps
1.	If monitoring is to be extend to secondary process, create ‘struct rte_fbarray´ with ‘rte_memzone_reserve'
2.	In Primary process we ‘rte_fbarray_init’
3.	In secondary we ‘rte_fbarray_attach’
4.	In primary process for each alloc we save the name and pointer to element struct and store it in index retrived by ‘rte_fbarray_find_next_free’.
5.	For each alloc we mark with ‘rte_fbarray_set_used’
6.	For each free we ‘rte_fbarray_set_free’
7.	In secondary fetch the details back by ‘rte_fbarray_find_next_used,|rte_fbarray_find_next_n_used’
8.	Extend these with count and statistics with total alloc and free by extend the ‘rte_memzone_reserve’ for ‘fb_arrary+stats’

Sample application is build over DPDK example hellowowrld
```
Primary: ``./build/helloworld -c 0x10 –log-level=eal,8``
Secondary: ``./build/helloworld -c 0x100 --log-level=eal,0  --proc-type=secondary``
```

