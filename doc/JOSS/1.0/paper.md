---
title: 'The Geodynamic World Builder: An initial conditions generator for the geosciences'
tags:
  - C++
  - CPP
  - C
  - Fortran
  - Python
  - Geodynamics
  - Earth science
  - Tectonics
  - Seismology
authors:
  - name: Menno R. T. Fraters
    orcid: 0000-0003-0035-7723
    affiliation: "1" # (Multiple affiliations must be quoted)
  - name: Magali I. Billen
    orcid: 0000-0002-7316-1791
    affiliation: "2"
  - name: Rene Gassmöller
    orcid: 0000-0001-7098-8198
    affiliation: "1"
  - name: Arushi Saxena
    orcid: 0000-0003-2930-3497
    affiliation: "3"
  - name: Timo Heister
    orcid: 0000-0002-8137-3903
    affiliation: "3"
  - name:  Juliane Dannberg
    orcid: 0000-0003-0357-7115
    affiliation: "1"
  - name: Haoyuan Li
    orcid: 0
    affiliation: "2"
  - name: Daniel Douglas
    orcid: 0
    affiliation: "4"
  - name: Yijun Wang
    orcid: 0
    affiliation: "5"
affiliations:
 - name: Universiy of Florida, USA
   index: 1
 - name: UC Davis, USA
   index: 2
 - name: Clemson University, USA
   index: 3
 - name: New Mexico Tech, USA
   index: 4
 - name: University of Oslo, Norway
   index: 4
date: 23 February 2023
bibliography: paper.bib
---

# Summary

Many Earth science applications require the discretization, parametrization and/or visualization of complex geological features in a 3D geometry in global or regional settings. A prime example are geodynamic models, which have to make assumptions about the Earth's thermal and chemical structure and the geometry of different features such as plates, subducted slabs, or mantle plumes. This structure is needed in instantaneous models, as model initial conditions, or to test different hypotheses and compare model predictions to observations. Other examples are the creation of an Earth velocity structure for seismic forward modeling and hypothesis-testing, or the visualization of tectonic features in structural geology.

