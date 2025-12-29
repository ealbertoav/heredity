# Heredity - Genetic Inheritance Probability Calculator

A Python program that uses Bayesian inference to calculate the probability distributions of genetic traits across family members, based on observed trait data and genetic inheritance patterns.

## Description

This project simulates genetic inheritance to determine the likelihood that individuals in a family carry a particular gene and exhibit an associated trait. Given a family tree with optional observed trait information, the program calculates:

- The probability that each person has 0, 1, or 2 copies of a gene
- The probability that each person exhibits or does not exhibit the associated trait

The model accounts for genetic inheritance from parents to children, including the possibility of genetic mutations during transmission.

## Background

### The Genetic Model

This program models a simplified version of genetic inheritance:

- **Gene Copies**: Each person has 0, 1, or 2 copies of a particular gene
- **Trait Expression**: The gene influences whether a person exhibits a certain trait (e.g., hearing impairment)
- **Inheritance**: Children inherit one gene copy from each parent
- **Mutation**: During transmission, there's a small probability that a gene mutates (a gene copy becomes a non-gene copy, or vice versa)

### Probability Constants

The model uses the following probabilities (defined in `PROBS`):

| Parameter       | Value | Description                                              |
|-----------------|-------|----------------------------------------------------------|
| Gene (0 copies) | 96%   | Probability of having no gene copies (no parental data)  |
| Gene (1 copy)   | 3%    | Probability of having one gene copy (no parental data)   |
| Gene (2 copies) | 1%    | Probability of having two gene copies (no parental data) |
| Trait (2 genes) | 65%   | Probability of exhibiting trait with 2 gene copies       |
| Trait (1 gene)  | 56%   | Probability of exhibiting trait with 1 gene copy         |
| Trait (0 genes) | 1%    | Probability of exhibiting trait with 0 gene copies       |
| Mutation        | 1%    | Probability of gene mutation during inheritance          |

## Requirements

- Python 3.6 or higher
- No external dependencies (uses only standard library modules: `csv`, `itertools`, `sys`)

## Installation

1. Clone or download this repository
2. Ensure Python 3 is installed on your system

```bash
git clone <repository-url>
cd heredity
```

## Usage

### Command Line Syntax

```bash
python heredity.py <data_file.csv>
```

### Example

```bash
python heredity.py data/family0.csv
```

### CSV File Format

The input CSV file must contain the following columns:

| Column   | Description                                           |
|----------|-------------------------------------------------------|
| `name`   | Person's name (unique identifier)                     |
| `mother` | Name of the person's mother (blank if unknown)        |
| `father` | Name of the person's father (blank if unknown)        |
| `trait`  | `1` if person has trait, `0` if not, blank if unknown |

**Example CSV:**
```csv
name,mother,father,trait
Harry,Lily,James,
James,,,1
Lily,,,0
```

**Rules:**
- Both `mother` and `father` must be blank, or both must reference valid names in the file
- Parent names must exist as entries in the CSV
- The `trait` column can be `0`, `1`, or empty (unknown)

### Interpreting Output

The program outputs probability distributions for each person:

```
Harry:
  Gene:
    2: 0.0092
    1: 0.4557
    0: 0.5351
  Trait:
    True: 0.2665
    False: 0.7335
```

This means:
- **Gene**: Harry has a 0.92% chance of having 2 gene copies, 45.57% chance of 1 copy, and 53.51% chance of 0 copies
- **Trait**: Harry has a 26.65% chance of exhibiting the trait and 73.35% chance of not exhibiting it

## How It Works

The program uses Bayesian inference to calculate probability distributions by:

1. **Enumerating all possible configurations**: For each possible combination of who has the gene and who exhibits the trait
2. **Calculating joint probabilities**: Computing the probability of each specific configuration
3. **Aggregating results**: Summing probabilities across all configurations for each person
4. **Normalizing**: Ensuring all probability distributions sum to 1

### Core Functions

#### `joint_probability(people, one_gene, two_genes, have_trait)`
Calculates the probability of a specific genetic configuration across all family members.

- For people without parents: Uses unconditional gene probabilities
- For people with parents: Calculates inheritance probability based on:
  - Parent's gene count
  - Mutation probability during transmission

#### `update(probabilities, one_gene, two_genes, have_trait, p)`
Adds a joint probability to the running probability distributions for each person, accumulating evidence from each possible world configuration.

#### `normalize(probabilities)`
Normalizes all probability distributions so each sums to 1, maintaining relative proportions.

## Example Output

Running the program on `family0.csv`:

```bash
$ python heredity.py data/family0.csv
Harry:
  Gene:
    2: 0.0092
    1: 0.4557
    0: 0.5351
  Trait:
    True: 0.2665
    False: 0.7335
James:
  Gene:
    2: 0.1976
    1: 0.5106
    0: 0.2918
  Trait:
    True: 1.0000
    False: 0.0000
Lily:
  Gene:
    2: 0.0036
    1: 0.0136
    0: 0.9827
  Trait:
    True: 0.0000
    False: 1.0000
```

**Interpretation**: James is known to have the trait (probability 1.0), and Lily is known not to have it (probability 0.0). Harry's trait status is unknown, so the program calculates probabilities based on inheritance from his parents.

## Data Files

The `data/` directory contains sample family datasets:

| File          | Description                                                                      |
|---------------|----------------------------------------------------------------------------------|
| `family0.csv` | Small family: Harry (child), James (father, has trait), Lily (mother, no trait)  |
| `family1.csv` | Larger family: Arthur & Molly (parents), with children Charlie, Fred, Ginny, Ron |
| `family2.csv` | Multi-generational: Includes grandparents, parents, and grandchild (Rose)        |

## Implementation Details

### Inheritance Probability

When a child inherits genes from parents, the probability of receiving a gene copy depends on the parent's gene count:

| Parent's Genes | Probability of Passing Gene |
|----------------|-----------------------------|
| 0 copies       | 1% (mutation only)          |
| 1 copy         | 50% (random selection)      |
| 2 copies       | 99% (unless mutation)       |

### Bayesian Inference

The algorithm implements exact inference by:

1. Iterating over all possible "worlds" (combinations of gene counts and trait expressions)
2. Filtering out worlds that contradict observed evidence
3. Computing the joint probability of each valid world
4. Marginalizing to get individual probability distributions

This approach guarantees exact probabilities but has exponential complexity in the number of family members.

## License

This project is provided for educational purposes.

