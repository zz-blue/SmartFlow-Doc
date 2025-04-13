# SmartFlow Documentation

The documentation for the **CaLES** project is written in **reStructuredText** and is hosted on **Read the Docs**.

You can view the full documentation for **SmartFlow** at the following link: https://cales-test.readthedocs.io/en/latest/

## Building the Documentation on `Read the Docs`

To build the documentation locally, you need to have the following files in the repository:

- `.readthedocs.yaml`: Configuration file for Read the Docs.
- `requirements.txt`: The dependencies required to build the documentation.

## Building the Documentation on Windows

To build the documentation, use the following command in the root directory of the project:

```
make.bat html
```

This will generate the HTML documentation, which can be found in the `build/html` directory. You can open the `index.html` file in your browser to view the documentation locally.
