Code Contributors: Sibo Wang, Renchi Yang

If you have any questions, feel free to contact us. Our emails are: {wangsibo.victor, anryyang}@gmail.com.

Plase cite our paper if you choose to use our code. 

```
@inproceedings{WANGYXWY17,
  author    = {Sibo Wang and
               Renchi Yang and
               Xiaokui Xiao and
               Zhewei Wei and
               Yin Yang},
  title     = {{FORA:} Simple and Effective Approximate Single-Source Personalized
               PageRank},
  booktitle = {{SIGKDD} 2017},
  pages     = {505--514},
  year      = {2017},

}
```

# Single Source Personalized PageRank

## Tested Environment
- Ubuntu
- C++ 11
- GCC 4.8
- Boost
- cmake

## Compile
```sh
$ cmake .
$ make
```

## Parameters
```sh
./fora action_name --algo <algorithm> [options]
```
- action:
    - query
    - topk
    - build: build index, for FORA only
    - generate-ss-query: generate queries file
    - gen-exact-topk: generate ground truth by power-iterations method
    - batch-topk: for different k=i*config.k/5, i=1,2,3,4,5, compute precision
- algo: which algorithm you prefer to run
    - bippr: Bidirectional PPR
    - fora: FORA
    - montecarlo: Monte Carlo
    - fwdpush: Forward Push
    - hubppr: HubPPR
- options
    - --prefix \<prefix\>
    - --epsilon \<epsilon\>
    - --dataset \<dataset\>
    - --query_size \<queries count\>
    - --k \<top k\>
    - --with_idx: for FORA or HubPPR
    - --hub_space \<hubppr oracle space-consumption\>
    - --exact_ppr_path \<directory to place generated ground truth\>
    - --result_dir \<directory to place results\>

## Data
The example data format is in `./data/webstanford/` folder. The data for DBLP, Pokec, Livejournal, Twitter are not included here for size limitation reason. You can find them online.

## Generate queries
Generate query files for the graph data. Each line contains a node id.

```sh
$ ./fora generate-ss-query --prefix <data-folder> --dataset <graph-name> --query_size <query count>
```

- Example:

```sh
$ ./fora generate-ss-query --prefix ./data/ --dataset webstanford --query_size 1000
```

## Indexing
Construct index files for the graph data using a single core. (Only for FORA)

```sh
$ ./fora build --prefix <data-folder> --dataset <graph-name> --epsilon <relative error>
```
Note: the code of indexing for hubppr is not included here, please turn to the code of hubppr: https://sourceforge.net/projects/hubppr/

- Example

```sh
$ ./fora build --prefix ./data/ --dataset webstanford --epsilon 0.5
```

## Query
Process queries.

```sh
$ ./fora query --algo <algo-name> --prefix <data-folder> --dataset <graph-name> --result_dir <output-folder> --epsilon <relative error> --query_size <query count>
```

- Example:

```sh
// without index
$ ./fora query --algo fora --prefix ./data/ --dataset webstanford --epsilon 0.5 --query_size 20

// with index
$ ./fora query --algo fora --prefix ./data/ --dataset webstanford --epsilon 0.5 --query_size 20 --with_idx
```

## Top-K
Process top-k queries.

```sh
$ ./fora topk --algo <algo-name> --prefix <data-folder> --dataset <graph-name> --result_dir <output-folder> --epsilon <relative error> --query_size <query count> --k <k>
```

- Example

```sh
// without index
$ ./fora topk --algo fora --prefix ./data/ --dataset webstanford --epsilon 0.5 --query_size 20 --k 500

// with index
$ ./fora topk --algo fora --prefix ./data/ --dataset webstanford --epsilon 0.5 --query_size 20 --k 500 --with_idx
```


## Exact PPR (ground truth)
Construct ground truth for the generated queries.

```sh
$ ./fora gen-exact-topk --prefix <data-folder> --dataset <graph-name> --k <k> --query_size <query count> --exact_ppr_path <folder to save exact ppr>
```

- Example

```sh
$ mkdir ./exact
$ ./fora gen-exact-topk --prefix ./data/ --dataset webstanford --k 1000 --query_size 100 --exact_ppr_path ./exact/
```


## Batch Top-K
Specify k value, and process queries for various k values (k/5, 2k/5, 3k/5, 4k/5, k), the folder contains ground truth need to be specified to obtain precision.

```sh
$ ./fora batch-topk --algo <algo-name> --prefix <data-folder> --dataset <graph-name> --result_dir <output-folder> --epsilon <relative error> --query_size <query count> --k <k> --exact_ppr_path <folder contains ground truth>
```

- Example

```sh
$ ./fora batch-topk --algo fora --prefix ./data/ --dataset webstanford --epsilon 0.5 --query_size 20 --k 500 --exact_ppr_path ./exact/
```
