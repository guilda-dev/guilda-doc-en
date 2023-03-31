# <div style="text-align: center;"><span style="font-size: 130%; color: black;">GUILDA Python Documentation</span></div>

---

This is the official documentation of GUILDA.

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【Description】</span></div>

<div style="text-align: center;"><img src="./Figures/GUILDAicon.png" width=70%;></div>

[GUILDA Python](https://github.com/guilda-dev/guilda_python) is the Python version of the original MATLAB based program [GUILDA](https://github.com/guilda-dev/guilda). While both programs are ment to be equivalent versions of each other, GUILDA is developed in MATLAB and documented in Japanese, while GUILDA Python is developed in Python and documented in English.

GUILDA stands for Grid & Utility Infrastructure Linkage Dynamics Analyzer.
GUILDA is a numerical simulator platform for smart energy management. The purpose of this program is to provide students and researchers in the field of systems and control engineering with an advanced numerical simulation environment that can be used with minimal knowledge of power systems. To achieve this, it is recommended to use this program in closeness with the textbook ["Power Systems Control Engineering: Systems Theory and MATLAB Simulation"](https://www.coronasha.co.jp/np/isbn/9784339033847/) (only available in its original language, Japanese. The English translation is currently being developed). As in this textbook the authors explain the structure and mathematical fundamentals of power systems in the language of systems and control engineering.

We are devising a way for students to learn the mathematical fundamentals and the construction of a numerical simulation environment in parallel. Through these activities, we aim to establish power systems as one of the familiar benchmark models in the field of systems and control, thereby helping to promote power system reform through the technologies and knowledge in this field.

This is an in-development project by the [Ishizaki Laboratory](https://www.ishizaki-lab.jp/home) at [Tokyo Institute of Technology](https://www.titech.ac.jp/english#) and [Assistant Professor Kawaguchi](http://hashi-lab.ei.st.gunma-u.ac.jp/~hashimotos/member/kawaguchi/en/index.html) of Gunma University.

To learn more visit [here](https://www.ishizaki-lab.jp/guilda).

<br><br>

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【Mathematical Model of GUILDA】</span></div>

The mathematical models used in GUILDA are in line with the contents of the textbook mentioned above, and the series of execution processes necessary for analysis are organized as procedures based on the theory introduced here. The rough structure of an electric power system is also introduced in the following page [What does an electric power system consist of? Please also refer to the following page for a rough explanation of the composition of the power system. Detailed mathematical models and formulas for state-space models are also introduced here and there, but if you want a systematic understanding, please refer to the textbook.

<br><br>

---

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【Power System Configuration】</span></div>

The following is a brief description of the power system configuration.

(**Click on the Illustration ↓**)  
[<div align="center"><img src="./Figures/index-3.jpg" width=80%; style="border: 7px pink solid;"></div>](aboutPowerSystem/0TopPage.md)
<br><br>

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【GUILDA Set up】</span></div>

This section explains how to download the public source code and set up the environment.

(**Click on the Illustration↓**)  
[<div align="center"><img src="./Figures/set_GUILDA.jpeg" width=80%; style="border: 7px pink solid;"></div>](SetEnvironment/0TopPage.md)

<br><br>

### <div style="text-align: center;"><span style="font-size: 150%; color: black;"><div style="text-align: center;">【A Series of Examples: Analysis using a Simple Model】</span></div>

The three busbar system introduced in the text is used as the analysis target, and the model is implemented on this simulator to actually run the simulation and see the response. The flow from definition to analysis is designed to be a single story. If you are new to GUILDA, please refer to this page to get an idea of the overall flow.

(**Click on the Illustration ↓**)  
[<div align="center"><img src="./Figures/tuto-withText.jpg" width=80%; style="border: 7px pink solid;"></div>](SeriesAnalysis/0TopPage.md)

<br><br>

### <div style="text-align: center;"><span style="font-size: 150%; color: black;"><div style="text-align: center;">【Reference】</span></div>

Each step in the process of implementing a model and performing an analysis will be explained. Specifically, we will explain how to use the methods used to perform the analysis and how to define a new device/controller model as a class.

**(Click on the Illustration ↓)**  
[<div align="center"><img src="./Figures/tuto-newSystem.jpg" width=80%; style="border: 7px pink solid;"></div>](Reference/0TopPage.md)

<br><br>

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【Explanation of Source Code】</span></div><div style="text-align: center;">

**(Click on the Illustration ↓)**
[<div align="center"><img src="./Figures/index-4.jpg" width=80%; style="border: 7px pink solid;"></div>](SourceCode/0TopPage.md)

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【Operation Using UI】</span></div><div style="text-align: center;">

**(Click on the Illustration ↓)**

<div align="center"><img src="./Figures/index-GUI.jpg" width=80%; style="border: 7px pink solid;"></div>

### <div style="text-align: center;"><span style="font-size: 150%; color: black;">【Requirements】</span><div style="text-align: center;">

Toolboxes necessary to run this simulator:

- Optimizaiton Toolbox.
- Control System Toolbox.
- Robus Control Toolbox.
