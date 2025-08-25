# GitLab CI/CD: What this pipeline does

## Overview
- **Stages**: `build` ➜ `test`.  
  The `test` stage runs only after all `build` jobs finish successfully.

---

## Job: `create_file` (stage: **build**)
- **Image**: `alpine` (a tiny Linux container with BusyBox tools).
- **What it does**:
  1) Prints a simple log line: `we are building`  
  2) Creates a `build/` directory  
  3) Creates an empty file: `build/somefile.txt`
- **Artifacts**:
  - Saves the `build/` directory as an **artifact** so later stages can use it.
  - These artifacts belong to the pipeline run (not your repo) and are downloadable from the job page.

---

## Job: `test_job` (stage: **test**)
- **Image**: `alpine`.
- **What it does**:
  - Automatically **downloads artifacts** from the previous stage (default behavior).
  - Verifies that the file produced in `build/` exists:
    - `test -f build/somefile.txt`  
      - Exit code `0` (success) if the file exists  
      - Non-zero (failure) if it doesn’t, which **fails** the job/pipeline.

---

## Notes & handy tweaks
- This pattern demonstrates a classic “produce artifact in build, validate in test” flow.
- If you want to be explicit about which artifacts to pull, add dependencies:
  ```yaml
  test_job:
    stage: test
    image: alpine
    dependencies: [create_file]  # explicitly fetch only create_file’s artifacts
    script:
      - test -f build/somefile.txt
