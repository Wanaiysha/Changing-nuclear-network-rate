### CHANGING NUCLEAR REACTION RATE**

(Version Modular2)

A brief idea of how physics module operates the nuclear calculations in the PPN (Refer ppn_physics.F90) ; 
![ppn](ppnb.png)











Hence, there are several options for changing/updating the reaction rates in the code, and the choice depends on the type of study being conducted. The most basic approach is to examine in the /physics/source/physics_knobs.F90 file and identify the switches you can adjust. Below is the default list of settings from physics_knobs. You can modify them as needed and include these settings in your ppn_physics.input file:
```
ininet              = 0 ! 1 = Display all available networks
i_nse               = 0
nse_option          = 0
nvcp                = 57 !number of isotopes 
nrcp                = 110 !number of charged particle reactions 
nnn                 = 1107 !number of isotopes NNN_max = 5235 
tbetamin            = 0.5_r8
index_reaclib       = 2 !0 = 'BASEL', 1 ='JINAR',2 ='JINAC',3='JINAV
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
- MODIFYING REACTION RATE : SIMPLE WAY 

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
Input from Umberto, with the default settings, enabling this rate as 'T' in ppn_physics.input will implement the NACRE analytic formula; otherwise, the code defaults to NACRE tables. A quick CNO-burning simulation comparing both options shows approximately a 2% difference in the final 13C abundance. If you prefer to use CF88, you must comment out the NACRE option in Vital.F90 and recompile the code before running a new simulation. This change will result in a significant difference in the final 13C abundance, around 20%, due to the use of a completely different rate source, not just a variation in rate interpolation. It's important to note that altering the label/reference in ppn_physics.input directly has no effect, as confirmed by him testing this.

*Hence if one prefers the table instead of analytic formula, the reactions list in ppn_physic.input must be set to 'F' first*

- CHANGING NUCLEAR REACTION SOURCES / REFERENCES
  
  How code setup the network. By default
  1. Charged particle reactions computed by analytic formula in VITAL module as per 'T' listed in the ppn_physics.input. However, if 'T' is switched to 'F', NACRE or (Illiadis tables for proton-capture on 20 < A < 40 nuclei) is being adopted following the netgen module.
  3. Special reactions interpolated from tables (C12-C12,O16-16,Triple-Alpha) in VITAL module.
  4. Reaction C12(a,g)O16 is based on analytic formula from Kunz et al 2002 but there is an option for Deboer et al 2016 (Must set in vital to be true first)
  5. Neutron capture (n,g) is interpolated from MAC and SEFT tables in the KADONIS module, and from REACLIB when reaction is not available.
  6. Reverse reaction from REVERSE module.
  7. Proton Capture (p,g), from REACLIB and ILIADIS
  8. Beta Decay from Fuller & Fowler 1985 (light isotopes, up to Fe) and ODA94 

* REACLIB*
* 
To set reactions to read from specific REACLIB Table.
Replacing nuclear table source can be done by setting the index_reaclib = '' in the ppn_physics.input. Currently '2' is the default. You can replace this by other table or simply add another case(4) in the reaclib.F90 .Be carefull with the new arrays in the new table implementation and corresponds reaclib partition function files(winvn). Below are the available sources.
```
         case(0)
            reacfile = '../NPDATA/REACLIB/reaclib.nosmo'
         case(1)
            reacfile = '../NPDATA/REACLIB/20081109ReaclibV0.5'
         case(2)
            reacfile = '../NPDATA/REACLIB/20120510ReaclibV1.1'
         case(3)
            reacfile = '../NPDATA/REACLIB/results01111258' ! Reaclib version2.2
