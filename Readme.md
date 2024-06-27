CHANGING NUCLEAR REACTION RATE

(Modular2 version)

There are several options for changing this, and the choice depends on the type of study being conducted. The most basic approach is to examine in the /physics/source/physics_knobs.F90 file and identify the switches you can adjust. Below is the default list of settings from physics_knobs. You can modify them as needed and include these settings in your ppn_physics.input file:
```
ininet              = 0
i_nse               = 0
nse_option          = 0
nvcp                = 57
nrcp                = 110
nnn                 = 1107
tbetamin            = 0.5_r8
index_reaclib       = 2 !0 = "Basel" Reaclib, 1 = JINA Reaclib, 2 = "Pure" Reaclib(JINAR)
jbj_mode            = 1 !1 = Oda rate, 2 = JBJ rate, 3 = Oda94+JBJ, 4 = JBJ everywhere
nkk_mode            = 1 !no merging with NKK04 rate 
nse_min_t9          = 6._r8
t9_nw_ini           = 7.8e-3_r8
ye_nw_ini           = 0.5_r8
rho_nw_ini          = 1._r8
use_cache           = .false.
yps_nw_ini(:,:)     = ZERO
detailed_balance    = .true.
screen_option       = CHUGUNOV !'none', 'GRABOSKE' screening, 'CHUGUNOV' screening.
weak_rates          = .true.
strong_rates        = .true.
rate_index(:)       = -1
rate_factor(:)      = ONE
use_other_nuc       = .true.
isomer_choice       = 0 ! 0 = do not include isomers, 1 = include isomers (so far only a few isomers are considered.Check NPDATA/isomersfiles)
decay               = .false.
decay_time          = 1.e17 ! seconds
kadonis_interp      = 3 ! 1 is linear, 3 is Akima
do_neutrinos        = .false.
```
MODIFYING REACTION RATE : EXAMPLE 

Consider the parameters rate_index and rate_factor  above. These are arrays whose sizes are determined by the num_rate_factors parameter, which is set by default to 10 in physics/source/physics_knobs.F90. 
This setting allows for the modification of up to 10 rates simultaneously during a run. To alter more rates, you would need to adjust the num_rate_factors in physics_knobs.F90 and recompile the code.
Firstly before changing anything, printout the networksetup.txt by running the ppn.exe with ininet = 1, which contains all the reactions in the network with their respective indexes shown in the first column. 
The desired factor can then be applied using rate_factor.
```
rate_index(1) = 161
rate_factor(1) = 30.0
```
In this case example, 161 corresponds to the reaction 13C(p,γ).
13C(p,γ) rate at all temperatures is multiplied by a factor of 30.

In case you want to modify one more rate, let's say  you want to double the rate of the 14N(p,g) whose index in the network is 17, all you need would be the following:
```
rate_index(1) = 161 
rate_factor(1) = 30
     
rate_index(2) = 17 
rate_factor(2) = 2
```
Important to note, make sure the reactions you are changing are set to be 'T' in the ppn_physics.input, else, the changes will take no effects.

CHANGING NUCLEAR REACTION SOURCE / REFERENCES

Important module that compute this can be refered to vital.F90. Information from Marco Pignatari, it is impossible to read source from only 1 source of file.
The vital.F90 works with several subroutines:

read_physics_input_data:
This subroutine reads input data related to species and reactions from an input file. It also handles the conversion of half-life units and checks if the isotopes considered are valid.

vital_init:
This subroutine initializes rate tables by reading data from external files. It sets up the necessary data for the computation of reaction rates, including reading tables for specific reactions.

vital_rates_derivs:
This subroutine is a wrapper for calling vital_calculate_rates, which computes the rates of nuclear reactions based on temperature and density inputs.

vital_calculate_rates:
This subroutine computes the actual reaction rates for various nuclear processes, including hydrogen burning, helium burning, carbon burning, and reactions involving heavier elements. It uses temperature and density inputs to determine the rates, applying formulas and interpolating values from pre-calculated tables.

Default Reaction Input Files:
```
../NPDATA/c12ag_jdb16.dat
../NPDATA/12C+12Crate_new.tex
../NPDATA/rate_ne22.dat
../NPDATA/ag_lo12.dat
../NPDATA/an_lo12.dat
../NPDATA/ne22a_mw15.dat
```
