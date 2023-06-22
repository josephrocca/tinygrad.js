# tinygrad.js
Just some initial experimentation around running tinygrad in the browser - not for serious usage. If someone makes a serious attempt at a production-grade tinygrad.js, I'll link to their repo from here.

## Pyodide
[Pyodide](https://github.com/pyodide/pyodide) is a port of CPython to WebAssembly/Emscripten. Pyodide makes it possible to install and run Python packages like tinygrad in the browser. It only supports CPU as of writing.

#### Demo
See [demos/pyodide](https://josephrocca.github.io/tinygrad.js/demos/pyodide).

#### Build
Just follow [the official out-of-tree Pyodide build instructions](https://pyodide.org/en/stable/development/building-and-testing-packages.html). As of writing you can just paste this in your terminal:
```bash
git clone https://github.com/geohot/tinygrad
cd tinygrad
pip install pyodide-build
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
PYODIDE_EMSCRIPTEN_VERSION=$(pyodide config get emscripten_version)
./emsdk install ${PYODIDE_EMSCRIPTEN_VERSION}
./emsdk activate ${PYODIDE_EMSCRIPTEN_VERSION}
source emsdk_env.sh
pyodide build
```
`tinygrad-0.6.0-py3-none-any.whl` is now in the `dist` folder and can be loaded into Pyodide like this:
```html
<script src="https://cdn.jsdelivr.net/pyodide/v0.23.3/full/pyodide.js"></script>
<script type="module">
  let pyodide = await loadPyodide();
  await pyodide.loadPackage("numpy");
  await pyodide.loadPackage("tinygrad-0.6.0-py3-none-any.whl");
  pyodide.runPython(`
    from tinygrad.tensor import Tensor;
    N = 128; a, b = Tensor.rand(N, N), Tensor.rand(N, N);
    c = (a.reshape(N, 1, N) * b.permute(1,0).reshape(1, N, N)).sum(axis=2);
    print((c.numpy() - (a.numpy() @ b.numpy())).mean())
  `);
</script>
```

## WebGPU
(not implemented)


## Non-Pyodide Wasm
(not implemented)
