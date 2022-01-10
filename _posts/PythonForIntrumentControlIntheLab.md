# Python for Intrument Control in a Research Lab

Python is fastly becoming the platform of choice for physicists. Obvioulsy, not having to manage licences on *N* computers helps quite a lot. Beyons this, it also blur the line between data processing and experiment control. During grad school, I was mostly using Matlab for simulation/data processing/plot for papers and LabView for controlling my experiments.

Then the epiphany occured once I started to transition to python for my data processing because of all the hype I heard, while realizing that the lack of available Instrument control toolbox in my lab. Duplicating a python environment onto another computer is a breeze (`pip —freeze requirement.txt` is your friend ;) )

The learning curve is quite smooth and it takes no time to get acquinted to using instruments with python. Most of the command used there are either similar to Matlab instrument toolbox control and the visa protocol remain the same than what is done in Labview. Therefore, even translating codes from Labview to python doesn’t require much effort. 

What drew me to keep using python for all my experiment is how it blurred the line with data processing. In a way, my experiment are much more targeted and efficient. I can while doing the experiment automatize the data processing that loop back to the acquisition part of the script and refine my results. The goal: much better data that I wouldn’t have been able to get manually (stability of the experiment, conter-intuitive data, very long acquisition, redundant acquisition, etc…)

If you want more info on my workflow you can check it out there. Here I will focus on the basis: the different way to connect to the lab instruments, how to interface them and some tips that I discovered along the years. 

## How to connect to an instrument? 

The hardware, and sometime unfortunately the software, will define how you can connect to them. 

For old instrument, RS232 and GPIB are the main protocol. Both of them can be interefaced using the visa protocol, which is nicely wrapped in the `pyvisa` package. Thankfully, a lot of USB instruments also allows for standard protocol through visa

Unfortunately, some manufacturer limit the accessibility to their instrument because of properietary softwares, intelectaul properties, etc … This is mostly high end intrument that connects through USB. Yet, their software sometime rely on DLLs that can be open with python and you could still interface their instruments 

Finally, ethernet connection becomes thankfully more and more ubiquitous. To me, this is the connection of choice. The fact that any computer in the lab can control an instrument is crucial, especially the more complex the experiment becomes (i.e. the more spread is the lab one become) and the data speed  transfer allows for real time monitoring.

### Visa protocol 

You would need to install the pyvisa package using: 

```python
pip install pyvisa
```
or 

```python
conda install pyvisa
```

All the instruments that can be directly connected can be listed using 

```python
from  pyvisa 
print(rm.list_ressources())
```


### Opening DLLs and Using their functions

What about instruments that canot connect with standards visa? Well, if you are lucky they come up with a DLL (??? ??? Library). For instance, NewFocus laser cannot be connected using pyvisa, but come with a USBWrapper.dll file that contains all the C++ function. 

if you are using windows, you could defintely open them and use them in python. 

You would have to install the pythonnet package, that allows to load net+ compiled function 

```python
pip install pythonnet
```



One drawback of such approach is that the function remain anonymous. If you don’t have the programation documentation there is no way to how to call the different compiled functions.

### Ethernet connection


## UI 

### Do you really need an GUI

### So you really need one? Where to start? 

#### PyQt

#### Dear PyGuy

## Notebooks and Jupyter 


where both can take place almost at the same time and 