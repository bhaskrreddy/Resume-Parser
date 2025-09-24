This repo contains two Jupyter notebooks that parse resumes and write results to JSON:

traditional_resume_parser.ipynb — rule-based extraction (regex, simple NLP, fuzzy match).

llm_resume_parser.ipynb — LLM-based extraction (JSON-only prompting).

Both notebooks read the same config and ground truth, process all resumes in a folder, write one JSON per resume, and print aggregate accuracy metrics.

1) Repository layout
Resume_Parser_Git/
├─ resumes/                 # put your .pdf / .docx resumes here
│  └─ ground_truth.json     # labels used for evaluation (filename-keyed)
├─ config.yaml              # paths & options used by both notebooks
├─ requirements.txt         # Python dependencies
├─ traditional_resume_parser.ipynb
└─ llm_resume_parser.ipynb


The example ground_truth.json is filename-keyed (each key is a resume file name) with name, email, and skills arrays. 

ground_truth

2) Prerequisites

Python 3.12

pip (bundled with Python)

Git (optional, for cloning)

On Linux, if venv is missing: sudo apt-get install python3.12-venv

3) Setup
# 1) Clone or download the repo
git clone <your-repo-url> && cd Resume_Parser_Git

# 2) Create and activate a virtual environment (Python 3.12)
python3.12 -m venv .venv
# Linux/macOS:
source .venv/bin/activate
# Windows (Powershell):
# .venv\Scripts\Activate.ps1

# 3) Upgrade pip and install dependencies
python -m pip install --upgrade pip
pip install -r requirements.txt

4) Prepare input files

Resumes
Place your .pdf and/or .docx files in the resumes/ folder.
Example:

resumes/
├─ 1.pdf
├─ 2.docx
└─ 3.pdf


Ground truth
Put ground_truth.json inside resumes/, with keys matching the resume filenames exactly (including extension).
Path must be: resumes/ground_truth.json (matches config.yaml).

5) Configure once

Open config.yaml and set the minimal keys (already provided):

input_dir: resumes
output_dir: llm_out_json
ground_truth_path: resumes/ground_truth.json
print_each_resume: true
exts: [".pdf", ".docx"]


input_dir: folder containing your resumes.

output_dir: where per-resume JSON files will be written.

ground_truth_path: labels used for evaluation.

print_each_resume: true prints each resume’s JSON to the notebook output; set false to silence.

exts: which file types to process.

Make sure every resume filename in resumes/ has a matching key in ground_truth.json if you want it included in evaluation and written to JSON.

6) Run the notebooks

Start Jupyter and run all cells in each notebook:

# From the repo root (venv activated)
python -m jupyter lab
# or
python -m jupyter notebook


Then:

Open traditional_resume_parser.ipynb

Kernel → Restart & Run All

It will read config.yaml, parse resumes using regex/fuzzy rules, compare to ground truth, and write JSON files to output_dir.

The last cell prints accuracy (name/email accuracy; micro precision/recall/F1 for skills).

Open llm_resume_parser.ipynb

Kernel → Restart & Run All

It will use an LLM to extract the same schema, write JSON to output_dir, and print the same metrics.

If your LLM flow needs an API key (e.g., Groq), set it as an environment variable before launching Jupyter:

export GROQ_API_KEY="your_key_here"   # Linux/macOS
# set GROQ_API_KEY "your_key_here"   # Windows (new shell after setx)

7) Outputs

Per-resume JSON files written to output_dir (e.g., llm_out_json/1.json).
Each JSON includes a diagnostic missing_block listing the ground-truth items not captured by the extractor. This block is not used in metrics.

Aggregate metrics are printed at the end of each notebook:

name_accuracy (exact/case-normalized)

email_accuracy (case-insensitive)

skills_precision_micro, skills_recall_micro, skills_f1_micro (TP/FP/FN pooled across all resumes)

8) Tips & common issues

No JSON written for a file: its filename likely isn’t present as a key in ground_truth.json.

PDF text missing: image-only/scanned PDFs require OCR; convert to text-based PDF or DOCX first.

Kernel argument error in notebooks (Jupyter’s hidden --f): the notebooks are configured to tolerate it; if you added CLI parsing, use parse_known_args.

Package not found: ensure your venv is active (which python / where python should point to .venv).

9) Re-running

You can drop in new resumes, update ground_truth.json keys/labels, and re-run the notebooks.

Outputs in output_dir will be recreated; delete old JSON if you want a clean slate.
