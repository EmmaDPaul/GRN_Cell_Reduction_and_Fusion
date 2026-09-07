# GRN Cell Reduction and Complementary Graph Fusion

A compact, implementation of a two-stage GRN workflow:

1. **Representative-cell reduction:** K-medoids selects actual observed cells from a gene-by-cell expression matrix.
2. **Complementary graph fusion:** predictions from two or more GRN inference methods are normalized and fused using method evidence, rank evidence, cross-method support, local graph topology, and Node2Vec-style structural embeddings.

## Repository structure

```text
GRN_Cell_Reduction_and_Fusion/
├── README.md
├── requirements.txt
├── notebooks/
│   ├── 01_cell_reduction_kmedoids.ipynb
│   └── 02_complementary_graph_fusion.ipynb
├── data/
│   └── example/
│       ├── ExpressionData.csv
│       ├── refNetwork.csv
│       └── predictions/
│           ├── Method_A.csv
│           └── Method_B.csv
└── outputs/
```

## Installation

```bash
python -m pip install -r requirements.txt
```

Open the notebooks from the repository root or from `notebooks/`. Each notebook contains one clearly marked **USER SETTINGS** cell.

## Part 1 input format

`ExpressionData.csv` is a gene × cell matrix. The first column contains gene names:

```text
Gene,Cell_001,Cell_002,...
G01,2.1,0.3,...
G02,0.7,1.5,...
```

Part 1 writes a reduced expression matrix containing the K-medoids-selected cells plus selection/quality metadata.

## Between Part 1 and Part 2

Run the GRN inference methods of interest on the same reduced expression matrix. This repository does **not** implement those external GRN algorithms. Their output should be saved as one CSV per method.

## Part 2 prediction format

Preferred format:

```text
Gene1,Gene2,EdgeWeight
G01,G02,0.91
G01,G03,0.72
```

Several common aliases (`source`, `target`, `importance`, `score`, etc.) are accepted. `EdgeWeight` is interpreted as a **method-specific ranking score**, not as a calibrated probability. Larger must mean stronger evidence.

## Reference network

`refNetwork.csv` is optional and is used only for evaluation:

```text
Gene1,Gene2
G01,G02
G03,G05
```

The reference is never used to construct the fusion score.

## Reproducibility notes

- Part 1 exposes the K-medoids distance metric, k, normalization settings and random seed.
- Part 2 exposes fusion weights, top-edge fraction, directionality, candidate-edge mode and embedding parameters.
- The original research implementation treated GRN edges as undirected. The shareable notebook preserves this as the default (`DIRECTED=False`) but makes it explicit; use `DIRECTED=True` for directional gold standards.
- `normalized_fusion_score` is a min-max-normalized score 
## Example data

The included example is synthetic and exists only to demonstrate file formats and allow both notebooks to run immediately. It is not intended as a biological benchmark.

