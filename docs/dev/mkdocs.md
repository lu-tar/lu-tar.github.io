# Mkdocs
- doc: https://www.mkdocs.org/user-guide/writing-your-docs/

## Installazione

```bash
python3 -m venv venv
source venv/bin/activate
python3 -m pip install mkdocs
pip freeze > requirements.txt
echo "venv/" >> .gitignore
echo "site/" >> .gitignore
```

## Local dev
```bash
mkdocs serve
```

## Update code
```bash
mkdocs build
mkdocs gh-deploy --config-file ../mkdocs.yml --remote-branch main
```