Based on the complexity of their geometry, geodynamic models can be divided into three broad categories. The first category are simple synthetic models. These models involve simplistic geometries for generic modeling and are easy to make and parameterize. Because of their simplicity, this type of model is useful for simplified problems, scaling analyses and benchmarks. The second category of models are the data driven models. These models derive initial conditions (semi-)automatically from datasets such as a seismic tomography model or a fault database and are often used for instantaneous models. The third category of models are complex synthetic models. These global or regional models are often very detailed and blend complex 3D geometrical features, thermal and chemical structure. They can be useful to parameterize instantaneous models or to create initial conditions for models starting in the past (or using Earth's presumed present-day state to predict the future).

The Geodynamic World Builder (GWB) has been designed to make the third case significantly easier, but can also be used to design simple synthetic models, and several types of datasets are supported to be used to setup models in a way which aligns with the GWB design philosophies.

Besides setting up initial conditions for geodynamic models, the GWB can also be used to make detailed 3D visualizations of geologic and geodynamic settings.

# Statement of need

The increase in available computational resources, infrastructure and new numerical methods over the last decades has allowed for the creation of complex numerical models that closely resemble specific locations on Earth using a 3D geometry and high resolution. However, the related increase in complexity has also made setting up these more detailed regional or global models exceedingly difficult, especially in three dimensions. Furthermore, investigating the model dynamics often requires testing different scenarios involving variations in model geometry, thermal or chemical structure, or other model assumptions. This highlights the need for changing the model setup in a way that is easy, efficient, and robust. Although successful studies have been published, such model setups often have one or many of the following disadvantages:

1. Code is not readable (even hard for their developers)
2. Code/initial conditions is/are not modifiable (even hard for their developers)
3. Code is not extendable
4. Code is not portable or reproducible in other codes
5. Code is not shareable which makes everyone reinvent the wheel.

The GWB has been designed to solve these problems. Furthermore, it includes programs for visualizing the constructed model through applications like Paraview, and creating the models requires no programming knowledge. Therefore, the GWB makes it easy to visualize tectonic and geodynamic settings for publication, teaching or discussion purposes. 

Other tools to solve this problem have emerged at around the same time as the first GWB release (@Fraters_Thieulot_etal_2019). Examples include GeomIO (@Bauville_Baumann_2019,@Spang_Baumann_2022), which uses an approach based on vector graphics, Easy (https://easyinit.readthedocs.io/), which uses a more generic function-based approach, UWGeodynamics (@Beucher_Moresi_etal_2019), which is specifically designed for Underworld (@Moresi_Dufour_2002) and GemPy (@Varga_Schaaf_2019,@Schaaf_Varga_2021), which is designed for structural modelling. 

# Methods

To address the challenges outlined in the previous section, the Geodynamic World Builder implements a specific code and user design philosophy.

## GWB Code Philosophy
The code philosophy is built around the following points:

1. A single text-based input file
2. Code, language and platform independent
    1. Supports **Linux**, **OSX** and **Windows**
    2. Has official interfaces for **C++**, **C**, **FORTRAN** and **Python** coupling
3. Safe to use in parallel codes
4. Readable and extensible (modular) codes
5. Strict version numbering to ensure reproducible results

Following these principles is the first step to create a clean, portable, extendable code with reproducible results. In addition, the GWB includes an automated test suite with integration and unit tests with high code coverage and automatic code indentation.

## GWB User Philosophy

The user philosophy is built around the idea that users define tectonic features. These tectonic features can be parameterized by defining their location and geometry in terms of points, lines or areas in a map view. For example, a continental plate can be represented as an area on a map, and the GWB user can define this area. A fault is a linear feature on a map, so the user can define the fault trace at the surface as a line. Users can also provide additional information, such a spatially variable thickness or dip angle. The GWB then uses these parameters to create the 3D geometry of the feature, defining its volume. Furthermore, users can attach one or many models to those volumes to define additional properties such as thermal or chemical structure. These can be very simple models, such as a uniform temperature distribution, or follow a more complex distribution, such as a half space cooling model, or a McKenzie [@McKenzie_1970] or mass conserving [@billen_Fraters_AGU_2023] slab temperature model. 

All these properties are set in a single JSON-styled input file, which is human readable, writeable and editable. The main idea behind this design of the GWB is that users can easily create, modify, and visualize complex parameterized initial conditions for their geodynamic or tectonic setting. 

## Example 
Below we show an example input file for a Cartesian model, which can easily be converted into a spherical model, such as shown in the figure below which also shows some extra capabilities.

```json
{
  "version": "1.0",
  "coordinate system":{"model":"cartesian"},
  "features":
  [
    {
       "model":"oceanic plate", "name":"Overriding Plate", "max depth":100e3, 
       "coordinates":[[0,0],[0,1000e3],[1500e3,1000e3],[1600e3,350e3],[1500e3,0]],
       "temperature models":[{"model":"linear", "max depth":100e3}],
       "composition models":[{"model":"uniform", "compositions":[0]}]
    },
    { 
      "model":"subducting plate", "name":"Slab", "dip point":[0,0],
      "coordinates":[[1500e3,1000e3],[1600e3,350e3],[1500e3,0]],
      "segments":[{"length":300e3, "thickness":[100e3], "angle":[0,60]}],
      "temperature models":[{"model":"plate model", "plate velocity":0.02}],
    }
  ]
}
```

![A schematic example of what can be built with 66 lines of a GWB input file formatted in the same way as in example input file shown above. \label{fig:example}](../../sphinx/_static/images/user_manual/basic_starter_tutorial/BST_17.png)


# Acknowledgements

We would like to acknowledge all other contributors to the project, especially Lorraine Hwang, Becky Fildes and John Naliboff for their advice and support for this project throughout the years. We would also like to acknowledge NSF for their funding and support through grants EAR-1620618 (CPO), OCE-1948902 (Cascadia), EAR-0949446 (CIG), EAR-1550901 (CIG) and EAR-1925677 (FRES).

# References


