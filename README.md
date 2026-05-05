# image-dataset-converter-examples
Examples for the [image-dataset-converter](https://github.com/waikato-datamining/image-dataset-converter) libraries:

https://www.data-mining.co.nz/image-dataset-converter-examples/


## Local

### Installation

```bash
python3 -m venv venv
./venv/bin/pip install mkdocs==1.6.1 jinja2==3.1.6 Markdown==3.10.2 MarkupSafe==3.0.3 mkdocs-material==9.7.6 mkdocs-material-extensions==1.3.1 Pygments==2.20.0 pymdown-extensions==10.21.2 click==8.2.1
```

### Serving

```bash
./venv/bin/mkdocs serve
```

## Deploying

Any push will trigger a rebuild of the site on github via github actions:

[.github/workflows/main.yml](.github/workflows/main.yml)
