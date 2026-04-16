# Process-Tensor Tomography of SGD

Project page for the paper:

**Process-Tensor Tomography of SGD: Measuring non-Markovian memory via back-flow of distinguishability**  
Vasileios Sevetlidis and George Pavlidis

Planned site: <https://vasiseve.github.io/Process-Tensor-Tomography-SGD/>  
Author site: <https://vasiseve.github.io/>

## About

This repository hosts a Vue/GitHub Pages companion site for the AISTATS paper.
The page explains the two-step intervention protocol, back-flow of
distinguishability, the causal-break mechanism test, theory, experiments,
selected figures, and the bundled paper/supplement PDFs.

## Local Development

```bash
npm install
npm run dev
```

## Build

```bash
npm run build
```

## GitHub Pages

Use:

```text
Settings -> Pages -> Build and deployment -> Source: GitHub Actions
```

The workflow builds the Vue app and deploys the generated `dist/` directory.

## Future Code Repository

For the corresponding reproducibility code, use a separate repository:

```text
process-tensor-tomography-sgd-code
```

That keeps the website lightweight while giving the code release space for
training scripts, configs, experiment outputs, and plotting utilities.
