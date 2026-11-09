# Identifying Mutations in S. pombe NGS Data
This Python project checks next-generation sequencing (NGS) reads against a reference sequence to find base mismatches and compare mutation patterns between treated and untreated *S. pombe* samples.

## How to Run the Program
Open `C7147_Project_1_assessment.ipynb` in Google Colab, run the **Setup Code** cells first so `reference.txt` and `reads.csv` download properly, and then click **Runtime > Run all** to run the full analysis.

## Inputs and Outputs
The code takes a 200-nucleotide reference file (`reference.txt`) and a CSV file of 6,000 reads (`reads.csv`) with columns for `read_id`, `sample`, `group`, and `sequence`. It returns lists and dictionaries of validated reads, sample summaries, and statistical comparisons, while printing out summary tables and Welch’s two-sided t-test results for the top three mismatch positions.

## Code Design

Instead of writing the top-position sorting logic twice, I made a helper function called `_get_top_positions()` that both `summarise_samples()` and `compare_positions()` use. Sorting with `(-count, position)` keeps tie-breaking consistent by always picking the earlier base position. In `validate_read()`, I stored valid bases as a `set` (`{"A", "C", "G", "T"}`) rather than a list so checking characters across thousands of reads is faster. I also added checks for `valid_reads > 0` before calculating percentages, returning `None` when a sample has no valid reads so the program does not crash with a division-by-zero error.

## Testing

1. **Invalid reads (`validate_read`)**: Passing `"ATN"` checked that both `"wrong_length"` and `"invalid_characters"` are returned in the right order without crashing.
2. **Empty sample edge case (`summarise_samples`)**: Testing a sample with zero valid reads confirmed `mismatch_percentage` safely returns `None`.
3. **Ties and sample exclusion (`compare_positions`)**: Using a small mock dataset checked that tied positions pick the earlier position and samples with zero valid reads are skipped before running the t-test.
