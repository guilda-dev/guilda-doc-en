<img src="../../../Figures/make_controller.jpg" width=100%;>

# <div style="text-align: center;"><span style="font-size: 140%; color: black; font-weight: bold">New Controller</span></div>

This section explains how to define a new controller model in GUILDA. The methods and properties that should be implemented are summarized in this section.

Creating a new controller model in GUILDA means creating an "m-file" that defines a new child class from the controller class. This page explains how to define this child class file.

**Contents:**

- [The Controller Class.](#the_controller_class)
- [Define a New Controller.](#define_a_new_controller)
- [Abstract Methods.](#abstract_methods)
    - [Required Arguments](#required_arguments)
    - [Required Abstract Methods.](#required_abstract_methods)
    - [Recommended Abstract Methods.](#recommended_abstract_methods)

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">The Controller Class</span></div>

The controller class defines several functions that should be provided in a controller model for analysis. By inheriting the properties from the already defined controller class in GUILDA, newly created controllers will automatically have these functions.

However, even though several functions are inherited from the controller class, still other functions are required to define the controller's algorithm. Such functions are added to the component class as abstract methods.

Fore more information on the source code of the controller class, please refer to [Source Code Explanation](../../SourceCode/0TopPage.md).

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Define a New Controller</span></div>

In this page, a new controller named "myController.m" will be created and used as an example of the process of defining a new controller.

*Note: When creating a new controller, it is recommended that a description and how to execute the newly define component is included as a comment at the beginning.*

The general process to create a new controller class is

1. Inherit the functions from the already defined controller class. To do this use the code myController < controller.

2. Define the required parameters of the new controller as variables of properties.

*Note: When defining the required parameters of the new component, it is recommended to name it as "parameter".*

3. Define the required functions as methods that define the control algorithm of the new controller.

The following code block shows the general structure of these 3 requirements as sections.

**myController.m**

```matlab
%Definition of new controller class as a child class.
classdef myController < controller

%Description of the controller to be implemented.
%Explanation of state variables, inputs, and outputs.
%Description on how to execute the constructor of this class and set its arguments.

    properties
        %Define the necessary parameters here.
    end

    methods
        %Define the methods here.
        function obj = myController(arg1,arg2,...)
            %Write the code.
        end

        %Required method: Controller State Derivative.
        function [dx, u] = get_dx_u(obj, x, X, V, I, U_global)
            %Write the code.
        end

        %Required method: Controller State Order.
        function nx = get_nx(obj)
            %Write the code.
        end


        %Recommended method: Approximate Linearization.
        function [A, BX, BV, BI, Bu, C, DX, DV, DI, Du] = get_linear_matrix(obj)
            %Write the code.
        end

        %Recommended method: Linear Controller State Derivation.
        function [dx, u] = get_dx_u_linear(obj, t, x, X, V, U_global)
            %Write code here.
        end
    end
end
```

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Abstract Methods</span></div>

The abstract methods are the set of required functions to define the behaviour of the controller. In this section the two required arguments, one required method, along with three recommended methods to be included are explained.

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Required Arguments</span></div>

- `idx_input`: Busbar number to which the output signal of the controller (i.e., the input signal to the system) are applied.

- `idx_observe`: Busbar number observed by the controller.

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Required Abstract Methods</span></div>

**Controller State Derivative**

This method is used to obtain the derivative of the controller state $\small (x)$; and the output signal $(\small u)$ (i.e., the input signal to the system).

Class Structure: `[dx, u] = get_dx_u(obj, x, X, V, I, U_global)`

*Note: The following notation is different to the ones used in other classes, e.g., Component class.*

Input Arguments

- `x`：Controller state $\small (x)$.

- `X`：State of the Device connected to each bus $\small (X)$.

- `V`：Busbar Voltage $\small (V)$ ([real part; imaginarty part]).

- `I`：Busbar Current $\small (I)$ ([real part; imaginarty part]).

- `U_global`： Input signals applied to each bus by the global controller $\small (U)$.

The controller model can only observe the above parameters. Therefore, even if there are other physical quantities required, they will have to be calculated on their own from the values of the arguments.

Output Parameters

- `dx`：Dertivative of the controller state $\small (\dot{x})$.

- `u`：Output signal of the controller (i.e., input signal to the system) $\small (u)$.

*Note:*

- *The controller class is commonly inherited by the global controller, retrofit controller, and other controllers. Therefore, when creating a distributed controller, it is necessary to explicitly write which signals may be referenced and which signals may not be referenced, in the derived class.*

- *It is not possible to reference the state of other controllers. If communication between controllers is required, they must be implemented as a single controller.*

**Controller State Order**

This method is used to obtain the order (dimension) of the controller state.

Class Structure:`nx = get_nx(obj)`

Input Arguments

- *None*

Output Parameters

- `nx`: Oder of the controller state.

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Recommended Abstract Methods</span></div>

The newly defined controller can be implemented without the following methods; however, these are necessary for performing analysis with an approximate linearization of the power system model.

**Approximate Linearization**

This method, as the name states, allows to linearize the power system model. It returns the system matrices $\small [A, B, C, D, BV, DV, BI, DI, R, S]$ of the linearized state-space representation as in the following equation.

 $$
    \begin{align*}
    \dot{x} &= A(x-x^*)+B(u-u^*)+B_V(V-V^*)+B_I(I-I^*)＋Rd\\
    0 &= C(x-x^*)+D(u-u^*)+D_V(V-V^*)+D_I(I-I^*)\\
    z &= S(x^*-x)
    \end{align*}
 $$

Class Structure: `[A, B, C, D, BV, DV, BI, DI, R, S] = get_linear_matrix(obj,x_st, Vst)`

Input Arguments

- `x_st`：Equilibrium point of the state variable.

- `Vst`：Equilibrium point of the voltage $-$ *obtained from the power flow calculation.*

Output Arguments

- `[A, B, C, D, BV, DV, BI, DI, R, S]`：System matrix of the state-space model shown above.
    - `R`: Disturbance matrix.
    - `S`: Output of the evaluation function.

*Note: Both `R` and `S` are needed when designing a control system; however, they are different from the properties of the device. Therefore, when simply performing a simulation without controller, it is safe to use `R` and `S` as zero matrices.*

**Linear Controller State Derivation**

This method is used to obtain the derivative of the state $\small (\dot{x})$ of the linearized controller, as well as its input $\small (u)$.

Class Structure: `[dx, u] = get_dx_u_linear(obj, t, x, X, V, U_global)`

Input Arguments

- `t`: Time $\small (t)$.

- `x`：Controller state $\small (x)$.

- `X`：State of the Device connected to each bus $\small (X)$.

- `V`：Busbar Voltage $\small (V)$ ([real part; imaginarty part]).

- `I`：Busbar Current $\small (I)$ ([real part; imaginarty part]).

- `U_global`： Input signals applied to each bus by the global controller $\small (U)$.

The controller model can only observe the above parameters. Therefore, even if there are other physical quantities required, they will have to be calculated on their own from the values of the arguments.

Output Parameters

- `dx`：Dertivative of the controller state $\small (\dot{x})$.

- `u`：Output signal of the controller (i.e., input signal to the system) $\small (u)$.
