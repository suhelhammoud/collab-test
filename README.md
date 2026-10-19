# colab-package-test

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_GITHUB_USERNAME/colab-package-test/blob/main/demo.ipynb)

A standalone experiment: install a Python package directly from GitHub with uv into the notebook kernel's Python, then import `tiny_math.add`. No runtime dependencies, website, widgets, or CI.

After publishing, open the badge, connect a Colab runtime, and choose **Runtime → Run all**. Expect `5`, a passing assertion, and a `tiny_math/__init__.py` location in the runtime's installed packages. Run all again to check reruns. The notebook creates no virtual environment.

Package source is pinned to `9265cdd0dbb1d2af544685454b40bc1caedd74ff`. This earlier commit contains the complete package; later commits add the notebook and instructions. uv, Python, and build tools are not locked.

## Publish this prepared repository

GitHub access was unavailable during preparation. No remote repository has been created, and the badge and GitHub installation URL remain templates until these steps are completed. Keep the existing Git history so the pinned package commit is published.

With GitHub CLI installed and authenticated (`gh auth login`), run from this directory:

```sh
# Fill both files with the authenticated account's username.
python - <<'PYCODE'
from pathlib import Path
import subprocess
owner = subprocess.check_output(["gh", "api", "user", "--jq", ".login"], text=True).strip()
for name in ("README.md", "demo.ipynb"):
    path = Path(name)
    path.write_text(path.read_text().replace("YOUR_GITHUB_USERNAME", owner))
PYCODE
git add README.md demo.ipynb
git commit -m "Set GitHub account links"
gh repo create colab-package-test --public --source=. --remote=origin --push
```

If that repository name already exists, stop and choose another name; do not push into an existing repository. The creation command should fail rather than reuse it.

## Verification status

Local verification uses a fresh environment and a commit-pinned local Git URL outside this checkout. This verifies package building, installation, and import isolation, but does not test GitHub fetching or Colab. An actual Colab run has not been performed.

To verify the published GitHub URL locally (substitute your username), use a disposable environment outside the checkout:

```sh
cd /tmp
uv venv colab-package-test-check
uv pip install --python /tmp/colab-package-test-check/bin/python "git+https://github.com/YOUR_GITHUB_USERNAME/colab-package-test.git@9265cdd0dbb1d2af544685454b40bc1caedd74ff"
/tmp/colab-package-test-check/bin/python -I -c 'import tiny_math; from tiny_math import add; print(add(2, 3)); assert add(2, 3) == 5; print(tiny_math.__file__)'
```

The notebook's explicit `--python sys.executable` follows [uv's interpreter targeting documentation](https://docs.astral.sh/uv/pip/environments/#using-arbitrary-python-environments).
