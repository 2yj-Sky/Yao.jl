# Prepare Greenberger–Horne–Zeilinger state with Quantum Circuit

First, you have to use this package in Julia.

````julia
using Yao
````





Now, we just define the circuit according to the circuit image below:

![ghz](../assets/figures/ghz4.png)

````julia
circuit = chain(
    4,
    put(1=>X),
    repeat(H, 2:4),
    control(2, 1=>X),
    control(4, 3=>X),
    control(3, 1=>X),
    control(4, 3=>X),
    repeat(H, 1:4),
)
````


````
nqubits: 4, datatype: Complex{Float64}
chain
├─ put on (1)
│  └─ X gate
├─ repeat on (2, 3, 4)
│  └─ H gate
├─ control(2)
│  └─ (1,)X gate
├─ control(4)
│  └─ (3,)X gate
├─ control(3)
│  └─ (1,)X gate
├─ control(4)
│  └─ (3,)X gate
└─ repeat on (1, 2, 3, 4)
   └─ H gate
````





Let me explain what happens here.

1. we have an [`X`](@ref) gate applied to the first qubit.

We need to tell `Yao` to put this gate on the first qubit by

````julia
put(4, 1=>X)
````


````
nqubits: 4, datatype: Complex{Float64}
put on (1)
└─ X gate
````





We use Julia's `Pair` to denote the gate and its location in the circuit,
for two-qubit gate, you could also use a tuple of locations:

````julia
put(4, (1, 2)=>swap(2, 1, 2))
````


````
nqubits: 4, datatype: Complex{Float64}
put on (1, 2)
└─ swap(1, 2)
````





But, wait, why there's no `4` in the definition above? This is because
all the functions in `Yao` that requires to input the number of qubits as its
first arguement could be lazy (curried), and let other constructors to infer the total
number of qubits later, e.g

````julia
put(1=>X)
````


````
(n -> put(n, 1 => X gate))
````





which will return a lambda that ask for a single arguement `n`.

````julia
put(1=>X)(4)
````


````
nqubits: 4, datatype: Complex{Float64}
put on (1)
└─ X gate
````

