There are several options for changing the nuclear network, and the choice depends on the type of study being conducted. The most basic approach is to examine the physics_knobs.F90 file and identify the switches you can adjust. Below is the default list of settings from physics_knobs. You can modify them as needed and include these settings in your ppn_physics.input file:
```
makefile
Copy code
ininet              = 0
i_nse               = 0
nse_option          = 0
nvcp                = 57
nrcp                = 110
nnn                 = 1107
tbetamin            = 0.5_r8
index_reaclib       = 2
jbj_mode            = 1
nkk_mode            = 1
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
isomer_choice       = 0
decay               = .false.
decay_time          = 1.e17 ! seconds
kadonis_interp      = 3 ! 1 is linear, 3 is Akima
do_neutrinos        = .false.
```
