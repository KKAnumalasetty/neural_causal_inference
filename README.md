# neural_causal_inference
neural_causal_inference


# 🐍 Python Multi-Version Setup & Jupyter Kernel Configuration on Windows 11 + VS Code

> A complete troubleshooting & setup guide for managing multiple Python versions using `winget`, virtual environments, and Jupyter kernels in VS Code.

---

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installing Multiple Python Versions via Winget](#installing-multiple-python-versions-via-winget)
3. [Creating Named Virtual Environments](#creating-named-virtual-environments)
4. [Activating & Verifying Your Venv](#activating--verifying-your-venv)
5. [Fixing the ExecutionPolicy Error](#fixing-the-executionpolicy-error)
6. [Removing an Existing .venv](#removing-an-existing-venv)
7. [Fixing the VS Code Auto-Activate Error After Deleting .venv](#fixing-the-vs-code-auto-activate-error-after-deleting-venv)
8. [Registering a Venv as a Jupyter Kernel](#registering-a-venv-as-a-jupyter-kernel)
9. [Selecting the Kernel in VS Code Jupyter Notebook](#selecting-the-kernel-in-vs-code-jupyter-notebook)
10. [Enable Line Numbers in Jupyter Notebook](#enable-line-numbers-in-jupyter-notebook)
11. [Fixing PyTorch DLL Initialization Error (WinError 1114)](#fixing-pytorch-dll-initialization-error-winerror-1114)
12. [Quick Reference Cheatsheet](#quick-reference-cheatsheet)

---

## Prerequisites

- Windows 11
- VS Code installed
- `winget` package manager available (built into Windows 11)
- Jupyter extension installed in VS Code

> 💡 `winget` is the Windows equivalent of `brew` on macOS — use it to install Python versions system-wide.

---

## Installing Multiple Python Versions via Winget

Install Python 3.10:
```powershell
winget install Python.Python.3.10
```

Install Python 3.14 (if not already installed):
```powershell
winget install Python.Python.3.14
```

Verify both versions are accessible via the `py` launcher:
```powershell
py -3.10 --version   # Should print Python 3.10.x
py -3.14 --version   # Should print Python 3.14.x
```

> ⚠️ **Note on `python` vs `python3` aliases:** On Windows, `python` and `python3` global aliases are determined by install order — whichever version was installed last tends to claim an alias. These are unreliable. Always use `py -3.x` or work inside an activated venv for predictable behavior.

---

## Creating Named Virtual Environments

Navigate to your project folder first, then create separate venvs for each Python version:

```powershell
py -3.10 -m venv .py_3_10_venv
py -3.14 -m venv .py_3_14_venv
```

> 💡 Using a dot prefix (e.g., `.py_3_10_venv`) keeps the folder hidden in file explorers and signals it's an environment folder, not source code.

---

## Activating & Verifying Your Venv

Activate the Python 3.10 venv:
```powershell
.py_3_10_venv\Scripts\Activate.ps1
```

Activate the Python 3.14 venv:
```powershell
.py_3_14_venv\Scripts\Activate.ps1
```

Verify the active Python version:
```powershell
python --version
```

Check which venv is currently active:
```powershell
echo $env:VIRTUAL_ENV
```

Deactivate when done:
```powershell
deactivate
```

---

## Fixing the ExecutionPolicy Error

If you see a permissions error when activating a venv, run this once:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## Removing an Existing .venv

To delete an existing venv (e.g., `.venv`):
```powershell
Remove-Item -Recurse -Force .venv
```

Verify it's gone:
```powershell
ls
```

> ⚠️ You cannot swap the Python interpreter inside an existing venv. If you need a different Python version, delete the venv and recreate it.

---

## Fixing the VS Code Auto-Activate Error After Deleting .venv

After deleting a venv, VS Code may throw this error on every new terminal:
```
The term '...\.venv\Scripts\Activate.ps1' is not recognized...
```

**Fix:**

1. Press `Ctrl + Shift + P` → type `Open Workspace Settings (JSON)`
2. Remove the stale interpreter path line:
```json
"python.defaultInterpreterPath": "c:\\...\\neural_causal_inference\\.venv\\Scripts\\python.exe"
```
3. Save the file
4. Press `Ctrl + Shift + P` → `Python: Select Interpreter` → choose your new venv

---

## Registering a Venv as a Jupyter Kernel

Every new venv needs `ipykernel` installed before it can appear as a Jupyter kernel.

**Step 1 — Activate the venv:**
```powershell
.py_3_10_venv\Scripts\Activate.ps1
```

**Step 2 — Install ipykernel:**
```powershell
pip install ipykernel
```

**Step 3 — Register the kernel:**
```powershell
python -m ipykernel install --user --name=.py_3_10_venv --display-name "Python 3.10 (.py_3_10_venv)"
```

> 💡 Use `echo $env:VIRTUAL_ENV` to confirm the exact venv name before registering.

**Repeat these steps for any new venv you create.**

---

## Selecting the Kernel in VS Code Jupyter Notebook

1. Open your `.ipynb` file in VS Code
2. Click the kernel picker in the **top right corner** (shows current kernel name)
3. Click **"Select Another Kernel"** → **"Python Environments"**
4. Select **"Python 3.10 (.py_3_10_venv)"**

Verify the correct kernel is active by running in a cell:
```python
import sys
print(sys.version)  # Should print 3.10.x
```

---

## Enable Line Numbers in Jupyter Notebook

**Option A — Toggle all cells at once:**

Press `Ctrl + Shift + P` and run:
```
Jupyter: Toggle Line Numbers
```

**Option B — Toggle a single cell:**

Click outside the cell text area so the border turns blue (command mode), then press:
```
L
```

---

## Fixing PyTorch DLL Initialization Error (WinError 1114)

If you see this error when importing `torch` or `causalnex`:
```
OSError: [WinError 1114] A dynamic link library (DLL) initialization routine failed.
Error loading "...\torch\lib\c10.dll" or one of its dependencies.
```

Try these fixes in order:

### Fix 1 — Install Visual C++ Redistributable *(most common fix)*

Download and install from Microsoft:
```
https://aka.ms/vs/17/release/vc_redist.x64.exe
```
Then **restart VS Code** and retry.

### Fix 2 — Reinstall PyTorch with the official CPU build

```powershell
pip uninstall torch -y
pip install torch --index-url https://download.pytorch.org/whl/cpu
```

### Fix 3 — Install Visual Studio Build Tools

```powershell
winget install Microsoft.VisualStudio.2022.BuildTools
```
Then restart and retry.

---

## Quick Reference Cheatsheet

| Task | Command |
|---|---|
| Install Python 3.10 | `winget install Python.Python.3.10` |
| Create venv (3.10) | `py -3.10 -m venv .py_3_10_venv` |
| Activate venv | `.py_3_10_venv\Scripts\Activate.ps1` |
| Deactivate venv | `deactivate` |
| Check active venv path | `echo $env:VIRTUAL_ENV` |
| Verify Python version | `python --version` |
| Delete a venv | `Remove-Item -Recurse -Force .venv` |
| Install Jupyter kernel support | `pip install ipykernel` |
| Register Jupyter kernel | `python -m ipykernel install --user --name=<venv_name> --display-name "<label>"` |
| Fix ExecutionPolicy error | `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| Upgrade pip | `python.exe -m pip install --upgrade pip` |

---

> 📌 **Golden Rule:** Never rely on `python` or `python3` globally on Windows. Always use `py -3.x` or work inside an activated venv for reliable, version-specific behavior.

---

*Guide based on a real troubleshooting session — Windows 11, VS Code, Python 3.10 & 3.14, CausalNex / PyTorch stack.*
