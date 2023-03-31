<img src="../../../Figures/make_power_net.jpg" width=100%;>

# <div style="text-align: center;"><span style="font-size: 140%; color: Black; font-weight: bold">New Power System Model</span></div>

This page provides a detailed explanation on how to define a power system model. For a simpler explanation please refer to [Power System Models](../../SeriesAnalysis/0TopPage.md).

In this section it is explained how to create a new 3-bus power system model and define it as the `power_network` class. This explanation uses the already defined components for Generators (`generator_1axis`) and for Loads (`load_impedance`).

**Contents:**

- [Network Definition.](#network_definition)
- [Branch Definition.](#branch_definition)
- [Network Parameters.](#network_parameters)
- [Busbar Definition.](#busbar_definition)
    - [Slack Bus.](#slack_bus)
    - [PV Bus.](#pv_bus)
    - [PQ Bus.](#pq-bus)
- [Component Definition.](#component_definition)
    - [Generator.](#generator_definition)
    - [Load.](#load_definition)
- [Complete Code.](#complete_code)

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Power System Model Definition</span></div>

To define a network, the `power_network` class must be assigned to a variable. For this example a variable named `net` is used. The class is assigned to the variable in the following manner

```matlab
net = power_network(); %Class assigned to the variable; however, no network is yet defined.
```

The `power_network` class has now been assigned to the variable `net` and is ready to be used. Note that at this point no component has been assigned, thus the network is still empty.

In the following sections various elements will be added to the power network (`net`). Such elements are branches, busbars, and components (generators and loads).

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Branch Definition</span></div>

Branches in GUILDA represent power transmission lines between busbars. The information of these transmission lines is stored as a `table`. The parameters of interest are

- `bus_from`, `bus_to`: Busbar number that the transmission line connects from and to, respectively.
- `x_real`, `x_imag`:   Real and imaginary part of the transmission line's impedance $\small (Z)$, respectively. The reciprocal of this value is the admittance $\small (Y)$.
- `y`: Ground capacitance.
- `tap`, `phase`: Phase Adjustment Transformer Parameters.

For example, in the 3-bus model, there are two transmission lines: one connecting "busbar 1 and busbar 2" and the other connecting "busbar 2 and busbar 3"; its table is the following

```matlab

  9×7 table

    bus_from    bus_to    x_real    x_imag      y       tap    phase
    ________    ______    ______    ______    ______    ___    _____

       1          2            0    0.0576         0     1       0
       3          2            0    0.0625         0     1       0
```



### **Sample code for defining a branch**

In the following section, values are obtained for each row from the table type data defining the branch information, and stored in a class variable named a_branch in the power_network class defined earlier.

```matlab
%Inser the branch table information in the following array.
branch_array = [Inser the table information here];

%Add variable names to convert the `branch_array` to a `table` type variable.
branch = array2table(branch_array, 'VariableNames', ...
    {'bus_from', 'bus_to', 'x_real', 'x_imag', 'y', 'tap', 'phase'}...
    );

%Define Branches.
for i = 1:size(branch, 1)
    %Define PI Branches
    if branch{i, 'tap'} == 0
        br = branch_pi(branch{i, 'bus_from'}, branch{i, 'bus_to'},...
            branch{i, {'x_real', 'x_imag'}}, branch{i, 'y'});
    %Define PI-Transformer Branches
    else
        br = branch_pi_transformer(branch{i, 'bus_from'}, branch{i, 'bus_to'},...
            branch{i, {'x_real', 'x_imag'}}, branch{i, 'y'},...
            branch{i, 'tap'}, branch{i, 'phase'});
    end
    %Add the defined Branches to the Network.
    net.add_branch(br);%This line assumes the network's variable name "net".
end
```

Note that the code above is for the case where the `power_network` class is assigned to a variable named `net`. Thus, in case another variable name is used (for example, `netbus3`), change the second line from the end to

```matlab
net3bus.ad_branch(br) %This line assumes the network's variable name "netbus3".
```

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Network Parameters</span></div>

In GUILDA, the busbars have six possible parameters.


- Active Power $(P)$.
- Reactive Power $(Q)$.
- Voltage Magnitude $(\lvert V \rvert)$.
- Voltage Phase Angle $(\angle V)$.
- Shunt Resistor Admittance [Shunt Resistor Conductance, Shunt Resistor Susceptance] $(Y_{\mathrm{shunt}} = [G_{\mathrm{shunt}}, B_{\mathrm{shunt}}])$.

For more information on it, please refer to [Power System Models](../../aboutPowerSystem/0TopPage.md).

The busbars, depending on its type, always specify the shunt resistor admittance $(Y_{\mathrm{shunt}})$, and two of the rest of parameters $($i.e., $P, Q, \lvert V \rvert, \angle V)$.

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Busbar Definition</span></div>

In GUILDA, the busbars are classified in three groups: PV busbar, PQ busbar, and Slack busbar. For each busbar type a class is defined (`bus_slack`, `bus_PV`, `bus_PQ`). Thus, to use it, it is necessary to define an instance of the corresponding class and store it as a cell array (`a_bus`) in the created power network (`net`).

```matlab
%Slack bus
b = bus_slack(...)；

%PV bus
b = bus_PV(...);

%PQ bus
b = bus_PQ(...);
```

In the previous code block the buses' arguments have been omitted (written as `...`). To properly run the code those must be included. The explanation of each argument can be found in the following sections.

### **Slack Bus**

All Generator Buses in a power network are basically classified as PV buses, but there is one special bus that specifies the voltage phase angle $\small (\angle V)$ and serves as a reference. This is the Slack Bus.

Thus, the Slack Bus is specified by voltage magnitude $\small (\lvert V \rvert)$ and voltage phase angle $\small (\angle V)$. Therefore, the slack bus takes those parameters, as well as the shunt admittance, as arguments (`V_abs`, `V_angle`, `[G_shunt, B_shunt]`) in the following manner.

```matlab
%Define a Slack Bus named "b"
b = bus_slack(V_abs, V_angle, [G_shunt, B_shunt]);
```
Input Arguments:

  - `V_abs`: Voltage Magnitude $\small (\lvert V \rvert)$.
  - `V_angle`: Voltage Phase Angle $\small (\angle V)$ $-$ *Normally 0.*
  - `[G_shunt, B_shunt]`: Real and imaginary part of the impedance of a shunt resistor connected to the ground $\small ([G_{\mathrm{shunt}}, B_{\mathrm{shunt}}])$.

### **PV Bus**

As mentioned before, all Generator Buses are classified as PV buses, except for the Slack Bus.

As the name implies, this bus type is specified by the active power $\small (P)$ and the voltage magnitude $\small (\lvert V \rvert)$, as well as the shunt admittance, and taking them as arguments (`P_gen`, `V_abs`, `[G_shunt, B_shunt]`) in the following manner.

```matlab
%Define a PV Bus named "b"
b = bus_PV(P_gen, V_abs, [G_shunt, B_shunt]);
```

Input Arguments:

  - `P_gen`: Active Power $\small (P)$.
  - `V_abs`: Voltage Magnitude $\small (\lvert V \rvert)$
  - `[G_shunt, B_shunt]`: Real and imaginary part of the impedance of a shunt resistor connected to the ground $\small ([G_{\mathrm{shunt}}, B_{\mathrm{shunt}}])$.

### **PQ Bus**

The PQ bus type is selected when loads are connected to the busbar or when nothing is connected to it (i.e., non-unit busbar). This bus type is specified by the active power $\small (P)$, reactive power $\small (Q)$, as well as the shunt admittance, and taking them as arguments (`-Pload`, `-Qload`, `[G_shunt, B_shunt]`).

```matlab
%Define a PQ Bus named "b"
b = bus_PQ(-Pload, -Qload, [G_shunt, B_shunt]);
```

Input Arguments:

  - `-Pload`: Negative of Active Power $\small (P)$.
  - `-Qload`: Negative of Reactive Power $\small (Q)$.
  - `[G_shunt, B_shunt]`: Real and imaginary part of the impedance of a shunt resistor connected to the ground $\small ([G_{\mathrm{shunt}}, B_{\mathrm{shunt}}])$.

This completes the process up to creating instances of the busbar class. 

Note: Before storing the defined busbars (`b`), in the defined power network (`net`); first, define the components (i.e., generators and loads) that are connected to each busbar. For it please refer to the following section.

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Component Definition</span></div>


The process to define a component is

1. Create a variable (e.g., `component`) and make it an instance of one of the component classes (i.e., `generator_1axis`, `load_impedance`).
2. Assign the newly created component variable (`component`) to the bus to which it is connected (e.g., `b`), by ussing the function `set_component`.

### **Generator Definition**

To define a synchronous generator (1-axis model), the function `generator_1axis` is used.

To define a new variable with the `generator_1axis` class, please use the following code

```matlab
component = generator_1axis(omega0, mac);
```

Input Arguments:

- `omega0`: Grid's frequency $\small (\omega_0)$.
- `mac`: Parameters of the Generator (Table with 8 fields)
    - `No_machine`: Generator number.
    - `No_bus`: Busbar to which the the generator is connected.
    - `Xd`: Synchronous Reactance around the d-axis $\small (X_d)$.
    - `Xd_prime`: Transient Synchronous Reactance around the d-axis $\small (X'_d)$.
    - `Xq`: Synchronous Reactance around the q-axis $\small (X_q)$.
    - `T`: Time constant of the field current aroudn the d-axis $\small (\tau_d)$.
    - `M`: Inertia Coefficient $\small (M)$.
    - `D`: Damping Factor $\small (D)$.

For example, defining the table (`mac`) for a synchronous generator with arbitrary parameters is like the following

```matlab
mac =
1×8 table

  No_machine     No_bus       Xd      Xd_prime      Xq      T      M     D
 ____________  _________  _________  __________  _______  ______  ___   ___

      1            3        1.569      0.324      1.548    5.14   100    2

```

This is an example of a 1-axis synchronous generator with arbitrary parameters. However, it is common for real generators to have controllers, like Automatic Voltage Regulator (AVR) or Power System Stabilizer (PSS), to make their response favorable. Thus, it is possible to add AVR and PSS information to the created generator instance as follows

```matlab
component.set_avr(avr_sadamoto2019(exc)); %Adds AVR to the created generator instance named "component".
component.set_pss(pss(p)) %Adds PSS to the created generator instance named "component".
```

- Automatic Voltage Regulator (AVR): In GUILDA, the AVR model is under the class `avr_sadamoto2019(exc)`.

    - Argument `exc`: Table that includes the required AVR parameters.

```matlab
exc  =
1×3 table

 No_bus     Ka      Te
________  _____   ______

    1        2      0.05
```

- Power System Stabilizer (PSS): In GUILDA, the PSS model is under the class `pss(p)`.

    - Argument `p`: Table that includes the required PSS parameters.

```matlab
p =
1×6 table

 No_bus    Kpss   Tpss   TL1p    TL1    TL2p   TL2
_________  ____   ____   ____   _____   ____   ___

    1        0     10    0.05   0.015   0.08   0.01
```

### **Load Definition**

To define a load, the function `load_impedance` is used.

To define a new variable with the `load_impedance` class, please use the following code.

```matlab
component = load_impedance();
```

As you can see from the code, when connecting loads, the bus parameters that have already been defined are used, so there are no new parameters that need to be defined.

### **Add Components to a Busbar**

To add a created component (e.g., `component`) to an already defined busbar (e.g., `b`), use the method `set_component`. Finally, add the busbar with the assigned component to the power network (e.g., `net`) with the method `add_bus`. The following is an example using the variable names of this paragraph.

```matlab
%Adds the generator or load named "component" to the busbar named "b"
b.set_component(component);
%Adds the busbar named "b" to the power network named "net"
net.add_bus(b);
```

*Add a component to a bus that already is in the power network*: As mentioned before, it is recommended to add the busbars to the power network along with its components. However, in case a component is to be added to a busbar that is already assigned to the power network use the following code.

```matlab
%Add the busbar named "b" to the power network named "net".
net.add_bus(b);
%Add the component named "component" to the "i-th" busbar added to the power network named "net".
net.a_bus{i}.set_component(component);
```
The method `add_bus` (used when adding a busbar to the power network) stores the specified busbar in the variable `a_bus`. The key point to note is that the busbars are defined in the order in which they are assigned to the field `a_bus`. In other words, the first busbar class that is `net.add_bus(b)` is treated as "bus 1".

Therefore, in the code line

```matlab
net.a_bus{i}.set_component(component);
```
the `i` corresponds to the `i-th` busbar that was added to the power network by using the method `add_bus`. Therefore, if this procedure is to be used, it is important to keep a record of the assignment order of the busbars to the power network. This method is useful for situtation like modifying the equipment model (i.e., component) of an existing busbar.

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Complete Code</span></div>

This completes the definition of the power system.
The code up to this point is summarized at the end of this section.

In this case, a 3 busbar power network  is implemented as an example. However, any network geometry can be implemented.

```matlab
%Function: define a 3-bus network
function net = network_3bus_1axis()

%Definition of gird frequency
omega0 = 60*2*pi;

%Define an empty network named "net".
net = power_network();

%Define the parameters of the 3 buses to add.
bus_array = [1 1.00 0 0.60 0 0 0 0 0 2;
             2 1.00 0 5.45 0 1.0 1.0 0 0 3;
             3 1.00 0 1.00 2 0 0 0 0 1];

%Add variable names to the buses' parameters.
bus = array2table(bus_array, 'VariableNames', ...
    {'No', 'V_abs', 'V_angle', 'P_gen', 'Q_gen', 'P_load', 'Q_load', 'G_shunt', 'B_shunt', 'type'} ...
    );

%Define the parameters of the 2 branches to add.
branch_array = [...
            1 2	0	1/12.56041	0	1	0;...
            3 2 0	1/13.65107	0	1	0;];

%Add variable names to the branches' parameters.      
branch = array2table(branch_array, 'VariableNames', ...
                {'bus_from', 'bus_to', 'x_real', 'x_imag', 'y', 'tap', 'phase'}...
                );

%Define the parameters of the 2 generators to add.
mac_array = [1, 1, 0.1,    0.031,  0.069, 10.2, 84,   4;
                   2, 3, 0.295,  0.0697, 0.282, 6.56, 60.4, 9.75];

%Add variable names to the generators' parameters.
mac_data = array2table(mac_array, 'VariableNames', ...
            {'No_machine', 'No_bus', 'Xd', 'Xd_prime', 'Xq', 'T', 'M', 'D'} ...
            );

%Define Automatic Voltage Regulator (AVR Controller) parameters.
exc_array = [1 0 0.05;
                    3 0 0.05];

%Add variable names to the AVR Controller's parameters.   
exc_data = array2table(exc_array, 'VariableNames', ...
            {'No_bus', 'Ka', 'Te'} ...
            );

%Define Power System Stabilizer (PSS Controller) parameters.
pss_array = [1 0 10 0.05 0.015 0.08 0.01;
             3 0 10 0.05 0.015 0.08 0.01];

%Add variable names to the PSS Controller's parameters.
pss_data = array2table(pss_array, 'VariableNames', ...
            {'No_bus', 'Kpss', 'Tpss', 'TL1p', 'TL1', 'TL2p', 'TL2'} ...
            );

%Define Buses.
for i = 1:size(bus, 1)
    shunt = bus{i, {'G_shunt', 'B_shunt'}};
    switch bus{i, 'type'}
        %Define Slack Bus.
        case 1
            V_abs = bus{i, 'V_abs'};
            V_angle = bus{i, 'V_angle'};
            b = bus_slack(V_abs, V_angle, shunt);
            b.set_component(get_generator(i, machinery, excitation, pss_data, omega0));

        %Define PV Bus.
        case 2
            V_abs = bus{i, 'V_abs'};
            P = bus{i, 'P_gen'};
            b = bus_PV(P, V_abs, shunt);
            b.set_component(get_generator(i, machinery, excitation, pss_data, omega0));
        
        %Define PQ Bus.
        case 3
            P = bus{i, 'P_load'};
            Q = bus{i, 'Q_load'};
            b = bus_PQ(-P, -Q, shunt);
            if P~=0 || Q~=0
                load = load_impedance();
                b.set_component(load);
            end

    end
    %Add buses to the power network.
    net.add_bus(b);
end

%Define Branches.
for i = 1:size(branch, 1)
    %Define PI Branches.
    if branch{i, 'tap'} == 0
        br = branch_pi(branch{i, 'bus_from'}, branch{i, 'bus_to'},...
            branch{i, {'x_real', 'x_imag'}}, branch{i, 'y'});
    %Define PI-Transformer Branches.
    else
        br = branch_pi_transformer(branch{i, 'bus_from'}, branch{i, 'bus_to'},...
            branch{i, {'x_real', 'x_imag'}}, branch{i, 'y'},...
            branch{i, 'tap'}, branch{i, 'phase'});
    end
    %Add branches to the power network.
    net.add_branch(br);
end
%Finilize: Calculate the power flow, the equilibrium point of the entire system, and store each value.
net.initialize();
end

%Function to obtain the generator information and define it.
function g = get_generator(i, mac_data, exc_data, pss_data, omega0)
idx = mac_data{:, 'No_bus'} == i;
if sum(idx) ~= 0
    g = generator_1axis(omega0, mac_data(idx, :));
    exc = exc_data(exc_data{:, 'No_bus'}==i, :);
    g.set_avr(avr_sadamoto2019(exc));
    p = pss_data(pss_data{:, 'No_bus'}==i, :);
    g.set_pss(pss(p));
end
end
```

This completes the definition of the power network.

To learn more about

- Calculation of the equations of state, please refer to [Derivation of a Linearized Model](../Analysis/net_getsys.md).

- More details on the process of simulation, please refer to [Simulating](../Analysis/net_simulate.md).

- Controller design, please refer to [Adding a Controller](../addController/0TopPage.md).
