# Example 5a: Dynamic analysis of a stick model of a nuclear power plant

## Model Description

This example demonstrates the dynamic analysis of a simplified stick model of a nuclear power plant taken from the SASSI2000 user manual [!citet](sassiuser2006)
in MASTODON and benchmarks the results with LS-DYNA. Both MASTODON and LS-DYNA input files for this example are included in the [GitHub repository](https://github.com/idaholab/mastodon). The nuclear power plant consists of a containment made of prestressed concrete and a reinforced concrete internal structure. The simplified numerical model [fig:stickmodel] consists of two sets of stick models, one for the containment and the other for the internal structure. The lumped masses and the section properties of the stick model are presented in the right part of [fig:stickmodel].
The superstructure is connected to the rigid foundation by four rigid foundation beams whose deformation is zero as shown in [fig:stickwithfoundation].
MASTODON currently does not have a rigid element formulation. Thus, the foundation beams and the solid foundation are modeled by increasing the value of Young's modulus of elasticity by ${10}^3$. An acceleration history is applied at the base of the foundation in the X, Y, and Z directions and the response spectra at different elevations are compared with the responses obtained from the LS-DYNA model taken from [!citet](fragilityanalysis).


!media media/examples/stickmodel.png
       style=width:100%;margin-right:0px;float:center;
       id=fig:stickmodel
       caption=Illustration of the representative nuclear power plant structures and the corresponding stick models (left) and section properties and lumped masses of stick model (right) [!citep](sassiuser2006).

!media media/examples/stickwithfoundation.png
       style=width:50%;margin-left:150px;float:center;
       id=fig:stickwithfoundation
       caption=Illustration of the connection between the stick model and foundation [!citep](sassiuser2006).             

!alert note
- The horizontal separation between the stick-mass model in the above figures is only for illustration. Both the stick-mass models are located along the center line and the base node is shared by both the stick mass models.
- The lumped mass value of mass no. 3 is 4600 kips and not 46000, and mass no. 2, which is omitted in the table is equal to 4200 kips.

## Modeling in MASTODON

!listing examples/ex05a/StickModel_accel_base.i

The mesh is generated by importing an existing mesh file generated in [Cubit](https://cubit.sandia.gov). The details of the types of supported mesh files can be found in the [User Manual](manuals/user/index.md#meshing). The stick-models and the foundation beams are modeled using 1D line elements and the foundation is modeled using 3D solid elements as shown in [fig:ex05input]. The displacements and rotations in X, Y and Z directions are defined in the variables section and the velocity, acceleration, rotational velocity and rotational acceleration are defined as the auxiliary variables.

!media media/examples/ex05input.png
       style=width:60%;margin-left:150px;float:center;
       id=fig:ex05input
       caption=Input model in MASTODON.


 The stick models are created using the [LineElementMaster](syntax/Modules/TensorMechanics/index.md) action. [DynamicTensorMechanics](syntax/index.md) and [InertialForce](syntax/index.md) kernels are used to model the dynamics of the 3D solid elements. The [Newmark time integration](manuals/theory/index.md#newmark-beta) parameters used in this problem correspond to the Newmark's average acceleration method, i.e. `beta = 0.25` and `gamma = 0.5`. A constant [Rayleigh damping](manuals/theory/index.md#rayleigh-damping) of 5% is implemented using the values of `zeta = 0.00054388` and `eta = 3.266`. The section properties and the lumped masses described in [fig:stickmodel] are assigned to the different blocks of the model through the LineElementMaster action.

The [ComputeElasticityBeam](syntax/index.md) block is used to create the elasticity tensor of the beam elements, using the parameters, Young's Modulus, Poisson's ratio, and shear coefficient. The forces and moments in the beam are calculated in the [ComputeBeamResultant](syntax/index.md) block.

The nodes at the connection between the beam elements and the solid elements at the foundation are restrained in local rotation. The acceleration time series shown in [fig:5a_accelx], [fig:5a_accely] and [fig:5a_accelz] whose response spectra is shown in [fig:5a_accspecx], [fig:5a_accspecy] and [fig:5a_accspecz] is applied at the base of the foundation in X, Y and Z directions respectively using the BCs block. The ground motion accelerations are read from the CSV files and a scale factor of 32.2 is used to convert the units of acceleration from g to ft/${s}^2$.

!row!

!col! small=12 medium=6 large=6
!media media/examples/5a_accelx.png
       style=width:400px id=fig:5a_accelx
       caption=Input acceleration time series in X direction
!col-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_accely.png
       style=width:400px id=fig:5a_accely
       caption=Input acceleration time series in Y direction
!col-end!

!row-end!

!row!

!col! small=12 medium=6 large=6
!media media/examples/5a_accelz.png
       style=width:400px id=fig:5a_accelz
       caption=Input acceleration time series in Z direction
!col-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_accespecx.png
       style=width:400px id=fig:5a_accspecx
       caption=Input acceleration response spectra in X direction
!col-end!

!row-end!

!row!

!col! small=12 medium=6 large=6
!media media/examples/5a_accespecy.png
       style=width:400px id=fig:5a_accspecy
       caption=Input acceleration response spectra in Y direction
!col-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_accespecz.png
       style=width:400px id=fig:5a_accspecz
       caption=Input acceleration response spectra in Z direction
!col-end!

!row-end!

The executioner and the post-processor blocks are provided to solve the nonlinear system and extract the required data.

## Comparison of Results

The response spectra at the two different elevations (22 ft and 61 ft) in the internal structure are compared with the response spectra obtained from LS-DYNA, calculated by [!citet](fragilityanalysis). These acceleration response spectra, calculated as functions of frequency, are plotted in [fig:5a_14x] to [fig:5a_17z]. The figures show that the response spectra obtained from MASTODON are in close agreement with the response spectra obtained from LS-DYNA in X and Y directions. There are minor differences in the results in the Z direction, likely due to the differences the damping formulations in MASTODON and LS-DYNA.

!row!

!col! small=12 medium=6 large=6
!media media/examples/5a_14x.png
       style=width:400px id=fig:5a_14x
       caption=Comparison of the response spectra at at an elevation of 22 ft at internal structure in X direction
!col-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_14y.png
       style=width:400px id=fig:5a_14y
       caption=Comparison of the response spectra at at an elevation of 22 ft at internal structure in Y direction
!col-end!

!row-end!

!row!

!col! small=12 medium=6 large=6
!media media/examples/5a_14z.png
       style=width:400px id=fig:5a_14z
       caption=Comparison of the response spectra at at an elevation of 22 ft at internal structure in Z direction
!col-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_17x.png
       style=width:400px id=fig:5a_17x
       caption=Comparison of the response spectra at at an elevation of 61 ft at internal structure in X direction
!col-end!

!row-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_17x.png
       style=width:400px id=fig:5a_17y
       caption=Comparison of the response spectra at at an elevation of 61 ft at internal structure in Y direction
!col-end!

!col! small=12 medium=6 large=6
!media media/examples/5a_17z.png
       style=width:400px id=fig:5a_17z
       caption=Comparison of the response spectra at at an elevation of 61 ft at internal structure in Z direction
!col-end!



!bibtex bibliography