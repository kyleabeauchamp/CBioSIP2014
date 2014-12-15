% title: Engineering a full python stack for biophysical computation
% author: Kyle A. Beauchamp
% favicon: figures/membrane.png

---
title: Moore's Law


<center>
<img height=550 src=figures/moore_law.png />
</center>

<footer class="source"> 
http://en.wikipedia.org/wiki/Moore%27s_law
</footer>

---
title: erooM's Law for R&D Efficiency
subtitle: Producing a drug costs $2B and 15 years, with 95% fail rate

<center>
<img height=400 src=figures/eroom.png />
</center>


<footer class="source"> 
Scannell, 2012
</footer>


---
title: How can computers help design drugs?

<center>
<img height=475 src=figures/cruise.png />
</center>

<footer class="source"> 
Figure credit: @jchodera, Tom Cruise
</footer>

---
title: Challenges in molecular medicine

- Can we link nanoscale biophysics with human disease?
- Can we rationally engineer protein-drug binding?

---
title: Our Toolbox: Molecular Dynamics

- Physics-based simulations of biomolecules
- Numerically integrate (classical) equations of motion
- Protein, water, salts, drugs

<div>
<video id="sampleMovie" class="center" src="movies/shaw-dasatanib-2.mov" loop=\"true\ autoPlay=\"true\  width="512" height="384"></video>
</div>


<footer class="source"> 
Shan et al: J. Am. Chem. Soc. (2011).  <br>
http://deshawresearch.com/
</footer>

---
title: Software for Biophysics
class: segue dark nobackground

---
title: How do we reach biological timescales?
subtitle: Challenge: $10^5$ atoms, $10^{12}$ iterations.

<center>
<img height=450 src=figures/protein_timescales.jpg />
</center>

<footer class="source">
Church, 2011.
</footer>

---
title: OpenMM
subtitle: GPU accelerated molecular dynamics

- Extensible C++ library with Python wrappers
- Hardware backends for CUDA, OpenCL, CPU
- $>100$ nanoseconds ($10^{-7}$ s) per day on a GTX Titan

<center>
<img height=300 src=figures/openmm.png />
</center>

<footer class="source"> 
openmm.org <br>
Eastman et al, 2012.
</footer>

---
title: OpenMM Powers Folding@Home

- Largest distributed computing project
- 100,000 CPUs, 10,000 GPUs, 40 petaflops!
- Hundreds of microseconds per day aggregate simulation
- ~100 research papers on folding, misfolding, signalling

<center>
<img height=300 src=figures/folding-icon.png />
</center>


<footer class="source"> 
http://folding.stanford.edu/ <br>
Gromacs also powers Folding@Home: http://gromacs.org/
</footer>

---
title: Trajectory munging with MDTraj
subtitle: Read, write, and analyze trajectories with only a few lines of Python.

- Multitude of formats (PDB, DCD, XTC, HDF, CDF, mol2)
- Geometric trajectory analysis (distances, angles, RMSD)
- Numpy / SSE kernels enable Folding@Home scale analysis

<center>
<img height=200 src=figures/mdtraj_logo-small.png />
</center>

<footer class="source"> 
mdtraj.org <br>
McGibbon et al, 2014
</footer>

---
title: Trajectory munging with MDTraj
subtitle: Lightweight Pythonic API

<pre class="prettyprint" data-lang="python">
import mdtraj as md

trajectory = md.load("./trajectory.h5")
indices, phi = md.compute_phi(trajectory)
</pre>


<center>
<img height=300 src=figures/phi.png />
</center>

<footer class="source"> 
mdtraj.org <br>
McGibbon et al, 2014
</footer>



---
title: MDTraj IPython Notebook

<center>
<img height=525 src=figures/mdtraj_notebook.png />
</center>


<footer class="source"> 
mdtraj.org <br>
McGibbon et al, 2014
</footer>

---
title: MSMBuilder
subtitle: Finding meaning in massive simulation datasets



<center>
<img height=300 src=figures/msmbuilder.png />
</center>


<footer class="source"> 
msmbuilder.org <br>
https://github.com/msmbuilder/msmbuilder
</footer>


---
title: MSMBuilder
subtitle: Markov State Models of Conformational Dynamics



<center>
<img height=400 src=figures/NTL9_network.jpg />
</center>

<footer class="source"> 
Voelz, Bowman, Beauchamp, Pande. J. Am. Chem. Soc., 2010
</footer>



---
title: MSMBuilder
subtitle: sklearn-like statistical models for conformation dynamics

<pre class="prettyprint" data-lang="python">

import mdtraj as md
from msmbuilder import datasets, cluster, markovstatemodel
from sklearn.pipeline import make_pipeline

dataset = datasets.alanine_dipeptide.fetch_alanine_dipeptide()  # From Figshare!
trajectories = dataset["trajectories"]  # List of MDTraj Trajectory Objects

clusterer = cluster.KCenters(n_clusters=10, metric=md.rmsd)
msm = markovstatemodel.MarkovStateModel()

pipeline = make_pipeline([clusterer, msm])
pipeline.fit(trajectories)

