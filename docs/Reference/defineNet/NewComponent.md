<img src="../../../Figures/make_component.jpg" width=100%;>

# <div style="text-align: center;"><span style="font-size: 140%; color: Black; font-weight: bold">New Components</span></div>


This page provides a detailed explanation on how to define a component class. 

Creating a new equipment model in GUILDA means creating an "m-file" that defines a new child class from the component class. This page explains how to define this child class file.

**Contents**:

- [The Component Class.](#the_component_class)
- [Define a New Component.](#define_a_new_component)
- [Abstract Methods.](#abstract_methods)
    - [Required Abstract Methods](#required_abstract_methods)
    - [Recommended Abstract Methods.](#recommended_abstract_methods)

---

## <div style="text-align: center;"><span style="font-size: 120%; color: Black; font-weight: bold">The Component Class</span></div>

The `component` class defines several functions that should be provided in a device model (e.g., a synchronous generator) for analysis. By inheriting the properties from the already defined `component` class, newly created equipment models will automatically have these functions. 

However, even though several functions are inherited from the `component` class, still other functions are required to define the dynamic characteristics of the new component. Such functions are added to the `component` class as abstract methods.

Fore more information on the source code of the `component` class, please refer to [Source Code Explanation](../../SourceCode/0TopPage.md).

---

## <div style="text-align: center;"><span style="font-size: 120%; color: Black; font-weight: bold">Define a New Component</span></div>

In this page, a new component named "myMachine.m" will be created and used as an example of the process of defining a new component.

*Note: When creating a new component, it is recommended that a description and how to execute the newly define component is included as a comment at the beginning.*

The general process to create a new component class is

1. Inherit the functions from the already defined `component` class. To do this use the code `myMachine < component`.

2.  Define the required parameters of the new component as variables of `properties`.

*Note: When defining the required parameters of the new component, it is recommended to name it as "parameter"*

3. Define the required functions as `methods` that define the dynamics characteristics of the new component.

The following code block shows the general structure of these 3 requirements as sections.

**myMachine.m**

```matlab
%Definition of new component class as a child class.
classdef myMachine < component

%Description of the model to be implemented.
%Explanation of state variables, inputs, and outputs.
%Description on how to execute the constructor of this class and set its arguments.


    properties
        %Define the necessary parameters here.
        x_equilibrium　%etc..
    end

    methods
        %Define the methods here
        function obj = myMachine(arg1,arg2,...)
            %Write the code.
        end

        %Required method: Set Equilibrium.
        function x = set_equilibrium(obj,Veq, Ieq)
            %Write the code.
        end

        %Required method: Input State Order.
        function nu = get_nu(obj)
            %Write the code.
        end

        %Required method: State Derivative.
        function [dx,constraint] = get_dx_constraint(obj,t,x,V,I,u)
            %Write the code.
        end

        %Recommended method: Approximate Linearization.
        function [A,B,C,D,BV,DV,BI,DI,R,S] = get_linear_matrix(obj,x_st,Vst)
            %Write the code.
        end

        %Recommended method: Linear State Derivation.
        function [dx,con] = get_dx_constraint_linear(obj,t,x,V,I,u)
            %Write the code.
        end

        %Recommended method: Name Tag.
        function name_tag = get_x_name(obj)
            %Write the code.
        end
    end
end
```

---

##　<div style="text-align: center;"><span style="font-size: 120%; color: Black; font-weight: bold">Abstract Methods</span></div>

The abstract methods are the set of required functions to define the dynamics of a component. In this section three required methods, along with three recommended methods to be included are explained.

### <div style="text-align: left;"><span style="font-size: 100%; color: Black; font-weight: bold">Required Abstract Methods</span></div>

**Set Equilibrium**

This method performs the initialization processing, determines the equilibrium points via power flow calculation (equilibrium voltage $\small (V_{eq})$ and equilibrium current $\small (I_{eq})$), and derives the equilibrium state.

Class Structure: `x = set_equilibrium(obj,Veq,Ieq)`

Input Arguments
  
  - `Veq`: Equilibrium point of the voltage (complex number) $-$ *obtained from the power flow calculation.*
  
  - `Ieq`: Equilibrium point of the current (complex number) $-$ *obtained from the power flow calculation.*

Output Parameters
  
  - `x`：Equilibrium state corresponding to the specified equilibrium point.


**Input State Order**

This method is used to determine the order of the input state (`u`).

Class Structure `nu = get_nu(obj)`

Input Arguments

  - *None*

Output Parameters

  - `nu`：Order of input state (`u`).

**State Derivative**

This method is used to obtain the derivative of the state $\small (x)$; when an input is applied it also provides the current $\small (I)$ to obtain the constraints (see below).

Class Structure: `[dx,constraint] = get_dx_constraint(obj,t, x, V, I, u)`

Input Arguments

- `t`: Time $\small (t)$.
- `x`: State $\small (x)$.
- `V`: Busbar Voltage $\small (V)$ ([real part; imaginary part]).
- `I`: Busbar Current $\small (I)$ ([real part; imaginary part]).
- `u`: Input Signal $\small (u)$.

Output Variables
    
- `dx`：Derivative of the State $\small (\dot{x})$.
    
- `constraint`：The difference between the actual busbar's current $\small (I)$ and the derived one from the provided arguments "state" $\small (x)$ and "voltage" $\small (V)$. The constraint condition is that the difference between both currents $\small (I)$ should be zero.

### <div style="text-align: left;"><span style="font-size: 100%; color: Black; font-weight: bold">Recommended Abstract Methods</span></div>

The newly defined component can be implemented without the following methods; however, these are necessary for performing analysis with an approximate linearization of the power system model.

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

**Linear State Derivation**

This method is the linearized version of the "State Derivate" (`get_dx_constraint`) method. 

*Note: To implement this method a linearized state-space model is needed. For it the method "Approximate Linearization" (`get_linear_matrix`) is available.*


Class Structure: `[dx, con] = get_dx_constraint_linear(obj,t, x, V, I, u)`.


Input Arguments

- `t`: Time $\small (t)$.
- `x`: State $\small (x)$.
- `V`: Busbar Voltage $\small (V)$ ([real part; imaginary part]).
- `I`: Busbar Current $\small (I)$ ([real part; imaginary part]).
- `u`: Input Signal $\small (u)$.

Output arguments
    
- `dx`：Derivative of the State $\small (\dot{x})$.
    
- `con`：The difference between the actual busbar's current $\small (I)$ and the derived one from the provided arguments "state" $\small (x)$ and "voltage" $\small (V)$. The constraint condition is that the difference between both currents $\small (I)$ should be zero.

**Name Tag**

This method allows to assign names to each entry of the state vector $\small (x)$ of the component.

Class Structure: `name_tag = get_x_name(obj)`

Input Arguments

- *None*
    
Output Parameters

- Cell array of strings indicating each state name.

Example: `name_tag = {'x1','x2','x3'};`

```matlab
>> out = net.simulate(t,varargin,'tools',true);
```

This is useful for automatically classifying and plotting the states based on the given names, instead of just having "state1, state2, ...".

For more information on how to use this function, please refer to [Simulating](../Analysis/net_simulate.md), as it is mainly presented and used there.

This concludes the explanation of defining new components.



 

