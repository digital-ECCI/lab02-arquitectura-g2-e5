[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Px-uYaj2)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=23102794&assignment_repo_type=AssignmentRepo)
# Lab02 - Sumador/Restador de 4 bits

# Integrantes

+ Alexandra Leyton Mahecha
+ Laura Camila Cuellar Guayara
+ Edgar Paternina Ortega
  
# Informe
## Documentación del diseño implementado
#### 1.1 Descripción
En este laboratorio se implementó un circuito digital capaz de realizar operaciones de suma y resta de números de 4 bits, utilizando únicamente un sumador y el concepto de complemento a 2.
El diseño fue desarrollado en Verilog, simulado en Visual Studio Code (Icarus Verilog) e implementado en Quartus.
# Objetivos 
Implementar un restador usando complemento a 2
Reutilizar un sumador de 4 bits para realizar restas
Verificar el funcionamiento mediante simulación
Implementar el diseño en hardware
### 1. Sumador/Restador
# Fundamento Teórico
A - B = A + (~B + 1)
~B → inversión de bits (complemento a 1)
+1 → complemento a 2
Esto permite usar un solo sumador para ambas operaciones.
El diseño se divide en 3 módulos principales:
+ SUMADOR 1 BIT
```verilog
module sumador ( 
    input A, 
    input B, 
    input Ci, 
    output So, 
    output Co 
);

wire x_ab;
wire cout_t;
wire a_ab;

xor (x_ab,A,B);
and (a_ab,A,B);
and (cout_t,Ci,x_ab);
xor (So,Ci,x_ab);
or (Co,cout_t,a_ab);

endmodule
```
+ Lo más importante de este módulo
Es un sumador completo (full adder)

Entradas:
A, B → bits a sumar
Ci → acarreo de entrada
Salidas:
So → resultado
Co → acarreo de salida
Este módulo es la base de todo el diseño. Sin él no se puede construir el sumador de 4 bits.

+ SUMADOR DE 4 BITS
```verilog
module sumador4bits ( 
    input [3:0] A, 
    input [3:0] B, 
    input Ci, 
    output [3:0] S, 
    output Co 
);

wire c1;
wire c2;
wire c3;

// Bit 0
sumador A0 (
    .A(A[0]),
    .B(B[0]),
    .Ci(Ci),
    .So(S[0]),
    .Co(c1)
);

// Bit 1
sumador A1 (
    .A(A[1]),
    .B(B[1]),
    .Ci(c1),
    .So(S[1]),
    .Co(c2)
);

// Bit 2
sumador A2 (
    .A(A[2]),
    .B(B[2]),
    .Ci(c2),
    .So(S[2]),
    .Co(c3)
);

// Bit 3
sumador A3 (
    .A(A[3]),
    .B(B[3]),
    .Ci(c3),
    .So(S[3]),
    .Co(Co)
);

endmodule
```
+ Lo más importante de este módulo

Implementa un sumador de 4 bits
Usa 4 sumadores de 1 bit en cascada
El acarreo se propaga:
Ci → c1 → c2 → c3 → Co
Este módulo permite trabajar con números completos de 4 bits, no solo bit a bit.
+ SUMADOR / RESTADOR DE 4 bITS
  
```verilog
module sumador_restador4bits(
    input [3:0] A,
    input [3:0] B,
    input Sel,
    output [3:0] S,
    output Co
);

wire [3:0] Bxor;

// Inversión de B controlada
xor (Bxor[0], B[0], Sel);
xor (Bxor[1], B[1], Sel);
xor (Bxor[2], B[2], Sel);
xor (Bxor[3], B[3], Sel);

// Instancia del sumador
sumador4bits UUT (
    .A(A),
    .B(Bxor),
    .Ci(Sel),
    .S(S),
    .Co(Co)
);

endmodule
```
+ Lo más importante de este módulo

Es el módulo principal del laboratorio
Permite:
Sel = 0 → Suma (A + B)
Sel = 1 → Resta (A - B)
+ Claves del funcionamiento:
Compuertas XOR
Si Sel = 1 → invierten B
Si Sel = 0 → B pasa normal
Acarreo inicial (Ci = Sel)
Cuando Sel = 1, se suma el +1 del complemento a 2
Reutilización del hardware
No se necesita un restador → solo un sumador

# Conclusiones 
En este laboratorio se logró diseñar e implementar correctamente un sumador/restador de 4 bits utilizando lenguaje HDL (Verilog), aplicando el concepto de complemento a 2 para realizar operaciones de resta a partir de un circuito sumador.
Inicialmente, se construyó un sumador de 1 bit, el cual constituye la base del sistema, ya que define la lógica fundamental de suma mediante compuertas básicas (XOR, AND y OR). Posteriormente, este módulo fue escalado a un sumador de 4 bits, conectando en cascada cuatro sumadores individuales y permitiendo así trabajar con números binarios de mayor tamaño, propagando correctamente el acarreo entre cada etapa.
Finalmente, se implementó el módulo principal de sumador/restador, donde se evidencia el aspecto más importante del laboratorio: la reutilización del hardware. Mediante el uso de compuertas XOR controladas por la señal Sel y el manejo del acarreo de entrada, se logró transformar la operación de resta en una suma equivalente, sin necesidad de diseñar un circuito restador independiente. Esto demuestra cómo el complemento a 2 simplifica el diseño de sistemas digitales.
Además, el desarrollo incluyó la simulación en Visual Studio Code con Icarus Verilog, lo que permitió validar el comportamiento del sistema tanto en suma como en resta, identificar errores y asegurar su correcto funcionamiento antes de la implementación física. Posteriormente, el diseño fue llevado a Quartus, donde se comprobó su funcionamiento en hardware utilizando los periféricos de la tarjeta.

#### 1.2 Diagramas
<img width="1600" height="918" alt="image" src="https://github.com/user-attachments/assets/0d2e2e12-5f4e-40ae-adf5-41621f5eacff" />

## Simulaciones 
<img width="1596" height="887" alt="image" src="https://github.com/user-attachments/assets/c9d85433-c8eb-49a7-ab3c-d854cca4cfec" />
<img width="1600" height="833" alt="image" src="https://github.com/user-attachments/assets/2a9a1a10-603a-4c6f-885d-97577a0e4025" />