```
note:Setting the file to read from results01111258.data produced an error: ' isotope not found in reaclib hash table4tl20 ' 
note2:I tried to replace the reaclib table v2.2 used in Mesa-r10389, but ended with 'bad floating points'.Need to check this. UPDATE: Need to update the arrays in parameter.inc for the new table.

- CHANGING OTHER NUCLEAR REACTIONS IN VITAL.F90 

1. Information from Marco Pignatari, it is impossible to read nuclear reaction from only a single source. Vital.F90 computes the charged particle reaction network using formula and adopts special reactions rates from tables (3alpha,c12c12 and CO reactions). Hence, only formulated reaction rates can be changed or updated manually in the VITAL.F90 (Some reactions are very outdated and could use some updates)
(Note to Aisha : 1. Maybe those special rates table can be updated too?

2. 'nrcp' number must be consistence with the 'T' reactions? UPDATE : No need.'nrcp' number is simply a total number of charged particles reactions.

The vital.F90 works with several subroutines:

read_physics_input_data:

This subroutine reads input data related to species and reactions from an input file. It also handles the conversion of half-life units and checks if the isotopes considered are valid.

vital_rates_derivs:

This subroutine is a wrapper for calling vital_calculate_rates, which computes the rates of nuclear reactions based on temperature and density inputs.

vital_calculate_rates:
This subroutine computes the reaction rates for various nuclear processes, including hydrogen burning, helium burning, carbon burning, and reactions involving heavier elements. It uses temperature and density inputs to determine the rates, applying formulas and interpolating values from pre-calculated tables. Changes can be made here.
The compulsory reaction rates used in the provided code are encapsulated within the `vital_calculate_rates` subroutine, which is part of the module `vital`. Below is a list of these reaction rates along with their alternative switching mechanisms as described in the module:

**Formula-based Reaction Rates ( Mostly sourced from JINA,CF88, NACRE. Refer to vital.f90 for details reference)**

If there are two/three references used for a reaction, the latter one will be adopted.
1. Hydrogen Burning

   PP-CHAIN

   CNO Cycle

   Neon-Sodium and Magnesium-Aluminium (Champagne 1994.MOstly outdated!)
2. Helium Burning
3. Reverse rates

4. **Neon Burning**
   
`NE20(A,G)MG24` 
**Table-interpolation Reaction Rates ** 

5. **Carbon Burning**

`C12(C12,A)NE20` 

`C12(C12,P)NA23` 
 
Default Rates Files for c12c12:
```
../NPDATA/12C+12Crate_new.tex  ! Joachim Gorres, M. Wiescher, G. Imbriani, J. deBoer, and Mary Beard, 2014
```

7. **Oxygen Burning**
   
`O16(O16,G)SI32`

9. **Alternative Switching**
    
 PP-IV Chain: `IPPIV` variable controls the inclusion of the hot H-deficient He3-burning (PP-IV chain).

 C12-Alpha Reactions: Alternative rates for `C12(A,G)O16` are provided by different studies (CF88, Buchmann1996, Kunz2002, and DeBoer+2016). The selection is managed by logical flags and parameters like `Buch`,`kunz`(default) and 'DeBoer'.DeBoer is switched off by the flag 'logical:: c12ag_jdb2016 = .false.' 
 Corresponding file = ../NPDATA/c12ag_jdb16.dat   ! Nobuya Nishimura et al., 2014.

Neon22 reactions: The module allows switching between different sources for reaction rates for `Ne22(A,N)` and `Ne22(A,G)`, including rates from Michael Wiescher, Longland+2012, and Talwar+2015. 
Default : rate_ne22.dat ! Joachim Gorres, M. Wiescher, G. Imbriani, J. deBoer, and Mary Beard, 2014

Alternative sources for Ne22 rates in the VITAL.F90. Change to 'true' with your preference or replace with your own file: 
```
   logical:: ne22_michael = .false.
   logical:: ne22_longland = .false.
   logical:: ne22_nd2015 = .false.
   
corresponding file respectively:
../NPDATA/ag_lo12.dat ! Longland et al., 2012
../NPDATA/an_lo12.dat ! Longland et al., 2012
../NPDATA/ne22a_mw15.dat ! Talwar et al., 2015
```
- CHANGING THE KADONIS REACTION RATES

Refer to Kadonis.F90 and one could updates the Kadonis table/data as needed. Some reactions have more than one database and only the latter one will be adopted in the calculation. Be carefull with the number of rows with the new table/data implementation. 

  
- CUSTOM AD-HOC CHANGES 
  
This section in vital.F90 is implemented in order to allow for quick ad-hoc rates
to be applied to the code, not a permanent additions or compilations.

Look for subroutine N14TEST(t9, rateout) and modify accordingly.