</pre>

<footer class="source"> 
msmbuilder.org <br>
https://github.com/msmbuilder/msmbuilder
</footer>

---
title: Yank
subtitle: Fast, accurate alchemical ligand binding simulations


<center>
<div>
<video id="sampleMovie" class="center" src="movies/alch.mov" loop=\"true\ autoPlay=\"true\  width="512" height="384"></video>
</div>
</center>


<footer class="source"> 
https://github.com/choderalab/yank <br>
http://alchemistry.org/
</footer>

---
title: Python Packaging Blues
class: segue dark nobackground

---
title: Building scientific software is hard!

<pre>

<font color="red">User: I couldn't really install the mdtraj module on my computer [...]
User: I tried easy_install and other things and that didn't work for me.</font>

</pre>

---
title: Building scientific software is hard!
subtitle: 2008: I was compiling BLAS / Numpy / Scipy by hand...


<center>
<img height=520 src=figures/dependencies0.png />
</center>

<footer class="source"> 
Red means hard to install.
</footer>


---
title: Building scientific software is hard!
subtitle: 2010: Switched to Enthought python

<center>
<img height=520 src=figures/dependencies1.png />
</center>

<footer class="source"> 
Red means hard to install.
</footer>

---
title: Building scientific software is hard!
subtitle: Present: Conda


<center>
<img height=520 src=figures/dependencies2.png />
</center>

<footer class="source"> 
Red means hard to install.
</footer>

---
title: Avoiding glibc Hell

<pre class="prettyprint" data-lang="bash">

-bash-4.1$ parmchk2
~/opt/bin/parmchk2_pvt: /lib64/libc.so.6: version `GLIBC_2.14' not found
</pre>

- Problem: users insist on old Linux versions
- Solution: build all recipes on a Centos 6.6 VM 

<footer class="source"> 
https://github.com/omnia-md/virtual-machines/
</footer>


---
title: Facile package sharing

<pre>

<font color="red">User: I couldn't really install the mdtraj module on my computer [...]
User: I tried easy_install and other things and that didn't work for me.</font>

<font color="blue">Me: Installing mdtraj should be a one line command:
Me: `conda install -c https://conda.binstar.org/omnia mdtraj`</font>

<font color="red">User: Success!</font>

</pre>


---
title: A full stack for biophysical computation
subtitle: Simulation, Munging, Analysis, Visualization

<pre class="prettyprint" data-lang="bash">
conda install -c http://conda.binstar.org/omnia/channel/omnia1_beta1 omnia
</pre>

- OpenMM
- MDTraj
- MSMBuilder / Mixtape
- Yank
- EMMA$^1$

<footer class="source"> 
1: Senne, Noe.  J. Chem. Theor. Comp. 2012
</footer>


---
title: Automating Biophysics
class: segue dark nobackground

---
title: Models are made to be broken
subtitle: How can we falsify and refine computer based models?

- Chemistry and biophysics are labor-intensive
- Thousands of parameters = thousands of measurements
- Reproducibilty and scalability

---
title: Can experiments be easy as Py(thon)?

<pre class="prettyprint" data-lang="python">

from itctools.procedures import ITCExperiment
from itctools.materials import Solvent
from itctools.labware import Labware

# [...]

water = Solvent('water', density=0.9970479 * grams / milliliter)
source_plate = Labware(RackLabel='SourcePlate', RackType='5x3 Vial Holder')
experiment = ITCExperiment()


</pre>



<footer class="source"> 
Work by @jhprinz and @bas-rustenburg
<br>
https://github.com/choderalab/robots <t> https://github.com/choderalab/itctools
</footer>

---
title: Robots!


<center>
<video width="960" height="540" controls>
  <source src="movies/robot.mp4" type="video/mp4">
</video>
</center> 


---
title: Biophysical modeling should be:

- Reproducible
- Automatable
- Accessible
- Tested
- Useful


---
title: People

- John Chodera + ChoderaLab (MSKCC)
- Robert McGibbon (Stanford)
- Peter Eastman (Stanford)
- Vijay Pande + PandeLab (Stanford)
- Daniel Parton (MSKCC)
- Yutong Zhao (Stanford, Folding@Home)
- Joy Ku (Stanford)
- Jason Swails (Rutgers)
- Justin MacCallum (U. Calgary)

<footer class="source"> 
Jan-Hendrik Prinz, Bas Rustenburg, Sonya Hanson
Greg Bowman, Christian Schwantes, TJ Lane, Vince Voelz, Imran Haque, Matthew Harrigan, Carlos Hernandez, Bharath Ramsundar, Lee-Ping Wang
Frank Noe, Martin Scherer, Xuhui Huang, Sergio Bacallado, Mark Friedrichs
</footer>


---
title: Questions?

<pre class="prettyprint" data-lang="bash">
conda install -c https://conda.binstar.org/omnia/channel/test omnia
</pre>

omnia.md

openmm.org

mdtraj.org

github.com/msmbuilder/msmbuilder

github.com/choderalab/yank/

