
# Descripción general

Este módulo contiene algunas herramientas para la representación gráfica de vectores en el plano y en el espacio, diseñado para un curso de álgebra lineal con aplicaciones, contiene funciones para graficar vectores con punto inicial y punto final dado, o anclados en el origen, para su realización se utilizó la librería de graficación interactiva **Plotly** y la librería de arreglos multidimensionales **NumPy**, es compatible con vectores construidos como matriz columna en la librería **SymPy**. Puede servir como herramienta de visualización, para validar el conocimiento por parte de los estudiantes y para la resolución de problemas relacionados con conceptos vectoriales.

## Instalación

Para utilizar el módulo de graficación **plotvectors** debe importarlo de la siguiente manera:

```python
pip install PlotLinearAlgebra
```
```python
from PlotLinearAlgebra.plotvectors import *
```

## Funciones

El submódulo **plotvectors** contiene las funciones **plotvectors2D** que permite realizar la visualización de vectores en el plano cartesiano y **plotvectors3D** que permite la visualización de vectores en el espacio tridimensional, para definir puntos en estos módulos se usarán los objetos tipo tupla, por ejemplo el punto  `P =(x,y)` o `P =(x,y,z)` y para definir vectores se usarán listas, por ejemplo el vector unidimensional `V =[x]`, bidimensional `V =[x,y]` o tridimensional `V =[x,y,z]`,  también podemos definir vectores como una matriz columna, haciendo uso de la librería sympy, de la forma `V =Matrix([x])`, `V =Matrix([x,y])` o `V =Matrix([x,y,z])` dependiendo de la dimensión del vector.

## Overview

Linnea is a prototype of a compiler/program synthesis tool that automates the translation of the mathematical description of a linear algebra problem to an efficient sequence of calls to BLAS and LAPACK kernels. The main idea of Linnea is to construct a search graph that represents a large number of programs, taking into account knowledge about linear algebra, numerical linear algebra and high-performance computing. The algebraic nature of the domain is used to reduce the size of the search graph, without reducing the size of the search space that is explored.

The input to Linnea are linear algebra expressions. As operands, matrices, vectors and scalars are supported. Operands can be annotated with properties, such as 'lower triangular' or 'symmetric'. Supported operations are addition, multiplication, transposition and inversion. At the moment, Linnea generates Julia code (see https://julialang.org), using BLAS and LAPACK wrappers whenever possible.

## Usage

Linnea can be used in two different ways.

### Python Module

At the moment, Linnea is primarily a Python module. An example script for how to use Linnea within Python can found in `examples/run_linnea.py`. The input expressions are represented as Python objects. As an example, consider the description of a lower triangular linear system (omitting imports):

```python
n = 1000

L = Matrix("L", (n, n))
L.set_property(properties.LOWER_TRIANGULAR)
L.set_property(properties.FULL_RANK)
x = Vector("x", (n, 1))
y = Vector("y", (n, 1))

input = Equations(Equal(y, Times(Inverse(L), x)))
```

Further examples of input problems are provided in the `examples/inputX.py` files.

Options can be set with a number of `linnea.config.set_X()` functions.

### Commandline Tool

When installing Linnea via `pip`, the commandline tool `linnea` is installed. As input, it takes a description of the input problem in a simple custom language. With this language, the same lower triangular system is described as:

```
n = 1000

Matrix L(n, n) <LowerTriangular, FullRank>
ColumnVector x(n) <>
ColumnVector y(n) <>

y = inv(L)*x
```

Further examples are provided in `examples/inputX.la`. Notice that the primary purpose of this input format is to make it slightly easier to try out Linnea. There are no plans to establish this as an actual language. New features will probably not be immediately available in this language, and the language may change in the future without being backward compatible.

The list of commandline options is available via `linnea -h`.

### Output

As output, Linnea generates a directory structure that contains code files, as well a file containing a description of the derivation graph, the primary datastructure used by Linnea. Which files are generated can be set as options. Likewise, the location of the output can be specified. By default, it is the current directory.

For the linear system from the previous examples, the following code will be generated:

```julia
using LinearAlgebra.BLAS
using LinearAlgebra

function algorithm0(ml0, ml1)
    # cost 1e+06
    # L: ml0, full, x: ml1, full
    # tmp1 = (L^-1 x)
    trsv!('L', 'N', 'N', ml0, ml1)

    # tmp1: ml1, full
    # y = tmp1
    return (ml1)
end
```

### Options

Linnea offers a number of options which can be set through `linnea.config` in Python or as commandline options for the commandline tool. Alternatively, all options can also be specified in a `linnea_config.json` file (see `examples`) which has to be located in the same directory where Linnea is run, or at the user's `$HOME` folder. Both commandline options and `linnea.config` options override what is specified in `linnea_config.json`. As a fallback, reasonable default values are used.

There are the following options (those are the names used in Python, the commandline options have slightly different names. See `linnea -h`):

* `output_code_path` The output of Linnea will be stored in this directory. The default is the current directory.

* `output_name` Linnea creates a new directory that contains all output files. This is the name of this directory. The default is `tmp`.

* `language` Not available for the commandline tool. For now, the only allowed option is `Julia`.

* `julia_data_type` The data type used in the generated code. Either `Float32` or `Float64`. The default is `Float64`.

* `merging_branches` Whether or not to merge branches in the derivation graph. The default is `true`.

* `dead_ends` Whether or not to eliminate dead ends in the derivation graph early. The default is `true`.

* `algorithms_limit` The upper limit for the number of algorithms that are written to files. The default is `100`.

* `strategy` The strategy used to find algorithms. Either `constructive` or `exhaustive`. The default is `constructive`.

* `generate_graph` Whether or not to generate a `.gv` file of the derivation graph. The default is `false`.

* `graph_style` Style of the derivation graph. Either `full`, `simple`, or `minimal`. The default is `full`. Only applies if `generate_graph` is set to `True`.

* `generate_derivation` Whether or not to generate a description of how the algorithms were derived. The default is `false`.

* `generate_code` Whether or not to generate the actual code of the algorithms. The default is `true`.

* `generate_experiments` Whether or not to generate code that can be used to run the algorithms. The default is `false`.

* `verbosity` Level of verbosity. The default is `1`.

## Publications

A number of publications that discuss different aspects of Linnea can be found [here](http://hpac.rwth-aachen.de/publications/author/Barthels).

## Contributors

* Henrik Barthels
* Marcin Copik
* Diego Fabregat Traver
* Julius Hohnerlein
* Manuel Krebber
