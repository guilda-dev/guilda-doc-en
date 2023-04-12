# <div style="text-align: center;"><span style="font-size: 140%; color: black; font-weight: bold">Simulation - Examples</span></div>

This page contains examples of handling the simulating function and examining its response under various settings.

**Contents:**

- [Example 1 - Initial Value Response.](#example_1_-_initial_value_response)
- [Example 2 - Disturbance Response.](#example_2_-_disturbance_response)
- [Example 3 - Input Response.](#example_3_-_input_response)
- [Example 4 - Combination Response.](#example_4_-_combination_response)
- [Simulation Results](#simulation_results)

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Example 1 - Initial Value Response</span></div>

**Scenario:**

- IEEE 68-bus model.

- Simulation time: 0 - 20 seconds.

- Simulation conditions: Initial value of frequency deviation $\small (\Delta \omega)$ (second state) of the generator connected to busbar 3 is deviated by 0.01 from the equilibrium point.

The second state of the generator connected to busbar 3 corresponds to the 16th state of the entire system. Remember that the state of the entire system is a vector composed of the states of all components (in the busbar ascending order). And since in the IEEE 68-bus model, the generators have 7 states each, the second state of the third generator is the 16th state of the entire system.

In cases where the number of internal states per components isn't known, the method `get_nx` can be used.

```matlab
%Network Definition
net = network_IEEE68bus();
%Define options for simulation
option 			= struct();
option.x0_sys   = net.x_equilibrium;
option.x0_sys(16)= option.x0_sys(16) + 0.01;
%Running the simulation
out = net.simulate([0 20], option);
```

Alternatively (without stating `option` as a structure).

```matlab
net = network_IEEE68bus();
x0    = net.x_equilibrium;
x0(16)= x0(16) + 0.01;
out = net.simulate([0 20],'x0_sys',x0);
```

Usage example of the method `get_nx` (obtain the number of internal state of a component).

```matlab
bus_idx   = 3; %Third busbar.
state_idx = 2; %Second state.

%Adds up the number of states from Busbar 1 to Busbar.
%Practically, skips the busbars previous to the one of interest.
(bus_idx-1).
idx = 0;
for i = 1:bus_idx-1
	idx = idx+net.a_bus{i}.component.get_nx;
end

%To know the second state of the third busbar.
%Practically, goes to the state of interest in the busbar of interest.
idx = idx+state_idx;

%In the case of the IEEE68bus model it is、idx = 7+7+2= 16.
option.x0_sys(idx)= option.x0sys(idx) + 0.1;
			：
```

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Example 2 - Disturbance Response</span></div>

**Scenario:**

- IEEE 68-bus model.

- Simulation time: 0 - 30 seconds.

- Simulation conditions: Ground fault on busbar 1 for 0-0.07 seconds and 15-15.05 seconds; on busbar 2, and busbars 5-7 for 10-10.01 seconds.

```matlab
%Network definition
net = network_IEEE68bus();
%Define options for simulation
option = struct();
option.fault = {{[0 0.07], 1}, {[15 15.05], 1}, {[10,10.01],[2,5:7]}};
%Running the simulation
out = net.simulate([0 30], option);
```

Alternatively (without stating option as a structure).

```matlab
net = network_IEEE68bus();
out = net.simulate([0 30],...
			'fault',{{[0 0.07], 1}, {[15 15.05], 1}, {[10,10.01],[2,5:7]}});
```

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Example 3 - Input Response</span></div>

**Scenario:**

- 3-bus model.

- Simulation time: 0 - 30 seconds.

- Simulation conditions: The impedance of the load connected to busbar 3 is increased.

This corresponds to the same scenario as the one explained in the [Guided Example](../../SeriesAnalysis/0TopPage.md).

```matlab
%Network definition
net = network_sample3bus();
%Condition Setting
time = [0,10,20,60];
u_idx = 3;
u = [0, 0.05, 0.1, 0.1;...
	 0,    0,   0,   0];
%Running the simulation
out = net.simulate(time,u, u_idx);
```

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Example 4 - Combination Response</span></div>

Combining all the previous conditions up to this point (i.e., initial value response, disturbance response, input response), the following scenario is presented.

**Scenario:**

- IEEE 68-bus model.

- Simulation time: 0 - 20 seconds.

- Simulation conditions:
  - Initial Value: The initial state of busbar 1 is slightly shifted (0.01) from the equilibrium point.
  - Disturbance: A ground fault is applied to bus 1 at 0-0.07 seconds and to bus 10 at 10-10.05 seconds.
  - Input: Random inputs are applied to buses 1 and 10.

```matlab
%Network definition
net = network_IEEE68bus();

%Define options for simulation
option 			= struct();
option.x0_sys   = net.x_equilibrium;
option.x0_sys(16)= option.x0_sys(16) + 0.01;

option.fault = {{[0 0.07], 1}, {[15 15.05], 1}, {[10,10.01],[2,5:7]}};

%Condition Setting
time = [0,10,20,60];
u_idx = 20;
u = [0, 0.05, 0.1, 0.1;...
	 0,    0,   0,   0];

%Running the simulation
out = net.simulate(time, u, u_idx, option);
```

Alternatively (without stating option as a structure).

```matlab
net = network_IEEE68bus();
x0    = net.x_equilibrium;
x0(16)= x0(16) + 0.01;
out = net.simulate([0 20],u,u_idx,...
				'x0_sys',x0,...
				'fault',{{[0 0.07], 1}, {[15 15.05], 1}, {[10,10.01],[2,5:7]}});
```

---

## <div style="text-align: center;"><span style="font-size: 120%; color: black; font-weight: bold">Simulation Results</span></div>

In the IEEE 68-bus model, the generators are connected to the busbars 1 to 16. The generator model used is the 1-axis model (i.e., `generator_1axis` class). Remember that for this model, the first three entries are

1. Rotor declination $\small (\delta)$.
2. Frequency deviation $\small (\Delta \omega)$
3. Internal voltage $\small (E)$.

Thus, to visualize the frequency deviation $\small (\Delta \omega)$ of each generator bus, all you have to do is to look at the 2nd state of each device. It can be easily plotted with the following code.

```matlab
figure;
hold on
arrayfun(@(idx) plot(out.t,out.X{idx}(:,2)),1:16);
xlabel('Time [s]','FontSize',15);
ylabel('Frequency Deviation','FontSize',15);
legend()
title('Frequency deviation of each synchronous generator','FontSize',20)
hold off
```

However, when there is more than one device with a state, as in this model, it is necessary to consider the state of each device. The `simulationResult` class described below automatically classifies and plots each device using its state variable name in the internal method.

---

### <div style="text-align: left;"><span style="font-size: 100%; color: black; font-weight: bold">Results Visualization</span></div>

As mentioned before, GUILDA implements a class called `simulationResult`, which is used to visualize the simulation results.

**Plot with command**

To use it, in the `simulate` function, set the option `tools` as `true`. For the case of example 4:

```matlab
net = network_IEEE68bus();
x0    = net.x_equilibrium;
x0(16)= x0(16) + 0.1;
out = net.simulate([0 20],u,u_idx,...
			'x0_sys',x0,...
			'fault',{{[0 0.07], 1}, {[15 15.05], 1}, {[10,10.01],[2,5:7]}},...
			'tools',true);
```

**Plot with User Interface (UI)**

To plot with the UI use:

```matlab
out.UIplot
```

The UI for plotting conditions will be displayed. Check the appropriate check boxes and press the "plot" button to display the plot. At this time, if you check the "command output" box, commands for plotting will be displayed.
