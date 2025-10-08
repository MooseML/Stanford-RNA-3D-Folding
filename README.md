# 🧬 RNA 3D Folding: Boltz Inference Pipeline 

This repository provides a [Boltz RNA 3D folding model](https://huggingface.co/boltz-community/boltz-1) inference workflow, adapted for the **Stanford RNA 3D Folding Kaggle competition**.  
It automates dataset preparation, model inference, and result aggregation into the competition submission format.

---

## Project Structure

```

├── inference.py                         # Core inference script (runs Boltz-1 model)
├── generate_yaml_inputs.py              # Converts test_sequences.csv -> YAML inputs
├── execute_inference.py                 # Runs inference.py as a subprocess
├── gather_results.py                    # Collects predictions and builds submission.csv
├── submission.csv                       # Final formatted submission file (output)
├── /inputs_prediction/                  # Auto-generated YAML inputs per sequence
├── /outputs_prediction/                 # Model outputs (predictions, MSAs, structures)
└── /kaggle/input/...                    # Kaggle dataset mounts

````

---

## Pipeline Overview

1. **Generate YAML Inputs**
    ```python
    from generate_yaml_inputs import generate_yaml_inputs
    generate_yaml_inputs(
        csv_path="/kaggle/input/stanford-rna-3d-folding/test_sequences.csv",
        output_dir="/kaggle/working/inputs_prediction",
    )
    ```

Converts the test sequences CSV into Boltz-compatible `.yaml` input files.

2. **Run Model Inference**

    ```bash
    !python inference.py
    ```

    or (in notebook form)

    ```python
    from execute_inference import run_inference_script
    result = run_inference_script("python", "inference.py")
    ```

    Uses `inference.py` to:

    * Download Boltz-1 model & CCD dictionary
    * Preprocess inputs (FASTA/YAML -> MSA -> NPZ)
    * Run structure prediction with `Boltz1`
    * Save predicted 3D structures + confidence metrics

3. **Gather Results**

    ```python
    from gather_results import aggregate_predictions
    aggregate_predictions()
    ```

    Reads the predicted `.cif` and confidence `.json` files, extracts **C1′ coordinates**, ranks models by confidence, and populates the official competition `submission.csv`.

---

## Requirements

The project expects the **Boltz** model dependencies and competition environment:

* Python ≥ 3.9
* PyTorch Lightning ≥ 2.0
* Biopython
* PyTorch + CUDA (GPU recommended)
* `boltz-community/boltz-1` model files (auto-downloaded from Hugging Face)

Install locally with:

```bash
pip install torch pytorch-lightning biopython tqdm click
```

---

## Key Components

| Module                    | Purpose                                                                     |
| ------------------------- | --------------------------------------------------------------------------- |
| `inference.py`            | Full pipeline to load model, preprocess data, and generate predictions.     |
| `generate_yaml_inputs.py` | Converts CSV input sequences into Boltz YAML files.                         |
| `execute_inference.py`    | Handles inference subprocess execution with GPU memory cleanup and logging. |
| `gather_results.py`       | Aggregates prediction outputs into competition-ready submission file.       |

---

## Output

After successful execution:

```
submission.csv
```

contains:

* Columns `x_1, y_1, z_1, …, x_5, y_5, z_5`
* Each row corresponding to an RNA atom
* Top 5 Boltz predictions (ranked by confidence)

---

**Maintainer:** *Matt Graham*
**Last Updated:** May 2025

```

---
