CHANGING NUCLEAR RATE / NUCLEAR NETWORK 


CHANGING / UPDATING NUCLEAR REACTION RATE
There are several options for changing the nuclear network, and the choice depends on the type of study being conducted. The most basic approach is to examine the physics_knobs.F90 file and identify the switches you can adjust. Below is the default list of settings from physics_knobs. You can modify them as needed and include these settings in your ppn_physics.input file:
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
screen_option       = CHUGUNOV
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
