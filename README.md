# P2: Make Forensic Copies & Verify Integrity

## Overview

This project demonstrates a digital forensics workflow using PowerShell and GitHub. The objective was to create evidence files, generate SHA256 hash values, create a forensic copy, and verify the integrity of the copied data.

---

## Step 1: Create Evidence

I created an `evidence` folder containing five text files (`file1.txt` through `file5.txt`) with sample content. These files represent original digital evidence.

---

## Step 2: Generate Hashes for Original Evidence

Using PowerShell, I calculated SHA256 hash values for each file in the `evidence` folder. The results were stored in a separate directory (`evidence-hashes`) to ensure the original evidence remained unchanged.

Example command used:

```
Get-ChildItem .\evidence | Get-FileHash -Algorithm SHA256 | Out-File .\evidence-hashes\hashes.txt
```

---

## Step 3: Create Forensic Copy

I created a forensic copy of the evidence using PowerShell:

```
Copy-Item -Path .\evidence -Destination .\evidence-copy -Recurse
```

This ensures that analysis can be performed without modifying the original evidence.

---

## Step 4: Verify Integrity of the Copy

I generated SHA256 hash values for the copied files and stored them in `evidence-copy-hashes`.

To improve comparison accuracy, I extracted only file names and hash values (excluding file paths):

```
Get-ChildItem .\evidence | Get-FileHash -Algorithm SHA256 | Select-Object @{Name='FileName';Expression={$_.Path | Split-Path -Leaf}}, Hash | Out-File .\evidence-hashes\hashes-clean.txt

Get-ChildItem .\evidence-copy | Get-FileHash -Algorithm SHA256 | Select-Object @{Name='FileName';Expression={$_.Path | Split-Path -Leaf}}, Hash | Out-File .\evidence-copy-hashes\hashes-copy-clean.txt
```

The hash values for all files matched, confirming that the forensic copy was identical to the original evidence.

---

## Step 5: Demonstrate Detection of Modification

To demonstrate integrity verification, I modified `file1.txt` in the `evidence-copy` folder and recalculated the hash values.

After comparison:

* `file1.txt` produced a different hash value 
* `file2.txt` through `file5.txt` remained unchanged 

This confirms that even a small change to a file results in a completely different SHA256 hash.

---

## Conclusion

This project demonstrates key digital forensics principles:

* Original evidence must remain unchanged
* Forensic copies are used for analysis
* Hashing verifies data integrity
* Any modification is detectable through hash comparison

---

## Hash Results

Full hash outputs are stored in:

* `evidence-hashes/hashes.txt`
* `evidence-copy-hashes/hashes-copy.txt`
* `evidence-hashes/hashes-clean.txt`
* `evidence-copy-hashes/hashes-copy-clean.txt`

## Hash Comparison Results

### Original Evidence Hashes

| File Name | SHA256 Hash |
|----------|------------|
| file1.txt | A98D4277B95E127F5CC25507490D29B2310EC731DF17BD84EC6564BEA2852A59 |
| file2.txt | A3A5B145E476CF0ADF6621E3C06057B86700385173CE4E70DEFEF31B55A7B9F0 |
| file3.txt | 170B0025D9DB2F51531E39CD304C4E5464868191C4DEF0D1D3E0693D2745377 |
| file4.txt | 5715F61A5E12A2D119F8AF9318E9A32327D238EFE98E669DF66C43F7D475642 |
| file5.txt | 5B6902FB670550FBC86678B9026487CC100E0529D8671E967B15D4DA67413E8C |

### Forensic Copy Hashes (Before Modification)

| File Name | SHA256 Hash |
|----------|------------|
| file1.txt | A98D4277B95E127F5CC25507490D29B2310EC731DF17BD84EC6564BEA2852A59 |
| file2.txt | A3A5B145E476CF0ADF6621E3C06057B86700385173CE4E70DEFEF31B55A7B9F0 |
| file3.txt | 170B0025D9DB2F51531E39CD304C4E5464868191C4DEF0D1D3E0693D2745377 |
| file4.txt | 5715F61A5E12A2D119F8AF9318E9A32327D238EFE98E669DF66C43F7D475642 |
| file5.txt | 5B6902FB670550FBC86678B9026487CC100E0529D8671E967B15D4DA67413E8C |

✔️ All hash values matched, confirming the forensic copy is identical to the original evidence.

---

### Forensic Copy Hashes (After Modification)

| File Name | SHA256 Hash |
|----------|------------|
| file1.txt | F571C1D505C22E66D33FDC6074770E83758F8F18FE138D4F1240780563EA1CC8 |
| file2.txt | A3A5B145E476CF0ADF6621E3C06057B86700385173CE4E70DEFEF31B55A7B9F0 |
| file3.txt | 170B0025D9DB2F51531E39CD304C4E5464868191C4DEF0D1D3E0693D2745377 |
| file4.txt | 5715F61A5E12A2D119F8AF9318E9A32327D238EFE98E669DF66C43F7D475642 |
| file5.txt | 5B6902FB670550FBC86678B9026487CC100E0529D8671E967B15D4DA67413E8C |


The hash for `file1.txt` changed after modification, demonstrating that hashing detects alterations.
