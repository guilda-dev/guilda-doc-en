# <div style="text-align: center;"><span style="font-size: 140%; color: black; font-weight: bold">Controller Class</span></div>

This page explains the variables and functions used in the controller class (`controller`).

**Contents:**

- [Controller Class](#controller_class_1)
- [Automatic Generation Control (AGC)](#automatic_generation_control_agc)

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Controller Class</span></div>

In GUILDA, the controllers are classified into two categories: local controllers and global controllers.

- **Local Controller:** A controller that is added locally to each device. Example: Retrofit controller.

- **Global Controller:** A controller for multiple devices. Example: Automatic Generation Control (AGC).

Both of these controllers are defined as objects of the controller class. The difference is that the local controller includes the input value of the global controller as an argument. This can be seen in the code, for example with the method "Controller State Derivative" (`get_dx_u`), which is defined in the controller class, but it is called differently, depending on the controller type, as follows.

- Controller State Derivative - Local Controller
  ```matlab
  [dx, u] = get_dx_u(obj, t, x, X, V, I, u_global)
  ```
- Controller State Derivative - Global Controller
  ```matlab
  [dx, u] = get_dx_u(obj, t, x, X, V, I, [])
  ```


### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Variables</span></div>

- `idx_input`: Busbar number to which the output signal of the controller (i.e., the input signal to the system) are applied.

- `idx_observe`: Busbar number observed by the controller.

- `idx`：`idx_input` and `idx_observe` as aligned vectors.

- `get_dx_u_func`: Anonymous functio that must be specified for each instance, for example `get_dx_u` for the predefined controllers in GUILDA.

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Required Methods</span></div>

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


### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Recommended Methods</span></div>

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

This method is used to obtain the derivative of the state $\small (\dot{x})$ of the linearized controller, as well as its input $\small (u)$, satisfying the following equation

 $$
    \dot{x}=Ax+B_X (X-X^*)+B_V (V-V^*)+B_I (I-I^*)+B_u U_{global}\\
    u=Cx+D_X (X-X^*)+D_V (V-V^*)+D_I (I-I^*)+D_u U_{global}
 $$

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

- `A`：A matrix of controllers.

- `BX`：B matrix of the controller (regarding the stacked vector of bus states to be observed).

- `BV`：B matrix of the controller (regarding the stacked vector of voltages on all buses).

- `BI`：B matrix of the controller (regarding the stacked vector of currents on all buses).

- `Bu`：B matrix of the controller (regarding the stacked vector of input signals to all buses generated by the global controller).

- `C`：C matrix of controller.

- `DX`：D matrix of the controller (with respect to the bus state vector to be observed).

- `DV`：D matrix of the controller (regarding the stacked vector of voltages on all buses).

- `DI`：D matrix of the controller (regarding the stacked vector of currents on all buses).

- `Du`：D matrix of the controller (regarding the stacked vector of input signals to all buses generated by the global controller).

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Abstract Methods</span></div>

**Linear Controller State Derivation (`[dx, u] = get_dx_u_linear(obj, t, x, X, V, U_global)`)**

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


---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Automatic Generation Control (AGC)</span></div>

This child class implements a Broadcast Controller (PI Controller) class (i.e., `controller_broadcast_PI_AGC`), which assumes that a 1-Axis Synchronous Generator Model (i.e., `generator_1axis`) is used.

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Variables</span></div>

- `idx_observe`: Busbar number observed by the controller.

- `idx_input`: Busbar number to which the output signal of the controller (i.e., the input signal to the system) are applied.

- `net`: Instance of the power network (i.e., the power system model) to which the controller will be added.

- `Kp`: Proportional gain of the controller.

- `Ki`: Integral gain of the controller.

- `K_broadcast`: Proportional gain for each generator (usually, all 1).

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Constructor Method</span></div>

**AGC Controller (`obj = controller_broadcast_PI_AGC(net, y_idx, u_idx, Kp, Ki)`)**

Input Argument

- `net`: Instance of the power network (i.e., the power system model) to which the controller will be added.

- `y_idx`: The number of the busbar whose output is to be observed.

- `u_idx`: Number of the busbar to which the input is applied.

- `Kp`: Proportional gain of the controller.

- `Ki`: Integral gain of the controller.